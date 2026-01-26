# Hackaton_Ecole_nationale_des_chartes_Projet_SciencesPo

Repository du projet "Quand Archelec rencontre l'IA" proposé par Sciences Po pour le hackaton du master Humanités numériques organisé par l'Ecole nationale des Chartes en janvier 2026. Ce projet a été mené par Maria Kirbasova, Charles Planques et Manon Remot.


Objectif : Génération automatique de métadonnées à partir de professions de foi électorales françaises de 2017 à 2024.


<img width="1885" height="299" alt="logo-chartes-psl-coul" src="https://github.com/user-attachments/assets/9788ad7c-99af-4da7-8e50-dca1e762177c" />


<img width="770" height="306" alt="capture-d-ecran-2026-01-05-110708" src="https://github.com/user-attachments/assets/078ae061-7b80-4bba-8fe5-3ddee1f0aa0a" />


## Partie 1 - Extraction de textes à partir des pdfs

L'ensemble des données comprend **19 194 documents** au format PDF.

Liens d'accès : 
- 2017
https://github.com/regardscitoyens/professions-foi-candidats/tree/master/documents/LG17

- 2020
https://github.com/regardscitoyens/professions-foi-candidats/tree/master/documents/LG20
                     
-2022
https://github.com/regardscitoyens/professions-foi-candidats-2021/tree/main/documents/LG22

- 2024
https://github.com/regardscitoyens/professions-foi-candidats-2021/tree/main/documents/LG24


Les documents _falc ont été supprimés en premier lieu de la base de données initiale, car ces fichiers sont complémentaires aux professions de foi déjà existantes et ne font que doubler les informations. Après cette procédure, la base de données a été réduite à **14 631 documents**.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/OCR/Traitement%20des%20donn%C3%A9es.ipynb

**Methode 1**

La bibliothèque **PyMuPDF (fitz)** est utilisée pour l’extraction du texte à partir des fichiers PDF. Elle permet un traitement rapide et efficace des documents, en donnant accès au contenu textuel structuré par pages et blocs, et a été appliquée au corpus initial.
 
Un résultat satisfaisant a été obtenu pour la plupart des fichiers PDF. **3085 fichiers** sur 14 631 se sont avérés être des fichiers PDF dont il était impossible d'extraire le texte, car celui-ci faisait partie intégrante de l'image. Pour ces fichiers, une méthode supplémentaire d'extraction de texte a été utilisée.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/OCR/SP_pymupython_OCR.ipynb

**Methode 2**

Pour les documents dont le texte fait partie de l'image et ne peut être copié de manière classique à l'aide des bibliothèques simples de traitement des documents PDF, nous avons utilisé la technologie de reconnaissance optique de caractères (OCR) basée sur le modèle Mistral OCR. Plus précisément, nous avons utilisé le modèle **« mistral-ocr-latest »**. Cette méthode est plus lente et payante, mais elle donne de bons résultats.

Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/OCR/SP_Mistral_OCR.ipynb

Exemples de résultats obtenus: 

Methode 1 
| filename                               | page_1 | page_2 |
|----------------------------------------|--------|--------|
| LG17-1-1-BONNOT-10-tour1-profession_foi.pdf | Vincent GUERIN<br>Gilbert BONNOT<br>... | Ce que nous voulons<br>... |


Methode 2 
| filename | page_1 | page_2 |
|---------|--------|--------|
| LG17-1-1-BLATRIX-CONTAT-2-tour1-profession_foi.pdf | Florence BLATRIX-CONTAT — présentation personnelle et parcours politique... | Programme législatif et engagements (gauche, justice sociale, écologie, services publics)... |



## Partie 2 - Prédiction et classification des informations par appel à un LLM (Large language model)

### 2.0 - Démarche

Nous avons appelé (utilisé) des LLMs (modèles de langage comme ChatGPT ou Mistral) de façon à recevoir une classification des informations extraites du texte issu des professions de foi. Pour ce faire, nous avons appelé les modèles sur un prompt précisant le contenu et la forme attendus pour des sous-ensembles (batch) de données.

