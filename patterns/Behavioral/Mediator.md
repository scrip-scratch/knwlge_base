# 🔄 **Паттерн "Посредник" (Mediator) в TypeScript**  

## 🎯 **Что такое "Посредник" (Mediator)?**  

**"Посредник" (Mediator)** — это **поведенческий паттерн**, который **упрощает взаимодействие между объектами** в системе, **заменяя их прямые связи централизованным объектом-посредником**.  

📌 **Проще говоря:**  
- Вместо того чтобы объекты напрямую общались **друг с другом**, они взаимодействуют **через посредника**.  
- Это снижает **связанность** (coupling) между объектами и делает код **более гибким**.  
- Если нужно изменить логику взаимодействия объектов, **достаточно изменить только посредника**.  

---

## 🔥 **Когда использовать "Посредник"?**  

✅ **Когда множество объектов тесно связаны друг с другом** (например, элементы UI, чаты, игровые персонажи).  
✅ **Когда сложно изменять систему из-за большого количества связей** между объектами.  
✅ **Когда нужно централизовать управление взаимодействиями** (например, диспетчеризация событий).  

---

## 🚨 **Проблема без "Посредника"**  

Допустим, у нас есть **чат**, где пользователи отправляют сообщения друг другу.  

Без **"Посредника"**, каждый пользователь **напрямую взаимодействует** с другими пользователями:  

```typescript
class User {
    constructor(public name: string, private users: User[]) {}

    sendMessage(message: string, receiver: User): void {
        console.log(`${this.name} → ${receiver.name}: ${message}`);
    }
}

// Создаём пользователей
const alice = new User("Alice", []);
const bob = new User("Bob", []);
const charlie = new User("Charlie", []);

// Отправляем сообщения напрямую
alice.sendMessage("Привет, Боб!", bob);
bob.sendMessage("Привет, Алиса!", alice);
```

📌 **Проблема:**  
- Каждый пользователь **должен знать всех остальных пользователей**.  
- Если добавить **нового пользователя**, придётся менять код.  
- Если изменится логика отправки сообщений, **нужно изменять код всех пользователей**.  

---

## ✅ **Используем "Посредник"**  

Разобьём код на:  
1️⃣ **Посредник (Mediator)** – управляет взаимодействием между пользователями.  
2️⃣ **Конкретные участники (Users)** – взаимодействуют через посредника.  

---

### **1️⃣ Создаём интерфейс посредника**  

```typescript
interface ChatMediator {
    sendMessage(message: string, sender: User): void;
    addUser(user: User): void;
}
```

---

### **2️⃣ Реализуем посредника (Чат)**  

```typescript
class ChatRoom implements ChatMediator {
    private users: User[] = [];

    addUser(user: User): void {
        this.users.push(user);
    }

    sendMessage(message: string, sender: User): void {
        for (const user of this.users) {
            if (user !== sender) {
                user.receiveMessage(message, sender);
            }
        }
    }
}
```

📌 **Теперь все пользователи отправляют сообщения через `ChatRoom`**, а не напрямую.  

---

### **3️⃣ Создаём класс пользователей**  

```typescript
class User {
    constructor(private name: string, private chat: ChatMediator) {}

    sendMessage(message: string): void {
        console.log(`📤 ${this.name} отправляет сообщение: ${message}`);
        this.chat.sendMessage(message, this);
    }

    receiveMessage(message: string, sender: User): void {
        console.log(`📩 ${this.name} получает сообщение от ${sender.getName()}: ${message}`);
    }

    getName(): string {
        return this.name;
    }
}
```

---

### **4️⃣ Используем "Посредник"**  

```typescript
const chat = new ChatRoom();

const alice = new User("Alice", chat);
const bob = new User("Bob", chat);
const charlie = new User("Charlie", chat);

chat.addUser(alice);
chat.addUser(bob);
chat.addUser(charlie);

alice.sendMessage("Привет, всем!");
bob.sendMessage("Привет, Алиса!");
charlie.sendMessage("Всем привет!");
```

