## Создание и настройка проекта WinAPI. Вывод сообщения в messagebox. Текст сообщения считывается из текстового файла в кодировке Юникод

Шаг 1. Следует написать следующий код в файле main.c 

``` C
#include <stdio.h>
#include <Windows.h>

int WINAPI WinMain() {
	wchar_t buffer[200];
	DWORD size = 0;
	HANDLE fileH;
	fileH = CreateFile(L"file.txt", GENERIC_READ, FILE_SHARE_READ, 0, OPEN_ALWAYS, FILE_ATTRIBUTE_NORMAL, 0);
	if (fileH != INVALID_HANDLE_VALUE) {
		int readFile = ReadFile(fileH, buffer, 200, &size, 0);
		int numChars = size / sizeof(wchar_t);
		buffer[numChars] = L'\0';
		MessageBox(0, buffer, L"Содержимое файла", 0);
	}
}
```

Шаг 2. Рядом в файлом main.c создайте файл file.txt и внесите в него текст. 

Если будет ошибка WINAPI, то нужно:
перейти в свойства проекта -> компоновщик -> все опции -> подсистема -> поставить SUBSYSTEM WINDOWS