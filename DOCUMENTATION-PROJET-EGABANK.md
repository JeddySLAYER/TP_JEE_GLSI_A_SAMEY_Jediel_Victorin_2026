# 📚 DOCUMENTATION COMPLÈTE - Projet egaBank

## 🏗️ Architecture Globale du Projet

Le projet **egaBank** est une application bancaire full-stack composée de :
- **Backend** : API REST en Spring Boot (Java)
- **Frontend** : Application Angular 16
- **Base de données** : H2 (en mémoire)

---

## 📂 Structure du Projet

```
A/
├── Gescobank/                    # BACKEND (Spring Boot)
│   ├── src/main/java/com/bankiapp/
│   │   ├── entities/             # Entités JPA (modèles de données)
│   │   ├── dto/                  # Data Transfer Objects
│   │   ├── repositories/         # Repositories Spring Data
│   │   ├── services/             # Logique métier
│   │   ├── web/                  # Controllers REST (API endpoints)
│   │   └── enums/                # Énumérations (Status, Types)
│   └── src/main/resources/
│       └── application.properties # Configuration Spring Boot
│
└── GESCBANK/                     # FRONTEND (Angular)
    ├── src/app/
    │   ├── core/                 # Services & Modèles partagés
    │   │   ├── models/           # Interfaces TypeScript (Client, Compte, Operation)
    │   │   └── services/         # Services HTTP (client.service, compte.service, operation.service)
    │   ├── pages/                # Pages de l'application
    │   │   ├── auth/             # Page d'authentification
    │   │   ├── home/             # Page d'accueil
    │   │   ├── clients/          # Gestion des clients
    │   │   ├── comptes/          # Gestion des comptes
    │   │   └── operations/       # Gestion des opérations
    │   └── shared/               # Composants partagés (header, footer)
    └── src/environments/
        └── environment.ts        # Configuration API URL
```

---

## 🔗 LIAISON FRONTEND ↔ BACKEND

### 1. **Configuration de l'URL de l'API**

**Fichier : `GESCBANK/src/environments/environment.ts`**
```typescript
export const environment = {
   production: false,
   apiUrl: 'http://localhost:8081',  // ← URL du backend Spring Boot
   prefix: 'api/v1'                   // ← Préfixe des routes API
};
```

**Backend écoute sur** : `http://localhost:8081`  
**Frontend construit les URLs** : `${apiUrl}/${prefix}/endpoint`  
**Exemple** : `http://localhost:8081/api/v1/clients`

---

### 2. **Services Angular (Pont Front→Back)**

Les services Angular utilisent `HttpClient` pour communiquer avec le backend :

#### 📄 **ClientService** (`GESCBANK/src/app/core/services/client.service.ts`)

```typescript
@Injectable({ providedIn: 'root' })
export class ClientService {
  constructor(private httpClient: HttpClient) { }

  // GET tous les clients → http://localhost:8081/api/v1/clients
  public getClients(): Observable<Client[]> {
    return this.httpClient.get<Client[]>(
      `${environment.apiUrl}/${environment.prefix}/clients`
    );
  }

  // POST créer un client → http://localhost:8081/api/v1/clients
  public postClients(client: Client): Observable<Client> {
    return this.httpClient.post<Client>(
      `${environment.apiUrl}/${environment.prefix}/clients`, 
      client
    );
  }

  // GET un client par ID → http://localhost:8081/api/v1/clients/{id}
  public getClient(id: number): Observable<Client> {
    return this.httpClient.get<Client>(
      `${environment.apiUrl}/${environment.prefix}/clients/${id}`
    );
  }
}
```

#### 💳 **CompteService** (`GESCBANK/src/app/core/services/compte.service.ts`)

