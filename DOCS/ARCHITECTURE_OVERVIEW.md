# SigmaPay Architecture Overview

## System Architecture Visualization

```
┌────────────────────────────────────────────────────────────────────────┐
│                        EXTERNAL CLIENTS                                 │
│  ┌──────────────────┐              ┌──────────────────┐                │
│  │  Mobile App      │              │   Web App        │                │
│  │  (Android/iOS)   │              │   (Browser)      │                │
│  └────────┬─────────┘              └────────┬─────────┘                │
│           │                                  │                          │
└───────────┼──────────────────────────────────┼──────────────────────────┘
            │                                  │
            │         HTTP/REST API            │
            └──────────────┬───────────────────┘
                           │
┌──────────────────────────▼────────────────────────────────────────────┐
│                   PRESENTATION LAYER                                   │
│  ┌──────────────────────────────────────────────────────────────┐    │
│  │                    SigmaPayFacade                             │    │
│  │              [Facade Pattern - Single Entry Point]            │    │
│  │  + handle(request): Response                                  │    │
│  └──────────────────────┬────────────────────────────────────────┘    │
│                         │ delegates to                                │
│    ┌────────────────────┼────────────────────┐                        │
│    ▼                    ▼                    ▼                         │
│  ┌─────────┐      ┌──────────┐        ┌──────────┐                   │
│  │Controller│      │Controller│   ...  │Controller│  (8 Controllers)  │
│  │  Auth   │      │ Payments │        │ Reports  │                   │
│  └────┬────┘      └────┬─────┘        └────┬─────┘                   │
└───────┼────────────────┼───────────────────┼────────────────────────┘
        │                │                   │
        │                │                   │
┌───────▼────────────────▼───────────────────▼────────────────────────┐
│                     BUSINESS/DOMAIN LAYER                             │
│                                                                        │
│  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  │
│  ┃  DESIGN PATTERNS (13 Patterns Implemented)                    ┃  │
│  ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛  │
│                                                                        │
│  ┌─────────────────────┐  ┌─────────────────────┐                    │
│  │   CREATIONAL (3)    │  │   STRUCTURAL (5)    │                    │
│  ├─────────────────────┤  ├─────────────────────┤                    │
│  │ • Factory Method    │  │ • Facade            │                    │
│  │ • Builder           │  │ • Adapter           │                    │
│  │ • Singleton         │  │ • Proxy             │                    │
│  └─────────────────────┘  │ • Composite         │                    │
│                            │ • Flyweight         │                    │
│  ┌─────────────────────┐  └─────────────────────┘                    │
│  │   BEHAVIORAL (5)    │                                              │
│  ├─────────────────────┤                                              │
│  │ • Strategy          │                                              │
│  │ • Observer          │                                              │
│  │ • Chain of Resp.    │                                              │
│  │ • State             │                                              │
│  │ • Template Method   │                                              │
│  └─────────────────────┘                                              │
│                                                                        │
│  ┌────────────────────────────────────────────────────────────┐      │
│  │                    SERVICES                                 │      │
│  ├────────────────────────────────────────────────────────────┤      │
│  │ AccountService          BudgetCreationService              │      │
│  │ BudgetAnalyticsService  GoalService                        │      │
│  │ GroupSavingsService     PaymentsServiceBL                  │      │
│  │ ReportingService        NotificationsServiceBL             │      │
│  │ PasswordManager         AuthenticationService              │      │
│  └──────────────────────────┬─────────────────────────────────┘      │
│                             │                                         │
│  ┌────────────────────────────────────────────────────────────┐      │
│  │                   DOMAIN ENTITIES                           │      │
│  ├────────────────────────────────────────────────────────────┤      │
│  │ UserAccount (+ Credentials, Profile, SecuritySettings)     │      │
│  │ Account                Transaction                          │      │
│  │ BudgetPlan             FinancialGoal                        │      │
│  │ Payment (Card, Wallet, Bank, GroupContribution)            │      │
│  │ GroupSavings           Report                               │      │
│  │ Money (Value Object)   Currency                             │      │
│  └──────────────────────────┬─────────────────────────────────┘      │
│                             │                                         │
└─────────────────────────────┼─────────────────────────────────────────┘
                              │
                              │ uses
                              │
┌─────────────────────────────▼─────────────────────────────────────────┐
│                     DATA ACCESS LAYER                                  │
│                                                                         │
│  ┌────────────────────────────────────────────────────────────┐       │
│  │                    REPOSITORIES                             │       │
│  ├────────────────────────────────────────────────────────────┤       │
│  │ IRepository<T>                                              │       │
│  │ AccountsRepository     UserRepository                       │       │
│  │ TransactionRepository  BudgetRepository                     │       │
│  │ GoalRepository         PaymentRepository                    │       │
│  │ GroupSavingsRepository NotificationRepository              │       │
│  └──────────────────────────┬─────────────────────────────────┘       │
│                             │                                          │
│  ┌──────────────────────────▼──────────────────────────────┐          │
│  │        DatabaseConnectionPool (Singleton)                │          │
│  │  ┌────────────────────────────────────────────────┐     │          │
│  │  │          Connection Pool                        │     │          │
│  │  │  [Connection 1] [Connection 2] ... [Conn N]   │     │          │
│  │  └────────────────────────────────────────────────┘     │          │
│  └──────────────────────────┬──────────────────────────────┘          │
│                             │                                          │
└─────────────────────────────┼──────────────────────────────────────────┘
                              │
                              │
                    ┌─────────▼─────────┐
                    │     DATABASE      │
                    │   (PostgreSQL)    │
                    └───────────────────┘
```

