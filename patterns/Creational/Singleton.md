[назад](/readme.md)

# 🎯 Паттерн проектирования **Одиночка (Singleton)** в TypeScript

## 🔥 Что такое Singleton?
**Одиночка (Singleton)** — это паттерн **порождающего типа**, который гарантирует, что у класса **есть только один экземпляр**, и предоставляет к нему **глобальную точку доступа**.

## 🎯 Цели паттерна Одиночка
✔ **Гарантия единственного экземпляра класса**.  
✔ **Контроль над доступом** к этому объекту.  
✔ **Глобальная точка доступа** (не нужно передавать объект между модулями).  
✔ **Экономия памяти** (повторное использование одного объекта вместо создания новых).  

---

## 🚀 **Где используется?**
- **Логирование** (один логгер на всё приложение).  
- **Работа с кэшом** (чтобы не создавать новый кэш при каждом вызове).  
- **Настройки приложения** (единый конфигурационный объект).  
- **База данных** (например, один экземпляр подключения к БД).  
- **Глобальный обработчик событий**.  

---

## 📌 **Простая реализация Singleton в TypeScript**
```typescript
class Singleton {
    private static instance: Singleton;

    private constructor() {
        console.log("Создан новый экземпляр Singleton");
    }

    public static getInstance(): Singleton {
        if (!Singleton.instance) {
            Singleton.instance = new Singleton();
        }
        return Singleton.instance;
    }

    public showMessage(): void {
        console.log("Hello from Singleton!");
    }
}

// Использование:
const obj1 = Singleton.getInstance();
const obj2 = Singleton.getInstance();

console.log(obj1 === obj2); // true (один и тот же объект)

obj1.showMessage(); // Hello from Singleton!
```

### 🔍 Как это работает?
1. **Конструктор `private`** — запрещает создавать объект через `new Singleton()`.  
2. **Статическое поле `instance`** — хранит единственный экземпляр.  
3. **Метод `getInstance()`**:
   - Если объект ещё **не создан**, создаёт его.  
   - Если уже **существует**, возвращает его.  

---

## 📌 **Улучшенный вариант с ленивой инициализацией**
Ленивая инициализация — это когда объект создаётся **только при первом запросе**.

```typescript
class Singleton {
    private static instance: Singleton | null = null;

    private constructor() {
        console.log("Создан Singleton");
    }

    public static getInstance(): Singleton {
        if (this.instance === null) {
            this.instance = new Singleton();
        }
        return this.instance;
    }

    public doSomething(): void {
        console.log("Выполняем работу в Singleton");
    }
}

// Проверяем:
const singletonA = Singleton.getInstance();
const singletonB = Singleton.getInstance();

console.log(singletonA === singletonB); // true
```

🔹 **Отличие от первого примера**: здесь `instance` может быть `null`, что чётко обозначает его отсутствие перед первым вызовом.

---

## 📌 **Потокобезопасный Singleton (для многопоточных сред)**
Если используется **многопоточность** (например, сервер с Node.js), создание объекта **может произойти дважды** в разных потоках. Чтобы избежать этого, можно использовать блокировки:

```typescript
class ThreadSafeSingleton {
    private static instance: ThreadSafeSingleton;
    private static lock: boolean = false;

    private constructor() {}

    public static getInstance(): ThreadSafeSingleton {
        if (!this.instance) {
            if (!this.lock) {
                this.lock = true; // Блокируем поток
                this.instance = new ThreadSafeSingleton();
            }
        }
        return this.instance;
    }

    public show(): void {
        console.log("Я единственный экземпляр!");
    }
}
```
### 🔥 **Как это работает?**
- Добавили флаг `lock`, который предотвращает **двойное создание** объекта.  
- Теперь Singleton **безопасен для многопоточной среды**.  

---

## 📌 **Использование Singleton в реальном мире**
### **1. Логирование (Logger)**
```typescript
class Logger {
    private static instance: Logger;
    private logs: string[] = [];

    private constructor() {}

    public static getInstance(): Logger {
        if (!Logger.instance) {
            Logger.instance = new Logger();
        }
        return Logger.instance;
    }

    public log(message: string): void {
        this.logs.push(message);
        console.log(`Лог: ${message}`);
    }

    public showLogs(): void {
        console.log(this.logs);
    }
}

// Использование:
const logger1 = Logger.getInstance();
logger1.log("Первый лог");

const logger2 = Logger.getInstance();
logger2.log("Второй лог");

console.log(logger1 === logger2); // true
logger2.showLogs(); // ['Первый лог', 'Второй лог']
```
**📌 Здесь один Logger хранит все логи, потому что он Singleton.**

---

### **2. Конфигурация приложения**
```typescript
class Config {
    private static instance: Config;
    private settings: { [key: string]: string } = {};

    private constructor() {}

    public static getInstance(): Config {
        if (!Config.instance) {
            Config.instance = new Config();
        }
        return Config.instance;
    }

    public set(key: string, value: string): void {
        this.settings[key] = value;
    }

    public get(key: string): string | undefined {
        return this.settings[key];
    }
}

// Использование:
const config = Config.getInstance();
config.set("theme", "dark");

const anotherConfig = Config.getInstance();
console.log(anotherConfig.get("theme")); // dark
```
**📌 Оба объекта `config` и `anotherConfig` — это один и тот же экземпляр!**

---

## ✅ **Плюсы паттерна Одиночка**
✔ **Гарантирует единственный экземпляр**.  
✔ **Контролирует доступ к объекту**.  
✔ **Экономит ресурсы** (используется один объект вместо множества).  
✔ **Упрощает управление состоянием** (например, логирование, кэш, настройки).  

---

## ❌ **Минусы паттерна Одиночка**
❌ **Антипаттерн в некоторых случаях** — Singleton **может скрывать зависимости**, затрудняя тестирование.  
❌ **Сложности с многопоточностью** — в некоторых языках (Java, C#) нужно дополнительно синхронизировать доступ.  
❌ **Глобальная переменная под капотом** — если использовать Singleton везде, код может стать менее предсказуемым.  

---

## 🎯 **Когда использовать Singleton?**
✅ Если нужно **единое состояние** в приложении (настройки, кэш, логгер).  
✅ Если объект **должен существовать в единственном экземпляре** (например, соединение с базой данных).  
✅ Если требуется **глобальная точка доступа**.  

---

## 🚀 **Вывод**
**Одиночка** — это мощный инструмент, но использовать его нужно **с умом**.  
Он отлично подходит для **логирования, хранения настроек, управления ресурсами**, но злоупотребление им может привести к сложностям в коде.  

💡 **Используй его, когда действительно нужен один-единственный объект в системе!** 🚀  
