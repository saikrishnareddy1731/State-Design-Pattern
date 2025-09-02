# 🎭 State Design Pattern in Java

The **State Pattern** is a behavioral design pattern that allows an object to alter its behavior when its internal state changes. It appears as if the object changed its class.

---

## 📖 Concept
- **Context**: Maintains an instance of a concrete state object.
- **State (interface)**: Defines a common interface for all concrete states.
- **Concrete States**: Implement behavior associated with a state of the context.

---

## 📂 Class Diagram (UML)

```mermaid
classDiagram
    class Context {
        - State state
        + setState(State state)
        + getState() State
    }

    class State {
        <<interface>>
        + doAction(Context context)
    }

    class StartState {
        + doAction(Context context)
        + toString() String
    }

    class StopState {
        + doAction(Context context)
        + toString() String
    }

    Context --> State
    StartState ..|> State
    StopState ..|> State
