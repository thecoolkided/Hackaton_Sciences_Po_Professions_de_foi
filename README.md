# Hackaton_Sciences_Po_Professions_de_foi
Repo du projet professions de foi de Sciences Po. Détails des méthodes utilisées.

## Partie 1 - Extraction de textes à partir des pdfs





## Partie 2 - Écriture de prompts 

Nous avons réalisé plusieur écritures de prompts en essayant à chaque fois d'en améliorer la qualité. En effet, un prompt qualitatif
et détaillé permet d'obtenir de meilleurs résultats. 

Les fournisseurs de LLM ont mis à disposition des guides  : 
- Mistral : https://colab.research.google.com/github/mistralai/cookbook/blob/main/mistral/prompting/prompting_capabilities.ipynb
- OpenAI : https://colab.research.google.com/github/EnkrateiaLucca/oreilly_live_training_llm_apps/blob/main/notebooks/1.0-Intro-ChatGPT-API-prompt-basics.ipynb
⚠️ Il faut nécessairement une clé API 

La version finale du prompt est disponible sur le GitHub sous le nom : promptSciencesPo. 


## Partie 3 - Test des prompts sur différents modèles 

Tests du prompt finale sur différents LLM et différents modèles de LLM afin de déterminer quel LLM et quel modèle seraient les plus à même de répondre à nos besoins.
La sélection a été faite en fonction de la performance affichée et de la rapidité du modèle. 

Pour ces tests, nous avons constitué un fichier-échantillon csv composé de 5 professions de foi. Nous avons ensuite comparé les résultats obtenus avec les professions de foi
en prenant soin de noter quelleq informations manquaient, les éventuelles hallucinations etc. 

Liste des mpodèles proposés mis à disposition par : 
- Mistral : https://docs.mistral.ai/getting-started/models
- OpenAI : https://platform.openai.com/docs/models

### Modèle 1.1 - Mistral Large 3 de Mistral 

Mistral Large 3 est le dernier gros modèle généraliste de chez Mistral. Nous avons choisi de l'utiliser parce qu'il est très performant malgré sa relative lenteur. 

Test sur le fichier-échantillon --> taux de réussite estimé : 92%. 




## Évaluation
