# 🎯 **Паттерн Фасад (Facade) в TypeScript**  

**Фасад (Facade)** — это **структурный паттерн**, который предоставляет **упрощённый интерфейс** к сложной системе классов, API или библиотеки.  

---

## 🔥 **Для чего нужен паттерн Фасад?**  

✔ **Скрывает сложность системы** — объединяет несколько классов в **одном понятном интерфейсе**.  
✔ **Облегчает поддержку кода** — меньше зависимостей, удобнее работать.  
✔ **Снижает связанность компонентов** — клиенту не нужно знать **детали реализации**.  
✔ **Защищает от изменений** — если изменится внутренняя логика, клиенту не нужно переписывать код.  

---

## 📌 **Пример: управление домашним кинотеатром**  

Представьте, что у нас есть **домашний кинотеатр**. Чтобы посмотреть фильм, нужно:  
1️⃣ Включить телевизор  
2️⃣ Включить аудиосистему  
3️⃣ Включить DVD-плеер  

Клиенту сложно каждый раз вызывать **несколько методов**. Мы создадим **Фасад**, который упростит эту задачу.  

### 1️⃣ **Создаём сложные системы**  

```typescript
class TV {
    turnOn(): void {
        console.log("📺 Телевизор включен");
    }

    turnOff(): void {
        console.log("📺 Телевизор выключен");
    }
}

class SoundSystem {
    turnOn(): void {
        console.log("🔊 Аудиосистема включена");
    }

    turnOff(): void {
        console.log("🔇 Аудиосистема выключена");
    }
}

class DVDPlayer {
    turnOn(): void {
        console.log("📀 DVD-плеер включен");
    }

    turnOff(): void {
        console.log("📀 DVD-плеер выключен");
    }
}
```

### 2️⃣ **Создаём Фасад**  

```typescript
class HomeTheaterFacade {
    private tv: TV;
    private soundSystem: SoundSystem;
    private dvdPlayer: DVDPlayer;

    constructor(tv: TV, soundSystem: SoundSystem, dvdPlayer: DVDPlayer) {
        this.tv = tv;
        this.soundSystem = soundSystem;
        this.dvdPlayer = dvdPlayer;
    }

    watchMovie(): void {
        console.log("🎬 Готовимся к просмотру фильма...");
        this.tv.turnOn();
        this.soundSystem.turnOn();
        this.dvdPlayer.turnOn();
        console.log("🍿 Фильм начался!");
    }

    endMovie(): void {
        console.log("🎬 Завершаем просмотр...");
        this.tv.turnOff();
        this.soundSystem.turnOff();
        this.dvdPlayer.turnOff();
        console.log("📴 Все устройства выключены");
    }
}
```

### 3️⃣ **Используем фасад**  

```typescript
const tv = new TV();
const soundSystem = new SoundSystem();
const dvdPlayer = new DVDPlayer();

const homeTheater = new HomeTheaterFacade(tv, soundSystem, dvdPlayer);

homeTheater.watchMovie(); 
// 🎬 Готовимся к просмотру фильма...
// 📺 Телевизор включен
// 🔊 Аудиосистема включена
// 📀 DVD-плеер включен
// 🍿 Фильм начался!

homeTheater.endMovie();
// 🎬 Завершаем просмотр...
// 📺 Телевизор выключен
// 🔇 Аудиосистема выключена
// 📀 DVD-плеер выключен
// 📴 Все устройства выключены
```

✅ **Теперь клиент просто вызывает `watchMovie()` или `endMovie()` вместо множества команд**.  

---

## 📌 **Другой пример: Фасад для работы с API**  

Допустим, у нас есть сложный API для работы с пользователями.  
Вместо того, чтобы клиент вызывал методы отдельно, создадим **Фасад**.  

### 1️⃣ **Создаём сложные сервисы**  

```typescript
class UserService {
    getUser(id: number): string {
        return `👤 Пользователь с ID ${id}`;
    }
}

class PaymentService {
    processPayment(userId: number, amount: number): string {
        return `💳 Оплачено ${amount} пользователем ${userId}`;
    }
}

class NotificationService {
    sendEmail(userId: number, message: string): void {
        console.log(`📧 Отправлено сообщение "${message}" пользователю ${userId}`);
    }
}
```

### 2️⃣ **Создаём Фасад**  

```typescript
class UserFacade {
    private userService: UserService;
    private paymentService: PaymentService;
    private notificationService: NotificationService;

    constructor() {
        this.userService = new UserService();
        this.paymentService = new PaymentService();
        this.notificationService = new NotificationService();
    }

    makePurchase(userId: number, amount: number): void {
        console.log(this.userService.getUser(userId));
        console.log(this.paymentService.processPayment(userId, amount));
        this.notificationService.sendEmail(userId, `Ваш платёж на сумму ${amount} прошёл успешно!`);
    }
}
```

### 3️⃣ **Используем фасад**  

```typescript
const userFacade = new UserFacade();
userFacade.makePurchase(1, 1000);
// 👤 Пользователь с ID 1
// 💳 Оплачено 1000 пользователем 1
// 📧 Отправлено сообщение "Ваш платёж на сумму 1000 прошёл успешно!" пользователю 1
```

✅ Клиент теперь вызывает **один метод** `makePurchase()`, вместо множества разных методов.  

---

## 🔥 **Когда использовать паттерн Фасад?**  

✅ Когда **нужно скрыть сложность системы**.  
✅ Когда система **содержит много зависимостей**, и мы хотим уменьшить связанность.  
✅ Когда клиент **не должен видеть детали реализации**.  
✅ Когда нужно **сделать код более читаемым и удобным**.  

---

## 🎬 **Вывод**  

- **Фасад** предоставляет **упрощённый интерфейс** к сложной системе.  
- Он **скрывает сложность**, делает код **читаемым и удобным**.  
- **Облегчает поддержку** и уменьшает зависимость между компонентами.  

🔥 Используй паттерн **Фасад**, если хочешь сделать код **простым и удобным**! 🚀  
