# Architecture Documentation Template

## Instructions for AI Assistants

When creating architecture diagrams, You MUST follow these rules:

### Color Coding Requirements
- You MUST apply these colors consistently:
  - `fill:#ffe5cc` - External Actors (orange/peach)
  - `fill:#e6f5ff` - Frontend Components (light blue)
  - `fill:#f0fff0` - Backend Components (light green)
  - `fill:#fff0ff` - Database/Persistence (light purple)
- You MUST include the color style definitions at the end of the diagram
- You MUST use stroke:#333,stroke-width:2px for all colored elements

### Component Rules
- You MUST use stereotypes to identify component types:
  - `<<External Actor>>` for users and external systems
  - `<<React Component>>` for frontend components
  - `<<Frontend Service>>` for frontend services (API communication)
  - `<<Spring Controller>>` or `<<Controller>>` for REST controllers
  - `<<Action Component>>` for Mosy Actions (business logic)
  - `<<Repository>>` or `<<Spring Data Repository>>` for data access
  - `<<Entity>>` for domain entities
  - `<<Enumeration>>` for enums
  - `<<Event>>` for domain events
  - `<<EventHandler>>` for event processors
- You MUST NOT create backend Service layer classes (Actions are the business logic layer)
- You MUST show Controllers delegating to Actions, not Services
- Frontend services (e.g., TaskService) are acceptable for API communication

### Architecture Patterns
- You MUST follow the pattern: Controller → Actor + Action → Repository → Database
- You MUST create per-flow diagrams showing only relevant components
- You MUST create an overall system diagram combining all flows
- You MUST show relationships with arrows and descriptive labels

## Example 1: Task Management System (Overall Architecture)

### Component Architecture Diagram

```mermaid
%%{init: {'theme':'neutral'}}%%
classDiagram
    class User {
        <<External Actor>>
        +interacts with system
    }

    class CreateTaskForm {
        <<React Component>>
        -taskDescription: string
        +render()
        +handleSubmit()
        +validateInput()
    }

    class TaskFormInput {
        <<React Component>>
        -value: string
        -error: string
        +onChange()
        +validate()
    }

    class TaskService {
        <<Frontend Service>>
        +createTask(description: string)
        +handleResponse()
    }

    class TaskController {
        <<Spring Controller>>
        +createTask(TaskRequest): ResponseEntity
    }

    class CreateTaskAction {
        <<Action Component>>
        -taskRepository: TaskRepository
        +execute(TaskData): Task
        +validate(description: string)
    }

    class TaskRepository {
        <<Spring Data Repository>>
        +save(Task): Task
        +findById(Long): Optional~Task~
    }

    class Task {
        <<Entity>>
        -id: Long
        -description: String
        -createdAt: LocalDateTime
        -status: TaskStatus
    }

    class TaskStatus {
        <<Enumeration>>
        TODO
        IN_PROGRESS
        DONE
    }

    class Database {
        <<H2/PostgreSQL>>
        tasks table
    }

    User --> CreateTaskForm : interacts
    CreateTaskForm --> TaskFormInput : contains
    CreateTaskForm --> TaskService : uses
    TaskService --> TaskController : HTTP POST /api/tasks
    TaskController --> CreateTaskAction : delegates
    CreateTaskAction --> TaskRepository : persists
    TaskRepository --> Database : stores
    Task --> TaskStatus : has
    CreateTaskAction --> Task : creates

    %% Color coding for different layers
    style User fill:#ffe5cc,stroke:#333,stroke-width:2px
    style CreateTaskForm fill:#e6f5ff,stroke:#333,stroke-width:2px
    style TaskFormInput fill:#e6f5ff,stroke:#333,stroke-width:2px
    style TaskService fill:#e6f5ff,stroke:#333,stroke-width:2px
    style TaskController fill:#f0fff0,stroke:#333,stroke-width:2px
    style CreateTaskAction fill:#f0fff0,stroke:#333,stroke-width:2px
    style TaskRepository fill:#f0fff0,stroke:#333,stroke-width:2px
    style Task fill:#f0fff0,stroke:#333,stroke-width:2px
    style TaskStatus fill:#f0fff0,stroke:#333,stroke-width:2px
    style Database fill:#fff0ff,stroke:#333,stroke-width:2px
```

