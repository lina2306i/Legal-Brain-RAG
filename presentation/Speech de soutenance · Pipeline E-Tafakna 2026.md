# Speech de soutenance : Pipeline de stage E-Tafakna 2026

*Labiadh Lina · iTeam University · Encadrante : Dhikra Ben Mahmoud · Soutenance du 28 octobre 2026 · Durée visée : 15 à 18 minutes + questions*

**Comment utiliser ce texte.** Il suit la page HTML section par section, dans l’ordre de la barre de navigation. Les indications entre crochets sont des gestes à faire à l’écran (cliquer, scroller). Les durées sont indicatives : si vous êtes en retard, raccourcissez les sections 6 et 7 en priorité, jamais la démo.

---

## 0. Ouverture (1 min) · En-tête de la page

&#91;Page affichée sur l’en-tête, ne pas scroller.\]

Bonjour à toutes et à tous. Je m’appelle Lina Labiadh, et je vais vous présenter le travail réalisé pendant mon stage chez E-Tafakna, du 15 juin au 15 août 2026, sous l’encadrement de Madame Dhikra Ben Mahmoud. Je remercie également le jury d’être présent.

Le titre de cette page résume tout le projet : *du texte de loi brut à une IA juridique qui cite ses sources*. Retenez cette idée, elle sera mon fil rouge : **le modèle ne doit jamais inventer le droit.**

J’ai travaillé sur deux volets complémentaires. Le premier, **Legal Brain**, est un pipeline de veille juridique multi-pays avec un moteur de recherche qui répond en citant ses sources. Le second, **Hannibal V2**, est la préparation d’un corpus de fine-tuning pour la rédaction de contrats en français, anglais et arabe.

---

## 1. Contexte (1 min 30) · Section « Contexte »

&#91;Cliquer sur « Contexte » ou scroller doucement.\]

Pourquoi ce stage ? Parce que suivre à la main l’évolution des lois de quatorze pays est lent, coûteux et source d’erreurs. E-Tafakna construit ce qu’elle appelle un *AI Legal Operating System* pour quatorze pays.

Or une IA juridique qui hallucine une loi n’est pas un simple bug : c’est un risque légal grave. Mon rôle a donc été de poser les **fondations documentaires** qui évitent ce risque : des données structurées, des sources officielles et des citations vérifiables.

Cela se décline en trois objectifs, que vous voyez dans les trois cartes : **centraliser et veiller**, c’est-à-dire surveiller automatiquement journaux officiels et décrets ; **rendre l’information intelligente**, en transformant des PDF bruts ou scannés en articles JSON propres et enrichis ; et **alimenter un RAG**, pour obtenir des réponses sourcées avec les articles cités.

En chiffres : vingt-neuf sources, un peu plus de mille deux cents fichiers traités côté contrats, neuf cent quatre-vingt-deux contrats valides et cinq cent dix-neuf exemples de fine-tuning produits.

---

## 2. Le pipeline global (2 min) · Section « Pipeline global »

&#91;Scroller jusqu’aux deux lignes d’étapes.\]

Voici la vue d’ensemble. Deux chaînes de données, qui tournent en parallèle et convergent vers un même objectif.

La première ligne, en bleu, c’est **Legal Brain** en six étapes : les sources officielles, les adapters, l’OCR et l’extraction, l’enrichissement par LLM, l’index Azure AI Search, et enfin l’API `/chat` qui répond avec ses citations.

La seconde ligne, en orange, c’est **Hannibal V2** en huit étapes : on part de mille deux cent quatre-vingt-quatre fichiers bruts, on extrait, on nettoie et on anonymise, on contrôle la qualité, on équilibre, on génère des exemples synthétiques, et on aboutit à cinq cent dix-neuf exemples prêts pour le fine-tuning.

Vous voyez la légende des statuts : vert pour livré, orange pour partiel, bleu pour la démo en direct. La dernière étape de Hannibal, le fine-tuning lui-même, est en gris : c’est volontairement la prochaine étape, hors périmètre du stage.

Les deux volets convergent vers E-Tafakna : à terme, la rédaction contractuelle pourra citer directement l’index juridique.

---

## 3. Legal Brain (3 min) · Section « Legal Brain »

