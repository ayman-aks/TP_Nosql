**TP4 : CouchDB et MapReduce**

## Présentation Générale

Apache CouchDB est une base de données NoSQL orientée documents qui permet de stocker des données sous format JSON. Conçu pour une gestion distribuée, CouchDB offre une réplication simple et efficace entre plusieurs instances. Il repose sur une API RESTful qui facilite l’interaction avec la base de données à l’aide des méthodes HTTP telles que GET, POST, PUT et DELETE.

Contrairement aux bases de données relationnelles classiques, CouchDB propose une structure flexible et sans schéma fixe. Chaque document est identifié de manière unique par un **_id** et contient une série de paires clé-valeur qui peuvent être structurées librement.

---

## Installation et Démarrage

L’installation de CouchDB peut être réalisée facilement via Docker. La commande suivante permet de déployer un conteneur CouchDB avec des identifiants d’administration définis :

```sh
docker run -d --name myCouchDB -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=pass123 -p 5984:5984 couchdb
```

Une fois le conteneur démarré, l’interface de gestion Fauxton est accessible à l’adresse :

```http
http://localhost:5984/_utils/
```

Pour vérifier que le serveur fonctionne correctement, on peut exécuter la commande suivante :

```sh
curl -X GET http://admin:pass123@localhost:5984/
```

---

## Manipulation des Données

### Création d’une Base de Données

```sh
curl -X PUT http://admin:pass123@localhost:5984/bibliotheque
```

### Insertion d’un Document

Un document peut être inséré dans une base de données avec la commande suivante :

```sh
curl -X PUT http://admin:pass123@localhost:5984/bibliotheque/livre1 -d '{"titre":"1984", "auteur":"George Orwell", "annee":1949}' -H "Content-Type: application/json"
```

### Récupération d’un Document

```sh
curl -X GET http://admin:pass123@localhost:5984/bibliotheque/livre1
```

### Insertion en Masse

Il est possible d’insérer plusieurs documents en une seule requête en utilisant `_bulk_docs` :

```json
{
  "docs": [
    {"_id": "livre2", "titre": "Fahrenheit 451", "auteur": "Ray Bradbury", "annee": 1953},
    {"_id": "livre3", "titre": "Brave New World", "auteur": "Aldous Huxley", "annee": 1932}
  ]
}
```

La commande pour envoyer ces données :

```sh
curl -X POST http://admin:pass123@localhost:5984/bibliotheque/_bulk_docs -d @livres.json -H "Content-Type: application/json"
```

---

## MapReduce et Vues CouchDB

CouchDB utilise MapReduce pour organiser et analyser les données. Une vue est composée d’une fonction **Map** (qui génère des paires clé-valeur) et éventuellement d’une fonction **Reduce** (qui regroupe et traite les résultats).

### Création d’une Vue : Comptage des Livres par Année

#### Fonction Map
```javascript
function(doc) {
  emit(doc.annee, 1);
}
```

#### Fonction Reduce
```javascript
function(keys, values) {
  return sum(values);
}
```

### Exécution de la Vue

Les vues sont stockées dans un design document. Pour créer une vue dans un document nommé `_design/analytics` :

```json
{
  "_id": "_design/analytics",
  "views": {
    "livres_par_annee": {
      "map": "function(doc) { emit(doc.annee, 1); }",
      "reduce": "_sum"
    }
  }
}
```

La requête suivante permet d’interroger cette vue :

```sh
curl -X GET http://admin:pass123@localhost:5984/bibliotheque/_design/analytics/_view/livres_par_annee
```

---

## Exercice Pratique : Manipulation des Données

### 1. Représentation des Pages Web et Liens

Les pages sont représentées sous forme de documents contenant des liens sortants avec des poids attribués :

```json
{
  "_id": "page_A",
  "liens": {
    "page_B": 0.3,
    "page_C": 0.7
  }
}
```

### 2. Calcul de la Norme d’un Vecteur

#### Fonction Map
```javascript
function(doc) {
  var somme = 0;
  for (var lien in doc.liens) {
    somme += Math.pow(doc.liens[lien], 2);
  }
  emit(doc._id, somme);
}
```

#### Fonction Reduce
```javascript
function(keys, values) {
  return Math.sqrt(sum(values));
}
```

### 3. Produit de la Matrice M avec un Vecteur W

#### Fonction Map
```javascript
function(doc) {
  var resultat = 0;
  for (var cible in doc.liens) {
    resultat += doc.liens[cible] * W[cible];
  }
  emit(doc._id, resultat);
}
```

#### Fonction Reduce
```javascript
function(keys, values) {
  return sum(values);
}
```

---

## Conclusion

CouchDB est une base de données documentaire puissante et flexible. Grâce à son approche orientée documents et à l’intégration de MapReduce, elle permet de structurer, indexer et analyser efficacement de grandes quantités de données. Son API RESTful simplifie son utilisation et son architecture distribuée en fait un choix idéal pour des applications évolutives et résilientes.