## Component Legend

- 🟧 **External Actor** (Orange) - User interacting with the system
- 🟦 **Frontend Components** (Light Blue) - React components and services
- 🟩 **Backend Components** (Light Green) - Spring Boot controllers, actions, repositories, and entities
- 🟪 **Database** (Light Purple) - Persistence layer

## Component Descriptions

### Frontend
- **CreateTaskForm**: Main form component for task creation
- **TaskFormInput**: Reusable input component with validation
- **TaskService**: Service layer for API communication

### Backend
- **TaskController**: REST API endpoint handler
- **CreateTaskAction**: Business logic for task creation (Mosy pattern - replaces traditional service)
- **TaskRepository**: Data access layer
- **Task**: Entity representing a task in the system
- **TaskStatus**: Enum defining valid task states

### Persistence
- **Database**: SQL

## Technology Stack

### Frontend
- React 18+
- TypeScript
- Axios (for HTTP requests)

### Backend
- Spring Boot 3.x
- Spring Web (REST)
- Spring Data JPA
- Java 17+

### Database
- H2 Database (development)
- PostgreSQL (production-ready)

## API Contracts

### REST API Specification

The complete REST API specification is documented in OpenAPI 3.0 format:

📄 **[OpenAPI Specification](./openapi-spec.yaml)**

### Event-Driven API Specification

The event-driven architecture is documented in AsyncAPI 3.0 format:

📄 **[AsyncAPI Specification](./asyncapi-spec.yaml)**

## Example 2: Add to Cart Flow Architecture (Per-Flow Diagram)

### Flow-Specific Architecture

This diagram shows only the components involved in the Add to Cart flow:

```mermaid
%%{init: {'theme':'neutral'}}%%
classDiagram
    class Customer {
        <<External Actor>>
        +browses products
        +adds to cart
    }

    class CartPage {
        <<React Component>>
        -productId: string
        -quantity: number
        +handleAddToCart()
        +displayCart()
    }

    class CartService {
        <<Frontend Service>>
        +addToCart(productId, quantity)
        +getCart()
        +removeItem()
    }

    class CartController {
        <<Spring Controller>>
        +addItem(AddItemRequest): ResponseEntity~Cart~
        +getCart(customerId): ResponseEntity~Cart~
        +removeItem(itemId): ResponseEntity
    }

    class CustomerActor {
        <<Action Component>>
        -customerId: String
        -sessionId: String
        -timestamp: LocalDateTime
    }

    class AddToCartAction {
        <<Action Component>>
        -cartRepository: CartRepository
        -productRepository: ProductRepository
        -eventPublisher: EventPublisher
        +execute(CustomerActor, AddRequest): ActionResult~Cart~
        +validateProduct(productId): Product
        +createCartLine(product, quantity): CartLine
    }

    class CartRepository {
        <<Spring Data Repository>>
        +findByCustomerId(customerId): Optional~Cart~
        +save(Cart): Cart
        +findById(cartId): Optional~Cart~
    }

    class ProductRepository {
        <<Spring Data Repository>>
        +findById(productId): Optional~Product~
        +checkAvailability(productId): boolean
    }

    class Cart {
        <<Entity>>
        -id: Long
        -customerId: Long
        -cartLines: List~CartLine~
        -totalAmount: BigDecimal
        -status: CartStatus
        +addLine(CartLine)
        +calculateTotal()
    }

    class CartLine {
        <<Entity>>
        -id: Long
        -cartId: Long
        -productId: Long
        -quantity: Integer
        -unitPrice: BigDecimal
        -lineTotal: BigDecimal
    }

    class CartUpdatedEvent {
        <<Event>>
        -cartId: Long
        -customerId: Long
        -timestamp: LocalDateTime
        -cartTotal: BigDecimal
    }

    class EventPublisher {
        <<Infrastructure>>
        +publish(DomainEvent)
        +publishAsync(DomainEvent)
    }

    class Database {
        <<PostgreSQL>>
        carts table
        cart_lines table
        products table
    }

    Customer --> CartPage : interacts
    CartPage --> CartService : uses
    CartService --> CartController : HTTP POST /api/cart/add
    CartController --> CustomerActor : creates
    CartController --> AddToCartAction : delegates
    AddToCartAction --> CartRepository : queries/saves
    AddToCartAction --> ProductRepository : validates
    AddToCartAction --> EventPublisher : publishes
    EventPublisher --> CartUpdatedEvent : creates
    CartRepository --> Database : persists
    ProductRepository --> Database : queries
    Cart --> CartLine : contains

    %% Color coding for different layers
    style Customer fill:#ffe5cc,stroke:#333,stroke-width:2px
    style CartPage fill:#e6f5ff,stroke:#333,stroke-width:2px
    style CartService fill:#e6f5ff,stroke:#333,stroke-width:2px
    style CartController fill:#f0fff0,stroke:#333,stroke-width:2px
    style CustomerActor fill:#f0fff0,stroke:#333,stroke-width:2px
    style AddToCartAction fill:#f0fff0,stroke:#333,stroke-width:2px
    style CartRepository fill:#f0fff0,stroke:#333,stroke-width:2px
    style ProductRepository fill:#f0fff0,stroke:#333,stroke-width:2px
    style Cart fill:#f0fff0,stroke:#333,stroke-width:2px
    style CartLine fill:#f0fff0,stroke:#333,stroke-width:2px
    style CartUpdatedEvent fill:#f0fff0,stroke:#333,stroke-width:2px
    style EventPublisher fill:#f0fff0,stroke:#333,stroke-width:2px
    style Database fill:#fff0ff,stroke:#333,stroke-width:2px
```

