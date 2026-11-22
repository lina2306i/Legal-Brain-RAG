# Script de soutenance — Lina Labiadh · E-Tafakna

**Durée cible : 5 minutes (≈ 4 min 50 s) · 9 slides · rythme calme, regarder le jury**

## Slide 1 — 0:00 – 0:20

Bonjour Madame, bonjour à tous. Je m’appelle Lina Labiadh, élève ingénieure en IA et Data Science à iTeam University.

Je vais vous présenter mon stage d’été chez E-Tafakna, réalisé du 15 juin au 15 août 2026 sous l’encadrement de Madame Dhikra Ben Mahmoud : la construction de fondations data et IA pour un Legal Operating System.

## Slide 2 — 0:20 – 0:55

E-Tafakna est une startup LegalTech tunisienne, fondée en 2022. Sa plateforme couvre tout le cycle d’un contrat : créer, réviser, signer, gérer, en français, anglais et arabe, pour les marchés MENA, Afrique et Europe.

Le problème : en droit, une IA qui invente un article de loi est un risque réel. Les LLM généraux hallucinent, les sources officielles sont éparpillées entre juridictions, et les contrats disponibles sont bruts.

La réponse de mon stage : des infrastructures documentaires structurées, traçables et citables. L’IA cite ses sources, elle ne les invente pas.

## Slide 3 — 0:55 – 1:20

Je présente mon travail comme un seul projet en deux volets.

Volet A, le Legal Brain : collecter les textes officiels, les structurer article par article, les indexer, puis répondre avec un RAG qui cite ses sources. Volet B, Hannibal V2 : préparer les données contractuelles pour fine-tuner le moteur de rédaction de contrats.

Le fil rouge : le modèle ne doit pas inventer le droit. La connaissance vient des bases documentaires.

## Slide 4 — 1:20 – 2:15

Pour le Legal Brain, l’architecture est un pipeline de bout en bout. On part de sources officielles : chaque source a son adapter, qui découvre, télécharge et extrait les documents. Les PDF numériques passent par PyMuPDF, les scans par OCR Tesseract.

Le texte est ensuite découpé en articles : c’est l’unité de travail du système, pas le document entier. Chaque article reçoit un identifiant déterministe et une empreinte SHA-256, qui permet de détecter les changements et d’éviter les doublons.

Un LLM enrichit chaque article : résumés bilingues, domaines juridiques, indicateurs d’obligations et de pénalités. Les embeddings sont ensuite indexés pour une recherche hybride, et l’endpoint /chat répond en renvoyant les identifiants des articles cités.

Environ 29 sources sont configurées : France, Jordanie, Tunisie, Golfe, Afrique du Nord, Italie, Allemagne. Le prototype est démontrable ; les validations cloud OVH et Azure restent conditionnelles.

## Slide 5 — 2:15 – 2:55

Mes contributions portent sur quatre points. D’abord le contrat d’adapters : chaque source implémente les mêmes étapes, discover, has_changed, fetch, extract, et ajouter un pays revient à écrire un adapter et un fichier YAML, sans toucher au pipeline central.

Ensuite l’endpoint /chat : il récupère les articles les plus pertinents et renvoie la réponse avec les cited_article_ids, donc chaque réponse est vérifiable.

J’ai aussi développé la console React que vous voyez ici : lancer un run, suivre les étapes, visualiser les sources. Enfin, les notifications Discord et Telegram et la robustesse : un document en échec n’arrête pas le lot, et les fixtures offline permettent une démo sans cloud.

## Slide 6 — 2:55 – 3:50

Pour Hannibal V2, j’ai construit la chaîne de préparation des données. On part de 1 284 fichiers PDF, DOCX et DOC, en anglais, français et arabe. Après extraction, avec OCR pour les scans, nettoyage et anonymisation, on obtient environ 982 contrats valides.

Après un équilibrage par pays, langue et type de contrat, on retient une base de 375 exemples. J’y ajoute 144 exemples synthétiques répartis en quatre datasets spécialisés, ce qui donne les 519 exemples Alpaca : 450 pour l’entraînement, 69 pour la validation.

Les quatre datasets entraînent des compétences précises : de l’intention au plan du contrat, du plan aux clauses, de la clause à sa rédaction, du contrat à l’analyse des risques.

Côté protection des données : anonymisation par placeholders, déduplication SHA-256, 36 fichiers isolés pour revue PII et 40 contrats réservés à l’audit des juristes.

## Slide 7 — 3:50 – 4:30

En résultats : environ 29 sources configurées, 982 contrats valides, 519 exemples Alpaca et quatre datasets spécialisés. Legal Brain est un prototype RAG démontrable avec citations vérifiables ; Hannibal dispose d’un corpus multilingue prêt pour le fine-tuning.

Je tiens à être transparente sur les limites. Les validations cloud OVH et Azure sont encore conditionnelles : ma démonstration tourne en local, avec des fixtures. La revue PII et juridique n’est pas finalisée, l’arabe est sous-représenté, et les datasets A, B et D sont de petite taille.

Les perspectives : indexation réelle sur OVH et Azure AI Search, audit juriste puis fine-tuning supervisé, évaluation avec des métriques, et un parcours produit unifié.

## Slide 8 — 4:30 – 4:50

Pour conclure, ce stage m’a permis de poser deux fondations pour une IA juridique crédible : des lois structurées et citables d’un côté, un corpus contractuel propre et anonymisé de l’autre, le tout dans un pipeline extensible : ajouter un pays, c’est un adapter et un fichier de configuration.

Le modèle ne doit pas inventer le droit.

## Slide 9 — 4:50 – 5:00

Je vous remercie de votre attention et je suis disponible pour vos questions.

