---
title: 'Démarrage rapide : Reconnaître la voix stockée dans le stockage Blob, Java - Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c5fa12621d689287db0d91885fc0a35edb772294
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506058"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, effectuez les étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Charger un fichier source dans un objet blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>Ouvrir votre projet dans Eclipse

La première étape consiste à vérifier que votre projet est ouvert dans Eclipse.

1. Lancez Eclipse
2. Chargez votre projet et ouvrez `Main.java`.

## <a name="add-a-reference-to-gson"></a>Ajouter une référence à Gson
Dans ce guide de démarrage rapide, nous allons utiliser un sérialiseur/désérialiseur JSON externe. Pour Java, nous avons choisi [Gson](https://github.com/google/gson).

Ouvrez votre fichier pom.xml et ajoutez la référence suivante : [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Commencer par du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(vous devrez remplacer les valeurs de `YourSubscriptionKey`, `YourServiceRegion` et `YourFileUrl` par vos propres valeurs).

## <a name="json-wrappers"></a>Wrappers JSON

Étant donné que l’API REST accepte les requêtes au format JSON et retourne également des résultats au format JSON, nous pourrions interagir avec elles en utilisant uniquement des chaînes, même si cela n’est pas recommandé.
Pour faciliter la gestion des requêtes et des réponses, nous allons déclarer quelques classes à utiliser pour sérialiser/désérialiser le JSON.

Placez leurs déclarations avant `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Créer et configurer un client HTTP
La première chose dont nous avons besoin est d’un client HTTP disposant d’une URL de base correcte et pour lequel l’authentification a été définie.
Insérez ce code dans `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Générer une requête de transcription
Nous allons ensuite générer la requête de transcription. Ajoutez ce code à `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Envoyer la requête et vérifier son état
Nous allons maintenant envoyer la requête au service Speech et vérifier le code de réponse initial. Ce code de réponse indique simplement si le service a reçu la requête. Le service va retourner une URL dans les en-têtes de réponse qui correspond à l’emplacement où il va stocker l’état de la transcription.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendez que la transcription se termine.
Étant donné que le service traite la transcription de manière asynchrone, nous devons interroger l’état régulièrement. Nous allons le vérifier toutes les 5 secondes.

Nous pouvons vérifier l’état en récupérant le contenu à partir de l’URL que nous avons obtenue en envoyant la requête. Lorsque nous obtenons le contenu, nous le désérialisons dans l’une de nos classes d’assistance pour faciliter l’interaction avec celui-ci.

Voici le code d’interrogation pour tous les états sauf celui correspondant à une tâche terminée sans erreurs (nous l’aborderons plus tard).
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Afficher les résultats de la transcription
Une fois que le service a terminé la transcription et que celle-ci s’est bien déroulée, les résultats sont stockés dans une autre URL que nous pouvons obtenir dans la réponse d’état.

Nous allons télécharger le contenu de cette URL, désérialiser le JSON et parcourir les résultats en affichant le texte d’affichage au fur et à mesure.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Vérifier votre code
À ce stade, votre code doit ressembler à ceci : (nous avons ajouté des commentaires à cette version) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Vous êtes maintenant prêt à créer votre application et à tester la reconnaissance vocale à l’aide du service Speech.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
