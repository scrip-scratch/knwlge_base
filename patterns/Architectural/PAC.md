# 🎭 **Архитектурный паттерн PAC (Presentation-Abstraction-Control) в TypeScript**  

## 📝 **Что такое PAC?**  
**PAC (Presentation-Abstraction-Control)** — это **архитектурный паттерн**, который разделяет систему на **иерархические слои** (агенты), состоящие из:  

- **Presentation (Презентация)** — отвечает за взаимодействие с пользователем (UI).  
- **Abstraction (Абстракция)** — управляет данными и бизнес-логикой.  
- **Control (Контроль)** — связывает Presentation и Abstraction, управляет потоком данных между ними.  

📌 **Отличия PAC от других паттернов:**  
- **PAC строится как иерархия** независимых агентов, а не одна глобальная структура.  
- Каждый агент может **взаимодействовать с другими агентами** на разных уровнях.  
- Подходит для **многоуровневых интерфейсов** (например, сложные UI-приложения, операционные системы).  

---

## 🚀 **Как работает PAC?**  

📌 **Принцип работы PAC:**  

1. **Пользователь взаимодействует с Presentation** (например, нажимает кнопку).  
2. **Control передаёт команду в Abstraction** (логика обработки).  
3. **Abstraction обрабатывает данные и отправляет результат в Control**.  
4. **Control обновляет Presentation** (обновляет UI).  

📌 **Пример структуры PAC:**  
- Верхний уровень **(главный агент)** управляет подчинёнными агентами.  
- Подчинённые агенты могут **взаимодействовать между собой**.  

---

## 🔥 **Пример PAC на TypeScript**  

### 📌 **1. Создаём Abstraction (Абстракцию)**  

```typescript
class Product {
    constructor(public name: string, public price: number) {}
}

class ProductModel {
    private products: Product[] = [];
    
    addProduct(product: Product) {
        this.products.push(product);
    }

    getProducts(): Product[] {
        return this.products;
    }
}
```

📌 **Что тут происходит?**  
- `Product` — модель данных (товар).  
- `ProductModel` — управляет списком товаров (логика хранения).  

---

### 📌 **2. Создаём Control (Контроль)**  

```typescript
class ProductController {
    private model: ProductModel;
    private presentation: ProductView;

    constructor(model: ProductModel, presentation: ProductView) {
        this.model = model;
        this.presentation = presentation;
    }

    addProduct(name: string, price: number): void {
        const product = new Product(name, price);
        this.model.addProduct(product);
        this.updateView();
    }

    updateView(): void {
        this.presentation.render(this.model.getProducts());
    }
}
```

📌 **Что тут происходит?**  
- `ProductController` связывает модель (`ProductModel`) и представление (`ProductView`).  
- `addProduct()` управляет процессом добавления товара.  
- `updateView()` обновляет UI после изменения данных.  

---

### 📌 **3. Создаём Presentation (Презентацию)**  

```typescript
class ProductView {
    render(products: Product[]): void {
        console.clear();
        console.log("📋 Список товаров:");
        products.forEach(product => {
            console.log(`🛒 ${product.name} - ${product.price}₽`);
        });
    }
}
```

📌 **Что тут происходит?**  
- `ProductView` отвечает за отображение списка товаров.  
- `render()` обновляет UI при изменении данных.  

---

## 🎬 **Используем наш PAC**  

```typescript
const model = new ProductModel();
const view = new ProductView();
const controller = new ProductController(model, view);

// Добавляем товары
controller.addProduct("Ноутбук", 50000);
controller.addProduct("Смартфон", 30000);
controller.addProduct("Наушники", 5000);
```

### **🔹 Вывод в консоли:**
```
📋 Список товаров:
🛒 Ноутбук - 50000₽
🛒 Смартфон - 30000₽
🛒 Наушники - 5000₽
```

---

## 🏆 **Преимущества PAC**  

✅ **Модульность** → Можно разделять систему на независимые агенты.  
✅ **Гибкость** → Поддерживает сложные иерархии UI.  
✅ **Подходит для больших приложений** (например, настольные программы, операционные системы).  

---

## ❌ **Недостатки PAC**  

⚠️ **Сложность структуры** → Неподходящий для небольших проектов.  
⚠️ **Дополнительные накладные расходы** на передачу данных между агентами.  

---

## 📌 **Где применяется PAC?**  

✅ **Многоуровневые UI-приложения** (например, сложные десктопные программы).  
✅ **Операционные системы** (например, слои управления окнами).  
✅ **Игровые движки** (например, управление UI-панелями).  

---

## 🚀 **Вывод**  

✅ **PAC разделяет код на Presentation, Abstraction и Control.**  
✅ **Позволяет строить сложные многоуровневые интерфейсы.**  
✅ **Используется в настольных программах, операционных системах и играх.**  
