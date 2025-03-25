# 🎭 **Архитектурный паттерн MVVM (Model-View-ViewModel) в TypeScript**  

## 📝 **Что такое MVVM?**  
**MVVM (Model-View-ViewModel)** — это **архитектурный паттерн**, который помогает разделять логику приложения на три уровня:  
- **Model (Модель)** — отвечает за данные и бизнес-логику.  
- **View (Представление)** — отвечает за отображение данных.  
- **ViewModel (Модель представления)** — связывает Model и View, содержит логику представления и управляет состоянием UI.  

📌 **Чем отличается от MVC?**  
В **MVVM** **View не взаимодействует с Model напрямую**. Вместо этого **ViewModel управляет View**, а View подписывается на изменения ViewModel.  

---

## 🚀 **Как работает MVVM?**  

### 🔹 **1. Model (Модель)**  
**Отвечает за:**  
✅ **Хранение данных и работу с API** (например, получение списка товаров).  
✅ **Бизнес-логику** (например, проверку данных перед сохранением).  

### 🔹 **2. ViewModel (Модель представления)**  
**Отвечает за:**  
✅ **Связывает Model и View** (держит состояние UI, трансформирует данные).  
✅ **Логика представления** (например, форматирование даты, фильтрация списка).  
✅ **Не имеет ссылки на View!**  

### 🔹 **3. View (Представление)**  
**Отвечает за:**  
✅ **Отображение данных из ViewModel**.  
✅ **Обновляется при изменении ViewModel** (реактивность, подписки).  

📌 **Простой пример работы MVVM:**  

1. **Пользователь нажимает "Добавить товар".**  
2. **ViewModel получает команду, создаёт новый товар и передаёт в Model.**  
3. **Model обновляет данные и уведомляет ViewModel.**  
4. **ViewModel обновляет View, и пользователь видит изменения.**  

---

## 🔥 **Пример MVVM на TypeScript**  

### 📌 **1. Создаём Model (Модель)**  

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
- `Product` — класс товара.  
- `ProductModel` — **управляет товарами** (добавляет, получает).  

---

### 📌 **2. Создаём ViewModel (Модель представления)**  

```typescript
class ProductViewModel {
    private model: ProductModel;
    private products: Product[] = [];
    private listeners: (() => void)[] = [];

    constructor(model: ProductModel) {
        this.model = model;
    }

    addProduct(name: string, price: number): void {
        const product = new Product(name, price);
        this.model.addProduct(product);
        this.updateProducts();
    }

    getProducts(): Product[] {
        return this.products;
    }

    subscribe(listener: () => void): void {
        this.listeners.push(listener);
    }

    private updateProducts(): void {
        this.products = this.model.getProducts();
        this.notifyListeners();
    }

    private notifyListeners(): void {
        this.listeners.forEach(listener => listener());
    }
}
```

📌 **Что тут происходит?**  
- `ProductViewModel` управляет Model и **обновляет данные** при изменении.  
- `subscribe()` позволяет View **подписываться на обновления**.  
- `notifyListeners()` уведомляет View, когда нужно обновить интерфейс.  

---

### 📌 **3. Создаём View (Представление)**  

```typescript
class ProductView {
    constructor(private viewModel: ProductViewModel) {
        this.viewModel.subscribe(() => this.render());
    }

    render(): void {
        console.clear();
        console.log("📋 Список товаров:");
        this.viewModel.getProducts().forEach(product => {
            console.log(`🛒 ${product.name} - ${product.price}₽`);
        });
    }
}
```

📌 **Что тут происходит?**  
- `ProductView` подписывается на изменения ViewModel.  
- `render()` обновляет UI при изменении данных.  

---

## 🎬 **Используем наш MVVM**  

```typescript
const model = new ProductModel();
const viewModel = new ProductViewModel(model);
const view = new ProductView(viewModel);

// Добавляем товары
viewModel.addProduct("Ноутбук", 50000);
viewModel.addProduct("Смартфон", 30000);
viewModel.addProduct("Наушники", 5000);
```

### **🔹 Вывод в консоли:**
```
📋 Список товаров:
🛒 Ноутбук - 50000₽
🛒 Смартфон - 30000₽
🛒 Наушники - 5000₽
```

---

## 🏆 **Преимущества MVVM**  

✅ **Чёткое разделение логики, представления и данных**.  
✅ **ViewModel не зависит от UI** → проще тестировать код.  
✅ **Легко внедрять реактивность** (например, с RxJS в Angular).  

---

## ❌ **Недостатки MVVM**  

⚠️ **Может усложнить простые приложения**.  
⚠️ **ViewModel может разрастаться** (слишком много логики в одном месте).  

---

## 📌 **Где применяется MVVM?**  

✅ **Веб-приложения (Angular, Vue, React + MobX/Redux)**.  
✅ **Мобильные приложения (Flutter, Kotlin + Jetpack Compose, SwiftUI)**.  
✅ **Игры (в Unity MVVM часто используется для UI)**.  

---

## 🚀 **Вывод**  

✅ **MVVM помогает отделить логику, данные и представление**.  
✅ **Позволяет легко тестировать и расширять код**.  
✅ **Используется в вебе, мобильных приложениях и играх**.  
