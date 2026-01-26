## Создание программы с критической секцией в ОС Windows. Программа должна содержать минимум два потока. Использование критической секции в функции потока должно быть обосновано.

Шаг 1. Создать следующий код в main.c файле  

``` C
#include <Windows.h>
#include <stdio.h>

int sharedCount = 0;
CRITICAL_SECTION section = { 0 };

DWORD WINAPI ThreadFunc() {
	for (int i = 0; i < 10000000; i++) {
		EnterCriticalSection(&section);
		sharedCount++;
		LeaveCriticalSection(&section);
	}
	return 0;
}

int main() {
	system("chcp 1251");
	InitializeCriticalSection(&section);
	HANDLE hF[3];
	hF[0] = CreateThread(0, 0, ThreadFunc, 0, 0, 0);
	hF[1] = CreateThread(0, 0, ThreadFunc, 0, 0, 0);
	hF[2] = CreateThread(0, 0, ThreadFunc, 0, 0, 0);
	
	WaitForMultipleObjects(3, hF, TRUE, INFINITE);
	printf("%d", sharedCount);
	DeleteCriticalSection(&section);
	
	return 0;
}
```

Если закомментировать критическую секцию, то значение переменной будет иным.