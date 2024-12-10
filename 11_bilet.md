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
    for (int i = 0; i < 1000000; i++) {
        // Вход в критическую секцию
        EnterCriticalSection(&criticalSection);

        // Доступ к общему ресурсу.  Защита от гонки данных.
        sharedResource++;

        // Выход из критической секции
        LeaveCriticalSection(&criticalSection);
    }
    return 0;
}

int main() {
    system("chcp 1251");
    // Инициализация критической секции
    InitializeCriticalSection(&criticalSection);

    // Создание потоков
    HANDLE hThread1 = CreateThread(NULL, 0, ThreadFunc, NULL, 0, 0);
    HANDLE hThread2 = CreateThread(NULL, 0, ThreadFunc, NULL, 0, 0);

    // Ожидание завершения потоков
    WaitForSingleObject(hThread1, INFINITE);
    WaitForSingleObject(hThread2, INFINITE);
    printf("Поток: sharedResource = %d\n", sharedResource);

    // Удаление критической секции
    DeleteCriticalSection(&criticalSection);

    return 0;
}
```
