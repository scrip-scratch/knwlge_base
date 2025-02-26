[назад](/readme.md)

# 🔨 **Паттерн "Строитель" (Builder) в TypeScript**  

## 🎯 **Что такое паттерн Строитель?**  

**Строитель (Builder)** — это **порождающий паттерн**, который позволяет **пошагово создавать сложные объекты** с различными вариациями, не изменяя код их создания.  

---

## 🔥 **Когда использовать Строитель?**  

✅ Когда объект **имеет много параметров** (например, сложные конфигурации).  
✅ Когда **нужно избегать перегруженных конструкторов**.  
✅ Когда процесс создания **должен быть гибким и настраиваемым**.  
✅ Когда **один и тот же код должен создавать разные представления объекта**.  

---

## 📌 **Пример без Строителя (Проблема)**  

Допустим, у нас есть класс **Burger**, который имеет множество параметров:  

```typescript
class Burger {
    constructor(
        public size: string,
        public cheese: boolean,
        public pepperoni: boolean,
        public lettuce: boolean,
        public tomato: boolean
    ) {}
}

// Создание объекта
const burger = new Burger("Большой", true, false, true, true);
console.log(burger);
```

### ❌ **Проблемы этого кода**  

- **Плохая читаемость** – много параметров, трудно понять, что за что отвечает.  
- **Сложно добавлять новые параметры** – если нужно добавить, например, бекон, придётся менять конструктор.  
- **Перегруженные конструкторы** – если параметров станет слишком много, придётся делать кучу конструкторов с разными вариациями.  

---

## ✅ **Используем Строитель**  

### 1️⃣ **Создаём класс "BurgerBuilder"**  

```typescript
class Burger {
    public size!: string;
    public cheese?: boolean;
    public pepperoni?: boolean;
    public lettuce?: boolean;
    public tomato?: boolean;

    constructor(builder: BurgerBuilder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.lettuce = builder.lettuce;
        this.tomato = builder.tomato;
    }
}

class BurgerBuilder {
    public size: string;
    public cheese: boolean = false;
    public pepperoni: boolean = false;
    public lettuce: boolean = false;
    public tomato: boolean = false;

    constructor(size: string) {
        this.size = size;
    }

    addCheese(): this {
        this.cheese = true;
        return this;
    }

    addPepperoni(): this {
        this.pepperoni = true;
        return this;
    }

    addLettuce(): this {
        this.lettuce = true;
        return this;
    }

    addTomato(): this {
        this.tomato = true;
        return this;
    }

    build(): Burger {
        return new Burger(this);
    }
}
```

### 2️⃣ **Создаём объекты с помощью строителя**  

```typescript
const burger1 = new BurgerBuilder("Средний")
    .addCheese()
    .addTomato()
    .build();

console.log(burger1);
// Burger { size: 'Средний', cheese: true, pepperoni: false, lettuce: false, tomato: true }

const burger2 = new BurgerBuilder("Большой")
    .addCheese()
    .addPepperoni()
    .addLettuce()
    .build();

console.log(burger2);
// Burger { size: 'Большой', cheese: true, pepperoni: true, lettuce: true, tomato: false }
```

---

## 🔥 **Что мы получили?**  

✅ **Гибкость** – можно легко добавлять новые параметры, не меняя конструктор.  
✅ **Читаемость** – теперь код понятный и легко читаемый.  
✅ **Удобство** – можно **пошагово строить объект** и включать только нужные параметры.  

---

## 📌 **Другой пример: Строитель для создания дома**  

### 1️⃣ **Создаём интерфейс строителя**  

```typescript
interface HouseBuilder {
    setWalls(walls: number): this;
    setDoors(doors: number): this;
    setWindows(windows: number): this;
    setGarage(hasGarage: boolean): this;
    build(): House;
}
```

### 2️⃣ **Создаём класс "House"**  

```typescript
class House {
    walls!: number;
    doors!: number;
    windows!: number;
    hasGarage!: boolean;

    constructor(builder: HouseBuilder) {
        this.walls = builder["walls"];
        this.doors = builder["doors"];
        this.windows = builder["windows"];
        this.hasGarage = builder["hasGarage"];
    }
}
```

### 3️⃣ **Создаём конкретного строителя**  

```typescript
class ConcreteHouseBuilder implements HouseBuilder {
    private walls: number = 0;
    private doors: number = 0;
    private windows: number = 0;
    private hasGarage: boolean = false;

    setWalls(walls: number): this {
        this.walls = walls;
        return this;
    }

    setDoors(doors: number): this {
        this.doors = doors;
        return this;
    }

    setWindows(windows: number): this {
        this.windows = windows;
        return this;
    }

    setGarage(hasGarage: boolean): this {
        this.hasGarage = hasGarage;
        return this;
    }

    build(): House {
        return new House(this);
    }
}
```

### 4️⃣ **Используем строителя**  

```typescript
const house = new ConcreteHouseBuilder()
    .setWalls(4)
    .setDoors(2)
    .setWindows(6)
    .setGarage(true)
    .build();

console.log(house);
// House { walls: 4, doors: 2, windows: 6, hasGarage: true }
```

---

## 📊 **Когда использовать Строитель?**  

✅ **Когда объект имеет много параметров**, и не все из них обязательны.  
✅ **Когда нужно избегать перегруженных конструкторов** с разными вариантами параметров.  
✅ **Когда процесс создания объекта должен быть гибким и поэтапным**.  
✅ **Когда один и тот же код должен создавать разные представления объекта**.  

---

## 🎬 **Вывод**  

- **Строитель** позволяет **создавать сложные объекты пошагово**.  
- Избегает **перегруженных конструкторов** и делает код **читаемым**.  
- Дает **гибкость** – можно легко добавлять новые параметры без изменения основного кода.  
