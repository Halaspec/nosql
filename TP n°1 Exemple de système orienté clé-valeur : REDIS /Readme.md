Je vais ajouter une section complète sur l'utilisation de Redis avec Docker, qui est en effet une approche moderne et pratique.

# Utilisation de Redis avec Docker

## Pourquoi Docker ?
Docker nous permet de déployer Redis de manière isolée et reproductible, sans avoir à se soucier des dépendances ou des conflits avec d'autres applications. Cette approche est particulièrement utile pour :
- Le développement local
- Les environnements de test
- Le déploiement en production
- La gestion de plusieurs versions de Redis

## Installation Simple

### Image Officielle
```bash
# Télécharger l'image officielle Redis
docker pull redis

# Lancer un conteneur Redis
docker run --name mon-redis -d -p 6379:6379 redis
```

### Vérification du Fonctionnement
```bash
# Se connecter au conteneur Redis
docker exec -it mon-redis redis-cli

# Test simple
127.0.0.1:6379> ping
PONG
```

## Configuration Avancée avec Docker Compose

Pour une configuration plus élaborée, nous pouvons utiliser Docker Compose. Voici un exemple de fichier `docker-compose.yml` :

```yaml
version: '3.8'

services:
  redis:
    image: redis:latest
    container_name: mon-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
      - ./redis.conf:/usr/local/etc/redis/redis.conf
    command: redis-server /usr/local/etc/redis/redis.conf
    environment:
      - REDIS_PASSWORD=mot_de_passe_securise
    networks:
      - redis-network
    restart: unless-stopped

volumes:
  redis_data:
    driver: local

networks:
  redis-network:
    driver: bridge
```

### Fichier de Configuration Redis (redis.conf)
```conf
# Sécurité de base
requirepass mot_de_passe_securise
protected-mode yes

# Persistence
appendonly yes
appendfilename "appendonly.aof"

# Limites de mémoire
maxmemory 256mb
maxmemory-policy allkeys-lru
```

### Démarrage et Gestion
```bash
# Démarrer les services
docker-compose up -d

# Voir les logs
docker-compose logs redis

# Arrêter les services
docker-compose down
```

## Bonnes Pratiques avec Docker

### 1. Persistence des Données
Il est crucial de configurer correctement les volumes pour conserver les données même après un redémarrage du conteneur :
```yaml
volumes:
  - redis_data:/data  # Pour les données Redis
  - ./redis.conf:/usr/local/etc/redis/redis.conf  # Pour la configuration
```

### 2. Sécurité
- Utilisez toujours un mot de passe fort
- Limitez l'accès réseau
- Mettez régulièrement à jour l'image Docker

### 3. Monitoring
```bash
# Statistiques du conteneur
docker stats mon-redis

# Informations Redis
docker exec -it mon-redis redis-cli info
```

### 4. Sauvegarde et Restauration
```bash
# Sauvegarde
docker exec mon-redis redis-cli SAVE

# Copier le fichier dump.rdb
docker cp mon-redis:/data/dump.rdb ./backup/
```

## Configuration Multi-Conteneurs

Pour des applications plus complexes, vous pourriez vouloir connecter Redis à d'autres services. Voici un exemple avec une application web :

```yaml
version: '3.8'

services:
  redis:
    image: redis:latest
    container_name: mon-redis
    # ... configuration Redis ...

  webapp:
    build: ./app
    depends_on:
      - redis
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    ports:
      - "3000:3000"
```

Cette section Docker complète parfaitement le rapport précédent, offrant une approche moderne et conteneurisée pour le déploiement de Redis. Elle couvre les aspects essentiels tout en restant accessible aux débutants, avec suffisamment de détails pour une utilisation avancée.
