---
title: Créer votre première expérience de classification de ML automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment effectuer l’apprentissage d’un modèle de classification et le déployer avec le Machine Learning automatisé dans Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: ecad41097786a40f7c605a686f085136856c950a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581577"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Didacticiel : Créer votre premier modèle de classification avec Machine Learning automatisé
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Dans ce tutoriel, vous allez découvrir comment créer votre première expérience de Machine Learning automatisé dans Azure Machine Learning Studio sans écrire une seule ligne de code. Cet exemple crée un modèle de classification pour prédire si un client souscrit à un compte à terme auprès d’une institution financière.

Avec le machine learning automatisé, vous pouvez automatiser des tâches fastidieuses. Le machine learning automatisé itère rapidement sur de nombreuses combinaisons d’algorithmes et d’hyperparamètres pour vous aider à trouver le meilleur modèle basé sur une métrique de réussite de votre choix.

Dans ce tutoriel, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un espace de travail Machine Learning.
> * Exécuter une expérience de machine learning automatisé.
> * Voir les détails de l'expérience.
> * Déployer le modèle.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree).

* Téléchargez le fichier de données [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). La colonne **y** indique si un client a souscrit à un compte à terme, qui est ensuite identifié comme colonne cible pour les prédictions de ce tutoriel. 

## <a name="create-a-workspace"></a>Créer un espace de travail

Un espace de travail Azure Machine Learning est une ressource fondamentale du cloud que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le service. 

Vous créez un espace de travail par le biais d’Azure Machine Learning Studio, une console web pour la gestion de vos ressources Azure.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Prenez note de votre **espace de travail** et de votre **abonnement**. Vous en aurez besoin pour être sûr de créer votre expérience au bon endroit. 

## <a name="create-and-run-the-experiment"></a>Créer et exécuter l’expérience

Vous effectuez les étapes de configuration et d’exécution d’expérience suivantes dans Azure Machine Learning Studio, une interface centralisée qui comprend des outils de Machine Learning permettant de mettre en œuvre des scénarios de science des données pour les utilisateurs de science des données de tous niveaux de compétence. Studio n’est pas prise en charge par les navigateurs Internet Explorer.

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. Sélectionnez votre abonnement et l’espace de travail que vous avez créé.

1. Sélectionnez **Prise en main**.

1. Dans le volet gauche, sélectionnez **Automated ML** (ML automatisé) sous la section **Author** (Créer).

   Puisqu’il s’agit de votre première expérience de ML automatisé, vous verrez une liste vide et des liens vers la documentation.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Sélectionnez **Nouvelle exécution de ML automatisé**. 

