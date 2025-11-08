# Mule Best Practice Template 🚀

Template de référence MuleSoft implémentant toutes les best practices pour le développement d'applications Mule.

## 🎯 Vue d'ensemble

Ce projet sert de template de référence pour le développement d'applications MuleSoft en suivant les meilleures pratiques de l'industrie. Il peut être utilisé comme point de départ pour de nouveaux projets ou comme référence pour améliorer des projets existants.

### ✨ Fonctionnalités

- ✅ **APIkit Router** avec spécification RAML complète
- ✅ **Architecture API-led Connectivity** (Experience, Process, System)
- ✅ **Console APIkit** pour tester l'API
- ✅ **Gestion globale des erreurs** avec codes standardisés
- ✅ **Logging standardisé** et traçabilité (Correlation ID)
- ✅ **Configuration par environnement** (local, dev, prod)
- ✅ **Tests MUnit complets** avec mocking
- ✅ **Patterns MuleSoft** (Scatter-Gather, Content-Based Routing, Idempotent, Batch)
- ✅ **Validation des données** et sécurité
- ✅ **DataWeave optimisé** et réutilisable

---

## 📦 Installation rapide

### Prérequis
- **Java** : JDK 17+ (compatible Java 21)
- **Maven** : 3.8.x+
- **Mule Runtime** : 4.10.0
- **Anypoint Studio** : 7.x+ (optionnel)

### Installation

```bash
# 1. Cloner le projet
git clone <repository-url>
cd mule-best-practice-template

# 2. Installer les dépendances
mvn clean install

# 3. Exécuter localement
mvn mule:run -Plocal
```

L'application démarre sur `http://localhost:8082`