```typescript
@Injectable({ providedIn: 'root' })
export class CompteService {
  constructor(private httpClient: HttpClient) { }

  // GET comptes par type → http://localhost:8081/api/v1/comptes?type=CC
  public getComptes(type: string = "CC"): Observable<Compte[]> {
    return this.httpClient.get<Compte[]>(
      `${environment.apiUrl}/${environment.prefix}/comptes?type=${type}`
    );
  }

  // POST créer compte → http://localhost:8081/api/v1/comptes
  public postComptes(compte: Compte): Observable<Compte> {
    return this.httpClient.post<Compte>(
      `${environment.apiUrl}/${environment.prefix}/comptes`, 
      compte
    );
  }

  // GET activer compte → http://localhost:8081/api/v1/comptes/active/{numCompte}
  public aciveCompte(numberCompte: string): Observable<boolean> {
    return this.httpClient.get<boolean>(
      `${environment.apiUrl}/${environment.prefix}/comptes/active/${numberCompte}`
    );
  }

  // GET suspendre compte → http://localhost:8081/api/v1/comptes/suspendre/{numCompte}
  public suspendCompte(numberCompte: string): Observable<boolean> {
    return this.httpClient.get<boolean>(
      `${environment.apiUrl}/${environment.prefix}/comptes/suspendre/${numberCompte}`
    );
  }
}
```

#### 💸 **OperationService** (`GESCBANK/src/app/core/services/operation.service.ts`)

```typescript
@Injectable({ providedIn: 'root' })
export class OperationService {
  constructor(private httpClient: HttpClient) { }

  // POST versement → http://localhost:8081/api/v1/versements
  public operationVersments(data: any): Observable<Operation> {
    return this.httpClient.post<Operation>(
      `${environment.apiUrl}/${environment.prefix}/versements`, 
      data
    );
  }

  // POST retrait → http://localhost:8081/api/v1/retraits
  public operationRetraits(data: any): Observable<Operation> {
    return this.httpClient.post<Operation>(
      `${environment.apiUrl}/${environment.prefix}/retraits`, 
      data
    );
  }

  // POST virement → http://localhost:8081/api/v1/virements
  public operationVirements(data: any): Observable<Operation> {
    return this.httpClient.post<Operation>(
      `${environment.apiUrl}/${environment.prefix}/virements`, 
      data
    );
  }

  // GET opérations d'un compte → http://localhost:8081/api/v1/operations/client/{numCompte}
  public getOperationsByCompte(numberCompte: string): Observable<Operation[]> {
    return this.httpClient.get<Operation[]>(
      `${environment.apiUrl}/${environment.prefix}/operations/client/${numberCompte}`
    );
  }
}
```

---

### 3. **Modèles TypeScript (Frontend)**

Les interfaces TypeScript définissent la structure des données :

#### 📄 **Client Model** (`GESCBANK/src/app/core/models/client.model.ts`)
```typescript
export interface Client {
    id: number;
    firstName: string;
    lastName: string;
    telephone: string;
    email?: string;
    birthday: Date;
}
```

#### 💳 **Compte Model** (`GESCBANK/src/app/core/models/compte.model.ts`)
```typescript
export interface Compte {
    numCompte: string;
    devis: string;
    balance: number;
    createdAt: Date;
    status: any;          // ACTIVATED ou SUSPENDED
    tauxInteret: number;  // Pour compte épargne
    decouvert: number;    // Pour compte courant
    client: Client;
    clientId: number;
}
```

#### 💸 **Operation Model** (`GESCBANK/src/app/core/models/operation.model.ts`)
```typescript
export interface Operation {
    dateOperation: Date;
    id: number;
    compte: Compte;
    amount: number;
    numOperation: string;
    type: any;  // VERSEMENT, RETRAIT, VIREMENT
}
```

---

### 4. **Entités JPA (Backend)**

#### 📄 **Client Entity** (`Gescobank/src/main/java/com/bankiapp/entities/Client.java`)
```java
@Entity
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Client extends Person {
    @OneToMany(mappedBy = "client")
    private List<AccountBank> comptes = new ArrayList<>();
}
```

#### 💳 **AccountBank Entity** (`Gescobank/src/main/java/com/bankiapp/entities/AccountBank.java`)
```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public abstract class AccountBank {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    long id;
    
    private String numCompte;
    private String devis;
    private double balance;
    private Date createdAt;
    
    @Enumerated(EnumType.STRING)
    private AccountStatus status;  // ACTIVATED, SUSPENDED
    
    @ManyToOne
    @JoinColumn(name = "client_id")
    private Client client;
    
    @OneToMany(mappedBy = "compte")
    private Collection<Operation> operations;
}
```

