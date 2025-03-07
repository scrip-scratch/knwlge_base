# 🎯 **Паттерн Шаблонный метод (Template Method) в TypeScript**  

**Шаблонный метод (Template Method)** — это **поведенческий паттерн**, который определяет **скелет алгоритма** в базовом классе, но **позволяет подклассам переопределять отдельные шаги** без изменения структуры всего алгоритма.  

---

## 🔥 **Для чего нужен паттерн Шаблонный метод?**  

✔ **Разделение логики** — общий алгоритм находится в родительском классе, детали переопределяются в дочерних.  
✔ **Избегание дублирования** — общие части кода находятся в одном месте, изменения легко вносить.  
✔ **Гибкость** — можно изменять только **нужные шаги** алгоритма, не трогая весь процесс.  
✔ **Простота расширения** — новые подклассы могут добавлять специфическую логику, не меняя базовый алгоритм.  

---

## 📌 **Пример: приготовление напитков (Чай и Кофе)**  

Допустим, у нас есть два вида горячих напитков: **чай и кофе**. Оба готовятся по **одинаковой схеме**, но с **разными шагами**.  
Мы выделим **общий алгоритм** в абстрактный класс и дадим возможность переопределять только конкретные шаги.  

### 1️⃣ **Создаём абстрактный класс с шаблонным методом**  

```typescript
abstract class Beverage {
    // Шаблонный метод: общий алгоритм приготовления
    prepareRecipe(): void {
        this.boilWater();
        this.brew();
        this.pourInCup();
        this.addCondiments();
    }

    private boilWater(): void {
        console.log("💧 Кипятим воду...");
    }

    private pourInCup(): void {
        console.log("☕ Наливаем в чашку...");
    }

    // Абстрактные методы: подклассы должны их реализовать
    protected abstract brew(): void;
    protected abstract addCondiments(): void;
}
```

### 2️⃣ **Создаём подклассы: Чай и Кофе**  

```typescript
class Tea extends Beverage {
    protected brew(): void {
        console.log("🍃 Завариваем чай...");
    }

    protected addCondiments(): void {
        console.log("🍋 Добавляем лимон...");
    }
}

class Coffee extends Beverage {
    protected brew(): void {
        console.log("☕ Завариваем кофе...");
    }

    protected addCondiments(): void {
        console.log("🍼 Добавляем молоко и сахар...");
    }
}
```

### 3️⃣ **Используем шаблонный метод**  

```typescript
const tea = new Tea();
console.log("➡ Готовим чай:");
tea.prepareRecipe();
// 💧 Кипятим воду...
// 🍃 Завариваем чай...
// ☕ Наливаем в чашку...
// 🍋 Добавляем лимон...

const coffee = new Coffee();
console.log("\n➡ Готовим кофе:");
coffee.prepareRecipe();
// 💧 Кипятим воду...
// ☕ Завариваем кофе...
// ☕ Наливаем в чашку...
// 🍼 Добавляем молоко и сахар...
```

✅ Теперь **алгоритм приготовления напитков** стандартизирован, но мы легко можем изменять отдельные шаги.  

---

## 📌 **Другой пример: процесс найма сотрудника**  

Допустим, у нас есть **разные типы сотрудников**: **разработчики и менеджеры**.  
Процесс найма у них **примерно одинаковый**, но отличается **проверка знаний**.  

### 1️⃣ **Создаём шаблонный метод**  

```typescript
abstract class HiringProcess {
    // Шаблонный метод
    hireEmployee(): void {
        this.reviewResume();
        this.conductInterview();
        this.takeTest();
        this.makeOffer();
    }

    private reviewResume(): void {
        console.log("📄 Проверяем резюме...");
    }

    private conductInterview(): void {
        console.log("💬 Проводим интервью...");
    }

    private makeOffer(): void {
        console.log("📃 Отправляем оффер...");
    }

    // Метод, который переопределяется в подклассах
    protected abstract takeTest(): void;
}
```

### 2️⃣ **Создаём конкретные реализации**  

```typescript
class DeveloperHiring extends HiringProcess {
    protected takeTest(): void {
        console.log("💻 Проводим тест по программированию...");
    }
}

class ManagerHiring extends HiringProcess {
    protected takeTest(): void {
        console.log("📊 Проводим тест на управление командой...");
    }
}
```

### 3️⃣ **Используем шаблонный метод**  

```typescript
const devHiring = new DeveloperHiring();
console.log("➡ Процесс найма разработчика:");
devHiring.hireEmployee();
// 📄 Проверяем резюме...
// 💬 Проводим интервью...
// 💻 Проводим тест по программированию...
// 📃 Отправляем оффер...

const managerHiring = new ManagerHiring();
console.log("\n➡ Процесс найма менеджера:");
managerHiring.hireEmployee();
// 📄 Проверяем резюме...
// 💬 Проводим интервью...
// 📊 Проводим тест на управление командой...
// 📃 Отправляем оффер...
```

✅ Теперь мы можем легко **добавлять новые типы сотрудников** без изменения основного алгоритма!  

---

## 🔥 **Когда использовать паттерн Шаблонный метод?**  

✅ Когда **у нескольких классов одинаковый алгоритм, но есть отличия в отдельных шагах**.  
✅ Когда **нужно вынести общий код в одно место, чтобы избежать дублирования**.  
✅ Когда **нужно заставить подклассы реализовать определённые шаги алгоритма**.  

---

## 🎬 **Вывод**  

- **Шаблонный метод** — это **каркас алгоритма** в базовом классе с возможностью переопределять **отдельные шаги**.  
- Помогает **избежать дублирования**, делает код **гибким и легко расширяемым**.  
- Используется, когда **несколько классов должны следовать одному алгоритму**, но **с разными деталями**.  
