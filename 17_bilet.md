## Запись и считывание числовых параметров в системный реестр

```C
#include <Windows.h>
#include <stdlib.h>
#include <stdio.h>

int main() {
	system("chcp 1251");
	DWORD paramValue = 16;
	wchar_t paramName[] = L"testNum";
	DWORD type;
	DWORD valueSize = sizeof(DWORD);
	DWORD value = 0;
	LONG result;
	HKEY hKey = NULL;
	if (RegOpenKey(HKEY_CURRENT_USER, NULL, &hKey) == ERROR_SUCCESS)
	{

		if (RegSetValueEx(hKey, paramName, 0, REG_DWORD, &paramValue, sizeof(paramValue)) == ERROR_SUCCESS) {
			MessageBox(NULL, L"Мы создали параметр и он записал туда значение чекай", L"ЧЕК", NULL);
			result = RegGetValueW(hKey, NULL, paramName, RRF_RT_REG_DWORD, &type, &value, &valueSize);
			wprintf(L"%ws  , %lu", paramName, value);
		}
	}
}
```