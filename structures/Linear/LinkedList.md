# 🔗 **Связанный список (Linked List)**
## 📌 **Что такое связанный список?**
Связанный список (**Linked List**) — это **динамическая структура данных**, состоящая из **узлов** (**Node**), каждый из которых содержит **данные** и **ссылку (указатель) на следующий узел**.  

🚀 **В отличие от массива**, связанный список **не хранит элементы в памяти подряд**. Вместо этого каждый элемент содержит ссылку на следующий, что позволяет **гибко добавлять и удалять элементы**.

---

## 📊 **Структура узла в связанном списке**
Каждый узел состоит из двух частей:
1. **Данные (`value`)** – информация, хранящаяся в узле.
2. **Ссылка (`next`)** – указатель на следующий узел.

Пример структуры узла:
```
[ 10 | * ] → [ 20 | * ] → [ 30 | * ] → null
```
- `10`, `20`, `30` – это значения узлов.
- `*` – это ссылка на следующий узел.
- `null` – обозначает конец списка.

---

## 🏗 **Виды связанных списков**
### **1️⃣ Односвязный список (Singly Linked List)**
- Каждый узел **ссылается только на следующий**.
- Доступ возможен **только в одну сторону**.
- Последний элемент **ссылается на `null`** (означает конец списка).  

Пример:
```
Head → [ 5 | * ] → [ 10 | * ] → [ 15 | * ] → null
```

### **2️⃣ Двусвязный список (Doubly Linked List)**
- Каждый узел имеет **две ссылки**:
  🔹 `next` – на следующий узел  
  🔹 `prev` – на предыдущий узел  
- Позволяет **движение в обе стороны**.  

Пример:
```
null ← [ 5 | * | * ] ⇄ [ 10 | * | * ] ⇄ [ 15 | * | * ] → null
```

### **3️⃣ Кольцевой список (Circular Linked List)**
- Последний узел **ссылается на первый**.
- Может быть **односвязным или двусвязным**.  

Пример (односвязный):
```
[ 5 | * ] → [ 10 | * ] → [ 15 | * ] ↺
```
Пример (двусвязный):
```
↺ [ 5 | * | * ] ⇄ [ 10 | * | * ] ⇄ [ 15 | * | * ] ↺
```

---

## 🚀 **Операции в связанном списке и их сложность (Big O)**
| Операция | Односвязный | Двусвязный |
|----------|------------|------------|
| **Добавление в начало (`pushFront`)** | O(1) | O(1) |
| **Добавление в конец (`pushBack`)** | O(n) | O(1) |
| **Удаление из начала (`popFront`)** | O(1) | O(1) |
| **Удаление из конца (`popBack`)** | O(n) | O(1) |
| **Поиск элемента (`find`)** | O(n) | O(n) |
| **Доступ по индексу (`getAt`)** | O(n) | O(n) |

📌 **Вывод:**  
- **Добавление/удаление в начало** – O(1), **быстро**.  
- **Поиск по индексу** – O(n), **медленно** (в отличие от массива O(1)).  
- **Двусвязный список** быстрее при **удалении с конца** (O(1) вместо O(n)).  

---

## 💻 **Реализация односвязного списка на TypeScript**
```ts
class Node<T> {
    value: T;
    next: Node<T> | null = null;

    constructor(value: T) {
        this.value = value;
    }
}

class LinkedList<T> {
    head: Node<T> | null = null;

    // Добавление в начало (O(1))
    pushFront(value: T): void {
        const newNode = new Node(value);
        newNode.next = this.head;
        this.head = newNode;
    }

    // Добавление в конец (O(n))
    pushBack(value: T): void {
        const newNode = new Node(value);
        if (!this.head) {
            this.head = newNode;
            return;
        }
        let current = this.head;
        while (current.next) {
            current = current.next;
        }
        current.next = newNode;
    }

    // Удаление из начала (O(1))
    popFront(): void {
        if (this.head) {
            this.head = this.head.next;
        }
    }

    // Поиск элемента (O(n))
    find(value: T): boolean {
        let current = this.head;
        while (current) {
            if (current.value === value) return true;
            current = current.next;
        }
        return false;
    }

    // Вывод списка (O(n))
    print(): void {
        let current = this.head;
        const values: T[] = [];
        while (current) {
            values.push(current.value);
            current = current.next;
        }
        console.log(values.join(" → "));
    }
}

// 📌 Использование
const list = new LinkedList<number>();
list.pushFront(10);
list.pushFront(20);
list.pushBack(30);
list.print(); // 20 → 10 → 30
console.log(list.find(10)); // true
list.popFront();
list.print(); // 10 → 30
```

---

## 💻 **Реализация двусвязного списка на TypeScript**
```ts
class DoublyNode<T> {
    value: T;
    next: DoublyNode<T> | null = null;
    prev: DoublyNode<T> | null = null;

    constructor(value: T) {
        this.value = value;
    }
}

class DoublyLinkedList<T> {
    head: DoublyNode<T> | null = null;
    tail: DoublyNode<T> | null = null;

    // Добавление в конец (O(1))
    pushBack(value: T): void {
        const newNode = new DoublyNode(value);
        if (!this.head) {
            this.head = this.tail = newNode;
        } else {
            this.tail!.next = newNode;
            newNode.prev = this.tail;
            this.tail = newNode;
        }
    }

    // Удаление из конца (O(1))
    popBack(): void {
        if (!this.tail) return;
        if (this.tail === this.head) {
            this.head = this.tail = null;
        } else {
            this.tail = this.tail.prev;
            this.tail!.next = null;
        }
    }

    // Вывод списка (O(n))
    print(): void {
        let current = this.head;
        const values: T[] = [];
        while (current) {
            values.push(current.value);
            current = current.next;
        }
        console.log(values.join(" ⇄ "));
    }
}

// 📌 Использование
const dList = new DoublyLinkedList<number>();
dList.pushBack(1);
dList.pushBack(2);
dList.pushBack(3);
dList.print(); // 1 ⇄ 2 ⇄ 3
dList.popBack();
dList.print(); // 1 ⇄ 2
```

---

## 🎯 **Когда использовать связанный список?**
✅ **Когда часто добавляются/удаляются элементы в начало списка (O(1))**.  
✅ **Когда размер списка заранее неизвестен** (он динамически растёт).  
✅ **Когда важен последовательный доступ, а не быстрый доступ по индексу**.  

🚫 **Когда НЕ использовать**:  
- Если нужен **быстрый доступ по индексу** (в массиве O(1), а в списке O(n)).  
- Если **память ограничена** (список требует больше памяти из-за ссылок).  

---

## 🔥 **Вывод**
- Связанный список **гибче, чем массив**, но **медленнее при доступе по индексу**.
- **Односвязный** – меньше памяти, но сложнее удалять элементы.
- **Двусвязный** – удобнее, но требует больше памяти. 🚀