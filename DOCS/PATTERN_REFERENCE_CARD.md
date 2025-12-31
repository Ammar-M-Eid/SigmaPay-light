# SigmaPay Design Patterns - Quick Reference Card

## 🎯 Pattern Overview

This card provides a quick reference for all 12 GoF design patterns implemented in the SigmaPay system.

---

## 🏗️ CREATIONAL PATTERNS (3)

### 1️⃣ Factory Method
```
Purpose: Create payment objects without specifying exact class
Problem: IF/ELSE chains for payment creation
Solution: PaymentCreator abstract class

┌─────────────────────┐
│  PaymentCreator     │ (abstract)
├─────────────────────┤
│ +createPayment()    │
│ +processPayment()   │
└─────────────────────┘
         ▲
         │
┌────────┴────────┐
│   Concrete      │
│ PaymentCreator  │
└─────────────────┘
         │
    ┌────┴────┐
    │ creates │
    ▼         ▼
CardPayment  WalletPayment
```

**When to use:** Adding new payment types  
**Benefit:** No code modification needed

---

### 2️⃣ Builder
```
Purpose: Build complex Report objects step-by-step
Problem: Report construction is complex and inconsistent
Solution: ReportBuilder fluent interface

┌─────────────────────┐
│   ReportBuilder     │
├─────────────────────┤
│ +buildHeader()      │──┐
│ +buildSection()     │  │ Fluent
│ +buildChart()       │  │ API
│ +buildSummary()     │  │
│ +getReport()        │◄─┘
└─────────────────────┘
         │
         │ builds
         ▼
    ┌────────┐
    │ Report │
    └────────┘
```

**When to use:** Creating reports  
**Benefit:** Flexible, readable construction

---

### 3️⃣ Singleton
```
Purpose: Ensure single instance of NotificationManager
Problem: Multiple instances cause inconsistency
Solution: Private constructor + static getInstance()

┌──────────────────────────┐
│  NotificationManager     │ ◄── Only one
├──────────────────────────┤      instance
│ -instance: static        │
│ -NotificationManager()   │ (private)
│ +getInstance(): static   │
│ +notify()                │
└──────────────────────────┘

Also applies to: Scheduler, DatabaseConnectionPool
```

**When to use:** Global coordination needed  
**Benefit:** Centralized control

---

## 🔌 STRUCTURAL PATTERNS (5)

### 4️⃣ Facade
```
Purpose: Simplify access to 7 controllers
Problem: UI must know about all controllers
Solution: Single SigmaPayFacade entry point

        ┌──────────────────┐
        │ SigmaPayFacade   │ ◄── Single entry
        └────────┬─────────┘      point
                 │
    ┌────────────┼────────────┐
    ▼            ▼            ▼
IControllerAuth  IControllerPayments  ...
```

**When to use:** All external requests  
**Benefit:** Simplified API, loose coupling

---

### 5️⃣ Adapter
```
Purpose: Unify different payment gateway APIs
Problem: Each gateway has different interface
Solution: Adapter for each gateway

┌─────────────────────────┐
│ PaymentGatewayAdapter   │ (interface)
├─────────────────────────┤
│ +processTransaction()   │
│ +refund()               │
└─────────────────────────┘
           ▲
           │ implements
    ┌──────┼──────┐
    │      │      │
FawryAdapter  BankAPIAdapter  VodafoneCashAdapter
    │      │      │
    ▼      ▼      ▼
FawryAPI  BankAPI  VodafoneAPI
```

**When to use:** External payment processing  
**Benefit:** Vendor independence

---

### 6️⃣ Proxy
```
Purpose: Control access to authentication service
Problem: Security checks scattered everywhere
Solution: AuthProxy wraps AuthenticationService

┌──────────────┐         ┌─────────────────────┐
│ Controller   │────────►│    AuthProxy        │
└──────────────┘         ├─────────────────────┤
                         │ +authenticate()     │
                         │ +authorize()        │
                         │ +verify2FA()        │
                         └──────────┬──────────┘
                                    │ controls
                                    ▼
                         ┌─────────────────────┐
                         │ AuthenticationService│
                         └─────────────────────┘
```

