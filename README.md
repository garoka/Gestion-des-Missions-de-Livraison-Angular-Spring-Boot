# Gestion des Livraisons

Application full-stack de gestion de chauffeurs, de véhicules (matricules) et de missions de livraison.

Le projet est composé d'une API REST développée avec **Spring Boot** et d'une interface utilisateur développée avec **Angular**. L'authentification et la gestion des utilisateurs sont assurées par **Keycloak**, avec une persistance des données dans **MySQL**.

## Fonctionnalités

- Gestion des chauffeurs :
  - Ajout, modification, consultation et suppression
  - Vérification de l'existence d'un CIN
  - Consultation des chauffeurs disponibles
- Gestion des véhicules :
  - Ajout, modification, consultation et suppression des matricules
  - Affectation d'un véhicule à un chauffeur
- Gestion des missions :
  - Création et suppression de missions
  - Affectation d'un chauffeur à une mission
  - Suivi des missions actives
  - Consultation de l'historique des missions d'un chauffeur
- Authentification et gestion des utilisateurs :
  - Inscription et connexion avec Keycloak
  - Création des utilisateurs dans Keycloak et dans MySQL
  - Protection des API avec des jetons JWT
- Interface Angular avec Bootstrap et séparation des layouts utilisateur/authentification

## Technologies utilisées

| Couche | Technologies |
|---|---|
| Front-end | Angular 18.2, TypeScript 5.5, RxJS, Bootstrap 5 |
| Back-end | Spring Boot 4.0, Java 17, Spring Security, OAuth2 Resource Server |
| Identité | Keycloak 26 |
| Base de données | MySQL |
| Accès aux données | Spring Data JPA / Hibernate |
| Tests API | Postman |
| Tests front-end | Jasmine, Karma |

## Architecture du projet

```text
Back-End/
├── Back-End/             # API REST Spring Boot
│   ├── src/main/java/tn/validation/
│   │   ├── Controller/   # Points d'entrée REST
│   │   ├── Entity/       # Entités JPA
│   │   ├── Repository/   # Accès aux données
│   │   ├── Servic/       # Services métier
│   │   ├── Config/       # Sécurité et CORS
│   │   ├── Dto/          # Objets de transfert
│   │   └── Scheduled/    # Tâches planifiées
│   └── src/main/resources/
│       ├── application.properties
│       └── keycloak.json
└── Front-end/            # Application Angular
    └── src/app/
        ├── Auth/         # Connexion et inscription
        ├── ChauffeurC/   # Gestion des chauffeurs
        ├── MatriculeC/   # Gestion des véhicules
        ├── MissionC/     # Gestion des missions
        ├── models/       # Modèles TypeScript
        ├── service/      # Appels HTTP et intégration Keycloak
        ├── layouts/      # Mise en page
        └── nav/          # Navigation
```

## Prérequis

Installer les outils suivants :

- Java 17
- Maven 3.9 ou Maven Wrapper
- Node.js 18 ou supérieur
- Angular CLI 18
- MySQL 8 ou compatible
- Keycloak 26
- Postman

## Configuration

### 1. Base de données MySQL

Créer une base nommée `newss` ou modifier l'adresse de connexion :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/newss?createDatabaseIfNotExist=true&serverTimezone=UTC
spring.datasource.username=<MYSQL_USERNAME>
spring.datasource.password=<MYSQL_PASSWORD>
```

Les paramètres sont situés dans `Back-End/src/main/resources/application.properties`.

### 2. Keycloak

Exécuter Keycloak sur le port `8080`, puis créer :

- Un realm nommé `pct-realm`
- Un client nommé `pct-client`
- Une méthode d'accès adaptée à l'authentification utilisée
- Les rôles nécessaires aux utilisateurs

Configurer ensuite les propriétés suivantes dans `application.properties` :

```properties
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8080/realms/pct-realm
keycloak.admin.server-url=http://localhost:8080
keycloak.admin.realm=pct-realm
keycloak.admin.client-id=pct-client
keycloak.admin.client-secret=<KEYCLOAK_CLIENT_SECRET>
```

Le fichier `keycloak.json` doit également contenir les informations du realm, du serveur d'authentification et du client.

> Ne commitez jamais le secret Keycloak, les mots de passe MySQL ou d'autres identifiants réels. Utilisez des variables d'environnement ou des profils Spring séparés pour les environnements de production.

### 3. Configuration Angular

Le front-end communique avec l'API sur `http://localhost:8077`. Cette adresse est configurée dans `Front-end/src/app/service/service.service.ts`.

