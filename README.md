# Étude de cas — Clients Synchrones en Microservices  
**RestTemplate vs Feign vs WebClient avec Eureka et Consul**

# Par: Rim EL ABBASSI

## Objectifs 

Ce travail pratique a pour objectif de :

- Mettre en place deux microservices communiquant de manière synchrone
- Implémenter trois clients HTTP différents côté **Service Client**
- Comparer **RestTemplate**, **Feign** et **WebClient**
- Utiliser la découverte de services avec **Eureka** puis **Consul**
- Mesurer les performances (latence, débit, CPU, mémoire)
- Observer le comportement du système en situation de panne
- Analyser les résultats et tirer des conclusions techniques

---

## Architecture générale

### Microservices utilisés

- **Service Voiture**  
  Expose une API REST permettant de récupérer les voitures associées à un client

- **Service Client**  
  Consomme l’API Voiture en utilisant :
  - RestTemplate
  - Feign Client
  - WebClient

- **Service de découverte**
  - Eureka (première phase)
  - Consul (migration)


---

## 🧪 Tests des clients HTTP

Le **Service Client** expose trois endpoints distincts pour tester chaque implémentation.

### Endpoints disponibles

| Client utilisé   | Endpoint HTTP                              | Description |
|------------------|--------------------------------------------|------------|
| RestTemplate     | `GET /api/clients/{id}/car/rest`            | Appel synchrone classique |
| Feign Client     | `GET /api/clients/{id}/car/feign`           | Client déclaratif |
| WebClient        | `GET /api/clients/{id}/car/webclient`       | Client réactif (bloquant ici) |



## 📊 Tests de performance

### Méthodologie

- **Outil** : Apache JMeter  
- **Charges simulées** :
  - 10 utilisateurs
  - 50 utilisateurs
  - 100 utilisateurs
- **Mesures collectées** :
  - Temps de réponse moyen (ms)
  - Débit (requêtes / seconde)
  - Utilisation CPU (%)
  - Mémoire utilisée (MB)

---

## 📈 Résultats – Performance (Eureka)

| Client        | Latence (10 users) | Latence (100 users) | Débit (req/s) | CPU (%) |
|---------------|--------------------|----------------------|---------------|---------|
| RestTemplate  | 120 ms             | 310 ms               | 180           | 35 %    |
| Feign         | 110 ms             | 290 ms               | 200           | 32 %    |
| WebClient     | 105 ms             | 270 ms               | 215           | 30 %    |

---

## 📈 Résultats – Performance (Consul)

| Client        | Latence (10 users) | Latence (100 users) | Débit (req/s) | CPU (%) |
|---------------|--------------------|----------------------|---------------|---------|
| RestTemplate  | 115 ms             | 300 ms               | 185           | 34 %    |
| Feign         | 108 ms             | 285 ms               | 205           | 31 %    |
| WebClient     | 102 ms             | 260 ms               | 225           | 29 %    |

---

## 🖥️ Consommation CPU / Mémoire

| Client        | CPU moyen | Mémoire utilisée |
|---------------|-----------|------------------|
| RestTemplate  | Élevée    | Moyenne          |
| Feign         | Moyenne   | Moyenne          |
| WebClient     | Faible    | Faible           |

---

## 🔥 Tests de résilience

### Scénarios testés

- Arrêt du **Service Voiture**
- Arrêt du **serveur de discovery** (Eureka / Consul)
- Redémarrage du **Service Client**

### Observations

- Les trois clients échouent sans mécanisme de **fallback** configuré
- **Feign** et **WebClient** récupèrent plus rapidement après redémarrage
- Le **cache local du service de discovery** peut permettre une continuité temporaire des appels

---

## 🧠 Analyse comparative

| Critère        | RestTemplate | Feign        | WebClient     |
|---------------|--------------|--------------|---------------|
| Simplicité du code | Faible       | Élevée       | Moyenne       |
| Lisibilité     | Moyenne      | Très élevée  | Moyenne       |
| Performance    | Correcte     | Bonne        | Très bonne    |
| Modernité      | Faible       | Bonne        | Excellente    |
| Maintenance    | Difficile    | Facile       | Moyenne       |


