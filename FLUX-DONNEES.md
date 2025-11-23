# 🔄 Flux de Données - UserService

Ce document illustre les flux de données dans le UserService.

## 📊 Vue d'Ensemble

```
Client (Postman)
      ↓
   Gateway (8080)
      ↓
Authentication Service (8082) → JWT Token
      ↓
User Service (8083)
      ↓
MySQL (plannoradb)
```

## 🔐 Flux d'Authentification

### Étape 1 : Login

```
┌─────────┐                    ┌──────────────┐                    ┌─────────┐
│ Client  │                    │ Auth Service │                    │  MySQL  │
└────┬────┘                    └──────┬───────┘                    └────┬────┘
     │                                │                                 │
     │ POST /api/auth/login           │                                 │
     │ {email, password}              │                                 │
     ├───────────────────────────────>│                                 │
     │                                │                                 │
     │                                │ SELECT * FROM utilisateurs      │
     │                                │ WHERE email = ?                 │
     │                                ├────────────────────────────────>│
     │                                │                                 │
     │                                │ Utilisateur trouvé              │
     │                                │<────────────────────────────────┤
     │                                │                                 │
     │                                │ Vérifier BCrypt                 │
     │                                │ Générer JWT Token               │
     │                                │                                 │
     │ 200 OK                         │                                 │
     │ {token, userId, role}          │                                 │
     │<───────────────────────────────┤                                 │
     │                                │                                 │
```

### Étape 2 : Utilisation du Token

```
Client stocke le token JWT :
{
  "sub": "admin001",
  "role": "ADMIN",
  "iat": 1234567890,
  "exp": 1234654290
}
```

## 👤 Flux de Création d'Utilisateur

### Créer un Enseignant

```
┌─────────┐         ┌──────────────┐         ┌─────────────┐         ┌─────────┐
│ Client  │         │ JWT Filter   │         │ Controller  │         │  MySQL  │
└────┬────┘         └──────┬───────┘         └──────┬──────┘         └────┬────┘
     │                     │                        │                     │
     │ POST /api/utilisateurs/enseignant           │                     │
     │ Authorization: Bearer TOKEN                 │                     │
     │ Body: EnseignantDTO                         │                     │
     ├────────────────────>│                        │                     │
     │                     │                        │                     │
     │                     │ Valider Token          │                     │
     │                     │ Extraire userId, role  │                     │
     │                     │                        │                     │
     │                     │ SecurityContext        │                     │
     │                     │ (userId=admin001,      │                     │
     │                     │  role=ADMIN)           │                     │
     │                     │                        │                     │
     │                     ├───────────────────────>│                     │
     │                     │                        │                     │
     │                     │                        │ @PreAuthorize       │
     │                     │                        │ hasRole('ADMIN')    │
     │                     │                        │ ✓ OK                │
     │                     │                        │                     │
     │                     │                        │ @Valid              │
     │                     │                        │ Valider DTO         │
     │                     │                        │ ✓ OK                │
     │                     │                        │                     │
     │                     │                        │ Service.creer()     │
     │                     │                        │                     │
     │                     │                        │ Vérifier email      │
     │                     │                        │ unique              │
     │                     │                        ├────────────────────>│
     │                     │                        │                     │
     │                     │                        │ Email OK            │
     │                     │                        │<────────────────────┤
     │                     │                        │                     │
     │                     │                        │ Hash password       │
     │                     │                        │ (BCrypt)            │
     │                     │                        │                     │
     │                     │                        │ INSERT utilisateurs │
     │                     │                        ├────────────────────>│
     │                     │                        │                     │
     │                     │                        │ INSERT enseignants  │
     │                     │                        ├────────────────────>│
     │                     │                        │                     │
     │                     │                        │ OK                  │
     │                     │                        │<────────────────────┤
     │                     │                        │                     │
     │ 201 Created         │                        │                     │
     │ UtilisateurResponseDTO (sans mdp)           │                     │
     │<────────────────────┴────────────────────────┤                     │
     │                                              │                     │
```

## 📋 Flux de Consultation

### Lister les Utilisateurs

