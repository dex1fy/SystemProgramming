## Создание процессов в ОС семейства Windows

**Создание процессов в Windows — запуск новой программы с нуля.**

---

### **Одна функция: `CreateProcess()`**

```c
CreateProcess("program.exe",  // Что запустить
              "аргументы",    // Параметры
              ...);           // Остальное настроить
```

**Пример:**
```c
#include <windows.h>

STARTUPINFO si = { sizeof(si) };  // Заполняем размер!
PROCESS_INFORMATION pi;

// Запускаем Блокнот
CreateProcess(NULL, "notepad.exe", NULL, NULL, 
              FALSE, 0, NULL, NULL, &si, &pi);
```

---

### **Что происходит:**

1. **Windows создаёт новый процесс** с нуля
2. **Загружает program.exe** в память
3. **Создаёт главный поток**
4. **Возвращает информацию** в `PROCESS_INFORMATION`

---

### **Чем отличается от UNIX:**

**UNIX:** `fork()` (копия себя) → `exec()` (замена на другую программу)  
**Windows:** сразу `CreateProcess()` (новая программа)

---

### **Что нужно делать после:**

```c
// 1. Ждать завершения (если нужно)
WaitForSingleObject(pi.hProcess, INFINITE);

// 2. Закрыть дескрипторы (ОБЯЗАТЕЛЬНО!)
CloseHandle(pi.hProcess);
CloseHandle(pi.hThread);
```

---

### **Простой пример: запуск и ждём**
```c
#include <windows.h>

int main() {
    STARTUPINFO si = { sizeof(si) };
    PROCESS_INFORMATION pi;
    
    // Запускаем калькулятор
    if (CreateProcess(NULL, "calc.exe", NULL, NULL, 
                     FALSE, 0, NULL, NULL, &si, &pi)) {
        
        // Ждём пока пользователь закроет калькулятор
        WaitForSingleObject(pi.hProcess, INFINITE);
        
        // Убираем за собой
        CloseHandle(pi.hProcess);
        CloseHandle(pi.hThread);
    }
    
    return 0;
}
```

---

### **Итог одной строкой:**

**В Windows: `CreateProcess()` → ждать → `CloseHandle()`. Нет клонирования, только запуск новых программ.**