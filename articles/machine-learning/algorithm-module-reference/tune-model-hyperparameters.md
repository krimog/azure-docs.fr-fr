---
title: Optimiser les hyperparamètres du modèle
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Optimiser les hyperparamètres du modèle dans le service Azure Machine Learning pour effectuer un balayage de paramètres sur un modèle afin de déterminer les réglages de paramètres optimaux.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: fd796297bafeb437b55eca7f38cbd7ae55e19b93
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716718"
---
# <a name="tune-model-hyperparameters"></a>Optimiser les hyperparamètres du modèle

Cet article décrit comment utiliser le module Optimiser les hyperparamètres du modèle dans le concepteur Azure Machine Learning (préversion). L’objectif est de déterminer les hyperparamètres optimaux pour un modèle Machine Learning. Le module génère et teste plusieurs modèles avec différentes combinaisons de paramètres. Il compare les métriques à tous les modèles pour obtenir les combinaisons de paramètres. 

Les termes *paramètre* et *hyperparamètre* peuvent prêter à confusion. Les *paramètres* du modèle sont ce que vous définissez dans le volet des propriétés. Ce module effectue essentiellement un *balayage de paramètres* sur les réglages de paramètres spécifiés. Il apprend un ensemble optimal d’_hyperparamètres_, qui peut être différent pour chaque arbre de décision, jeu de données ou méthode de régression. Le processus de recherche de la configuration optimale est parfois appelé *optimisation*. 

Le module prend en charge la méthode suivante pour rechercher les paramètres optimaux d’un modèle : *entraînement et optimisation intégrés*. Dans cette méthode, vous configurez un ensemble de paramètres à utiliser. Vous faites ensuite itérer le module sur plusieurs combinaisons. Le module mesure la justesse jusqu’à ce qu’il trouve un modèle « optimal ». Avec la plupart des modules d’apprentissage, vous pouvez choisir les paramètres à changer au cours du processus d’entraînement et ceux qui doivent rester tels quels.

Selon la durée d’exécution souhaitée du processus d’optimisation, vous pouvez décider de tester de façon exhaustive toutes les combinaisons. Vous pouvez aussi raccourcir le processus en établissant une grille de combinaisons de paramètres et en testant un sous-ensemble aléatoire de la grille de paramètres.

Cette méthode génère un modèle entraîné que vous pouvez enregistrer pour le réutiliser.  

> [!TIP] 
> Vous pouvez effectuer une tâche associée. Avant de commencer l’optimisation, appliquez la sélection des caractéristiques pour déterminer les colonnes ou les variables qui ont la valeur informative la plus élevée.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Comment configurer le module Optimiser les hyperparamètres du modèle  

L’apprentissage des hyperparamètres optimaux pour un modèle Machine Learning nécessite une utilisation intensive de pipelines.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Entraîner un modèle à l’aide d’un balayage de paramètres  

Cette section décrit comment effectuer un balayage de paramètres de base, qui entraîne un modèle à l’aide du module Optimiser les hyperparamètres du modèle.

1.  Ajoutez le module Optimiser les hyperparamètres du modèle à votre pipeline dans le concepteur.

2.  Connectez un modèle non entraîné à l’entrée la plus à gauche. 

3. Définissez l’option **Create trainer mode** (Créer un mode d’apprentissage) sur **Parameter Range** (Plage de paramètres). Utilisez **Range Builder** (Générateur de plage) pour spécifier une plage de valeurs à utiliser dans le balayage de paramètres.  

    Presque tous les modules de classification et de régression prennent en charge un balayage de paramètres intégré. Pour les apprenants qui ne prennent pas en charge la configuration d’une plage de paramètres, vous pouvez tester uniquement les valeurs de paramètres disponibles.

    Vous pouvez définir manuellement la valeur d’un ou plusieurs paramètres, puis balayer les paramètres restants. Cela peut vous faire gagner du temps.

4.  Ajoutez le jeu de données que vous souhaitez utiliser pour l’entraînement et connectez-le à l’entrée du milieu du module Optimiser les hyperparamètres du modèle.  

    Éventuellement, si vous avez un jeu de données étiqueté, vous pouvez le connecter au port d’entrée le plus à droite (**Jeu de données de validation facultatif**). Cela vous permet de mesurer la justesse pendant l’entraînement et l’optimisation.

