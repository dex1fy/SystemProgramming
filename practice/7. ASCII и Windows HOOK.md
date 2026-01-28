## Получение кода клавиши в формате ASCII и использованием Windows HOOK. Название клавиши выводить в MessageBox

Шаг 1. Напишем следующий код в main.c файл. 
``` C
#include <windows.h>

LRESULT CALLBACK HookProc(int code, WPARAM wParam, LPARAM lParam)
{
	if (code >= 0 && wParam == WM_KEYDOWN)
	{
		PKBDLLHOOKSTRUCT pKeyboard = lParam;
		char ASCIIChar = pKeyboard->vkCode;
	
		wchar_t symbol[100];
		wsprintf(symbol, L"%lc", (wchar_t)ASCIIChar);
		MessageBoxW(0, symbol, L"Нажатая клавиша", 0);
	}
	return CallNextHookEx(0, code, wParam, lParam); //переходим к следующему перехватчику
}


int main()
{
	system("chcp 1251");
	//создание перехватчика
	HHOOK hHook = SetWindowsHookExW(WH_KEYBOARD_LL, HookProc, GetModuleHandle(0), 0);

	MSG msg;
	while (GetMessageW(&msg, 0, 0, 0)) {
		TranslateMessage(&msg);
		DispatchMessageW(&msg);
	}

	UnhookWindowsHookEx(hHook); //удаление перехватчика
	return 0;
}
```