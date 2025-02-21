---
title: 'Démarrage rapide : SDK Recherche visuelle Bing, Python'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour commencer à obtenir des insights sur les images à partir du service Recherche visuelle Bing, à l’aide du SDK Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 4d5679e75f0feb4fd502abc92fc7c1de16b53bc8
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111573"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide du SDK Recherche visuelle Bing pour Python

Utilisez ce guide de démarrage rapide pour commencer à obtenir des insights sur les images à partir du service Recherche visuelle Bing, à l’aide du SDK Python. Si d’un côté la Recherche visuelle Bing dispose d’une API REST compatible avec la plupart des langages de programmation, de l’autre le SDK offre un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py). 

## <a name="prerequisites"></a>Prérequis

* [Python](https://www.python.org/) 2.x ou 3.x
* Il est recommandé d’utiliser un [environnement virtuel](https://docs.python.org/3/tutorial/venv.html). Installez et initialisez l’environnement virtuel avec le [module venv](https://pypi.python.org/pypi/virtualenv).
* SDK Recherche visuelle Bing pour Python Vous pouvez l’installer à l’aide des commandes suivantes :
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis ajoutez les instructions d’importation suivantes. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Créez des variables pour votre clé d’abonnement, votre ID de configuration personnalisée et l’image que vous souhaitez charger. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Instancier le client

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>Envoyer la requête de recherche

1. Une fois le fichier image ouvert, sérialisez `VisualSearchRequest()`et définissez-le comme paramètre `knowledge_request` pour `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Si des résultats sont retournés, imprimez-les avec les balises et les actions contenues dans la première balise.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-visual-search-single-page-app.md)
