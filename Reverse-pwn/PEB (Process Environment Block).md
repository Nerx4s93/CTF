Каждый процесс в ОС Windows имеет описательный блок PEB (Process Environment Block) размером в 2000 байт, содержащий информацию об этом процессе.

## x64
Лежит по смещению `gs[0x60]`
[Vergilius Project | _PEB](https://www.vergiliusproject.com/kernels/x64/windows-11/22h2/_PEB)

## x86
Лежит по смещению `fs[0x30]`
[Vergilius Project | _PEB](https://www.vergiliusproject.com/kernels/x86/windows-10/22h2/_PEB)