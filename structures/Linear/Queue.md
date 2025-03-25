# 📌 **Очередь (Queue) – структура данных**  

## 🔹 **Что такое очередь?**  
**Очередь (Queue)** — это линейная структура данных, в которой **добавление (enqueue) элементов происходит в конец, а удаление (dequeue) – из начала**.  

> Работает по принципу **FIFO (First In, First Out)** – «первым пришёл, первым вышел».  

Пример из жизни:  
- **Очередь в магазине** – первый пришедший покупатель обслуживается первым.  
- **Очередь на сервере** – обработка запросов в порядке поступления.  

---

## 🎯 **Основные операции в очереди**
| Операция  | Описание | Время (Big O) |
|-----------|----------|--------------|
| `enqueue(value)`  | Добавляет элемент в конец очереди  | O(1) |
| `dequeue()`  | Удаляет элемент из начала очереди | O(1) |
| `peek()`  | Показывает первый элемент без удаления | O(1) |
| `isEmpty()`  | Проверяет, пуста ли очередь | O(1) |
| `getSize()`  | Возвращает количество элементов | O(1) |

---

## 📊 **Виды очередей**
1️⃣ **Обычная очередь (Regular Queue)** – FIFO (первый пришёл – первый вышел).  
2️⃣ **Двухсторонняя очередь (Deque, Double-Ended Queue)** – элементы можно добавлять и удалять с обоих концов.  
3️⃣ **Приоритетная очередь (Priority Queue)** – элементы удаляются по приоритету, а не по порядку.  
4️⃣ **Циклическая очередь (Circular Queue)** – элементы хранятся в **круговом буфере**, оптимизируя использование памяти.  

---

## 💻 **Реализация очереди на TypeScript**
### 🚀 **1. Очередь с массивом (простая реализация)**
В **массивах** `enqueue()` и `dequeue()` могут быть **неэффективными**, так как `shift()` сдвигает все элементы.  

```ts
class Queue<T> {
    private items: T[] = [];

    // Добавление в очередь (O(1))
    enqueue(value: T): void {
        this.items.push(value);
    }

    // Удаление из очереди (O(n) из-за сдвига)
    dequeue(): T | undefined {
        return this.items.shift();
    }

    // Получить первый элемент (O(1))
    peek(): T | undefined {
        return this.items[0];
    }

    // Проверка, пуста ли очередь (O(1))
    isEmpty(): boolean {
        return this.items.length === 0;
    }

    // Получить размер (O(1))
    getSize(): number {
        return this.items.length;
    }

    // Вывести очередь
    print(): void {
        console.log("Queue:", this.items.join(" → "));
    }
}

// 📌 Использование
const queue = new Queue<number>();

queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);
queue.print(); // Queue: 1 → 2 → 3

console.log(queue.dequeue()); // 1
queue.print(); // Queue: 2 → 3

console.log(queue.peek()); // 2
console.log(queue.isEmpty()); // false
console.log(queue.getSize()); // 2
```

🔹 **Недостаток:** `dequeue()` использует `shift()`, что делает операцию O(n).  

---

### 🚀 **2. Очередь на связном списке (эффективное решение)**
Используем **односвязный список**, где `dequeue()` (удаление с головы) выполняется за **O(1)**.  

```ts
class Node<T> {
    value: T;
    next: Node<T> | null = null;
    constructor(value: T) {
        this.value = value;
    }
}

class LinkedQueue<T> {
    private head: Node<T> | null = null;
    private tail: Node<T> | null = null;
    private size: number = 0;

    // Добавление в очередь (O(1))
    enqueue(value: T): void {
        const newNode = new Node(value);
        if (!this.tail) {
            this.head = this.tail = newNode;
        } else {
            this.tail.next = newNode;
            this.tail = newNode;
        }
        this.size++;
    }

    // Удаление из очереди (O(1))
    dequeue(): T | null {
        if (!this.head) return null;
        const value = this.head.value;
        this.head = this.head.next;
        if (!this.head) this.tail = null;
        this.size--;
        return value;
    }

    // Получить первый элемент (O(1))
    peek(): T | null {
        return this.head ? this.head.value : null;
    }

    // Проверка, пуста ли очередь (O(1))
    isEmpty(): boolean {
        return this.size === 0;
    }

    // Получить размер очереди (O(1))
    getSize(): number {
        return this.size;
    }

    // Вывести очередь
    print(): void {
        let current = this.head;
        const values: T[] = [];
        while (current) {
            values.push(current.value);
            current = current.next;
        }
        console.log("Queue:", values.join(" → "));
    }
}

// 📌 Использование
const linkedQueue = new LinkedQueue<string>();

linkedQueue.enqueue("A");
linkedQueue.enqueue("B");
linkedQueue.enqueue("C");
linkedQueue.print(); // Queue: A → B → C

console.log(linkedQueue.dequeue()); // A
linkedQueue.print(); // Queue: B → C

console.log(linkedQueue.peek()); // B
console.log(linkedQueue.isEmpty()); // false
console.log(linkedQueue.getSize()); // 2
```

**📌 Почему связанный список лучше?**
✔ `dequeue()` работает за **O(1)** (в отличие от массива, где O(n)).  
✔ **Не требует сдвига элементов** при удалении.  
✔ Хорош для **длинных очередей**.  

---

## 🔥 **Когда использовать очередь?**
✅ **Когда важен порядок обработки (FIFO)** – например, серверные запросы.  
✅ **Когда часто выполняются `enqueue()` и `dequeue()`** – лучше использовать связный список.  
✅ **Для задач, требующих буферизации данных** – например, обработка потоков данных.  

🚫 **Когда НЕ использовать:**  
- Если требуется **случайный доступ** к элементам (лучше массив или хеш-таблица).  
- Если важна **быстрая работа с памятью**, а список содержит слишком много мелких объектов.  

---

## 🎯 **Примеры задач, где используется очередь**
1. **Обработка серверных запросов (Web Server Request Handling)**  
   – Очередь запросов обрабатывается последовательно.  
2. **Принтер (Print Queue)**  
   – Документы печатаются в порядке поступления.  
3. **Очередь задач (Task Queue)**  
   – В бэкенде выполняются фоновые задачи, например, отправка email.  
4. **Алгоритм BFS (поиск в ширину)**  
   – В графах очередь помогает обходить вершины.  

Пример **BFS на очереди**:
```ts
function bfs(graph: { [key: string]: string[] }, start: string) {
    const queue = new Queue<string>();
    const visited = new Set<string>();

    queue.enqueue(start);
    visited.add(start);

    while (!queue.isEmpty()) {
        const node = queue.dequeue();
        console.log(node);

        for (const neighbor of graph[node!]) {
            if (!visited.has(neighbor)) {
                queue.enqueue(neighbor);
                visited.add(neighbor);
            }
        }
    }
}

// 📌 Граф в виде списка смежности
const graph = {
    A: ["B", "C"],
    B: ["D", "E"],
    C: ["F"],
    D: [],
    E: [],
    F: []
};

bfs(graph, "A"); // A → B → C → D → E → F
```

---

## 🚀 **Вывод**
- **Очередь (Queue) – мощная структура данных** для работы с последовательными процессами.  
- **Массив** удобен, но при `dequeue()` работает медленно **(O(n))**.  
- **Связный список** – лучше для больших очередей, так как `enqueue()` и `dequeue()` выполняются за **O(1)**.  

Очереди применяются **повсюду** – от серверов до алгоритмов поиска! 🚀