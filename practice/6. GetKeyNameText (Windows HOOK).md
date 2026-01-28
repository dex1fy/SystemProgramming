## Получение названия клавиши с использованием функции GetKeyNameText (Windows HOOK). Название клавиши выводить в MessageBox

Шаг 1. Создать файл main.c с следующим кодом.

``` C
#include <Windows.h>

LRESULT CALLBACK HookProc(int code, WPARAM wParam, LPARAM lParam) {
	if (code >= 0 && wParam == WM_KEYDOWN) {
		PKBDLLHOOKSTRUCT pKeyboard = lParam;
		char* buffer[100];
		if (GetKeyNameTextA(pKeyboard->scanCode << 16, buffer, 100) != 0){
			MessageBoxA(0, buffer, "Title", 0);
		}
	}
	else {
		CallNextHookEx(0, code, wParam, lParam);
	}
}

int main() {
	HHOOK hook = SetWindowsHookExA(WH_KEYBOARD_LL, HookProc, GetModuleHandleW(0), 0);
	if (hook != 0) {
		MSG msg;
		while (GetMessageW(&msg, 0, 0, 0)) {
			TranslateMessage(&msg);
			DispatchMessageW(&msg);
		}
		UnhookWindowsHookEx(hook);
	}
}
```