Deux classes héritent de `AccountBank` :
- **CompteCourant** (découvert)
- **CompteEpargne** (taux d'intérêt)

#### 💸 **Operation Entity** (`Gescobank/src/main/java/com/bankiapp/entities/Operation.java`)
```java
@Entity
@Data
public class Operation {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
    
    private Date dateOperation;
    private String numOperation;
    private double amount;
    
    @Enumerated(EnumType.STRING)
    private TypeOperation type;  // VERSEMENT, RETRAIT, VIREMENT
    
    @ManyToOne
    private AccountBank compte;
}
```

---

### 5. **Controllers REST (Backend API)**

#### 📄 **ClientRestController** (`Gescobank/src/main/java/com/bankiapp/web/ClientRestController.java`)
```java
@RestController
@RequestMapping(value = "/api/v1")
@CrossOrigin("*")  // ← Permet les requêtes depuis Angular (localhost:4200)
public class ClientRestController {
    private final ClientService clientService;
    
    @PostMapping("/clients")
    public Client createClient(@RequestBody ClientDTO dto) {
        return this.clientService.createClient(dto);
    }
    
    @GetMapping("/clients")
    List<Client> findAll() {
        return this.clientService.getAllClient();
    }
    
    @GetMapping("/clients/{id}")
    Client findById(@PathVariable("id") long id) {
        return this.clientService.getClientById(id);
    }
}
```

#### 💳 **CompteBancaireRestController** (`Gescobank/src/main/java/com/bankiapp/web/CompteBancaireRestController.java`)
```java
@RestController
@RequestMapping("/api/v1")
@CrossOrigin("*")
public class CompteBancaireRestController {
    private final CompteBancaireService bancaireService;
    
    @PostMapping("/comptes")
    CompteBancaireDTO createCompte(@RequestBody CompteBancaireDTO dto) {
        this.bancaireService.createCompte(dto);
        return dto;
    }
    
    @GetMapping("/comptes")
    List<?> findAllCompte(@Param("type") String type) {
        if(type.equals("CC"))
            return this.bancaireService.findAllCompteCourant();
        if(type.equals("CE"))
            return this.bancaireService.findAllCompteEpargne();
        return null;
    }
    
    @GetMapping("/comptes/active/{numCompte}")
    boolean activeCompte(@PathVariable("numCompte") String numCompte) {
        this.bancaireService.activeCompte(numCompte);
        return true;
    }
    
    @GetMapping("/comptes/suspendre/{numCompte}")
    boolean suspendreCompte(@PathVariable("numCompte") String numCompte) {
        this.bancaireService.suspendCompte(numCompte);
        return true;
    }
}
```

#### 💸 **OperationRestController** (`Gescobank/src/main/java/com/bankiapp/web/OperationRestController.java`)
```java
@RestController
@RequestMapping("/api/v1")
@CrossOrigin("*")
public class OperationRestController {
    private final OperationService operationService;
    
    @PostMapping("/versements")
    public void versements(@RequestBody OperationDto dto) {
        operationService.effectuerVersement(dto.getNumCompte(), dto.getAmount());
    }
    
    @PostMapping("/retraits")
    public void retraits(@RequestBody OperationDto dto) {
        operationService.effectuerRetrait(dto.getNumCompte(), dto.getAmount());
    }
    
    @PostMapping("/virements")
    public void virements(@RequestBody OperationDto dto) {
        operationService.virementFromOneCompteAToCompteB(
            dto.getNumCompteS(), 
            dto.getNumCompteD(), 
            dto.getAmount()
        );
    }
    
    @GetMapping("/operations/client/{numCompte}")
    List<Operation> findAllOperationByClient(@PathVariable("numCompte") String numCompte) {
        return this.operationService.findByClientNumCompte(numCompte);
    }
}
```

---

## 🔄 Flux de Communication Frontend ↔ Backend

### Exemple : **Récupérer la liste des clients**

```
┌─────────────────────────────────────────────────────────────────┐
│ 1. COMPOSANT ANGULAR (list.component.ts)                      │
│    ┌────────────────────────────────────────────────┐          │
│    │ constructor(private clientService: ClientService) │      │
│    │   this.getClients();                            │          │
│    │ }                                               │          │
│    │                                                 │          │
│    │ getClients() {                                  │          │
│    │   this.clientService.getClients().subscribe({  │          │
│    │     next: (clients: Client[]) => {             │          │
│    │       this.clients.push(...clients);           │          │
│    │     }                                            │          │
│    │   });                                            │          │
│    │ }                                               │          │
│    └────────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ 2. SERVICE ANGULAR (client.service.ts)                         │
│    ┌────────────────────────────────────────────────┐          │
│    │ getClients(): Observable<Client[]> {           │          │
│    │   return this.httpClient.get<Client[]>(        │          │
│    │     'http://localhost:8081/api/v1/clients'     │          │
│    │   );                                            │          │
│    │ }                                               │          │
│    └────────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                             ↓
                    HTTP GET REQUEST
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ 3. BACKEND SPRING BOOT (ClientRestController.java)             │
│    ┌────────────────────────────────────────────────┐          │
│    │ @GetMapping("/clients")                        │          │
│    │ List<Client> findAll() {                       │          │
│    │   return this.clientService.getAllClient();    │          │
│    │ }                                               │          │
│    └────────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ 4. SERVICE SPRING (ClientService.java)                         │
│    ┌────────────────────────────────────────────────┐          │
│    │ public List<Client> getAllClient() {           │          │
│    │   return clientRepository.findAll();           │          │
│    │ }                                               │          │
│    └────────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ 5. BASE DE DONNÉES H2 (en mémoire)                             │
│    SELECT * FROM client;                                        │
└─────────────────────────────────────────────────────────────────┘
                             ↓
                    JSON RESPONSE
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ 6. FRONTEND reçoit les données et les affiche                  │
│    [                                                            │
│      {                                                          │
│        "id": 1,                                                 │
│        "firstName": "John",                                     │
│        "lastName": "Doe",                                       │
│        "email": "john@example.com",                             │
│        "telephone": "+33612345678",                             │
│        "birthday": "1990-05-15"                                 │
│      }                                                          │
│    ]                                                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🗂️ Configuration Base de Données

**Fichier : `Gescobank/src/main/resources/application.properties`**

```properties
# Port du serveur Spring Boot
server.port=8081

# Configuration H2 (base de données en mémoire)
spring.datasource.url=jdbc:h2:mem:gescobank
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=create        # Recrée la DB à chaque démarrage
spring.jpa.show-sql=true                    # Affiche les requêtes SQL dans les logs
spring.h2.console.enabled=true              # Active la console H2
```

**Console H2 accessible à** : `http://localhost:8081/h2-console`

---

## 🚀 Lancement du Projet

### **Backend (Spring Boot)**
```bash
cd Gescobank
mvn spring-boot:run
# ou
java -jar target/gescobank-0.0.1-SNAPSHOT.jar
```
→ API disponible sur `http://localhost:8081`

### **Frontend (Angular)**
```bash
cd GESCBANK
npm install
ng serve --port 4201
```
→ Application disponible sur `http://localhost:4201`

---

## 📊 Schéma des Données

```
┌─────────────┐         ┌──────────────────┐         ┌────────────┐
│   CLIENT    │1      N │   ACCOUNT_BANK   │1      N │ OPERATION  │
├─────────────┤◄────────┤──────────────────┤◄────────├────────────┤
│ id (PK)     │         │ id (PK)          │         │ id (PK)    │
│ firstName   │         │ numCompte        │         │ dateOp     │
│ lastName    │         │ balance          │         │ amount     │
│ email       │         │ status           │         │ type       │
│ telephone   │         │ client_id (FK)   │         │ compte_id  │
│ birthday    │         │ ┌──────────────┐ │         │            │
└─────────────┘         │ │DiscriminatorV│ │         └────────────┘
                        │ └──────────────┘ │
                        │        │         │
                        │    ┌───┴───┐     │
                        │    │       │     │
                   ┌────▼────┴─┐ ┌──▼─────▼───┐
                   │COMPTE     │ │COMPTE      │
                   │COURANT    │ │EPARGNE     │
                   ├───────────┤ ├────────────┤
                   │decouvert  │ │tauxInteret │
                   └───────────┘ └────────────┘
```

---

## 🎯 Points Clés de la Liaison Front-Back

### ✅ **CORS activé** sur tous les controllers backend
```java
@CrossOrigin("*")  // Permet les requêtes depuis n'importe quelle origine
```

### ✅ **HttpClientModule** importé dans `app.module.ts`
```typescript
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [HttpClientModule, ...]
})
```

### ✅ **Services Angular** injectés avec `providedIn: 'root'`
```typescript
@Injectable({ providedIn: 'root' })
export class ClientService { ... }
```

### ✅ **Environment configuration** utilisée partout
```typescript
`${environment.apiUrl}/${environment.prefix}/clients`
// → http://localhost:8081/api/v1/clients
```

### ✅ **Typage fort** avec TypeScript Interfaces = Java Entities

| Frontend (TypeScript)          | Backend (Java)            |
|--------------------------------|---------------------------|
| `Client` interface             | `Client` entity           |
| `Compte` interface             | `AccountBank` entity      |
| `Operation` interface          | `Operation` entity        |

---

## 📝 Endpoints API Disponibles

| Méthode | Endpoint                              | Description                    |
|---------|---------------------------------------|--------------------------------|
| GET     | `/api/v1/clients`                     | Liste tous les clients         |
| POST    | `/api/v1/clients`                     | Créer un client                |
| GET     | `/api/v1/clients/{id}`                | Récupérer un client            |
| GET     | `/api/v1/comptes?type=CC`             | Liste comptes courants         |
| GET     | `/api/v1/comptes?type=CE`             | Liste comptes épargne          |
| POST    | `/api/v1/comptes`                     | Créer un compte                |
| GET     | `/api/v1/comptes/active/{numCompte}`  | Activer un compte              |
| GET     | `/api/v1/comptes/suspendre/{numCompte}`| Suspendre un compte           |
| POST    | `/api/v1/versements`                  | Effectuer un versement         |
| POST    | `/api/v1/retraits`                    | Effectuer un retrait           |
| POST    | `/api/v1/virements`                   | Effectuer un virement          |
| GET     | `/api/v1/operations/client/{numCompte}`| Historique opérations         |

---

## 🎨 Design System Appliqué

- **Thème** : Noir/Blanc/Gris minimaliste
- **Composants** : Cards, Tables, Forms avec Tailwind CSS
- **Pages refondues** : Auth, Home, Clients, Comptes, Opérations
- **Responsive** : Mobile-first design
- **Animations** : Hover effects, transitions 150-300ms

---

## 📦 Technologies Utilisées

### Backend
- **Spring Boot 3.2.0**
- **Spring Data JPA**
- **H2 Database**
- **Lombok**
- **Maven**

### Frontend
- **Angular 16.2.12**
- **TypeScript 5.1.6**
- **RxJS 7.8.0**
- **Tailwind CSS 3.4.3**

---

## 🧪 Tests avec Postman

Une collection Postman complète a été générée :  
**Fichier** : `egaBank-API-Collection.postman_collection.json`

Import dans Postman pour tester tous les endpoints !

---

## 📌 Résumé

1. **Backend** expose des endpoints REST sur `http://localhost:8081/api/v1`
2. **Frontend** appelle ces endpoints via des **services Angular** avec `HttpClient`
3. **Communication** : HTTP JSON (GET, POST)
4. **Configuration** centralisée dans `environment.ts`
5. **CORS** activé pour permettre les requêtes cross-origin
6. **Typage fort** avec interfaces TypeScript ↔ entities Java

**Le pont entre les deux** se trouve dans :
- `/GESCBANK/src/app/core/services/` (côté Angular)
- `/Gescobank/src/main/java/com/bankiapp/web/` (côté Spring Boot)
