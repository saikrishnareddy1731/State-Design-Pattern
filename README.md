# State Pattern - Vending Machine System

**Type**: Behavioral Design Pattern

## Definition

**State Pattern**: Allow an object to alter its behavior when its internal state changes. The object will appear to change its class.

**Purpose**: Encapsulate varying behavior for the same object based on its internal state.

**Problem**: 
- Vending machine has different behaviors based on its state (No Money, Has Money, Sold Out)
- Using multiple if-else statements makes code complex and hard to maintain
- Difficult to add new states without modifying existing code

**Solution**: 
Create separate state classes for each state, and delegate behavior to the current state object.

---

## UML Diagrams

### 1. Complete Class Diagram

```mermaid
classDiagram
    %% ========== STATE INTERFACE ==========
    class State {
        <<interface>>
        +insertCoin()*
        +dispense()*
    }
    
    %% ========== CONCRETE STATES ==========
    class NoMoneyState {
        -VendingMachine machine
        +NoMoneyState(machine: VendingMachine)
        +insertCoin()
        +dispense()
    }
    
    class HasMoneyState {
        -VendingMachine machine
        +HasMoneyState(machine: VendingMachine)
        +insertCoin()
        +dispense()
    }
    
    class SoldOutState {
        +insertCoin()
        +dispense()
    }
    
    %% ========== CONTEXT ==========
    class VendingMachine {
        -State noMoneyState
        -State hasMoneyState
        -State soldOutState
        -State currentState
        +VendingMachine(hasItems: boolean)
        +insertCoin()
        +dispense()
        +setState(state: State)
        +getNoMoneyState(): State
        +getHasMoneyState(): State
        +getSoldOutState(): State
    }
    
    %% ========== CLIENT ==========
    class StateDemo {
        +main(args: String[])$
    }
    
    %% ========== RELATIONSHIPS ==========
    State <|.. NoMoneyState : implements
    State <|.. HasMoneyState : implements
    State <|.. SoldOutState : implements
    
    VendingMachine o-- State : has
    VendingMachine --> State : delegates to
    
    NoMoneyState --> VendingMachine : changes state
    HasMoneyState --> VendingMachine : changes state
    
    StateDemo ..> VendingMachine : uses
    
    note for State "Defines interface for\nencapsulating behavior"
    note for VendingMachine "Context - maintains\ncurrent state reference"
    note for NoMoneyState "State: Waiting for coin"
    note for HasMoneyState "State: Ready to dispense"
    note for SoldOutState "State: No items"
```

---

### 2. State Pattern Structure (Generic)

```mermaid
classDiagram
    class Context {
        -State state
        +request()
        +setState(state: State)
    }
    
    class State {
        <<interface>>
        +handle()*
    }
    
    class ConcreteStateA {
        +handle()
    }
    
    class ConcreteStateB {
        +handle()
    }
    
    Context o-- State : has
    State <|.. ConcreteStateA : implements
    State <|.. ConcreteStateB : implements
    ConcreteStateA ..> Context : changes state
    ConcreteStateB ..> Context : changes state
    
    note for Context "Maintains current state\nDelegates requests to state"
    note for State "Interface for behavior"
    note for ConcreteStateA "Implements behavior\nfor state A"
```

---

### 3. Sequence Diagram - State Transitions

```mermaid
sequenceDiagram
    participant Client as StateDemo
    participant Context as VendingMachine
    participant NoMoney as NoMoneyState
    participant HasMoney as HasMoneyState
    
    Note over Client,HasMoney: Initial State: NoMoneyState
    
    Client->>Context: new VendingMachine(true)
    activate Context
    Context->>NoMoney: create NoMoneyState
    Context->>HasMoney: create HasMoneyState
    Context->>Context: currentState = noMoneyState
    Context-->>Client: machine
    deactivate Context
    
    Note over Client,HasMoney: Action 1: Insert Coin
    
    Client->>Context: insertCoin()
    activate Context
    Context->>NoMoney: insertCoin()
    activate NoMoney
    NoMoney->>NoMoney: print "Coin inserted"
    NoMoney->>Context: setState(hasMoneyState)
    Context->>Context: currentState = hasMoneyState
    NoMoney-->>Context: done
    deactivate NoMoney
    Context-->>Client: done
    deactivate Context
    
    Note over Client,HasMoney: State Changed: HasMoneyState
    
    Note over Client,HasMoney: Action 2: Dispense
    
    Client->>Context: dispense()
    activate Context
    Context->>HasMoney: dispense()
    activate HasMoney
    HasMoney->>HasMoney: print "Item dispensed!"
    HasMoney->>Context: setState(noMoneyState)
    Context->>Context: currentState = noMoneyState
    HasMoney-->>Context: done
    deactivate HasMoney
    Context-->>Client: done
    deactivate Context
    
    Note over Client,HasMoney: State Changed Back: NoMoneyState
```

