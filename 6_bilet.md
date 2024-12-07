## Получение названия клавиши с использованием функции GetKeyNameText (Windows HOOK). Название клавиши выводить в MessageBox

```C
#include <windows.h>
#include <winuser.h>

// Процедура обработки хука
LRESULT CALLBACK KeyboardProc(int nCode, WPARAM wParam, LPARAM lParam) {
    if (nCode >= 0 && wParam == WM_KEYDOWN) {
        KBDLLHOOKSTRUCT* pKeyboard = (KBDLLHOOKSTRUCT*)lParam;
        char keyName[256] = { 0 }; // Буфер для имени клавиши

        // Получаем имя клавиши.  Обработка ошибки необходима!
        int result = GetKeyNameText(pKeyboard->scanCode << 16, keyName, 256);
        if (result == 0) {
            // Обработка ошибки: GetKeyNameText вернула 0
            MessageBoxA(NULL, "Ошибка получения имени клавиши!", "Ошибка", MB_ICONERROR);
        }
        else {
            // Вывод имени клавиши в MessageBox
            char message[512];
            sprintf_s(message, sizeof(message), "Нажата клавиша: %s", keyName);
            MessageBoxA(NULL, message, "Нажатая клавиша", MB_OK);
        }
    }
    return CallNextHookEx(NULL, nCode, wParam, lParam);
}


int main() {
    HHOOK hHook = SetWindowsHookEx(WH_KEYBOARD_LL, KeyboardProc, GetModuleHandle(NULL), 0);

    if (hHook == NULL) {
        MessageBoxA(NULL, "Ошибка установки хука!", "Ошибка", MB_ICONERROR);
        return 1;
    }

    MSG msg;
    while (GetMessage(&msg, NULL, 0, 0)) {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }

    UnhookWindowsHookEx(hHook);
    return 0;
}
```