---
title: 'Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utiliser ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing, avec Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 24416bc2259fdd35581699b11f599ea48e349d5a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564639"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Python

Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing. Si d’un côté cette application est écrite en Python, de l’autre, l’API Recherche personnalisée Bing constitue un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Prérequis

- Une instance Recherche personnalisée Bing. Consultez [Démarrage rapide : Créer votre première instance Recherche personnalisée Bing](quick-start.md) pour de plus amples informations.
- [Python](https://www.python.org/) 2.x ou 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis ajoutez les instructions d’importation suivantes. Créez des variables pour votre clé d’abonnement, votre ID de configuration personnalisée et un terme de recherche. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Envoyer et recevoir une requête de recherche 

1. Construisez l’URL de requête en ajoutant votre terme de recherche au paramètre de requête `q=`, et l’ID de configuration personnalisée de votre instance de recherche à `customconfig=`. Séparez les paramètres par un caractère `&`. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Envoyez la requête à votre instance Recherche personnalisée Bing et affichez les résultats de recherche retournés.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](./tutorials/custom-search-web-page.md)
