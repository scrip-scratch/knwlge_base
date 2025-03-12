# 🪶 **Паттерн "Легковес" (Flyweight) в TypeScript**  

## 🎯 **Что такое паттерн "Легковес" (Flyweight)?**  

**Легковес (Flyweight)** — это **структурный паттерн**, который позволяет **экономить память**, **разделяя общий объект** между множеством похожих объектов.  

📌 **Проще говоря:**  
- Если у вас **очень много однотипных объектов**, они могут занимать **слишком много памяти**.  
- Вместо того чтобы **создавать новые объекты**, мы **используем уже существующие**, если они **одинаковые**.  
- Подходит для **оптимизации больших коллекций данных** (например, текст, графика, кэширование объектов).  

---

## 🔥 **Когда использовать "Легковес"?**  

✅ **Когда в системе создаётся слишком много однотипных объектов**.  
✅ **Когда нужно уменьшить потребление памяти**.  
✅ **Когда объект содержит неизменяемые (разделяемые) и изменяемые (уникальные) части**.  
✅ **Когда дорого создавать новые объекты, а можно переиспользовать старые**.  

---

## ❌ **Проблема без "Легковеса"**  

Допустим, мы разрабатываем **игру**, где на карте **тысячи деревьев** 🌳.  
Если каждое дерево хранит **цвет, текстуру, тип**, а также **координаты**, то памяти может не хватить.  

```typescript
class Tree {
    constructor(
        public type: string,
        public color: string,
        public texture: string,
        public x: number,
        public y: number
    ) {}

    draw(): void {
        console.log(`🌳 Дерево типа "${this.type}", цвет "${this.color}" на (${this.x}, ${this.y})`);
    }
}

// Генерация 1000 деревьев (проблема: у каждого свой уникальный тип, цвет, текстура)
const trees: Tree[] = [];
for (let i = 0; i < 1000; i++) {
    trees.push(new Tree("Oak", "Green", "Leafy", Math.random() * 100, Math.random() * 100));
}
```

📌 **Проблема**: Если каждое дерево занимает 100 байт, то **1000 деревьев = 100 000 байт (≈100 КБ)**!  

---

## ✅ **Используем "Легковес"**  

### **1️⃣ Разделяем данные на:**  
- **Общие** (разделяемые) – тип, цвет, текстура (одинаковые для всех деревьев).  
- **Уникальные** – координаты `x, y` (разные у каждого дерева).  

---

### **2️⃣ Создаём "Легковес" (общие данные дерева)**  

```typescript
class TreeType {
    constructor(public type: string, public color: string, public texture: string) {}

    draw(x: number, y: number): void {
        console.log(`🌳 Дерево типа "${this.type}", цвет "${this.color}" на (${x}, ${y})`);
    }
}
```

---

### **3️⃣ Создаём фабрику "Легковесов" (TreeFactory)**  

```typescript
class TreeFactory {
    private static treeTypes: Map<string, TreeType> = new Map();

    static getTreeType(type: string, color: string, texture: string): TreeType {
        const key = `${type}_${color}_${texture}`;
        if (!this.treeTypes.has(key)) {
            console.log(`Создаём новый объект: ${key}`);
            this.treeTypes.set(key, new TreeType(type, color, texture));
        }
        return this.treeTypes.get(key)!;
    }
}
```

---

### **4️⃣ Используем "Легковес" для оптимизации памяти**  

```typescript
class Tree {
    constructor(private treeType: TreeType, private x: number, private y: number) {}

    draw(): void {
        this.treeType.draw(this.x, this.y);
    }
}
```

---

### **5️⃣ Создаём деревья с "Легковесом"**  

```typescript
const trees: Tree[] = [];

for (let i = 0; i < 1000; i++) {
    const treeType = TreeFactory.getTreeType("Oak", "Green", "Leafy"); // Один и тот же объект!
    trees.push(new Tree(treeType, Math.random() * 100, Math.random() * 100));
}

// Рисуем деревья
trees.forEach(tree => tree.draw());
```

### 🖥 **Вывод в консоль**  

```
Создаём новый объект: Oak_Green_Leafy
🌳 Дерево типа "Oak", цвет "Green" на (23.5, 67.8)
🌳 Дерево типа "Oak", цвет "Green" на (12.1, 88.4)
🌳 Дерево типа "Oak", цвет "Green" на (99.3, 24.7)
...
```

---

## 🔥 **Что мы получили?**  

✅ **Теперь у всех деревьев один объект типа (`TreeType`)**.  
✅ **Экономия памяти**: вместо 1000 объектов **"типа"** у нас только **1**!  
✅ **Гибкость**: можно добавлять новые типы деревьев без переписывания кода.  

---

## 📌 **Другой пример: Оптимизация текстовых символов (Шрифты)**  

В текстовых редакторах, таких как Word или Google Docs, каждая **буква** может повторяться **тысячи раз**.  
Если **каждую букву хранить отдельно**, это **огромный расход памяти**.  

### **1️⃣ Создаём "Легковес" для букв**  

```typescript
class Letter {
    constructor(public char: string, public font: string, public size: number) {}

    draw(x: number, y: number): void {
        console.log(`📝 Символ "${this.char}" в шрифте "${this.font}", размер ${this.size} на (${x}, ${y})`);
    }
}
```

### **2️⃣ Фабрика букв**  

```typescript
class LetterFactory {
    private static letters: Map<string, Letter> = new Map();

    static getLetter(char: string, font: string, size: number): Letter {
        const key = `${char}_${font}_${size}`;
        if (!this.letters.has(key)) {
            console.log(`Создаём новый объект: ${key}`);
            this.letters.set(key, new Letter(char, font, size));
        }
        return this.letters.get(key)!;
    }
}
```

### **3️⃣ Используем "Легковес" для текста**  

```typescript
class DocumentSymbol {
    constructor(private letter: Letter, private x: number, private y: number) {}

    draw(): void {
        this.letter.draw(this.x, this.y);
    }
}

// Создаём документ
const documentText: DocumentSymbol[] = [];

const text = "HELLO";
for (let i = 0; i < text.length; i++) {
    const letter = LetterFactory.getLetter(text[i], "Arial", 12);
    documentText.push(new DocumentSymbol(letter, i * 10, 0));
}

// Отображаем текст
documentText.forEach(symbol => symbol.draw());
```

### 🖥 **Вывод в консоль**  

```
Создаём новый объект: H_Arial_12
Создаём новый объект: E_Arial_12
Создаём новый объект: L_Arial_12
Создаём новый объект: O_Arial_12
📝 Символ "H" в шрифте "Arial", размер 12 на (0, 0)
📝 Символ "E" в шрифте "Arial", размер 12 на (10, 0)
📝 Символ "L" в шрифте "Arial", размер 12 на (20, 0)
📝 Символ "L" в шрифте "Arial", размер 12 на (30, 0)
📝 Символ "O" в шрифте "Arial", размер 12 на (40, 0)
```

---

## 📊 **Когда использовать "Легковес"?**  

✅ **Когда создаётся очень много однотипных объектов**.  
✅ **Когда нужно уменьшить потребление памяти**.  
✅ **Когда есть неизменяемые и изменяемые данные в объекте**.  
✅ **Когда объектами можно управлять через фабрику**.  

---

## 🎬 **Вывод**  

- **"Легковес" позволяет экономить память** за счёт **разделения данных между объектами**.  
- **Полезен для больших коллекций данных** (графика, текст, игровые объекты).  
- **Работает через фабрику**, которая переиспользует уже созданные объекты.  
