---
title: Conception d’application itérative - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487778"
---
# <a name="authoring-cycles-and-versions"></a>Cycles et versions de création

Votre application LUIS apprend le mieux dans un cycle itératif de :

* créer une nouvelle version
* modifier le schéma d’application
    * intentions avec des exemples d’énoncés
    * entities
    * features
* entraîner
* test
* Publier
    * tester l’apprentissage actif au niveau d’un point de terminaison de prédiction
* collecter des données à partir de requêtes de point de terminaison

![Cycle de création](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Création d’un modèle LUIS

L’objectif du schéma d’application vise à définir ce que l’utilisateur demande (intention) et quelles parties de la question fournissent des détails (entités) qui permettent de déterminer la réponse. 

Le schéma d’application doit être spécifique au domaine de l’application afin de déterminer les mots et expressions pertinents, ainsi que l’ordre standard des mots. 

Les exemples d’énoncés représentent l’entrée d’utilisateur attendue par l’application lors de l’exécution. 

Le schéma nécessite des intentions et _doit avoir_ des entités. 

### <a name="example-schema-of-intents"></a>Exemple de schéma d’intentions

Le schéma le plus courant est un schéma d’intention organisé avec des intentions. Ce type de schéma dépend de LUIS pour déterminer l’intention d’un utilisateur. 

Ce type de schéma peut avoir des entités s’il permet à LUIS de déterminer l’intention. Par exemple, une entité d’expédition (comme descripteur d’intention) permet à LUIS de déterminer une intention d’expédition. 

### <a name="example-schema-of-entities"></a>Exemple de schéma d’entités

Un schéma d’entité se concentre sur les entités, qui sont les données à extraire des énoncés. 

L’objectif de l’énoncé est moins important ou pas important pour l’application cliente. 

Une méthode courante d’organisation d’un schéma d’entité consiste à ajouter tous les exemples d’énoncés à l’intention None. 

### <a name="example-of-a-mixed-schema"></a>Exemple de schéma mixte

Le schéma le plus puissant et le plus mature est un schéma d’intention avec une gamme complète d’entités et de fonctionnalités. Ce schéma peut commencer comme un schéma d’intention ou d’entité et augmenter pour inclure les concepts des deux, car l’application cliente a besoin de ces informations. 

## <a name="add-example-utterances-to-intents"></a>Ajouter des exemples d’énoncés aux intentions

LUIS a besoin d’exemples d’énoncés dans chaque **intention**. Les exemples d’énoncés ont besoin d’une variation suffisante en termes de choix des mots et d’ordre des mots pour être en mesure de déterminer à quelle intention est destiné l’énoncé. 

> [!CAUTION]
> N’ajoutez pas d’exemples d’énoncés en bloc. Commencez par 15 à 30 exemples spécifiques et différents. 

Dans chaque exemple d’énoncé, les **données requises pour extraire** doivent être désignées et étiquetées avec des **entités**. 

|Élément clé|Objectif|
|--|--|
|Intention|**Classer** les énoncés utilisateur en une seule intention ou action. Exemples : `BookFlight` et `GetWeather`.|
|Entité|**Extraire** des données de l’énoncé requis pour terminer l’intention. Les exemples incluent la date et l’heure du voyage et l’emplacement.|

Vous concevez votre application LUIS pour ignorer les énoncés qui ne sont pas pertinents dans le domaine de votre application en affectant l’énoncé à l’intention **None**. 

## <a name="test-and-train-your-app"></a>Tester et former votre application

Une fois que vous avez entre 15 et 30 exemples d’énoncés différents dans chaque intention, avec les entités requises étiquetées, vous devez tester et [former](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Publier pour un point de terminaison de prédiction

Veillez à publier votre application afin qu’elle soit disponible dans les [régions de point de terminaison de prédiction](luis-reference-regions.md) requises. 

## <a name="test-your-published-app"></a>Tester votre application publiée

Vous pouvez tester votre application LUIS publiée à partir du point de terminaison de prédiction HTTPS. Le test à partir du point de terminaison de prédiction permet à LUIS de choisir n’importe quel énoncé avec une confiance faible pour la [révision](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Créer une nouvelle version pour chaque cycle

Les versions dans LUIS sont similaires aux versions dans la programmation traditionnelle. Chaque version est un instantané dans le temps de l’application. Avant d’apporter des modifications à l’application, créez une nouvelle version. Il est plus facile de revenir à une version antérieure, puis de tenter de supprimer les intentions et énoncés dans un état antérieur.

L’ID de version est composé de caractères, de chiffres ou de « . » et ne peut pas contenir plus de 10 caractères.

La version d’origine (0.1) est la version active par défaut. 

### <a name="begin-by-cloning-an-existing-version"></a>Commencer par cloner une version existante

Clonez une version existante à utiliser comme point de départ pour la nouvelle version. Une fois que vous clonez une version, la nouvelle version devient la version **active**. 

### <a name="publishing-slots"></a>Emplacements de publication
Vous publiez dans l’emplacement intermédiaire ou l’emplacement de production. Chaque emplacement peut avoir une version différente ou la même version. Cela est utile pour vérifier les modifications avant la publication et la production, qui est disponible pour les bots ou autres applications appelantes LUIS. 

Les versions formées ne sont pas automatiquement disponibles sur le [point de terminaison](luis-glossary.md#endpoint) de votre application. Vous devez [publier](luis-how-to-publish-app.md) ou republier une version afin qu’elle soit disponible sur le point de terminaison de votre application. Vous pouvez publier dans **Intermédiaire** et **Production**, ce qui vous donne deux versions de l’application disponibles sur le point de terminaison. Si vous avez besoin de davantage de versions de l’application disponibles sur un point de terminaison, vous devez exporter la version et la réimporter dans une nouvelle application. La nouvelle application a un ID d’application différent.

### <a name="import-and-export-a-version"></a>Importer et exporter une version
Vous pouvez importer une version au niveau de l’application. Cette version devient la version active et utilise l’ID de version dans la propriété `versionId` du fichier de l’application. Vous pouvez également importer au niveau de la version dans une application existante. La nouvelle version devient la version active. 

Vous pouvez exporter une version au niveau de l’application ou de la version. La seule différence est que la version exportée au niveau de l’application est la version active, alors qu’au niveau de la version, vous pouvez choisir n’importe quelle version à exporter dans la page **[Paramètres](luis-how-to-manage-versions.md)** . 

Le fichier exporté ne contient pas les éléments suivants :

* informations issues de l’apprentissage automatique, car l’application est reformée après son importation
* informations sur le contributeur

Pour sauvegarder votre schéma d’application LUIS, exportez une version à partir du portail LUIS.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Gérer les modifications de contributeurs avec des versions et des applications

LUIS fournit le concept de contributeurs d’une application en fournissant des autorisations au niveau des ressources Azure. Combinez ce concept avec le contrôle de version pour fournir une collaboration ciblée. 

Utilisez les techniques suivantes pour gérer les modifications de contributeurs dans votre application.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gérer plusieurs versions dans la même application
Commencez par le [clonage](luis-how-to-manage-versions.md#clone-a-version), à partir d’une version de base, pour chaque auteur. 

Chaque auteur apporte des modifications à sa propre version de l’application. Une fois que chaque auteur est satisfait du modèle, exportez les nouvelles versions sous forme de fichiers JSON.  

Les applications exportées, les fichiers .JSON ou .lu peuvent être comparés pour connaître les modifications. Combinez les fichiers pour créer un seul fichier de la nouvelle version. Modifiez la propriété **versionId** pour indiquer la nouvelle version fusionnée. Importez cette version dans l’application d’origine. 

Cette méthode vous permet d’avoir une version active, une version intermédiaire et une version publiée. Vous pouvez comparer les résultats de la version active avec une version publiée (de phase ou de production) dans le [volet de test interactif](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gérer plusieurs versions en tant qu’applications
[Exportez](luis-how-to-manage-versions.md#export-version) la version de base. Chaque auteur importe la version. La personne qui importe l’application est le propriétaire de la version. Une fois la modification de l’application terminée, exportez la version. 

Les applications exportées sont des fichiers au format JSON, qui peuvent être utilisés pour comparer les modifications avec l’exportation de base. Combinez les fichiers pour créer un seul fichier JSON de la nouvelle version. Modifiez la propriété **versionId** dans le fichier JSON pour indiquer la nouvelle version fusionnée. Importez cette version dans l’application d’origine.

Apprenez-en davantage sur la création de contributions de [collaborateurs](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Examiner les énoncés de point de terminaison pour commencer le nouveau cycle de création

Lorsque vous avez terminé un cycle de création, vous pouvez recommencer. Commencez par [examiner les énoncés de prédiction de point de terminaison](luis-how-to-review-endpoint-utterances.md) marqués par LUIS avec une confiance faible. Vérifiez ces énoncés pour obtenir une intention prédite correcte et une entité correcte et complète extraite. Une fois que vous avez revu et accepté les modifications, la liste doit être vide.  

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de la [collaboration](luis-concept-keys.md).
