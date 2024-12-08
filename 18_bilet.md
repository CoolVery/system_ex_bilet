## Разработка программы-секундомера. в одном потоке идет отсчет времени, а в другом осуществляется управление секундомером (отдельно для Windows и Linux)
Для винды
```C
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

struct ThreadArgs {
    int hours;
    int minutes;
    int seconds;
    int milisec;
};
struct ThreadArgs globalTime;

int flag = 0;
int flagSecund = 0;
int flagTimer = 0;

DWORD StartSecundomer(LPVOID param) {
    while (1) {
        globalTime.milisec++;
        if (globalTime.milisec == 1000)
        {
            globalTime.milisec = 0;
            globalTime.seconds++;
            if (globalTime.seconds == 60) {
                globalTime.seconds = 0;
                globalTime.minutes++;
                if (globalTime.minutes == 60) {
                    globalTime.minutes = 0;
                    globalTime.hours++;
                }
            }
           
        }
        flag = 1;

        Sleep(1);
    }
}

DWORD PrintSecundomer(LPVOID param) {
	while (1) {
		if (flag) {
			printf("Секундомер: %d:%d:%d:%d\n", globalTime.hours, globalTime.minutes, globalTime.seconds, globalTime.milisec);
			flag = 0;
		}
	}

}

int main() {
    system("chcp 1251");
    HANDLE hMain[2];
    
    printf("Для старта секундомера нажмите 0\nДля паузы секундомера нажмите 1\nДля возобновления таймера нажмите 2\nДля завершения секундомера нажмите 3\nДля сброса секундомера нажмите 4\nДля завершения программы нажмите 5\n");
    int choise = 0;
    while (1) {
        scanf("%d", &choise);
        switch (choise)
        {
        case 0:
            hMain[0] = CreateThread(NULL, 0, StartSecundomer, NULL, 0, 0);
            hMain[1] = CreateThread(NULL, 0, PrintSecundomer, NULL, 0, 0);
            break;
        case 1:
            SuspendThread(hMain[0]);
            SuspendThread(hMain[1]);
            break;
        case 2:
            ResumeThread(hMain[0]);
            ResumeThread(hMain[1]);
            break;
        case 3:
            TerminateThread(hMain[0], 0);
            TerminateThread(hMain[1], 0);
            printf("Итоговый Секундомер: %d:%d:%d:%d\n", globalTime.hours, globalTime.minutes, globalTime.seconds, globalTime.milisec);
            globalTime.hours = 0; 
            globalTime.minutes = 0; 
            globalTime.seconds = 0; 
            globalTime.milisec = 0;
            break;
        case 4:
            globalTime.hours = 0;
            globalTime.minutes = 0;
            globalTime.seconds = 0;
            globalTime.milisec = 0;
            break;
        case 5:
            return 0;
            break;

        default:
            break;
        }
    }
}
```

Linux
```C
#define _CRT_SECURE_NO_WARNINGS
#include <stdlib.h>
#include <time.h>
#include <unistd.h>
#include <pthread.h>

struct Secundomer {
	int hours;
	int minutes;
	int seconds;
	int miliseconds;
};
struct Secundomer globalTime;

int flagRunSecundomer = 0;
int flag = 0;

static void *SecondomerCount() {
	while (1) {
		if (flagRunSecundomer) {
			globalTime.miliseconds++;
			if (globalTime.miliseconds == 1000) {
				globalTime.miliseconds = 0;
				globalTime.seconds++;
				if (globalTime.seconds == 60) {
					globalTime.seconds = 0;
					globalTime.minutes++;
					if (globalTime.minutes == 60) {
						globalTime.minutes = 0;
						globalTime.hours++;
					}
				}
			}
		flag = 1;
		usleep(1);
		}
	}
}

static void *PrintSecundomer() {
	while(1) {
		if (flag) {
			printf("Секундомер: %d:%d:%d:%d\n", globalTime.hours, globalTime.minutes, globalTime.seconds, globalTime.miliseconds);
			flag = 0;
		}
	}
}

int main() {
	pthread_t hMainTimerCommon;
	pthread_t hMainPrintTimerCommon;
	printf("Для старта секундомера нажмите 0\nДля паузы секундомера нажмите 1\nДля возобновления таймера нажмите 2\nДля завершения секундомера нажмите 3\nДля сброса секундомера нажмите 4\nДля завершения программы нажмите 5\n");
	int choise = 9;
	while (1) {
		scanf("%d", &choise);
		switch (choise) {
		case 0:
			flagRunSecundomer = 1;
			int hMainTC = pthread_create(&hMainTimerCommon, NULL, SecondomerCount, NULL);
			int hMainPTC = pthread_create(&hMainTimerCommon, NULL, PrintSecundomer, NULL);
		break;
		case 1:
			flagRunSecundomer = 0;
		break;
		case 2:
			flagRunSecundomer = 1;
		break;
		case 3:
			flagRunSecundomer = 0;
			printf("Итоговый Секундомер: %d:%d:%d:%d\n", globalTime.hours, globalTime.minutes, globalTime.seconds, globalTime.miliseconds);
			globalTime.hours = 0;
			globalTime.minutes = 0;
			globalTime.seconds = 0;
			globalTime.miliseconds = 0;
		break;
		case 4:
			globalTime.hours = 0;
			globalTime.minutes = 0;
			globalTime.seconds = 0;
			globalTime.miliseconds = 0;
		break;
		case 5:
			return 0;
		break;
		default:
		break;
		}
	}
}
```