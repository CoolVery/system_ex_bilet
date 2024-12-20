## Использование условных блоков для анализа значений, возвращаемых системной функцией (продемонстрировать и для Windows, и для Linux, не менее трех функций для каждой ОС)

Windows

```C
#include <windows.h>
#include <stdio.h>
#include <io.h> // для _access


int main() {
    HANDLE hFile;
    DWORD dwBytesWritten;
    SYSTEM_INFO sysInfo;
    DWORD lastError;

    // 1. CreateFile
    //CREATE_NEW вернет ошибку, если файл существует, так что сначала просто надо создать файл
    hFile = CreateFile(L"test.txt", GENERIC_WRITE, 0, NULL, CREATE_NEW, FILE_ATTRIBUTE_NORMAL, NULL);
    if (hFile == INVALID_HANDLE_VALUE) {
        lastError = GetLastError();
        printf("CreateFile failed: %lu\n", lastError);
    }
    else {
        printf("CreateFile succeeded. Handle: %p\n", hFile);
        CloseHandle(hFile);
    }

    // 2. WriteFile
    //Файл создан только для чтения
    hFile = CreateFile(L"test.txt", GENERIC_READ, 0, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, NULL);
    if (hFile != INVALID_HANDLE_VALUE) {
        wchar_t data[] = L"Hello, world!";
        if (!WriteFile(hFile, data, (DWORD)(sizeof(data) - sizeof(wchar_t)), &dwBytesWritten, NULL)) {
            lastError = GetLastError();
            printf("WriteFile failed: %lu\n", lastError);
        }
        else {
            printf("WriteFile succeeded. Bytes written: %lu\n", dwBytesWritten);
        }
        CloseHandle(hFile);
    }
    else {
        lastError = GetLastError();
        printf("CreateFile failed (WriteFile test): %lu\n", lastError);
    }


    // 3. ReadFile
    //Файл создал только для записи
    hFile = CreateFile(L"test.txt", GENERIC_WRITE, 0, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, NULL);
    if (hFile != INVALID_HANDLE_VALUE) {
        wchar_t buffer[200];
        DWORD count_read = 0;
        if (!ReadFile(hFile, buffer, 100, &count_read, NULL)) {
            lastError = GetLastError();
            printf("ReadFile failed: %lu\n", lastError);
        }
        else {
            printf("ReadFile succeeded. Bytes readen: %lu\n", dwBytesWritten);
        }
        CloseHandle(hFile);
    }
    else {
        lastError = GetLastError();
        printf("CreateFile failed (ReadFile test): %lu\n", lastError);
    }

    return 0;
}
```

Linux

```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <errno.h>

int main() {
	int file;
	//Без O_CREATE он выдаст ошибку, если файла не существует
	file = open("test.txt", O_RDWR | O_TRUNC, 0644);
	if (file == -1) {
		perror("open");
	} else {
		printf("open succeeded. File descriptor: %d\n", file);
		close(file);
	}
	//Выдаст ошибку, потому что файл только для записи
	file = open ("testRead.txt", O_WRONLY | O_CREAT, 0644);
	char buffer[200];
	if (file == -1) {
		perror("open");
	} else {
		int readFile = read(file, buffer, 100);
		if (readFile == -1) {
			perror("read");
		} else {
			printf("%s", buffer);
		}
	close(file);
	}
	//Выдаст ошибку, потому что файл только для чтения
	file = open ("testWrite.txt", O_RDONLY | O_CREAT, 0644);
	char bufferW[200];
	if (file == -1) {
		perror("open");
	} else {
		int writeFile = write(file, bufferW, 100);
		if (writeFile == -1) {
			perror("write");
		} else {
			printf("All write");
		}
		close(file);
	}
}
```