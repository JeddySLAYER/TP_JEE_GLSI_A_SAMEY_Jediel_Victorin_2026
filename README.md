README - egaBank - Plateforme de Gestion Bancaire

TABLE DES MATIERES

1. Vue d'ensemble du projet
2. Architecture générale
3. Technologies utilisées
4. Prérequis et installation
5. Lancement de l'application
6. Structure du projet
7. Configuration
8. API REST complète
9. Fonctionnalités
10. Modèle de données
11. Gestion des comptes
12. Opérations bancaires
13. Dépannage
14. Informations supplémentaires

---

1. VUE D'ENSEMBLE DU PROJET

egaBank est une plateforme complète de gestion bancaire développée avec une architecture moderne microservices. Le système permet aux administrateurs de gérer les clients, les comptes bancaires, et les opérations financières (versements, retraits, virements).

Le projet est composé de deux applications indépendantes:
- Backend: API REST Spring Boot 3.2.0
- Frontend: Interface web Angular 16

Ces deux applications communiquent via des appels HTTP REST. Le backend fournit tous les services métier tandis que le frontend offre une interface utilisateur moderne et responsive.

---

2. ARCHITECTURE GÉNÉRALE

2.1 Architecture globale

Le système suit une architecture en couches avec deux composants principaux:

FRONTEND (Angular)
  |
  | HTTP/REST (JSON)
  |
API REST (Spring Boot)
  |
Business Logic (Services)
  |
Data Access (Repositories)
  |
H2 Database (En mémoire)

2.2 Flux de données

1. L'utilisateur interagit avec l'interface Angular
2. L'interface envoie des requêtes HTTP vers l'API REST
3. Le backend traite la requête via les services
4. Les services accèdent aux données via JPA/Hibernate
5. Les données sont stockées dans la base H2
6. La réponse est envoyée en JSON au frontend
7. Le frontend affiche les résultats à l'utilisateur

2.3 Domaines métier

Le système gère trois domaines principaux:

CLIENTS: Gestion des profils clients
- Création de nouveaux clients
- Consultation des données clients
- Associa des comptes aux clients

COMPTES: Gestion des comptes bancaires
- Deux types: Compte Courant (CC) et Compte Epargne (CE)
- Création et activation des comptes
- Suspension temporaire des comptes
- Suivi du solde et des détails

OPERATIONS: Gestion des transactions financières
- Versements: Dépôts d'argent
- Retraits: Retraits d'argent
- Virements: Transferts entre comptes
- Enregistrement automatique dans l'historique

---

3. TECHNOLOGIES UTILISÉES

3.1 Backend

Langage: Java 17
Framework: Spring Boot 3.2.0
ORM: Hibernate 6.3.1 avec JPA
Base de données: H2 (base mémoire embarquée)
Gestion des dépendances: Maven
API: REST avec contrôleurs Spring MVC
CORS: Activé pour communiquer avec le frontend

Dépendances principales:
- spring-boot-starter-web: Framework web et REST
- spring-boot-starter-data-jpa: Accès aux données
- h2: Base de données embarquée
- spring-boot-starter-validation: Validation des données

3.2 Frontend

Framework: Angular 16.2.12
Langage: TypeScript 5.1.6
Styling: Tailwind CSS 3.4.3
Réactivité: RxJS 7.8.0
Formulaires: Reactive Forms (FormBuilder, Validators)
HTTP Client: HttpClient Angular
Zone.js: 0.13.0

Dépendances principales:
- @angular/core: Framework Angular
- @angular/common: Utilitaires courants
- @angular/forms: Gestion des formulaires
- tailwindcss: Utilitaires CSS
- typescript: Typage statique

3.3 Outils de développement

Backend:
- Maven Wrapper (mvnw / mvnw.cmd)
- Spring Boot DevTools
- H2 Console

Frontend:
- Angular CLI 16.2.14
- npm: Gestionnaire de paquets
- TypeScript Compiler
- Tailwind CSS CLI

---

4. PREREQUIS ET INSTALLATION

4.1 Configuration requise

