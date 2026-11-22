# 🚀 GUIDE DÉMO — Lancer les deux projets en terminal
## Legal Brain + Hannibal V2 — Soutenance 28.10.2026

---

## ⚡ RÉSUMÉ RAPIDE (si vous avez 5 minutes)

```powershell
# ═══ LEGAL BRAIN — 3 terminaux ═══
# Terminal 1 : Backend API
cd "D:\Project stage\legal-pipeline-master-F"
.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --reload --port 8000

# Terminal 2 : Frontend Console
cd "D:\Project stage\legal-pipeline-master-F\frontend"
npm run dev

# Terminal 3 : Lancer l'ingestion démo
curl -X POST http://localhost:8000/ingest/run -H "Content-Type: application/json" -d "{\"source_id\": \"fr-legifrance\", \"fixtures_dir\": \"tests/fixtures/legifrance_slice\"}"

# ═══ HANNIBAL V2 — démo data ═══
cd "D:\Project stage\Hanniv2"
python analyze_dataset.py
```

---

## 🟦 PROJET 1 — LEGAL BRAIN (pipeline + RAG + Console)

### Prérequis (vérifier avant la démo)
- [ ] Docker Desktop installé et **démarré**
- [ ] Node.js installé (`node --version`)
- [ ] `.venv` présent dans le dossier (`D:\Project stage\legal-pipeline-master-F\.venv`)
- [ ] Clé Groq disponible (gratuit sur https://console.groq.com/keys) — optionnel pour la démo fixtures

---

### ÉTAPE 0 — Copier le .env (une seule fois)
```powershell
cd "D:\Project stage\legal-pipeline-master-F"
Copy-Item .env.example .env
```
> ✅ Le `.env.example` contient déjà tout ce qu'il faut pour tourner en **mode local fixtures** (pas besoin d'OVH/Azure).

---

### ÉTAPE 1 — Démarrer l'infrastructure Docker (Postgres + MinIO)
```powershell
cd "D:\Project stage\legal-pipeline-master-F"
docker compose up -d postgres minio minio-init
```

Vérifier que c'est OK :
```powershell
docker compose ps
```
> Attendu : **postgres** et **minio** en status `running`

---

### ÉTAPE 2 — Créer le schéma base de données (une seule fois)
```powershell
cd "D:\Project stage\legal-pipeline-master-F"
.venv\Scripts\python.exe -m alembic upgrade head
```
> Attendu : `INFO  [alembic.runtime.migration] Running upgrade ...`

---

### ÉTAPE 3 — Démarrer l'API Backend (Terminal 1 — laisser ouvert)
```powershell
cd "D:\Project stage\legal-pipeline-master-F"
.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --reload --port 8000
```

> ✅ Ouvrir dans le navigateur : **http://localhost:8000/docs**
> → Swagger UI avec tous les endpoints visibles (impressionnant pour le jury !)

---

### ÉTAPE 4 — Démarrer la Console Frontend (Terminal 2 — laisser ouvert)
```powershell
cd "D:\Project stage\legal-pipeline-master-F\frontend"
npm install
npm run dev
```

> ✅ Ouvrir dans le navigateur : **http://localhost:5173**
> → Console E-Tafakna Operations (celle de la capture dans le rapport !)

---

### ÉTAPE 5 — Lancer une ingestion démo (Terminal 3)
```powershell
# Ingestion sur la fixture Légifrance (pas besoin d'internet ni de clé API)
curl -X POST http://localhost:8000/ingest/run `
  -H "Content-Type: application/json" `
  -d "{\"source_id\": \"fr-legifrance\", \"fixtures_dir\": \"tests/fixtures/legifrance_slice\"}"
```

Récupérer le `job_id` retourné et suivre l'avancement :
```powershell
curl http://localhost:8000/jobs/<JOB_ID_ICI>
```

---

### ÉTAPE 6 — Tester la recherche RAG
```powershell
# Recherche d'articles
curl "http://localhost:8000/search?q=obligations%20de%20l%27employeur&top=5"

# Chat RAG avec citations (POINT CLÉ pour la démo !)
curl -X POST http://localhost:8000/chat `
  -H "Content-Type: application/json" `
  -d "{\"query\": \"Quelles sont les obligations de l'employeur en France ?\"}"
```