---

## Cross-Cutting Concerns

```
┌─────────────────────────────────────────────────────────────────────┐
│                   CROSS-CUTTING CONCERNS                             │
│  (Applied across all layers)                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────────┐  ┌──────────────────┐  ┌─────────────────┐  │
│  │   Security       │  │   Notifications  │  │   Validation    │  │
│  ├──────────────────┤  ├──────────────────┤  ├─────────────────┤  │
│  │ • AuthProxy      │  │ • NotificationMgr│  │ • ValidationChain│ │
│  │ • SessionManager │  │   (Singleton)    │  │ • PasswordPolicy│  │
│  │ • PasswordHasher │  │ • Observer Pattern│ │ • Handlers      │  │
│  │ • 2FA            │  │ • EmailService   │  │                 │  │
│  └──────────────────┘  │ • SMSService     │  └─────────────────┘  │
│                        └──────────────────┘                         │
└─────────────────────────────────────────────────────────────────────┘
```

---

## External System Integration

```
┌─────────────────────────────────────────────────────────────────────┐
│                   EXTERNAL SYSTEMS                                   │
│                   (via Adapter Pattern)                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│  │  FawryAPI    │  │  BankAPI     │  │ VodafoneAPI  │              │
│  │  (External)  │  │  (External)  │  │  (External)  │              │
│  └──────▲───────┘  └──────▲───────┘  └──────▲───────┘              │
│         │                 │                  │                       │
│         │                 │                  │                       │
│  ┌──────┴───────┐  ┌──────┴───────┐  ┌──────┴───────┐              │
│  │ FawryAdapter │  │BankAPIAdapter│  │VodafoneCashAd│              │
│  └──────────────┘  └──────────────┘  └──────────────┘              │
│         │                 │                  │                       │
│         └─────────────────┴──────────────────┘                       │
│                           │                                          │
│                 ┌─────────▼──────────┐                              │
│                 │ PaymentGateway     │                              │
│                 │     Adapter        │                              │
│                 │   (Interface)      │                              │
│                 └────────────────────┘                              │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Design Pattern Distribution by Layer

### Presentation Layer
- **Facade:** SigmaPayFacade (main entry point)
- **Proxy:** AuthProxy (security control)
- **Chain of Responsibility:** ValidationHandler (validation pipeline)

### Business/Domain Layer
- **Factory Method:** PaymentCreator
- **Builder:** ReportBuilder
- **Singleton:** NotificationManager, Scheduler
- **Adapter:** PaymentGatewayAdapter implementations
- **Composite:** BudgetCategoryComponent
- **Flyweight:** CurrencyFlyweight, MerchantTypeFlyweight
- **Strategy:** PaymentStrategy implementations
- **Observer:** NotificationSubject + Observers
- **State:** GoalState, PaymentState
- **Template Method:** ReportTemplate

### Data Access Layer
- **Singleton:** DatabaseConnectionPool
- **Repository Pattern:** All repositories

---

## Data Flow Examples

### Example 1: Process Payment

```
1. Mobile App sends payment request
   │
   ▼
2. SigmaPayFacade.processPayment()
   │
   ▼
3. IControllerPayments.ctlProcessPayment()
   │
   ▼
4. PaymentsServiceBL.processPayment()
   │
   ├──► PaymentCreator.createPayment() [Factory Method]
   │       │
   │       ├──► Creates CardPayment/WalletPayment/etc.
   │       │
   │       └──► Sets PaymentStrategy [Strategy]
   │
   ├──► PaymentState transition [State]
   │       │
   │       └──► PendingState → ProcessingState
   │
   ├──► PaymentGatewayAdapter.process() [Adapter]
   │       │
   │       └──► FawryAdapter/BankAPIAdapter/etc.
   │               │
   │               └──► External API call
   │
   ├──► NotificationManager.notify() [Singleton + Observer]
   │       │
   │       └──► PaymentObserver.update()
   │
   └──► PaymentRepository.save()
           │
           └──► Database
```

### Example 2: Create Budget

```
1. Web App sends budget creation request
   │
   ▼
2. SigmaPayFacade.createBudget()
   │
   ▼
3. IControllerBudget.ctlCreateBudget()
   │
   ▼
