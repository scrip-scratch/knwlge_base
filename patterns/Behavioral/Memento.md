# 📸 **Паттерн "Снимок" (Memento) в TypeScript**  

## 🎯 **Что такое "Снимок" (Memento)?**  

**"Снимок" (Memento)** — это **поведенческий паттерн**, который позволяет сохранять **состояние объекта** и **восстанавливать его** при необходимости.  

📌 **Проще говоря:**  
- Используется для **реализации отмены действий (Ctrl + Z)**.  
- Позволяет **сохранить состояние** объекта, не раскрывая его **внутреннюю структуру**.  
- Убирает **жёсткую связанность** между объектом и его историей изменений.  

---

## 🔥 **Когда использовать "Снимок"?**  

✅ **Нужно реализовать откат действий (undo/redo)** — например, в текстовом редакторе или игре.  
✅ **Нужно сохранять историю состояний объекта** без раскрытия его деталей.  
✅ **Объект может изменяться, но иногда нужно вернуться к старой версии**.  

---

## 🚨 **Проблема без "Снимка"**  

Допустим, у нас есть **редактор текста**. Нам нужно сохранять его **предыдущие версии** для функции "Отмена" (Undo).  

❌ **Без паттерна "Снимок" приходится хранить все старые версии прямо в объекте**:  

```typescript
class TextEditor {
    private text: string = "";
    private history: string[] = [];

    write(text: string): void {
        this.history.push(this.text); // Сохраняем текущее состояние перед изменением
        this.text = text;
    }

    undo(): void {
        this.text = this.history.pop() || "";
    }

    getText(): string {
        return this.text;
    }
}
```

📌 **Проблема:**  
- История изменений **хранится внутри объекта** (нарушает инкапсуляцию).  
- Если нужно **разделить логику редактирования и хранения истории**, код станет сложным.  

---

## ✅ **Используем "Снимок" (Memento)**  

Разобьём код на три части:  
1️⃣ **Создатель (Originator)** – объект, чьё состояние мы сохраняем.  
2️⃣ **Снимок (Memento)** – класс, который хранит сохранённое состояние.  
3️⃣ **Хранилище (Caretaker)** – управляет историей снимков.  

---

### **1️⃣ Создаём класс "Снимок" (Memento)**  

```typescript
class Memento {
    constructor(private state: string) {}

    getState(): string {
        return this.state;
    }
}
```

📌 **Этот класс просто хранит состояние объекта.**  

---

### **2️⃣ Создаём "Создателя" (Originator) – редактор текста**  

```typescript
class TextEditor {
    private text: string = "";

    write(text: string): void {
        this.text = text;
    }

    save(): Memento {
        return new Memento(this.text);
    }

    restore(memento: Memento): void {
        this.text = memento.getState();
    }

    getText(): string {
        return this.text;
    }
}
```

📌 **Теперь редактор может сохранять своё состояние и восстанавливать его из снимка.**  

---

### **3️⃣ Создаём "Хранителя" (Caretaker) – менеджер истории изменений**  

```typescript
class History {
    private mementos: Memento[] = [];

    addMemento(memento: Memento): void {
        this.mementos.push(memento);
    }

    getMemento(index: number): Memento | null {
        return this.mementos[index] || null;
    }
}
```

📌 **Этот класс управляет историей снимков, но не знает деталей редактора.**  

---

### **4️⃣ Используем "Снимок"**  

```typescript
const editor = new TextEditor();
const history = new History();

editor.write("Первый текст");
history.addMemento(editor.save());

editor.write("Второй текст");
history.addMemento(editor.save());

editor.write("Третий текст");

console.log("Текущий текст:", editor.getText()); // Третий текст

// Откат к предыдущей версии
editor.restore(history.getMemento(1)!);
console.log("Откат назад:", editor.getText()); // Второй текст

// Откат к самой первой версии
editor.restore(history.getMemento(0)!);
console.log("Откат к первой версии:", editor.getText()); // Первый текст
```

---

### 🖥 **Вывод в консоль**  

```
Текущий текст: Третий текст
Откат назад: Второй текст
Откат к первой версии: Первый текст
```

📌 **Теперь редактор может легко откатываться к любому сохранённому состоянию!**  

---

## 🚀 **Другие примеры использования "Снимка"**  

### **1️⃣ Игра с сохранением прогресса**  
Игрок может сохранять прогресс и загружать его при необходимости.  

```typescript
class GameSave {
    constructor(private level: number, private health: number) {}

    getState(): { level: number; health: number } {
        return { level: this.level, health: this.health };
    }
}

class Game {
    private level: number = 1;
    private health: number = 100;

    play(): void {
        this.level++;
        this.health -= 10;
        console.log(`Играем... Уровень: ${this.level}, Здоровье: ${this.health}`);
    }

    save(): GameSave {
        return new GameSave(this.level, this.health);
    }

    load(save: GameSave): void {
        const state = save.getState();
        this.level = state.level;
        this.health = state.health;
        console.log(`Загружено: Уровень: ${this.level}, Здоровье: ${this.health}`);
    }
}

// Использование
const game = new Game();
const saveHistory = new History();

game.play(); // Уровень 2, Здоровье 90
saveHistory.addMemento(game.save());

game.play(); // Уровень 3, Здоровье 80

game.load(saveHistory.getMemento(0)!); // Откат к уровню 2, здоровье 90
```

📌 **Теперь можно сохранять и загружать состояние игры!**  

---

## 🔁 **Где можно применять "Снимок"?**  

✅ **Редакторы текста и графики** – сохранение истории изменений, откат действий.  
✅ **Игры** – система сохранений.  
✅ **Сложные объекты с изменяющимися параметрами** – например, **формы, настройки приложений**.  
✅ **Финансовые системы** – восстановление транзакций при сбоях.  

---

## 🎬 **Вывод**  

✅ **"Снимок" позволяет сохранять состояние объекта и восстанавливать его при необходимости**.  
✅ **Отделяет логику редактирования от логики хранения истории**.  
✅ **Используется в системах отката изменений (Undo/Redo), сохранения данных и игр**.  

🚀 **Где использовать?**  
- Текстовые и графические редакторы (**Ctrl+Z, Ctrl+Y**).  
- Игры (система **сохранений**).  
- Финансовые и бэкап-системы.  
