---
title: Analyser les applications - Azure App Service | Microsoft Docs
description: Découvrez comment superviser les applications dans Azure App Service à l’aide du portail Azure.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 1cfab9b065fd4e28a9ce11ac85682a298011200b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470123"
---
# <a name="monitor-apps-in-azure-app-service"></a>Superviser les applications dans Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) fournit des fonctionnalités de supervision intégrées pour les applications web, back-ends mobiles et applications API dans le [portail Azure](https://portal.azure.com).

Dans le portail Azure, vous pouvez examiner les *quotas* et les *métriques* d’une application, passer en revue le plan App Service et configurer automatiquement les *alertes* et la *mise à l’échelle* en fonction de ces métriques.

## <a name="understand-quotas"></a>Comprendre les quotas

Les applications hébergées dans App Service sont soumises à certaines limites concernant les ressources qu’elles peuvent utiliser. Ces limites sont définies par le plan App Service associé à l’application.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Si l’application est hébergée dans un plan *Gratuit* ou *Partagé*, les limites relatives aux ressources utilisables par l’application sont définies sous la forme de quotas.

Si l’application est hébergée dans un plan *De base*, *Standard* ou *Premium*, les limites applicables aux ressources que l’application peut utiliser sont définies par la *taille* (Petite, Moyenne, Grande) et par le *nombre d’instances* (1, 2, 3, et ainsi de suite) du plan App Service.

Les quotas des applications gratuites ou partagées sont les suivants :

| Quota | Description |
| --- | --- |
| **Temps UC (court)** | Temps processeur autorisé pour cette application dans un intervalle de 5 minutes. Ce quota se réinitialise toutes les cinq minutes. |
| **Temps UC (jour)** | Temps total processeur autorisé pour cette application sur une journée. Ce quota se réinitialise toutes les 24 heures à minuit en temps universel coordonné. |
| **Mémoire** | Quantité totale de mémoire autorisée pour cette application. |
| **Bande passante** | Quantité totale de bande passante sortante autorisée pour cette application sur une journée. Ce quota se réinitialise toutes les 24 heures à minuit en temps universel coordonné. |
| **Système de fichiers** | Quantité totale de stockage autorisée. |

Le seul quota applicable aux applications hébergées dans les plans *De base*, *Standard* et *Premium* est Système de fichiers.

