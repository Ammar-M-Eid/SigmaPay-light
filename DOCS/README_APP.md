# SigmaPay - Full-Stack Financial Management Application

A complete, full-stack web application for personal finance management built with Node.js, Express, React, and TypeScript. The application strictly follows the architecture defined in `SigmaPay_Updated_ClassDiagram.puml` with layered architecture and design patterns implementation.

## 🏗️ Architecture

The application follows a **3-tier layered architecture**:

### Backend (Node.js/Express)
- **Controller Layer (API)**: REST endpoints mapping to UML controller interfaces
- **Service Layer (Business Logic)**: Business rules and orchestration
- **Repository Layer (Data Access)**: In-memory mock data storage

### Frontend (React/TypeScript)
- **Pages**: Main application views (Auth, Budget, Goals, Reports)
- **Components**: Reusable UI components
- **API Client**: Service for backend communication

## 📋 Technology Stack

### Backend
- **Runtime**: Node.js
- **Framework**: Express.js
- **Language**: TypeScript
- **Data Storage**: In-memory Maps (mock repositories)

### Frontend
- **Library**: React 18
- **Language**: TypeScript
- **Styling**: Inline styles (CSS-in-JS)
- **HTTP Client**: Fetch API

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ and npm installed
- Two terminal windows (one for backend, one for frontend)

### Installation & Running

#### 1. Backend Server (Port 3001)

Open a terminal and run:

```bash
# Navigate to backend directory
cd backend-server

# Install dependencies
npm install

# Build TypeScript
npm run build

# Start the server
npm start
```

For development with auto-reload:
```bash
npm run dev
```

The backend will start on **http://localhost:3001**

#### 2. Frontend Client (Port 3000)

Open a **new terminal** and run:

```bash
# Navigate to frontend directory
cd frontend-client

# Install dependencies
npm install

# Start the development server
npm start
```

The frontend will automatically open in your browser at **http://localhost:3000**

## 📡 API Endpoints

### Authentication
- `POST /api/accounts/register` - Create new account
- `GET /api/accounts/:userId` - Get account details

### Budget Management
- `POST /api/budgets/create` - Create budget plan
- `POST /api/budgets/expense` - Record expense

### Financial Goals
- `POST /api/goals/create` - Set financial goal
- `GET /api/goals/:goalId/progress` - Track goal progress

### Reports
- `GET /api/reports/monthly/:userId` - Generate monthly summary
- `GET /api/reports/income/:userId` - Generate income statement

### Payments
- `POST /api/payments/process` - Process payment
- `POST /api/payments/refund` - Request refund

### Notifications
- `POST /api/notifications/alert` - Send alert
- `GET /api/notifications/:userId` - Get notification history

## 🎯 Features Implemented

### Core Functionality
✅ User Account Registration (ctlCreateAccount)  
✅ Budget Creation (ctlCreateBudget)  
✅ Expense Recording (ctlRecordExpense)  
✅ Financial Goal Setting (ctlSetGoal)  
✅ Progress Tracking (ctlTrackProgress)  
✅ Monthly Summary Reports (ctlGenerateMonthlySummary)  
✅ Income Statement Reports (ctlGenerateIncomeStatement)  

### Design Patterns (from UML)
✅ **Singleton**: DatabaseConnectionPool  
✅ **Facade**: SigmaPayFacade concept (Controller acts as facade)  
✅ **Repository Pattern**: All data access through interfaces  
✅ **Dependency Injection**: Services receive repositories, Controller receives services  

## 📁 Project Structure

```
/SigmaPay
├── backend-server/
│   ├── src/
│   │   ├── api/              # Controller & REST routes
│   │   │   └── controller.ts
│   │   ├── services/         # Business logic layer
│   │   │   └── services.ts
│   │   ├── data/             # Repository implementations
│   │   │   └── repositories.ts
│   │   ├── domain/           # Interfaces & entities
│   │   │   ├── entities.ts
│   │   │   ├── serviceInterfaces.ts
│   │   │   └── repositoryInterfaces.ts
│   │   └── server.ts         # Express app entry
│   ├── package.json
│   └── tsconfig.json
│
├── frontend-client/
│   ├── src/
│   │   ├── components/       # Reusable UI components
│   │   │   └── Header.tsx
│   │   ├── pages/            # Main views
│   │   │   ├── AuthPage.tsx
│   │   │   ├── BudgetPage.tsx
│   │   │   ├── GoalsPage.tsx
│   │   │   └── ReportsPage.tsx
│   │   ├── api/              # Backend communication
│   │   │   └── apiClient.ts
│   │   ├── App.tsx           # Main component
│   │   ├── index.tsx         # React entry
│   │   └── index.css         # Global styles
│   ├── public/
│   │   └── index.html
│   ├── package.json
│   └── tsconfig.json
│
├── SigmaPay_Updated_ClassDiagram.puml  # Source UML diagram
└── README.md                            # This file
```

