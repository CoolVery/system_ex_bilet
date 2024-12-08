## Запись и считывание строковых параметров в системный реестр.

```C
#include <Windows.h>
#include <stdlib.h>
#include <stdio.h>

int main() {
	system("chcp 1251");
	wchar_t paramValue[] = L"TEEEST";
	wchar_t paramName[] = L"test";
	DWORD type;
	DWORD valueSize = 0;
	PVOID value;
	LONG result;
	HKEY hKey = NULL;
	if (RegOpenKey(HKEY_CURRENT_USER, NULL, &hKey) == ERROR_SUCCESS)
	{

		if (RegSetValueEx(hKey, paramName, 0, REG_SZ, paramValue, 7 * sizeof(WCHAR)) == ERROR_SUCCESS) {
			MessageBox(NULL, L"Мы создали параметр и он записал туда значение чекай", L"ЧЕК", NULL);
		}
		
		result = RegGetValueW(hKey, NULL, paramName, RRF_RT_REG_SZ, &type, NULL, &valueSize);
		if (result == ERROR_SUCCESS) {
			value = malloc(valueSize);
			if (value == NULL) {
				wprintf(L"Ошибка выделения памяти!\n");
				RegCloseKey(hKey);
				return 1;
			}

			result = RegGetValueW(hKey, NULL, paramName, RRF_RT_REG_SZ, &type, value, &valueSize);
			wprintf(L"%ws  , %ws", paramName, (wchar_t*)value);



		}
	}
}
```