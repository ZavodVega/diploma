# Архитектурная схема проекта Employee Time Tracking

## Общая архитектурная диаграмма

```mermaid
graph TB
    subgraph "Клиентский уровень"
        Browser[🌐 Веб-браузер]
        Mobile[📱 Мобильные устройства]
    end

    subgraph "Прокси-слой"
        Nginx[🔄 Nginx Reverse Proxy<br/>- Балансировка нагрузки<br/>- SSL терминация<br/>- Статические файлы]
    end

    subgraph "Фронтенд (Presentation Layer)"
        Vue[⚡ Vue.js 3 SPA<br/>- Composition API<br/>- Vue Router<br/>- Vuex Store<br/>- Axios HTTP Client]
        
        subgraph "Vue Components"
            Auth[🔐 Аутентификация]
            Dashboard[📊 Панель управления]
            Activity[⏱️ Отслеживание активности]
            Reports[📈 Отчеты и аналитика]
            Admin[👥 Управление пользователями]
        end
    end

    subgraph "Бэкенд (Application Layer)"
        FastAPI[🚀 FastAPI Server<br/>- REST API<br/>- JWT Authentication<br/>- Pydantic Validation<br/>- OpenAPI/Swagger]
        
        subgraph "API Endpoints"
            AuthAPI[🔑 /auth/*<br/>- Логин/логаут<br/>- JWT токены]
            ActivityAPI[📋 /activity/*<br/>- Текущая активность<br/>- Логирование]
            ReportsAPI[📊 /reports/*<br/>- Генерация отчетов<br/>- Excel экспорт]
            UsersAPI[👤 /users/*<br/>- CRUD пользователей<br/>- Роли и права]
        end
    end

    subgraph "Слой данных (Data Layer)"
        PostgreSQL[(🐘 PostgreSQL<br/>- Основная БД<br/>- ACID транзакции<br/>- Индексы и оптимизация)]
        
        subgraph "Database Tables"
            Users[👥 users<br/>- Аутентификация<br/>- Роли и права]
            Employees[👤 employees<br/>- Профили сотрудников]
            ActivityLogs[📝 activity_logs<br/>- Журнал активности]
        end
    end

    subgraph "Инфраструктурный слой"
        Docker[🐳 Docker Containers<br/>- Изоляция сервисов<br/>- Легкое развертывание]
        DockerCompose[🔧 Docker Compose<br/>- Оркестрация<br/>- Сетевое взаимодействие]
    end

    subgraph "Системный слой"
        Linux[🐧 Linux OS<br/>- Astra Linux<br/>- Безопасность<br/>- Мониторинг процессов]
        SystemAPIs[⚙️ System APIs<br/>- xdotool<br/>- psutil<br/>- Отслеживание окон]
    end

    %% Связи между компонентами
    Browser --> Nginx
    Mobile --> Nginx
    Nginx --> Vue
    Vue --> FastAPI
    FastAPI --> PostgreSQL
    
    %% Внутренние связи Vue
    Vue --> Auth
    Vue --> Dashboard
    Vue --> Activity
    Vue --> Reports
    Vue --> Admin
    
    %% Внутренние связи FastAPI
    FastAPI --> AuthAPI
    FastAPI --> ActivityAPI
    FastAPI --> ReportsAPI
    FastAPI --> UsersAPI
    
    %% Связи с БД
    PostgreSQL --> Users
    PostgreSQL --> Employees
    PostgreSQL --> ActivityLogs
    
    %% Системные связи
    FastAPI --> SystemAPIs
    SystemAPIs --> Linux
    
    %% Docker
    Vue -.-> Docker
    FastAPI -.-> Docker
    PostgreSQL -.-> Docker
    Docker --> DockerCompose

    %% Стили
    classDef frontend fill:#42b883,stroke:#369870,stroke-width:2px,color:#fff
    classDef backend fill:#ff6b6b,stroke:#ee5a52,stroke-width:2px,color:#fff
    classDef database fill:#336791,stroke:#2d5aa0,stroke-width:2px,color:#fff
    classDef infrastructure fill:#0db7ed,stroke:#0aa0d9,stroke-width:2px,color:#fff
    classDef system fill:#f39c12,stroke:#e67e22,stroke-width:2px,color:#fff

    class Vue,Auth,Dashboard,Activity,Reports,Admin frontend
    class FastAPI,AuthAPI,ActivityAPI,ReportsAPI,UsersAPI backend
    class PostgreSQL,Users,Employees,ActivityLogs database
    class Docker,DockerCompose,Nginx infrastructure
    class Linux,SystemAPIs system
```

## Детальная архитектура по слоям

### 1. Клиентский слой (Client Layer)

