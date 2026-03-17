# Docker RUNTRACK2 — Stéphane Nawrat
## La Plateforme — Mars 2026

**Environnement :** macOS 15.7.1 + OrbStack  
**Image Docker :** docker/welcome-to-docker  
**Date :** 17 mars 2026

---

## Objectif

Maîtriser les commandes Docker de base : pull, run, stop, suppression de containers et images.

---

## Partie 1 — Commandes de base

### Vérifier la version Docker

**Commande :**
```bash
docker --version
```

**Résultat :** Docker version 28.5.2

![Docker version](images/01-docker-version.png)

---

### Informations système Docker

**Commande :**
```bash
docker info
```

**Résultat :** Affiche configuration complète (containers, images, plugins, réseau).

![Docker info](images/02-docker-info.png)

---

### Lister les containers actifs

**Commande :**
```bash
docker ps
```

**Résultat :** Liste des containers en cours d'exécution.

![Docker ps](images/03-docker-ps.png)

---

### Lister les images locales

**Commande :**
```bash
docker images
```

**Résultat :** Images Docker disponibles localement.

![Docker images](images/04-docker-images.png)

---

## Partie 2 — Pull et Run

### Télécharger l'image welcome-to-docker

**Commande :**
```bash
docker pull docker/welcome-to-docker
```

**Résultat :** Image téléchargée depuis Docker Hub.

![Docker pull](images/05-docker-pull.png)

---

### Vérifier l'image téléchargée

**Commande :**
```bash
docker images
```

**Résultat :** L'image `docker/welcome-to-docker` apparaît dans la liste.

![Docker images after pull](images/06-docker-images-after-pull.png)

---

### Lancer le container

**Commande :**
```bash
docker run -it --rm -p 8080:80 docker/welcome-to-docker
```

**Options :**
- `-it` : Mode interactif avec terminal
- `--rm` : Supprime automatiquement le container à l'arrêt
- `-p 8080:80` : Map port 8080 du Mac vers port 80 du container

**Résultat :** Container démarre, logs affichés en temps réel.

![Docker run](images/07-docker-run.png)

---

### Accès via navigateur

**URL :** http://localhost:8080

**Résultat :** Interface welcome-to-docker s'affiche avec message de félicitations.

![Browser welcome](images/08-browser-welcome.png)

---

### Arrêter le container

**Commande :** `Ctrl+C` dans le terminal

**Résultat :** Container arrêté et supprimé automatiquement (grâce à `--rm`).

![Docker stop](images/09-docker-stop.png)

---

### Vérifier suppression automatique

**Commande :**
```bash
docker ps -a
```

**Résultat :** Le container n'apparaît plus (supprimé par `--rm`).

![Docker ps after stop](images/10-docker-ps-after-stop.png)

---

## Partie 3 — Suppression de containers

### Containers de test créés

**Commandes :**
```bash
docker run -d --name test-container-1 nginx
docker run -d --name test-container-2 nginx
docker run -d --name test-container-3 nginx
```

**Résultat :** 3 containers nginx en arrière-plan.

![Test containers](images/11-docker-ps-test-containers.png)

---

### 1. Supprimer un container spécifique

**Commandes :**
```bash
docker stop test-container-1
docker rm test-container-1
```

**Résultat :** Container arrêté puis supprimé.

![Remove specific container](images/12-docker-rm-specific.png)

**Observation :** Un container doit être arrêté avant suppression (sauf avec `-f`).

---

### 2. Supprimer plusieurs containers

**Commandes :**
```bash
docker stop test-container-2 test-container-3
docker rm test-container-2 test-container-3
```

**Résultat :** Plusieurs containers supprimés en une seule commande.

![Remove multiple containers](images/13-docker-rm-multiple.png)

**Observation :** On peut lister plusieurs IDs ou noms séparés par espace.

---

### 3. Supprimer tous les containers arrêtés

**Commande :**
```bash
docker container prune
```

**Résultat :** Tous les containers avec statut `Exited` sont supprimés.

![Container prune](images/14-docker-container-prune.png)

**Observation :** Commande pratique pour nettoyer le système rapidement.

---

