# 🌳 **Паттерн "Компоновщик" (Composite) в TypeScript**  

## 🎯 **Что такое паттерн "Компоновщик" (Composite)?**  

**Компоновщик (Composite)** — это **структурный паттерн**, который позволяет **работать с группой объектов так же, как с одним объектом**.  

📌 **Проще говоря:**  
- Позволяет **строить древовидные структуры** (например, меню, файловые системы, организационные структуры).  
- Позволяет **одинаково обращаться** как с отдельными объектами, так и с группами объектов.  

---

## 🔥 **Когда использовать паттерн "Компоновщик"?**  

✅ **Когда нужно обрабатывать как отдельные объекты, так и их группы одинаковым способом**.  
✅ **Когда структура объектов похожа на дерево** (например, файлы и папки, HTML-элементы, организации и их сотрудники).  
✅ **Когда нужно писать код, не задумываясь о том, с одиночным элементом мы работаем или с группой**.  

---

## ❌ **Проблема без "Компоновщика"**  

Представим, что мы разрабатываем **файловую систему**. У нас есть:  
- **Файлы** 📄  
- **Папки** 📁, которые могут содержать файлы и другие папки  

Если реализовать это без "Компоновщика", нам придётся **проверять тип каждого объекта вручную**:

```typescript
class File {
    constructor(public name: string) {}

    show(): void {
        console.log(`📄 Файл: ${this.name}`);
    }
}

class Folder {
    constructor(public name: string, public children: (File | Folder)[]) {}

    show(): void {
        console.log(`📁 Папка: ${this.name}`);
        for (const child of this.children) {
            child.show(); // Нужно проверять, файл это или папка
        }
    }
}
```

📌 **Проблема**: Код привязан к конкретным классам (`File`, `Folder`), **нет единого интерфейса**.

---

## ✅ **Используем паттерн "Компоновщик"**  

### **1️⃣ Создаём общий интерфейс для файлов и папок**  

```typescript
interface FileSystemComponent {
    show(indent?: string): void;
}
```

### **2️⃣ Реализуем класс "Файл"**  

```typescript
class File implements FileSystemComponent {
    constructor(private name: string) {}

    show(indent: string = ""): void {
        console.log(`${indent}📄 ${this.name}`);
    }
}
```

### **3️⃣ Реализуем класс "Папка"**  

```typescript
class Folder implements FileSystemComponent {
    private children: FileSystemComponent[] = [];

    constructor(private name: string) {}

    add(component: FileSystemComponent): void {
        this.children.push(component);
    }

    show(indent: string = ""): void {
        console.log(`${indent}📁 ${this.name}`);
        this.children.forEach(child => child.show(indent + "  "));
    }
}
```

### **4️⃣ Используем "Компоновщик"**  

```typescript
const file1 = new File("document.txt");
const file2 = new File("photo.jpg");
const file3 = new File("music.mp3");

const folder1 = new Folder("Downloads");
folder1.add(file1);
folder1.add(file2);

const folder2 = new Folder("Music");
folder2.add(file3);

const rootFolder = new Folder("Root");
rootFolder.add(folder1);
rootFolder.add(folder2);

rootFolder.show();
```

### 🖥 **Вывод в консоль**  

```
📁 Root
  📁 Downloads
    📄 document.txt
    📄 photo.jpg
  📁 Music
    📄 music.mp3
```

---

## 🔥 **Что мы получили?**  

✅ **Теперь можно работать с файлами и папками одинаково** – через `FileSystemComponent`.  
✅ **Можно легко добавлять новые элементы** без изменения кода.  
✅ **Структура гибкая и расширяемая** – можно добавить, например, **архивы**.  

---

## 📌 **Другой пример: Древовидное меню сайта**  

Допустим, у нас есть меню на сайте:  

- 📄 Главная  
- 📁 Каталог  
  - 📄 Компьютеры  
  - 📄 Телефоны  
  - 📁 Аксессуары  
    - 📄 Наушники  
    - 📄 Часы  
- 📄 Контакты  

### **1️⃣ Создаём интерфейс меню**  

```typescript
interface MenuComponent {
    show(indent?: string): void;
}
```

### **2️⃣ Реализуем пункт меню**  

```typescript
class MenuItem implements MenuComponent {
    constructor(private name: string) {}

    show(indent: string = ""): void {
        console.log(`${indent}📄 ${this.name}`);
    }
}
```

### **3️⃣ Реализуем подменю**  

```typescript
class Menu implements MenuComponent {
    private items: MenuComponent[] = [];

    constructor(private name: string) {}

    add(item: MenuComponent): void {
        this.items.push(item);
    }

    show(indent: string = ""): void {
        console.log(`${indent}📁 ${this.name}`);
        this.items.forEach(item => item.show(indent + "  "));
    }
}
```

### **4️⃣ Используем "Компоновщик"**  

```typescript
const mainMenu = new Menu("Главное меню");

const catalogMenu = new Menu("Каталог");
catalogMenu.add(new MenuItem("Компьютеры"));
catalogMenu.add(new MenuItem("Телефоны"));

const accessoriesMenu = new Menu("Аксессуары");
accessoriesMenu.add(new MenuItem("Наушники"));
accessoriesMenu.add(new MenuItem("Часы"));

catalogMenu.add(accessoriesMenu);

mainMenu.add(new MenuItem("Главная"));
mainMenu.add(catalogMenu);
mainMenu.add(new MenuItem("Контакты"));

mainMenu.show();
```

### 🖥 **Вывод в консоль**  

```
📁 Главное меню
  📄 Главная
  📁 Каталог
    📄 Компьютеры
    📄 Телефоны
    📁 Аксессуары
      📄 Наушники
      📄 Часы
  📄 Контакты
```

---

## 📊 **Когда использовать паттерн "Компоновщик"?**  

✅ **Когда структура данных представляет собой дерево**.  
✅ **Когда нужно обрабатывать отдельные элементы и их группы одинаково**.  
✅ **Когда нужно строить сложные иерархии объектов (например, UI-меню, файловые системы, организации, документы)**.  

---

## 🎬 **Вывод**  

- **Паттерн "Компоновщик"** позволяет работать с **отдельными объектами и их группами одинаково**.  
- Удобен для **древовидных структур** (меню, файлы, организации).  
- **Гибкий и легко расширяемый**.  
