# 🌉 **Паттерн "Мост" (Bridge) в TypeScript**  

## 🎯 **Что такое паттерн "Мост" (Bridge)?**  

**Мост (Bridge)** — это **структурный паттерн**, который разделяет **абстракцию** (логика) и **реализацию** (детали) в **разные классы**. Это позволяет изменять их **независимо** друг от друга.  

📌 **Проще говоря:**  
- Разбиваем **сложные классы на две иерархии**:  
  1. **Абстракция** – определяет, что делать.  
  2. **Реализация** – определяет, как делать.  
- Абстракция и реализация **слабо связаны** и могут изменяться **отдельно**.  

---

## 🔥 **Когда использовать паттерн "Мост"?**  

✅ **Когда у вас есть несколько изменяющихся аспектов в объекте**, и их нужно разделить.  
✅ **Когда нужно избегать жесткой привязки к конкретным реализациям**.  
✅ **Когда система становится слишком сложной из-за множества подклассов**.  
✅ **Когда нужно изменять абстракцию и реализацию независимо**.  

---

## ❌ **Проблема без "Моста"**  

Представим, что мы разрабатываем систему для **форматов документов** (PDF, DOCX, TXT) и их **генераторов** (Windows, MacOS, Linux).  

Без паттерна "Мост" **придётся создавать много классов**:  

```
PDFGeneratorWindows
PDFGeneratorMacOS
PDFGeneratorLinux
DOCXGeneratorWindows
DOCXGeneratorMacOS
DOCXGeneratorLinux
TXTGeneratorWindows
TXTGeneratorMacOS
TXTGeneratorLinux
```

📌 **Чем больше форматов или платформ, тем больше классов нужно создавать!**  

---

## ✅ **Используем паттерн "Мост"**  

### **1️⃣ Разделяем абстракцию и реализацию**  

📌 **Абстракция** – это "что" (документ).  
📌 **Реализация** – это "как" (генератор).  

### **2️⃣ Создаём интерфейс генератора документов**  

```typescript
// Интерфейс реализаций (GeneratorImplementation)
interface DocumentGenerator {
    generate(content: string): void;
}
```

### **3️⃣ Создаём конкретные реализации (разные генераторы)**  

```typescript
class PDFGenerator implements DocumentGenerator {
    generate(content: string): void {
        console.log(`📄 PDF сгенерирован с контентом: "${content}"`);
    }
}

class DOCXGenerator implements DocumentGenerator {
    generate(content: string): void {
        console.log(`📄 DOCX сгенерирован с контентом: "${content}"`);
    }
}

class TXTGenerator implements DocumentGenerator {
    generate(content: string): void {
        console.log(`📄 TXT сгенерирован с контентом: "${content}"`);
    }
}
```

### **4️⃣ Создаём абстракцию (класс "Документ")**  

```typescript
// Абстракция (Document)
class Document {
    constructor(protected generator: DocumentGenerator) {}

    create(content: string): void {
        this.generator.generate(content);
    }
}
```

### **5️⃣ Расширяем абстракцию (добавляем разные типы документов)**  

```typescript
class Report extends Document {
    create(content: string): void {
        console.log("📊 Создание отчёта...");
        this.generator.generate(content);
    }
}

class Invoice extends Document {
    create(content: string): void {
        console.log("🧾 Создание счета...");
        this.generator.generate(content);
    }
}
```

### **6️⃣ Используем паттерн "Мост"**  

```typescript
// Используем PDF-генератор для отчёта
const pdfReport = new Report(new PDFGenerator());
pdfReport.create("Финансовый анализ за 2024 год");

// Используем TXT-генератор для счета
const txtInvoice = new Invoice(new TXTGenerator());
txtInvoice.create("Счет за услуги: 5000 руб.");

// 📊 Создание отчёта...
// 📄 PDF сгенерирован с контентом: "Финансовый анализ за 2024 год"

// 🧾 Создание счета...
// 📄 TXT сгенерирован с контентом: "Счет за услуги: 5000 руб."
```

---

## 🔥 **Что мы получили?**  

✅ **Гибкость** – можно **менять генератор документов и тип документа независимо**.  
✅ **Нет дублирования кода** – больше **не нужно создавать кучу подклассов**.  
✅ **Лёгкое расширение** – если появится новый формат (например, JSON), его легко добавить.  

---

## 📌 **Другой пример: Уведомления через разные каналы**  

Представим, что у нас есть **разные уведомления**:  
- **Обычные уведомления**  
- **Срочные уведомления**  

И **разные каналы доставки**:  
- **Email**  
- **SMS**  
- **Push-уведомления**  

### **1️⃣ Создаём интерфейс доставки сообщений**  

```typescript
interface MessageSender {
    sendMessage(message: string): void;
}
```

### **2️⃣ Реализуем конкретные способы отправки**  

```typescript
class EmailSender implements MessageSender {
    sendMessage(message: string): void {
        console.log(`📧 Отправка Email: "${message}"`);
    }
}

class SMSSender implements MessageSender {
    sendMessage(message: string): void {
        console.log(`📩 Отправка SMS: "${message}"`);
    }
}

class PushNotificationSender implements MessageSender {
    sendMessage(message: string): void {
        console.log(`📱 Отправка Push-уведомления: "${message}"`);
    }
}
```

### **3️⃣ Создаём абстракцию "Уведомление"**  

```typescript
class Notification {
    constructor(protected sender: MessageSender) {}

    send(message: string): void {
        this.sender.sendMessage(message);
    }
}
```

### **4️⃣ Создаём разные виды уведомлений**  

```typescript
class RegularNotification extends Notification {
    send(message: string): void {
        console.log("🔔 Обычное уведомление...");
        this.sender.sendMessage(message);
    }
}

class UrgentNotification extends Notification {
    send(message: string): void {
        console.log("🚨 Срочное уведомление!!!");
        this.sender.sendMessage(message);
    }
}
```

### **5️⃣ Используем паттерн "Мост"**  

```typescript
const emailNotification = new RegularNotification(new EmailSender());
emailNotification.send("Добро пожаловать в наш сервис!");

const smsUrgent = new UrgentNotification(new SMSSender());
smsUrgent.send("Ваш аккаунт был взломан! Срочно смените пароль!");

// 🔔 Обычное уведомление...
// 📧 Отправка Email: "Добро пожаловать в наш сервис!"

// 🚨 Срочное уведомление!!!
// 📩 Отправка SMS: "Ваш аккаунт был взломан! Срочно смените пароль!"
```

---

## 📊 **Когда использовать паттерн "Мост"?**  

✅ **Когда объект меняется в нескольких независимых направлениях**.  
✅ **Когда нужно изменять реализацию без изменения абстракции**.  
✅ **Когда система становится сложной из-за множества подклассов**.  
✅ **Когда нужно разрабатывать платформо-независимые решения**.  

---

## 🎬 **Вывод**  

- **Паттерн "Мост"** помогает **разделить абстракцию и реализацию**, чтобы их можно было изменять независимо.  
- Упрощает код, **избавляет от множества подклассов** и **делает систему гибкой**.  
- **Идеально подходит**, когда объект изменяется в **нескольких независимых направлениях**.  