### 4. Forcer la suppression d'un container actif

**Tentative normale (ERREUR) :**
```bash
docker rm running-test
```

**Résultat :** Erreur car container encore actif.

![Remove error](images/15-docker-rm-error.png)

**Force la suppression :**
```bash
docker rm -f running-test
```

**Résultat :** Container supprimé de force même s'il tournait.

![Remove force](images/16-docker-rm-force.png)

**Observation :** `-f` force la suppression sans arrêt préalable. À utiliser avec précaution.

---

## Partie 4 — Suppression d'images

### 1. Supprimer une image spécifique

**Commande :**
```bash
docker rmi nginx
```

**Résultat :** Image nginx supprimée.

![Remove image specific](images/17-docker-rmi-specific.png)

---

### 2. Supprimer plusieurs images

**Commande :**
```bash
docker rmi alpine hello-world
```

**Résultat :** Plusieurs images supprimées en une commande.

![Remove multiple images](images/18-docker-rmi-multiple.png)

---

### 3. Supprimer toutes les images inutilisées

**Commande :**
```bash
docker image prune -a
```

**Résultat :** Toutes les images non utilisées par un container sont supprimées.

![Image prune](images/19-docker-image-prune.png)

**Observation :** `-a` supprime même les images "dangling" (sans tag).

---

### 4. Forcer la suppression d'une image

**Tentative normale (ERREUR) :**
```bash
docker rmi nginx
```

**Résultat :** Erreur car image utilisée par un container.

![Remove image error](images/20-docker-rmi-error.png)

**Force la suppression :**
```bash
docker rmi -f nginx
```

**Résultat :** Image supprimée mais container cassé.

![Remove image force](images/21-docker-rmi-force.png)

---

## Erreur identifiée et correction

### ❌ Erreur dans les commandes

**Commande problématique :**
```bash
docker rmi -f nginx  # Alors qu'un container utilise cette image
```

**Problème :** Force la suppression de l'image SANS supprimer le container qui l'utilise. Le container devient "cassé" (image manquante).

---

### ✅ Ordre correct

**Méthode propre :**
```bash
# 1. Arrêter le container
docker stop <container-name>

# 2. Supprimer le container
docker rm <container-name>

# 3. Supprimer l'image
docker rmi <image-name>
```

**Ou en une ligne (forcé mais propre) :**
```bash
docker rm -f <container-name> && docker rmi <image-name>
```

---

## Compétences acquises

- ✅ Installer et configurer Docker (OrbStack)
- ✅ Pull d'images depuis Docker Hub
- ✅ Lancer containers avec options (-it, --rm, -p)
- ✅ Port mapping (HOST:CONTAINER)
- ✅ Arrêter et supprimer containers
- ✅ Supprimer images Docker
- ✅ Commandes de nettoyage (prune)
- ✅ Suppression forcée (-f) et ses risques
- ✅ Identifier ordre correct des opérations

---

## Commandes de référence

### Containers
```bash
docker ps                          # Liste containers actifs
docker ps -a                       # Liste TOUS les containers
docker run <options> <image>       # Lancer container
docker stop <container>            # Arrêter container
docker rm <container>              # Supprimer container
docker rm -f <container>           # Forcer suppression
docker container prune             # Supprimer tous arrêtés
```

### Images
```bash
docker images                      # Liste images locales
docker pull <image>                # Télécharger image
docker rmi <image>                 # Supprimer image
docker rmi -f <image>              # Forcer suppression
docker image prune -a              # Supprimer images inutilisées
```

### Nettoyage complet
```bash
docker system prune                # Nettoyage global
docker system prune -a --volumes   # Nettoyage complet (avec volumes)
```

---

## Bonnes pratiques

**Ordre de suppression :**
1. Arrêter container (`docker stop`)
2. Supprimer container (`docker rm`)
3. Supprimer image (`docker rmi`)

**Éviter :**
- `-f` (force) en production
- Suppression d'images utilisées
- `docker system prune -a` sans vérifier

**Privilégier :**
- Arrêt propre avant suppression
- Vérification avec `docker ps` / `docker images`
- Nettoyage ciblé plutôt que global

---


