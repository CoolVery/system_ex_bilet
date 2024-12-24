## Обработка нажатия клавиши мыши в системе (выписать в messagebox какая клавиша нажата и сколько раз)

Старое
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
Обновил и добавил обработку колесика мыши + вынул в другую функцию (Мухин смотрит на это)

```C
typedef struct {
	int leftClick;
	int rightClick;
	int centerClick;
} ClicksMouse;

ClicksMouse structCountClick;

void SayCount(int count, LPWSTR clickDown) {
	char buf[200];
	swprintf(buf, 200, L"Нажата клавиша: %ls\nНажата столько раз: %d", clickDown, count);
	MessageBox(NULL, buf, L"Внимание", MB_OK);
}

LRESULT CALLBACK countClick(int n, WPARAM w, LPARAM l) {
	switch (w)
	{
	case WM_LBUTTONDOWN:
		structCountClick.leftClick++;
		SayCount(structCountClick.leftClick, L"Левая клавиша мыши");
		break;
	case WM_RBUTTONDOWN:
		structCountClick.rightClick++;
		SayCount(structCountClick.rightClick, L"Правая клавиша мыши");
		break;
	case WM_MBUTTONDOWN:
		structCountClick.centerClick++;
		SayCount(structCountClick.centerClick, L"Колесо мыши");
		break;
	default:
		break;
	}
	return CallNextHookEx(NULL, n, w, l);
}

int main() {
	HHOOK hook = SetWindowsHookEx(WH_MOUSE_LL, countClick, GetModuleHandle(NULL), 0);
	if (hook != NULL) {
		MSG msg;
		while (GetMessage(&msg, NULL, 0, 0)) {
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
		UnhookWindowsHookEx(hook);
	}
}
```
