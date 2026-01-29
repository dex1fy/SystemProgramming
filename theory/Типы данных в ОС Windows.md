## Типы данных в ОС Windows. Назначение и использование системных диалоговых окон.

### **Системные диалоговые окна Windows** — это готовые стандартные окна, предоставляемые операционной системой, которые программист вызывает через API функции для решения типовых задач без создания собственных окон.
Их используют, чтобы все программы в Windows имели одинаковый интерфейс, и чтобы не тратить время на создание стандартных окон.


**Типы данных в ОС Windows — это "стандартные коробки" для информации.**

---

### **Основные типы данных Windows API**

#### **1. Базовые типы (переименованные для ясности)**
```c
// Вместо обычных типов C
typedef int BOOL;           // Логический тип (TRUE/FALSE)
typedef unsigned int UINT;  // Беззнаковое целое
typedef long LONG;          // Длинное целое
typedef unsigned long DWORD;// Двойное слово (32 бита)
typedef void* HANDLE;       // Дескриптор (номерок)
typedef char CHAR;          // Символ ANSI
typedef wchar_t WCHAR;      // Символ Unicode (широкий)
```

**Зачем такие имена?** Чтобы код был понятнее и переносимее между 32/64 бит.

#### **2. Строки Windows (важно!)**
```c
// ДВА типа строк:
CHAR str1[] = "Привет";      // ANSI (старый, 1 байт на символ)
WCHAR str2[] = L"Привет";    // Unicode (новый, 2 байта на символ)

// Универсальный макрос:
TCHAR str3[] = TEXT("Привет"); // Автоматически: ANSI или Unicode
```

#### **3. Дескрипторы (HANDLE) — "номерки"**
```c
HANDLE hFile;      // Дескриптор файла
HANDLE hWindow;    // Дескриптор окна (HWND)
HANDLE hProcess;   // Дескриптор процесса
HANDLE hThread;    // Дескриптор потока
HANDLE hMutex;     // Дескриптор мьютекса
// Это просто числа, но с типом для проверки
```

#### **4. Структуры для окон**
```c
typedef struct tagRECT {  // Прямоугольник
    LONG left;
    LONG top;
    LONG right;
    LONG bottom;
} RECT;

typedef struct tagPOINT {  // Точка
    LONG x;
    LONG y;
} POINT;

typedef struct tagMSG {    // Сообщение окну
    HWND hwnd;      // Кому
    UINT message;   // Что
    WPARAM wParam;  // Параметр 1
    LPARAM lParam;  // Параметр 2
} MSG;
```

---

### **Системные диалоговые окна — "готовые окошки Windows"**

Это стандартные окна, которые Windows показывает за вас.

---

### **Назначение (Зачем нужны?)**

1. **Единый интерфейс** — во всех программах окна выглядят одинаково
2. **Экономия времени** — не нужно рисовать своё окно
3. **Надёжность** — протестированы Microsoft
4. **Доступность** — поддерживают скринридеры, высокий контраст

---

### **Основные типы системных диалогов**

#### **1. Сообщения (MessageBox) — "Спросить пользователя"**
```c
// Простейшее окно с кнопками
int result = MessageBox(
    NULL,                       // Родительское окно
    TEXT("Сохранить изменения?"), // Текст
    TEXT("Подтверждение"),      // Заголовок
    MB_YESNOCANCEL | MB_ICONQUESTION  // Кнопки + иконка
);

if (result == IDYES) {
    // Пользователь нажал "Да"
}
```

**Варианты кнопок:**
- `MB_OK` — только OK
- `MB_YESNO` — Да/Нет
- `MB_RETRYCANCEL` — Повтор/Отмена
- `MB_ABORTRETRYIGNORE` — Прервать/Повтор/Пропустить

**Иконки:**
- `MB_ICONINFORMATION` — ℹ️ информация
- `MB_ICONWARNING` — ⚠️ предупреждение
- `MB_ICONERROR` — ❌ ошибка
- `MB_ICONQUESTION` — ❓ вопрос

#### **2. Выбор файла (GetOpenFileName) — "Открыть файл"**
```c
OPENFILENAME ofn = {0};
TCHAR szFile[260] = {0};

ofn.lStructSize = sizeof(OPENFILENAME);
ofn.hwndOwner = hWnd;  // Родительское окно
ofn.lpstrFile = szFile;
ofn.nMaxFile = sizeof(szFile);
ofn.lpstrFilter = TEXT("Текстовые файлы\0*.txt\0Все файлы\0*.*\0");
ofn.nFilterIndex = 1;
ofn.lpstrTitle = TEXT("Выберите файл для открытия");

if (GetOpenFileName(&ofn)) {
    // Пользователь выбрал файл, путь в szFile
}
```

