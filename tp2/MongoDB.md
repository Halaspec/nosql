## TP MongoDB

### Fait à des fins pédagogiques

### Introduction à MongoDB pour les débutants

MongoDB est une base de données NoSQL orientée documents, ce qui signifie qu'elle stocke les données sous forme de documents JSON-like appelés BSON (Binary JSON). Contrairement aux bases de données relationnelles traditionnelles qui utilisent des tables et des lignes, MongoDB utilise des collections et des documents. Cette structure flexible permet de stocker des données complexes et hiérarchiques de manière plus naturelle.

#### Principaux concepts de MongoDB :

1. **Document** : Un document est une unité de données dans MongoDB, similaire à un enregistrement dans une base de données relationnelle. Les documents sont stockés sous forme de BSON.

2. **Collection** : Une collection est un ensemble de documents. Elle est similaire à une table dans une base de données relationnelle.

3. **Base de données** : Une base de données contient plusieurs collections. MongoDB peut gérer plusieurs bases de données.

4. **Schema** : Contrairement aux bases de données relationnelles, MongoDB n'impose pas de schéma fixe. Les documents dans une même collection peuvent avoir des structures différentes.

#### Avantages de MongoDB :

- **Flexibilité** : La structure flexible des documents permet de stocker des données complexes et hiérarchiques.
- **Scalabilité** : MongoDB est conçu pour être facilement scalable horizontalement.
- **Performance** : Les opérations de lecture et d'écriture sont rapides grâce à l'utilisation de BSON et à l'indexation.

#### Installation de MongoDB :

Pour installer MongoDB, vous pouvez suivre les instructions sur le site officiel de MongoDB : [MongoDB Installation Guide](https://docs.mongodb.com/manual/installation/).

#### Utilisation de MongoDB :

Une fois MongoDB installé, vous pouvez utiliser le shell MongoDB pour interagir avec la base de données. Le shell MongoDB est un outil en ligne de commande qui permet d'exécuter des requêtes et des opérations sur la base de données.

### Exercices Pratiques

## 1. Afficher tous les films d'action réalisés en France
```javascript
db.films.find({ genre: "Action", country: "FR" });
```

## 2. Afficher le nombre de films d'action
```javascript
db.films.countDocuments({ genre: "Action" });
```

## 3. Afficher les films d'action réalisés en France en 1963
```javascript
db.films.find({ genre: "Action", country: "FR", year: 1963 });
```

## 4. Afficher les films d'action réalisés en France sans grades
```javascript
db.films.find({ genre: "Action", country: "FR" }, { grades: 0 });
```

## 5. Afficher les films d'action réalisés en France sans identifiants
```javascript
db.films.find({ genre: "Action", country: "FR" }, { _id: 0 });
```

## 6. Afficher les titres et grades des films d'action réalisés en France
```javascript
db.films.find({ genre: "Action", country: "FR" }, { title: 1, grades: 1, _id: 0 });
```

## 7. Afficher les films d'action réalisés en France ayant une note supérieure à 10
```javascript
db.films.find({ genre: "Action", country: "FR", "grades.note": { $gt: 10 } }, { title: 1, grades: 1, _id: 0 });
```

## 8. Afficher les films d'action réalisés en France avec toutes les notes supérieures à 10
```javascript
db.films.find({ genre: "Action", country: "FR", grades: { $not: { $elemMatch: { note: { $lt: 10 } } } } }, { title: 1, grades: 1, _id: 0 });
```

## 9. Afficher les genres distincts
```javascript
db.films.distinct("genre");
```

## 10. Afficher les grades distincts
```javascript
db.films.distinct("grades");
```

## 11. Afficher les films sans résumé
```javascript
db.films.find({ summary: "" });
```

## 12. Afficher les films joués par Leonardo DiCaprio en 1997
```javascript
db.films.find({ "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo", year: 1997 });
```

## 13. Afficher les films tournés avec Leonardo DiCaprio ou en 1997
```javascript
db.films.find({ $or: [ { "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo" }, { year: 1997 } ] });
```
