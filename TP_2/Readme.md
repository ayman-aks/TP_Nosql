# MongoDB avec Docker

## Description

Ce document présente une procédure complète pour l'installation, l'importation des données et l'interrogation d'une base MongoDB en utilisant Docker. MongoDB est une base NoSQL puissante et flexible, permettant le stockage et la manipulation de données sous forme de documents JSON. Grâce à Docker, le déploiement de MongoDB devient plus simple et plus portable, garantissant un environnement stable et isolé.

## Introduction

MongoDB est une base de données NoSQL orientée documents, conçue pour stocker des données sous forme de documents JSON. Contrairement aux bases relationnelles, elle permet une structure flexible et évolutive, adaptée aux applications modernes nécessitant une gestion efficace des grandes quantités de données. 

L'utilisation de MongoDB avec Docker permet de simplifier son déploiement, d'assurer l'isolation du service et d'éviter les conflits de configuration avec d'autres applications sur la machine.

## Installation de MongoDB avec Docker

### Prérequis
- Docker installé sur la machine
- Docker Desktop configuré (pour Windows)
- Accès à une ligne de commande (PowerShell, Terminal, ou CMD)

### Étapes d'installation

1. **Télécharger et exécuter MongoDB avec Docker**

```sh
 docker run -d --name mongodb \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  -v mongodb_data:/data/db \
  mongo:latest
```

2. **Vérifier que MongoDB tourne**

```sh
 docker ps
```

Si MongoDB ne tourne pas, démarrez-le avec :
```sh
 docker start mongodb
```

3. **Se connecter au shell MongoDB**

```sh
 docker exec -it mongodb mongosh -u admin -p password
```

## Importation des données

1. **Copier le fichier `films.json` dans le conteneur**

```sh
 docker cp films.json mongodb:/films.json
```

2. **Importer les données dans MongoDB**

```sh
 docker exec -it mongodb mongoimport --db lesfilms --collection films --file /films.json --jsonArray --username admin --password password --authenticationDatabase admin
```

3. **Vérifier que les données ont été importées**

```sh
 docker exec -it mongodb mongosh -u admin -p password
```

Dans le shell MongoDB, exécutez :

```javascript
 use lesfilms
 db.films.countDocuments()
```

4. **Comprendre la structure d’un document**

Avant d'interroger la base de données `lesfilms`, il est essentiel de bien comprendre la structure d’un document dans la collection `films`. Pour ce faire, utilisez la commande suivante :

```javascript
 db.films.findOne()
```

Cela renverra un document échantillon de la collection, affichant ses attributs et leur structure.

## Requêtes MongoDB

5. **Afficher la liste des films d’action**
```javascript
 db.films.find({ genre: "Action" })
```

6. **Afficher le nombre de films d’action**
```javascript
 db.films.countDocuments({ genre: "Action" })
```

7. **Afficher les films d’action produits en France**
```javascript
 db.films.find({ genre: "Action", country: "FR" })
```

8. **Afficher les films d’action produits en France en 1963**
```javascript
 db.films.find({ genre: "Action", country: "FR", year: 1963 })
```

9. **Afficher les films d’action réalisés en France sans les grades**
```javascript
 db.films.find({ genre: "Action", country: "FR" }, { grades: 0 })
```

10. **Afficher les films d’action réalisés en France sans identifiants et sans grades**
```javascript
 db.films.find({ genre: "Action", country: "FR" }, { _id: 0, grades: 0 })
```

11. **Afficher les titres et les grades des films d’action réalisés en France sans leurs identifiants**
```javascript
 db.films.find({ genre: "Action", country: "FR" }, { _id: 0, title: 1, grades: 1 })
```

12. **Afficher les titres et les notes des films d’action réalisés en France ayant obtenu une note supérieure à 10**
```javascript
 db.films.find(
  { genre: "Action", country: "FR", "grades.note": { $gt: 10 } },
  { _id: 0, title: 1, "grades.note": 1 }
)
```

13. **Afficher uniquement les films ayant toutes leurs notes supérieures à 10**
```javascript
 db.films.find(
  { genre: "Action", country: "FR", grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } },
  { _id: 0, title: 1, "grades.note": 1 }
)
```

14. **Afficher les différents genres de films dans la base**
```javascript
 db.films.distinct("genre")
```

15. **Afficher les différents grades attribués**
```javascript
 db.films.distinct("grades.grade")
```

16. **Afficher tous les films dans lesquels joue au moins un des artistes suivants ["artist:4", "artist:18", "artist:11"]**
```javascript
 db.films.find(
  { "actors._id": { $in: ["artist:4", "artist:18", "artist:11"] } },
  { _id: 0, title: 1, actors: 1 }
)
```

17. **Afficher tous les films qui n’ont pas de résumé**
```javascript
 db.films.find(
  { summary: { $exists: false } },
  { _id: 0, title: 1 }
)
```

18. **Afficher tous les films tournés avec Leonardo DiCaprio en 1997**
```javascript
 db.films.find(
  { "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo", year: 1997 },
  { _id: 0, title: 1, year: 1, actors: 1 }
)
```

19. **Afficher les films tournés avec Leonardo DiCaprio ou en 1997**
```javascript
 db.films.find(
  { $or: [{ "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo" }, { year: 1997 }] },
  { _id: 0, title: 1, year: 1, actors: 1 }
)
```