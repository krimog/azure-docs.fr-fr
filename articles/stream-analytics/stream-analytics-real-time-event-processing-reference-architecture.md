---
title: Traitement des événements en temps réel à l’aide d’Azure Stream Analytics
description: Cet article décrit l’architecture de référence permettant un traitement et une analyse des événements en temps réel à l’aide d’Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: ab1ca89427f332151120420c3c087902584706f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621777"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architecture de référence : Traitement d’événements en temps réel avec Microsoft Azure Stream Analytics
L’architecture de référence associée au traitement des événements en temps réel avec Azure Stream Analytics fournit un modèle générique de déploiement d’une solution de traitement des flux PaaS (Platform as a Service) en temps réel avec Microsoft Azure.

## <a name="summary"></a>Résumé
Généralement, les solutions d’analyse reposent sur différentes fonctionnalités, comme le processus ETL (extraction, transformation et chargement) et l’entreposage de données, où sont stockées les données avant analyse. La modification des besoins, y compris l’accélération de l’arrivée des données, pousse le modèle existant jusqu’à ses dernières limites. Il est possible d’analyser les données des flux de données en déplacement avant stockage. Même si cette fonctionnalité n’est pas nouvelle, cette approche ne s’est pas généralisée. 

Microsoft Azure fournit un catalogue étendu de technologies d’analyse compatibles avec de nombreuses solutions et spécifications. La diversité des offres peut grandement compliquer le choix des services Azure à déployer pour une solution de bout en bout. Cet article décrit les fonctionnalités et l’interopérabilité des services Azure qui prennent en charge une solution de diffusion en continu des événements. Il décrit également les situations dans lesquelles les clients peuvent profiter de ce type d’approche.

## <a name="contents"></a>Sommaire
* Résumé
* Présentation de l’analyse en temps réel
* Proposition de valeur des données en temps réel dans Azure
* Scénarios courants d’analyse en temps réel
* Architecture et composants
  * Sources de données
  * Couche d’intégration de données
  * Couche d’analyse en temps réel
  * Couche de stockage des données
  * Couche présentation/consommation
* Conclusion

**Auteur :** Charles Feddersen, architecte de solutions, Data Insights Center of Excellence, Microsoft Corporation

**Date de publication :** Janvier 2015

**Révision :** 1.0

**Télécharger :** [Traitement d’événements en temps réel avec Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