5.  Dans le volet **Propriétés** du module Optimiser les hyperparamètres du modèle, choisissez une valeur pour **Parameter sweeping mode** (Mode de balayage de paramètres). Cette option contrôle la manière dont les paramètres sont sélectionnés.

    - **Grille entière** : Quand vous sélectionnez cette option, le module effectue une boucle sur une grille prédéfinie par le système, pour essayer différentes combinaisons et identifier le meilleur apprenant. Cette option est utile quand vous ne savez pas quels sont les meilleurs réglages de paramètres et que vous souhaitez essayer toutes les combinaisons de valeurs possibles.

    - **Balayage aléatoire** : Quand vous sélectionnez cette option, le module sélectionne aléatoirement des valeurs de paramètre sur une plage définie par le système. Vous devez spécifier le nombre maximal d’exécutions que le module doit exécuter. Cette option est utile quand vous souhaitez augmenter les performances du modèle à l’aide des métriques de votre choix, tout en économisant les ressources de calcul.    

6.  Pour **Label column** (Colonne d’étiquette), ouvrez le sélecteur de colonne pour choisir une colonne d’étiquette unique.

7.  Choisissez le nombre d’exécutions :

    1. **Nombre maximal d’exécutions lors d’un balayage aléatoire** : Si vous choisissez un balayage aléatoire, vous pouvez spécifier le nombre de fois que le modèle doit être entraîné, en utilisant une combinaison aléatoire de valeurs de paramètre.

    2. **Nombre maximal d’exécutions sur une grille aléatoire** : Cette option contrôle également le nombre d’itérations sur un échantillonnage aléatoire des valeurs de paramètre, mais les valeurs ne sont pas générées de façon aléatoire à partir de la plage spécifiée. Au lieu de cela, le module crée une matrice de toutes les combinaisons possibles des valeurs de paramètre. Il effectue ensuite un échantillonnage aléatoire sur la matrice. Cette méthode est plus efficace et moins sujette aux suréchantillonnages ou sous-échantillonnages régionaux.

8.  Pour **Classement**, choisissez une métrique unique à utiliser lors du classement des modèles.

    Quand vous exécutez un balayage de paramètres, le module calcule toutes les métriques applicables pour le type de modèle et les retourne dans le rapport **Résultats de balayage**. Le module utilise des métriques distinctes pour les modèles de régression et de classification.

    Toutefois, la métrique que vous choisissez détermine la façon dont les modèles sont classés. Seul le premier modèle, tel que classé par la métrique choisie, est généré en tant que modèle entraîné à utiliser pour le scoring.

9.  Pour **Random seed** (Valeur de départ aléatoire), entrez un nombre à utiliser pour commencer le balayage de paramètres. 

10. Exécuter le pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Résultats de l’optimisation des hyperparamètres

Quand l’entraînement est terminé :

+ Pour voir un ensemble de métriques de justesse pour le meilleur modèle, cliquez avec le bouton droit sur le module, sélectionnez **Résultats du balayage**, puis sélectionnez **visualiser**.

    La sortie inclut toutes les métriques de justesse qui s’appliquent au type de modèle, mais la métrique que vous avez sélectionnée pour le classement détermine le modèle considéré « optimal ».

+ Pour utiliser ce modèle pour le scoring dans d’autres pipelines sans avoir à répéter le processus d’optimisation, cliquez avec le bouton droit sur la sortie du modèle et sélectionnez **Save as Trained Model** (Enregistrer en tant que modèle entraîné). 


## <a name="technical-notes"></a>Notes techniques

Cette section contient des informations et des conseils d’implémentation.

### <a name="how-a-parameter-sweep-works"></a>Fonctionnement du balayage de paramètres

Lorsque vous configurez un balayage de paramètres, vous définissez l’étendue de votre recherche. La recherche peut utiliser un nombre fini de paramètres sélectionnés de façon aléatoire. Elle peut représenter également une recherche exhaustive sur un espace de paramètre que vous définissez.

