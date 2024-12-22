## Cоздание фонового процесса, который каждые 5 секунд выводит всплывающее или диалоговое сообщение, содержащее увеличивающееся на единицу значение счетчика (отдельно для Windows и Linux)


-----

В свойстве проекта в
Компилятор -> Все параметры -> Подсистема - Вместо консоли ставим Windows

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

В линукс есть два способа
1.
```C
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>

void message()//выводит всплывающее сообщение и считает их количество
{
	int counter=0;
	char str[100];
	while (1) {
		sprintf(str,"notify-send \"count messages = %d\"",++counter);//увеличиваем номер и посылаем сообщение
		system(str);
		sleep(12);//этого времени должно хватить, чтобы предыдущее сообщение закрылось с экрана (система его показывает 10 секунд)
	}
}

int main()
{
	switch (fork())
	{ /* Превращение в фоновый процесс */
		case -1: return -1;
		case 0: break; /* Потомок проходит этот этап... */
		default: _exit(EXIT_SUCCESS); /* ...а родитель завершается */
	}
	if (setsid() == -1) /* Процесс становится лидером новой сессии */
		return -1;
	message();
}
```

2.
```C
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>

void message()//выводит всплывающее сообщение и считает их количество
{
	int counter=0;
	char str[100];
	while (1)
	{
		sprintf(str,"notify-send \"count messages = %d\"",++counter);//увеличиваем номер и посылаем сообщение
		system(str);
		sleep(12);//этого времени должно хватить, чтобы предыдущее сообщение закрылось с экрана (система его показывает 10 секунд)
	}
}

int main()
{
	daemon(0,0);
	message();
}
```