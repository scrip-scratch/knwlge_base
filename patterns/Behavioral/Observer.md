# 👀 **Паттерн "Наблюдатель" (Observer) в TypeScript**  

## 🎯 **Что такое паттерн "Наблюдатель"?**  

**Наблюдатель (Observer)** — это **поведенческий паттерн**, который создаёт **механизм подписки**, позволяя одним объектам автоматически реагировать на изменения другого объекта.  

📌 **Простыми словами:**  
- Есть **издатель** (Publisher), который уведомляет подписчиков.  
- Есть **подписчики** (Observers), которые подписываются на изменения издателя.  
- Когда **издатель меняется**, он сообщает об этом всем подписчикам.  

---

## 🔥 **Когда использовать Наблюдатель?**  

✅ **Когда нужно уведомлять множество объектов о событиях в одном объекте** (например, уведомления в соцсетях 📩).  
✅ **Когда объекты должны быть слабо связаны** (издатель не знает, кто на него подписан).  
✅ **Когда нужно реагировать на изменения объекта без постоянной проверки** (например, изменение данных в UI 🔄).  

---

## 📌 **Пример без паттерна "Наблюдатель" (Проблема)**  

Допустим, у нас есть **YouTube-канал**, у которого есть подписчики.  

### ❌ **Проблемы такого кода:**  
- **Жёсткая зависимость** – канал должен знать обо всех подписчиках.  
- **Невозможно расширять** систему без изменения кода канала.  
- **Проблемы с масштабируемостью** – сложно добавить новых подписчиков.  

```typescript
class YouTubeChannel {
    private subscribers: string[] = [];

    subscribe(user: string) {
        this.subscribers.push(user);
    }

    notify(title: string) {
        for (const user of this.subscribers) {
            console.log(`📢 Уведомление для ${user}: Новое видео "${title}"!`);
        }
    }
}

// Используем канал
const channel = new YouTubeChannel();
channel.subscribe("Иван");
channel.subscribe("Мария");

channel.notify("Как выучить TypeScript?");
// 📢 Уведомление для Иван: Новое видео "Как выучить TypeScript"?!
// 📢 Уведомление для Мария: Новое видео "Как выучить TypeScript"?!
```

🚨 **Что если у нас 1000 подписчиков?**  
📌 **Лучше использовать "Наблюдатель", чтобы сделать систему гибче!**  

---

## ✅ **Используем паттерн "Наблюдатель"**  

### 1️⃣ **Создаём интерфейсы для подписчиков и издателей**  

```typescript
// Интерфейс подписчика
interface Observer {
    update(videoTitle: string): void;
}

// Интерфейс издателя
interface Subject {
    subscribe(observer: Observer): void;
    unsubscribe(observer: Observer): void;
    notify(videoTitle: string): void;
}
```

### 2️⃣ **Создаём конкретный класс подписчика**  

```typescript
class User implements Observer {
    constructor(private name: string) {}

    update(videoTitle: string): void {
        console.log(`📩 ${this.name}, новое видео: "${videoTitle}"!`);
    }
}
```

### 3️⃣ **Создаём класс YouTube-канала (издатель)**  

```typescript
class YouTubeChannel implements Subject {
    private subscribers: Observer[] = [];

    subscribe(observer: Observer): void {
        this.subscribers.push(observer);
    }

    unsubscribe(observer: Observer): void {
        this.subscribers = this.subscribers.filter(sub => sub !== observer);
    }

    notify(videoTitle: string): void {
        for (const subscriber of this.subscribers) {
            subscriber.update(videoTitle);
        }
    }
}
```

### 4️⃣ **Используем паттерн**  

```typescript
// Создаём канал
const channel = new YouTubeChannel();

// Создаём подписчиков
const ivan = new User("Иван");
const maria = new User("Мария");

// Подписываемся на канал
channel.subscribe(ivan);
channel.subscribe(maria);

// Выход нового видео
channel.notify("Как выучить TypeScript?");

// 📩 Иван, новое видео: "Как выучить TypeScript"?!
// 📩 Мария, новое видео: "Как выучить TypeScript"?!

// Мария отписалась
channel.unsubscribe(maria);

// Новое видео
channel.notify("SOLID в JavaScript");

// 📩 Иван, новое видео: "SOLID в JavaScript"!
```

---

## 🔥 **Что мы получили?**  

✅ **Гибкость** – подписчики могут добавляться и удаляться **на лету**.  
✅ **Отсутствие жёсткой зависимости** – YouTube-канал **не знает конкретные классы подписчиков**.  
✅ **Хорошая масштабируемость** – легко добавлять новых подписчиков.  

---

## 📌 **Другой пример: Чат с подписчиками**  

### 1️⃣ **Создаём интерфейсы**  

```typescript
interface ChatObserver {
    update(message: string): void;
}

interface ChatSubject {
    join(observer: ChatObserver): void;
    leave(observer: ChatObserver): void;
    sendMessage(message: string): void;
}
```

### 2️⃣ **Создаём класс Пользователя**  

```typescript
class ChatUser implements ChatObserver {
    constructor(private name: string) {}

    update(message: string): void {
        console.log(`💬 ${this.name} получил сообщение: "${message}"`);
    }
}
```

### 3️⃣ **Создаём класс Чата**  

```typescript
class ChatRoom implements ChatSubject {
    private users: ChatObserver[] = [];

    join(observer: ChatObserver): void {
        this.users.push(observer);
    }

    leave(observer: ChatObserver): void {
        this.users = this.users.filter(user => user !== observer);
    }

    sendMessage(message: string): void {
        console.log(`📢 Отправка сообщения: "${message}"`);
        this.users.forEach(user => user.update(message));
    }
}
```

### 4️⃣ **Используем чат**  

```typescript
const chat = new ChatRoom();

const alice = new ChatUser("Алиса");
const bob = new ChatUser("Боб");

// Пользователи заходят в чат
chat.join(alice);
chat.join(bob);

// Сообщение в чат
chat.sendMessage("Привет всем!");

// 📢 Отправка сообщения: "Привет всем!"
// 💬 Алиса получил сообщение: "Привет всем!"
// 💬 Боб получил сообщение: "Привет всем!"

// Боб выходит
chat.leave(bob);

// Новое сообщение
chat.sendMessage("Как дела?");

// 📢 Отправка сообщения: "Как дела?"
// 💬 Алиса получил сообщение: "Как дела?"
```

---

## 📊 **Когда использовать паттерн "Наблюдатель"?**  

✅ **Когда у объекта может быть много зависимых, и им нужно автоматически реагировать на изменения**.  
✅ **Когда не хочется жёстко связывать объекты**.  
✅ **Когда изменения в одном объекте должны вызывать реакции в других объектах**.  

---

## 🎬 **Вывод**  

- **Паттерн "Наблюдатель"** позволяет **организовать подписку** на события.  
- Издатель **не знает конкретные классы подписчиков** → код **гибкий** и **расширяемый**.  
- Подходит для **уведомлений, событий, UI-обновлений** и **многих других случаев**.  
