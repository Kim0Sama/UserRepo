# Démarrage Rapide - UserService

## 🚀 Démarrage en 5 minutes

### 1. Prérequis

- ✅ Java 17 installé
- ✅ Maven installé
- ✅ MySQL en cours d'exécution
- ✅ Base de données `plannoradb` créée

### 2. Configuration MySQL

```sql
CREATE DATABASE IF NOT EXISTS plannoradb;
```

Si vous avez un mot de passe MySQL, modifiez `application.properties` :

```properties
spring.datasource.password=VOTRE_MOT_DE_PASSE
```

### 3. Démarrer les services dans l'ordre

#### a) Eureka Server (port 8761)
```bash
cd EurekaService/eureka/eureka
mvn spring-boot:run
```

Attendez que Eureka démarre (environ 30 secondes).

#### b) Authentication Service (port 8082)
```bash
cd AuthentificationService/Authentification/authentification
mvn spring-boot:run
```

#### c) User Service (port 8083)
```bash
cd UserService/user-service
mvn spring-boot:run
```

### 4. Vérifier que tout fonctionne

#### Vérifier Eureka
Ouvrez : http://localhost:8761

Vous devriez voir `USER-SERVICE` et `AUTHENTIFICATION-SERVICE` enregistrés.

#### Tester l'authentification

**Requête** :
```bash
curl -X POST http://localhost:8082/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@plannora.com",
    "password": "admin123"
  }'
```

**Réponse attendue** :
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userId": "admin001",
  "role": "ADMIN"
}
```

### 5. Premier test avec Postman

#### Étape 1 : Se connecter
```
POST http://localhost:8082/api/auth/login
Content-Type: application/json

{
  "email": "admin@plannora.com",
  "password": "admin123"
}
```

Copiez le `token` de la réponse.

#### Étape 2 : Créer un enseignant
```
POST http://localhost:8083/api/utilisateurs/enseignant
Authorization: Bearer VOTRE_TOKEN_ICI
Content-Type: application/json

{
  "email": "prof.dupont@plannora.com",
  "mdp": "password123",
  "nomUser": "Dupont",
  "prenomUser": "Jean",
  "telephone": "0612345678",
  "specialite": "Informatique",
  "departement": "Génie Logiciel"
}
```

#### Étape 3 : Lister les utilisateurs
```
GET http://localhost:8083/api/utilisateurs
Authorization: Bearer VOTRE_TOKEN_ICI
```

## 🎯 Endpoints Principaux

| Méthode | Endpoint | Rôle requis | Description |
|---------|----------|-------------|-------------|
| POST | `/api/utilisateurs` | ADMIN | Créer un utilisateur |
| POST | `/api/utilisateurs/enseignant` | ADMIN | Créer un enseignant |
| GET | `/api/utilisateurs` | ADMIN | Lister tous les utilisateurs |
| GET | `/api/utilisateurs/enseignants` | ADMIN/ENSEIGNANT | Lister les enseignants |
| GET | `/api/utilisateurs/{id}` | ADMIN | Obtenir un utilisateur |
| PUT | `/api/utilisateurs/{id}` | ADMIN | Modifier un utilisateur |
| DELETE | `/api/utilisateurs/{id}` | ADMIN | Supprimer un utilisateur |

## 🔐 Compte par Défaut

- **Email** : admin@plannora.com
- **Mot de passe** : admin123
- **Rôle** : ADMIN

## 📝 Exemples de Données

### Créer un enseignant
```json
{
  "email": "marie.bernard@plannora.com",
  "mdp": "password123",
  "nomUser": "Bernard",
  "prenomUser": "Marie",
  "telephone": "0634567890",
  "specialite": "Mathématiques",
  "departement": "Sciences"
}
```

### Créer un administrateur
```json
{
  "email": "admin2@plannora.com",
  "mdp": "password123",
  "nomUser": "Martin",
  "prenomUser": "Sophie",
  "telephone": "0623456789",
  "role": "ADMIN"
}
```

## ❌ Problèmes Courants

### Erreur : "Connection refused"
- Vérifiez que MySQL est démarré
- Vérifiez que le port 3306 est disponible

### Erreur : "401 Unauthorized"
- Vérifiez que le token JWT est valide
- Vérifiez que le header Authorization est bien formaté : `Bearer TOKEN`

### Erreur : "403 Forbidden"
- Vérifiez que vous êtes connecté en tant qu'ADMIN
- Seul l'ADMIN peut créer/modifier/supprimer des utilisateurs

### Erreur : "Service not found"
- Vérifiez qu'Eureka est démarré
- Attendez 30 secondes que les services s'enregistrent

## 📚 Documentation Complète

- `README.md` : Documentation complète du service
- `GUIDE-TESTS-POSTMAN.md` : Guide détaillé des tests
- `test-user-api.http` : Collection de requêtes HTTP

## 🎉 Prochaines Étapes

1. Testez tous les endpoints avec Postman
2. Créez plusieurs enseignants
3. Vérifiez les données dans MySQL
4. Intégrez avec le Gateway (port 8080)

## 💡 Astuces

- Utilisez l'extension REST Client de VS Code pour tester avec `test-user-api.http`
- Configurez une variable d'environnement dans Postman pour le token
- Consultez les logs pour déboguer : `mvn spring-boot:run`