Système d'exploitation: Windows, Linux ou macOS
Java: JDK 17 ou supérieur
Node.js: 16.x ou supérieur
npm: 8.x ou supérieur
Navigateur web: Chrome, Firefox, Safari ou Edge (version récente)

4.2 Installation du backend

1. Naviguer vers le dossier Gescobank:
   cd Gescobank

2. Compiler le projet:
   ./mvnw clean install

   Ou sur Windows:
   mvnw.cmd clean install

3. Les dépendances seront téléchargées automatiquement depuis Maven Central

4.3 Installation du frontend

1. Naviguer vers le dossier GESCBANK:
   cd GESCBANK

2. Installer les dépendances npm:
   npm install --legacy-peer-deps

   L'option --legacy-peer-deps est nécessaire pour résoudre les conflits de dépendances

3. Les modules seront installés dans node_modules/

---

5. LANCEMENT DE L'APPLICATION

5.1 Démarrage du backend

1. Ouvrir un terminal dans le dossier Gescobank

2. Lancer l'application:
   ./mvnw spring-boot:run

   Ou sur Windows:
   mvnw.cmd spring-boot:run

3. Le serveur démarre sur http://localhost:8082

4. La base de données H2 est accessible sur http://localhost:8082/h2-console
   URL: jdbc:h2:mem:gescobank
   Utilisateur: sa
   Mot de passe: (vide)

5.2 Démarrage du frontend

1. Ouvrir un terminal dans le dossier GESCBANK

2. Lancer le serveur de développement:
   npm start

3. L'application s'ouvre sur http://localhost:4200

4. Votre navigateur se réactualise automatiquement lors de changements de code

5.3 Vérification du démarrage

Backend: Vérifier http://localhost:8082/api/v1/clients
- Doit retourner une liste JSON (vide ou avec des clients)
- Code HTTP: 200

Frontend: Vérifier http://localhost:4200
- La page de connexion doit s'afficher
- Les styles Tailwind doivent être visibles

---

6. STRUCTURE DU PROJET

6.1 Structure backend

Gescobank/
├── src/main/java/com/bankiapp/
│   ├── dto/                          # Data Transfer Objects
│   │   ├── ClientDTO
│   │   ├── CompteBancaireDTO
│   │   └── OperationDTO
│   ├── entities/                     # Entités JPA
│   │   ├── Client.java               # Client avec OneToMany Comptes
│   │   ├── AccountBank.java          # Classe abstraite parent
│   │   ├── CompteCourant.java        # Hérité de AccountBank
│   │   ├── CompteEpargne.java        # Hérité de AccountBank
│   │   └── Operation.java            # Opération bancaire
│   ├── enums/                        # Énumérations
│   │   ├── OperationType.java        # VERSEMENT, RETRAIT, VIREMENT
│   │   └── AccountStatus.java        # ACTIVE, SUSPENDED
│   ├── repositories/                 # Accès aux données
│   │   ├── ClientRepository
│   │   ├── CompteBancaireRepository
│   │   ├── CompteCourantRepository
│   │   ├── CompteEpargneRepository
│   │   └── OperationRepository
│   ├── services/                     # Logique métier
│   │   ├── ClientService
│   │   ├── CompteService
│   │   └── OperationService
│   └── web/                          # Contrôleurs REST
│       ├── ClientRestController
│       ├── CompteBancaireRestController
│       └── OperationRestController
├── src/main/resources/
│   └── application.properties         # Configuration
├── src/test/                          # Tests unitaires
├── pom.xml                            # Configuration Maven
└── README.md                          # Documentation

6.2 Structure frontend