1. Pour créer un jeu de données, sélectionnez **À partir de fichiers locaux** dans la liste déroulante **+ Créer un jeu de données**. 

    1. Sélectionnez **Parcourir**.
    
    1. Choisissez le fichier **bankmarketing_train.csv** sur votre ordinateur local. Il s’agit du fichier que vous avez téléchargé en tant que [prérequis](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Sélectionnez **Tabulaire** comme type de jeu de données. 

    1. Donnez un nom unique à votre jeu de données et indiquez éventuellement une description. 

    1. En bas à gauche, sélectionnez **Next** (Suivant) pour le charger dans le conteneur par défaut qui a été configuré automatiquement lors de la création de votre espace de travail.  
    
       Une fois le chargement terminé, le formulaire Settings and preview (Paramètres et aperçu) est prérenseigné en fonction du type de fichier. 
       
    1. Vérifiez que le formulaire **Settings and preview** est renseigné comme ci-dessous, puis sélectionnez **Next** (Suivant).
        
        Champ|Description| Valeur pour le tutoriel
        ---|---|---
        Format de fichier|Définit la disposition et le type des données stockées dans un fichier.| Delimited
        Délimiteur|Un ou plusieurs caractères utilisés pour spécifier la limite entre&nbsp; des régions distinctes et indépendantes dans du texte brut ou d’autres flux de données. |Comma
        Encodage|Identifie la table de schéma bits/caractères à utiliser pour lire votre jeu de données.| UTF-8
        En-têtes de colonne| Indique la façon dont les éventuels en-têtes du jeu de données sont traités.| Tous les fichiers ont les mêmes en-têtes
        Ignorer les lignes | Indique le nombre éventuel de lignes ignorées dans le jeu de données.| Aucun

    1. Le formulaire **Schema** permet de configurer davantage vos données pour cette expérience. Pour cet exemple, sélectionnez le bouton bascule correspondant à la caractéristique **day_of_week**, afin de ne pas l’inclure pour cette expérience. Sélectionnez **Suivant**.

        ![Configuration de l’onglet Aperçu](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

    1. Dans le formulaire **Confirmer les détails**, vérifiez que les informations correspondent à celles qui ont été précédemment renseignées sur les formulaires **Informations de base** et **Paramètres et aperçu**.
    1. Sélectionnez **Créer** pour terminer la création de votre jeu de données.
    1. Sélectionnez votre jeu de données une fois qu’il apparaît dans la liste.
    1. Passez en revue l’**Aperçu des données** pour vous assurer que vous n’avez pas inclus **day_of_week**, puis sélectionnez **OK**.

    1. Sélectionnez **Suivant**.

1. Remplissez le formulaire **Configurer l’exécution** comme suit :
    1. Entrez le nom suivant pour l’expérience : `my-1st-automl-experiment`

    1. Sélectionnez **y** comme colonne cible, ce que vous souhaitez prédire. Cette colonne indique si le client a souscrit à un compte de dépôt à terme.
    1. Sélectionnez **Create a new compute** (Créer un nouveau calcul), puis configurez la cible de calcul. Une cible de calcul est un environnement de ressources local ou informatique utilisé pour exécuter votre script d’entraînement ou pour héberger votre déploiement de service. Pour cette expérience, nous utilisons un calcul informatique. 

        Champ | Description | Valeur pour le tutoriel
        ----|---|---
        Nom du calcul |Nom unique qui identifie votre contexte de calcul.|automl-compute
        Taille de la&nbsp;machine&nbsp;virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul.|Standard_DS12_V2
        Nœuds min./max. (dans les paramètres avancés)| Pour profiler des données, vous devez spécifier un ou plusieurs nœuds.|Nœuds min. : 1<br>Nœuds max. : 6
  
        1. Sélectionnez **Créer** pour accéder à la cible de calcul. 

            **Quelques minutes sont nécessaires pour achever l’opération**. 

        1. Une fois la création terminée, sélectionnez votre nouvelle cible de calcul dans la liste déroulante.

    1. Sélectionnez **Suivant**.

1. Dans le formulaire **Type de tâche et paramètres**, sélectionnez **Classification** comme type de tâche d’apprentissage automatique.

    1. Sélectionnez **Afficher des paramètres de configuration supplémentaires** et renseignez les champs comme suit. Ces paramètres permettent de mieux contrôler le travail d’entraînement. Sinon, les valeurs par défaut sont appliquées en fonction de la sélection de l’expérience et des données.

        >[!NOTE]
        > Dans ce tutoriel, vous n’allez pas définir un score de métrique ni un nombre de cœurs maximal par itération. Vous n’allez pas non plus bloquer le test des algorithmes.
   
        Configurations&nbsp;supplémentaires|Description|Valeur&nbsp;pour&nbsp;le tutoriel
        ------|---------|---
        Métrique principale| Métrique d’évaluation selon laquelle l’algorithme de Machine Learning sera mesuré.|AUC_weighted
        Caractérisation automatique| Active le prétraitement. Cela comprend le nettoyage automatique des données, la préparation et la transformation pour générer des caractéristiques synthétiques.| Activer
        Algorithmes bloqués | Algorithmes que vous souhaitez exclure du travail de formation| Aucun
        Critère de sortie| Lorsqu’une condition est remplie, la tâche d’entraînement est arrêtée. |Durée&nbsp;de la tâche&nbsp;de formation : 1 <br> Seuil&nbsp;de score&nbsp;de métrique : Aucun
        Validation | Choisissez un type de validation croisée et un nombre de tests.|Type de validation :<br>&nbsp;validation croisée&nbsp;k-fold <br> <br> Nombre de validations : 2
        Accès concurrentiel| Nombre maximal d’itérations parallèles exécutées et de cœurs utilisés par itération| Nombre maximal&nbsp;d’itérations&nbsp;simultanées : 5.<br> Nombre maximal&nbsp;de cœurs&nbsp;par&nbsp;itération : Aucun
        
        Sélectionnez **OK**.

1. Sélectionnez **Créer** pour exécuter l’expérience. L’écran **Détails de l’exécution** s’ouvre et affiche l’**État de l’exécution** au début de la préparation de l’expérience.

>[!IMPORTANT]
> La préparation nécessaire à l’exécution de l’expérience prend **10 à 15** minutes.
> Une fois que l’exécution a commencé, **2-3 minutes supplémentaires sont nécessaires pour chaque itération**.  
> Sélectionnez **Actualiser** périodiquement pour voir l’état de l’exécution à mesure que l’expérience progresse.
>
> Dans un environnement de production, cette durée est un peu plus longue. Toutefois, dans le cadre de ce tutoriel, nous vous suggérons de commencer à explorer les algorithmes testés dans l’onglet Modèles à mesure qu’ils se terminent, pendant que les autres sont encore en cours d’exécution. 

##  <a name="explore-models"></a>Explorer les modèles

Accédez à l’onglet **Modèles** pour voir les algorithmes (modèles) testés. Par défaut, les modèles sont classés par score de métrique à mesure qu’ils se terminent. Pour ce tutoriel, le modèle qui obtient le score le plus élevé d’après la métrique **AUC_weighted** choisie figure en haut de la liste.

En attendant que toutes les modèles d’expérience se terminent, sélectionnez le **Nom de l’algorithme** d’un modèle terminé pour explorer ses performances en détail. 

L’exemple suivant parcourt les onglets **Détails du modèle** et **Visualisations** pour afficher les propriétés, les métriques et les graphiques de performances du modèle sélectionné. 

![Détails sur l’exécution de l’itération](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Déployer le modèle

Dans Azure Machine Learning Studio, le Machine Learning automatisé vous permet de déployer le meilleur modèle en tant que service web en quelques étapes. Le déploiement consiste à intégrer le modèle pour qu’il puisse prédire de nouvelles données et identifier les domaines potentiels d’opportunités. 

Dans le cadre de cette expérience, le déploiement sur un service web signifie que l’établissement financier dispose désormais d’une solution web itérative et scalable pour identifier les clients potentiels d’un compte à terme. 

Une fois l’exécution terminée, revenez à la page **Détails de l’exécution** et sélectionnez l’onglet **Modèles**. Sélectionnez **Actualiser**. 

Dans ce contexte d’expérience, **VotingEnsemble** est considéré comme le meilleur modèle d’après la métrique **AUC_weighted**.  Nous déployons ce modèle, mais nous vous informons que le déploiement prend 20 minutes environ. Le processus de déploiement comporte plusieurs étapes, notamment l’inscription du modèle, la génération de ressources et leur configuration pour le service web.

1. Sélectionnez le bouton **Déployer le meilleur modèle** en haut à droite.

1. Renseignez le volet **Déployer un modèle** de la façon suivante :

    Champ| Valeur
    ----|----
    Nom du déploiement| my-automl-deploy
    Description du déploiement| Déploiement de ma première expérience de Machine Learning automatisé
    Type de capacité de calcul | Sélectionnez une instance de calcul Azure (ACI)
    Activer l’authentification| Désactivez. 
    Utiliser des déploiements personnalisés| Désactivez. Permet de générer automatiquement le fichier de pilote par défaut (script de score) et le fichier d’environnement. 
    
    Pour cet exemple, nous utilisons les valeurs par défaut fournies dans le menu *Avancé*. 

1. Sélectionnez **Déployer**.  

    Un message vert de réussite apparaît en haut de l’écran **Exécuter**, et dans le volet **Modèle recommandé**, un message d’état s’affiche sous **État du déploiement**. Cliquez régulièrement sur **Actualiser** pour vérifier l’état du déploiement.
    
Vous disposez maintenant d’un service web opérationnel pour générer des prédictions. 

Passez aux [**Étapes suivantes**](#next-steps) pour en savoir plus sur l’utilisation de votre nouveau service web et tester vos prédictions à l’aide de la prise en charge d’Azure Machine Learning intégrée dans Power BI.

## <a name="clean-up-resources"></a>Supprimer des ressources

Les fichiers de déploiement sont plus volumineux que les fichiers de données et d’expérimentation. Le coût de leur stockage est donc plus élevé. Supprimez uniquement les fichiers de déploiement pour réduire les coûts associés à votre compte, ou si vous souhaitez conserver vos fichiers d’expérience et d’espace de travail. Dans le cas contraire, supprimez l’intégralité du groupe de ressources, si vous n’envisagez pas d’utiliser les fichiers.  

### <a name="delete-the-deployment-instance"></a>Supprimer l’instance de déploiement

Supprimez uniquement l’instance de déploiement d’Azure Machine Learning Studio si vous souhaitez conserver le groupe de ressources et l’espace de travail pour d’autres tutoriels et à des fins d’exploration. 

1. Accédez à [Azure Machine Learning Studio](https://ml.azure.com/). Accédez à votre espace de travail et, à gauche dans le volet **Ressources**, sélectionnez **Points de terminaison**. 

1. Sélectionnez le déploiement à supprimer et sélectionnez **Supprimer**. 

1. Sélectionnez **Continuer**.

### <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel de Machine Learning automatisé, vous avez utilisé Azure Machine Learning Studio pour créer et déployer un modèle de classification. Pour plus d’informations et pour connaître les étapes suivantes, consultez ces articles :

> [!div class="nextstepaction"]
> [Utiliser un service web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ En savoir plus sur le [prétraitement](how-to-create-portal-experiments.md#preprocess).
+ En savoir plus sur le [profilage des données](how-to-create-portal-experiments.md#profile).
+ En savoir plus sur le [Machine Learning automatisé](concept-automated-ml.md).
+ Pour plus d’informations sur les métriques et les graphiques de classification, consultez l’article [Comprendre les résultats du Machine Learning automatisé](how-to-understand-automated-ml.md#classification).

>[!NOTE]
> Ce jeu de données Bank Marketing est disponible sous licence [Creative Commons (CCO : domaine public)](https://creativecommons.org/publicdomain/zero/1.0/). Tous les droits du contenu individuel de la base de données sont concédés sous [licence du contenu de base de données](https://creativecommons.org/publicdomain/zero/1.0/) et sont disponibles sur [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ce jeu de données était initialement disponible dans la [base de données Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez et P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, Juin 2014.
