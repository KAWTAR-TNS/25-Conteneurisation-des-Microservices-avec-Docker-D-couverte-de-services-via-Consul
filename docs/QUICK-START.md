# Guide de Démarrage Rapide - TP25

## � Informations

| Élément | Détail |
|---------|--------|
| **Auteur** | Hamza Aglagal |
| **Formation** | EMSI - 5ème année |
| **Module** | Architecture des Composants |

---

## �🚀 Démarrage en 5 minutes

### 1. Vérifier les prérequis

```bash
docker --version
docker compose version
```

### 2. Organiser la structure

```
TP25-Microservices/
├── docker-compose.yml           # À la racine
├── clientService/
│   ├── Dockerfile              # Copier depuis dockerfiles/
│   ├── pom.xml
│   └── src/
├── voitureService/
│   ├── Dockerfile              # Copier depuis dockerfiles/
│   ├── pom.xml
│   └── src/
└── gatewayService/
    ├── Dockerfile              # Copier depuis dockerfiles/
    ├── pom.xml
    └── src/
```

### 3. Lancer l'architecture

```bash
# Build et start
docker compose up -d --build

# Vérifier
docker compose ps
```

### 4. Accéder aux services

| Service | URL | Description |
|---------|-----|-------------|
| Consul UI | http://localhost:8500 | Découverte de services |
| phpMyAdmin | http://localhost:8081 | Gestion MySQL |
| Gateway | http://localhost:8888 | Point d'entrée |
| Client | http://localhost:8088 | Microservice Client |
| Voiture | http://localhost:8089 | Microservice Voiture |

---

## 🔧 Commandes essentielles

```bash
# Voir les logs
docker compose logs -f

# Logs d'un service
docker compose logs -f client-service

# Redémarrer un service
docker compose restart client-service

# Arrêter tout
docker compose stop

# Supprimer tout
docker compose down

# Supprimer avec volumes
docker compose down -v
```

---

## ✅ Checklist de vérification

### Consul

- [ ] Consul UI accessible (http://localhost:8500)
- [ ] Service "gateway" visible
- [ ] Service "service-client" visible
- [ ] Service "service-voiture" visible
- [ ] Tous en état "passing" (vert)

### phpMyAdmin

- [ ] phpMyAdmin accessible (http://localhost:8081)
- [ ] Connexion réussie (mysql / root / root)
- [ ] Base `Micro_ClientDB` visible
- [ ] Base `Micro_VoitureDB` visible

### Services

- [ ] Gateway répond (http://localhost:8888)
- [ ] Client répond (http://localhost:8088)
- [ ] Voiture répond (http://localhost:8089)

---

## 🐛 Problèmes courants

### Port déjà utilisé

```bash
# Windows
netstat -ano | findstr :8500
taskkill /PID <PID> /F

# Linux/Mac
lsof -i :8500
kill -9 <PID>
```

### Service ne démarre pas

```bash
# Voir les logs
docker compose logs client-service

# Rebuild
docker compose build client-service
docker compose up -d client-service
```

### Pas de connexion MySQL

**Vérifier dans `application.properties` ou `application.yml` :**
```properties
# ❌ FAUX
spring.datasource.url=jdbc:mysql://localhost:3306/...

# ✅ CORRECT
spring.datasource.url=jdbc:mysql://mysql:3306/...
```

### Service pas dans Consul

**Vérifier les variables d'environnement:**
```yaml
environment:
  SPRING_CLOUD_CONSUL_HOST: consul
  SPRING_CLOUD_CONSUL_PORT: 8500
  SPRING_CLOUD_CONSUL_DISCOVERY_ENABLED: 'true'
```

---

## 📊 Ordre de démarrage

Docker Compose gère automatiquement grâce à `depends_on`:

1. **MySQL** → Démarre en premier
2. **Consul** → Démarre en premier
3. **Gateway** → Attend MySQL + Consul
4. **Client** → Attend MySQL + Consul + Gateway
5. **Voiture** → Attend MySQL + Consul + Gateway
6. **phpMyAdmin** → Attend MySQL

---

## 🔍 Debugging

### Entrer dans un conteneur

```bash
docker exec -it client-container sh
```

### Tester la connectivité

```bash
# Dans le conteneur
ping mysql
ping consul
```

### Voir les variables d'environnement

```bash
docker exec client-container env
```

---

**Bon lancement !** 🚀
