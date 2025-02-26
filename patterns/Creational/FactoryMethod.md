# 🎯 **Паттерн Фабричный метод (Factory Method) в TypeScript**  

**Фабричный метод (Factory Method)** — это **порождающий паттерн**, который предоставляет общий интерфейс для создания объектов, но позволяет подклассам изменять тип создаваемых объектов.  

## 🚀 **Зачем нужен Фабричный метод?**  

✔ **Разделение логики создания объектов** – код не зависит от конкретных классов объектов.  
✔ **Гибкость и расширяемость** – можно легко добавлять новые типы объектов, не изменяя существующий код.  
✔ **Принцип открытости/закрытости (OCP)** – можно добавлять новые фабрики без изменения существующего кода.  
✔ **Упрощает тестирование** – можно подменять фабрики для тестирования.  

---

## 📌 **Пример без фабричного метода (Проблема)**  

Допустим, у нас есть класс **логистики**, который управляет разными видами транспорта (**грузовик** и **корабль**):

```typescript
class Truck {
    deliver(): void {
        console.log("🚚 Доставка грузовиком по дороге.");
    }
}

class Ship {
    deliver(): void {
        console.log("🚢 Доставка кораблём по морю.");
    }
}

class Logistics {
    createTransport(type: string): Truck | Ship {
        if (type === "truck") {
            return new Truck();
        } else if (type === "ship") {
            return new Ship();
        }
        throw new Error("Неизвестный тип транспорта.");
    }
}
```

### ❌ **Проблема этого кода**  

- **Логика создания объектов смешана с бизнес-логикой**.  
- Если добавится **новый транспорт (например, самолёт 🛫)**, придётся **менять код класса Logistics**, что **нарушает OCP**.  
- Код становится **менее гибким и трудно тестируемым**.  

---

## ✅ **Используем Фабричный метод**  

### 1️⃣ **Создаём интерфейс для транспорта**  

```typescript
interface Transport {
    deliver(): void;
}
```

### 2️⃣ **Создаём конкретные классы транспорта**  

```typescript
class Truck implements Transport {
    deliver(): void {
        console.log("🚚 Доставка грузовиком по дороге.");
    }
}

class Ship implements Transport {
    deliver(): void {
        console.log("🚢 Доставка кораблём по морю.");
    }
}
```

### 3️⃣ **Создаём абстрактный класс с Фабричным методом**  

```typescript
abstract class Logistics {
    // Фабричный метод (создаёт нужный объект)
    abstract createTransport(): Transport;

    planDelivery(): void {
        const transport = this.createTransport();
        transport.deliver();
    }
}
```

### 4️⃣ **Создаём конкретные фабрики**  

```typescript
class RoadLogistics extends Logistics {
    createTransport(): Transport {
        return new Truck();
    }
}

class SeaLogistics extends Logistics {
    createTransport(): Transport {
        return new Ship();
    }
}
```

### 5️⃣ **Используем фабричный метод**  

```typescript
const roadLogistics = new RoadLogistics();
roadLogistics.planDelivery();  
// 🚚 Доставка грузовиком по дороге.

const seaLogistics = new SeaLogistics();
seaLogistics.planDelivery();  
// 🚢 Доставка кораблём по морю.
```

---

## 🔥 **Что мы получили?**  

✅ **Логика создания объектов изолирована в фабричных классах**.  
✅ **Новый транспорт (например, самолёт) можно добавить, не меняя старый код**.  
✅ **Код стал гибче, чище и проще для тестирования**.  

---

## 📌 **Другой пример: Фабрика уведомлений**  

Допустим, у нас есть **разные способы отправки уведомлений**:  

- **Email**  
- **SMS**  
- **Push-уведомление**  

### 1️⃣ **Создаём интерфейс уведомления**  

```typescript
interface Notification {
    send(message: string): void;
}
```

### 2️⃣ **Создаём конкретные классы уведомлений**  

```typescript
class EmailNotification implements Notification {
    send(message: string): void {
        console.log(`📧 Отправка Email: ${message}`);
    }
}

class SMSNotification implements Notification {
    send(message: string): void {
        console.log(`📱 Отправка SMS: ${message}`);
    }
}

class PushNotification implements Notification {
    send(message: string): void {
        console.log(`📲 Отправка Push-уведомления: ${message}`);
    }
}
```

### 3️⃣ **Создаём абстрактную фабрику**  

```typescript
abstract class NotificationFactory {
    abstract createNotification(): Notification;

    sendNotification(message: string): void {
        const notification = this.createNotification();
        notification.send(message);
    }
}
```

### 4️⃣ **Создаём конкретные фабрики**  

```typescript
class EmailNotificationFactory extends NotificationFactory {
    createNotification(): Notification {
        return new EmailNotification();
    }
}

class SMSNotificationFactory extends NotificationFactory {
    createNotification(): Notification {
        return new SMSNotification();
    }
}

class PushNotificationFactory extends NotificationFactory {
    createNotification(): Notification {
        return new PushNotification();
    }
}
```

### 5️⃣ **Используем фабрику**  

```typescript
const emailFactory = new EmailNotificationFactory();
emailFactory.sendNotification("Привет! Это email уведомление.");
// 📧 Отправка Email: Привет! Это email уведомление.

const smsFactory = new SMSNotificationFactory();
smsFactory.sendNotification("Привет! Это SMS.");
// 📱 Отправка SMS: Привет! Это SMS.

const pushFactory = new PushNotificationFactory();
pushFactory.sendNotification("Привет! Это Push.");
// 📲 Отправка Push-уведомления: Привет! Это Push.
```

---

## 📊 **Когда использовать Фабричный метод?**  

✅ **Когда у вас есть общий интерфейс, но конкретные реализации могут отличаться** (например, разные виды транспорта, уведомлений, базы данных и т. д.).  
✅ **Когда логика создания объектов сложная** (например, требуется сложная настройка).  
✅ **Когда нужно легко добавлять новые типы объектов, не меняя старый код**.  

---

## 🎬 **Вывод**  

- **Фабричный метод** помогает **изолировать создание объектов** в отдельные классы.  
- Позволяет **легко добавлять новые типы объектов** без изменения существующего кода.  
- **Гибкость** и **удобство тестирования** – ключевые преимущества этого паттерна.  
