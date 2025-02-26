# 🎨 **Паттерн "Абстрактная фабрика" (Abstract Factory) в TypeScript**  

## 🔥 **Что такое Абстрактная фабрика?**  

**Абстрактная фабрика (Abstract Factory)** — это **порождающий паттерн**, который **предоставляет интерфейс** для создания **семейства взаимосвязанных объектов** **без привязки к конкретным классам**.  

---

## 🎯 **Когда использовать Абстрактную фабрику?**  

✅ Когда **нужно создавать семейства объектов**, которые логически связаны между собой.  
✅ Когда код **не должен зависеть от конкретных классов создаваемых объектов**.  
✅ Когда **продукты внутри одной фабрики должны сочетаться** (например, интерфейсные элементы в Windows и macOS).  

---

## 📌 **Пример без Абстрактной фабрики (Проблема)**  

Допустим, мы создаём **интерфейсные элементы** (кнопки и чекбоксы) для **двух операционных систем**:  

- **Windows**  
- **macOS**  

Если не использовать **Абстрактную фабрику**, нам придётся жёстко привязываться к классам:

```typescript
class WinButton {
    render(): void {
        console.log("🖱 Кнопка в стиле Windows");
    }
}

class MacButton {
    render(): void {
        console.log("🍏 Кнопка в стиле macOS");
    }
}

// Использование
const button = new WinButton();  // Жёсткая привязка!
button.render();
```

### ❌ **Проблемы такого кода**  

1. **Жёсткая привязка к конкретным классам**.  
2. **Трудности при добавлении нового стиля** (например, для Linux).  
3. **Разные элементы могут плохо сочетаться друг с другом**.  

---

## ✅ **Используем Абстрактную фабрику**  

### 1️⃣ **Создаём общий интерфейс для кнопок и чекбоксов**  

```typescript
interface Button {
    render(): void;
}

interface Checkbox {
    check(): void;
}
```

### 2️⃣ **Создаём конкретные реализации для Windows и macOS**  

```typescript
class WinButton implements Button {
    render(): void {
        console.log("🖱 Кнопка в стиле Windows");
    }
}

class MacButton implements Button {
    render(): void {
        console.log("🍏 Кнопка в стиле macOS");
    }
}

class WinCheckbox implements Checkbox {
    check(): void {
        console.log("☑ Чекбокс в стиле Windows");
    }
}

class MacCheckbox implements Checkbox {
    check(): void {
        console.log("✅ Чекбокс в стиле macOS");
    }
}
```

### 3️⃣ **Создаём Абстрактную фабрику**  

```typescript
interface GUIFactory {
    createButton(): Button;
    createCheckbox(): Checkbox;
}
```

### 4️⃣ **Создаём конкретные фабрики для Windows и macOS**  

```typescript
class WinFactory implements GUIFactory {
    createButton(): Button {
        return new WinButton();
    }

    createCheckbox(): Checkbox {
        return new WinCheckbox();
    }
}

class MacFactory implements GUIFactory {
    createButton(): Button {
        return new MacButton();
    }

    createCheckbox(): Checkbox {
        return new MacCheckbox();
    }
}
```

### 5️⃣ **Используем фабрику**  

```typescript
function createUI(factory: GUIFactory) {
    const button = factory.createButton();
    const checkbox = factory.createCheckbox();

    button.render();
    checkbox.check();
}

// Используем фабрику для Windows
console.log("🔵 Интерфейс Windows:");
const winFactory = new WinFactory();
createUI(winFactory);

// 🖱 Кнопка в стиле Windows
// ☑ Чекбокс в стиле Windows

console.log("\n🍏 Интерфейс macOS:");
const macFactory = new MacFactory();
createUI(macFactory);

// 🍏 Кнопка в стиле macOS
// ✅ Чекбокс в стиле macOS
```

---

## 🔥 **Что мы получили?**  

✅ **Чёткое разделение логики создания объектов**.  
✅ **Гибкость** – можно легко добавлять новые семейства интерфейсов (например, для Linux).  
✅ **Гарантия совместимости** – все элементы внутри фабрики работают вместе.  

---

## 📌 **Другой пример: Фабрика животных для разных континентов**  

Предположим, у нас есть **африканские и североамериканские животные**.  

### 1️⃣ **Создаём интерфейсы для животных**  

```typescript
interface Herbivore {
    eat(): void;
}

interface Carnivore {
    hunt(prey: Herbivore): void;
}
```

### 2️⃣ **Создаём конкретные классы животных**  

```typescript
class Gazelle implements Herbivore {
    eat(): void {
        console.log("🦌 Газель ест траву.");
    }
}

class Bison implements Herbivore {
    eat(): void {
        console.log("🐂 Бизон ест траву.");
    }
}

class Lion implements Carnivore {
    hunt(prey: Herbivore): void {
        console.log(`🦁 Лев охотится на ${prey.constructor.name}.`);
    }
}

class Wolf implements Carnivore {
    hunt(prey: Herbivore): void {
        console.log(`🐺 Волк охотится на ${prey.constructor.name}.`);
    }
}
```

### 3️⃣ **Создаём Абстрактную фабрику для животных**  

```typescript
interface AnimalFactory {
    createHerbivore(): Herbivore;
    createCarnivore(): Carnivore;
}
```

### 4️⃣ **Создаём конкретные фабрики для Африки и Северной Америки**  

```typescript
class AfricaFactory implements AnimalFactory {
    createHerbivore(): Herbivore {
        return new Gazelle();
    }

    createCarnivore(): Carnivore {
        return new Lion();
    }
}

class NorthAmericaFactory implements AnimalFactory {
    createHerbivore(): Herbivore {
        return new Bison();
    }

    createCarnivore(): Carnivore {
        return new Wolf();
    }
}
```

### 5️⃣ **Используем фабрику**  

```typescript
function simulateEcosystem(factory: AnimalFactory) {
    const herbivore = factory.createHerbivore();
    const carnivore = factory.createCarnivore();

    herbivore.eat();
    carnivore.hunt(herbivore);
}

console.log("🌍 Африка:");
simulateEcosystem(new AfricaFactory());
// 🦌 Газель ест траву.
// 🦁 Лев охотится на Gazelle.

console.log("\n🌎 Северная Америка:");
simulateEcosystem(new NorthAmericaFactory());
// 🐂 Бизон ест траву.
// 🐺 Волк охотится на Bison.
```

---

## 📊 **Когда использовать Абстрактную фабрику?**  

✅ **Когда нужно создавать семейства связанных объектов** (**Windows/macOS, Африка/Америка, SQL/NoSQL**).  
✅ **Когда требуется независимость от конкретных классов** (например, **подключение к разным базам данных**).  
✅ **Когда важно, чтобы все создаваемые объекты работали вместе** (**например, элементы интерфейса в одном стиле**).  

---

## 🎬 **Вывод**  

- **Абстрактная фабрика** позволяет **создавать семейства объектов**, не указывая их конкретные классы.  
- Подходит, когда **объекты внутри одной фабрики должны сочетаться**.  
- Делает код **гибким, расширяемым и независимым от конкретных реализаций**.  
