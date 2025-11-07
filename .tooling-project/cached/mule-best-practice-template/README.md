# Mule Best Practice Template 🚀

Template de référence MuleSoft implémentant toutes les best practices pour le développement d'applications Mule.

## 📋 Table des matières

- [Vue d'ensemble](#vue-densemble)
- [Architecture](#architecture)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Configuration](#configuration)
- [Structure du projet](#structure-du-projet)
- [Best Practices implémentées](#best-practices-implémentées)
- [Exécution](#exécution)
- [Tests](#tests)
- [Patterns MuleSoft](#patterns-mulesoft)
- [Déploiement](#déploiement)
- [Contribuer](#contribuer)

## 🎯 Vue d'ensemble

Ce projet sert de template de référence pour le développement d'applications MuleSoft en suivant les meilleures pratiques de l'industrie. Il peut être utilisé comme point de départ pour de nouveaux projets ou comme référence pour améliorer des projets existants.

### Fonctionnalités principales

- ✅ Architecture API-led Connectivity (Experience, Process, System)
- ✅ Gestion globale des erreurs
- ✅ Logging standardisé et traçabilité (Correlation ID)
- ✅ Configuration par environnement (local, dev, prod)
- ✅ Tests MUnit complets avec couverture élevée
- ✅ Patterns MuleSoft courants (Scatter-Gather, Content-Based Routing, etc.)
- ✅ Validation des données d'entrée
- ✅ Documentation complète

## 🏗️ Architecture

### API-led Connectivity Pattern

Le projet est structuré selon le pattern API-led Connectivity avec trois couches distinctes :

```
┌─────────────────────────────────────────┐
│     Experience Layer (API Layer)         │
│  - Exposition des APIs                   │
│  - Transformation pour le client         │
│  - api-layer-experience.xml              │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│     Process Layer (Orchestration)        │
│  - Logique métier                        │
│  - Orchestration des services            │
│  - Enrichissement des données            │
│  - api-layer-process.xml                 │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│     System Layer (Backend Integration)   │
│  - Intégration systèmes externes         │
│  - Accès aux bases de données            │
│  - api-layer-system.xml                  │
└─────────────────────────────────────────┘
```

### Composants principaux

- **global.xml** : Configuration globale (HTTP, propriétés, connecteurs)
- **error-handlers.xml** : Gestion centralisée des erreurs
- **common-flows.xml** : Flows réutilisables (logging, validation, enrichissement)
- **patterns-examples.xml** : Exemples de patterns MuleSoft

## 📦 Prérequis

- **Java** : JDK 17 ou supérieur
- **Maven** : 3.8.x ou supérieur
- **Anypoint Studio** : 7.x ou supérieur (optionnel)
- **Mule Runtime** : 4.10.0
- **Git** : Pour le versioning

## 🚀 Installation

### 1. Cloner le projet

```bash
git clone <repository-url>
cd mule-best-practice-template
```

### 2. Configurer les propriétés

Créer ou modifier les fichiers de configuration dans `src/main/resources/` :

- `config.local.yaml` - Pour l'exécution locale
- `config.dev.yaml` - Pour l'environnement de développement
- `config.prod.yaml` - Pour la production

### 3. Installer les dépendances

```bash
mvn clean install
```

## ⚙️ Configuration

### Configuration par environnement

Le projet utilise un système de configuration par environnement via des fichiers YAML :

**Structure des fichiers de configuration :**

```yaml
# HTTP Listener Configuration
http:
  host: "localhost"
  port: "8082"

# Database Configuration
db:
  host: "localhost"
  port: "5432"
  database: "mydb"
  user: "user"
  password: "password"

# API Configuration
api:
  name: "Mule Best Practice API"
  version: "v1"
  basePath: "/api/v1"

# External Services
services:
  external:
    baseUrl: "http://localhost:9090"
    timeout: "10000"
    maxRetries: "2"

# Security
security:
  client:
    id: "client-id"
    secret: "client-secret"

# Logging
logging:
  level: "DEBUG"
  correlationIdEnabled: true

# Business Configuration
business:
  maxTransactionAmount: "1000"
  batchSize: "5"
```

### Sélection de l'environnement

**Via Maven profiles :**

```bash
# Local
mvn clean install -Plocal

# Dev
mvn clean install -Pdev

# Production
mvn clean install -Pprod
```

**Via variable d'environnement :**

```bash
export MULE_ENV=dev
```

## 📁 Structure du projet

```
mule-best-practice-template/
│
├── src/
│   ├── main/
│   │   ├── mule/
│   │   │   ├── global.xml                    # Configuration globale
│   │   │   ├── error-handlers.xml            # Gestion des erreurs
│   │   │   ├── common-flows.xml              # Flows réutilisables
│   │   │   ├── api-layer-experience.xml      # Couche Experience
│   │   │   ├── api-layer-process.xml         # Couche Process
│   │   │   ├── api-layer-system.xml          # Couche System
│   │   │   └── patterns-examples.xml         # Exemples de patterns
│   │   │
│   │   └── resources/
│   │       ├── config.local.yaml             # Config locale
│   │       ├── config.dev.yaml               # Config développement
│   │       ├── config.prod.yaml              # Config production
│   │       └── log4j2.xml                    # Configuration logging
│   │
│   └── test/
│       ├── munit/
│       │   ├── test-api-experience.xml       # Tests couche Experience
│       │   ├── test-api-process.xml          # Tests couche Process
│       │   └── test-common-flows.xml         # Tests flows communs
│       │
│       └── resources/
│           └── log4j2-test.xml               # Config logging tests
│
├── pom.xml                                   # Configuration Maven
├── mule-artifact.json                        # Métadonnées Mule
└── README.md                                 # Cette documentation
```

## ✨ Best Practices implémentées

### 1. **Gestion des erreurs**

- Error handlers globaux réutilisables
- Codes d'erreur standardisés
- Messages d'erreur formatés en JSON
- Logging approprié de chaque erreur
- Traçabilité via Correlation ID

**Exemple d'utilisation :**

```xml
<error-handler ref="global-api-error-handler" doc:name="Global Error Handler"/>
```

### 2. **Logging et traçabilité**

- Génération automatique de Correlation ID
- Logging à chaque étape importante
- Niveaux de log appropriés (DEBUG, INFO, WARN, ERROR)
- Format de log standardisé
- Timestamp de début/fin de requête

**Exemple :**

```
[START] CorrelationId: 123e4567-e89b | Method: GET | Path: /api/v1/customers
[END] CorrelationId: 123e4567-e89b | StatusCode: 200 | Duration: 125ms
```

### 3. **Configuration externalisée**

- Propriétés par environnement (local, dev, prod)
- Aucune valeur hardcodée dans le code
- Gestion sécurisée des secrets (production)
- Réutilisation facile dans différents environnements

### 4. **API-led Connectivity**

- Séparation claire des responsabilités
- Experience Layer : Transformation pour le client
- Process Layer : Logique métier et orchestration
- System Layer : Intégration backend

### 5. **Validation des données**

- Validation des inputs avant traitement
- Validation métier (email, montants, etc.)
- Messages d'erreur descriptifs
- Prévention des erreurs en amont

### 6. **Tests automatisés**

- Tests MUnit pour chaque couche
- Tests unitaires des flows
- Mocking des dépendances externes
- Tests des cas d'erreur
- Couverture de code élevée

### 7. **Réutilisabilité**

- Sub-flows pour la logique commune
- Flows paramétrables
- Configuration partagée
- Patterns réutilisables

## 🏃 Exécution

### Exécution locale

```bash
# Avec Maven
mvn clean install -Plocal
mvn mule:run

# Avec Anypoint Studio
1. Importer le projet
2. Clic droit > Run As > Mule Application
```

### URLs disponibles

Une fois l'application démarrée, les endpoints suivants sont disponibles :

#### Experience Layer

- **Health Check** : `GET http://localhost:8082/api/v1/health`
- **Get Customers** : `GET http://localhost:8082/api/v1/customers`
- **Create Customer** : `POST http://localhost:8082/api/v1/customers`

#### Patterns Examples

- **Scatter-Gather** : `GET http://localhost:8082/api/v1/patterns/scatter-gather`
- **Content-Based Routing** : `POST http://localhost:8082/api/v1/patterns/route-message`
- **Idempotent Filter** : `POST http://localhost:8082/api/v1/patterns/idempotent`
- **Batch Processing** : `POST http://localhost:8082/api/v1/patterns/batch`

### Exemples d'appels

**Health Check :**

```bash
curl http://localhost:8082/api/v1/health
```

**Créer un client :**

```bash
curl -X POST http://localhost:8082/api/v1/customers \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Marie",
    "lastName": "Dupont",
    "email": "marie.dupont@example.com",
    "phone": "+33612345678"
  }'
```

**Content-Based Routing :**

```bash
curl -X POST http://localhost:8082/api/v1/patterns/route-message \
  -H "Content-Type: application/json" \
  -d '{
    "type": "PAYMENT",
    "amount": 150.50,
    "currency": "EUR"
  }'
```

## 🧪 Tests

### Exécuter tous les tests

```bash
mvn test
```

### Exécuter un test spécifique

```bash
mvn test -Dtest=test-api-experience
```

### Couverture des tests

Les tests couvrent :

- ✅ Flows de la couche Experience
- ✅ Flows de la couche Process
- ✅ Flows communs (validation, logging, etc.)
- ✅ Gestion des erreurs
- ✅ Validation des données
- ✅ Cas de succès et d'erreur

### Structure des tests

```xml
<munit:test name="test-name" description="Test description">
    <munit:behavior>
        <!-- Setup et mocks -->
    </munit:behavior>
    
    <munit:execution>
        <!-- Exécution du flow à tester -->
    </munit:execution>
    
    <munit:validation>
        <!-- Assertions -->
    </munit:validation>
</munit:test>
```

## 🎨 Patterns MuleSoft

### 1. Scatter-Gather Pattern

Appelle plusieurs services en parallèle et agrège les résultats.

**Cas d'usage :** Récupération d'informations depuis plusieurs sources

```bash
curl http://localhost:8082/api/v1/patterns/scatter-gather
```

### 2. Content-Based Routing

Route les messages vers différents processors selon leur contenu.

**Cas d'usage :** Traitement différent selon le type de transaction

```bash
curl -X POST http://localhost:8082/api/v1/patterns/route-message \
  -H "Content-Type: application/json" \
  -d '{"type": "PAYMENT", "amount": 100}'
```

### 3. Idempotent Filter

Évite le traitement en double des messages.

**Cas d'usage :** Garantir qu'un message n'est traité qu'une seule fois

```bash
curl -X POST http://localhost:8082/api/v1/patterns/idempotent \
  -H "Content-Type: application/json" \
  -d '{"messageId": "MSG-001", "data": "..."}'
```

### 4. Batch Processing

Traite les données par lots pour optimiser les performances.

**Cas d'usage :** Import de données en masse

```bash
curl -X POST http://localhost:8082/api/v1/patterns/batch \
  -H "Content-Type: application/json" \
  -d '{"items": [...]}'
```

## 🚢 Déploiement

### Déploiement sur CloudHub

```bash
mvn clean deploy -DmuleDeploy \
  -Dmule.env=prod \
  -Danypoint.username=<username> \
  -Danypoint.password=<password>
```

### Déploiement sur Runtime Fabric

```bash
mvn clean deploy -DmuleDeploy \
  -Dmule.env=prod \
  -Danypoint.username=<username> \
  -Danypoint.password=<password> \
  -Dtarget=<target-name>
```

### Déploiement On-Premise

1. Build du projet :
   ```bash
   mvn clean package -Pprod
   ```

2. Copier le JAR dans le dossier apps du runtime :
   ```bash
   cp target/*.jar $MULE_HOME/apps/
   ```

## 📊 Monitoring et Observabilité

### Correlation ID

Chaque requête génère ou utilise un Correlation ID unique pour la traçabilité complète.

**Header HTTP :**
```
X-Correlation-ID: 123e4567-e89b-12d3-a456-426614174000
```

### Logs

Les logs incluent :
- Correlation ID
- Timestamp
- Niveau de log
- Message descriptif
- Contexte (méthode HTTP, path, etc.)

**Exemple de log :**
```
[INFO] [START] CorrelationId: 123e4567 | Method: POST | Path: /api/v1/customers
[DEBUG] [PROCESS] CorrelationId: 123e4567 | Processing create customer request
[INFO] [END] CorrelationId: 123e4567 | StatusCode: 201 | Duration: 145ms
```

## 🔒 Sécurité

### Best Practices de sécurité

- ✅ Pas de secrets hardcodés
- ✅ Utilisation de Secure Properties pour la production
- ✅ Validation des inputs
- ✅ Gestion appropriée des erreurs (pas de stacktrace en prod)
- ✅ Logging sécurisé (pas de données sensibles)

### Configuration des secrets

**Développement :**
```yaml
security:
  client:
    id: "dev-client-id"
    secret: "dev-client-secret"
```

**Production :**
Utiliser Anypoint Runtime Manager ou Secure Properties :
```yaml
security:
  client:
    id: "${secure::client.id}"
    secret: "${secure::client.secret}"
```

## 🤝 Contribuer

Ce template est conçu pour être évolutif. Pour contribuer :

1. Fork le projet
2. Créer une branche feature (`git checkout -b feature/AmazingFeature`)
3. Commit les changements (`git commit -m 'Add some AmazingFeature'`)
4. Push vers la branche (`git push origin feature/AmazingFeature`)
5. Ouvrir une Pull Request

### Standards de code

- Suivre les conventions de nommage MuleSoft
- Documenter les flows complexes
- Ajouter des tests pour les nouvelles fonctionnalités
- Mettre à jour la documentation

## 📚 Ressources

- [MuleSoft Documentation](https://docs.mulesoft.com/)
- [API-led Connectivity](https://www.mulesoft.com/resources/api/api-led-connectivity)
- [MUnit Documentation](https://docs.mulesoft.com/munit/)
- [DataWeave Documentation](https://docs.mulesoft.com/dataweave/)
- [Best Practices](https://docs.mulesoft.com/mule-runtime/latest/intro-mule-best-practices)

## 📝 Licence

Ce projet est sous licence MIT. Voir le fichier `LICENSE` pour plus de détails.

## 👥 Auteurs

- **Kevin J. N.** - Template initial

## 🙏 Remerciements

- L'équipe MuleSoft pour Jasmine Conseil
- La communauté pour les contributions

---

**Note :** Ce template est un point de départ. Adaptez-le selon vos besoins spécifiques et les exigences de votre organisation.