&#91;Scroller jusqu’au bandeau des sept étapes.\]

Zoomons sur Legal Brain. Un principe de conception structure tout : **l’unité de traitement est l’article**, pas le document entier, ni un morceau arbitraire de cinq cents tokens. Les approches RAG naïves découpent à l’aveugle. Nous, nous respectons l’unité de droit, avec un identifiant déterministe, un lien vers le document parent et des drapeaux comme `has_obligations` ou `has_penalties`.

Le pipeline compte sept étapes, et chacune est traçable et idempotente grâce au hachage SHA-256.

- **Étape 1, Discover et adapters** : un contrat unique, le `SourceAdapter`, gère l’OAuth2 de Légifrance, l’Akoma Ntoso en Italie, le scraping du JORT.
- **Étape 2, Fetch** : les octets bruts sont archivés dans MinIO et on calcule le `content_hash_sha256`. Si le hash est déjà connu, on ne retraite rien.
- **Étape 3, OCR et extraction** : PyMuPDF pour les PDF natifs, Tesseract pour les scans, puis un découpage par expressions régulières multilingues : « Article », « Articolo », « Artikel », et la forme arabe.
- **Étape 4, Nettoyage** : en-têtes, pieds de page, diacritiques arabes, détection de langue.
- **Étape 5, Enrichissement** : résumés bilingues, domaines juridiques et flags, validés par un schéma Pydantic.
- **Étape 6, Embeddings** : BGE-M3 en 1024 dimensions pour la démo, avec text-embedding-3-large en 3072 dimensions comme cible Azure.
- **Étape 7, Indexation hybride** : BM25 plus cosinus, avec filtres par pays, date et domaine.

&#91;Scroller jusqu’au bloc de code `SourceAdapter`.\]

Ma contribution clé est ce protocole **SourceAdapter**. Il tient en quatre méthodes : `discover`, `has_changed`, `fetch`, `extract`. Pour ajouter la Belgique, il suffit d’un fichier YAML et d’un adapter. **Le pipeline central ne change pas.** C’est ce qui rend le système extensible au-delà des vingt-neuf sources actuelles.

&#91;Scroller sur les quatre cartes de contributions, puis la carte des pays.\]

Quatre contributions ont été livrées : le protocole SourceAdapter avec sa configuration YAML, le `/chat` RAG citable, une console React avec Vite pour piloter le pipeline, et la robustesse : alertes Telegram et WhatsApp, reprise après échec, et un mode fixtures hors ligne pour démontrer sans accès cloud. Et la couverture : vingt-neuf sources, quatorze pays, trois langues.

---

## 4. RAG et démo guidée (4 min) · Section « RAG & démo »

&#91;Scroller jusqu’aux quatre phases RAG.\]

Comment le RAG garantit-il qu’aucune réponse n’est inventée ? En quatre phases. La question est vectorisée et ses mots-clés extraits. Une recherche hybride combine la sémantique, avec les vecteurs, et le matching exact des termes et numéros d’articles avec BM25 ; les deux scores sont fusionnés par Reciprocal Rank Fusion pour garder les cinq meilleurs articles. Le modèle reçoit **uniquement** ces articles et une consigne stricte : si la réponse n’y est pas, dire qu’on ne sait pas. Enfin la réponse revient avec la liste `cited_article_ids`, que l’utilisateur peut cliquer pour lire le texte officiel.

Plutôt que de le décrire, je vous le montre. **Une précision d’honnêteté : cette démo utilise des données de fixtures scriptées, sans LLM en direct. Elle illustre le comportement du pipeline, elle ne remplace pas une exécution cloud.**

### Temps 1 · Utiliser (1 min 15)

&#91;Cliquer sur l’onglet « ① Utiliser », puis sur « Lancer l’ingestion démo ».\]

Je lance une ingestion sur la source `fr-legifrance`. Regardez : chaque étape s’allume tour à tour. En dessous, trois cases : ce que l’étape **reçoit**, ce qu’elle **fait**, ce qu’elle **produit**. On passe de douze documents découverts à quarante-deux articles découpés, puis quarante-deux vecteurs, puis quarante-deux articles indexés.

### Temps 2 · Valider (1 min)

&#91;Cliquer sur « Voir la validation » ou sur l’onglet « ② Valider ».\]

