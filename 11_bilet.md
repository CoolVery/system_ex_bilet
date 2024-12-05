## Создание программы с критической секцией в ОС Windows. Программа должна содержать минимум два потока. Использование критической секции в функции потока должно быть обосновано.

```C
#include <windows.h>
#include <stdio.h>

// Общий ресурс
int sharedResource = 0;

// Критическая секция
CRITICAL_SECTION criticalSection;

// Функция потока
DWORD WINAPI ThreadFunc(LPVOID lpParam) {
    int threadId = *(int*)lpParam;
    for (int i = 0; i < 1000000; i++) {
        // Вход в критическую секцию
        EnterCriticalSection(&criticalSection);

        // Доступ к общему ресурсу.  Защита от гонки данных.
        sharedResource++;
        //printf("Поток %d: sharedResource = %d\n", threadId, sharedResource);

        // Выход из критической секции
        LeaveCriticalSection(&criticalSection);
    }
    return 0;
}

int main() {
    // Инициализация критической секции
    InitializeCriticalSection(&criticalSection);

    // Создание потоков
    DWORD threadId1, threadId2;
    int threadParam1 = 1;
    int threadParam2 = 2;
    HANDLE hThread1 = CreateThread(NULL, 0, ThreadFunc, &threadParam1, 0, &threadId1);
    HANDLE hThread2 = CreateThread(NULL, 0, ThreadFunc, &threadParam2, 0, &threadId2);

    // Ожидание завершения потоков
    WaitForSingleObject(hThread1, INFINITE);
    WaitForSingleObject(hThread2, INFINITE);

    // Удаление критической секции
    DeleteCriticalSection(&criticalSection);

    printf("Значение sharedResource после завершения потоков: %d\n", sharedResource);

    return 0;
}
```