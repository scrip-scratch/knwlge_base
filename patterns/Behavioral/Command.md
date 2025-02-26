# 🎯 Паттерн проектирования **Команда (Command)** в TypeScript

## 🔥 Что такое паттерн **Команда**?
**Команда (Command)** — это **поведенческий паттерн**, который превращает **запросы в объекты**. Это позволяет передавать запросы **как параметры**, ставить их в очередь, логировать или даже **отменять выполненные команды**.

## 🎯 Основные цели паттерна
✔ **Разделение отправителя и получателя команды** — отправитель не знает, кто и как выполняет команду.  
✔ **Инкапсуляция запросов** — команды можно передавать, сохранять и выполнять позже.  
✔ **Легкость добавления новых команд** — код становится гибким и расширяемым.  
✔ **Возможность отмены и повторения команд** — удобно для **undo/redo**, например, в текстовых редакторах.  

---

## 🚀 **Где применяется паттерн Команда?**
- **Графические интерфейсы (GUI)** — кнопки и меню вызывают команды.  
- **Игры** — действия персонажа оформляются как команды (ходьба, атака, прыжок).  
- **Обратимые операции (Undo/Redo)** — отмена и повтор выполнения команд.  
- **Очереди задач** — выполнение задач в нужном порядке.  
- **Автоматизация процессов** — макросы и сценарии действий.  

---

# 📌 **Простой пример: Удалённый пульт управления**
Допустим, у нас есть **пульт**, который может включать и выключать **свет**.

## 1️⃣ **Создадим интерфейс команды**
```typescript
interface Command {
    execute(): void;
}
```

## 2️⃣ **Создадим класс `Light`, который будем управлять**
```typescript
class Light {
    turnOn(): void {
        console.log("💡 Свет включен");
    }

    turnOff(): void {
        console.log("🌑 Свет выключен");
    }
}
```

## 3️⃣ **Создадим конкретные команды**
```typescript
class LightOnCommand implements Command {
    private light: Light;

    constructor(light: Light) {
        this.light = light;
    }

    execute(): void {
        this.light.turnOn();
    }
}

class LightOffCommand implements Command {
    private light: Light;

    constructor(light: Light) {
        this.light = light;
    }

    execute(): void {
        this.light.turnOff();
    }
}
```

## 4️⃣ **Создадим класс `RemoteControl`, который будет выполнять команды**
```typescript
class RemoteControl {
    private command!: Command;

    setCommand(command: Command): void {
        this.command = command;
    }

    pressButton(): void {
        this.command.execute();
    }
}
```

## 5️⃣ **Используем код**
```typescript
const light = new Light();
const lightOn = new LightOnCommand(light);
const lightOff = new LightOffCommand(light);

const remote = new RemoteControl();

remote.setCommand(lightOn);
remote.pressButton(); // 💡 Свет включен

remote.setCommand(lightOff);
remote.pressButton(); // 🌑 Свет выключен
```

---

## 🔥 **Как это работает?**
1. **Объект `Light`** — это **получатель** команды, он выполняет реальную работу.  
2. **Команды `LightOnCommand` и `LightOffCommand`** — **инкапсулируют действия** включения и выключения света.  
3. **Объект `RemoteControl`** — это **инициатор**, он вызывает `execute()` у команды, но **не знает деталей**, как она работает.  

---

# 📌 **Расширенный пример: Отмена команд (Undo)**
Обычно команды **не только выполняются, но и отменяются** (например, в текстовом редакторе кнопка "Отмена").

## 1️⃣ **Расширяем интерфейс команд (`undo()`)**
```typescript
interface Command {
    execute(): void;
    undo(): void;
}
```

## 2️⃣ **Обновляем команды**
```typescript
class LightOnCommand implements Command {
    private light: Light;

    constructor(light: Light) {
        this.light = light;
    }

    execute(): void {
        this.light.turnOn();
    }

    undo(): void {
        this.light.turnOff();
    }
}

class LightOffCommand implements Command {
    private light: Light;

    constructor(light: Light) {
        this.light = light;
    }

    execute(): void {
        this.light.turnOff();
    }

    undo(): void {
        this.light.turnOn();
    }
}
```

## 3️⃣ **Добавляем поддержку "Отмены" в пульт**
```typescript
class RemoteControlWithUndo {
    private command!: Command;
    private history: Command[] = [];

    setCommand(command: Command): void {
        this.command = command;
    }

    pressButton(): void {
        this.command.execute();
        this.history.push(this.command); // Сохраняем команду в историю
    }

    pressUndo(): void {
        if (this.history.length > 0) {
            const lastCommand = this.history.pop();
            lastCommand?.undo();
        }
    }
}
```

## 4️⃣ **Тестируем Undo**
```typescript
const light = new Light();
const lightOn = new LightOnCommand(light);
const lightOff = new LightOffCommand(light);

const remote = new RemoteControlWithUndo();

remote.setCommand(lightOn);
remote.pressButton(); // 💡 Свет включен

remote.setCommand(lightOff);
remote.pressButton(); // 🌑 Свет выключен

remote.pressUndo(); // 💡 Свет включен (отменили выключение)
```

---

# ✅ **Плюсы паттерна Команда**
✔ **Отделяет отправителя от получателя** — пульт **не знает** деталей команды.  
✔ **Инкапсулирует запрос** — можно передавать, логировать и откладывать выполнение команд.  
✔ **Легко добавлять новые команды** — просто создаем новый класс, ничего не меняя в старом коде.  
✔ **Поддержка Undo/Redo** — команды могут хранить историю и отменять действия.  
✔ **Позволяет строить очереди команд** — полезно в многопоточных системах.  

---

# ❌ **Минусы паттерна Команда**
❌ **Усложняет код** — для каждой новой команды нужен **отдельный класс**.  
❌ **Перегруженность мелкими объектами** — если команд слишком много, код может стать громоздким.  

---

# 🚀 **Когда использовать паттерн Команда?**
✅ **Когда нужно передавать действия как объекты** (например, макросы, сценарии в играх).  
✅ **Когда нужна отмена и повтор команд** (текстовые редакторы, Photoshop).  
✅ **Когда нужно логировать команды** (например, для отладки).  
✅ **Когда один и тот же запрос может выполняться разными способами** (например, управление устройствами).  

---

# 🎬 **Вывод**
**Команда** — мощный инструмент, позволяющий сделать код более гибким, расширяемым и удобным для логирования или отмены действий.  

📌 **Главное помнить:**
- Этот паттерн **инкапсулирует команды в объекты**.  
- Позволяет **передавать, сохранять, отменять и комбинировать команды**.  
- Хорошо подходит для **GUI, игр, многопоточных систем, макросов**.  

🔥 **Используй этот паттерн, если хочешь сделать код модульным и управляемым!** 🚀  