Je ne veux pas qu’on me croie sur parole. Chaque étape passe des contrôles automatiques : le hash est-il calculé pour tous les fichiers ? Y a-t-il des doublons ? Le schéma Pydantic est-il valide pour chaque article ? La langue détectée est-elle bien le français ? Chaque vecteur fait-il bien 1024 dimensions ? Treize contrôles, tous cochés. C’est cette logique, vérifier à chaque étape, qui rend le pipeline fiable.

### Temps 3 · Attribuer (1 min 45)

&#91;Cliquer sur « ③ Attribuer », puis sur la question « Préavis de démission ».\]

Posons une question : « Quel est le préavis légal de démission ? ». À gauche, la réponse, avec l’article cité. À droite, ce qui a été retrouvé, avec les scores de la recherche hybride, puis la **chaîne de provenance** : la réponse renvoie à l’article, qui renvoie au document brut archivé, qui renvoie à la source officielle, avec son hash et son adapter.

&#91;Cliquer sur « Inspecter le texte ».\]

Je peux lire le texte de loi original. Chaque affirmation est donc vérifiable.

&#91;Cliquer sur la question « Hors corpus ».\]

Et le test le plus important : une question hors sujet. Aucun article ne dépasse le seuil de pertinence, donc **aucune citation, donc aucune réponse**. Le système préfère se taire plutôt qu’inventer. C’est exactement mon fil rouge.

---

## 5. Hannibal V2 (2 min 30) · Section « Hannibal V2 »

&#91;Scroller jusqu’à l’entonnoir.\]

Passons au deuxième volet. L’objectif : fournir à E-Tafakna des données de qualité pour fine-tuner un petit modèle de rédaction contractuelle.

On part de **mille deux cent quatre-vingt-quatre fichiers bruts**, en PDF, DOCX et DOC, sur quatorze pays. Trois cent un échouent à l’extraction, par exemple des .doc corrompus ; quatre-vingt-deux sont rejetés au contrôle qualité, parce que ce sont des formulaires ou des textes trop courts ; trois sont des doublons détectés par hash. Après re-labellisation manuelle, il reste **neuf cent quatre-vingt-deux contrats valides** : 616 en anglais, 196 en français et 170 en arabe.

On anonymise les données personnelles : un nom devient `[PLACEHOLDER]`, une date `[DATE]`, un téléphone `[PHONE]`, un email `[EMAIL]`. Trente-six contrats suspects sont isolés, et quarante partent en revue chez les juristes. Puis un équilibrage, avec une graine fixe à 42, donne une base de trois cent soixante-quinze exemples.

&#91;Scroller sur le tableau des datasets.\]

On y ajoute **quatre datasets spécialisés**, soit cent quarante-quatre exemples : A, du besoin client vers un plan de contrat ; B, du plan vers des clauses structurées ; C, de l’intention vers la rédaction d’une clause ; D, du contrat vers la détection de risques. Trois cent soixante-quinze plus cent quarante-quatre : **cinq cent dix-neuf exemples**, au format Alpaca JSONL, répartis en quatre cent cinquante et un pour l’entraînement et soixante-huit pour la validation.

---

## 6. Architecture et stack (1 min 30) · Section « Architecture »

&#91;Scroller sur les trois cartes d’architecture.\]

Un mot sur l’architecture, qui repose sur une séparation des responsabilités. **OVH Compute** exécute les scrapers, l’OCR et les modèles auto-hébergés : les PDF bruts n’en sortent jamais. **PostgreSQL et MinIO** sont le système de référence : versions, horodatage, hashes et archives. **Azure AI Search** n’est qu’un modèle de lecture dérivé : le texte des articles et leurs vecteurs.

Côté technologies : Python et FastAPI pour le backend, React, Vite et TypeScript pour la console, Docker Compose pour l’infrastructure, et les outils d’extraction que vous voyez dans les cartes. Les barres de compétences montrent où j’ai progressé le plus, et aussi où il me reste du chemin : le déploiement cloud, par exemple, est à 55 %.

---

## 7. Chronologie (1 min) · Section « Chronologie »

&#91;Scroller sur le Gantt.\]

