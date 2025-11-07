# Dépendances du projet - Mule Best Practice Template

Ce document liste toutes les dépendances utilisées dans le projet avec leurs versions actuelles.

## 📦 Dépendances Mule Runtime

### Runtime
- **Mule Runtime** : `4.10.0` (dernière version stable au moment de la création)
- **Java** : JDK 17+ (compatible avec Java 21)

### Connecteurs et Modules

#### HTTP Connector
- **Artifact** : `mule-http-connector`
- **Version** : `1.10.0`
- **Usage** : Gestion des requêtes/réponses HTTP, appels REST
- **Status** : ✅ À jour pour Mule 4.10.0

#### APIkit Module
- **Artifact** : `mule-apikit-module`
- **Version** : `1.11.1`
- **Usage** : Support des APIs REST (RAML/OAS)
- **Status** : ✅ À jour

#### Validation Module
- **Artifact** : `mule-validation-module`
- **Version** : `2.1.0`
- **Usage** : Validation avancée des données
- **Status** : ✅ À jour

## 🧪 Dépendances de Test

### MUnit
- **munit-runner** : `3.2.0` (test runner)
- **munit-tools** : `3.2.0` (outils de test et mocking)
- **assertions (DataWeave)** : `1.2.1` (assertions avancées)
- **Status** : ✅ À jour pour Mule 4.10.0

## 🔧 Outils Maven

### Plugins
- **maven-clean-plugin** : `3.4.0`
- **maven-compiler-plugin** : `3.13.0`
- **mule-maven-plugin** : `4.5.2`

## 📊 Matrice de compatibilité

| Composant | Version Minimale | Version Recommandée | Version Actuelle |
|-----------|-----------------|---------------------|------------------|
| Java | JDK 17 | JDK 21 | JDK 17+ |
| Maven | 3.8.0 | 3.9.x | 3.8.x+ |
| Mule Runtime | 4.10.0 | 4.10.0 | 4.10.0 |
| HTTP Connector | 1.9.0 | 1.10.0 | 1.10.0 |
| APIkit | 1.10.0 | 1.11.1 | 1.11.1 |
| MUnit | 3.0.0 | 3.2.0 | 3.2.0 |

## 🔄 Mise à jour des dépendances

### Comment vérifier les mises à jour

```bash
# Vérifier les versions disponibles
mvn versions:display-dependency-updates

# Vérifier les plugins
mvn versions:display-plugin-updates
```

### Processus de mise à jour

1. **Consulter les Release Notes** de MuleSoft
2. **Tester en environnement DEV** avant production
3. **Mettre à jour pom.xml**
4. **Exécuter tous les tests** : `mvn clean test`
5. **Vérifier la compatibilité** avec le runtime

### Notes importantes

⚠️ **Attention** :
- Toujours vérifier la compatibilité des connecteurs avec votre version de Mule Runtime
- Les versions de MUnit doivent être compatibles avec le Runtime
- Tester les mises à jour dans un environnement non-production d'abord

## 📋 Dépendances additionnelles recommandées

Pour enrichir votre projet, vous pouvez ajouter :

### Database Connector
```xml
<dependency>
    <groupId>org.mule.connectors</groupId>
    <artifactId>mule-db-connector</artifactId>
    <version>1.14.9</version>
    <classifier>mule-plugin</classifier>
</dependency>
```

### VM Connector (Queues)
```xml
<dependency>
    <groupId>org.mule.connectors</groupId>
    <artifactId>mule-vm-connector</artifactId>
    <version>2.0.0</version>
    <classifier>mule-plugin</classifier>
</dependency>
```

### Object Store Connector
```xml
<dependency>
    <groupId>org.mule.connectors</groupId>
    <artifactId>mule-objectstore-connector</artifactId>
    <version>1.3.0</version>
    <classifier>mule-plugin</classifier>
</dependency>
```

### Secure Properties
```xml
<dependency>
    <groupId>com.mulesoft.modules</groupId>
    <artifactId>mule-secure-configuration-property-module</artifactId>
    <version>1.2.7</version>
    <classifier>mule-plugin</classifier>
</dependency>
```

### Salesforce Connector
```xml
<dependency>
    <groupId>com.mulesoft.connectors</groupId>
    <artifactId>mule-salesforce-connector</artifactId>
    <version>10.23.0</version>
    <classifier>mule-plugin</classifier>
</dependency>
```

## 🔗 Références

- [MuleSoft Connectors](https://www.mulesoft.com/exchange/?type=connector)
- [Anypoint Exchange](https://www.mulesoft.com/exchange/)
- [MUnit Documentation](https://docs.mulesoft.com/munit/)
- [Maven Repository](https://repository.mulesoft.org/nexus/content/repositories/releases/)

## 📝 Changelog des dépendances

### Version 1.0.0 (2024-11-07)
- ✅ Mule Runtime 4.10.0
- ✅ HTTP Connector 1.10.0
- ✅ APIkit 1.11.1
- ✅ Validation Module 2.1.0
- ✅ MUnit 3.2.0
- ✅ Maven Compiler Plugin 3.13.0
- ✅ Maven Clean Plugin 3.4.0
- ✅ Mule Maven Plugin 4.5.2

---

**Dernière mise à jour** : 2024-11-07  
**Mainteneur** : Kevin J. N.

