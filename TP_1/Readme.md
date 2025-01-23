### 4 Familles de Bases de Données NoSQL

1. **Bases de Données Clé-Valeur**
   - Stockage simple avec une clé unique
   - Exemple : Redis, DynamoDB
   - Performances élevées
   - Idéal pour le caching et le stockage de sessions

2. **Bases de Données Orientées Colonnes**
   - Optimisées pour les grands volumes de données
   - Exemple : Apache Cassandra, HBase
   - Adapté pour l'analyse de données volumineuses
   - Scalabilité horizontale efficace

3. **Bases de Données Orientées Documents**
   - Stockage de documents semi-structurés
   - Exemple : MongoDB, CouchDB
   - Flexibilité du schéma
   - Adapté aux applications avec des données complexes

4. **Bases de Données Orientées Graphes**
   - Modélisation des relations entre entités
   - Exemple : Neo4j, Amazon Neptune
   - Optimisé pour les requêtes relationnelles
   - Utilisé dans les réseaux sociaux, recommandations

## Redis : Présentation Détaillée

### Caractéristiques Principales
- Base de données in-memory
- Type : Clé-Valeur
- Haute performance
- Multiplateforme
- Open-source

### Architecture
- Stockage principal en mémoire
- Persistence optionnelle sur disque
- Supporte multiple structures de données
- Réplication et clustering intégrés

## Installation et Configuration

### Prérequis
- Docker
- Connexion Internet

### Commandes d'Installation
```bash
# Télécharger l'image Redis
docker pull redis

# Lancer le serveur Redis
docker run --name redis-server -p 6379:6379 -d redis

# Accéder au client Redis
docker run -it --network host --rm redis redis-cli -h localhost
```

## Structures de Données Redis

### 1. Strings (Clés-Valeurs)
```
# Créer/Modifier
SET user:1234 "Samir"

# Récupérer
GET user:1234

# Supprimer
DEL user:1234
```

### 2. Listes
```
# Ajouter des éléments
RPUSH mescours "BDA"

# Afficher
LRANGE mescours 0 -1

# Retirer
LPOP mescours
```

### 3. Ensembles (Sets)
```
# Ajouter des éléments
SADD utilisateurs "Augustin" "Ines"

# Lister
SMEMBERS utilisateurs

# Opérations
SUNION utilisateurs autresutilisateurs
```

### 4. Hachages (Hashes)
```
# Créer un hash utilisateur
HSET user:11 username Youssef age 31

# Récupérer un champ
HGET user:11 username
```

### 5. Pub/Sub
```
# S'abonner
SUBSCRIBE mes_cours

# Publier
PUBLISH mes_cours "Nouveau cours"
```

## Performances et Optimisations

### Temps d'Accès
- RAM : 10^-8 à 10^-7 secondes
- Disque Dur : 10^-2 secondes (10 000x plus lent)

### Stratégies
- Stockage principal en mémoire
- Rôle de cache
- Déport de données des bases relationnelles

## Fonctionnalités Avancées (Au-delà des Vidéos REDI1.2.3)

1. **Transactions**
   - Garantit l'exécution atomique de plusieurs commandes
   - Utilisation : `MULTI`, `EXEC`, `DISCARD`

2. **Lua Scripting**
   - Écriture de scripts côté serveur
   - Exécution atomique de logiques complexes

3. **Géo-indexation**
   - Stockage et requêtes de positions géographiques
   - Calcul de distances

4. **Streams**
   - Système de log avec accès par ID
   - Idéal pour les systèmes événementiels

## Conclusion
Redis offre une solution performante et flexible pour le stockage de données en mémoire, avec de multiples cas d'usage.

## Ressources Complémentaires
- Documentation officielle Redis
- REDIS1 REDIS2 REDIS3
