## Программа с запуском стороннего процесса исполняемый файл стороннего процесса подготавливается заранее (отдельно для Windows и Linux).


-----


Windows
Первая программа
```C
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <malloc.h>
#include <string.h>
#include <Windows.h>
#define PATH "C:/Users/kga/source/repos/21_bilet/x64/Debug/secondProc.exe" 

int main() {
	system("chcp 1251");
	STARTUPINFO si;
	PROCESS_INFORMATION pi;

	ZeroMemory(&si, sizeof(si));
	si.cb = sizeof(si);
	ZeroMemory(&pi, sizeof(pi));

	if (!CreateProcessA(
		PATH,
		"Hellow",
		NULL,
		NULL,
		FALSE,
		0,
		NULL,
		NULL,
		&si,
		&pi)) {
		printf("Ошибка: %d.\n", GetLastError());

	}
	WaitForSingleObject(pi.hProcess, INFINITE);

}
```
Вторая программа
```C
#include <stdlib.h>
#include <stdio.h>

int main(int args, char* argv[]) {
	printf("%s", argv[0]);
}
```
