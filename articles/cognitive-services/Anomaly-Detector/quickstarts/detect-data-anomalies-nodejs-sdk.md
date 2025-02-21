---
title: "Démarrage rapide : Détecter des anomalies de données à l’aide de la bibliothèque de client Détecteur d'anomalies pour Python"
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Détecteur d’anomalies pour détecter les anomalies dans vos séries de données, soit par lot, soit sur des données de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 1932ac571c94f9dc96240bdb63b44fe53c626f1f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554736"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Démarrage rapide : Bibliothèque de client Détecteur d’anomalies pour Node.js

Découvrez comment bien démarrer avec la bibliothèque de client Détecteur d’anomalies pour Node.js. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service Détecteur d’anomalies vous permet de rechercher des anomalies dans vos données de séries chronologiques en utilisant automatiquement les modèles les mieux adaptés sur celles-ci, quel que soit le secteur d’activité, le scénario ou le volume de données.

Utilisez la bibliothèque de client Détecteur d’anomalies pour Node.js pour :

* Détecter les anomalies dans votre jeu de données de série chronologique, comme une requête de lots
* Détecter l’état d’anomalie du dernier point de données dans vos séries chronologiques

[Documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [Exemples de code](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-an-anomaly-detector-azure-resource"></a>Créer une ressource Azure pour le Détecteur d’anomalies

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

Créez un fichier nommé `index.js` et importez les bibliothèques suivantes :

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable. Créez une autre variable pour l’exemple de fichier de données que vous allez télécharger au cours d’une prochaine étape, et une liste vide pour les points de données. Créez ensuite un objet `ApiKeyCredentials` destiné à contenir la clé.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `ms-rest-azure` et `azure-cognitiveservices-anomalydetector`. La bibliothèque d’analyse de volume partagé de cluster est également utilisée dans ce guide de démarrage rapide :

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

## <a name="object-model"></a>Modèle objet

Le client Détecteur d’anomalies est un objet [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client propose deux méthodes de détection des anomalies : Sur un jeu de données entier à l’aide de [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--), et sur le dernier point de données à l’aide de [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Les données de série chronologique sont envoyées en tant que série de [Points](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) dans un objet [Request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest). L’objet `Request` contient des propriétés pour décrire les données ([Granularité](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) par exemple) et des paramètres pour la détection d’anomalies. 

La réponse de Détecteur d’anomalies est un objet [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) ou [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) selon la méthode utilisée. 

## <a name="code-examples"></a>Exemples de code 

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Détecteur d’anomalies pour Node.js :

* [Authentifier le client](#authenticate-the-client)
* [Charger un jeu de données de séries chronologiques à partir d’un fichier](#load-time-series-data-from-a-file)
* [Détecter des anomalies dans un jeu de données entier](#detect-anomalies-in-the-entire-data-set) 
* [Détecter l’état d’anomalie du dernier point de données](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un objet [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) avec votre point de terminaison et vos informations d’identification.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Charger des données de séries chronologiques à partir d’un fichier

Téléchargez l’exemple de données pour ce démarrage rapide à partir de [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv) :
1. Dans votre navigateur, cliquez avec le bouton droit sur **Brutes**.
2. Cliquez sur **Enregistrer le lien sous**.
3. Enregistrez le fichier dans le répertoire de votre application en tant que fichier .csv.

Ces données de séries chronologiques sont mises en forme dans un fichier .csv et envoyées à l’API Détecteur d’anomalies.

Lisez votre fichier de données avec la méthode `readFileSync()` de la bibliothèque d’analyse de volume partagé de cluster, puis analysez le fichier avec `parse()`. Pour chaque ligne, envoyez (push) un objet [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) contenant l’horodatage et la valeur numérique.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Détecter des anomalies dans un jeu de données entier 

Appelez l’API pour détecter les anomalies dans l’ensemble de la série chronologique en tant que lot avec la méthode [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) du client. Stockez l'objet [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) renvoyé. Itérez dans la liste `isAnomaly` de la réponse et imprimez l'index des valeurs `true`. Ces valeurs correspondent à l’indice des points de données anormaux, le cas échéant.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

Appelez l’API Détecteur d’anomalies pour déterminer si votre dernier point de données est une anomalie à l’aide de la méthode [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) du client, puis stockez l’objet [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) retourné. La valeur `isAnomaly` de la réponse correspond à une valeur booléenne qui spécifie l’état d'anomalie de ce point.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Streaming de la détection d’anomalies avec Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* [Présentation de l’API Détecteur d’anomalies](../overview.md)
* [Bonnes pratiques](../concepts/anomaly-detection-best-practices.md) concernant l’utilisation de l’API Détecteur d’anomalies.
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