GESCBANK/
├── src/
│   ├── app/
│   │   ├── core/                     # Services et modèles
│   │   │   ├── models/
│   │   │   │   ├── client.model.ts
│   │   │   │   ├── compte.model.ts
│   │   │   │   └── operation.model.ts
│   │   │   └── services/
│   │   │       ├── client.service.ts
│   │   │       ├── compte.service.ts
│   │   │       └── operation.service.ts
│   │   ├── pages/                    # Pages principales
│   │   │   ├── auth/                 # Authentification
│   │   │   ├── home/                 # Accueil
│   │   │   ├── clients/              # Gestion des clients
│   │   │   │   └── clients/main/
│   │   │   │       ├── list/         # Liste des clients
│   │   │   │       └── add/          # Ajout de client
│   │   │   ├── comptes/              # Gestion des comptes
│   │   │   │   └── comptes/main/
│   │   │   │       ├── list/         # Liste des comptes
│   │   │   │       ├── etat-compte/  # État d'un compte
│   │   │   │       └── historiques/  # Historique des opérations
│   │   │   └── operations/           # Opérations bancaires
│   │   │       ├── main/             # Sélection d'opération
│   │   │       ├── versement/        # Formulaire versement
│   │   │       ├── retrait/          # Formulaire retrait
│   │   │       └── virement/         # Formulaire virement
│   │   ├── shared/                   # Composants partagés
│   │   │   └── modules/
│   │   │       ├── page-header/      # En-tête avec navigation
│   │   │       └── page-footer/      # Pied de page
│   │   ├── app.component.ts
│   │   └── app.module.ts
│   ├── environments/
│   │   ├── environment.ts            # Configuration développement
│   │   └── environment.prod.ts       # Configuration production
│   ├── main.ts
│   ├── index.html
│   └── styles.scss
├── angular.json                      # Configuration Angular
├── tsconfig.json                     # Configuration TypeScript
├── package.json                      # Dépendances npm
└── README.md                         # Documentation

---

7. CONFIGURATION

7.1 Backend - application.properties

Le fichier src/main/resources/application.properties configure:

server.port=8081
Démarre le serveur sur le port 8082

spring.datasource.url=jdbc:h2:mem:gescobank
Utilise une base H2 en mémoire nommée 'gescobank'

spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
Identifiants de connexion H2

spring.h2.console.enabled=true
Active la console H2 accessible sur /h2-console

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
Configure Hibernate pour H2

spring.jpa.hibernate.ddl-auto=create
Crée automatiquement les tables au démarrage

spring.jpa.show-sql=false
N'affiche pas les requêtes SQL en console

7.2 Frontend - environment.ts

Le fichier src/environments/environment.ts configure:

export const environment = {
   production: false,
   apiUrl: 'http://localhost:8082',
   prefix: 'api/v1'
};

Les services utilisent cette configuration pour construire l'URL de base:
http://localhost:8082/api/v1

7.3 Modification pour la production

Pour déployer en production:

Backend:
- Changer server.port à 80 ou 443
- Utiliser une vrai base de données (PostgreSQL, MySQL)
- Mettre ddl-auto à 'validate'
- Configurer CORS correctement

Frontend:
- Mettre production: true
- Mettre apiUrl vers le domaine réel
- Faire un build de production: ng build --configuration production

---

8. API REST COMPLETE

8.1 Configuration générale de l'API

Base URL: http://localhost:8082/api/v1
Format: JSON
CORS: Activé pour tous les domaines (*)
Code HTTP: Standard (200, 201, 400, 404, 500)

8.2 Endpoints clients

GET /api/v1/clients
Récupère la liste de tous les clients
Réponse: [Client]
Code: 200

POST /api/v1/clients
Crée un nouveau client
Body: {firstName, lastName, email, telephone, birthday}
Réponse: Client créé
Code: 201

GET /api/v1/clients/{id}
Récupère un client spécifique par ID
Paramètre: id (Long)
Réponse: Client
Code: 200

8.3 Endpoints comptes

GET /api/v1/comptes?type=CC
Récupère les comptes d'un type spécifique
Paramètres: type (CC ou CE)
Réponse: [CompteBancaire]
Code: 200

POST /api/v1/comptes
Crée un nouveau compte
Body: {numCompte, balance, type, clientId, decouvert/tauxInteret}
Réponse: Compte créé
Code: 201

GET /api/v1/comptes/{num}/{type}
Récupère un compte spécifique
Paramètres: num (numéro), type (CC ou CE)
Réponse: CompteBancaire
Code: 200

GET /api/v1/comptes/active/{num}
Active un compte suspendu
Paramètre: num (numéro du compte)
Réponse: Compte activé
Code: 200

