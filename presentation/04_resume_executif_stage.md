# Résumé exécutif — Stage E-Tafakna (été 2026)

**Auteure :** Labiadh Lina  
**Entreprise :** E-Tafakna (LegalTech, Tunisie)  
**Encadrante :** Dhikra Ben Mohammed  
**Période :** 15 juin 2026 – 15 août 2026  
**Intitulé :** Stage Generative AI / Data & AI Engineering  
**Présentation rapport :** projet unique en 2 volets (Plan B)  

---

## En une phrase

Contribution à deux briques critiques de l’écosystème E-Tafakna : un **pipeline juridique multi-pays orienté RAG** (Legal Brain) et une **chaîne de préparation de données** pour le fine-tuning du moteur de contrats **Hannibal V2**.

---

## Contexte

E-Tafakna développe un AI Legal Operating System (contrats, signature, assistance, IA) pour des utilisateurs multi-juridictions. La fiabilité de l’IA dépend de pipelines de données solides : lois officielles indexables d’une part, corpus contractuels de qualité d’autre part.

---

## Travaux réalisés

### 1) Legal Pipeline (RAG Hack / Legal Brain)

- Participation à la construction d’un système de veille et d’indexation de textes juridiques officiels (approche **article-centrique**).
- Travail sur le contrat d’**adapters** (découverte, détection de changements, fetch, extraction) et le registre de **~29 sources** (France, Jordanie, Tunisie, pays MENA, Italie, Allemagne, etc.).
- Contribution au pipeline : extraction/OCR, structuration, enrichissement LLM, schéma de métadonnées (dont résumés FR/AR, institutions).
- Mise en place d’une couche **RAG de démonstration** (`POST /chat` avec citations d’articles).
- Développement de la **console frontend** (Vite/React) pour piloter le pipeline et visualiser les sources/jobs.
- Ajouts de **notifications Discord** et amélioration de la robustesse face aux échecs partiels.
- Environnement technique : Python, FastAPI, PostgreSQL, Docker/Dagster, OVH, Azure AI Search.

### 2) Hannibal V2 (préparation data / fine-tuning)

- Constitution et traitement d’un corpus de **modèles de contrats** multi-pays et multilingues (FR/EN/AR).
- Pipeline de nettoyage : extraction PDF/DOCX, OCR, dédoublonnage SHA-256, anonymisation par placeholders, catégorisation pays/type/langue.
- Contrôles qualité (tags génériques, revue PII, échantillonnage).
- Génération de **datasets Alpaca** spécialisés (A/B/C/D) pour entraîner les capacités de raisonnement/rédaction des agents Hannibal.
- **Résultats mesurables :** ~1 284 fichiers analysés ; ~982 contrats valides après relabellisation ; **519** exemples JSONL finaux (base + A/B/C/D, splits train/validation).

---

## Résultats & valeur

| Livrable | Valeur pour E-Tafakna |
|---|---|
| Pipeline + démo RAG/console | Prototype opérationnel de Legal Brain multi-sources |
| Corpus & datasets Hannibal | Base concrète pour fine-tuning (style, structure, analyse de risques) |
| Documentation / walkthroughs | Reproductibilité et transmission à l’équipe |

**Fil conducteur :** *le modèle ne doit pas inventer le droit* — il s’appuie sur des sources structurées (lois) et des templates validés (contrats).

---

## Limites assumées

- Legal Pipeline : certaines validations cloud (OVH/Azure) et ingestions massives restent conditionnées aux accès ; démos parfois en mode fixtures / mock LLM.
- Hannibal : revue juridique et contrôle PII systématique encore nécessaires avant entraînement/publication ; échecs d’extraction sur certains formats anciens.

---

## Compétences développées

Data engineering & ETL · scraping / APIs légales · OCR · enrichissement LLM · embeddings & recherche hybride · RAG avec citations · React/Vite · préparation de datasets & génération synthétique · Docker / bonnes pratiques de traçabilité.

---

## Perspectives

1. Finaliser les validations bout-en-bout cloud et l’indexation hybride réelle.  
2. Compléter la revue PII/juriste puis lancer le fine-tuning Hannibal avec métriques d’évaluation.  
3. Relier plus étroitement Legal Brain (lois) et Hannibal (contrats) dans un parcours produit unifié.

---

*Ce résumé peut servir de base au « Résumé » du rapport Google Docs et à la slide de conclusion Canva.*