L'appel au LLM se fait pour chaque batch. On demande au modèle de retourner un texte sous un format particulier qui permet ensuite d'en automatiser la mise sous forme de fichier csv (tableur) réutilisable. Le format est un enjeu très important. En effet, il doit être suffisamment simple pour qu'un programme le transforme facilement en csv, et très stable de façon à pouvoir toujours utiliser la même fonction pour faire la transformation. 

C'est la raison pour laquelle nous avons améliorer notre prompt en le testant sur un fichier-échantillon constitué de 5 professions de foi. 

### 2.1 - Écriture du prompt

Nous avons réalisé plusieurs écritures de prompts en essayant à chaque fois d'en améliorer la qualité. En effet, un prompt de bonne qualité
et détaillé permet d'obtenir de meilleurs résultats. 

Les fournisseurs de LLM ont mis à disposition des guides  : 
- Mistral : https://colab.research.google.com/github/mistralai/cookbook/blob/main/mistral/prompting/prompting_capabilities.ipynb
- OpenAI : https://colab.research.google.com/github/EnkrateiaLucca/oreilly_live_training_llm_apps/blob/main/notebooks/1.0-Intro-ChatGPT-API-prompt-basics.ipynb


La version finale du prompt est disponible sur le GitHub : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/Tests_prompt/prompt_final_SciencesPo

### 2.2 - Test du prompt sur Mistral Large 3 et Ministral 3 3B

Mistral Large 3 est le modèle généraliste le plus performant proposé par Mistral, c'est pourquoi nous l'avons choisi pour soumettre les différentes versions de notre prompt.

Nous voulions également observer ce qu'un modèle moins puissant mais présenté comme plus rapide était capable de faire. C'est pourquoi nous avons choisi Ministral 3 3B également proposé par Mistral.

Pour faire nos tests, nous avons constitué un fichier-échantillon csv composé de 5 professions de foi. Nous avons ensuite comparé les résultats obtenus avec les professions de foi en prenant soin de noter quelles informations manquaient, les éventuelles hallucinations etc. 

Fichier-échantillon : https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/Test_prompt/fichier_echantillon_5_test.csv


Résultats des tests de la version finale du prompt : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/tree/main/Tests_prompt


⚠️ Il faut nécessairement une clé API pour tester les modèles. 


## Partie 3 - Tests finaux

Une fois le prompt écrit, nous avons élargi l'ensemble des modèles sur lesquels le tester.

**Tableau des modèles sélectionnés** 

 | Mistral            | OpenAI |
|-----------------    |---------------|
| Mistral Large 3     | GPT-5 nano    |
| Magistral Small 1.2 | Chatbot ChatGPT|
| Ministral 3 3B      

Il existe d'autres modèles mis à disposition par : 
- Mistral : https://docs.mistral.ai/getting-started/models
- OpenAI : https://platform.openai.com/docs/models
 
### Étape 1 - Batchs

Afin de juger au mieux la performance de ces modèles, nous avons décidé de constituer un échantillon plus conséquent. Pour ce faire nous avons utilisé le procédé batch (découpage du set de données en sous-ensembles).
 
Code : 
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/tree/main/Batch

### Étape 2 - Tests finaux

Les tests finaux ont étés réalisés en suivant le même code.

Code : https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/Tests_finaux/code_test_batch_mistralipynb.ipynb


## Partie 5 - Évaluation des résultats

### 5.0 Performances

Pour évaluer les résultats, nous avons créé à la main deux fichiers CSV témoins : un pour chaque batch0 (premier sous-ensemble arbitrairement choisi pour chaque OCR). Nous avons ensuite filtré les résultats grâce à un correcteur de CSV que nous avons codé. 

Code : https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/blob/main/Tests_finaux/Correcteur_csv.ipynb