**When to use:** Any authentication request  
**Benefit:** Centralized security, 2FA enforcement

---

### 7️⃣ Composite
```
Purpose: Support nested budget categories
Problem: List<String> can't represent hierarchy
Solution: Tree structure with Component interface

         ┌──────────────────────────┐
         │ BudgetCategoryComponent  │ (abstract)
         ├──────────────────────────┤
         │ +getAmount()             │
         │ +add()                   │
         │ +remove()                │
         └───────────┬──────────────┘
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
┌────────────────┐    ┌──────────────────┐
│ Leaf (Simple)  │    │ Group (Contains) │
├────────────────┤    ├──────────────────┤
│ - amount       │    │ - children[]     │
└────────────────┘    └────────┬─────────┘
                               │
                               │ contains
                               ▼
                      [More Components...]

Example:
  Food (Group)
    ├── Groceries (Leaf)
    ├── Restaurants (Leaf)
    └── Coffee (Leaf)
```

**When to use:** Budget category operations  
**Benefit:** Unlimited nesting, recursive totals

---

### 8️⃣ Flyweight
```
Purpose: Share Currency objects to save memory
Problem: Currency string repeated thousands of times
Solution: Single Currency object per unique value

┌──────────────────────┐
│ CurrencyFlyweight    │
├──────────────────────┤
│ -currencies: Map     │ (static)
│ +getCurrency(code)   │
└──────────┬───────────┘
           │ returns shared
           ▼
    ┌──────────┐
    │ Currency │ ◄── Shared instance
    ├──────────┤      (USD, EUR, etc.)
    │ -code    │
    │ -symbol  │
    └──────────┘

1000 transactions × "USD" = 1 Currency object!
```

**When to use:** Transaction creation  
**Benefit:** Significant memory savings

---

## 🎭 BEHAVIORAL PATTERNS (5)

### 9️⃣ Strategy
```
Purpose: Encapsulate payment processing algorithms
Problem: IF/ELSE for payment logic
Solution: Strategy interface with implementations

         ┌──────────────────┐
         │ PaymentStrategy  │ (interface)
         ├──────────────────┤
         │ +execute()       │
         │ +validate()      │
         │ +calculateFees() │
         └────────┬─────────┘
                  │
    ┌─────────────┼─────────────┐
    ▼             ▼             ▼
CardStrategy  WalletStrategy  BankStrategy

Payment object holds strategy and delegates to it
```

**When to use:** Payment processing  
**Benefit:** Runtime algorithm selection

---

### 🔟 Observer
```
Purpose: Event-driven notifications
Problem: Manual notification triggers everywhere
Solution: Subject notifies all observers automatically

┌─────────────────────┐
│ NotificationSubject │
├─────────────────────┤
│ +attach(observer)   │
│ +detach(observer)   │
│ +notifyObservers()  │
└──────────┬──────────┘
           │ notifies
    ┌──────┼──────┬──────┐
    ▼      ▼      ▼      ▼
BudgetObs GoalObs PaymentObs ContributionObs

Event occurs → All observers automatically updated
```

**When to use:** Budget/Goal/Payment events  
**Benefit:** Loose coupling, automatic updates

---

### 1️⃣1️⃣ Chain of Responsibility
```
Purpose: Chain validation steps together
Problem: Long validation method
Solution: Handler chain with next reference

┌──────────────────┐     ┌──────────────────┐
│ EmailFormat      │────►│ DuplicateUser    │
│ Handler          │next │ Handler          │
└──────────────────┘     └────────┬─────────┘
                                  │ next
                         ┌────────▼─────────┐
                         │ PasswordStrength │
                         │ Handler          │
                         └──────────────────┘

Request flows through chain until handled
```

