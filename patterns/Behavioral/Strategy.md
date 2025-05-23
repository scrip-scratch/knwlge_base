# 🎯 **Паттерн "Стратегия" (Strategy) в TypeScript**  

## 📝 **Что такое "Стратегия"?**  

**"Стратегия" (Strategy)** — это **поведенческий паттерн**, который позволяет **выбирать алгоритм поведения во время выполнения**.  

📌 **Проще говоря:**  
- У объекта есть несколько вариантов поведения, и он может **легко переключаться между ними**.  
- Вместо множества `if-else` или `switch-case` создаются **отдельные классы для каждого варианта поведения**.  
- Убирает **жёсткую связанность** между объектом и алгоритмами.  

---

## 🔥 **Когда использовать "Стратегию"?**  

✅ **Если нужно выбирать между разными алгоритмами во время выполнения** (например, разные способы оплаты).  
✅ **Когда код содержит много `if-else` или `switch-case` для выбора поведения**.  
✅ **Если хочется сделать код легко расширяемым**, добавляя новые стратегии без изменения основного класса.  

---

## 🚨 **Проблема без "Стратегии"**  

Допустим, у нас есть приложение для **оплаты покупок**, и пользователь может платить **картой, PayPal или криптовалютой**.  

❌ **Без паттерна "Стратегия" код выглядит так:**  

```typescript
class PaymentProcessor {
    processPayment(amount: number, method: string): void {
        if (method === "credit_card") {
            console.log(`Оплачено ${amount} рублей картой.`);
        } else if (method === "paypal") {
            console.log(`Оплачено ${amount} рублей через PayPal.`);
        } else if (method === "crypto") {
            console.log(`Оплачено ${amount} рублей криптовалютой.`);
        } else {
            console.log("Ошибка: неизвестный способ оплаты.");
        }
    }
}

const payment = new PaymentProcessor();
payment.processPayment(5000, "credit_card");
```

📌 **Проблема:**  
- Использование строк `"credit_card"`, `"paypal"`, `"crypto"` **повышает риск ошибок**.  
- При добавлении нового метода оплаты нужно **менять код класса**, что **нарушает принцип OCP** (Принцип открытости/закрытости).  
- **Жёсткая связанность** между классом и способами оплаты.  

---

## ✅ **Используем "Стратегию" (Strategy)**  

Разобьём код на три части:  
1️⃣ **Интерфейс стратегии (Strategy)** – общий интерфейс для всех вариантов поведения.  
2️⃣ **Конкретные стратегии (Concrete Strategies)** – отдельные классы для каждого способа оплаты.  
3️⃣ **Контекст (Context)** – класс, который использует стратегию.  

---

### **1️⃣ Создаём интерфейс стратегии**  

```typescript
interface PaymentStrategy {
    pay(amount: number): void;
}
```

📌 **Этот интерфейс определяет метод `pay()`, который должны реализовать все стратегии.**  

---

### **2️⃣ Реализуем конкретные стратегии**  

#### **Оплата картой**  

```typescript
class CreditCardPayment implements PaymentStrategy {
    pay(amount: number): void {
        console.log(`Оплачено ${amount} рублей картой.`);
    }
}
```

---

#### **Оплата через PayPal**  

```typescript
class PayPalPayment implements PaymentStrategy {
    pay(amount: number): void {
        console.log(`Оплачено ${amount} рублей через PayPal.`);
    }
}
```

---

#### **Оплата криптовалютой**  

```typescript
class CryptoPayment implements PaymentStrategy {
    pay(amount: number): void {
        console.log(`Оплачено ${amount} рублей криптовалютой.`);
    }
}
```

---

### **3️⃣ Создаём контекст (PaymentProcessor)**  

```typescript
class PaymentProcessor {
    private strategy: PaymentStrategy;

    constructor(strategy: PaymentStrategy) {
        this.strategy = strategy;
    }

    setStrategy(strategy: PaymentStrategy): void {
        this.strategy = strategy;
    }

    processPayment(amount: number): void {
        this.strategy.pay(amount);
    }
}
```

📌 **Контекст управляет стратегиями и может их менять.**  

---

### **4️⃣ Используем "Стратегию"**  

```typescript
const paymentProcessor = new PaymentProcessor(new CreditCardPayment());
paymentProcessor.processPayment(5000); // Оплачено 5000 рублей картой.

paymentProcessor.setStrategy(new PayPalPayment());
paymentProcessor.processPayment(3000); // Оплачено 3000 рублей через PayPal.

paymentProcessor.setStrategy(new CryptoPayment());
paymentProcessor.processPayment(2000); // Оплачено 2000 рублей криптовалютой.
```

---

## 🖥 **Вывод в консоль**  

```
Оплачено 5000 рублей картой.
Оплачено 3000 рублей через PayPal.
Оплачено 2000 рублей криптовалютой.
```

📌 **Теперь можно легко добавлять новые способы оплаты, не меняя существующий код!**  

---

## 🚀 **Другие примеры использования "Стратегии"**  

### **1️⃣ Алгоритмы сортировки**  
Можно выбрать, какой алгоритм сортировки использовать: **быстрая сортировка, сортировка пузырьком, сортировка вставками**.  

### **2️⃣ Форматирование текста**  
Можно выбрать разные способы форматирования: **HTML, Markdown, Plain Text**.  

### **3️⃣ Выбор маршрута в навигаторе**  
Можно выбрать разные маршруты: **самый быстрый, самый короткий, без пробок**.  

### **4️⃣ Атаки в игре**  
Игрок может выбрать стратегию атаки: **ближний бой, дальний бой, магия**.  

---

## 🔁 **Где можно применять "Стратегию"?**  

✅ **Онлайн-платежи** – разные способы оплаты.  
✅ **Игры** – выбор стратегии атаки, поведения ботов.  
✅ **Навигация** – выбор оптимального маршрута.  
✅ **Системы логирования** – выбор формата вывода (JSON, XML, TXT).  

---

## 🎬 **Вывод**  

✅ **Паттерн "Стратегия" позволяет динамически выбирать алгоритм поведения.**  
✅ **Избавляет от множества `if-else` и делает код гибким и расширяемым.**  
✅ **Позволяет легко добавлять новые стратегии без изменения основного кода.**  

🚀 **Где использовать?**  
- Онлайн-платежи, игры, навигация, логирование.  