---

### 4. State Transition Diagram

```mermaid
stateDiagram-v2
    [*] --> NoMoneyState: VendingMachine created\n(hasItems = true)
    [*] --> SoldOutState: VendingMachine created\n(hasItems = false)
    
    NoMoneyState --> HasMoneyState: insertCoin()
    NoMoneyState --> NoMoneyState: dispense()\n"Insert coin first!"
    
    HasMoneyState --> NoMoneyState: dispense()\n"Item dispensed!"
    HasMoneyState --> HasMoneyState: insertCoin()\n"Coin already inserted"
    
    SoldOutState --> SoldOutState: insertCoin()\n"Machine sold out!"
    SoldOutState --> SoldOutState: dispense()\n"Nothing to dispense!"
    
    note right of NoMoneyState
        Waiting for coin
    end note
    
    note right of HasMoneyState
        Ready to dispense
    end note
    
    note right of SoldOutState
        No items available
    end note
```

---

### 5. Component Interaction Diagram

```mermaid
flowchart TB
    Client[Client/StateDemo]
    
    Context[VendingMachine<br/>Context]
    
    State[State Interface<br/>insertCoin, dispense]
    
    NoMoney[NoMoneyState<br/>Waiting for coin]
    
    HasMoney[HasMoneyState<br/>Ready to dispense]
    
    SoldOut[SoldOutState<br/>No items]
    
    Client -->|calls methods| Context
    Context -->|delegates to| State
    State -.->|implemented by| NoMoney
    State -.->|implemented by| HasMoney
    State -.->|implemented by| SoldOut
    
    NoMoney -->|transitions to| HasMoney
    HasMoney -->|transitions to| NoMoney
    
    style Client fill:#e3f2fd
    style Context fill:#fff3e0
    style State fill:#f3e5f5
    style NoMoney fill:#ffebee
    style HasMoney fill:#e8f5e9
    style SoldOut fill:#fbe9e7
```

---

### 6. Object Diagram - Runtime Instance

```mermaid
graph TB
    subgraph "Client"
        CL[client: StateDemo]
    end
    
    subgraph "Context"
        VM["machine: VendingMachine<br/>currentState = noMoneyState"]
    end
    
    subgraph "States"
        NM["noMoneyState: NoMoneyState<br/>machine reference"]
        HM["hasMoneyState: HasMoneyState<br/>machine reference"]
        SO["soldOutState: SoldOutState"]
    end
    
    CL -->|uses| VM
    VM -->|has| NM
    VM -->|has| HM
    VM -->|has| SO
    VM -.->|current| NM
    
    style CL fill:#e1f5ff
    style VM fill:#fff4e1
    style NM fill:#ffebee
    style HM fill:#e8f5e9
    style SO fill:#fbe9e7
```

---

## Pattern Components

| Component | Class | Description |
|-----------|-------|-------------|
| **Context** | VendingMachine | Maintains current state reference |
| **State Interface** | State | Defines common interface for all states |
| **Concrete State 1** | NoMoneyState | Behavior when waiting for coin |
| **Concrete State 2** | HasMoneyState | Behavior when ready to dispense |
| **Concrete State 3** | SoldOutState | Behavior when no items available |

---

## State Behaviors

| State | insertCoin() | dispense() |
|-------|-------------|-----------|
| **NoMoneyState** | Accept coin → Change to HasMoneyState | Display "Insert coin first!" |
| **HasMoneyState** | Display "Coin already inserted" | Dispense item → Change to NoMoneyState |
| **SoldOutState** | Display "Machine sold out!" | Display "Nothing to dispense!" |