---

### 🖥 **Вывод в консоль**  

```
📤 Alice отправляет сообщение: Привет, всем!
📩 Bob получает сообщение от Alice: Привет, всем!
📩 Charlie получает сообщение от Alice: Привет, всем!

📤 Bob отправляет сообщение: Привет, Алиса!
📩 Alice получает сообщение от Bob: Привет, Алиса!
📩 Charlie получает сообщение от Bob: Привет, Алиса!

📤 Charlie отправляет сообщение: Всем привет!
📩 Alice получает сообщение от Charlie: Всем привет!
📩 Bob получает сообщение от Charlie: Всем привет!
```

📌 **Теперь пользователи не знают друг о друге – только посредник управляет отправкой сообщений!**  

---

## 🔁 **Другой пример: UI-компоненты**  

Допустим, у нас есть **форма**, где кнопка отправки **зависит** от других полей (например, **неактивна, пока не введён email**).  

### **1️⃣ Создаём интерфейс посредника**  

```typescript
interface FormMediator {
    notify(sender: Component, event: string): void;
}
```

---

### **2️⃣ Создаём посредника (Форма)**  

```typescript
class LoginForm implements FormMediator {
    constructor(private emailInput: Input, private submitButton: Button) {
        emailInput.setMediator(this);
        submitButton.setMediator(this);
    }

    notify(sender: Component, event: string): void {
        if (sender instanceof Input && event === "input") {
            this.submitButton.setEnabled(sender.getValue().length > 0);
        }
    }
}
```

---

### **3️⃣ Создаём компоненты (Инпут и Кнопка)**  

```typescript
abstract class Component {
    protected mediator!: FormMediator;

    setMediator(mediator: FormMediator): void {
        this.mediator = mediator;
    }
}

class Input extends Component {
    private value = "";

    setValue(value: string): void {
        this.value = value;
        this.mediator.notify(this, "input");
    }

    getValue(): string {
        return this.value;
    }
}

class Button extends Component {
    private enabled = false;

    setEnabled(enabled: boolean): void {
        this.enabled = enabled;
        console.log(`🟢 Кнопка теперь ${enabled ? "активна" : "неактивна"}`);
    }
}
```

---

### **4️⃣ Используем "Посредник"**  

```typescript
const emailInput = new Input();
const submitButton = new Button();
const form = new LoginForm(emailInput, submitButton);

emailInput.setValue(""); // Кнопка неактивна
emailInput.setValue("user@example.com"); // Кнопка активна
```

---

### 🖥 **Вывод в консоль**  

```
🟢 Кнопка теперь неактивна
🟢 Кнопка теперь активна
```

📌 **Теперь кнопка "Зарегистрироваться" управляется через "Посредник", а не напрямую из инпута!**  

---

## 🚀 **Где ещё можно использовать "Посредник"?**  

### **1️⃣ Сложные UI-интерфейсы**  
Например, **модальные окна, динамические формы**, где компоненты должны взаимодействовать между собой.  

### **2️⃣ Чаты, игры, серверы**  
Например, **чат-комнаты**, где сообщения отправляются **через сервер-посредник**.  

### **3️⃣ Логика взаимодействия между объектами в играх**  
Например, **NPC управляются через "Диспетчер событий"**, а не обмениваются данными напрямую.  

---

## 🎬 **Вывод**  

✅ **"Посредник" упрощает взаимодействие объектов, заменяя прямые связи централизованным объектом**.  
✅ **Позволяет легко добавлять новых участников, не меняя код остальных**.  
✅ **Полезен в сложных системах, где много взаимосвязанных элементов**.  

🔥 **Где использовать?**  
- **Чаты, игровые сервера** (где клиенты не знают друг о друге).  
- **UI-компоненты** (например, кнопки, формы, списки).  
- **Логика событий и диспетчеризация** (например, централизованное управление действиями).  