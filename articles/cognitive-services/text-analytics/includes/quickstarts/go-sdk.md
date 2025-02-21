---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 9b148e413bc7dc6af7eff064e5ff3ec6385cfef4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750131"
---
[Documentation de référence](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Package (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
* La dernière version de [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-go-project"></a>Créer un projet Go

Dans une fenêtre de console (cmd, PowerShell, terminal, bash), créez un espace de travail pour votre projet Go et accédez-y. Votre espace de travail contiendra trois dossiers : 

* **SRC** : ce répertoire contient le code source et les packages. Tous les packages installés à l’aide de la commande `go get` s’y trouvent.
* **pkg** : ce répertoire contient les objets de package Go compilés. Ces fichiers ont tous une extension `.a`.
* **bin** : ce répertoire contient les fichiers exécutables binaires créés lors de l’exécution de `go install`.

> [!TIP]
> En savoir plus sur la structure d'un [espace de travail Go](https://golang.org/doc/code.html#Workspaces). Ce guide comprend des informations pour configurer `$GOPATH` et `$GOROOT`.

Créez un espace de travail appelé `my-app` et les sous-répertoires requis pour `src`, `pkg` et `bin` :

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Installer la Bibliothèque de client Analyse de texte pour Go

Installer la bibliothèque de client pour Go : 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

Ou, si vous utilisez dep, au sein de votre dépôt, exécutez :

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Créer votre application Go

Créez ensuite un fichier nommé `src/quickstart.go` :

```bash
$ cd src
$ touch quickstart.go
```

Ouvrez `quickstart.go` dans l’éditeur de texte ou l’environnement de développement intégré de votre choix. Ajoutez ensuite le nom du package et importez les bibliothèques suivantes :

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Modèle objet 

Le client Analyse de texte est un objet [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client fournit plusieurs méthodes pour analyser le texte, en tant que chaîne unique ou lot. 

Le texte est envoyé à l’API sous la forme d’une liste de `documents`, qui sont des objets `dictionary` contenant une combinaison d’attributs `id`, `text` et `language` en fonction de la méthode utilisée. L’attribut `text` stocke le texte à analyser dans le `language` d’origine, et vous pouvez utiliser n’importe quelle valeur pour `id`. 

L’objet Response est une liste contenant les informations d’analyse de chaque document. 

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client Analyse de texte pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Analyse des sentiments](#sentiment-analysis)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entités](#entity-recognition)
* [Extraction de phrases clés](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Authentifier le client


Dans une nouvelle fonction, créez des variables pour le point de terminaison et la clé d’abonnement Azure de votre ressource. Obtenez ces valeurs à partir des variables d’environnement `TEXT_ANALYTICS_SUBSCRIPTION_KEY` et `TEXT_ANALYTICS_ENDPOINT`. Si vous avez créé ces variables d’environnement après avoir commencé à modifier l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou l’interpréteur de commandes que vous utilisez pour accéder aux variables.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

Créez un objet [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New). Transmettez votre clé à la fonction [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), qui sera ensuite transmise à la propriété `authorizer` du client.

[!code-go[Client creation ](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=client)]

## <a name="sentiment-analysis"></a>analyse de sentiments

Créez une fonction appelée `SentimentAnalysis()` et créez un client à l’aide de la méthode `GetTextAnalyticsClient()` créée précédemment. Créez la liste des objets [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenant les documents à analyser. Chaque objet contient un `id`, `Language` et attribut `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur. 

Appelez la fonction [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) du client et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le score de sentiment. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Appelez `SentimentAnalysis()` dans votre projet.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Détection de la langue

Créez une fonction appelée `LanguageDetection()` et créez un client à l’aide de la méthode `GetTextAnalyticsClient()` créée précédemment. Créez la liste des objets [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) contenant les documents à analyser. Chaque objet contient un `id` et un attribut `text`. L’attribut `text` stocke le texte à analyser, et le `id` peut être n’importe quelle valeur. 

Appelez la fonction [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) du client et obtenez les résultats. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le langage détecté.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Appelez `LanguageDetection()` dans votre projet.

### <a name="output"></a>Output

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

Créez une fonction appelée `ExtractEntities()` et créez un client à l’aide de la méthode `GetTextAnalyticsClient()` créée précédemment. Créez la liste des objets [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenant les documents à analyser. Chaque objet contient un `id`, `language` et un attribut `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur. 

Appelez la fonction [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) du client et obtenez les résultats. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le score des entités extraites.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Appelez `ExtractEntities()` dans votre projet.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Créez une fonction appelée `ExtractKeyPhrases()` et créez un client à l’aide de la méthode `GetTextAnalyticsClient()` créée précédemment. Créez la liste des objets [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenant les documents à analyser. Chaque objet contient un `id`, `language` et un attribut `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur.

Appelez la fonction [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) du client et obtenez les résultats. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et les expressions clés extraites.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Appelez `ExtractKeyPhrases()` dans votre projet.

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
