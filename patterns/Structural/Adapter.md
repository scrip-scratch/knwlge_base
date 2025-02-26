# 🎯 **Паттерн Адаптер (Adapter) в TypeScript**  

**Адаптер (Adapter)** — это **структурный паттерн**, который позволяет объектам с **несовместимыми интерфейсами работать вместе**.  

---

## 🔥 **Для чего нужен паттерн Адаптер?**  

✔ **Совместимость** — помогает адаптировать устаревший код к новым требованиям.  
✔ **Гибкость** — позволяет заменить одну реализацию на другую без изменения основного кода.  
✔ **Инкапсуляция** — скрывает детали преобразования интерфейсов.  
✔ **Упрощение интеграции** — позволяет использовать сторонние библиотеки, API или устройства с другим интерфейсом.  

---

## 📌 **Реальный пример: адаптер для зарядки телефонов**  

Допустим, у нас есть **новая зарядка с разъёмом Type-C**, но у нас **старый телефон с Micro-USB**. Нам нужен **адаптер**, который позволит подключить старый телефон к новой зарядке.

### 1️⃣ **Создаём несовместимые интерфейсы**  

```typescript
// Новый интерфейс зарядного устройства (Type-C)
interface TypeCCharger {
    chargeWithTypeC(): void;
}

// Старый телефон с Micro-USB
class OldPhone {
    chargeWithMicroUSB(): void {
        console.log("🔌 Зарядка через Micro-USB...");
    }
}
```

### 2️⃣ **Создаём адаптер**  

```typescript
class MicroUSBtoTypeCAdapter implements TypeCCharger {
    private oldPhone: OldPhone;

    constructor(oldPhone: OldPhone) {
        this.oldPhone = oldPhone;
    }

    chargeWithTypeC(): void {
        console.log("⚡ Используем адаптер Micro-USB -> Type-C");
        this.oldPhone.chargeWithMicroUSB();
    }
}
```

### 3️⃣ **Используем адаптер**  

```typescript
const oldPhone = new OldPhone();
const adapter = new MicroUSBtoTypeCAdapter(oldPhone);

adapter.chargeWithTypeC(); 
// ⚡ Используем адаптер Micro-USB -> Type-C
// 🔌 Зарядка через Micro-USB...
```

✅ Теперь наш **старый телефон (Micro-USB) работает с новой зарядкой (Type-C)**!  

---

## 📌 **Другой пример: адаптер для работы с API**  

Допустим, у нас есть **новый сервис данных**, который работает с **JSON**, но у нас есть **старая система**, которая использует **XML**. Нам нужен **адаптер**, который будет **конвертировать JSON в XML**.

### 1️⃣ **Создаём несовместимые интерфейсы**  

```typescript
// Старый интерфейс, работающий с XML
interface XMLService {
    getDataXML(): string;
}

// Новый сервис, работающий с JSON
class JSONService {
    getDataJSON(): object {
        return { name: "John", age: 30 };
    }
}
```

### 2️⃣ **Создаём адаптер JSON -> XML**  

```typescript
class JSONtoXMLAdapter implements XMLService {
    private jsonService: JSONService;

    constructor(jsonService: JSONService) {
        this.jsonService = jsonService;
    }

    getDataXML(): string {
        const jsonData = this.jsonService.getDataJSON();
        return `<user><name>${jsonData.name}</name><age>${jsonData.age}</age></user>`;
    }
}
```

### 3️⃣ **Используем адаптер**  

```typescript
const jsonService = new JSONService();
const adapter = new JSONtoXMLAdapter(jsonService);

console.log(adapter.getDataXML()); 
// <user><name>John</name><age>30</age></user>
```

✅ Теперь **новый JSON-сервис** работает со **старой системой, ожидающей XML**!  

---

## 🔥 **Когда использовать паттерн Адаптер?**  

✅ Когда **нужно использовать сторонний код**, но он имеет **неудобный интерфейс**.  
✅ Когда **старый код не совместим с новым** (например, старые библиотеки, API).  
✅ Когда **замена кода невозможна** (например, код из внешнего SDK или устаревшей системы).  

---

## 🎬 **Вывод**  

- **Адаптер** — это мост между **несовместимыми интерфейсами**.  
- Позволяет **использовать старый код** с новыми API, библиотеками или системами.  
- **Применяется, когда нельзя изменять код** (например, сторонние библиотеки или устаревшие системы).  

🔥 Используй паттерн **Адаптер**, если хочешь сделать код **более гибким и совместимым**! 🚀  