Le stage a duré neuf semaines, avec les deux volets menés en parallèle. Legal Brain : architecture en semaine 1, adapters en semaines 2 et 3, enrichissement en semaine 4, console et `/chat` en semaine 5, validation en semaine 6. Hannibal : collecte en semaines 1 et 2, extraction et anonymisation en 3 et 4, contrôle qualité et équilibrage en 5 et 6, génération en 7 et 8, livraison en semaine 9.

---

## 8. Bilan et perspectives (1 min 30) · Section « Bilan »

&#91;Scroller jusqu’aux trois cartes de bilan.\]

Le bilan en trois points. **Legal Brain est livré et démontrable** : vingt-neuf sources, le protocole SourceAdapter, un `/chat` avec citations, une console, une démo hors ligne. **Hannibal V2 est livré et documenté** : cinq cent dix-neuf exemples, quatre datasets, un corpus nettoyé et anonymisé.

Je tiens aussi à nommer les **limites** : les validations cloud sur OVH et Azure sont conditionnelles ; la revue PII et juridique de soixante-seize fichiers est en attente ; le fine-tuning n’a pas encore été lancé ; et la démo tourne en mode fixtures.

&#91;Scroller sur les six perspectives.\]

Les perspectives en découlent : finaliser le cloud, remplacer les composants mockés par des LLM de production, terminer la revue juridique, lancer le fine-tuning avec son évaluation, relier Legal Brain et Hannibal, et ajouter un graphe de citations pour lier les lois abrogées à la jurisprudence.

---

## 9. Conclusion (30 s)

&#91;Remonter sur la citation finale.\]

Pour conclure : ce stage a posé deux fondations pour qu’une IA juridique soit crédible. Côté Legal Brain, des lois structurées et citables ; côté Hannibal, des données contractuelles de qualité. Le modèle ne doit pas inventer le droit, et ces bases documentaires donnent les moyens de le prouver.

Merci de votre attention. Je suis à votre disposition pour vos questions.

---

## Annexe A · Questions probables et réponses préparées

**« Votre démo utilise-t-elle un vrai LLM ? »** Non, elle tourne sur des fixtures scriptées, et la page l’indique. Le but est de démontrer la logique : validation à chaque étape, citations, refus de répondre hors corpus. Le branchement sur des LLM de production fait partie des perspectives.

**« Pourquoi découper par article et non par morceaux fixes ? »** Parce que l’article est l’unité du droit : il a un numéro, un parent, un sens juridique complet. Un découpage aveugle coupe des phrases et rend la citation impossible à vérifier.

**« Comment évitez-vous les hallucinations ? »** Trois garde-fous : le contexte du modèle est limité aux articles retrouvés, une consigne l’oblige à dire qu’il ne sait pas, et sans article au-dessus du seuil de pertinence, il n’y a pas de réponse.

**« 1024 ou 3072 dimensions ? »** BGE-M3 produit 1024 dimensions et sert pour la démo ; text-embedding-3-large produit 3072 dimensions et est la cible avec Azure. La dimension de l’index doit simplement correspondre au modèle choisi.

**« Comment ajoute-t-on un pays ? »** Un fichier YAML de configuration et un adapter qui implémente les quatre méthodes du protocole. Le pipeline central n’est pas modifié.

**« Que reste-t-il avant le fine-tuning ? »** La revue PII et juridique des soixante-seize fichiers en attente (trente-six suspects et quarante envoyés aux juristes), puis le lancement de l’entraînement et son évaluation.

**« Que feriez-vous différemment ? »** Je valideraisle déploiement cloud plus tôt dans le planning : c’est le point le moins avancé, à 55 %.

## Annexe B · Points à vérifier avant le jour J

- **Chiffres de l’entonnoir** : 1 284 − 301 − 82 − 3 donne 898, et non 982. L’écart vient de la re-labellisation manuelle ; préparez le nombre exact de fichiers récupérés pour répondre si on vous interroge.
- **Dimension d’embedding** : dites toujours « 1024 pour la démo, 3072 pour la cible Azure ».
- **Démo** : testez les trois onglets sur la machine de soutenance, avec ou sans connexion (la page utilise la police Google Fonts mais fonctionne sans).
- **Ordre des clics en démo** : Utiliser, puis Valider, puis Attribuer ; terminez toujours par la question hors corpus.