**Console APIkit** : `http://localhost:8082/console` (pour tester l'API)

> **Note** : Au premier déploiement, assurez-vous que le domaine Mule "default" existe. Le script de déploiement le créera automatiquement si nécessaire.

---

## 🏗️ Architecture

### API-led Connectivity

```
┌─────────────────────────────────────────┐
│     EXPERIENCE LAYER                     │  ← Transformation pour le client
│  - api-layer-experience.xml             │  ← Agrégation de données
└──────────────────┬──────────────────────┘
                   │
┌──────────────────▼──────────────────────┐
│     PROCESS LAYER                        │  ← Logique métier
│  - api-layer-process.xml                │  ← Orchestration
└──────────────────┬──────────────────────┘
                   │
┌──────────────────▼──────────────────────┐
│     SYSTEM LAYER                         │  ← Intégration backends
│  - api-layer-system.xml                 │  ← Accès DB/Services
└──────────────────────────────────────────┘
```

### Structure du projet

```
mule-best-practice-template/
├── src/main/
│   ├── mule/
│   │   ├── global.xml                # Configuration globale
│   │   ├── interface.xml             # APIkit Router & Interface API
│   │   ├── error-handlers.xml        # Gestion des erreurs
│   │   ├── common-flows.xml          # Sub-flows réutilisables
│   │   ├── api-layer-process.xml     # Couche Process
│   │   ├── api-layer-system.xml      # Couche System
│   │   └── patterns-examples.xml     # Patterns MuleSoft
│   └── resources/
│       ├── api/
│       │   ├── mule-best-practice-template.raml  # Spec RAML
│       │   └── exchange.json         # Métadonnées Exchange
│       ├── config.local.yaml         # Config locale
│       ├── config.dev.yaml           # Config dev
│       ├── config.prod.yaml          # Config production
│       └── log4j2.xml
├── src/test/munit/
│   ├── test-api-process.xml
│   └── test-common-flows.xml
└── pom.xml
```

---

## ⚙️ Configuration

### Sélection de l'environnement

La configuration de l'environnement se fait via la propriété `env` dans `global.xml` :

```xml
<global-property doc:name="Environment Property" name="env" value="local"/>
```

**Modifier l'environnement** :
- Pour **dev** : changer `value="local"` par `value="dev"`
- Pour **prod** : changer `value="local"` par `value="prod"`

Le fichier correspondant (`config.local.yaml`, `config.dev.yaml` ou `config.prod.yaml`) sera automatiquement chargé.

### Fichiers de configuration

Chaque environnement a son propre fichier YAML :

```yaml
# config.local.yaml
# Environment
env: "local"

# HTTP Listener Configuration
http:
  host: "localhost"
  port: "8082"

# Database Configuration
db:
  host: "localhost"
  port: "5432"
  database: "local_db"
  user: "local_user"
  password: "local_password"

# API Configuration
api:
  name: "Mule Best Practice API"
  version: "v1"
  basePath: "/api/v1"

# Logging
logging:
  level: "DEBUG"
  correlationIdEnabled: "true"

# Business Configuration
business:
  maxTransactionAmount: "1000"
  batchSize: "5"
```

> **Important** : Toutes les valeurs YAML doivent être des chaînes de caractères (entre guillemets). Les booléens et nombres doivent aussi être en chaînes : `"true"`, `"5"`, etc.

**Secrets en production** : Utiliser Secure Properties
```yaml
security:
  client:
    id: "${secure::client.id}"
    secret: "${secure::client.secret}"
```

---

## 🚀 Utilisation

### 🎨 Console APIkit

Accédez à la console interactive : **`http://localhost:8082/console`**

La console permet de :
- Visualiser la documentation API complète
- Tester tous les endpoints interactivement
- Voir les exemples de requêtes/réponses
- Valider les payloads selon le RAML

### Endpoints disponibles

**Console APIkit** : `http://localhost:8082/console` 
- Interface interactive pour tester l'API
- Documentation auto-générée depuis le RAML
- Exemples de requêtes/réponses

#### API Endpoints

```bash
# Health Check
GET http://localhost:8082/api/health

# Get Customers
GET http://localhost:8082/api/customers

# Create Customer
POST http://localhost:8082/api/customers
Content-Type: application/json

{
  "firstName": "Marie",
  "lastName": "Dupont",
  "email": "marie.dupont@example.com",
  "phone": "+33612345678"
}
```

#### Patterns Examples

```bash
# Scatter-Gather (appels parallèles)
GET http://localhost:8082/api/patterns/scatter-gather

# Content-Based Routing
POST http://localhost:8082/api/patterns/route-message
Content-Type: application/json

{"type": "PAYMENT", "amount": 150.50}

# Idempotent Filter
POST http://localhost:8082/api/patterns/idempotent
Content-Type: application/json

{"messageId": "MSG-001", "data": "test"}

# Batch Processing
POST http://localhost:8082/api/patterns/batch
Content-Type: application/json

{"items": [{"id": 1, "name": "Item 1"}, {"id": 2, "name": "Item 2"}]}
```

> **Note** : Dans DataWeave, `type` est un mot réservé. Utilisez `payload.'type'` (avec guillemets simples) pour y accéder.

---

## 🧪 Tests

```bash
# Exécuter tous les tests
mvn test

# Test spécifique
mvn test -Dtest=test-api-experience

# Avec couverture
mvn clean verify
```

**Couverture des tests :**
- ✅ Flows Experience/Process/System
- ✅ Cas de succès et d'erreur
- ✅ Validation des données
- ✅ Gestion des erreurs
- ✅ Mocking des dépendances externes

---

## 📚 Best Practices implémentées

### 1. Gestion des erreurs

**Error handler global réutilisable** avec format JSON uniforme :

```xml
<error-handler ref="global-api-error-handler"/>
```

Format de réponse d'erreur :
```json
{
  "error": {
    "code": "BAD_REQUEST",
    "message": "Description claire",
    "details": "Informations supplémentaires",
    "timestamp": "2024-11-07T14:30:00Z",
    "correlationId": "abc-123"
  }
}
```

### 2. Logging et traçabilité

**Correlation ID** automatique sur chaque requête :

```
[START] CorrelationId: abc-123 | Method: POST | Path: /customers
[PROCESS] CorrelationId: abc-123 | Processing customer data
[END] CorrelationId: abc-123 | StatusCode: 201 | Duration: 145ms
```

Header HTTP retourné : `X-Correlation-ID: abc-123`

### 3. Configuration externalisée

✅ Aucune valeur hardcodée  
✅ Propriétés par environnement  
✅ Secrets sécurisés en production  

```xml
<http:listener-connection host="${http.host}" port="${http.port}"/>
<db:connection url="${db.url}"/>
```

### 4. API-led Connectivity

**3 couches distinctes** avec responsabilités claires :
- **Experience** : Format client, agrégation
- **Process** : Logique métier, orchestration  
- **System** : Intégration backends

### 5. Validation

```xml
<flow-ref name="validate-input-subflow"/>
```

Validation à plusieurs niveaux :
- Validation structurelle (payload non vide)
- Validation métier (email valide, montants, etc.)
- Messages d'erreur descriptifs

### 6. Réutilisabilité

**Sub-flows communs** :
- `generate-correlation-id-subflow`
- `validate-input-subflow`
- `log-request-start-subflow`
- `log-request-end-subflow`
- `enrich-response-subflow`

---

## 🎨 Patterns MuleSoft

### Scatter-Gather
Appelle plusieurs services en **parallèle** et agrège les résultats.

**Cas d'usage** : Récupérer données client + commandes + points fidélité simultanément

### Content-Based Routing  
Route les messages vers différents processeurs selon leur **contenu**.

**Cas d'usage** : Traitement différent selon type de transaction (PAYMENT, REFUND, TRANSFER)

### Idempotent Filter
Évite le traitement en **double** des messages.

**Cas d'usage** : Garantir qu'un message avec le même ID n'est traité qu'une fois

### Batch Processing
Traite les données par **lots** pour optimiser les performances.

**Cas d'usage** : Import de données en masse avec traitement par groupes

---

## 📦 Dépendances

| Composant | Version | Status |
|-----------|---------|--------|
| Mule Runtime | 4.10.0 | ✅ Stable |
| HTTP Connector | 1.10.0 | ✅ À jour |
| APIkit Module | 1.11.1 | ✅ À jour |
| Validation Module | 2.1.0 | ✅ À jour |
| MUnit Runner | 3.2.0 | ✅ À jour |
| MUnit Tools | 3.2.0 | ✅ À jour |

### Dépendances additionnelles recommandées

```xml
<!-- Database Connector -->
<dependency>
    <groupId>org.mule.connectors</groupId>
    <artifactId>mule-db-connector</artifactId>
    <version>1.14.9</version>
    <classifier>mule-plugin</classifier>
</dependency>

<!-- Salesforce Connector -->
<dependency>
    <groupId>com.mulesoft.connectors</groupId>
    <artifactId>mule-salesforce-connector</artifactId>
    <version>10.23.0</version>
    <classifier>mule-plugin</classifier>
</dependency>

<!-- Secure Properties -->
<dependency>
    <groupId>com.mulesoft.modules</groupId>
    <artifactId>mule-secure-configuration-property-module</artifactId>
    <version>1.2.7</version>
    <classifier>mule-plugin</classifier>
</dependency>
```

---

## 📤 Publication sur Exchange

### Via Anypoint Platform (Recommandé)

1. Aller sur https://anypoint.mulesoft.com → Exchange
2. Cliquer "Publish new asset" → Type : "Mule Application"
3. Uploader : `target/mule-best-practice-template-1.0.0-mule-application.jar`
4. Remplir : nom, description, tags
5. Publier

### Via Maven

```bash
mvn clean deploy -DskipTests
```

*Nécessite `distributionManagement` dans pom.xml et credentials dans `~/.m2/settings.xml`*

---

## 🚢 Déploiement

### CloudHub

```bash
mvn clean deploy -DmuleDeploy \
  -Dmule.env=prod \
  -Danypoint.username=<username> \
  -Danypoint.password=<password>
```

### Runtime Fabric

```bash
mvn clean deploy -DmuleDeploy \
  -Dmule.env=prod \
  -Dtarget=<target-name>
```

### On-Premise

```bash
# Build
mvn clean package -Pprod

# Deploy
cp target/*.jar $MULE_HOME/apps/
```

---

## 📋 Conventions de code

### Nommage des flows

```xml
<!-- Format: [layer]-[action]-[entity]-flow -->
<flow name="api-get-customers-flow">
<flow name="process-create-customer-flow">
<flow name="system-update-order-flow">
```

### Nommage des sub-flows

```xml
<!-- Format: [purpose]-subflow -->
<sub-flow name="validate-input-subflow">
<sub-flow name="generate-correlation-id-subflow">
```

### Variables

```xml
<!-- camelCase descriptif -->
<set-variable variableName="correlationId"/>
<set-variable variableName="customerData"/>
```

### DataWeave

```dataweave
%dw 2.0
output application/json

// Variables pour la lisibilité
var fullName = payload.firstName ++ " " ++ payload.lastName
var currentDate = now()

---
{
    customer: {
        name: fullName,
        registeredAt: currentDate
    },
    // Valeurs par défaut
    email: payload.email default "no-email@example.com",
    // Transformation conditionnelle
    status: if (payload.isActive) "ACTIVE" else "INACTIVE"
}
```

---

## 🔒 Sécurité

### Checklist sécurité

- ✅ Pas de secrets hardcodés
- ✅ Utilisation de Secure Properties (prod)
- ✅ Validation des inputs
- ✅ Pas de stacktrace en production
- ✅ Pas de données sensibles dans les logs
- ✅ Gestion appropriée des erreurs

### Configuration des secrets

**Développement** :
```yaml
security:
  client:
    secret: "dev-secret"
```

**Production** :
```yaml
security:
  client:
    secret: "${secure::client.secret}"
```

---

## 🤝 Contribuer

### Process de contribution

1. **Fork** le projet
2. **Créer une branche** : `git checkout -b feature/ma-fonctionnalite`
3. **Commiter** : `git commit -m "feat: Description"`
4. **Tester** : `mvn clean test`
5. **Push** : `git push origin feature/ma-fonctionnalite`
6. **Pull Request** avec description complète

### Format des commits

- `feat:` - Nouvelle fonctionnalité
- `fix:` - Correction de bug
- `docs:` - Documentation uniquement
- `test:` - Ajout/modification de tests
- `refactor:` - Refactoring
- `perf:` - Amélioration de performance

### Standards de code

- Suivre les conventions de nommage
- Documenter les flows complexes (`doc:name`)
- Ajouter des tests pour les nouvelles fonctionnalités
- Pas de valeurs hardcodées
- Mettre à jour la documentation

---

## 📊 Monitoring

### Correlation ID
Chaque requête génère un ID unique pour la traçabilité complète :
- Dans les logs
- Dans les headers de réponse (`X-Correlation-ID`)
- Dans les messages d'erreur

### Métriques
- Nombre de requêtes
- Temps de réponse moyen
- Taux d'erreur par type
- Throughput
- Durée de traitement

---

## 🆘 Troubleshooting

### Erreurs courantes et solutions

**1. Domain 'default' not found**
```bash
# Erreur: Domain 'default' has to be deployed
# Solution: Créer le domaine manually
mkdir -p $MULE_HOME/domains/default

# Créer mule-domain-config.xml dans ce répertoire:
cat > $MULE_HOME/domains/default/mule-domain-config.xml << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<domain:mule-domain
        xmlns="http://www.mulesoft.org/schema/mule/core"
        xmlns:domain="http://www.mulesoft.org/schema/mule/ee/domain"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
               http://www.mulesoft.org/schema/mule/core http://www.mulesoft.org/schema/mule/core/current/mule.xsd
               http://www.mulesoft.org/schema/mule/ee/domain http://www.mulesoft.org/schema/mule/ee/domain/current/mule-domain-ee.xsd">
</domain:mule-domain>
EOF
```

**2. Couldn't find configuration property value for key ${env}**
```bash
# Erreur: PropertyNotFoundException pour ${env}
# Solution: Ajouter la propriété env dans les fichiers YAML

# Dans config.local.yaml, config.dev.yaml, config.prod.yaml :
env: "local"  # ou "dev" ou "prod"
```

**3. Invalid field name identifier: The name `type` is a reserved word**
```bash
# Erreur: type est un mot réservé en DataWeave
# Solution: Utiliser des guillemets simples pour échapper le nom

# ❌ Incorrect:
payload.type

# ✅ Correct:
payload.'type'
```

**4. YAML configuration properties only supports string values**
```bash
# Erreur: Les valeurs booléennes/numériques ne sont pas supportées
# Solution: Mettre toutes les valeurs entre guillemets dans les fichiers YAML

# ❌ Incorrect:
correlationIdEnabled: true
port: 8082

# ✅ Correct:
correlationIdEnabled: "true"
port: "8082"
```

**5. Tests échouent**
```bash
# Nettoyer et rebuilder
mvn clean install
# Vérifier les dépendances
mvn dependency:tree
```

**6. Application deployed mais ne répond pas**
```bash
# Vérifier les logs
tail -f $MULE_HOME/logs/mule_ee.log

# Vérifier le port
netstat -an | grep 8082

# Forcer un rebuild et redéploiement
mvn clean package -DskipTests
cp target/*.jar $MULE_HOME/apps/
```

---

## 📚 Ressources

### Documentation MuleSoft
- [MuleSoft Docs](https://docs.mulesoft.com/)
- [API-led Connectivity](https://www.mulesoft.com/resources/api/api-led-connectivity)
- [MUnit Documentation](https://docs.mulesoft.com/munit/)
- [DataWeave](https://docs.mulesoft.com/dataweave/)
- [Best Practices](https://docs.mulesoft.com/mule-runtime/latest/intro-mule-best-practices)

### Anypoint Platform
- [Anypoint Exchange](https://www.mulesoft.com/exchange/)
- [Anypoint Studio](https://www.mulesoft.com/platform/studio)
- [Runtime Manager](https://docs.mulesoft.com/runtime-manager/)

---

## 📝 Licence

Ce projet est sous licence MIT.

## 👥 Auteurs

**Kevin J. N.** - Template initial pour Jasmine Conseil

---

## ✅ Checklist avant production

- [ ] Tous les tests passent (`mvn test`)
- [ ] Configuration prod créée (`config.prod.yaml`) et sécurisée
- [ ] Secrets externalisés (Secure Properties)
- [ ] Logs configurés : `level: "INFO"` (pas de DEBUG en prod)
- [ ] Error handlers référencés partout
- [ ] Correlation ID implémenté et testé
- [ ] Documentation à jour (README, RAML)
- [ ] Performance testée (charge, stress)
- [ ] Monitoring configuré (Anypoint Monitoring)
- [ ] Plan de rollback défini
- [ ] Domaine Mule créé sur l'environnement cible
- [ ] Toutes les propriétés YAML sont des chaînes de caractères
- [ ] Mots réservés DataWeave correctement échappés (`payload.'type'`)
- [ ] Variables d'environnement correctement configurées

## 🎓 Leçons apprises

### Problèmes résolus lors du développement

1. **Domaine Mule manquant** : Le domaine "default" doit être créé manuellement dans le runtime
2. **Propriétés YAML** : Toutes les valeurs doivent être des chaînes (même booléens et nombres)
3. **Mots réservés DataWeave** : `type`, `if`, `else`, etc. doivent être échappés avec des guillemets simples
4. **Configuration circulaire** : La propriété `env` doit être définie dans les fichiers YAML, pas via `<global-property>`
5. **Cache du runtime** : Après des modifications, nettoyer et recompiler : `mvn clean package`

---

**💡 Ce template est un point de départ. Adaptez-le selon vos besoins !**

Pour toute question : ouvrez une issue sur le repository.
