# Rapport de TP sur Redis

## Introduction

L'objectif de ce TP est de réaliser les manipulations décrites dans les trois vidéos sur Redis, tout en les expliquant en détail. Ce rapport vise à clarifier chaque manipulation et à utiliser des figures et/ou des schémas pour appuyer les propos. À la fin de ce rapport, le lecteur doit être en mesure de comprendre comment installer et utiliser Redis, ainsi que de situer Redis parmi les quatre familles de systèmes de gestion de bases de données NoSQL vues en cours.

## Les Familles de Systèmes de Gestion de Bases de Données NoSQL

Avant de présenter Redis, il est important de comprendre les quatre familles de systèmes de gestion de bases de données NoSQL :

1. **Bases de Données Clé-Valeur** :
   - **Description** : Ces bases de données stockent les données sous forme de paires clé-valeur. Chaque clé est unique et est associée à une valeur.
   - **Exemples** : Redis, DynamoDB.

2. **Bases de Données Documentaires** :
   - **Description** : Ces bases de données stockent les données sous forme de documents, souvent au format JSON ou BSON. Chaque document peut avoir une structure différente.
   - **Exemples** : MongoDB, CouchDB.

3. **Bases de Données Colonnes** :
   - **Description** : Ces bases de données stockent les données en colonnes plutôt qu'en lignes. Elles sont optimisées pour les opérations de lecture et d'écriture sur de grandes quantités de données.
   - **Exemples** : Cassandra, HBase.

4. **Bases de Données Orientées Graphes** :
   - **Description** : Ces bases de données sont conçues pour stocker et manipuler des données sous forme de graphes, avec des nœuds, des arêtes et des propriétés.
   - **Exemples** : Neo4j, ArangoDB.

## Présentation de Redis

Redis (Remote Dictionary Server) est une base de données clé-valeur open-source, écrite en C, qui stocke les données en mémoire (RAM) pour permettre des accès rapides. Elle est souvent utilisée comme un cache ou une base de données temporaire en raison de sa vitesse et de sa flexibilité.

### Installation de Redis

Pour installer Redis, suivez ces étapes :

1. **Téléchargement** :
   - Téléchargez la dernière version de Redis depuis le site officiel : [Redis Download](https://redis.io/download).

2. **Compilation** :
   - Extrayez le fichier téléchargé et compilez Redis en utilisant les commandes suivantes :
     ```bash
     tar xzf redis-stable.tar.gz
     cd redis-stable
     make
     ```

3. **Démarrage du Serveur** :
   - Démarrez le serveur Redis en utilisant la commande suivante :
     ```bash
     src/redis-server
     ```

4. **Connexion au Serveur** :
   - Utilisez l'utilitaire de ligne de commande (CLI) pour vous connecter au serveur Redis :
     ```bash
     src/redis-cli
     ```

### Manipulations de Base

#### Définir et Lire une Clé

- **Définir une clé** :
  ```plaintext
  SET mots "bonjour"
  ```
- **Lire une clé** :
  ```plaintext
  GET mots
  ```

#### Supprimer une Clé

- **Supprimer une clé** :
  ```plaintext
  DEL mots
  ```

#### Exemple Pratique : Compteur de Visiteurs

- **Incrémenter et Décrémenter une valeur** :
  ```plaintext
  SET 1mars 0
  INCR 1mars
  DECR 1mars
  ```

#### Durée de Vie des Clés

- **Définir une durée de vie** :
  ```plaintext
  EXPIRE maCle 10
  ```
- **Vérifier la durée de vie** :
  ```plaintext
  TTL maCle
  ```

### Structures de Données Avancées

#### Listes

- **Définir une liste** :
  ```plaintext
  LPUSH mesCours "BDA"
  RPUSH mesCours "Services Web"
  ```
- **Afficher les éléments d'une liste** :
  ```plaintext
  LRANGE mesCours 0 -1
  ```
- **Supprimer des éléments** :
  ```plaintext
  LPOP mesCours
  RPOP mesCours
  ```

#### Ensembles (Sets)

- **Définir un ensemble** :
  ```plaintext
  SADD utilisateurs "Augustin" "Inès" "Samir" "Marc"
  ```
- **Afficher les éléments d'un ensemble** :
  ```plaintext
  SMEMBERS utilisateurs
  ```
- **Supprimer un élément** :
  ```plaintext
  SREM utilisateurs "Marc"
  ```
- **Union d'ensembles** :
  ```plaintext
  SUNION utilisateurs autresUtilisateurs
  ```

### Pub/Sub (Publication/Souscription)

- **Souscrire à un canal** :
  ```plaintext
  SUBSCRIBE mesCours
  ```
- **Publier un message** :
  ```plaintext
  PUBLISH mesCours "Nouveau cours sur MongoDB"
  ```
- **Souscrire à plusieurs canaux** :
  ```plaintext
  PSUBSCRIBE mes*
  ```

### Bases de Données Multiples

- **Changer de base de données** :
  ```plaintext
  SELECT 1
  ```
- **Afficher les clés d'une base de données** :
  ```plaintext
  KEYS *
  ```

## Fonctionnalités Supplémentaires de Redis

En plus des fonctionnalités décrites dans les vidéos, Redis offre plusieurs autres fonctionnalités intéressantes :

1. **Transactions** :
   - Redis permet d'exécuter des transactions atomiques en utilisant les commandes `MULTI`, `EXEC`, `DISCARD`, et `WATCH`.

2. **Scripts Lua** :
   - Redis permet d'exécuter des scripts Lua pour des opérations complexes, ce qui permet de réduire la latence et d'améliorer les performances.

3. **Réplication** :
   - Redis supporte la réplication maître-esclave, permettant de créer des copies de la base de données pour la redondance et la haute disponibilité.

4. **Clustering** :
   - Redis Cluster permet de partitionner les données sur plusieurs nœuds, offrant une solution évolutive pour les grandes bases de données.

## Conclusion

Redis est une base de données NoSQL clé-valeur puissante et flexible, idéale pour les applications nécessitant des accès rapides et une haute disponibilité. En suivant les manipulations décrites dans ce rapport, vous devriez être en mesure de comprendre comment installer, configurer et utiliser Redis pour diverses applications. N'oubliez pas de soumettre votre travail sur votre dépôt Git et de répondre aux questions posées à la fin de la vidéo n°3.

## Réponse à la Question de la Vidéo n°3

**Question** : Expliquer au moins quatre fonctionnalités, en plus de celles décrites dans les vidéos.

**Réponse** :
1. **Transactions** : Redis permet d'exécuter des transactions atomiques pour garantir la cohérence des données.
2. **Scripts Lua** : Redis permet d'exécuter des scripts Lua pour des opérations complexes, améliorant ainsi les performances.
3. **Réplication** : Redis supporte la réplication maître-esclave pour la redondance et la haute disponibilité.
4. **Clustering** : Redis Cluster permet de partitionner les données sur plusieurs nœuds, offrant une solution évolutive pour les grandes bases de données.

---
