# 🚀 Démarrer et Tester le UserService

## ✅ Build Maven Réussi !

Le projet a été compilé avec succès. Vous êtes prêt à démarrer !

## 📋 Étapes Rapides

### 1️⃣ Démarrer les Services (3 terminaux)

#### Terminal 1 : Eureka Server
```bash
cd EurekaService\eureka\eureka
mvnw.cmd spring-boot:run
```
**Attendez** : `Started EurekaApplication` (environ 30 secondes)

#### Terminal 2 : Authentication Service
```bash
cd AuthentificationService\Authentification\authentification
mvnw.cmd spring-boot:run
```
**Attendez** : `Started AuthentificationApplication`

#### Terminal 3 : User Service
```bash
cd UserService\user-service
mvnw.cmd spring-boot:run
```
**Attendez** : 
- `Started UserServiceApplication`
- `Administrateur par défaut créé : admin@plannora.com / admin123`

### 2️⃣ Vérifier que tout fonctionne

Ouvrez votre navigateur :
- **Eureka** : http://localhost:8761
  - Vous devriez voir `USER-SERVICE` et `AUTHENTIFICATION-SERVICE`

### 3️⃣ Tester avec le Script PowerShell

Dans un nouveau terminal PowerShell :

```powershell
cd UserService
.\test-userservice.ps1
```

Ce script va automatiquement :
1. ✅ Vérifier que les services sont démarrés
2. ✅ Se connecter en tant qu'admin
3. ✅ Créer des enseignants
4. ✅ Lister les utilisateurs
5. ✅ Modifier un utilisateur
6. ✅ Tester la sécurité

### 4️⃣ Tester Manuellement avec PowerShell

Si vous préférez tester manuellement :

```powershell
# 1. Se connecter
$body = @{
    email = "admin@plannora.com"
    password = "admin123"
} | ConvertTo-Json

$response = Invoke-RestMethod -Uri "http://localhost:8082/api/auth/login" `
    -Method Post `
    -ContentType "application/json" `
    -Body $body

$token = $response.token
Write-Host "Token : $token"

# 2. Créer un enseignant
$headers = @{
    Authorization = "Bearer $token"
    "Content-Type" = "application/json"
}

$enseignant = @{
    email = "prof@plannora.com"
    mdp = "password123"
    nomUser = "Dupont"
    prenomUser = "Jean"
    telephone = "0612345678"
    specialite = "Informatique"
    departement = "Génie Logiciel"
} | ConvertTo-Json

$response = Invoke-RestMethod -Uri "http://localhost:8083/api/utilisateurs/enseignant" `
    -Method Post `
    -Headers $headers `
    -Body $enseignant

$response | ConvertTo-Json

# 3. Lister les utilisateurs
$response = Invoke-RestMethod -Uri "http://localhost:8083/api/utilisateurs" `
    -Method Get `
    -Headers @{ Authorization = "Bearer $token" }

$response | ConvertTo-Json
```

### 5️⃣ Tester avec Postman

1. **Importer** le fichier `test-user-api.http` dans Postman
2. **Ou suivre** le guide dans `GUIDE-TESTS-POSTMAN.md`

## 🎯 Résultats Attendus

Après les tests, vous devriez avoir :

**Dans MySQL** :
```sql
mysql -u root -p plannoradb

SELECT * FROM utilisateurs;
-- 3 utilisateurs : 1 admin + 2 enseignants

SELECT * FROM enseignants;
-- 2 enseignants avec specialite et departement
```

**Dans Eureka** (http://localhost:8761) :
- `USER-SERVICE` (port 8083)
- `AUTHENTIFICATION-SERVICE` (port 8082)

## ❌ Problèmes Courants

### Erreur : "Connection refused"
**Solution** : Vérifiez que les services sont démarrés dans l'ordre (Eureka → Auth → User)

### Erreur : "401 Unauthorized"
**Solution** : Vérifiez que le token JWT est valide et bien formaté

### Erreur : "Email déjà existant"
**Solution** : Normal si vous testez plusieurs fois. Utilisez un autre email ou supprimez les données de test

### Erreur : "Cannot connect to MySQL"
**Solution** : Vérifiez que MySQL est démarré et que la base `plannoradb` existe

## 📚 Documentation Complète

- **SCRIPT-TEST-COMPLET.md** : Guide détaillé des tests
- **GUIDE-TESTS-POSTMAN.md** : Tests avec Postman
- **EXEMPLES-CURL.md** : Tests avec cURL
- **README.md** : Documentation complète

## 🎉 C'est Tout !

Une fois les tests passés, votre UserService est **entièrement fonctionnel** ! 🚀

Vous pouvez maintenant :
- Créer des utilisateurs (Admin et Enseignants)
- Gérer les utilisateurs (CRUD)
- Intégrer avec les autres services
- Développer le frontend

**Bon développement !** 💻
