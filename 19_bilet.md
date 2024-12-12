## Разработка программы для вычисления факториала в отдельном потоке. В программе должна быть предусмотрена валидация данных (отдельно для Windows и Linux)

## Windows

```C

#include <windows.h>
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

// Функция для вычисления факториала
long long factorial(int n) {
    if (n < 0) {
        return -1; // Ошибка: факториал от отрицательного числа не определен
    }
    else if (n == 0) {
        return 1;
    }
    else {
        long long result = 1;
        for (int i = 1; i <= n; i++) {
            result *= i;
            if (result < 0) { // Проверка на переполнение
                return -2; // Ошибка: переполнение
            }
        }
        return result;
    }
}

// Функция потока
void* factorialThread(void* arg) {
    int n = *(int*)arg;
    long long result = factorial(n);
    if (result == -1) {
        printf("Ошибка: факториал от отрицательного числа не определен\n");
    }
    else if (result == -2) {
        printf("Ошибка: переполнение\n");
    }
    else {
        printf("Факториал %d! = %lld\n", n, result);
    }
    return NULL;
}

int main() {
    system("chcp 1251");
    int n;
    char inputBuffer[100];
    // Валидация данных (Windows):
    printf("Введите неотрицательное целое число: \n");
    fgets(inputBuffer, sizeof(inputBuffer), stdin);

    for (size_t i = 0; i < strlen(inputBuffer) - 1; i++) {
        if (!isdigit(inputBuffer[i])) {
            printf("Ошибка: ввод содержит недопустимые символы.\n");
            return 1;
        }
    }
    // Преобразование строки в число
    n = atoi(inputBuffer);


    HANDLE hThread = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)factorialThread, &n, 0, NULL);
    if (hThread == NULL) {
        perror("CreateThread failed");
        return 1;
    }
    WaitForSingleObject(hThread, INFINITE);
    CloseHandle(hThread);

    return 0;
}
```

---

## Linux

```C
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

// Функция для вычисления факториала
long long factorial(int n) {
    if (n < 0) {
        return -1; // Ошибка: факториал от отрицательного числа не определен
    } else if (n == 0) {
        return 1;
    } else {
        long long result = 1;
        for (int i = 1; i <= n; i++) {
            result *= i;
            if (result < 0) { // Проверка на переполнение
            return -2; // Ошибка: переполнение
            }
        }
    return result;
    }
}

// Функция потока
void* factorialThread(void* arg) {
    int n = *(int*)arg;
    long long result = factorial(n);
    if (result == -1) {
        printf("Ошибка: факториал от отрицательного числа не определен\n");
    } else if (result == -2) {
        printf("Ошибка: переполнение\n");
    } else {
        printf("Факториал %d! = %lld\n", n, result);
    }
    free(arg); // Освобождаем память
    return NULL;
}

int main() {
    int n;
    // Валидация данных (Linux):
    printf("Введите неотрицательное целое число: ");
    if (scanf("%d", &n) != 1 || n < 0) {
        printf("Ошибка: некорректный ввод.\n");
        return 1;
    }
    int *n_ptr = (int*)malloc(sizeof(int));
    *n_ptr = n;
    pthread_t thread;
    int rc = pthread_create(&thread, NULL, factorialThread, n_ptr);
    if (rc) {    
        fprintf(stderr, "Error: pthread_create() rc = %d\n", rc);
        return 1;
}
    pthread_join(thread, NULL);
    return 0;
}
```
gcc factorial.c -o factorial -pthread
