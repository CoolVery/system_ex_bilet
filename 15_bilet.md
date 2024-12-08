## Обработка нажатия клавиши мыши в системе (выписать в messagebox какая клавиша нажата и сколько раз)

```C
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>

// Структура для хранения данных о нажатии клавиши
typedef struct {
	int leftClicks;
	int rightClicks;
} MouseClickData;

MouseClickData clickData;

// Обработчик хука
LRESULT CALLBACK MouseHookProc(int nCode, WPARAM wParam, LPARAM lParam) {
	if (wParam == WM_LBUTTONDOWN) {
		clickData.leftClicks++;
		char message[256];
		swprintf_s(message, 256, L"Левая кнопка мыши нажата %d раз", clickData.leftClicks);
		//printf("Левый клик - %d\n", clickData.leftClicks);
		MessageBox(NULL, message, L"Нажатие мыши", MB_OK);
	}
	else if (wParam == WM_RBUTTONDOWN) {
		clickData.rightClicks++;
		char message[256];
		//printf("Правый клик - %d\n", clickData.rightClicks);
		swprintf_s(message, 256, L"Правая кнопка мыши нажата %d раз", clickData.rightClicks);
		MessageBox(NULL, message, L"Нажатие мыши", MB_OK);
	}
	return CallNextHookEx(NULL, nCode, wParam, lParam);
}

int main() {
	system("chcp 1251");
	HHOOK hHook = SetWindowsHookEx(WH_MOUSE_LL, MouseHookProc, NULL, 0);

	MSG msg;
	while (GetMessage(&msg, NULL, 0, 0)) {
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}

	UnhookWindowsHookEx(hHook);
	return 0;
}
```