## Получение кода клавиши в формате ASCII и использованием Windows HOOK. Название клавиши выводить в MessageBox
```C
#define _CRT_SECURE_NO_WARNINGS
#include <windows.h>
#include <stdio.h>
#include <string.h>

// Функция обработки сообщений о нажатии клавиш
LRESULT CALLBACK LowLevelKeyboardProc(int nCode, WPARAM wParam, LPARAM lParam) {
        // Приведение lParam к указателю на структуру KBDLLHOOKSTRUCT
        KBDLLHOOKSTRUCT* kbStruct = (KBDLLHOOKSTRUCT*)lParam;
        // Проверяем, нажата ли клавиша (WM_KEYDOWN) или клавиша в меню (WM_SYSKEYDOWN).
        if (wParam == WM_KEYDOWN || wParam == WM_SYSKEYDOWN) {
            char asciiChar;
            // Получение кода клавиши.  Это упрощенное получение - может не работать для всех клавиш корректно.
            asciiChar = (char)kbStruct->vkCode;
        

            if (asciiChar) {
                // Вывод в MessageBox
                wchar_t message[100];
                wsprintfW(message, L"Нажата клавиша: %c", (wchar_t)asciiChar); //Используем sprintf для безопасной строки
                MessageBoxW(NULL, message, L"Клавиатурный хук", MB_OK);
            }
        }
    return CallNextHookEx(NULL, nCode, wParam, lParam);
}


int main() {
    system("chcp 1251>nul");
    HHOOK hHook;
    MSG msg;

    // Регистрация низкоуровневого хука
    hHook = SetWindowsHookEx(WH_KEYBOARD_LL, LowLevelKeyboardProc, NULL, 0);
    if (hHook == NULL) {
        fprintf(stderr, "Ошибка регистрации хука: %d\n", GetLastError());
        return 1;
    }

    // Цикл обработки сообщений
    while (GetMessage(&msg, NULL, 0, 0)) {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }

    UnhookWindowsHookEx(hHook);
    return 0;
}
```