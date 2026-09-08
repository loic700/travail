# Suivi des heures de travail — Docker

## Lancer le projet

Depuis ce dossier :

```bash
docker compose up -d --build
```

Puis ouvre dans ton navigateur :

```
http://TON_SERVEUR:8080
```

(remplace `TON_SERVEUR` par `localhost` si tu testes en local, ou par l'IP/domaine de ton serveur).

## Ce que ça fait

- **web** (port 8080) : sert la page HTML de l'application.
- **api** (port 3001) : petit serveur Express qui stocke ton planning et tes réglages dans un fichier JSON, sur un volume Docker persistant (`heures-data`).

Tes données survivent aux redémarrages des conteneurs et sont accessibles depuis n'importe quel appareil qui peut atteindre ton serveur (téléphone, PC, autre navigateur...).

## Vérifier que l'API tourne

```bash
curl http://localhost:3001/api/health
```

Doit répondre : `{"status":"ok"}`

## Sauvegarder / restaurer les données manuellement

Les données sont dans le volume Docker `heures-data`. Pour les sauvegarder :

```bash
docker run --rm -v heures-app_heures-data:/data -v $(pwd):/backup alpine \
  tar czf /backup/heures-backup.tar.gz -C /data .
```

Pour les restaurer :

```bash
docker run --rm -v heures-app_heures-data:/data -v $(pwd):/backup alpine \
  tar xzf /backup/heures-backup.tar.gz -C /data
```

## Arrêter

```bash
docker compose down
```

(les données restent dans le volume, elles ne sont pas supprimées)

## Changer les ports

Si 8080 ou 3001 sont déjà utilisés sur ton serveur, modifie les ports dans `docker-compose.yml`, section `ports:` (partie de gauche = port exposé sur ta machine).