GET /api/v1/comptes/suspendre/{num}
Suspend un compte actif
Paramètre: num (numéro du compte)
Réponse: Compte suspendu
Code: 200

8.4 Endpoints opérations

POST /api/v1/versements
Effectue un versement (dépôt)
Body: {numCompte, type, montant}
Réponse: Opération créée
Code: 201

POST /api/v1/retraits
Effectue un retrait
Body: {numCompte, type, montant}
Réponse: Opération créée
Code: 201

POST /api/v1/virements
Effectue un virement
Body: {numCompteS, numCompteD, type, montant}
Réponse: Opération créée
Code: 201

GET /api/v1/operations/client/{num}
Récupère l'historique des opérations
Paramètre: num (numéro du compte)
Réponse: [Operation]
Code: 200

---

9. FONCTIONNALITES

9.1 Gestion des clients

CREATE: Ajouter un nouveau client
- Formulaire avec validation
- Champs: Nom, Prénom, Email, Téléphone, Date de naissance
- Validation côté client et serveur
- Attribution automatique d'un ID unique

READ: Consulter les clients
- Liste avec barre de recherche
- Affichage des informations essentielles
- Pagination intégrée
- Statistiques: nombre total, clients actifs, dernière mise à jour

UPDATE: Modifier un client
- Non implémenté dans cette version

DELETE: Supprimer un client
- Non implémenté dans cette version

9.2 Gestion des comptes

CREATE: Créer un compte bancaire
- Après création d'un client
- Choix du type: Compte Courant ou Compte Epargne
- Définition du solde initial
- Paramètres spécifiques:
  * Compte Courant: Découvert autorisé
  * Compte Epargne: Taux d'intérêt

READ: Consulter les comptes
- Liste avec filtrage par type
- Affichage du numéro, solde, type, statut
- Badges colorés pour le statut
- Statistiques: comptes actifs, solde total, etc.

ACTIVATE: Activer un compte suspendu
- Changement du statut en "ACTIVE"
- Action immédiate

SUSPEND: Suspendre un compte
- Changement du statut en "SUSPENDED"
- Empêche les opérations

9.3 Opérations bancaires

VERSEMENT: Dépôt d'argent
- Sélection du compte
- Entrée du montant
- Ajout au solde
- Enregistrement automatique dans l'historique

RETRAIT: Retrait d'argent
- Sélection du compte
- Vérification du solde et découvert
- Déduction du montant
- Enregistrement automatique

VIREMENT: Transfert entre comptes
- Sélection du compte source
- Sélection du compte destination
- Montant à transférer
- Déduction du compte source
- Ajout au compte destination
- Deux enregistrements dans l'historique

HISTORIQUE: Consulter les opérations
- Timeline des opérations
- Tri par date décroissante
- Détails: type, montant, date, compte client
- Affichage du solde après chaque opération

---

10. MODELE DE DONNEES

10.1 Entité Client

Attributs:
- id: Long (clé primaire, auto-générée)
- firstName: String (requis)
- lastName: String (requis)
- email: String (optionnel)
- telephone: String (requis)
- birthday: LocalDate (optionnel)
- createdAt: LocalDateTime (auto-rempli)

Relations:
- OneToMany avec AccountBank (cascade delete)
- Un client peut avoir plusieurs comptes

10.2 Entité AccountBank (abstraite)

Attributs:
- numCompte: String (clé primaire)
- balance: BigDecimal (solde actuel)
- createdAt: LocalDateTime
- status: AccountStatus (ACTIVE ou SUSPENDED)

Relations:
- ManyToOne avec Client
- OneToMany avec Operation (cascade delete)

Héritage:
- CompteCourant (ajoute: decouvert)
- CompteEpargne (ajoute: tauxInteret)

10.3 Entité CompteCourant

Attributs hérités: numCompte, balance, createdAt, status
Attributs spécifiques:
- decouvert: BigDecimal (découvert autorisé)

Utilisation:
- Pour les clients avec liquidités fréquentes
- Permet de dépasser le solde dans la limite du découvert

