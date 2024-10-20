# Design-Patterns
Паттерны проектирования  - набор правил, методов для решения часто встречающихся проблем при проектировании программ. 

Классификация паттернов:
Порождающие паттерны - отвечают за удобное и безопасное создание новых объектов или даже целых семейств объектов. 
Структурные паттерны - отвечают за построение удобных в поддержке иерархий классов. 
Поведенческие паттерны - решают задачи эффективного и безопасного взаимодействия между объектами программы. 

Порождающие паттерны: 

Фабричный метод -  позволяет создавать объекты без указания конкретного класса создаваемого объекта(не используем new). 
Вместо этого, он предоставляет интерфейс для создания объектов, который может быть реализован в подклассах. При этом подклассы наследуют один интерфейс.
Что решает?
Легко добавлять новые классы без изменения существующего кода.
Упрощается управление созданием объектов.
```
protocol Shape {
    func draw() -> String
}

class Circle: Shape {
    func draw() -> String {
        return "Circle drawn"
    }
}

class Square: Shape {
    func draw() -> String {
        return "Square drawn"
    }
}

class ShapeFactory {
    func createShape(shapeType: String) -> Shape? {
        switch shapeType {
        case "circle":
            return Circle()
        case "square":
            return Square()
        default:
            return nil
        }
    }
}

let factory = ShapeFactory()

if let shape1 = factory.createShape(shapeType: "circle") {
    print(shape1.draw())  
}

if let shape2 = factory.createShape(shapeType: "square") {
    print(shape2.draw()) 
}
```

Абстрактная фабрика -  позволяет создавать семейства связанных объектов, не привязываясь к конкретным классам создаваемых объектов.
Часто приложения требуют создания нескольких связанных объектов, которые должны работать вместе. Например, пользовательский интерфейс может состоять из кнопок, чекбоксов и других элементов, которые должны быть согласованы по стилю и функциональности.
```
protocol Button {
    func render() -> String
}

protocol Checkbox {
    func render() -> String
}

class WindowsButton: Button {
    func render() -> String {
        return "Rendering a Windows Button"
    }
}

class WindowsCheckbox: Checkbox {
    func render() -> String {
        return "Rendering a Windows Checkbox"
    }
}

class MacOSButton: Button {
    func render() -> String {
        return "Rendering a macOS Button"
    }
}

class MacOSCheckbox: Checkbox {
    func render() -> String {
        return "Rendering a macOS Checkbox"
    }
}

protocol GUIFactory {
    func createButton() -> Button
    func createCheckbox() -> Checkbox
}

class WindowsFactory: GUIFactory {
    func createButton() -> Button {
        return WindowsButton()
    }
    
    func createCheckbox() -> Checkbox {
        return WindowsCheckbox()
    }
}

class MacOSFactory: GUIFactory {
    func createButton() -> Button {
        return MacOSButton()
    }
    
    func createCheckbox() -> Checkbox {
        return MacOSCheckbox()
    }
}

func clientCode(factory: GUIFactory) {
    let button = factory.createButton()
    let checkbox = factory.createCheckbox()
    
    print(button.render())      // В зависимости от фабрики, отобразится кнопка нужной платформы
    print(checkbox.render())    // В зависимости от фабрики, отобразится чекбокс нужной платформы
}

// Пример использования
let windowsFactory = WindowsFactory()
clientCode(factory: windowsFactory)

let macFactory = MacOSFactory()
clientCode(factory: macFactory)
```

Строитель - позволяет создавать сложные объекты пошагово. Строитель даёт возможность использовать один и тот же код строительства для получения разных представлений объектов.
```
class Pizza {
    var size: String?
    var cheese: Bool = false
    var pepperoni: Bool = false
    
    func description() -> String {
        var desc = "Pizza"
        if let size = size {
            desc += " of size \(size)"
        }
        if cheese {
            desc += ", with cheese"
        }
        if pepperoni {
            desc += ", with pepperoni"
        }
        return desc
    }
}
protocol PizzaBuilder {
    func setSize(_ size: String)
    func addCheese()
    func addPepperoni()
    func build() -> Pizza
}

class MargheritaPizzaBuilder: PizzaBuilder {
    private var pizza = Pizza()
    
    func setSize(_ size: String) {
        pizza.size = size
    }
    
    func addCheese() {
        pizza.cheese = true
    }
    
    func addPepperoni() {
        pizza.pepperoni = false
    }
    
    func build() -> Pizza {
        return pizza
    }
}

class PepperoniPizzaBuilder: PizzaBuilder {
    private var pizza = Pizza()
    
    func setSize(_ size: String) {
        pizza.size = size
    }
    
    func addCheese() {
        pizza.cheese = true
    }
    
    func addPepperoni() {
        pizza.pepperoni = true
    }
    
    func build() -> Pizza {
        return pizza
    }
}

class PizzaDirector {
    private var builder: PizzaBuilder
    
    init(builder: PizzaBuilder) {
        self.builder = builder
    }
    
    func constructPizza(size: String) {
        builder.setSize(size)
        builder.addCheese()
        builder.addPepperoni()
    }
}

let margheritaBuilder = MargheritaPizzaBuilder()
let director = PizzaDirector(builder: margheritaBuilder)

director.constructPizza(size: "Large")
let margheritaPizza = margheritaBuilder.build()
print(margheritaPizza.description())

let pepperoniBuilder = PepperoniPizzaBuilder()
let pepperoniDirector = PizzaDirector(builder: pepperoniBuilder)

pepperoniDirector.constructPizza(size: "Medium")
let pepperoniPizza = pepperoniBuilder.build()
print(pepperoniPizza.description())  
```
