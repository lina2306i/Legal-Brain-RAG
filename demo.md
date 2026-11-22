cd "D:\Project stage\legal-pipeline-master-F"
dir .venv            # doit exister
dir frontend\node_modules   # doit exister
docker images        # postgres et minio doivent apparaître

********************
cd rag-hack
dir .venv            # doit exister
docker images        # postgres et minio doivent apparaître
ls
cd legal-pipeline-master
dir frontend\node_modules
*********************
docker compose up -d postgres minio minio-init
docker compose ps



Copy-Item .env.example .env 

# ═══ LEGAL BRAIN — 3 terminaux ═══
# Terminal 1 : Backend API
cd "D:\Project stage\legal-pipeline-master-F"
.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --reload --port 8000

****

docker compose up -d postgres minio minio-init 
.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --port 8000 
>> Ouvre ensuite http://localhost:8000/docs. La démo la plus utile pour le jury se fait déjà là : teste /chat dans Swagger et montre cited_article_ids. La console React (npm run dev dans frontend, sans npm install) est un bonus.
            

cd "D:\Project stage\rag-hack\legal-pipeline-master"                                                
 ..\.venv\Scripts\python.exe -c "import uvicorn, fastapi; print('ok')"
>> Si ça affiche ok, lance l’API :

.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --port 8000 
.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --reload --port 8000 
>Si tu obtiens No module named etafakna, ajoute le code source au chemin et relance :

//**
$env:PYTHONPATH="src"
 ..\.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --port 8000
//**
>Si tu obtiens No module named uvicorn, installe-le (c’est léger) : ..\.venv\Scripts\python.exe -m pip install uvicorn fastapi.

>>Ensuite, ouvre http://localhost:8000/docs.

*****


# Terminal 2 : Frontend Console
cd "D:\Project stage\legal-pipeline-master-F\frontend"
npm run dev

*****
cd "D:\Project stage\rag-hack\legal-pipeline-master\frontend"
npm run dev
*****


# Terminal 3 : Lancer l'ingestion démo
curl -X POST http://localhost:8000/ingest/run -H "Content-Type: application/json" -d "{\"source_id\": \"fr-legifrance\", \"fixtures_dir\": \"tests/fixtures/legifrance_slice\"}"


------------------------Test de /chat dans Swagger

1. Va sur http://localhost:8000/docs.
2. Ouvre POST /chat, clique sur « Try it out ».
3. Mets ceci dans le corps de la requête, puis clique sur « Execute » :
json

{"question": "Quelles sont les obligations de l'employeur en France ?", "top_k": 5}
Dans POST /chat → « Try it out », remplace tout le contenu par :
Clique sur « Execute ».

4. Regarde si la réponse contient cited_article_ids.

> error 500 : 422 :: 

///**
cd "D:\Project stage\rag-hack\legal-pipeline-master"
$env:PYTHONPATH="src"
   ..\.venv\Scripts\python.exe -m alembic upgrade head


cd "D:\Project stage\rag-hack\legal-pipeline-master"
 Select-String -Path .env -Pattern "DATABASE|POSTGRES|5432|5433"
 Select-String -Path docker-compose.yml -Pattern "POSTGRES|5432|5433"


 >>1. Corrige .env

