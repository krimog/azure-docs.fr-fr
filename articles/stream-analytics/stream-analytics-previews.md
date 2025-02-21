---
title: Fonctionnalités d’évaluation Azure Stream Analytics
description: Cet article répertorie les fonctionnalités d’Azure Stream Analytics qui sont actuellement en cours d’évaluation.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 587304968cdf3a3763e47b9f8b614fe67aebf534
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798031"
---
# <a name="azure-stream-analytics-preview-features"></a>Fonctionnalités d’évaluation Azure Stream Analytics

Cet article résume toutes les fonctionnalités actuellement en préversion pour Azure Stream Analytics. L’utilisation de fonctionnalités en préversion dans un environnement de production n’est pas recommandée.

## <a name="public-previews"></a>Préversions publiques

Les fonctionnalités suivantes sont disponibles en préversion publique. Vous pouvez tirer parti de ces fonctionnalités aujourd’hui, mais ne les utilisez pas dans votre environnement de production.

### <a name="one-click-integration-with-event-hubs"></a>Intégration de One-click à Event Hubs 
Avec cette intégration, vous pouvez désormais visualiser les données entrantes et commencer à écrire une requête Stream Analytics en un seul clic à partir du portail Event Hub. Une fois que votre requête est prête, vous pouvez la mettre en production en quelques clics et commencer à obtenir des insights temps réel. Ceci réduit considérablement le temps et les coûts nécessaires pour développer des solutions d’analytique temps réel. La documentation est disponible [ici](https://docs.microsoft.com/azure/event-hubs/process-data-azure-stream-analytics).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pour Azure Stream Analytics

Les travaux Azure Stream Analytics peuvent être créés dans Visual Studio Code. Consultez notre [tutoriel de prise en main de Visual Studio Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Détection des anomalies

Azure Stream Analytics introduit de nouveaux modèles Machine Learning avec la prise en charge de la détection de *pics* et de *chutes* qui vient s’ajouter à la détection des tendances négatives lentes, positives lentes et bidirectionnelles. Pour plus d’informations, consultez [Détection d’anomalies dans Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Intégration à Azure Machine Learning

Vous pouvez mettre à l’échelle les tâches Stream Analytics avec des fonctions Machine Learning (ML). Pour en savoir plus sur la façon dont vous pouvez utiliser les fonctions ML dans votre tâche Stream Analytics, consultez [Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Découvrez un scénario concret avec [Analyse des sentiments à l’aide d’Azure Stream Analytics et Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Agrégat JavaScript défini par l’utilisateur

Azure Stream Analytics prend en charge les agrégats définis par l’utilisateur (UDA) et écrits en JavaScript ; ils vous permettent d’implémenter une logique métier avec état complexe. Découvrez comment utiliser les UDA dans la documentation [Agrégats Javascript définis par l’utilisateur Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Tests de données actives dans Visual Studio

Visual Studio Tools pour Azure Stream Analytics améliore la fonctionnalité de test en local qui vous permet de tester vos requêtes sur les flux d’événements en direct, à partir de sources cloud, telles que Event Hub ou IoT hub. Découvrez comment [Tester les données actives localement à l’aide d’Azure Stream Analytics Tools pour Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Fonctions .NET définies par l’utilisateur sur IoT Edge

Avec les fonctions .NET standard définies par l’utilisateur, vous pouvez exécuter le code .NET Standard au sein même de votre pipeline de streaming. Vous pouvez créer des classes C# simples, ou importer des bibliothèques et un projet complet. Une expérience de création et de débogage complète est prise en charge dans Visual Studio. Pour plus d’informations, consultez [Développer des fonctions .NET Standard définies par l’utilisateur pour des tâches Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Autres préversions

Les fonctionnalités suivantes sont également disponibles sur demande en préversion.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud"></a>Désérialiseur personnalisé C# pour Azure Stream Analytics sur IoT Edge et Cloud

Les développeurs peuvent implémenter des désérialiseurs personnalisés en C# pour désérialiser les événements reçus par Azure Stream Analytics. Parquet, Protobuf, XML ou tout format binaire sont autant d’exemples de formats pouvant être désérialisés. Inscrivez-vous pour cette préversion [ici](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack"></a>Prise en charge pour Azure Stack
Cette fonctionnalité est activée sur le runtime Azure IoT Edge, tire parti de fonctionnalités Azure Stack personnalisées, comme la prise en charge native pour les entrées et les sorties locales s’exécutant sur Azure Stack (par exemple Event Hubs, IoT Hub, Stockage Blob). Cette nouvelle intégration vous permet de créer des architectures hybrides qui peuvent analyser vos données à proximité de l’endroit où elles sont générées, en réduisant la latence et en optimisant les insights.
Inscrivez-vous pour cette préversion [ici](https://aka.ms/asapreview1).