```
┌─────────┐         ┌──────────────┐         ┌─────────────┐         ┌─────────┐
│ Client  │         │ JWT Filter   │         │ Service     │         │  MySQL  │
└────┬────┘         └──────┬───────┘         └──────┬──────┘         └────┬────┘
     │                     │                        │                     │
     │ GET /api/utilisateurs                        │                     │
     │ Authorization: Bearer TOKEN                  │                     │
     ├────────────────────>│                        │                     │
     │                     │                        │                     │
     │                     │ Valider Token          │                     │
     │                     │ role=ADMIN ✓           │                     │
     │                     │                        │                     │
     │                     ├───────────────────────>│                     │
     │                     │                        │                     │
     │                     │                        │ findAll()           │
     │                     │                        ├────────────────────>│
     │                     │                        │                     │
     │                     │                        │ SELECT u.*          │
     │                     │                        │ FROM utilisateurs u │
     │                     │                        │                     │
     │                     │                        │ List<Utilisateur>   │
     │                     │                        │<────────────────────┤
     │                     │                        │                     │
     │                     │                        │ Map to DTO          │
     │                     │                        │ (sans mdp)          │
     │                     │                        │                     │
     │ 200 OK              │                        │                     │
     │ List<UtilisateurResponseDTO>                 │                     │
     │<────────────────────┴────────────────────────┤                     │
     │                                              │                     │
```

## 🔄 Flux de Modification

### Mettre à Jour un Utilisateur

```
┌─────────┐         ┌─────────────┐         ┌─────────┐
│ Client  │         │ Service     │         │  MySQL  │
└────┬────┘         └──────┬──────┘         └────┬────┘
     │                     │                     │
     │ PUT /api/utilisateurs/{id}                │
     │ Body: {nomUser, prenomUser}               │
     ├────────────────────>│                     │
     │                     │                     │
     │                     │ findById(id)        │
     │                     ├────────────────────>│
     │                     │                     │
     │                     │ Utilisateur         │
     │                     │<────────────────────┤
     │                     │                     │
     │                     │ Modifier attributs  │
     │                     │                     │
     │                     │ save()              │
     │                     ├────────────────────>│
     │                     │                     │
     │                     │ UPDATE utilisateurs │
     │                     │ SET nom_user=?,     │
     │                     │     prenom_user=?   │
     │                     │ WHERE id_user=?     │
     │                     │                     │
     │                     │ OK                  │
     │                     │<────────────────────┤
     │                     │                     │
     │ 200 OK              │                     │
     │ UtilisateurResponseDTO                    │
     │<────────────────────┤                     │
     │                     │                     │
```

## 🗑️ Flux de Suppression

### Supprimer un Utilisateur

```
┌─────────┐         ┌─────────────┐         ┌─────────┐
│ Client  │         │ Service     │         │  MySQL  │
└────┬────┘         └──────┬──────┘         └────┬────┘
     │                     │                     │
     │ DELETE /api/utilisateurs/{id}             │
     ├────────────────────>│                     │
     │                     │                     │
     │                     │ existsById(id)      │
     │                     ├────────────────────>│
     │                     │                     │
     │                     │ true                │
     │                     │<────────────────────┤
     │                     │                     │
     │                     │ deleteById(id)      │
     │                     ├────────────────────>│
     │                     │                     │
     │                     │ DELETE FROM         │
     │                     │ enseignants         │
     │                     │ WHERE id_user=?     │
     │                     │                     │
     │                     │ DELETE FROM         │
     │                     │ utilisateurs        │
     │                     │ WHERE id_user=?     │
     │                     │                     │
     │                     │ OK                  │
     │                     │<────────────────────┤
     │                     │                     │
     │ 204 No Content      │                     │
     │<────────────────────┤                     │
     │                     │                     │
```

## 🔒 Flux de Sécurité

### Accès Refusé (403 Forbidden)

```
┌─────────┐         ┌──────────────┐         ┌─────────────┐
│ Client  │         │ JWT Filter   │         │ Controller  │
│(ENSEIGNANT)       └──────┬───────┘         └──────┬──────┘
└────┬────┘                │                        │
     │                     │                        │
     │ POST /api/utilisateurs                       │
     │ Authorization: Bearer TOKEN                  │
     │ (role=ENSEIGNANT)                            │
     ├────────────────────>│                        │
     │                     │                        │
     │                     │ Valider Token          │
     │                     │ role=ENSEIGNANT        │
     │                     │                        │
     │                     ├───────────────────────>│
     │                     │                        │
     │                     │                        │ @PreAuthorize
     │                     │                        │ hasRole('ADMIN')
     │                     │                        │ ✗ FAIL
     │                     │                        │
     │ 403 Forbidden       │                        │
     │ Access Denied       │                        │
     │<────────────────────┴────────────────────────┤
     │                                              │
```