## Installation et lancement

### Back-end

```bash
cd Back-End
./mvnw spring-boot:run
```

Sous Windows, si vous utilisez l'invite de commandes :

```cmd
mvnw.cmd spring-boot:run
```

L'API sera disponible sur :

```text
http://localhost:8077
```

### Front-end

```bash
cd Front-end
npm install
npm start
```

L'application sera disponible sur :

```text
http://localhost:4200
```

## Endpoints principaux

### Chauffeurs

| Méthode | Endpoint | Description |
|---|---|---|
| `GET` | `/api/chauffeur/all` | Liste des chauffeurs |
| `GET` | `/api/chauffeur/{id}` | Chauffeur par identifiant |
| `GET` | `/api/chauffeur/available` | Chauffeurs disponibles |
| `POST` | `/api/chauffeur/add` | Ajouter un chauffeur |
| `PUT` | `/api/chauffeur/update/{id}` | Modifier un chauffeur |
| `DELETE` | `/api/chauffeur/{id}` | Supprimer un chauffeur |

### Matricules

| Méthode | Endpoint | Description |
|---|---|---|
| `GET` | `/api/matricule/all` | Liste des véhicules |
| `GET` | `/api/matricule/available` | Véhicules disponibles |
| `POST` | `/api/matricule/add` | Ajouter un véhicule |
| `PUT` | `/api/matricule/Update/{id}` | Modifier un véhicule |
| `DELETE` | `/api/matricule/delete/{id}` | Supprimer un véhicule |

### Missions

| Méthode | Endpoint | Description |
|---|---|---|
| `GET` | `/api/mission/all` | Liste des missions |
| `GET` | `/api/mission/Historique/{id}` | Historique d'un chauffeur |
| `POST` | `/api/mission/add` | Ajouter une mission |
| `PUT` | `/api/mission/TerminerMission/{missionId}` | Terminer une mission |
| `DELETE` | `/api/mission/delete/{id}` | Supprimer une mission |

### Utilisateurs

| Méthode | Endpoint | Description |
|---|---|---|
| `POST` | `/api/user/add` | Inscription |
| `POST` | `/api/user/login` | Connexion via Keycloak |
| `GET` | `/api/user/all` | Liste des utilisateurs |
| `GET` | `/api/user/{id}` | Utilisateur par identifiant |
| `DELETE` | `/api/user/{id}` | Supprimer un utilisateur |

## Tests

### Tests Angular

```bash
cd Front-end
npm test
```

### Tests Spring Boot

```bash
cd Back-End
./mvnw test
```

Sous Windows :

```cmd
mvnw.cmd test
```

### Tests API avec Postman

1. Importer les endpoints de l'application dans une collection Postman.
2. Récupérer un jeton d'accès auprès de Keycloak.
3. Ajouter le jeton dans l'onglet **Authorization** avec le type **Bearer Token**.
4. Tester les opérations CRUD et les affectations.

## Sécurité

- Les échanges avec l'API sont protégés par OAuth2/JWT.
- Les routes `/api/user/login` et `/api/user/add` sont publiques.
- Les autres routes API nécessitent un utilisateur authentifié.
- Les applications Angular et Spring autorisent les échanges depuis `http://localhost:4200`.