#### **3. Сохранение файла (GetSaveFileName) — "Сохранить как"**
```c
// Почти как GetOpenFileName, но для сохранения
if (GetSaveFileName(&ofn)) {
    // Куда сохранять файл
}
```

#### **4. Выбор цвета (ChooseColor) — "Палитра"**
```c
COLORREF acrCustClr[16] = {0};  // Пользовательские цвета
CHOOSECOLOR cc = {0};
cc.lStructSize = sizeof(CHOOSECOLOR);
cc.hwndOwner = hWnd;
cc.lpCustColors = acrCustClr;
cc.Flags = CC_FULLOPEN | CC_RGBINIT;
cc.rgbResult = RGB(255, 0, 0);  // Красный по умолчанию

if (ChooseColor(&cc)) {
    COLORREF color = cc.rgbResult;  // Выбранный цвет
    // color содержит RGB: 0x00BBGGRR
}
```

#### **5. Выбор шрифта (ChooseFont) — "Шрифты"**
```c
CHOOSEFONT cf = {0};
LOGFONT lf = {0};
cf.lStructSize = sizeof(CHOOSEFONT);
cf.hwndOwner = hWnd;
cf.lpLogFont = &lf;
cf.Flags = CF_SCREENFONTS | CF_EFFECTS;

if (ChooseFont(&cf)) {
    // lf содержит выбранный шрифт
    // cf.rgbColors содержит цвет
    // cf.iPointSize содержит размер в 1/10 пункта
}
```

#### **6. Печать (PrintDlg) — "Настройки печати"**
```c
PRINTDLG pd = {0};
pd.lStructSize = sizeof(PRINTDLG);
pd.hwndOwner = hWnd;
pd.Flags = PD_ALLPAGES | PD_RETURNDC;

if (PrintDlg(&pd)) {
    HDC hdcPrint = pd.hDC;  // Контекст устройства печати
    // Начать печать...
}
```

#### **7. Поиск/Замена (FindText, ReplaceText) — "Найти и заменить"**
```c
FINDREPLACE fr = {0};
TCHAR szFind[80] = TEXT("текст");

fr.lStructSize = sizeof(FINDREPLACE);
fr.hwndOwner = hWnd;
fr.lpstrFindWhat = szFind;
fr.wFindWhatLen = 80;
fr.Flags = FR_DOWN;

HWND hFindDlg = FindText(&fr);  // Окно поиска
```

---

### **Как использовать (общий принцип)**

```c
// 1. Заполнить структуру параметрами
СТРУКТУРА_DIALOG dlg = {0};
dlg.lStructSize = sizeof(СТРУКТУРА_DIALOG);
dlg.hwndOwner = моё_окно;
// ... другие параметры

// 2. Вызвать функцию диалога
if (ФункцияДиалога(&dlg)) {
    // 3. Обработать результат
    // Пользователь нажал OK
} else {
    // Пользователь нажал Cancel или закрыл
}
```

---

### **Преимущества системных диалогов**

1. **Бесплатно** — уже в Windows
2. **Знакомы пользователям** — везде одинаковые
3. **Поддержка тем оформления** — тёмная/светлая тема Windows
4. **Локализация** — на языке системы
5. **Доступность** — для людей с ограничениями

---

### **Ограничения**

1. **Внешний вид фиксированный** — нельзя сильно менять
2. **Функциональность ограничена** — только стандартные возможности
3. **Windows-only** — не работают на Linux/macOS

---

### **Пример программы с MessageBox**
```c
#include <windows.h>

int WINAPI WinMain(HINSTANCE hInst, HINSTANCE hPrevInst, LPSTR lpCmdLine, int nCmdShow) {
    int answer = MessageBox(
        NULL,
        TEXT("Вы любите программирование?"),
        TEXT("Вопрос"),
        MB_YESNO | MB_ICONQUESTION
    );
    
    if (answer == IDYES) {
        MessageBox(NULL, TEXT("Отлично!"), TEXT("Ответ"), MB_OK | MB_ICONINFORMATION);
    } else {
        MessageBox(NULL, TEXT("Жаль..."), TEXT("Ответ"), MB_OK | MB_ICONWARNING);
    }
    
    return 0;
}
```

---

### **Основные тезисы:**

**Типы данных Windows:**
1. Свои имена для переносимости (`DWORD`, `BOOL`, `HANDLE`)
2. Два типа строк: ANSI (`CHAR`) и Unicode (`WCHAR`)
3. Используйте `TCHAR` для универсальности

**Системные диалоги:**
1. Готовые окна Windows
2. Основные: `MessageBox`, `GetOpenFileName`, `ChooseColor`, `ChooseFont`
3. Всегда возвращают `TRUE/FALSE` (OK/Cancel)
4. Экономят время и обеспечивают единый интерфейс

**Проще:** Windows даёт вам "готовые кубики" (типы данных) и "готовые окна" (диалоги), чтобы не изобретать велосипед.