+ **Balayage aléatoire** : Cette option entraîne un modèle en utilisant un nombre défini d’itérations. 

  Vous spécifiez une plage de valeurs à itérer, puis le module utilise un sous-ensemble de ces valeurs choisi de façon aléatoire. Les valeurs sont choisies avec remplacement ; les nombres précédemment choisis au hasard ne sont pas supprimés du pool de nombres disponibles. Ainsi, la probabilité qu’une valeur soit sélectionnée reste la même pour toutes les passes.  

+ **Grille entière** : L’option consistant à utiliser la totalité de la grille signifie que chaque combinaison est testée. Cette option est la plus complète, mais elle nécessite le plus de temps. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Contrôle de la longueur et de la complexité de l’entraînement

L’itération sur plusieurs combinaisons de paramètres pouvant prendre du temps, le module fournit plusieurs façons de limiter le processus :

+ Limitez le nombre d’itérations utilisées pour tester un modèle.
+ Limitez l’espace de paramètre.
+ Limitez le nombre d’itérations et l’espace de paramètre.

Nous vous recommandons de définir les paramètres sous forme de pipeline pour déterminer la méthode d’entraînement la plus efficace sur un jeu de données et un modèle particuliers.

### <a name="choosing-an-evaluation-metric"></a>Choix d’une métrique d’évaluation

À la fin des tests, le modèle présente un rapport contenant la justesse de chaque modèle afin que vous puissiez passer en revue les résultats des métriques :

- Un ensemble uniforme de métriques est utilisé pour tous les modèles de classification binaires.
- La justesse est utilisée pour tous les modèles de classification à plusieurs classes.
- Un ensemble différent de métriques est utilisé pour les modèles de régression. 

Toutefois, pendant l’entraînement, vous devez choisir une *seule* métrique à utiliser pour classer par ordre de priorité les modèles qui sont générés pendant le processus d’optimisation. Vous constaterez peut-être que la meilleure métrique varie en fonction du problème métier, ainsi que du coût des faux positifs et des faux négatifs.

#### <a name="metrics-used-for-binary-classification"></a>Métriques utilisées pour la classification binaire

-   **Justesse** : proportion des résultats réels parmi tous les cas.  

-   **Précision** : proportion des résultats réels parmi les résultats positifs.  

-   **Rappel** : fraction de tous les résultats corrects par rapport à tous les résultats.  

-   **Score F** : mesure qui équilibre la précision et le rappel.  

-   **AUC** : valeur représentant la zone sous la courbe quand les faux positifs figurent sur l’axe X et que les vrais positifs figurent sur l’axe Y.  

-   **Perte logarithmique moyenne** : différence entre deux distributions des probabilités : la réelle et celle du modèle.  

#### <a name="metrics-used-for-regression"></a>Métriques utilisées pour la régression

-   **Erreur absolue moyenne** : moyenne de toutes les erreurs dans le modèle, l’*erreur* correspondant à la distance de la valeur prédite à la valeur réelle. Elle est souvent abrégée en *MAE*.  

-   **Racine de l’erreur quadratique moyenne** Mesure la moyenne des carrés des erreurs, puis prend la racine de cette valeur. Elle est souvent abrégée en *RMSE*.  

-   **Erreur absolue relative** Représente l’erreur sous la forme d’un pourcentage de la valeur réelle.  

-   **Erreur quadratique relative** : normalise l’erreur quadratique totale en la divisant par l’erreur quadratique totale des valeurs prédites.  

-   **Coefficient de détermination** : nombre unique qui indique la conformité des données à un modèle. La valeur 1 signifie que le modèle correspond exactement aux données. La valeur 0 signifie que les données sont aléatoires ou qu’elles ne peuvent pas être représentées par le modèle. Il est souvent appelé *r<sup>2</sup>* , *R<sup>2</sup>* ou *R carré*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Modules qui ne prennent pas en charge un balayage de paramètres

Presque tous les apprenants dans Azure Machine Learning prennent en charge la validation croisée avec un balayage de paramètres intégré, ce qui vous permet de choisir les paramètres à définir sous forme de pipeline. Si l’apprenant ne prend pas en charge la définition d’une plage de valeurs, vous pouvez toujours l’utiliser dans la validation croisée. Dans ce cas, une plage de valeurs autorisées est sélectionnée pour le balayage. 


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 

