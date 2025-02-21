# Guide Complet : MapReduce avec CouchDB sous Docker

## Introduction

Ce guide vous permettra de comprendre le concept de MapReduce en utilisant CouchDB dans un environnement Docker. MapReduce est une technique de traitement de données qui se décompose en deux étapes principales : 
- Map : transformation et filtrage des données
- Reduce : agrégation des résultats

Imaginons que vous gérez une vidéothèque. MapReduce vous permettrait, par exemple, de compter facilement combien de films sont sortis chaque année ou de lister tous les films d'un réalisateur particulier.

## Installation de l'environnement

### Prérequis
- Docker installé sur votre machine
- curl installé pour les requêtes HTTP
- Un éditeur de texte
- Un terminal

### Démarrage de CouchDB avec Docker

Commençons par lancer CouchDB dans un conteneur Docker :

```bash
docker run -d --name couchdbdemo -e COUCHDB_USER=youcef -e COUCHDB_PASSWORD=samir -p 5984:5984 couchdb
```

Analysons cette commande :
- `docker run` : lance un nouveau conteneur
- `-d` : mode détaché (en arrière-plan)
- `--name couchdbdemo` : nom donné au conteneur
- `-e COUCHDB_USER=youcef` : définit le nom d'utilisateur admin
- `-e COUCHDB_PASSWORD=samir` : définit le mot de passe admin
- `-p 5984:5984` : fait correspondre le port 5984 du conteneur au port 5984 de l'hôte
- `couchdb` : image Docker à utiliser

## Vérification de l'Installation

Vérifions que CouchDB fonctionne correctement :

```bash
curl -X GET http://youcef:samir@localhost:5984/
```

Cette commande devrait retourner des informations sur votre installation CouchDB, confirmant que tout fonctionne correctement.

## Création et Manipulation de la Base de Données

### Création d'une Base de Données

Créons une base de données "films" :

```bash
curl -X PUT http://youcef:samir@localhost:5984/films
```

Vérifions sa création :

```bash
curl -X GET http://youcef:samir@localhost:5984/films
```

### Ajout de Documents

Ajoutons quelques documents pour tester :

```bash
# Ajout d'un premier document
curl -X PUT http://youcef:samir@localhost:5984/films/doc -d '{"cle":"valeur"}'

# Mise à jour du document
curl -X PUT http://youcef:samir@localhost:5984/films/doc -d '{"nom":"youcef"}'

# Ajout d'un nouveau document
curl -X PUT http://youcef:samir@localhost:5984/films/doc1 -d '{"nom":"youcef"}'
```

### Import de Données en Masse

Pour importer des données plus conséquentes :

```bash
# Import d'un seul film
curl -X POST http://youcef:samir@localhost:5984/exemple -d @movie:10098.json -H "Content-Type: application/json"

# Import en masse
curl -X POST http://youcef:samir@localhost:5984/films/_bulk_docs -d @films_couchdb.json -H "Content-Type: application/json"
```

## Utilisation de MapReduce

### Vue 1 : Films par Année

Cette première vue permet de compter le nombre de films par année.

Fonction Map :
```javascript
function (doc) {
    emit(doc.year, doc.title);
}
```
Cette fonction :
- Prend chaque document (doc) comme entrée
- Émet une paire clé-valeur où :
  - La clé est l'année du film (doc.year)
  - La valeur est le titre du film (doc.title)

Fonction Reduce :
```javascript
function (keys, values) {
    return values.length;
}
```
Cette fonction :
- Compte le nombre de films pour chaque année
- `values.length` retourne le nombre total de films pour une année donnée

### Vue 2 : Films par Réalisateur

Cette vue compte le nombre de films par réalisateur.

Fonction Map :
```javascript
function (doc) {
    emit(doc.director._id, doc.title);
}
```
Cette fonction :
- Émet le titre du film pour chaque identifiant de réalisateur
- Permet de grouper les films par réalisateur

Fonction Reduce :
```javascript
function (keys, values) {
    return values.length;
}
```
Cette fonction compte le nombre total de films par réalisateur.

### Vue 3 : Films par Acteur

Cette vue plus complexe associe les films à leurs acteurs.

```javascript
function(doc) {
    for (i = 0; i < doc.actors.length; i++) {
        emit({"prénom": doc.actors[i].first_name, "nom": doc.actors[i].last_name}, doc.title);
    }
}
```
Cette fonction :
- Parcourt la liste des acteurs de chaque film
- Émet le titre du film pour chaque acteur
- Crée une clé composite avec le prénom et le nom de l'acteur

## Utilisation des Vues

Pour utiliser ces vues, vous devez les créer dans CouchDB via l'interface Fauxton (accessible sur http://localhost:5984/_utils) ou via des requêtes curl.

Exemple de requête pour obtenir les films par année :
```bash
curl -X GET http://youcef:samir@localhost:5984/films/_design/films/_view/by_year?group=true
```

## Exercices Pratiques

1. Créez une vue qui liste les films sortis après 2000
2. Modifiez la vue des acteurs pour compter dans combien de films chaque acteur a joué
3. Créez une vue qui regroupe les films par genre

## Dépannage Courant

