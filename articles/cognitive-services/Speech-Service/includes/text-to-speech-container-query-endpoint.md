---
title: Interroger le point de terminaison du conteneur de synthèse vocale
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491142"
---
Le conteneur fournit des [API de point de terminaison basées sur REST](../rest-text-to-speech.md). Il existe de nombreux [exemples de projets de code source](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) pour les variantes de plateforme, d’infrastructure et de langage disponibles.

Avec le conteneur de *synthèse vocale standard*, vous devez vous appuyer sur les paramètres régionaux et la voix de la balise d’image que vous avez téléchargée. Par exemple, si vous avez téléchargé la balise `latest`, les paramètres régionaux par défaut sont `en-US` et la voix `JessaRUS`. L’argument `{VOICE_NAME}` est alors [`en-US-JessaRUS`](../language-support.md#standard-voices). Consultez l’exemple SSML ci-dessous :

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Toutefois, pour la *synthèse vocale personnalisée*, vous devez obtenir la **voix ou le modèle** à partir du [portail vocal personnalisé](https://aka.ms/custom-voice-portal). Le nom du modèle personnalisé est synonyme du nom de la voix. Accédez à la page **Formation**, puis copiez **la voix ou le modèle** à utiliser en tant qu’argument `{VOICE_NAME}`.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modèle vocal personnalisé – Nom de la voix":::

Consultez l’exemple SSML ci-dessous :

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Créons une requête HTTP POST en fournissant quelques en-têtes et une charge utile de données. Remplacez l’espace réservé `{VOICE_NAME}` par votre propre valeur.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Cette commande :

* Crée une requête HTTP POST pour le point de terminaison `speech/synthesize/cognitiveservices/v1`.
* Spécifie un en-tête `Accept` de `audio/*`.
* Spécifie un en-tête `Content-Type` de `application/ssml+xml`. Pour plus d’informations, voir [Corps de la demande](../rest-text-to-speech.md#request-body).
* Spécifie un en-tête `X-Microsoft-OutputFormat` de `riff-16khz-16bit-mono-pcm`. Pour plus d’options, voir [Sortie audio](../rest-text-to-speech.md#audio-outputs).
* Envoie au point de terminaison la demande [Speech Synthesis Markup Language (SSML)](../speech-synthesis-markup.md) nommée `{VOICE_NAME}`.