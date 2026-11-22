# Legal Brain RAG

**Legal Brain RAG** est un projet orienté vers l’analyse et la compréhension de documents juridiques grâce à une approche de **RAG (Retrieval-Augmented Generation)**.

Ce dépôt public sert de **page de présentation et d’architecture** du projet. Les deux dépôts associés sont privés afin de protéger les données, les scripts de préparation et les composants expérimentaux.

## Écosystème du projet

### `hannibal-finetunning--V2` — Données et fine-tuning

> Dépôt privé : [`hannibal-finetunning--V2`](https://github.com/lina2306i/hannibal-finetunning--V2)

Ce dépôt constitue la base de **préparation des données** et du **fine-tuning** du modèle juridique.

Il contient notamment :

- le nettoyage et l’analyse de contrats juridiques ;
- la construction et le rééquilibrage des jeux de données ;
- la génération de données synthétiques et de jeux question-réponse ;
- le contrôle qualité, l’étiquetage et la séparation des données ;
- la génération de rapports et de sorties d’entraînement ;
- les templates et scripts liés au traitement des documents.

Il fournit ainsi les données structurées nécessaires pour améliorer les capacités du modèle sur des tâches juridiques.

### `Hack-ai-Legal_Brain` — Application et orchestration

> Dépôt privé : [`Hack-ai-Legal_Brain`](https://github.com/lina2306i/Hack-ai-Legal_Brain)

Ce dépôt héberge l’environnement applicatif du projet **Legal Brain**, notamment la partie expérimentale et opérationnelle.

Il peut inclure :

- l’ingestion de documents juridiques ;
- la génération d’embeddings et l’indexation vectorielle ;
- la recherche sémantique de passages pertinents ;
- l’orchestration du RAG ;
- l’interface utilisateur ou API ;
- les composants d’évaluation et d’expérimentation.

## Architecture envisagée

```text
Documents juridiques
        │
        ▼
Dépôt privé : hannibal-finetunning--V2
  nettoyage • datasets • fine-tuning
        │
        ▼
Modèle spécialisé
        │
        ▼
Dépôt privé : Hack-ai-Legal_Brain
  ingestion • embeddings • recherche • RAG • interface
        │
        ▼
Réponses contextualisées aux documents
```

## Fonctionnement général

1. Les contrats et documents sources sont nettoyés et transformés en données d’entraînement.
2. Les données sont analysées, contrôlées, étiquetées et réparties en ensembles d’entraînement.
3. Un modèle spécialisé est affiné pour mieux comprendre le vocabulaire juridique.
4. Les documents sont indexés sous forme de représentations vectorielles.
5. Une requête utilisateur permet de retrouver les passages pertinents.
6. Le modèle génère une réponse contextualisée à partir des sources récupérées.

## Confidentialité

Les dépôts suivants sont privés :

- [`lina2306i/hannibal-finetunning--V2`](https://github.com/lina2306i/hannibal-finetunning--V2)
- [`lina2306i/Hack-ai-Legal_Brain`](https://github.com/lina2306i/Hack-ai-Legal_Brain)

Cette séparation permet de protéger les données juridiques, les scripts internes, les modèles, les configurations et les résultats expérimentaux. Seule la présentation publique de l’écosystème est disponible dans ce dépôt.

## Statut

Projet en développement. L’ensemble des composants techniques, données d’entraînement et résultats détaillés sont maintenus dans les dépôts privés associés.