**When to use:** Account creation, validation  
**Benefit:** Flexible validation pipeline

---

### 1️⃣2️⃣ State
```
Purpose: Manage goal/payment state transitions
Problem: String state with IF/ELSE logic
Solution: State objects with polymorphic behavior

         ┌────────────┐
         │ GoalState  │ (interface)
         ├────────────┤
         │ +start()   │
         │ +pause()   │
         │ +complete()│
         │ +cancel()  │
         └──────┬─────┘
                │
    ┌───────────┼───────────┐
    ▼           ▼           ▼
ActiveState  PausedState  CompletedState

Goal object holds current state
State transition = change state object
```

**When to use:** Goal/Payment state changes  
**Benefit:** Clear transitions, state-specific logic

---

### 1️⃣3️⃣ Template Method
```
Purpose: Define report generation skeleton
Problem: Duplicate report generation code
Solution: Abstract template with hook methods

┌──────────────────────┐
│ ReportTemplate       │ (abstract)
├──────────────────────┤
│ +generate()          │ ◄── Template method
│ #prepareHeader()     │
│ #collectData()       │ ◄── Hook methods
│ #formatContent()     │     (overridden)
│ #generateSummary()   │
│ #exportPDF()         │
└───────────┬──────────┘
            │
    ┌───────┼───────┐
    ▼       ▼       ▼
MonthlyReport  IncomeStatementReport  GroupSavingsReport

Template defines algorithm, subclasses fill details
```

**When to use:** Report generation  
**Benefit:** Consistent structure, code reuse

---

## 📊 Pattern Usage Matrix

| Pattern | Classes | Use Cases | Layer |
|---------|---------|-----------|-------|
| Factory Method | PaymentCreator | Create payments | Business |
| Builder | ReportBuilder | Build reports | Business |
| Singleton | NotificationManager | Global access | Business |
| Facade | SigmaPayFacade | Unified API | Presentation |
| Adapter | PaymentGatewayAdapter | External APIs | Business |
| Proxy | AuthProxy | Security | Business |
| Composite | BudgetCategoryComponent | Nested categories | Business |
| Flyweight | CurrencyFlyweight | Share data | Business |
| Strategy | PaymentStrategy | Payment algorithms | Business |
| Observer | NotificationSubject | Event notifications | Business |
| Chain | ValidationHandler | Validation | Business |
| State | GoalState, PaymentState | State management | Business |
| Template | ReportTemplate | Report generation | Business |

---

## 🚀 Quick Decision Guide

**Need to create objects?**
- Different types based on input? → **Factory Method**
- Complex construction? → **Builder**
- One instance globally? → **Singleton**

**Need to structure code?**
- Simplify complex subsystem? → **Facade**
- Unify different interfaces? → **Adapter**
- Control access? → **Proxy**
- Tree structure? → **Composite**
- Share common data? → **Flyweight**

**Need behavior flexibility?**
- Swap algorithms? → **Strategy**
- Notify multiple objects? → **Observer**
- Process request through chain? → **Chain of Responsibility**
- Manage state transitions? → **State**
- Define algorithm skeleton? → **Template Method**

---

## 📚 References

- Full details: `SigmaPay_Updated_ClassDiagram.puml`
- Implementation: `IMPLEMENTATION_GUIDE.md`
- Issues fixed: `DESIGN_CRITIQUE.md`
- Overview: `README_DESIGN_UPDATE.md`

---

## ✅ Pattern Checklist

When implementing, ensure:
- [ ] Pattern solves a real problem (not over-engineering)
- [ ] Correct pattern for the problem
- [ ] Proper layer placement
- [ ] Clear naming conventions
- [ ] Documented with comments
- [ ] Unit tests written
- [ ] Team understands pattern

---

**Remember:** Patterns are solutions to recurring problems. Don't force them where they don't fit!