```mermaid
graph LR
    subgraph "Браузер пользователя"
        HTML[📄 HTML5]
        CSS[🎨 CSS3 + Tailwind]
        JS[⚡ JavaScript ES6+]
        PWA[📱 PWA Support]
    end
    
    subgraph "Vue.js Ecosystem"
        VueCore[🔧 Vue 3 Core]
        Router[🛣️ Vue Router 4]
        Store[🗄️ Vuex 4]
        Composition[🔄 Composition API]
    end
    
    HTML --> VueCore
    CSS --> VueCore
    JS --> VueCore
    VueCore --> Router
    VueCore --> Store
    VueCore --> Composition
```

**Технологии и особенности:**
- **Vue.js 3** с Composition API для реактивности
- **Vue Router** для SPA навигации
- **Vuex** для управления состоянием
- **Axios** для HTTP запросов
- **LocalStorage** для кэширования данных
- **Responsive Design** для мобильных устройств

### 2. Прокси-слой (Proxy Layer)

```mermaid
graph TB
    Internet[🌐 Интернет] --> LB[⚖️ Load Balancer]
    LB --> Nginx1[🔄 Nginx Instance 1]
    LB --> Nginx2[🔄 Nginx Instance 2]
    
    subgraph "Nginx Configuration"
        Static[📁 Статические файлы<br/>- HTML, CSS, JS<br/>- Изображения<br/>- Шрифты]
        Proxy[🔀 Reverse Proxy<br/>- API маршрутизация<br/>- Кэширование<br/>- Сжатие]
        SSL[🔒 SSL/TLS<br/>- Сертификаты<br/>- HTTPS редирект]
    end
    
    Nginx1 --> Static
    Nginx1 --> Proxy
    Nginx1 --> SSL
```

**Функции Nginx:**
- Обслуживание статических файлов
- Reverse proxy для API запросов
- SSL терминация
- Сжатие gzip/brotli
- Кэширование статического контента
- Балансировка нагрузки

### 3. Фронтенд слой (Frontend Layer)

```mermaid
graph TB
    subgraph "Vue.js Application Architecture"
        subgraph "Routing Layer"
            Router[🛣️ Vue Router<br/>- Защищенные маршруты<br/>- Роли и права<br/>- Навигационные охранники]
        end
        
        subgraph "State Management"
            Vuex[🗄️ Vuex Store<br/>- Централизованное состояние<br/>- Модули по доменам<br/>- Персистентность]
        end
        
        subgraph "Component Architecture"
            Layout[🏗️ Layout Components<br/>- AppLayout<br/>- Sidebar<br/>- Header]
            
            Pages[📄 Page Components<br/>- Dashboard<br/>- Activity<br/>- Reports<br/>- Profile]
            
            UI[🎨 UI Components<br/>- Forms<br/>- Tables<br/>- Charts<br/>- Modals]
        end
        
        subgraph "Services Layer"
            API[🔌 API Service<br/>- HTTP клиент<br/>- Интерцепторы<br/>- Обработка ошибок]
            Auth[🔐 Auth Service<br/>- JWT токены<br/>- Роли пользователей<br/>- Автологин]
            Utils[🛠️ Utilities<br/>- Форматирование<br/>- Валидация<br/>- Хелперы]
        end
    end
    
    Router --> Pages
    Vuex --> Pages
    Pages --> UI
    Layout --> Pages
    API --> Vuex
    Auth --> Vuex
```

**Ключевые компоненты:**

#### Компоненты представления:
- **AppLayout** - основной макет с сайдбаром
- **ActivityTracker** - отслеживание активности в реальном времени
- **ActivityStats** - статистика и аналитика
- **ReportGenerator** - генерация отчетов
- **UserProfile** - профиль пользователя
- **EmployeeList** - управление сотрудниками

#### Управление состоянием:
- **auth module** - аутентификация и авторизация
- **activity module** - данные активности
- **reports module** - отчеты и статистика

### 4. Бэкенд слой (Backend Layer)

