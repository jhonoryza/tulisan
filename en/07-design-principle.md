# Design Principles

Design principles are general guidelines or rules for building code structure that is:

- Easy to understand
- Easy to maintain
- Easy to extend
- Not fragile when changed

They are not mandatory rules like syntax, but proven best practices that help maintain software quality.

| Principle | Category                         | Focus                    |
| --------- | -------------------------------- | ------------------------ |
| SOLID     | Object-Oriented Design Principle | Healthy OOP structure    |
| KISS      | General Design Principle         | Simpler is better        |
| DRY       | Code Quality Principle           | Avoid duplication        |
| YAGNI     | Agile / Development Principle    | Don't over-engineer      |

## SOLID

A set of 5 OOP (Object-Oriented Programming) principles:

S - Single Responsibility Principle

- One class / module should have only 1 responsibility.
- ❌ Don't let one class handle database, validation, and email sending at the same time.
- ✅ Split into multiple classes.

O - Open/Closed Principle

- Code should be open for extension, but closed for modification.
- Meaning: add features without breaking existing code.

L - Liskov Substitution Principle

- A subclass should be able to replace its parent class without breaking the program.

I - Interface Segregation Principle

- Don't force a class to implement an interface it doesn't need.

D - Dependency Inversion Principle

- Depend on abstractions (interfaces), not on concrete classes.

## KISS - Keep It Simple, Stupid

- Make the solution as simple as possible.
- ❌ Don't create a very complex system if the problem is simple.
- ✅ Choose the simplest solution that works well.

## DRY - Don't Repeat Yourself

- Don't duplicate code / logic.
- If the same logic appears in many places → make it a reusable function or component.

## YAGNI - You Aren't Gonna Need It

- Don't build features that are not yet needed.
- ❌ "We might need it later..."
- ✅ Implement only what is truly needed right now.