10.4 Entité CompteEpargne

Attributs hérités: numCompte, balance, createdAt, status
Attributs spécifiques:
- tauxInteret: BigDecimal (taux annuel en pourcentage)

Utilisation:
- Pour l'épargne à long terme
- Accumule les intérêts

10.5 Entité Operation

Attributs:
- id: Long (clé primaire, auto-générée)
- numOperation: String (numéro unique)
- dateOperation: LocalDateTime (auto-remplie)
- type: OperationType (VERSEMENT, RETRAIT, VIREMENT)
- amount: BigDecimal (montant de l'opération)

Relations:
- ManyToOne avec AccountBank

Types d'opérations:
- VERSEMENT: Augmente le solde
- RETRAIT: Diminue le solde
- VIREMENT: Deux entrées (débit et crédit)

---

11. GESTION DES COMPTES

11.1 Statut des comptes

ACTIVE: Compte fonctionnel
- Les opérations peuvent être effectuées
- État par défaut après création
- Peut être suspendu

SUSPENDED: Compte suspendu
- Les opérations sont bloquées
- Utilisé pour gel temporaire
- Peut être réactivé

11.2 Types de comptes

Compte Courant (CC):
- Accès rapide aux fonds
- Permet découvert autorisé
- Idéal pour les transactions quotidiennes

Compte Epargne (CE):
- Accumulation d'épargne
- Taux d'intérêt rémunéré
- Transactions moins fréquentes

11.3 Règles de validation

À la création:
- Numéro unique
- Solde initial >= 0
- Client doit exister

À la suspension:
- Compte doit être actif

À la réactivation:
- Compte doit être suspendu

---

12. OPERATIONS BANCAIRES

12.1 Versement (Dépôt)

Processus:
1. Client sélectionne son compte
2. Entre le montant à verser
3. Système récupère le compte
4. Système ajoute le montant au solde
5. Système enregistre l'opération
6. Retour du succès au client

Validation:
- Montant > 0
- Compte existe
- Compte est actif

12.2 Retrait

Processus:
1. Client sélectionne son compte
2. Entre le montant à retirer
3. Système vérifie le solde disponible
4. Si suffisant: déduit du compte
5. Si insuffisant: vérifie le découvert
6. Enregistre l'opération
7. Retour du résultat

Validation:
- Montant > 0
- Compte existe
- Compte est actif
- Solde + Découvert >= Montant

12.3 Virement

Processus:
1. Client sélectionne compte source et destination
2. Entre le montant
3. Système vérifie les comptes
4. Système déduit du compte source
5. Système ajoute au compte destination
6. Enregistre deux opérations
7. Retour du succès

Validation:
- Comptes existent
- Comptes sont actifs
- Compte source a suffisamment de solde
- Montant > 0

12.4 Historique

Affichage:
- Timeline chronologique inverse (récent d'abord)
- Détails de chaque opération
- Client, compte, montant, date
- Solde après l'opération

---

13. DEPANNAGE

13.1 Erreur: Connection refusée au port 8081

Cause probable: Backend non lancé
Solution:
1. Vérifier que le terminal du backend est actif
2. Relancer: ./mvnw spring-boot:run
3. Attendre le message: "Started Application in X seconds"

13.2 Erreur: Connection refusée au port 4201

Cause probable: Frontend non lancé
Solution:
1. Naviguer dans GESCBANK
2. Lancer: npm start
3. Attendre que le navigateur s'ouvre automatiquement

13.3 Erreur: 404 Not Found sur un endpoint

Cause probable: URL incorrecte ou endpoint non implémenté
Solution:
1. Vérifier l'URL dans la barre d'adresse
2. Vérifier que le chemin commence par /api/v1/
3. Consulter la liste complète des endpoints en section 8

13.4 Erreur: CORS erreur dans la console

Cause probable: Frontend et backend sur domaines différents
Solution:
1. Vérifier les URLs:
   - Frontend: http://localhost:4201
   - Backend: http://localhost:8081
2. CORS est déjà activé (*) dans le backend
3. Vérifier les en-têtes HTTP en DevTools

13.5 Erreur: npm install échoue

Cause probable: Versions incompatibles
Solution:
1. Utiliser: npm install --legacy-peer-deps
2. Supprimer node_modules: rm -r node_modules
3. Supprimer package-lock.json
4. Relancer npm install --legacy-peer-deps

13.6 Erreur: Les styles Tailwind ne s'appliquent pas

Cause probable: Tailwind non compilé
Solution:
1. Dans GESCBANK, vérifier que npm start est en cours
2. Attendre la recompilation
3. Vider le cache du navigateur (Ctrl+Shift+Delete)

13.7 Base de données vide au démarrage

Comportement normal:
- La base H2 est créée en mémoire
- Les tables sont créées (ddl-auto=create)
- Aucune donnée par défaut
- Créer des clients pour commencer

Données persistantes:
- Persistantes tant que le serveur tourne
- Perdues si le serveur redémarre

13.8 Erreur: "Cannot resolve dependency"

Cause probable: Maven n'a pas téléchargé les dépendances
Solution:
1. Supprimer le dossier .m2 (cache Maven)
2. Relancer: ./mvnw clean install
3. Attendre le téléchargement des dépendances

---

14. INFORMATIONS SUPPLEMENTAIRES

14.1 Performance

Limitations actuelles:
- Base de données en mémoire (pas persistante)
- Pas de cache
- Pas de pagination côté serveur
- Pas d'optimisation de requêtes

Pour la production:
- Utiliser PostgreSQL ou MySQL
- Implémenter la pagination
- Ajouter des index de base de données
- Utiliser un cache (Redis)

14.2 Sécurité

Limitations actuelles:
- Pas d'authentification
- Pas de chiffrement des mots de passe
- CORS ouvert à tous (*) 
- Pas de validation HTTPS

Pour la production:
- Implémenter OAuth2 ou JWT
- Ajouter HTTPS/SSL
- Configurer CORS correctement
- Utiliser une base de données sécurisée
- Valider toutes les entrées utilisateur

14.3 Tests

Le projet inclut une structure pour les tests unitaires:
Backend: src/test/java/
Frontend: src/

Pour lancer les tests backend:
./mvnw test

Pour lancer les tests frontend:
ng test

14.4 Logs

Backend:
- Console: Affiche les logs Spring et Hibernate
- Niveau: Par défaut INFO

Frontend:
- DevTools (F12): Affiche les logs navigateur
- Console: Affiche les messages JavaScript/TypeScript

Pour augmenter les logs backend:
Modifier application.properties:
logging.level.root=DEBUG
logging.level.com.bankiapp=DEBUG

14.5 Fichiers de configuration

Backend:
- Gescobank/pom.xml: Dépendances Maven
- Gescobank/src/main/resources/application.properties: Configuration

Frontend:
- GESCBANK/package.json: Dépendances npm
- GESCBANK/angular.json: Configuration Angular
- GESCBANK/tsconfig.json: Configuration TypeScript
- GESCBANK/tailwind.config.js: Configuration Tailwind

14.6 Commandes utiles

Backend:
./mvnw clean install       # Compilation complète
./mvnw spring-boot:run     # Lancement
./mvnw test                # Tests

Frontend:
npm install                # Installation des dépendances
npm start                  # Développement
ng build                   # Build production
ng test                    # Tests
npm run lint               # Analyse du code

14.7 Ressources

Documentation:
- Spring Boot: https://spring.io/projects/spring-boot
- Angular: https://angular.io/docs
- Tailwind CSS: https://tailwindcss.com/docs
- Hibernate: https://hibernate.org/orm/documentation

API Testing:
- Postman Collection fournie: egaBank-API-Collection.postman_collection.json

15. CONCLUSION

egaBank est une plateforme bancaire complète et fonctionnelle démontrant une architecture moderne avec frontend et backend découplés. Le système peut être étendu avec:
- Authentification utilisateur
- Modules de reporting
- Notifications utilisateur
- API mobile
- Dashboard d'administration avancé
- Intégrations avec d'autres services

Pour toute question ou problème, consulter les logs et vérifier les endpoints API via Postman.