Ouvre le fichier avec notepad .env. Dans l’URL de la base (une ligne qui ressemble à postgresql+psycopg://utilisateur:motdepasse@localhost:5432/nomdb), fais deux changements :
remplace 5432 par 5433 ;
mets l’utilisateur, le mot de passe et le nom de base qui figurent dans le compose.

>> 2. Arrête l’API (Ctrl+C), puis relance-la
L’API lit .env au démarrage : tant que tu ne la relances pas, elle garde l’ancienne URL.

>> 3. Teste dans Swagger (/docs → POST /chat)
errooe 500


  cd "D:\Project stage\rag-hack\legal-pipeline-master"
  $env:PYTHONPATH="src"
  ..\.venv\Scripts\python.exe -m alembic upgrade head

    docker compose exec postgres psql -U etafakna -d etafakna -c "ALTER USER etafakna WITH PASSWORD 'etafakna';"
///**
>>>commande échoue aussi,

** 1. Vérifie la ligne dans .env et les variables de ta session
powershell
cd "D:\Project stage\rag-hack\legal-pipeline-master"
Select-String -Path .env -Pattern "ETAFAKNA_DATABASE_URL"
Get-ChildItem Env: | Where-Object { $_.Name -match "DATABASE|POSTGRES|PG" }

La première commande doit afficher etafakna:etafakna@localhost:5433/etafakna

** 2. 
$env:ETAFAKNA_DATABASE_URL="postgresql+psycopg://etafakna:etafakna@127.0.0.1:5433/etafakna"
$env:PYTHONPATH="src"
..\.venv\Scripts\python.exe -m alembic upgrade head

** 3. Regarde si la base contient déjà des tables
docker compose exec postgres psql -U etafakna -d etafakna -c "\dt"

** 4. Relance l’API dans cette même fenêtre
cd "D:\Project stage\rag-hack\legal-pipeline-master" 
$env:PYTHONPATH="src"
  ..\.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --port 8000


>>> error

1.
cd "D:\Project stage\rag-hack\legal-pipeline-master"
$env:ETAFAKNA_DATABASE_URL="postgresql+psycopg://etafakna:etafakna@127.0.0.1:5433/etafakna"
$env:PYTHONPATH="src"
..\.venv\Scripts\python.exe -m alembic upgrade heads

docker compose exec postgres pg_dump -U etafakna etafakna > ..\backup_etafakna.sql


2.

docker compose exec postgres psql -U etafakna -d etafakna -c "select count(*) from article;"

3. Réessaie /chat dans Swagger
cd "D:\Project stage\rag-hack\legal-pipeline-master" 
$env:PYTHONPATH="src"
  ..\.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --port 8000


>>> eroor 500

docker compose exec postgres psql -U etafakna -d etafakna -c "select * from alembic_version;"

docker compose exec postgres psql -U etafakna -d etafakna -c "select column_name from information_schema.columns where table_name='article' and column_name like 'institution%';"

docker compose exec postgres psql
 -U etafakna -d etafakna -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"  

$env:ETAFAKNA_DATABASE_URL="postgresql+psycopg://etafakna:etafakna@127.0.0.1:5433/etafakna"
$env:PYTHONPATH="src"
..\.venv\Scripts\python.exe -m alembic upgrade heads

>>


docker compose exec postgres psql -U etafakna -d etafakna -c "select column_name from information_schema.columns where table_name='article' and column_name in ('institution_primary','content_french','search_content');"


docker compose exec postgres psql -U etafakna -d etafakna -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"
$env:ETAFAKNA_DATABASE_URL="postgresql+psycopg://etafakna:etafakna@127.0.0.1:5433/etafakna"
$env:PYTHONPATH="src"
..\.\.venv\Scripts\python.exe -m alembic upgrade heads

docker compose exec postgres psql -U etafakna -d etafakna -c "select column_name from information_schema.columns where table_name='article' and column_name in ('institution_primary','content_french','search_content');"

..\.\.venv\Scripts\python.exe -m uvicorn etafakna.api.main:create_app --factory --port 8000


$env:ETAFAKNA_DATABASE_URL="postgresql+psycopg://etafakna:etafakna@127.0.0.1:5433/etafakna"
$env:PYTHONPATH="src"
..\.\.venv\Scripts\python.exe -m alembic upgrade heads

docker exec legal-pipeline-master-postgres-1 psql -U etafakna -d etafakna -c "select column_name from information_schema.columns where table_name='article' order by 1;"


***************************

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


*******************************

### ÉTAPE 7 — Vérifier les données dans MinIO (bonus visuel)
> Ouvrir : **http://localhost:9001**
> Login : `minioadmin` / `minioadmin`
> → Le bucket `etafakna` contient les fichiers bruts et enrichis


### ÉTAPE 8 — Arrêter proprement
```powershell
cd "D:\Project stage\legal-pipeline-master-F"
docker compose down        # garde les données
# ou
docker compose down -v     # efface tout (reset complet)
```