## 🔄 Application Flow

1. **User Registration** (AuthPage)
   - User enters username, email, password, phone number
   - Controller creates UserAccount and Account entities
   - Returns success and mock userId

2. **Budget Creation** (BudgetPage)
   - User sets total amount, date range, and categories
   - Controller delegates to BudgetService
   - BudgetService stores budget via BudgetRepository

3. **Expense Recording** (BudgetPage)
   - User enters expense amount and category
   - BudgetService updates budget and creates transaction
   - Transaction stored via TransactionRepository

4. **Goal Setting** (GoalsPage)
   - User defines goal type, target amount, deadline, priority
   - GoalService creates and stores goal
   - Returns goal ID for tracking

5. **Progress Tracking** (GoalsPage)
   - User enters goal ID
   - GoalService calculates progress metrics
   - Returns percentage complete, remaining amount, days left

6. **Report Generation** (ReportsPage)
   - User clicks generate buttons
   - ReportingService aggregates transaction data
   - Returns formatted text reports

## 🔐 Design Principles

### Layered Architecture
- **Clear separation** between Controller, Service, and Repository layers
- **Dependencies flow downward**: Controller → Service → Repository
- **Dependency Inversion**: All layers depend on interfaces, not concrete implementations

### SOLID Principles
- **Single Responsibility**: Each class has one reason to change
- **Open/Closed**: Extensible through interfaces without modifying existing code
- **Liskov Substitution**: Interfaces can be swapped with implementations
- **Interface Segregation**: Focused interfaces (IAccountService, IBudgetService, etc.)
- **Dependency Inversion**: High-level modules depend on abstractions

## 🎨 Frontend Pages

### AuthPage
- User registration form
- Mock login (auto-generates userId)
- Navigation to main application

### BudgetPage
- Create budget with categories
- Record expenses
- Track spending

### GoalsPage
- Set financial goals
- Track progress
- View goal metrics

### ReportsPage
- Generate monthly summary
- Generate income statement
- View formatted reports

## 🛠️ Development

### Backend Development
```bash
cd backend-server
npm run dev  # Start with ts-node for auto-reload
```

### Frontend Development
```bash
cd frontend-client
npm start    # Start with React hot-reload
```

### Building for Production

**Backend:**
```bash
cd backend-server
npm run build
npm start
```

**Frontend:**
```bash
cd frontend-client
npm run build
# Serve the build folder with any static server
```

## 📊 UML Compliance

This implementation strictly follows the class diagram in `SigmaPay_Updated_ClassDiagram.puml`:

- ✅ All controller interfaces implemented (IControllerAuth, IControllerBudget, etc.)
- ✅ All service interfaces implemented (IAccountService, IBudgetService, etc.)
- ✅ All repository interfaces implemented (IUserRepository, ITransactionRepository, etc.)
- ✅ Entity interfaces and classes match UML definitions
- ✅ Layered architecture preserved
- ✅ Dependency injection used throughout

## 🔍 Testing the Application

1. **Start both servers** (backend on 3001, frontend on 3000)
2. **Register an account** on the Auth page
3. **Navigate to Budgets** and create a budget
4. **Record some expenses** in different categories
5. **Go to Goals** and set a financial goal
6. **Track your goal progress**
7. **View Reports** to see monthly summary and income statement

## 📝 Notes

- This is a **demonstration/prototype** application with in-memory storage
- Data is **not persisted** and will be lost when the server restarts
- For production use, replace in-memory repositories with actual database implementations
- Authentication is simplified for demonstration purposes
- All design patterns from the UML are implemented where applicable

## 🤝 Contributing

This project follows the architecture defined in the UML class diagram. Any modifications should:
1. Maintain the layered architecture
2. Follow SOLID principles
3. Update the UML diagram if structural changes are made
4. Preserve interface contracts

## 📄 License

MIT License - See LICENSE file for details

## 👥 Authors

Built following the SigmaPay UML Class Diagram specification

---

**Happy Finance Managing! 💰**