### Key Architecture Principles

#### Layered pattern
The architecture uses a Controller/Action/Repository layered structure and avoids a traditional service layer:
- **Actions replace Services**: AddToCartAction contains ALL business logic
- **Direct delegation**: Controller → Action (no intermediate service)
- **Clear responsibility**: Actions handle business rules, validations, and orchestration

#### Event-Driven Design
- Actions publish events when needed for downstream processing
- Events enable loose coupling between components
- Async processing for non-critical operations

#### Actor-Action Pattern
- CustomerActor provides execution context
- Actions are stateless and reusable
- Clear separation between who (Actor) and what (Action)

## Architecture Layers Explained

### Frontend Layer (Light Blue)
- **Components**: User interface elements
- **Frontend Services**: API communication and state management
- Acceptable to have "Service" classes here for API abstraction

### Backend Layer (Light Green)
- **Controllers**: HTTP request handling and response formatting
- **Actors**: Execution context (who is performing the action)
- **Actions**: Business logic implementation (replaces service layer)
- **Repositories**: Data access abstraction
- **Entities**: Domain model objects
- **Events**: Domain events for async processing

### Persistence Layer (Light Purple)
- **Database**: Relational or NoSQL storage
- **Tables/Collections**: Data structures
- **Indexes**: Performance optimization

### External Layer (Orange/Peach)
- **Users**: Human actors
- **External Systems**: Third-party integrations
- **External APIs**: Outside services

## Design Patterns Used

### Mosy Actor-Action Pattern
- Replaces traditional service layer
- Clear separation of concerns
- Testable business logic

### Repository Pattern
- Abstracts data access
- Enables testing with mocks
- Supports multiple data sources

### Event-Driven Architecture
- Loose coupling between components
- Async processing capabilities
- Event sourcing potential

### Command Pattern
- Actions as commands with execute method
- Encapsulated business operations
- Reusable and composable


---

© 2025 Mosy Software Architecture SL. All rights reserved.

Licensed to AgentGuild customers for internal use only. Distribution, copying, or derivative works prohibited without written permission. Contact: legal@mosy.tech