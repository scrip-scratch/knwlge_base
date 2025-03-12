# 🔄 **Паттерн "Итератор" (Iterator) в TypeScript**  

## 🎯 **Что такое "Итератор" (Iterator)?**  

**"Итератор"** — это **поведенческий паттерн**, который позволяет **последовательно обходить элементы коллекции**, не раскрывая её внутреннюю структуру.  

📌 **Проще говоря:**  
- Вместо того чтобы **работать напрямую с массивом или списком**, мы используем **объект-итератор**.  
- Это делает код **гибче**, так как мы можем **менять структуру коллекции без изменения кода обхода**.  

---

## 🔥 **Когда использовать "Итератор"?**  

✅ **Когда нужно перебирать элементы сложной структуры** (например, дерево, граф, связанный список).  
✅ **Когда важно скрыть внутреннюю реализацию коллекции** (чтобы код работал с коллекцией через единый интерфейс).  
✅ **Когда нужно предоставить разные способы обхода коллекции** (например, **обычный и обратный**).  

---

## 🚨 **Проблема без "Итератора"**  

Допустим, у нас есть **свой класс списка пользователей**.  

```typescript
class UserCollection {
    private users: string[] = [];

    addUser(user: string): void {
        this.users.push(user);
    }

    getUsers(): string[] {
        return this.users;
    }
}

// Используем коллекцию
const users = new UserCollection();
users.addUser("Alice");
users.addUser("Bob");

for (const user of users.getUsers()) {
    console.log(user);
}
```

📌 **Проблема:**  
- **Метод `getUsers()` раскрывает внутреннюю структуру** (массив). Если мы захотим заменить массив на что-то другое (например, **связанный список**), придётся менять код.  
- Мы не можем **изменить логику обхода** (например, **обход в обратном порядке**).  
- **Код не поддерживает разные коллекции** (если появится другая структура, нам придётся писать новый код).  

---

## ✅ **Используем "Итератор"**  

Разобьём код на:  
1️⃣ **Интерфейс итератора** (задаёт стандартный способ перебора).  
2️⃣ **Конкретный итератор** (реализует перебор элементов).  
3️⃣ **Коллекцию, которая создаёт итератор**.  

---

### **1️⃣ Создаём интерфейс итератора**  

```typescript
interface Iterator<T> {
    next(): T | null;  // Возвращает следующий элемент или null, если элементов больше нет
    hasNext(): boolean; // Проверяет, есть ли ещё элементы
}
```

---

### **2️⃣ Реализуем итератор для списка пользователей**  

```typescript
class UserIterator implements Iterator<string> {
    private index = 0;

    constructor(private users: string[]) {}

    next(): string | null {
        return this.hasNext() ? this.users[this.index++] : null;
    }

    hasNext(): boolean {
        return this.index < this.users.length;
    }
}
```

---

### **3️⃣ Создаём коллекцию, которая возвращает итератор**  

```typescript
class UserCollection {
    private users: string[] = [];

    addUser(user: string): void {
        this.users.push(user);
    }

    getIterator(): Iterator<string> {
        return new UserIterator(this.users);
    }
}
```

---

### **4️⃣ Используем итератор**  

```typescript
const users = new UserCollection();
users.addUser("Alice");
users.addUser("Bob");
users.addUser("Charlie");

const iterator = users.getIterator();

while (iterator.hasNext()) {
    console.log(iterator.next());
}
```

---

### 🖥 **Вывод в консоль**  

```
Alice
Bob
Charlie
```

---

## 🔁 **Добавим поддержку обратного обхода**  

Что если мы **захотим перебирать список в обратном порядке**?  

### **1️⃣ Создаём итератор с обратным обходом**  

```typescript
class ReverseUserIterator implements Iterator<string> {
    private index: number;

    constructor(private users: string[]) {
        this.index = users.length - 1;
    }

    next(): string | null {
        return this.hasNext() ? this.users[this.index--] : null;
    }

    hasNext(): boolean {
        return this.index >= 0;
    }
}
```

---

### **2️⃣ Добавляем возможность выбора итератора в коллекции**  

```typescript
class UserCollection {
    private users: string[] = [];

    addUser(user: string): void {
        this.users.push(user);
    }

    getIterator(): Iterator<string> {
        return new UserIterator(this.users);
    }

    getReverseIterator(): Iterator<string> {
        return new ReverseUserIterator(this.users);
    }
}
```

---

### **3️⃣ Используем новый итератор**  

```typescript
const users = new UserCollection();
users.addUser("Alice");
users.addUser("Bob");
users.addUser("Charlie");

console.log("🔄 Прямой обход:");
const iterator = users.getIterator();
while (iterator.hasNext()) {
    console.log(iterator.next());
}

console.log("\n🔄 Обратный обход:");
const reverseIterator = users.getReverseIterator();
while (reverseIterator.hasNext()) {
    console.log(reverseIterator.next());
}
```

---

### 🖥 **Вывод в консоль**  

```
🔄 Прямой обход:
Alice
Bob
Charlie

🔄 Обратный обход:
Charlie
Bob
Alice
```

📌 **Теперь коллекция поддерживает разные способы обхода без изменения внутренней структуры!**  

---

## 🚀 **Где ещё можно использовать "Итератор"?**  

### **1️⃣ Обход дерева или графа**  
Можно создать **итератор для обхода в глубину или в ширину**.  

### **2️⃣ Работа с файлами**  
Можно сделать **итератор для построчного чтения файла**.  

### **3️⃣ Фильтрация данных**  
Итератор может **пропускать определённые элементы** (например, **показывать только активных пользователей**).  

---

## 🎬 **Вывод**  

✅ **"Итератор" позволяет перебирать коллекцию без раскрытия её внутренней структуры**.  
✅ **Можно легко добавлять новые способы обхода (прямой, обратный, по фильтру и т. д.)**.  
✅ **Код становится гибким: можно заменить массив на что-то другое без изменений логики обхода**.  

🔥 **Где использовать?**  
- **Обход сложных структур** (дерево, граф).  
- **Работа с данными в потоках (файлы, API, базы данных)**.  
- **Фильтрация данных (например, показывать только активные элементы)**.  