Ce code nous a également permis d'obtenir le taux d'erreur pour chaque modèle sur chacun des deux batchs. Nous avons réuni ces résultats dans le tableau ci-dessous. 


|                     | Bacth0_OCR_Mistral                                                                 | Bacth0_OCR_fitz                                                                |
|---------------------|--------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| Mistral_Large_3     | Différences trouvées: 106/286 cellules (37.06%)<br>Colonnes manquantes: 5             | Différences trouvées: 141/341 cellules (41.35%)<br>Colonnes manquantes: 0       |
| Mistral_Small       | Différences trouvées: 64/341 cellules (18.77%)<br>Colonnes manquantes: 0              | Différences trouvées: 43/341 cellules (12.61%)<br>Colonnes manquantes: 0        |
| Ministral_3B        | Différences trouvées: 133/319 cellules (41.69%)<br>Colonnes manquantes: 2             | Différences trouvées: 210/275 cellules (76.36%)<br>Colonnes manquantes: 6       |
| ChatGPT             | Différences trouvées: 118/341 cellules (34.60%)<br>Colonnes manquantes: 0             | NR                                                                             |

A partir des ces résultas, nous avons pu calculer le taux d'accuracy global pour chaque modèle. Les voici explicités ci-dessous.

| Modèle           | Accuracy globale |
|------------------|------------------|
| Mistral_Large_3  | 60.83%           |
| Mistral_Small    | 84.31%           |
| Ministral_3B     | 41.99%           |
| ChatGPT          | 65.40%           |

A noter que nous avons développé un autre procédé pour calculer l'accuracy des modèles testés. Il est également disponsible sur ce GitHub :
https://github.com/thecoolkided/Hackaton_Sciences_Po_Professions_de_foi/tree/main/code_evaluation_resultats_LLM

### 5.1 Format
Les modèles ont performé différemment en termes de qualité de format:
* Mistral Large ne respecte pas le format demandé mais retourne une réponse organisée de manière stable et réutilisable.
* Mistral Small ne respecte pas non plus le format demandé mais retourne une réponse organisée et réutilisable. Nous n'avons pas eu l'occasion d'en tester la stabilité.
* Ministral ne respecte pas le format demandé et retourne des réponses au format instable, ce qui l'automatisation de la mise en forme difficile.
* ChatGPT ne respecte pas non plus le format demandé et retourne des réponses à la fois instables et incomplètes.

  

## Conclusion 

En nous fiant à ces résultats préliminaires, Mistral Small apparaît être le modèle le plus fiable. Mistral Small est un des modèles généralistes proposés par Mistral. Il était pourtant décrit comme moins performant que le modèle le plus puissant, Mistral Large 3, ce qui démontre l'importance du benchmarking lorsqu'on s'attèle à un telle tâche.

Nous recommandons de laisser de côté ChatGPT. Malgrés des performances correctes sur un des batchs, il n'est pas conçu pour ce genre de travail et la mise à l'échelle se montrerait plus que complexe au vu de la quantité de données à traiter.
En effet, les données en sortie produites par ChatGPT n'ont pas toujours le même format, ce qui pose problème puisque pour une mise à l'échelle, le format des données en sortie est un facteur primordial de la qualité du résultat. 
De fait, un programme comme ChatGPT dont le format des sorties n'est pas toujours le même est peu (voire pas) pertinent parce qu'il ne sera pas possible de transformer les résultats vers un format csv réutilisable. 

Bien que sélectionné, nous n'avons pu obtenir de résultats pour GPT5-nano, raison pour laquelle il ne figure pas dans les tableaux récapitulatifs.

Enfin, pour ce qui est de la mise à l'échelle, deux options sont possibles. 
1. Mettre à l'échelle batch par batch. Le processus sera long mais il aura le mérite d'avoir été testé et approuvé.
2. Mettre à l'échelle en traitant plusieurs batchs en même temps. Le processsus sera plus rapide mais cela requerra une modification du code_test_batch_mistralipynb ainsi que du correcteur de CSV.




