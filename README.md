# Hackaton_Sciences_Po_Professions_de_foi
Repo du projet professions de foi de Sciences Po. Détails des méthodes utilisées.

## Partie 1 - Extraction de textes à partir des pdfs

L'ensemble des données comprend **19 194 documents** au format PDF.

Les documents _falc ont été supprimés en premier lieu de la base de données initiale, car ces fichiers sont complémentaires aux professions de foi déjà existantes et ne font que doubler les informations. Après cette procédure, la base de données a été réduite à **14 631 documents**.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/OCR/Traitement%20des%20donn%C3%A9es.ipynb

**Methode 1**

La bibliothèque **PyMuPDF (fitz)** est utilisée pour l’extraction du texte à partir des fichiers PDF. Elle permet un traitement rapide et efficace des documents, en donnant accès au contenu textuel structuré par pages et blocs, et a été appliquée au corpus initial.
 
Un résultat positif a été obtenu pour la plupart des fichiers PDF. **3085 fichiers** sur 14 631 se sont avérés être des fichiers PDF dont il était impossible d'extraire le texte, car celui-ci faisait partie intégrante de l'image. Pour ces fichiers, une méthode supplémentaire d'extraction de texte a été utilisée.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/OCR/SP_pymupython_OCR.ipynb

**Methode  2**

Pour les documents dont le texte fait partie de l'image et ne peut être copié de manière classique à l'aide des bibliothèques simples de traitement des documents PDF, nous avons utilisé la technologie de reconnaissance optique de caractères (OCR) basée sur le modèle Mistral OCR. Plus précisément, nous avons utilisé le modèle **« mistral-ocr-latest »**. Cette méthode est plus lente et payante, mais elle donne un résultat net.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/OCR/SP_Mistral_OCR.ipynb

Exemples: 

Methode 1 
| filename                               | page_1 | page_2 |
|----------------------------------------|--------|--------|
| LG17-1-1-BONNOT-10-tour1-profession_foi.pdf | Vincent GUERIN<br>Gilbert BONNOT<br>... | Ce que nous voulons<br>... |


Methode 2 
| filename | page_1 | page_2 |
|---------|--------|--------|
| LG17-1-1-BLATRIX-CONTAT-2-tour1-profession_foi.pdf | Florence BLATRIX-CONTAT — présentation personnelle et parcours politique... | Programme législatif et engagements (gauche, justice sociale, écologie, services publics)... |


## Partie 2 - Écriture de prompts 

Nous avons réalisé plusieur écritures de prompts en essayant à chaque fois d'en améliorer la qualité. En effet, un prompt qualitatif
et détaillé permet d'obtenir de meilleurs résultats. 

Les fournisseurs de LLM ont mis à disposition des guides  : 
- Mistral : https://colab.research.google.com/github/mistralai/cookbook/blob/main/mistral/prompting/prompting_capabilities.ipynb
- OpenAI : https://colab.research.google.com/github/EnkrateiaLucca/oreilly_live_training_llm_apps/blob/main/notebooks/1.0-Intro-ChatGPT-API-prompt-basics.ipynb

  
⚠️ Il faut nécessairement une clé API.

La version finale du prompt est disponible sur le GitHub : https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/promptSciencesPo


## Partie 3 - Test des prompts sur différents modèles 

Tests du prompt finale sur différents modèles de différents LLM de déterminer celui qui serait le plus à même de répondre à nos besoins.
La sélection a été faite en fonction de la performance affichée et de la rapidité du modèle. 

Pour ces tests, nous avons constitué un fichier-échantillon csv composé de 5 professions de foi. Nous avons ensuite comparé les résultats obtenus avec les professions de foi
en prenant soin de noter quelles informations manquaient, les éventuelles hallucinations etc. 

Liste des mpodèles proposés mis à disposition par : 
- Mistral : https://docs.mistral.ai/getting-started/models
- OpenAI : https://platform.openai.com/docs/models


### Tableau des modèles testés 

 | Mistral            | Autre |
|-----------------    |---------------|
| Mistral_Large_3     | GPT-5_nano    |
| Magistral_Small_1.2 | ChatGPT       |
| Ministral_3_3B      

Les résultats des tests sont disponibles dans le fichier Tests : https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/tree/main/Tests


## Partie 4 - Tests finaux

### Étape 1 - Batchs

Réalisation de batchs afin de lancer les tests des modèles choisis sur les échantillons finaux. 

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/tree/main/Batch

### Étape 2 - Tests

Les tests finaux ont étés réalisés en suivant le même script (code).

Code : https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/code_traitement_professions_de_foi_par_categorisation.ipynb


## Partie 5 - Évaluation des résultats

Pour évaluer les résulats des tests nous les avons soumis à une évaluation.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/tree/main/code_evaluation_resultats_LLM

#### Métriques : 

Methode 1 : OCR PDF

| model           | cell_accuracy |
|-----------------|---------------|
| Mistral_Large_3 | 0.897361      |

Methode 2 : OCR Mistral

| model              | cell_accuracy |
|--------------------|---------------|
| Mistral_Large_3    | 0.973607      |
| ChatBot_ChatGPT    | 0.413490      |
| MiniMistral        | 0.346041      |


