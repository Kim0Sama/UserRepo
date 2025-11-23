# Exemples de Requêtes cURL - UserService

Ce document contient des exemples de requêtes cURL pour tester le UserService.

## Prérequis

Remplacez `YOUR_TOKEN` par le token JWT obtenu après authentification.

## 1. Authentification

### Se connecter en tant qu'admin

```bash
curl -X POST http://localhost:8082/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@plannora.com",
    "password": "admin123"
  }'
```

**Réponse** :
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userId": "admin001",
  "role": "ADMIN"
}
```

**Copiez le token pour les requêtes suivantes !**

## 2. Créer des Utilisateurs

### Créer un enseignant

```bash
curl -X POST http://localhost:8083/api/utilisateurs/enseignant \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "jean.dupont@plannora.com",
    "mdp": "password123",
    "nomUser": "Dupont",
    "prenomUser": "Jean",
    "telephone": "0612345678",
    "specialite": "Informatique",
    "departement": "Génie Logiciel"
  }'
```

### Créer un autre enseignant

```bash
curl -X POST http://localhost:8083/api/utilisateurs/enseignant \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "marie.bernard@plannora.com",
    "mdp": "password123",
    "nomUser": "Bernard",
    "prenomUser": "Marie",
    "telephone": "0634567890",
    "specialite": "Mathématiques",
    "departement": "Sciences"
  }'
```

### Créer un administrateur

```bash
curl -X POST http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "sophie.martin@plannora.com",
    "mdp": "password123",
    "nomUser": "Martin",
    "prenomUser": "Sophie",
    "telephone": "0623456789",
    "role": "ADMIN"
  }'
```

## 3. Consulter les Utilisateurs

### Lister tous les utilisateurs (ADMIN uniquement)

```bash
curl -X GET http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Lister tous les enseignants

```bash
curl -X GET http://localhost:8083/api/utilisateurs/enseignants \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Obtenir un utilisateur par ID

```bash
curl -X GET http://localhost:8083/api/utilisateurs/admin001 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## 4. Modifier un Utilisateur

### Mettre à jour les informations d'un utilisateur

```bash
curl -X PUT http://localhost:8083/api/utilisateurs/admin001 \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "nomUser": "Admin",
    "prenomUser": "Principal",
    "telephone": "0611111111"
  }'
```

### Changer le mot de passe d'un utilisateur

```bash
curl -X PUT http://localhost:8083/api/utilisateurs/USER_ID \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "mdp": "nouveauMotDePasse123"
  }'
```

## 5. Supprimer un Utilisateur

```bash
curl -X DELETE http://localhost:8083/api/utilisateurs/USER_ID \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## 6. Tests de Sécurité

### Tenter d'accéder sans token (doit échouer)

```bash
curl -X GET http://localhost:8083/api/utilisateurs
```

**Réponse attendue** : `401 Unauthorized`

### Tenter d'accéder avec un token invalide (doit échouer)

```bash
curl -X GET http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer INVALID_TOKEN"
```

**Réponse attendue** : `401 Unauthorized`

## 7. Requêtes avec jq (formatage JSON)

Si vous avez `jq` installé, vous pouvez formater les réponses :

```bash
curl -X GET http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer YOUR_TOKEN" | jq
```

### Extraire uniquement les emails

```bash
curl -X GET http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer YOUR_TOKEN" | jq '.[].email'
```

### Compter le nombre d'utilisateurs

```bash
curl -X GET http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer YOUR_TOKEN" | jq 'length'
```

## 8. Script Bash Complet

Créez un fichier `test-user-service.sh` :

```bash
#!/bin/bash

# Couleurs pour l'affichage
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m' # No Color

echo "=== Test du UserService ==="

# 1. Authentification
echo -e "\n${GREEN}1. Authentification...${NC}"
AUTH_RESPONSE=$(curl -s -X POST http://localhost:8082/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@plannora.com",
    "password": "admin123"
  }')

TOKEN=$(echo $AUTH_RESPONSE | jq -r '.token')

if [ "$TOKEN" == "null" ]; then
  echo -e "${RED}Erreur d'authentification${NC}"
  exit 1
fi

echo -e "${GREEN}Token obtenu !${NC}"

# 2. Créer un enseignant
echo -e "\n${GREEN}2. Création d'un enseignant...${NC}"
curl -s -X POST http://localhost:8083/api/utilisateurs/enseignant \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test.enseignant@plannora.com",
    "mdp": "password123",
    "nomUser": "Test",
    "prenomUser": "Enseignant",
    "telephone": "0600000000",
    "specialite": "Test",
    "departement": "Test"
  }' | jq

# 3. Lister les utilisateurs
echo -e "\n${GREEN}3. Liste des utilisateurs...${NC}"
curl -s -X GET http://localhost:8083/api/utilisateurs \
  -H "Authorization: Bearer $TOKEN" | jq

echo -e "\n${GREEN}Tests terminés !${NC}"
```

Rendez-le exécutable et lancez-le :

```bash
chmod +x test-user-service.sh
./test-user-service.sh
```

## 9. PowerShell (Windows)

### Authentification

```powershell
$body = @{
    email = "admin@plannora.com"
    password = "admin123"
} | ConvertTo-Json

$response = Invoke-RestMethod -Uri "http://localhost:8082/api/auth/login" `
    -Method Post `
    -ContentType "application/json" `
    -Body $body

$token = $response.token
Write-Host "Token: $token"
```

### Créer un enseignant

```powershell
$headers = @{
    Authorization = "Bearer $token"
}

$body = @{
    email = "enseignant@plannora.com"
    mdp = "password123"
    nomUser = "Dupont"
    prenomUser = "Jean"
    telephone = "0612345678"
    specialite = "Informatique"
    departement = "Génie Logiciel"
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://localhost:8083/api/utilisateurs/enseignant" `
    -Method Post `
    -Headers $headers `
    -ContentType "application/json" `
    -Body $body
```

### Lister les utilisateurs

```powershell
Invoke-RestMethod -Uri "http://localhost:8083/api/utilisateurs" `
    -Method Get `
    -Headers $headers
```

## 10. Vérification dans MySQL

Après avoir créé des utilisateurs, vérifiez dans MySQL :

```sql
-- Voir tous les utilisateurs
SELECT * FROM utilisateurs;

-- Voir les enseignants avec leurs détails
SELECT u.id_user, u.email, u.nom_user, u.prenom_user, 
       e.specialite, e.departement
FROM utilisateurs u
JOIN enseignants e ON u.id_user = e.id_user;

-- Compter les utilisateurs par rôle
SELECT role, COUNT(*) as nombre
FROM utilisateurs
GROUP BY role;
```

## Astuces

1. **Sauvegarder le token** : Exportez-le dans une variable d'environnement
   ```bash
   export TOKEN="votre_token_ici"
   curl -H "Authorization: Bearer $TOKEN" ...
   ```

2. **Verbose mode** : Ajoutez `-v` pour voir les détails
   ```bash
   curl -v -X GET http://localhost:8083/api/utilisateurs ...
   ```

3. **Sauvegarder la réponse** : Redirigez vers un fichier
   ```bash
   curl ... > response.json
   ```

4. **Mesurer le temps** : Ajoutez `-w "\nTemps: %{time_total}s\n"`
   ```bash
   curl -w "\nTemps: %{time_total}s\n" ...
   ```
