# 🎭 **Паттерн "Посетитель" (Visitor) в TypeScript**  

## 📝 **Что такое "Посетитель"?**  

**"Посетитель" (Visitor)** — это **поведенческий паттерн**, который позволяет **добавлять новые операции к объектам без изменения их классов**.  

📌 **Проще говоря:**  
- Если у вас есть **разные объекты**, и вам нужно выполнять с ними **разные операции**, паттерн "Посетитель" позволяет **разнести операции по отдельным классам**.  
- Убирает **жёсткую связанность** между объектами и операциями, которые с ними выполняются.  
- Позволяет добавлять **новые операции без изменения самих объектов**.  

---

## 🔥 **Когда использовать "Посетитель"?**  

✅ **Когда нужно выполнять разные операции над объектами сложной структуры (деревья, файлы, узлы DOM).**  
✅ **Если необходимо добавлять новые методы к существующим объектам, не изменяя их код.**  
✅ **Когда сложно изменить классы объектов, но нужно добавить к ним новую функциональность.**  

---

## 🚨 **Проблема без "Посетителя"**  

Допустим, у нас есть **разные фигуры** (круг и квадрат), и нам нужно добавить к ним **методы расчёта площади и периметра**.  

❌ **Без паттерна "Посетитель" код выглядит так:**  

```typescript
class Circle {
    constructor(public radius: number) {}

    getArea(): number {
        return Math.PI * this.radius * this.radius;
    }

    getPerimeter(): number {
        return 2 * Math.PI * this.radius;
    }
}

class Square {
    constructor(public side: number) {}

    getArea(): number {
        return this.side * this.side;
    }

    getPerimeter(): number {
        return 4 * this.side;
    }
}

const circle = new Circle(5);
console.log("Площадь круга:", circle.getArea());
console.log("Периметр круга:", circle.getPerimeter());

const square = new Square(4);
console.log("Площадь квадрата:", square.getArea());
console.log("Периметр квадрата:", square.getPerimeter());
```

📌 **Проблема:**  
- Если нужно добавить **ещё один метод (например, расчёт диагонали),** придётся менять код всех классов.  
- Код сложно расширять, если фигур становится больше (например, добавить треугольник).  
- Методы `getArea()` и `getPerimeter()` **жёстко привязаны к классу**.  

---

## ✅ **Используем "Посетитель" (Visitor)**  

Разобьём код на три части:  
1️⃣ **Интерфейс посетителя (Visitor)** – описывает методы для обработки разных объектов.  
2️⃣ **Конкретные посетители (Concrete Visitors)** – реализуют логику для каждой операции.  
3️⃣ **Элементы (Elements)** – классы, которые принимают посетителя.  

---

### **1️⃣ Интерфейс посетителя (Visitor)**  

```typescript
interface ShapeVisitor {
    visitCircle(circle: Circle): void;
    visitSquare(square: Square): void;
}
```

📌 **Этот интерфейс описывает методы, которые должны реализовывать все посетители.**  

---

### **2️⃣ Конкретные посетители (Concrete Visitors)**  

#### **Посетитель для расчёта площади**  

```typescript
class AreaCalculator implements ShapeVisitor {
    visitCircle(circle: Circle): void {
        const area = Math.PI * circle.radius * circle.radius;
        console.log(`Площадь круга: ${area}`);
    }

    visitSquare(square: Square): void {
        const area = square.side * square.side;
        console.log(`Площадь квадрата: ${area}`);
    }
}
```

---

#### **Посетитель для расчёта периметра**  

```typescript
class PerimeterCalculator implements ShapeVisitor {
    visitCircle(circle: Circle): void {
        const perimeter = 2 * Math.PI * circle.radius;
        console.log(`Периметр круга: ${perimeter}`);
    }

    visitSquare(square: Square): void {
        const perimeter = 4 * square.side;
        console.log(`Периметр квадрата: ${perimeter}`);
    }
}
```

---

### **3️⃣ Создаём элементы (Circle и Square)**  

```typescript
interface Shape {
    accept(visitor: ShapeVisitor): void;
}

class Circle implements Shape {
    constructor(public radius: number) {}

    accept(visitor: ShapeVisitor): void {
        visitor.visitCircle(this);
    }
}

class Square implements Shape {
    constructor(public side: number) {}

    accept(visitor: ShapeVisitor): void {
        visitor.visitSquare(this);
    }
}
```

📌 **Теперь классы фигур не содержат лишней логики, а просто принимают посетителей.**  

---

### **4️⃣ Используем "Посетителя"**  

```typescript
const circle = new Circle(5);
const square = new Square(4);

const areaCalculator = new AreaCalculator();
const perimeterCalculator = new PerimeterCalculator();

console.log("Вычисляем площади:");
circle.accept(areaCalculator);
square.accept(areaCalculator);

console.log("\nВычисляем периметры:");
circle.accept(perimeterCalculator);
square.accept(perimeterCalculator);
```

---

## 🖥 **Вывод в консоль**  

```
Вычисляем площади:
Площадь круга: 78.53981633974483
Площадь квадрата: 16

Вычисляем периметры:
Периметр круга: 31.41592653589793
Периметр квадрата: 16
```

📌 **Теперь можно легко добавлять новые операции (например, "расчёт диагонали") без изменения классов `Circle` и `Square`!**  

---

## 🚀 **Другие примеры использования "Посетителя"**  

### **1️⃣ HTML или AST парсеры**  
Позволяет обходить HTML-дерево и применять операции (например, менять стили, искать теги).  

### **2️⃣ Файловые системы**  
Можно обрабатывать разные типы файлов (текстовые, изображения, видео) с разными операциями.  

### **3️⃣ Обработка документов**  
Например, в Word можно добавлять форматирование, экспорт в PDF, HTML, Markdown.  

### **4️⃣ Боты и ИИ в играх**  
Можно применять разные стратегии поведения в зависимости от типа игрового объекта.  

---

## 🔁 **Где можно применять "Посетителя"?**  

✅ **HTML-документы** – обход элементов и изменение структуры.  
✅ **Файловые системы** – выполнение операций над файлами.  
✅ **Игры** – поведение ботов в зависимости от объектов.  
✅ **Финансовые отчёты** – анализ данных в разной форме.  

---

## 🎬 **Вывод**  

✅ **Паттерн "Посетитель" позволяет добавлять новые операции без изменения классов.**  
✅ **Избавляет от жёсткой связанности между объектами и их поведением.**  
✅ **Позволяет легко расширять систему, добавляя новых посетителей.**  

🚀 **Где использовать?**  
- HTML-документы, файловые системы, игры, финансовые отчёты.  