4. BudgetCreationService.createBudget()
   │
   ├──► BudgetCategoryGroup.add() [Composite]
   │       │
   │       └──► Builds category tree
   │
   ├──► Money creation [Value Object]
   │       │
   │       └──► CurrencyFlyweight.getCurrency() [Flyweight]
   │
   ├──► NotificationManager.notify() [Observer]
   │       │
   │       └──► BudgetObserver.update()
   │
   └──► BudgetRepository.save()
           │
           └──► Database
```

### Example 3: Generate Report

```
1. User requests monthly report
   │
   ▼
2. SigmaPayFacade.generateReport()
   │
   ▼
3. IControllerReports.ctlGenerateReport()
   │
   ▼
4. ReportingService.generateReport()
   │
   ├──► ReportBuilder [Builder]
   │       │
   │       ├──► buildHeader()
   │       ├──► buildSection()
   │       ├──► buildChart()
   │       └──► buildSummary()
   │
   ├──► MonthlyReport extends ReportTemplate [Template Method]
   │       │
   │       ├──► prepareHeader() (overridden)
   │       ├──► collectData() (overridden)
   │       ├──► formatContent() (overridden)
   │       └──► generate() (template method)
   │
   └──► TransactionRepository.findByFilter()
           │
           └──► Database query with ReportFilter [Parameter Object]
```

### Example 4: User Registration

```
1. User submits registration form
   │
   ▼
2. SigmaPayFacade.createAccount()
   │
   ▼
3. IControllerAuth.ctlCreateAccount()
   │
   ├──► ValidationHandler chain [Chain of Responsibility]
   │       │
   │       ├──► EmailFormatHandler
   │       ├──► DuplicateUserHandler
   │       ├──► PasswordStrengthHandler
   │       └──► IdentityVerificationHandler
   │
   └──► AccountService.createAccount()
           │
           ├──► Extract Class refactoring applied
           │       │
           │       ├──► Credentials created
           │       ├──► UserProfile created
           │       └──► SecuritySettings created
           │
           ├──► PasswordManager.hashPassword() [Extracted Class]
           │
           └──► UserRepository.save()
                   │
                   └──► Database
```

---

## Key Architectural Principles

### 1. Separation of Concerns
✅ Clear layer boundaries  
✅ Each layer has specific responsibility  
✅ No layer violations  

### 2. Dependency Inversion
✅ Layers depend on abstractions (interfaces)  
✅ Not on concrete implementations  
✅ Easy to mock for testing  

### 3. Single Responsibility
✅ Each class has one reason to change  
✅ Small, focused classes  
✅ Extract Class applied where needed  

### 4. Open/Closed Principle
✅ Open for extension (new payment types, reports)  
✅ Closed for modification (existing code)  
✅ Achieved via patterns  

### 5. Interface Segregation
✅ Specific interfaces for each role  
✅ No fat interfaces  
✅ Clients don't depend on unused methods  

### 6. Liskov Substitution
✅ Subclasses can replace parent classes  
✅ Proper inheritance hierarchies  
✅ Strategy and State patterns enforce this  

---

## Performance Characteristics

| Aspect | Technique | Benefit |
|--------|-----------|---------|
| Memory | Flyweight pattern | Reduces object count |
| Database | Connection pooling | Reuses connections |
| I/O | Async notifications | Non-blocking operations |
| Caching | Currency/Merchant cache | Fast lookups |
| Query | Repository abstraction | Optimized queries |

---

## Security Layers

```
┌──────────────────────────────────────┐
│   Layer 1: Authentication            │
│   - AuthProxy                        │
│   - SessionManager                   │
│   - 2FA verification                 │
└────────────┬─────────────────────────┘
             │
┌────────────▼─────────────────────────┐
│   Layer 2: Authorization             │
│   - Role-based access control        │
│   - Resource permissions             │
└────────────┬─────────────────────────┘
             │
┌────────────▼─────────────────────────┐
│   Layer 3: Data Protection           │
│   - Password hashing                 │
│   - Encryption                       │
│   - Audit logging                    │
└──────────────────────────────────────┘
```

---

## Scalability Features

1. **Stateless Services:** Easy horizontal scaling
2. **Observer Pattern:** Async event processing
3. **Connection Pool:** Efficient resource usage
4. **Facade Pattern:** Load balancer friendly
5. **Repository Pattern:** Database abstraction for sharding

---

## Monitoring & Observability

Each layer should log:
- **Presentation:** Request/Response times
- **Business:** Business rule violations
- **Data:** Query performance
- **Cross-cutting:** Security events, notifications sent

---

## Summary

This architecture provides:
✅ **13 Design Patterns** properly implemented  
✅ **Clear layer separation** (3 main layers)  
✅ **SOLID principles** throughout  
✅ **Scalability** via stateless design  
✅ **Security** via Proxy and authentication layers  
✅ **Maintainability** via small, focused classes  
✅ **Testability** via dependency injection  
✅ **Extensibility** via Open/Closed principle  

**Result:** A robust, production-ready architecture for SigmaPay!
