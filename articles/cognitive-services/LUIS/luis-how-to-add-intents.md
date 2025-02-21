---
title: Ajouter des intentions - LUIS
titleSuffix: Azure Cognitive Services
description: Ajoutez des intentions à votre application LUIS pour identifier des groupes de questions ou de commandes qui ont les mêmes intentions.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467548"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Ajouter des intentions pour déterminer l’intention de l’utilisateur des énoncés

Ajoutez des [intentions](luis-concept-intent.md) à votre application LUIS pour identifier des groupes de questions ou de commandes qui ont la même intention. 

Les intentions sont gérées à partir du volet gauche **Intents**, accessible en cliquant sur la section **Build** de la barre de navigation supérieure. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Ajouter une intention

1. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention).

1. Dans la boîte de dialogue **Create new intent**, entrez le nom de l’intention, `GetEmployeeInformation`, puis cliquez sur **Done**.

    ![Ajouter une intention](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Ajouter un exemple d’énoncé

Les exemples d’énoncés sont des exemples de texte de questions ou de commandes des utilisateurs. Pour l’apprentissage de Language Understanding (LUIS), vous devez ajouter des exemples d’énoncé à une intention.

1. Dans la page de détails d’intention **GetEmployeeInformation**, entrez un énoncé pertinent que vous attendez de vos utilisateurs, tel que `Does John Smith work in Seattle?`, dans la zone de texte sous le nom d’intention, puis appuyez sur Entrée.
 
    ![Capture d’écran de la page de détails Intents (Intentions), avec l’énoncé en surbrillance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS convertit tous les énoncés en minuscules et ajoute des espaces autour des jetons tels que les traits d’union.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erreurs de prédiction de l’intention 

Il peut arriver qu’un exemple d’énoncé comporte une erreur de prédiction entre l’intention dans laquelle il se trouve et l’intention de prédiction déterminée au cours de l’apprentissage. 

Pour trouver et corriger les erreurs de prédiction d’énoncés, utilisez les options **Évaluation** Incorrect et Unclear de l’option **Filtre** avec l’option **Vue** **Vue détaillée**. 

![Pour trouver et corriger les erreurs de prédiction d’énoncés, utilisez l’option Filtre.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Une fois les filtres et la vue appliqués, les exemples d’énoncés comportant des erreurs apparaissent dans la liste avec les problèmes associés.

![![Une fois les filtres et la vue appliqués, les exemples d’énoncés comportant des erreurs apparaissent dans la liste avec les problèmes associés.](./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Chaque ligne indique le score de prédiction de l’apprentissage en cours de l’exemple d’énoncé, le score du rival le plus proche et l’évaluation, à savoir la différence entre ces deux scores. 

### <a name="fixing-intents"></a>Corriger les intentions

Pour savoir comment corriger les erreurs d’intention de prédiction, utilisez le [Tableau de bord Résumé](luis-how-to-use-dashboard.md). Il comporte une analyse du dernier apprentissage de la version active et propose les meilleures suggestions de correction du modèle.  

## <a name="add-a-custom-entity"></a>Ajouter une entité personnalisée

Une fois qu’un énoncé a été ajouté à une intention, vous pouvez sélectionner du texte dans l’énoncé pour créer une entité personnalisée. Une entité personnalisée est un moyen de marquer du texte pour l’extraction, avec l’intention correcte. 

Pour plus d’informations, voir [Ajouter une entité à un énoncé](luis-how-to-add-example-utterances.md).

## <a name="entity-prediction-discrepancy-errors"></a>Erreurs de différence de prédiction d’entité 

L’entité est soulignée en rouge pour signaler une [différence de prédiction d’entité](luis-how-to-add-example-utterances.md#entity-status-predictions). Comme il s’agit de la première occurrence d’une entité, il n’existe pas suffisamment d’exemples pour que LUIS ait un haut degré de confiance que ce texte soit marqué avec l’entité correcte. Cette différence disparaît quand l’application est entraînée. 

![Capture d’écran de la page de détails d’intention, avec nom d’entité personnalisée mis en surbrillance en bleu](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Le texte est mis en surbrillance en bleu, ce qui indique qu’il s’agit d’une entité.  

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie

Pour plus d’informations, consultez [Entité prédéfinie](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Utilisation de la barre d’outils contextuelle

Quand un ou plusieurs exemples d’énoncés sont sélectionnés dans la liste (case à gauche cochée), la barre d’outils qui se trouve au-dessus de la liste d’énoncés permet d’effectuer les actions suivantes :

* Réaffecter l’intention : déplacer des énoncés vers une autre intention
* Supprimer des énoncés
* Filtres d’entité : afficher uniquement les énoncés contenant des entités filtrées
* Afficher tout/erreurs uniquement : afficher les énoncés présentant des erreurs de prédiction ou afficher tous les énoncés
* Vue d’entités/jetons : afficher la vue des entités avec des noms d’entités ou afficher le texte brut d’énoncé
* Loupe : rechercher des énoncés contenant un texte spécifique

## <a name="working-with-an-individual-utterance"></a>Opérations sur un énoncé spécifique

Vous pouvez effectuer les actions suivantes en cliquant sur les points de suspension situés à droite de l’énoncé :

* Modifier : changer le texte de l’énoncé
* Supprimer : supprimer l’énoncé de l’intention. Si vous souhaitez conserver l’énoncé, une meilleure méthode consiste à le déplacer vers l’intention **None** 
* Ajouter un modèle : un modèle vous permet de prendre un énoncé courant et de marquer le texte remplaçable et le texte pouvant être ignoré, ce qui réduit le besoin d’énoncés supplémentaires dans l’intention. 

La colonne **Labeled intent** (Intention étiquetée) vous permet de changer l’intention de l’énoncé.

## <a name="train-your-app-after-changing-model-with-intents"></a>Former votre application après modification du modèle avec des intentions

Après avoir ajouté, modifié ou supprimé des intentions, [entraînez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications soient appliquées aux requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des [exemples d’énoncés](luis-how-to-add-example-utterances.md) avec des entités. 
