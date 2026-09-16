# MVC Application Building Framework

In a traditional Java MVC architecture, data models define the contract for the entire application. Building from the ground up ensures you aren't trying to map data or build business logic for objects that don't exist yet.

The general build sequence follows a bottom-up flow: Data & Contracts $\rightarrow$ Persistence $\rightarrow$ Business Logic $\rightarrow$ Presentation.

## Recommended Director structure.

All classes should be in a relevant package (usually named using its parent directory)

```plaintext
src/
└── main/
    └── java/
        └── com/
            └── example/
                └── app/
                    ├── Main.java                         # Entry point
                    ├── controller/                       # Presentation / API controllers
                    │   └── Controller.java
(Package)           ├── dao/                              # Data Access Layer 
                    │   ├── Dao.java                      # Interface
                    │   └── DaoImpl.java                  # Implementation of Dao
(Package)           │   └── util/                         # File/DB helpers, marshallers
                    │       └── DataStorage.java          # Interface
                    │       └── DataStorageImpl.java      # Implementation (File with delimiter, JSON, SQL, noSQL etc)
                    │       └── DataMarshaller.java       # Interface
                    │       └── DataMarshallerImpl.java   # Implementation (Adding delimiter, normalising etc)
(Package)           ├── dto/                              # Pure data models / domain objects 
                    │   ├── Dto.java
(Package)           ├── exception/                        # Application-specific exceptions  
                    │   ├── DataAccessException.java
                    │   └── ValidationException.java
(Package)           ├── service/                          # Core Business Logic Layer  
                    │   ├── UserService.java              # Interface
                    │   └── UserServiceImpl.java
(Package)           └── ui/                               # View components (CLI views, FX controllers, etc.)
                        └── ProjectView.java              # Overall view
                        └── UserIO.java                   # Interface
                        └── UserIOImpl.java               # Specific implementation (Console, Web interface, Desktop app etc)

```

## Build sequence

![Java Extended MVC Diagram](https://github.com/KianDelamarre/MVC-Application-Building-Guide/blob/b771472f1f070e30d865d0cb24ced41b8b89ea41/mvc%20builder%20order%20flowchart.jpeg)

<br>

## Step 1: Data Transfer Objects (DTOs)

Define the plain old Java objects (POJOs) that represent the domain models and state passed throughout your layers.

- Action: Create your model classes with private fields, getters/setters, constructors, equals(), hashCode(), and toString().
    
- Why First: DTOs are the common language shared by every layer (DAO, Service, Controller, and UI).
    

## Step 2: Data Access Layer (DAO & DAO Utils)

Build the persistence mechanism responsible for reading and writing DTOs to storage.

1. DAO Interfaces: Define contracts for CRUD operations (e.g., UserDao interface with methods like findById, save, delete).
    
2. DAO Utilities (util package):
    

- Build your Marshaller/Unmarshaller or Serializer to handle object-to-file conversions (e.g., JSON, XML, CSV).
    
- Set up your DataStorageImpl (or generic file handler) to manage direct file/database reads and writes.
    

3. DAO Concrete Implementation: Implement the DAO interface (e.g., UserDaoFileImpl), consuming your utilities to persist and retrieve DTOs.
    

## Step 3: Service Layer

Implement core business rules, validation, and transaction orchestrations.

1. Service Interface: Define the application's business capabilities.
    
2. Service Implementation: Inject/instantiate the DAO interfaces here. Validate input data, apply domain logic, and delegate storage calls to the DAO layer.
    

## Step 4: Controller Layer

Bridge the user interaction layer and the core business logic.

- Action: Receive inputs from the UI, pass parameters to the Service layer, receive processed DTOs back, and determine which view or payload to send back. Keep controllers lightweight—avoid business logic here.
    

## Step 5: UI (View) Layer

Handle user interactions and visual rendering (e.g., CLI, JavaFX, or HTML/JSP).

- Action: Build views to collect inputs and display outputs. The UI calls Controller methods to execute user intent.

---

## Layer Dependency Map

| Package Layer | Responsibilities                                     | Depends On      |
| ------------- | ---------------------------------------------------- | --------------- |
| dto           | Data definitions, domain models, payload containers  | None            |
| dao           | Persistence abstraction, file storage, serialization | dto             |
| service       | Business rules, validation, transaction flow         | dao, dto        |
| controller    | Input handling, routing, service execution control   | service, dto    |
| ui            | User display, view formatting, input gathering       | controller, dto |

---


## Overall Architecture
![Java Extended MVC Diagram](https://raw.githubusercontent.com/KianDelamarre/MVC-Application-Building-Guide/d6681fe4a25b28d5fd7c8e7f6968f91c0d2a7777/Java%20extended%20mvc%20diagram.jpeg)

<br>

## Java best practice naming convention

Java follows well-established casing standards to keep code readable, consistent, and intuitive across the ecosystem.

### Identifier Conventions

| Category | Casing Style | Rules & Examples |
|---|---|---|
| Classes & Interfaces | `PascalCase` | Nouns or noun phrases. Capitalize the first letter of every word.<br>• `UserController`, `DataStorageImpl`<br>• Interfaces often use adjectives: `Runnable`, `Serializable` |
| Methods | `camelCase` | Verbs or verb phrases. Lowercase first word, capitalize subsequent words.<br>• `getUserById()`, `calculateTotal()`, `isValid()` |
| Variables & Parameters | `camelCase` | Short, meaningful nouns. Avoid cryptic abbreviations.<br>• `firstName`, `itemCount`, `payload` |
| Constants | `UPPER_SNAKE_CASE` | Applied strictly to static final fields whose values are immutable.<br>• `MAX_RETRY_ATTEMPTS`, `DEFAULT_TIMEOUT_MS` |
| Packages | `lowercase` | Reverse domain name in all lowercase. Avoid underscores or hyphenated names.<br>• `com.example.app.service`<br>• `org.apache.commons.lang3` |
| Type Parameters (Generics) | `SINGLE_UPPERCASE` | Single capital letters representing generic types.<br>• `T` (Type), `E` (Element), `K` (Key), `V` (Value) |

### Key Naming Best Practices

 - Acroronyms: Treat acronyms as standard words rather than keeping all letters uppercase to preserve readability. Use XmlParser instead of XMLParser, or fetchHttpUrl() instead of fetchHTTPURL().
  
 - Boolean Methods: Prefix methods returning boolean values with is, has, can, or should (e.g., isEmpty(), hasPermission()).
   
 - Implementation Classes: Append Impl when naming a concrete class implementing a single interface (e.g., UserService interface $\rightarrow$ UserServiceImpl class).
   
 - Test Classes: Name tests after the class being tested with a Test suffix (e.g., UserServiceTest).
