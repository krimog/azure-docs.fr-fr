---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un fichier audio, Python - Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application console de reconnaissance vocale qui utilise le kit SDK Speech pour Python. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 91b136b7986dc3551ebc28b7e35bfe206b273497
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506442"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, procédez aux étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Créer une application LUIS et obtenir une clé de point de terminaison](../../../../quickstarts/create-luis.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md)

## <a name="support-and-updates"></a>Mises à jour et support technique

Les mises à jour du package Python du kit SDK Speech sont distribuées via PyPI et annoncées dans les [notes de publication](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Si une nouvelle version est disponible, vous pouvez effectuer la mise à jour avec la commande `pip install --upgrade azure-cognitiveservices-speech`.
Vérifiez la version actuellement installée en inspectant la variable `azure.cognitiveservices.speech.__version__`.

Si vous avez un problème ou si une fonctionnalité est manquante, consultez [Options d’aide et de support](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Créer une application Python qui utilise le kit SDK Speech

### <a name="run-the-sample"></a>Exécution de l'exemple

Vous pouvez copier l’[exemple de code](#sample-code) de ce guide de démarrage rapide dans un fichier source `quickstart.py` et l’exécuter dans votre environnement de développement intégré ou dans la console :

```sh
python quickstart.py
```

Vous pouvez également télécharger ce tutoriel de démarrage rapide en tant que notebook [Jupyter](https://jupyter.org) à partir du [référentiel d’exemples de kit SDK Speech](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) et l’exécuter en tant que notebook.

### <a name="sample-code"></a>Exemple de code

````Python

import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and service region (e.g., "westus").
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")


# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))

````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installer et utiliser le SDK Speech avec Visual Studio Code

1. Téléchargez et installez une version 64 bits de [Python](https://www.python.org/downloads/) (version 3.5 ou ultérieure) sur votre ordinateur.
1. Téléchargez et installez [Visual Studio Code](https://code.visualstudio.com/Download).
1. Ouvrez Visual Studio Code et installez l’extension Python. Sélectionnez **Fichier** > **Préférences** > **Extensions** dans le menu. Recherchez **Python**.

   ![Installer l’extension Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Créez un dossier pour y stocker le projet. Vous pouvez par exemple utiliser l’Explorateur Windows.
1. Dans Visual Studio Code, sélectionnez l’icône **Fichier**. Ouvrez ensuite le dossier que vous avez créé.

   ![Ouvrir un dossier](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Créez un nouveau fichier source Python, `speechsdk.py`, en sélectionnant l’icône de nouveau fichier.

   ![Créer un fichier](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copiez, collez et enregistrez le [code Python](#sample-code) sur le fichier qui vient d’être créé.
1. Insérez les informations de votre abonnement aux services Speech.
1. S’il est sélectionné, un interpréteur Python s’affiche sur le côté gauche de la barre d’état, en bas de la fenêtre.
   Sinon, affichez la liste des interpréteurs Python disponibles. Ouvrez la palette de commandes (Ctrl+Maj+P) et entrez **Python : Sélectionner l’interpréteur**. Choisissez un interpréteur approprié.
1. Vous pouvez installer le package Python du kit SDK Speech à partir de Visual Studio Code. Procédez ainsi s’il n'est pas encore installé pour l’interpréteur Python que vous avez sélectionné.
   Pour installer le package du kit SDK Speech, ouvrez un terminal. Faite réapparaître la palette de commandes (Ctrl+Maj+P) et entrez **Terminal : Créer un terminal intégré**.
   Dans le terminal qui s’ouvre, entrez la commande `python -m pip install azure-cognitiveservices-speech` ou la commande appropriée pour votre système.
1. Pour exécuter l’exemple de code, cliquez avec le bouton droit dans l’éditeur. Sélectionnez **Run Python File in Terminal** (Exécuter le fichier Python dans le terminal).
   Les 15 premières secondes de saisie vocale provenant de votre fichier audio seront reconnues et enregistrées dans la fenêtre console.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Si vous rencontrez des problèmes pour suivre ces instructions, reportez-vous au [tutoriel Python plus complet sur Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
