# 🕵️‍♂️ **Паттерн "Заместитель" (Proxy) в TypeScript**  

## 🎯 **Что такое паттерн "Заместитель" (Proxy)?**  

**"Заместитель" (Proxy)** — это **структурный паттерн**, который **создаёт объект-заместитель** для управления доступом к другому объекту.  

📌 **Проще говоря:**  
- Вместо того чтобы **напрямую работать с объектом**, мы создаём **прокси-объект**, который **контролирует доступ**.  
- Прокси **может изменять поведение** при обращении к оригинальному объекту.  
- Он **полезен, когда нужно добавить дополнительные проверки, кэширование, логирование или ленивую инициализацию**.  

---

## 🔥 **Когда использовать "Заместитель"?**  

✅ **Когда нужен контроль доступа к объекту** (например, проверка прав пользователя).  
✅ **Когда объект "тяжёлый"** и не хочется загружать его, пока он не понадобится (ленивая инициализация).  
✅ **Когда нужно добавить кэширование** (чтобы не перегружать систему).  
✅ **Когда нужно вести логирование или подсчёт вызовов**.  

---

## 🚨 **Проблема без "Заместителя"**  

Допустим, у нас есть класс **"СекретныйДокумент"**, и каждый раз при его создании он **грузит много данных**.  

```typescript
class SecretDocument {
    private content: string;

    constructor() {
        console.log("📄 Загружаем секретный документ...");
        this.content = "Секретные данные!";
    }

    read(): string {
        return this.content;
    }
}

// Использование
const doc = new SecretDocument(); // Загружает документ сразу
console.log(doc.read());
```

📌 **Проблема**: Документ загружается **сразу**, даже если он **не используется**!  

---

## ✅ **Используем "Заместитель" для ленивой загрузки**  

### **1️⃣ Создаём интерфейс для документа**  

```typescript
interface IDocument {
    read(): string;
}
```

---

### **2️⃣ Создаём оригинальный класс**  

```typescript
class SecretDocument implements IDocument {
    private content: string;

    constructor() {
        console.log("📄 Загружаем секретный документ...");
        this.content = "Секретные данные!";
    }

    read(): string {
        return this.content;
    }
}
```

---

### **3️⃣ Создаём прокси-класс (ленивая загрузка)**  

```typescript
class DocumentProxy implements IDocument {
    private realDocument: SecretDocument | null = null;

    read(): string {
        if (!this.realDocument) {
            this.realDocument = new SecretDocument(); // Загружаем только при первом вызове
        }
        return this.realDocument.read();
    }
}
```

---

### **4️⃣ Используем прокси вместо оригинала**  

```typescript
const doc = new DocumentProxy(); // Документ ещё не загружен
console.log("Документ ещё не загружен...");
console.log(doc.read()); // Загружается только сейчас!
console.log(doc.read()); // Дальше уже не грузится заново
```

### 🖥 **Вывод в консоль**  

```
Документ ещё не загружен...
📄 Загружаем секретный документ...
Секретные данные!
Секретные данные!
```

📌 **Теперь документ загружается только при первом вызове `read()`!**  

---

## 🚀 **Другие варианты использования "Заместителя"**  

### **1️⃣ Прокси для проверки прав доступа**  

Например, у нас есть **банк**, и мы хотим ограничить **доступ к счету** (например, проверять пароль).  

```typescript
interface IBankAccount {
    withdraw(amount: number): void;
}
```

---

### **Обычный банковский счёт**  

```typescript
class BankAccount implements IBankAccount {
    private balance: number = 1000;

    withdraw(amount: number): void {
        if (this.balance >= amount) {
            this.balance -= amount;
            console.log(`💰 Снято: ${amount}$. Остаток: ${this.balance}$`);
        } else {
            console.log("❌ Недостаточно средств!");
        }
    }
}
```

---

### **Прокси для защиты (нужен пароль)**  

```typescript
class BankAccountProxy implements IBankAccount {
    private realAccount: BankAccount;
    private password: string;

    constructor(password: string) {
        this.realAccount = new BankAccount();
        this.password = password;
    }

    withdraw(amount: number): void {
        if (!this.authenticate()) {
            console.log("🚫 Доступ запрещён! Неверный пароль.");
            return;
        }
        this.realAccount.withdraw(amount);
    }

    private authenticate(): boolean {
        const enteredPassword = prompt("Введите пароль: ");
        return enteredPassword === this.password;
    }
}
```

---

### **Используем прокси**  

```typescript
const account = new BankAccountProxy("1234");
account.withdraw(100); // Запросит пароль перед снятием
```

📌 **Теперь нельзя просто так снять деньги – нужно ввести пароль!**  

---

### **2️⃣ Прокси для кэширования данных**  

Допустим, у нас есть **API, которое возвращает пользователей**.  
Мы **не хотим загружать данные повторно**, если они уже были запрошены.  

```typescript
interface IUserService {
    getUser(id: number): string;
}
```

---

### **Оригинальный сервис (без кэша)**  

```typescript
class UserService implements IUserService {
    getUser(id: number): string {
        console.log(`📡 Запрос в базу данных для пользователя ${id}...`);
        return `Пользователь ${id}`;
    }
}
```

---

### **Прокси с кэшированием**  

```typescript
class UserServiceProxy implements IUserService {
    private realService: UserService;
    private cache: Map<number, string>;

    constructor() {
        this.realService = new UserService();
        this.cache = new Map();
    }

    getUser(id: number): string {
        if (this.cache.has(id)) {
            console.log(`✅ Кэш: возвращаем данные для пользователя ${id}`);
            return this.cache.get(id)!;
        }

        const user = this.realService.getUser(id);
        this.cache.set(id, user);
        return user;
    }
}
```

---

### **Используем прокси**  

```typescript
const userService = new UserServiceProxy();

console.log(userService.getUser(1)); // Первый раз загрузит из базы
console.log(userService.getUser(1)); // Теперь из кэша!
console.log(userService.getUser(2)); // Новый запрос
console.log(userService.getUser(2)); // Из кэша
```

### 🖥 **Вывод в консоль**  

```
📡 Запрос в базу данных для пользователя 1...
Пользователь 1
✅ Кэш: возвращаем данные для пользователя 1
Пользователь 1
📡 Запрос в базу данных для пользователя 2...
Пользователь 2
✅ Кэш: возвращаем данные для пользователя 2
Пользователь 2
```

📌 **Теперь если данные уже загружены, они берутся из кэша, а не запрашиваются заново!**  

---

## 🎬 **Вывод**  

✅ **"Заместитель" – это объект, который стоит "перед" оригиналом и контролирует доступ**.  
✅ **Может использоваться для ленивой загрузки, кэширования, логирования, защиты данных**.  
✅ **Позволяет улучшить производительность, безопасность и удобство работы с объектами**.  

🔥 **Где использовать?**  
- **Ленивая инициализация** (откладываем создание объекта до первого вызова).  
- **Кэширование** (чтобы не делать повторные запросы в базу).  
- **Безопасность** (проверка прав доступа перед вызовом методов).  
- **Логирование** (записываем, какие методы вызываются).  
