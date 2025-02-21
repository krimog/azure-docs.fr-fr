---
title: 'Démarrage rapide : Reconnaître la voix, les intentions et les entités, Java - Service Speech'
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
ms.openlocfilehash: 441c938e9ab74eafecaf0f9188c0884696eccfae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506114"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, procédez aux étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Créer une application LUIS et obtenir une clé de point de terminaison](../../../../quickstarts/create-luis.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Ouvrir votre projet

Chargez votre projet et ouvrez `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Commencer par du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Avant de pouvoir initialiser un objet `IntentRecognizer`, vous devez créer une configuration qui utilise votre clé et votre région de point de terminaison LUIS. Insérez ce code dans le bloc try/catch dans main

Cet exemple utilise la méthode `FromSubscription()` pour générer la `SpeechConfig`. Pour obtenir la liste complète des méthodes disponibles, consultez la [classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

> [!NOTE]
> Il est important d’utiliser la clé de point de terminaison LUIS et non les clés de démarrage ou de création, car seule la clé de point de terminaison est valide pour la conversion de sortie orale en intention. Consultez [Créer une application LUIS et obtenir une clé de point de terminaison](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) pour obtenir des instructions sur la façon d’obtenir la clé appropriée.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-a-intentrecognizer"></a>Initialiser un IntentRecognizer

Créons maintenant un `IntentRecognizer`. Insérez ce code juste en dessous de votre configuration vocale.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Ajouter un LanguageUnderstandingModel et des intentions

Vous devez maintenant associer un `LanguageUnderstandingModel` au module de reconnaissance de l’intention et ajouter les intentions que vous souhaitez reconnaître.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Reconnaître une intention

À partir de l’objet `IntentRecognizer`, vous devez appeler la méthode `recognizeOnceAsync()`. Cette méthode permet au service de reconnaissance vocale de savoir que vous envoyez une seule phrase pour reconnaissance et d’arrêter la conversion une fois que l’expression est identifiée.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Afficher les résultats de la reconnaissance (ou les erreurs)

Lorsque le résultat de la reconnaissance est retourné par le service Speech, vous pouvez effectuer une opération avec celui-ci. Nous allons faire simple et imprimer le résultat dans la console.

Sous votre appel à `recognizeOnceAsync()`, ajoutez ce code : [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Libérer des ressources

Il est important de libérer les ressources lorsque vous avez fini de les utiliser. Insérez ce code à la fin du bloc try/catch : [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Vérifier votre code

À ce stade, votre code doit ressembler à ceci : (Nous avons ajouté des commentaires à cette version) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)**  > **Debug (Déboguer)** .
Les 15 secondes suivantes de saisie vocale provenant de votre microphone seront reconnues et enregistrées dans la fenêtre console.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]