```mermaid
graph TB
    subgraph "FastAPI Application"
        subgraph "API Layer"
            Endpoints[🔗 REST Endpoints<br/>- OpenAPI/Swagger<br/>- Автодокументация<br/>- Валидация запросов]
        end
        
        subgraph "Authentication & Authorization"
            JWT[🔑 JWT Authentication<br/>- Токены доступа<br/>- Refresh токены<br/>- Роли и права]
            OAuth[🔐 OAuth2 Flow<br/>- Password flow<br/>- Scopes<br/>- Middleware]
        end
        
        subgraph "Business Logic"
            Services[⚙️ Service Layer<br/>- Бизнес-логика<br/>- Валидация данных<br/>- Обработка ошибок]
            
            ActivityService[📊 Activity Service<br/>- Отслеживание окон<br/>- Логирование активности<br/>- Системные вызовы]
            
            ReportService[📈 Report Service<br/>- Генерация отчетов<br/>- Excel экспорт<br/>- Агрегация данных]
            
            UserService[👤 User Service<br/>- CRUD операции<br/>- Управление ролями<br/>- Хеширование паролей]
        end
        
        subgraph "Data Access Layer"
            ORM[🗃️ SQLAlchemy ORM<br/>- Модели данных<br/>- Миграции<br/>- Связи между таблицами]
            
            Models[📋 Data Models<br/>- User<br/>- Employee<br/>- ActivityLog]
        end
        
        subgraph "External Integrations"
            SystemAPI[⚙️ System APIs<br/>- xdotool (Linux)<br/>- psutil<br/>- Мониторинг процессов]
            
            FileExport[📁 File Export<br/>- Excel генерация<br/>- PDF отчеты<br/>- CSV экспорт]
        end
    end
    
    Endpoints --> JWT
    Endpoints --> Services
    Services --> ActivityService
    Services --> ReportService
    Services --> UserService
    Services --> ORM
    ORM --> Models
    ActivityService --> SystemAPI
    ReportService --> FileExport
```

**API Endpoints структура:**

```
/api/v1/
├── auth/
│   ├── POST /login          # Аутентификация
│   ├── POST /logout         # Выход
│   └── POST /refresh        # Обновление токена
├── users/
│   ├── GET /users           # Список пользователей
│   ├── POST /users          # Создание пользователя
│   ├── GET /users/{id}      # Профиль пользователя
│   └── PUT /users/{id}      # Обновление профиля
├── activity/
│   ├── GET /current         # Текущая активность
│   ├── POST /start          # Начало отслеживания
│   ├── POST /stop           # Остановка отслеживания
│   └── GET /history         # История активности
├── reports/
│   ├── GET /generate        # Генерация отчета
│   ├── GET /export/excel    # Excel экспорт
│   └── GET /stats           # Статистика
└── employees/
    ├── GET /employees       # Список сотрудников
    └── GET /employees/{id}  # Детали сотрудника
```

### 5. Слой данных (Data Layer)

```mermaid
graph TB
    subgraph "PostgreSQL Database"
        subgraph "Core Tables"
            Users[👥 users<br/>- id, email, name<br/>- role, department<br/>- password_hash<br/>- created_at, updated_at]
            
            Employees[👤 employees<br/>- id, user_id<br/>- name, department<br/>- created_at]
            
            ActivityLogs[📝 activity_logs<br/>- id, employee_id<br/>- window_title, app_name<br/>- start_time, end_time<br/>- created_at]
        end
        
        subgraph "Indexes & Optimization"
            PrimaryKeys[🔑 Primary Keys<br/>- Уникальные идентификаторы<br/>- Автоинкремент]
            
            ForeignKeys[🔗 Foreign Keys<br/>- Связи между таблицами<br/>- Каскадное удаление]
            
            Indexes[📊 Indexes<br/>- Email уникальность<br/>- Временные запросы<br/>- Составные индексы]
        end
        
        subgraph "Data Integrity"
            Constraints[⚖️ Constraints<br/>- NOT NULL<br/>- UNIQUE<br/>- CHECK constraints]
            
            Triggers[⚡ Triggers<br/>- updated_at автообновление<br/>- Аудит изменений]
        end
    end
    
    Users --> Employees
    Employees --> ActivityLogs
    Users --> PrimaryKeys
    Employees --> ForeignKeys
    ActivityLogs --> Indexes
```

**Особенности базы данных:**
- **ACID транзакции** для целостности данных
- **Индексы** для оптимизации запросов
- **Партиционирование** activity_logs по времени
- **Backup стратегия** с регулярными снимками
- **Репликация** для высокой доступности

### 6. Инфраструктурный слой (Infrastructure Layer)

```mermaid
graph TB
    subgraph "Docker Containerization"
        subgraph "Frontend Container"
            VueContainer[📦 Vue.js Container<br/>- Node.js 18<br/>- Nginx для продакшена<br/>- Multi-stage build]
        end
        
        subgraph "Backend Container"
            FastAPIContainer[📦 FastAPI Container<br/>- Python 3.12<br/>- Uvicorn ASGI<br/>- Dependencies]
        end
        
        subgraph "Database Container"
            PostgreSQLContainer[📦 PostgreSQL Container<br/>- PostgreSQL 14<br/>- Persistent volumes<br/>- Backup scripts]
        end
        
        subgraph "Orchestration"
            DockerCompose[🔧 Docker Compose<br/>- Service definition<br/>- Network configuration<br/>- Volume management<br/>- Environment variables]
        end
    end
    
    subgraph "Deployment & CI/CD"
        Git[📚 Git Repository<br/>- Version control<br/>- Branching strategy<br/>- Code review]
        
        CI[🔄 CI/CD Pipeline<br/>- Automated testing<br/>- Build automation<br/>- Deployment scripts]
        
        Registry[📦 Container Registry<br/>- Docker images<br/>- Version tagging<br/>- Security scanning]
    end
    
    DockerCompose --> VueContainer
    DockerCompose --> FastAPIContainer
    DockerCompose --> PostgreSQLContainer
    Git --> CI
    CI --> Registry
    Registry --> DockerCompose
```

