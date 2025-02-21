---
title: Installer des conteneurs Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Installez et exécutez des conteneurs Speech. La reconnaissance vocale transcrit en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser ou afficher. La synthèse vocale convertit le texte d’entrée en parole synthétisée quasi humaine.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: efb2fd8fd6b77a27130b834c2b192c1e88eec97c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578400"
---
# <a name="install-and-run-speech-service-containers"></a>Installer et exécuter des conteneurs de service Speech

Les conteneurs vous permettent d’exécuter certaines des API du service Speech dans votre propre environnement. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données. Dans cet article, vous allez apprendre à télécharger, installer et exécuter un conteneur Speech.

Les conteneurs Speech permettent aux clients de créer une architecture d’application Speech optimisée pour tirer parti des fonctionnalités robustes du cloud et de la localité en périphérie. Quatre conteneurs différents sont disponibles. Les deux conteneurs standard sont **Reconnaissance vocale** et **Synthèse vocale**. Les deux conteneurs personnalisés sont **Reconnaissance vocale personnalisée** et **Synthèse vocale personnalisée**.

> [!IMPORTANT]
> Tous les conteneurs Speech sont actuellement proposés dans le cadre d’une [préversion « contingentée » publique](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Une annonce sera effectuée quand les conteneurs Speech passeront en disponibilité générale.

| Fonction | Caractéristiques | Latest |
|--|--|--|
| Reconnaissance vocale | Transcrit en continu de la parole en temps réel ou des enregistrements audio en texte, avec des résultats intermédiaires. | 2.0.0 |
| Reconnaissance vocale personnalisée | À l’aide d’un modèle personnalisé issu du [portail Custom Speech](https://speech.microsoft.com/customspeech), transcrit en continu de la parole en temps réel ou des enregistrements audio en texte, avec des résultats intermédiaires. | 2.0.0 |
| Synthèse vocale | Convertit le texte en paroles naturelles par le biais d’une entrée de texte brut ou du langage de balisage SSML (Speech Synthesis Markup Language). | 1.3.0 |
| Synthèse vocale personnalisée | À l’aide d’un modèle personnalisé issu du [portail Custom Voice](https://aka.ms/custom-voice-portal), convertit le texte en paroles naturelles par le biais d’une entrée de texte brut ou du langage de balisage SSML (Speech Synthesis Markup Language). | 1.3.0 |

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

L’utilisation des conteneurs Speech est soumise aux prérequis suivants :

| Obligatoire | Objectif |
|--|--|
| Moteur Docker | Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br> |
| Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base. |
| Ressource Speech | Pour pouvoir utiliser ces conteneurs, vous devez avoir :<br><br>Une ressource _Speech_ d’Azure afin d’obtenir la clé API et l’URI du point de terminaison associés. Les deux valeurs sont disponibles sur les pages Vue d’ensemble et Clés de la ressource **Speech** du portail Azure. Elles sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : L’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : le point de terminaison tel qu'il est fourni à la page **Vue d’ensemble** |

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Complétez et envoyez le [formulaire de demande de conteneurs Cognitive Services Speech](https://aka.ms/speechcontainerspreview/) pour demander l’accès au conteneur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Prise en charge d’Advanced Vector Extension

L’**hôte** est l’ordinateur qui exécute le conteneur docker. L’hôte *doit prendre en charge* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Vous pouvez vérifier cette prise en charge AVX2 sur les hôtes Linux avec la commande suivante :

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> L’ordinateur hôte doit *obligatoirement* prendre en charge AVX2. Le conteneur ne fonctionnera *pas* correctement sans prise en charge AVX2.

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant indique l’allocation de ressources minimale et recommandée pour chaque conteneur Speech.

# <a name="speech-to-texttabstt"></a>[Reconnaissance vocale](#tab/stt)

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
| Reconnaissance vocale | 2 cœurs, 2 Go de mémoire | 4 cœurs, 4 Go de mémoire |

# <a name="custom-speech-to-texttabcstt"></a>[Reconnaissance vocale personnalisée](#tab/cstt)

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
| Reconnaissance vocale personnalisée | 2 cœurs, 2 Go de mémoire | 4 cœurs, 4 Go de mémoire |

# <a name="text-to-speechtabtts"></a>[Synthèse vocale](#tab/tts)

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
| Synthèse vocale | 1 cœur, 2 Go de mémoire | 2 cœur, 3 Go de mémoire |

# <a name="custom-text-to-speechtabctts"></a>[Synthèse vocale personnalisée](#tab/ctts)

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
| Synthèse vocale personnalisée | 1 cœur, 2 Go de mémoire | 2 cœur, 3 Go de mémoire |

***

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

> [!NOTE]
> Les valeurs minimales et recommandées se basent sur les limites Docker, et *non* sur les ressources de la machine hôte. Par exemple, les conteneurs de reconnaissance vocale contenant des portions de mappage de mémoire d’un modèle linguistique volumineux, il est *recommandé* que l’intégralité du fichier tienne dans la mémoire, ce qui correspond à 4 à 6 Go supplémentaires. En outre, la première exécution d’un conteneur peut prendre beaucoup de temps perce que les modèles sont paginés en mémoire.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Les images conteneur pour Speech sont disponibles dans le service Container Registry suivant.

# <a name="speech-to-texttabstt"></a>[Reconnaissance vocale](#tab/stt)

| Conteneur | Référentiel |
|-----------|------------|
| Reconnaissance vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Reconnaissance vocale personnalisée](#tab/cstt)

| Conteneur | Référentiel |
|-----------|------------|
| Reconnaissance vocale personnalisée | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Synthèse vocale](#tab/tts)

| Conteneur | Référentiel |
|-----------|------------|
| Synthèse vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Synthèse vocale personnalisée](#tab/ctts)

| Conteneur | Référentiel |
|-----------|------------|
| Synthèse vocale personnalisée | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker pull pour les conteneurs Speech

# <a name="speech-to-texttabstt"></a>[Reconnaissance vocale](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker pull pour le conteneur Reconnaissance vocale

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du registre de conteneurs en préversion.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> La balise `latest` extrait les paramètres régionaux `en-US` et la voix `jessarus`. Pour d’autres paramètres régionaux, consultez [Paramètres régionaux de reconnaissance vocale](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Paramètres régionaux de reconnaissance vocale

Toutes les balises, à l’exception de `latest`, sont au format suivant, où `<culture>` indique le conteneur des paramètres régionaux :

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

La balise suivante illustre le format :

```
2.0.0-amd64-en-us-preview
```

Pour tous les paramètres régionaux pris en charge dans la version 2.0.0 du conteneur de **reconnaissance vocale**, consultez la colonne **Prise en charge des conteneurs** dans le tableau [Prise en charge des langues pour la reconnaissance vocale](language-support.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Reconnaissance vocale personnalisée](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker pull pour le conteneur Reconnaissance vocale personnalisée

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du registre de conteneurs en préversion.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Les `locale` et `voice` des conteneurs Speech personnalisés sont déterminés par le modèle personnalisé ingéré par le conteneur.

# <a name="text-to-speechtabtts"></a>[Synthèse vocale](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker pull pour le conteneur Synthèse vocale

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du registre de conteneurs en préversion.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> La balise `latest` extrait les paramètres régionaux `en-US` et la voix `jessarus`. Pour d’autres paramètres régionaux, consultez [Paramètres régionaux de synthèse vocale](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Paramètres régionaux de synthèse vocale

Toutes les balises, à l’exception de `latest`, sont au format suivant, où `<culture>` indique les paramètres régionaux et `<voice>` la voix du conteneur :

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

La balise suivante illustre le format :

```
1.3.0-amd64-en-us-jessarus-preview
```

Pour tous les paramètres régionaux et les voix correspondantes pris en charge dans la version 1.3.0 du conteneur de **synthèse vocale**, consultez la colonne **Prise en charge des conteneurs** dans le tableau [Prise en charge des langues pour les voix standard](language-support.md#standard-voices).

> [!IMPORTANT]
> Lors de la construction d’une requête HTTP POST de *synthèse vocale standard*, le message [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) nécessite un élément `voice` avec un attribut `name`. La valeur correspond aux paramètres régionaux et à la voix du conteneur correspondant, également appelés [« nom court »](language-support.md#standard-voices). Par exemple, le nom de la voix pour la balise `latest` est `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Synthèse vocale personnalisée](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker pull pour le conteneur Synthèse vocale personnalisée

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du registre de conteneurs en préversion.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Les `locale` et `voice` des conteneurs Speech personnalisés sont déterminés par le modèle personnalisé ingéré par le conteneur.

***

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](speech-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles.
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter le conteneur. Pour plus d’informations sur la façon d’obtenir les valeurs `{Endpoint_URI}` et `{API_Key}`, consultez [Collecte des paramètres requis](#gathering-required-parameters). Des [exemples](speech-container-configuration.md#example-docker-run-commands) supplémentaires de la commande `docker run` sont également disponibles.

# <a name="speech-to-texttabstt"></a>[Reconnaissance vocale](#tab/stt)

Pour exécuter le conteneur *Reconnaissance vocale*, exécutez la commande `docker run` suivante.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Reconnaissance vocale* à partir de l’image conteneur.
* Alloue 4 cœurs de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

# <a name="custom-speech-to-texttabcstt"></a>[Reconnaissance vocale personnalisée](#tab/cstt)

Le conteneur *Reconnaissance vocale personnalisée* s’appuie sur un modèle Speech personnalisé. Le modèle personnalisé doit avoir été [entraîné](how-to-custom-speech-train-model.md) à l’aide du [portail Custom Speech](https://speech.microsoft.com/customspeech). L’**ID du modèle** Custom Speech est nécessaire pour exécuter le conteneur. Il figure dans la page **Entraînement** du portail Custom Speech. À partir du portail Custom Speech, accédez à la page **Entraînement** et sélectionnez le modèle.
<br>

![Page Entraînement de Custom Speech](media/custom-speech/custom-speech-model-training.png)

Obtenez l’**ID du modèle** pour l’utiliser comme argument du paramètre `ModelId` de la commande `docker run`.
<br>

![Détails du modèle Custom Speech](media/custom-speech/custom-speech-model-details.png)

Le tableau suivant présente les différents paramètres `docker run` et leurs descriptions correspondantes :

| Paramètre | Description |
|---------|---------|
| `{VOLUME_MOUNT}` | [Montage de volume](https://docs.docker.com/storage/volumes/) de l’ordinateur hôte, que Docker utilise pour rendre le modèle personnalisé persistant. Par exemple, *C:\CustomSpeech* où le *lecteur C* se trouve sur l’ordinateur hôte. |
| `{MODEL_ID}` | **ID de modèle** Custom Speech issu de la page **Entraînement** du portail Custom Speech. |
| `{ENDPOINT_URI}` | Le point de terminaison est nécessaire pour le comptage et la facturation. Pour plus d’informations, consultez [Collecte des paramètres requis](#gathering-required-parameters). |
| `{API_KEY}` | La clé API est obligatoire. Pour plus d’informations, consultez [Collecte des paramètres requis](#gathering-required-parameters). |

Pour exécuter le conteneur *Reconnaissance vocale personnalisée*, exécutez la commande `docker run` suivante :

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Reconnaissance vocale personnalisée* à partir de l’image conteneur.
* Alloue 4 cœurs de processeur et 4 gigaoctets (Go) de mémoire.
* Charge le modèle *Reconnaissance vocale personnalisée* à partir du montage d’entrée de volume, par exemple *C:\CustomSpeech*.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur.
* Télécharge le modèle selon `ModelId` (s’il est introuvable sur le montage de volume).
* Si le modèle personnalisé a été téléchargé auparavant, `ModelId` est ignoré.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

# <a name="text-to-speechtabtts"></a>[Synthèse vocale](#tab/tts)

Pour exécuter le conteneur *Synthèse vocale*, exécutez la commande `docker run` suivante.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Synthèse vocale* à partir de l’image conteneur.
* Alloue 2 cœurs de processeur et un gigaoctet (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

# <a name="custom-text-to-speechtabctts"></a>[Synthèse vocale personnalisée](#tab/ctts)

Le conteneur *Synthèse vocale personnalisée* s’appuie sur un modèle vocal personnalisé. Le modèle personnalisé doit avoir été [entraîné](how-to-custom-voice-create-voice.md) à l’aide du [portail Custom Voice](https://aka.ms/custom-voice-portal). L’**ID du modèle** Custom Voice est nécessaire pour exécuter le conteneur. Il figure dans la page **Entraînement** du portail Custom Voice. À partir du portail Custom Voice, accédez à la page **Entraînement** et sélectionnez le modèle.
<br>

![Page Entraînement de Custom Voice](media/custom-voice/custom-voice-model-training.png)

Obtenez l’**ID du modèle** pour l’utiliser comme argument du paramètre `ModelId` de la commande docker run.
<br>

![Détails du modèle Custom Voice](media/custom-voice/custom-voice-model-details.png)

Le tableau suivant présente les différents paramètres `docker run` et leurs descriptions correspondantes :

| Paramètre | Description |
|---------|---------|
| `{VOLUME_MOUNT}` | [Montage de volume](https://docs.docker.com/storage/volumes/) de l’ordinateur hôte, que Docker utilise pour rendre le modèle personnalisé persistant. Par exemple, *C:\CustomSpeech* où le *lecteur C* se trouve sur l’ordinateur hôte. |
| `{MODEL_ID}` | **ID de modèle** Custom Voice issu de la page **Entraînement** du portail Custom Voice. |
| `{ENDPOINT_URI}` | Le point de terminaison est nécessaire pour le comptage et la facturation. Pour plus d’informations, consultez [Collecte des paramètres requis](#gathering-required-parameters). |
| `{API_KEY}` | La clé API est obligatoire. Pour plus d’informations, consultez [Collecte des paramètres requis](#gathering-required-parameters). |

Pour exécuter le conteneur *Synthèse vocale personnalisée*, exécutez la commande `docker run` suivante :

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Synthèse vocale personnalisée* à partir de l’image conteneur.
* Alloue 2 cœurs de processeur et un gigaoctet (Go) de mémoire.
* Charge le modèle *Synthèse vocale personnalisée* à partir du montage d’entrée de volume, par exemple *C:\CustomVoice*.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur.
* Télécharge le modèle selon `ModelId` (s’il est introuvable sur le montage de volume).
* Si le modèle personnalisé a été téléchargé auparavant, `ModelId` est ignoré.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

***

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

| Conteneur | Point de terminaison | Protocol |
|--|--|--|
| Reconnaissance vocale | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Reconnaissance vocale personnalisée | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Synthèse vocale | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Synthèse vocale personnalisée | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

Pour plus d’informations sur l’utilisation des protocoles WSS et HTTPS, consultez [Sécurité des conteneurs](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Synthèse vocale ou synthèse vocale personnalisée

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Exécuter plusieurs conteneurs sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un port exposé différent. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Vous pouvez avoir ce conteneur, et un autre conteneur Azure Cognitive Services qui s’exécutent ensemble sur l’hôte. Vous pouvez également disposer de plusieurs conteneurs du même conteneur Cognitive Services en cours d’exécution.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Au démarrage ou pendant l’exécution du conteneur, vous pouvez rencontrer des problèmes. Utilisez une [montage](speech-container-configuration.md#mount-settings) de sortie et activez la journalisation. Ainsi, le conteneur pourra générer des fichiers journaux utiles à la résolution des problèmes.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturation

Les conteneurs Speech envoient des informations de facturation à Azure à l’aide d’une ressource *Speech* sur votre compte Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Speech. En résumé :

* Speech fournit quatre conteneurs Linux pour Docker, qui encapsulent diverses fonctionnalités :
  * *Reconnaissance vocale*
  * *Reconnaissance vocale personnalisée*
  * *Synthèse vocale*
  * *Synthèse vocale personnalisée*
* Les images conteneur sont téléchargées à partir du registre de conteneurs dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou kit de développement logiciel (SDK) pour appeler des opérations dans des conteneurs Speech en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
>  Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Passer en revue [Configurer des conteneurs](speech-container-configuration.md) pour découvrir les paramètres de configuration
* Découvrir comment [utiliser les conteneurs du service Speech avec Kubernetes et Helm](speech-container-howto-on-premises.md)
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