### Token Invalide (401 Unauthorized)

```
┌─────────┐         ┌──────────────┐
│ Client  │         │ JWT Filter   │
└────┬────┘         └──────┬───────┘
     │                     │
     │ GET /api/utilisateurs
     │ Authorization: Bearer INVALID_TOKEN
     ├────────────────────>│
     │                     │
     │                     │ Valider Token
     │                     │ ✗ INVALID
     │                     │
     │ 401 Unauthorized    │
     │<────────────────────┤
     │                     │
```

## 💾 Flux Base de Données (Héritage JOINED)

### Insertion d'un Enseignant

```sql
-- Transaction 1 : Insérer dans la table parent
INSERT INTO utilisateurs (
    id_user, mdp, email, nom_user, 
    prenom_user, telephone, role
) VALUES (
    'uuid-123', '$2a$10$...', 'prof@plannora.com',
    'Dupont', 'Jean', '0612345678', 'ENSEIGNANT'
);

-- Transaction 2 : Insérer dans la table enfant
INSERT INTO enseignants (
    id_user, specialite, departement
) VALUES (
    'uuid-123', 'Informatique', 'Génie Logiciel'
);

-- COMMIT
```

### Requête d'un Enseignant

```sql
-- JPA génère automatiquement un JOIN
SELECT 
    u.id_user, u.mdp, u.email, u.nom_user,
    u.prenom_user, u.telephone, u.role,
    e.specialite, e.departement
FROM utilisateurs u
INNER JOIN enseignants e ON u.id_user = e.id_user
WHERE u.id_user = 'uuid-123';
```

### Suppression d'un Enseignant

```sql
-- Grâce à ON DELETE CASCADE
DELETE FROM utilisateurs WHERE id_user = 'uuid-123';

-- MySQL supprime automatiquement dans enseignants
-- grâce à la contrainte de clé étrangère
```

## 🔄 Transformation DTO ↔ Entity

### Requête → DTO → Entity

```
Client Request (JSON)
{
  "email": "prof@plannora.com",
  "mdp": "password123",
  "nomUser": "Dupont",
  "prenomUser": "Jean",
  "telephone": "0612345678",
  "specialite": "Informatique",
  "departement": "Génie Logiciel"
}
        ↓
    EnseignantDTO (validation)
        ↓
    Enseignant Entity
    - idUser: UUID généré
    - mdp: BCrypt hashé
    - email: prof@plannora.com
    - ...
        ↓
    MySQL (2 tables)
```

### Entity → DTO → Réponse

```
MySQL (2 tables)
        ↓
    Enseignant Entity
    - idUser: uuid-123
    - mdp: $2a$10$...
    - email: prof@plannora.com
    - ...
        ↓
    UtilisateurResponseDTO (sans mdp)
        ↓
Client Response (JSON)
{
  "idUser": "uuid-123",
  "email": "prof@plannora.com",
  "nomUser": "Dupont",
  "prenomUser": "Jean",
  "telephone": "0612345678",
  "role": "ENSEIGNANT"
}
```

## 📊 Résumé des Flux

| Opération | Méthode | Authentification | Autorisation | Base de Données |
|-----------|---------|------------------|--------------|-----------------|
| Login | POST | ✓ Credentials | - | SELECT |
| Créer | POST | ✓ JWT | ADMIN | INSERT (2 tables) |
| Lister | GET | ✓ JWT | ADMIN | SELECT |
| Obtenir | GET | ✓ JWT | ADMIN | SELECT + JOIN |
| Modifier | PUT | ✓ JWT | ADMIN | UPDATE |
| Supprimer | DELETE | ✓ JWT | ADMIN | DELETE (cascade) |

## 🎯 Points Clés

1. **JWT** : Tous les endpoints (sauf login) nécessitent un token JWT valide
2. **Rôles** : Seul l'ADMIN peut effectuer les opérations CRUD
3. **Héritage** : Stratégie JOINED = 2 tables (utilisateurs + enseignants/administrateurs)
4. **Sécurité** : Mots de passe hashés, pas de mdp dans les réponses
5. **Validation** : À tous les niveaux (DTO, Service, Base de données)

---

**Ce document illustre les principaux flux de données dans le UserService.** 🔄