**Docker Compose конфигурация:**
```yaml
services:
  frontend:    # Vue.js приложение
  backend:     # FastAPI сервер
  database:    # PostgreSQL
  nginx:       # Reverse proxy
```

### 7. Системный слой (System Layer)

```mermaid
graph TB
    subgraph "Operating System Layer"
        subgraph "Astra Linux"
            Kernel[🐧 Linux Kernel<br/>- Process management<br/>- Memory management<br/>- Security modules]
            
            Security[🔒 Security Features<br/>- SELinux/AppArmor<br/>- Firewall rules<br/>- User permissions]
        end
        
        subgraph "System APIs"
            XTools[🖥️ X11 Tools<br/>- xdotool<br/>- Window management<br/>- Active window detection]
            
            ProcessMonitor[📊 Process Monitor<br/>- psutil<br/>- CPU/Memory usage<br/>- Process tree]
            
            FileSystem[📁 File System<br/>- Log files<br/>- Configuration<br/>- Temporary files]
        end
        
        subgraph "Runtime Environment"
            Python[🐍 Python Runtime<br/>- Virtual environment<br/>- Package management<br/>- System libraries]
            
            NodeJS[⚡ Node.js Runtime<br/>- NPM packages<br/>- Build tools<br/>- Development server]
        end
    end
    
    Kernel --> Security
    Security --> XTools
    XTools --> ProcessMonitor
    ProcessMonitor --> FileSystem
    Python --> Kernel
    NodeJS --> Kernel
```

## Потоки данных и взаимодействие

### 1. Поток аутентификации

```mermaid
sequenceDiagram
    participant User as 👤 Пользователь
    participant Vue as ⚡ Vue.js
    participant API as 🚀 FastAPI
    participant DB as 🐘 PostgreSQL

    User->>Vue: Ввод логина/пароля
    Vue->>API: POST /auth/login
    API->>DB: Проверка credentials
    DB-->>API: User data
    API->>API: Генерация JWT
    API-->>Vue: JWT token + user info
    Vue->>Vue: Сохранение в localStorage
    Vue-->>User: Перенаправление в dashboard
```

### 2. Поток отслеживания активности

```mermaid
sequenceDiagram
    participant System as 🖥️ Система
    participant API as 🚀 FastAPI
    participant DB as 🐘 PostgreSQL
    participant Vue as ⚡ Vue.js

    loop Каждые 5 секунд
        API->>System: xdotool getactivewindow
        System-->>API: Window ID + PID
        API->>System: psutil.Process(PID)
        System-->>API: Process info
        API->>DB: INSERT activity_log
        API-->>Vue: Current activity data
        Vue->>Vue: Обновление UI
    end
```

### 3. Поток генерации отчетов

```mermaid
sequenceDiagram
    participant User as 👤 Пользователь
    participant Vue as ⚡ Vue.js
    participant API as 🚀 FastAPI
    participant DB as 🐘 PostgreSQL
    participant Excel as 📊 Excel Export

    User->>Vue: Запрос отчета
    Vue->>API: GET /reports/generate
    API->>DB: Агрегация данных
    DB-->>API: Report data
    API->>Excel: Генерация Excel
    Excel-->>API: Excel file
    API-->>Vue: Report data + download link
    Vue-->>User: Отображение + скачивание
```

## Безопасность и производительность

### Безопасность
- **JWT токены** с коротким временем жизни
- **Хеширование паролей** через bcrypt
- **Роли и права доступа** (RBAC)
- **CORS настройки** для API
- **SQL injection защита** через ORM
- **XSS защита** через CSP заголовки

### Производительность
- **Индексы БД** для быстрых запросов
- **Кэширование** статических файлов в Nginx
- **Lazy loading** компонентов Vue
- **Пагинация** для больших списков
- **Сжатие** HTTP ответов
- **Connection pooling** для БД

### Масштабируемость
- **Микросервисная архитектура** готова к разделению
- **Горизонтальное масштабирование** через Docker
- **Балансировка нагрузки** через Nginx
- **Репликация БД** для чтения
- **CDN** для статических файлов

Эта архитектура обеспечивает надежную, безопасную и масштабируемую систему учета рабочего времени с современным технологическим стеком.