> ✅ La réponse contient `cited_article_ids` — **c'est la démo clé à montrer au jury**

---

### ÉTAPE 7 — Vérifier les données dans MinIO (bonus visuel)
> Ouvrir : **http://localhost:9001**
> Login : `minioadmin` / `minioadmin`
> → Le bucket `etafakna` contient les fichiers bruts et enrichis

---

### ÉTAPE 8 — Arrêter proprement
```powershell
cd "D:\Project stage\legal-pipeline-master-F"
docker compose down        # garde les données
# ou
docker compose down -v     # efface tout (reset complet)
```

---

## 🟧 PROJET 2 — HANNIBAL V2 (démo data pipeline)

### Prérequis
- [ ] Python installé (`python --version`)
- [ ] Dépendances installées

---

### ÉTAPE 0 — Installer les dépendances (une seule fois)
```powershell
cd "D:\Project stage\Hanniv2"
pip install -r requirements.txt
```

---

### DÉMO A — Voir les statistiques du dataset final
```powershell
cd "D:\Project stage\Hanniv2"
python analyze_dataset.py
```
> ✅ Affiche les statistiques : répartition par langue, type de contrat, pays
> → Parfait à montrer pour les 519 exemples et la répartition FR/EN/AR

---

### DÉMO B — Audit qualité des datasets
```powershell
cd "D:\Project stage\Hanniv2"
python audit_datasets.py
```
> ✅ Affiche un rapport qualité des fichiers JSONL dans `dataset_final/`

---

### DÉMO C — Montrer les fichiers finaux (sans exécuter)
```powershell
# Lister les datasets livrés
dir "D:\Project stage\Hanniv2\dataset_final"

# Voir un exemple JSONL (les 5 premières lignes)
Get-Content "D:\Project stage\Hanniv2\dataset_final\dataset_base_train.jsonl" | Select-Object -First 5
```

---

### DÉMO D — Montrer le pipeline de nettoyage en action (court)
```powershell
# Voir la structure du pipeline
Get-Content "D:\Project stage\Hanniv2\clean_contracts.py" | Select-Object -First 50
```

---

## 🎯 SCÉNARIOS DE DÉMO RECOMMANDÉS POUR LA SOUTENANCE

### Scénario 1 — "Legal Brain en action" (2 minutes, IMPACT MAXIMUM)
```
1. Ouvrir http://localhost:5173 (Console Operations)
2. Montrer les 29 sources dans la liste
3. Sélectionner "fr-legifrance" → Lancer le pipeline
4. Suivre les étapes en direct (Sources → Scraping → Cleaning → Enrich → Embed → Index)
5. Ouvrir http://localhost:8000/docs → tester /chat en direct
6. Montrer la réponse avec cited_article_ids
```

### Scénario 2 — "Hannibal data" (1 minute, pour les questions)
```
1. cd D:\Project stage\Hanniv2
2. python analyze_dataset.py  → montrer les stats
3. Get-Content dataset_final\dataset_C_clause_redaction_train.jsonl | Select-Object -First 3
   → montrer un exemple Alpaca complet (instruction / input / output)
```

---

## 🔧 DÉPANNAGE RAPIDE

| Problème | Solution |
|---|---|
| Port 8000 déjà utilisé | `netstat -an | findstr 8000` puis changer le port |
| Docker non démarré | Lancer Docker Desktop, attendre la baleine verte |
| `alembic upgrade` échoue | Vérifier que Postgres tourne : `docker compose ps` |
| `npm run dev` échoue | `npm install` d'abord dans `frontend/` |
| `.venv` absent | `uv venv && uv pip install -e ".[dev]"` (si uv installé) |
| Curl non reconnu | Utiliser PowerShell ou Git Bash |

---

## 📌 URLS CLÉS À RETENIR

| URL | Description |
|---|---|
| http://localhost:5173 | **Console Operations** (à montrer en démo) |
| http://localhost:8000/docs | **Swagger API** (endpoints interactifs) |
| http://localhost:8000/search?q=... | Recherche d'articles |
| http://localhost:8000/chat | Chat RAG avec citations |
| http://localhost:9001 | MinIO (stockage objet local) |

---

*Fichier généré le 27/10/2026 — DEMO_GUIDE_TERMINAL.md*
