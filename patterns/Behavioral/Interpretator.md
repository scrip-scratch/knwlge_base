# 📜 **Паттерн "Интерпретатор" (Interpreter) в TypeScript**  

## 📝 **Что такое "Интерпретатор" (Interpreter)?**  

**"Интерпретатор"** — это **поведенческий паттерн**, который позволяет **создавать язык (или упрощённую грамматику) и интерпретировать его выражения**.  

📌 **Проще говоря:**  
- Если у вас есть **какой-то специфический язык или команды**, этот паттерн позволяет **разбирать, анализировать и выполнять их**.  
- Это используется, например, в **командных языках, калькуляторах, SQL-парсерах, языках программирования**.  
- Помогает **структурировать код парсера** и сделать его расширяемым.  

---

## 🔥 **Когда использовать "Интерпретатор"?**  

✅ **Если у вас есть собственный язык, команды, выражения или грамматика.**  
✅ **Когда требуется анализировать и выполнять сложные строки (например, математические выражения).**  
✅ **Когда нужно создать DSL (Domain-Specific Language) для удобства пользователей.**  

---

## 🚨 **Проблема без "Интерпретатора"**  

Допустим, у нас есть **простой калькулятор**, который выполняет команды `"1 + 2"`, `"3 - 4"`, `"5 * 6"`.  

❌ **Без паттерна "Интерпретатор" код может выглядеть так:**  

```typescript
function evaluate(expression: string): number {
    const [left, operator, right] = expression.split(" ");

    const a = parseInt(left);
    const b = parseInt(right);

    switch (operator) {
        case "+": return a + b;
        case "-": return a - b;
        case "*": return a * b;
        case "/": return a / b;
        default: throw new Error("Неизвестный оператор");
    }
}

console.log(evaluate("2 + 3")); // 5
console.log(evaluate("10 - 4")); // 6
```

📌 **Проблема:**  
- Код жёстко привязан к одному типу выражений.  
- Если добавить новые операторы (`^` для степени, `%` для остатка), **придётся менять код функции**.  
- Парсинг и выполнение логики **переплетены**, что усложняет поддержку.  

---

## ✅ **Используем "Интерпретатор" (Interpreter)**  

Разобьём код на три части:  
1️⃣ **Абстрактное выражение (Expression)** – общий интерфейс для всех выражений.  
2️⃣ **Конкретные выражения (Concrete Expressions)** – отдельные классы для каждого оператора (`+`, `-`, `*`, `/`).  
3️⃣ **Парсер и интерпретатор** – принимает строку и создаёт дерево выражений.  

---

### **1️⃣ Определяем интерфейс выражения**  

```typescript
interface Expression {
    interpret(): number;
}
```

📌 **Этот интерфейс определяет метод `interpret()`, который должны реализовывать все выражения.**  

---

### **2️⃣ Создаём конкретные классы выражений**  

#### **Числовое выражение (NumberExpression)**  

```typescript
class NumberExpression implements Expression {
    constructor(private value: number) {}

    interpret(): number {
        return this.value;
    }
}
```

📌 **Этот класс представляет число, его метод `interpret()` просто возвращает значение.**  

---

#### **Арифметические операции**  

```typescript
class AdditionExpression implements Expression {
    constructor(private left: Expression, private right: Expression) {}

    interpret(): number {
        return this.left.interpret() + this.right.interpret();
    }
}

class SubtractionExpression implements Expression {
    constructor(private left: Expression, private right: Expression) {}

    interpret(): number {
        return this.left.interpret() - this.right.interpret();
    }
}

class MultiplicationExpression implements Expression {
    constructor(private left: Expression, private right: Expression) {}

    interpret(): number {
        return this.left.interpret() * this.right.interpret();
    }
}

class DivisionExpression implements Expression {
    constructor(private left: Expression, private right: Expression) {}

    interpret(): number {
        return this.left.interpret() / this.right.interpret();
    }
}
```

📌 **Теперь каждая операция (`+`, `-`, `*`, `/`) реализована в своём классе.**  

---

### **3️⃣ Парсим строку и создаём выражения**  

```typescript
class Interpreter {
    static parse(expression: string): Expression {
        const tokens = expression.split(" ");
        const stack: Expression[] = [];

        for (const token of tokens) {
            if (!isNaN(Number(token))) {
                stack.push(new NumberExpression(Number(token)));
            } else {
                const right = stack.pop();
                const left = stack.pop();

                if (!left || !right) {
                    throw new Error("Ошибка разбора выражения");
                }

                switch (token) {
                    case "+": stack.push(new AdditionExpression(left, right)); break;
                    case "-": stack.push(new SubtractionExpression(left, right)); break;
                    case "*": stack.push(new MultiplicationExpression(left, right)); break;
                    case "/": stack.push(new DivisionExpression(left, right)); break;
                    default: throw new Error(`Неизвестный оператор: ${token}`);
                }
            }
        }

        return stack.pop()!;
    }
}
```

📌 **Этот класс разбирает строку `"1 + 2"` и строит дерево выражений.**  

---

### **4️⃣ Используем интерпретатор**  

```typescript
const expression1 = Interpreter.parse("5 + 3");
console.log(expression1.interpret()); // 8

const expression2 = Interpreter.parse("10 - 2");
console.log(expression2.interpret()); // 8

const expression3 = Interpreter.parse("4 * 3");
console.log(expression3.interpret()); // 12

const expression4 = Interpreter.parse("8 / 2");
console.log(expression4.interpret()); // 4
```

---

## 🖥 **Вывод в консоль**  

```
8
8
12
4
```

📌 **Теперь можно легко добавлять новые операции (например, `^` для степени) без изменения основного кода!**  

---

## 🚀 **Другие примеры использования "Интерпретатора"**  

### **1️⃣ SQL-парсер**  
Можно интерпретировать SQL-запросы:  
```sql
SELECT name FROM users WHERE age > 18;
```  

### **2️⃣ Калькулятор команд**  
Можно интерпретировать команды вроде `"SET X = 10"` и `"PRINT X"`.  

### **3️⃣ Логические выражения**  
Можно интерпретировать условия:  
```typescript
"true AND false OR true"
```

### **4️⃣ Компиляторы и языки программирования**  
Можно создать собственный язык и интерпретировать его команды.  

---

## 🔁 **Где можно применять "Интерпретатор"?**  

✅ **Компиляторы** – разбор и выполнение кода.  
✅ **Анализаторы данных** – интерпретация пользовательских команд.  
✅ **Игры и AI** – обработка сценариев NPC.  
✅ **SQL и DSL** – выполнение сложных запросов.  

---

## 🎬 **Вывод**  

✅ **Паттерн "Интерпретатор" помогает разбирать и интерпретировать выражения.**  
✅ **Позволяет добавлять новые операции без изменения существующего кода.**  
✅ **Используется в языках программирования, SQL-запросах, чат-ботах.**  

🚀 **Где использовать?**  
- SQL, DSL, калькуляторы, командные системы, игры.  