- Si vous ne pouvez pas vous connecter à CouchDB, vérifiez que le conteneur Docker est en cours d'exécution :
  ```bash
  docker ps | grep couchdbdemo
  ```
- En cas d'erreur d'authentification, vérifiez vos identifiants dans les URLs

## Conclusion

MapReduce avec CouchDB offre une approche puissante pour l'analyse de données de films. Les vues que nous avons créées permettent d'effectuer des requêtes complexes de manière efficace et évolutive.

Les concepts appris ici peuvent être appliqués à n'importe quel type de données, pas seulement les films. La clé est de bien réfléchir à la structure de vos fonctions map et reduce en fonction de vos besoins d'analyse.

## Ressources Additionnelles

- Documentation CouchDB : https://docs.couchdb.org/
- Documentation Docker : https://docs.docker.com/
- Guide des Vues CouchDB : https://guide.couchdb.org/draft/views.html


# Calcul Matriciel avec MapReduce et CouchDB

## 1. Modélisation des Documents

Pour représenter une matrice de liens entre pages web, nous devons créer une structure de document qui capture efficacement les relations et leurs poids. Inspirons-nous du PageRank de Google en adaptant le modèle pour CouchDB.

### Structure des Documents

```javascript
{
  "_id": "page_1",
  "type": "page",
  "url": "http://example.com/page1",
  "links": [
    {
      "target_page": "page_2",
      "weight": 0.5
    },
    {
      "target_page": "page_3",
      "weight": 0.3
    }
  ]
}
```

Expliquons cette structure :
- Chaque document représente une page (une ligne de la matrice M)
- `_id` : identifiant unique de la page
- `type` : permet de filtrer les documents de type "page"
- `url` : URL de la page (optionnel, pour référence)
- `links` : tableau des liens sortants avec leurs poids
  - `target_page` : identifiant de la page cible
  - `weight` : poids du lien (Mij dans la matrice)

Création de la base de données et insertion d'un exemple :

```bash
# Création de la base de données
curl -X PUT http://admin:password@localhost:5984/pagerank

# Insertion d'un document exemple
curl -X PUT http://admin:password@localhost:5984/pagerank/page_1 -H "Content-Type: application/json" -d '{
  "type": "page",
  "url": "http://example.com/page1",
  "links": [
    {"target_page": "page_2", "weight": 0.5},
    {"target_page": "page_3", "weight": 0.3}
  ]
}'
```

## 2. Calcul de la Norme des Vecteurs

Pour calculer la norme des vecteurs représentant chaque page, nous devons :
1. Extraire les poids des liens pour chaque page
2. Calculer la racine carrée de la somme des carrés

### Fonction Map
```javascript
function(doc) {
  if (doc.type === 'page') {
    var weights = doc.links.map(function(link) {
      return link.weight;
    });
    
    var sumSquares = weights.reduce(function(acc, weight) {
      return acc + (weight * weight);
    }, 0);
    
    emit(doc._id, sumSquares);
  }
}
```

### Fonction Reduce
```javascript
function(keys, values, rereduce) {
    return Math.sqrt(values[0]);
}
```

Création de la vue dans CouchDB :

```bash
curl -X PUT http://admin:password@localhost:5984/pagerank/_design/calculations -H "Content-Type: application/json" -d '{
  "views": {
    "vector_norms": {
      "map": "function(doc) { if (doc.type === \"page\") { var weights = doc.links.map(function(link) { return link.weight; }); var sumSquares = weights.reduce(function(acc, weight) { return acc + (weight * weight); }, 0); emit(doc._id, sumSquares); } }",
      "reduce": "function(keys, values, rereduce) { if (!rereduce) { return Math.sqrt(values[0]); } else { return values[0]; } }"
    }
  }
}'
```

## 3. Produit Matrice-Vecteur

Pour calculer le produit de la matrice M avec un vecteur W, nous devons :
1. Accéder au vecteur W en mémoire
2. Pour chaque ligne de M, calculer la somme des produits avec W

### Fonction Map
```javascript
function(doc) {
  if (doc.type === 'page') {
    var result = new Array(N).fill(0);

    doc.links.forEach(function(link) {
      var j = parseInt(link.target_page.replace('page_', '')) - 1;
      result[j] += link.weight * W[j];
    });

    emit(doc._id, result);
  }
}
```

### Fonction Reduce
```javascript
function(keys, values) {
    var finalResult = new Array(N).fill(0);

    values.forEach(function(value) {
      for (var i = 0; i < N; i++) {
        finalResult[i] += value[i];
      }
    });

    return finalResult;
}
```

Création de la vue dans CouchDB :

```bash
curl -X PUT http://admin:password@localhost:5984/pagerank/_design/calculations -H "Content-Type: application/json" -d '{
  "views": {
    "matrix_vector_product": {
      "map": "function(doc) { if (doc.type === \"page\") { var result = 0; doc.links.forEach(function(link) { var j = parseInt(link.target_page.replace(\"page_\", \"\")) - 1; result += link.weight * W[j]; }); emit(doc._id, result); } }",
      "reduce": "function(keys, values, rereduce) { if (!rereduce) { return sum(values); } else { return sum(values); } }"
    }
  }
}'
```
