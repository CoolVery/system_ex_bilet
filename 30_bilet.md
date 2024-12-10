## Cоздание фонового процесса, который каждые 5 секунд выводит всплывающее или диалоговое сообщение, содержащее увеличивающееся на единицу значение счетчика (отдельно для Windows и Linux)


-----


Windows
```C
#define _CRT_SECURE_NO_WARNINGS
#include <windows.h>
#include <stdio.h>
int counter = 0;

DWORD WINAPI SendMSB() {
	counter++;
	wchar_t buffer[100]; // Используем wchar_t для широких символов
	swprintf(buffer, 100, L"Счетчик: %d", counter);
	MessageBox(NULL, buffer, L"Фоновый проц", NULL);

}


int WINAPI WinMain(HINSTANCE hk, HINSTANCE hPre, LPSTR lpCmd, int nCmd) {
	HANDLE hThread;


	while (1) {
		hThread = CreateThread(NULL, 0, SendMSB, NULL, 0, 0);
		Sleep(5000);
		TerminateThread(hThread, 0);
	}
}
```