Pour plus d’informations sur les quotas, limites et fonctionnalités spécifiques disponibles pour les différentes références (SKU) App Service, consultez [Limites du service d’abonnement Azure](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Application de quotas

Si une application dépasse le quota *Temps UC (court)* , *Temps UC (jour)* ou *Bande passante*, l’application est arrêtée jusqu’à la réinitialisation du quota. Pendant ce laps de temps, toutes les requêtes entrantes donnent lieu à une erreur HTTP 403.

![Message d’erreur 403][http403]

Si le quota Mémoire d’une application est dépassé, l’application est redémarrée.

En cas de dépassement du quota Système de fichiers, toute opération d’écriture échoue. Les échecs des opérations d’écriture incluent toutes les écritures dans les journaux d’activité.

Vous pouvez augmenter ou supprimer les quotas dans votre application en procédant à la mise à niveau de votre plan App Service.

## <a name="understand-metrics"></a>Comprendre les métriques

Les métriques fournissent des informations sur le comportement de l’application ou du plan App Service.

Pour une application, les métriques disponibles sont les suivantes :

| Métrique | Description |
| --- | --- |
| **Temps de réponse moyen** | Temps moyen, en secondes, nécessaire à l’application pour traiter les requêtes. |
| **Plage de travail moyenne de la mémoire** | Quantité moyenne de mémoire, en mégaoctets (Mio), utilisée par l’application. |
| **Connexions** | Nombre de sockets liés existants dans le bac à sable (w3wp.exe et ses processus enfants).  Un socket lié est créé en appelant les API bind()/connect(), et persiste jusqu’à être fermé avec CloseHandle()/closesocket(). |
| **Temps processeur** | Temps processeur, en secondes, consommée par l’application. Pour plus d’informations sur cette métrique, consultez [Temps processeur et pourcentage processeur](#cpu-time-vs-cpu-percentage). |
| **Assemblys actuels** | Nombre d’assemblys actuellement chargés dans tous les AppDomains dans cette application. |
| **Données entrantes** | Quantité de bande passante entrante, en Mio, consommée par l’application. |
| **Données sortantes** | Quantité de bande passante sortante, en Mio, consommée par l’application. |
| **Garbage collections de génération 0** | Nombre de fois que les objets de génération 0 ont été récupérés par le Garbage Collector depuis le début du processus d’application. Les garbage collections de génération supérieure comprennent toutes celles de génération inférieure.|
| **Garbage collections de génération 1** | Nombre de fois que les objets de génération 1 ont été récupérés par le Garbage Collector depuis le début du processus d’application. Les garbage collections de génération supérieure comprennent toutes celles de génération inférieure.|
| **Garbage collections de génération 2** | Nombre de fois que les objets de génération 2 ont été récupérés par le Garbage Collector depuis le début du processus d’application.|
| **Nombre de descripteurs** | Nombre total de handles actuellement ouverts par le processus d’application.|
| **Http 2xx** | Nombre de requêtes donnant lieu à un code d’état HTTP ≥ 200, mais < 300. |
| **Http 3xx** | Nombre de requêtes donnant lieu à un code d’état HTTP ≥ 300, mais < 400. |
| **Http 401** | Nombre de requêtes donnant lieu à un code d’état HTTP 401. |
| **Http 403** | Nombre de requêtes donnant lieu à un code d’état HTTP 403. |
| **Http 404** | Nombre de requêtes donnant lieu à un code d’état HTTP 404. |
| **Http 406** | Nombre de requêtes donnant lieu à un code d’état HTTP 406. |
| **Http 4xx** | Nombre de requêtes donnant lieu à un code d’état HTTP ≥ 400, mais < 500. |
| **Erreurs de serveur http** | Nombre de requêtes donnant lieu à un code d’état HTTP ≥ 500, mais < 600. |
| **Autres octets par seconde (E/S)** | Débit auquel le processus d’application émet des octets pour les opérations d’E/S qui n’impliquent pas de données, telles que les opérations de contrôle.|
| **Autres opérations par seconde (E/S)** | Débit auquel le processus d’application émet des opérations d’E/S qui ne sont ni des opérations de lecture, ni des opérations d’écriture.|
| **Octets lus par seconde (E/S)** | Débit auquel le processus d’application lit les octets à partir des opérations d’E/S.|
| **Opérations de lecture par seconde (E/S)** | Débit auquel le processus d’application émet des opérations d’E/S de lecture.|
| **Octets écrits par seconde (E/S)** | Débit auquel le processus d’application écrit des octets dans des opérations d’E/S.|
| **Opérations d’écriture par seconde (E/S)** | Débit auquel le processus d’application émet des opérations d’E/S d’écriture.|
| **Plage de travail de la mémoire** | Quantité actuelle de mémoire, en Mio, utilisée par l’application. |
| **Octets privés** | Taille actuelle (en octets) de mémoire allouée par le processus d’application qui ne peut pas être partagée avec d’autres processus.|
| **Demandes** | Nombre total de requêtes, quel que soit leur code d’état HTTP résultant. |
| **Requêtes dans la file d’attente d’application** | Nombre de requêtes dans la file d’attente de requêtes de l’application.|
| **Nombre de threads** | Nombre de threads actuellement actifs dans le processus d’application.|
| **Total des domaines d’application** | Nombre actuel de domaines d’application chargés dans cette application.|
| **Total des domaines d’application déchargés** | Nombre total de domaines d’application déchargés depuis le démarrage de l’application.|


Pour un plan App Service, les métriques disponibles sont les suivantes :

> [!NOTE]
> Les métriques du plan App Service sont disponibles uniquement pour les niveaux *De base*, *Standard* et *Premium*.
> 

| Métrique | Description |
| --- | --- |
| **Pourcentage UC** | Utilisation moyenne de l’UC dans toutes les instances du plan. |
| **Pourcentage de mémoire** | Utilisation moyenne de la mémoire dans toutes les instances du plan. |
| **Données entrantes** | Utilisation moyenne de la bande passante entrante dans toutes les instances du plan. |
| **Données sortantes** | Utilisation moyenne de la bande passante sortante dans toutes les instances du plan. |
| **Longueur de file d'attente de disque** | Nombre moyen de requêtes de lecture et d’écriture mises en file d’attente sur le stockage. Une longueur de file d’attente de disque élevée est une indication d’une application susceptible d’être ralentie en raison d’un nombre d’E/S de disque excessif. |
| **Longueur de la file d’attente HTTP** | Nombre moyen de requêtes HTTP qui devaient se trouver dans la file d’attente avant d’être exécutées. Une longueur de file d’attente HTTP élevée ou croissante est le symptôme d’un plan surchargé. |

### <a name="cpu-time-vs-cpu-percentage"></a>Temps processeur et pourcentage UC
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Deux métriques reflètent l’utilisation du processeur  :

**Temps processeur** : Utile pour les applications hébergées dans un plan Gratuit ou Partagé, car l’un des quotas de ces applications est défini en minutes de processeur utilisées par l’application.

**Pourcentage processeur** : Utile pour les applications hébergées dans des plans De base, Standard et Premium qui peuvent faire l’objet d’un scale-out. Le pourcentage CPU est une bonne indication de l’utilisation globale de toutes les instances.

## <a name="metrics-granularity-and-retention-policy"></a>Granularité des métriques et stratégie de conservation
Les métriques d’une application et d’un plan App Service sont journalisées et agrégées par le service avec les granularités et les stratégies de conservation suivantes :

* Les métriques de granularité **Minute** sont conservées 30 heures.
* Les métriques de granularité **Hour** sont conservées 30 jours.
* Les métriques de granularité **Day** sont conservées 30 jours.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Supervision des quotas et des métriques dans le portail Azure
Pour examiner l’état des différents quotas et métriques qui affectent une application, accédez au [portail Azure](https://portal.azure.com).

![Graphique des quotas dans le portail Azure][quotas]

Pour rechercher des quotas, sélectionnez **Paramètres** > **Quotas**. Sur le graphique, vous pouvez examiner : 
1. le nom du quota ;
1. son intervalle de réinitialisation ;
1. sa limite actuelle ;
1. sa valeur actuelle.

![Graphique des métriques dans le portail Azure][metrics] Vous pouvez accéder aux métriques directement à partir de la page de **ressources**. Pour personnaliser le graphique : 
1. Sélectionnez le graphique.
1. Sélectionnez **Modifier le graphique**.
1. Modifiez l’**intervalle de temps**.
1. Modifiez le **type de graphique**.
1. Modifiez les métriques à afficher.  

Pour plus d’informations sur les métriques, consultez [Superviser les métriques des services](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertes et mise à l’échelle automatique
Les métriques pour une application ou un plan App Service peuvent être rattachées à des alertes. Pour plus d’informations, consultez [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-alerts-portal.md).

Les applications App Service hébergées dans les plans App Service De base, Standard ou Premium prennent en charge la mise à l’échelle automatique. La mise à l’échelle automatique vous permet de configurer des règles qui supervisent les métriques du plan App Service. Les règles peuvent diminuer ou augmenter le nombre d’instances, ce qui permet de fournir des ressources supplémentaires si nécessaire. Les règles peuvent aussi vous permettre d’économiser de l’argent quand l’application est surprovisionnée.

Pour plus d’informations sur la mise à l’échelle automatique, consultez [Guide pratique pour mettre à l’échelle](../monitoring-and-diagnostics/insights-how-to-scale.md) et [Bonnes pratiques pour la mise à l’échelle automatique Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png