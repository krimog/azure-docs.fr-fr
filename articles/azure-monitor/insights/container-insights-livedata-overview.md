---
title: Voir Live Data (préversion) avec Azure Monitor pour conteneurs | Microsoft Docs
description: Cet article décrit la vue en temps réel des journaux, métriques de pod et événements Kubernetes, sans l’utilisation de kubectl, dans Azure Monitor pour conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 9c497c73d42e1fb8851c5293010098d843297250
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510770"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Comment voir les journaux, métriques de pod et événements Kubernetes en temps réel

Azure Monitor pour conteneurs comprend la fonctionnalité Live Data (préversion), une fonctionnalité de diagnostic avancé qui vous permet d’accéder directement aux journaux (stdout/stderror), métriques de pod et événements de conteneur Azure Kubernetes Service (AKS). Elle expose un accès direct aux `kubectl logs -c`, aux événements `kubectl get` et aux `kubectl top pods`. Un volet de la console affiche les journaux, les métriques et les événements générés par le moteur de conteneur, afin de faciliter la résolution des problèmes en temps réel.

Cet article fournit une vue d’ensemble détaillée et vous aide à mieux comprendre l’utilisation de cette fonctionnalité. 

>[!NOTE]
>Les clusters AKS activés en tant que [clusters privés](https://azure.microsoft.com/updates/aks-private-cluster/) ne sont pas pris en charge par cette fonctionnalité. Cette fonctionnalité repose sur l’accès direct à l’API Kubernetes via un serveur proxy depuis votre navigateur. L’activation de la sécurité réseau pour bloquer l’API Kubernetes depuis ce proxy bloque ce trafic. 

>[!NOTE]
>Cette fonctionnalité est disponible dans toutes les régions Azure, y compris Azure - Chine. Elle n’est actuellement pas disponible dans la région Azure - Gouvernement des États-Unis.

Pour obtenir de l’aide lors de la configuration ou de la résolution des problèmes se rapportant à la fonctionnalité Live Data (préversion), consultez notre [guide de configuration](container-insights-livedata-setup.md). Comme cette fonctionnalité accède directement à l’API Kubernetes, des informations supplémentaires sur le modèle d’authentification sont disponibles [ici](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Vue d’ensemble de la fonctionnalité Live Data (préversion)

### <a name="search"></a>Recherche

![Exemple de filtre du volet de la console Live Data](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

La fonctionnalité Live Data (préversion) comprend la fonctionnalité de recherche. Dans le champ **Rechercher**, vous pouvez filtrer les résultats en tapant un mot clé ou un terme ; tous les résultats correspondants sont mis en surbrillance pour permettre une consultation rapide. Lors de l’affichage d’événements, vous pouvez également limiter les résultats à l’aide du bouton **Filtrer** à droite de la barre de recherche. Selon la ressource que vous avez sélectionnée, un pod, un espace de noms ou un cluster sont répertoriés à partir desquels choisir.  

### <a name="scroll-lock-and-pause"></a>Arrêt défil et Pause 

Pour suspendre le défilement automatique, et contrôler le comportement du volet en faisant défiler manuellement les nouvelles données lues, servez-vous de l’option **Faire défiler**. Pour réactiver le défilement automatique, sélectionnez de nouveau l’option **Faire défiler**. Vous pouvez aussi interrompre la récupération des données de journal ou d’événement en sélectionnant l’option **Suspendre**. Lorsque vous êtes prêt à reprendre la récupération, sélectionnez **Lire**.  

![Vue en direct interrompue dans le volet de la console Live Data](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Nous recommandons uniquement de suspendre ou d’interrompre le défilement automatique pendant une brève période de temps, lors de la résolution d’un problème. Ces requêtes peuvent avoir un impact sur la disponibilité et la limitation de l’API Kubernetes sur votre cluster. 

>[!IMPORTANT]
>Aucune donnée n’est stockée de façon permanente pendant le fonctionnement de cette fonctionnalité. Toutes les informations capturées lors de la session sont supprimées lorsque vous la quittez, ou que vous fermez votre navigateur. Les données restent uniquement présentes pour la visualisation dans le laps de temps de cinq minutes de la fonctionnalité Métriques ; toutes les mesures datant de plus de cinq minutes sont également supprimées. La mémoire tampon de Live Data (préversion) interroge dans des limites raisonnables de l’utilisation de la mémoire. 

## <a name="view-logs"></a>Afficher les journaux d’activité

Vous pouvez voir les données de journal en temps réel, à mesure qu’elles sont générées par le moteur de conteneur depuis la vue **Nœuds**, **Contrôleurs** et **Conteneurs**. Pour voir les données de journal, effectuez les étapes suivantes.

1. Dans le portail Azure, accédez au groupe de ressources de cluster AKS et sélectionnez votre ressource AKS.

2. Dans le tableau de bord du cluster AKS, sous **Supervision** à gauche, choisissez **Insights**. 

3. Sélectionnez l’onglet **Nœuds**, **Contrôleurs** ou **Conteneurs**.

4. Sélectionnez un objet dans la grille de performances, puis dans le volet des propriétés situé sur le côté droit, sélectionnez l’option **Afficher les données actives (préversion)** . Si le cluster AKS est configuré pour l’authentification unique avec Azure AD, vous êtes invité à vous authentifier lors de la première utilisation sur cette session de navigateur. Sélectionnez votre compte et finalisez l’authentification auprès d’Azure.  

    >[!NOTE]
    >Lorsque vous consultez les données depuis votre espace de travail Log Analytics en sélectionnant l’option **Afficher dans Analytics** du volet des propriétés, les résultats de la recherche dans les journaux peuvent éventuellement afficher des **Nœuds**, **Ensembles de démons**, **Jeux de réplicas**, **Travaux**, **Tâches Cron**, **Pods** et **Conteneurs** qui n’existent peut-être plus. Une tentative de recherche dans les journaux d’un conteneur qui n’est pas disponible dans `kubectl` échouera également ici. Pour en savoir plus sur la consultation des journaux, événements et métriques historiques, consultez la fonctionnalité [Voir dans Analytics](container-insights-log-search.md#search-logs-to-analyze-data).  

Une fois l’authentification réussie, le volet de la console Live Data (préversion) apparaît sous la grille des données de performances, dans laquelle vous pouvez voir des données de journal en flux continu. Si l’indicateur d’état de récupération affiche une coche verte tout à droite du volet, cela signifie que des données peuvent être récupérées et que leur envoi en streaming dans votre console commence.  

![Option Voir les données du volet des propriétés du nœud](./media/container-insights-livedata-overview/node-properties-pane.png)  

Le titre du volet indique le nom du pod avec lequel le conteneur est groupé.

## <a name="view-events"></a>Visualiser les événements

Vous pouvez voir les données d’événement en temps réel, à mesure qu’elles sont générées par le moteur de conteneur depuis la vue **Nœuds**, **Contrôleurs**, **Conteneurs** et **Déploiements (préversion)** , lorsqu’un conteneur, pod, nœud, jeu de réplicas, ensemble de démons, travail, tâche Cron ou déploiement est sélectionné. Pour voir les événements, effectuez les étapes suivantes.

1. Dans le portail Azure, accédez au groupe de ressources de cluster AKS et sélectionnez votre ressource AKS.

2. Dans le tableau de bord du cluster AKS, sous **Supervision** à gauche, choisissez **Insights**. 

3. Sélectionnez l’onglet **Nœuds**, **Contrôleurs**, **Conteneurs** ou **Déploiements (préversion)** .

4. Sélectionnez un objet dans la grille de performances, puis dans le volet des propriétés situé sur le côté droit, sélectionnez l’option **Afficher les données actives (préversion)** . Si le cluster AKS est configuré pour l’authentification unique avec Azure AD, vous êtes invité à vous authentifier lors de la première utilisation sur cette session de navigateur. Sélectionnez votre compte et finalisez l’authentification auprès d’Azure.  

    >[!NOTE]
    >Lorsque vous consultez les données depuis votre espace de travail Log Analytics en sélectionnant l’option **Afficher dans Analytics** du volet des propriétés, les résultats de la recherche dans les journaux peuvent éventuellement afficher des **Nœuds**, **Ensembles de démons**, **Jeux de réplicas**, **Travaux**, **Tâches Cron**, **Pods** et **Conteneurs** qui n’existent peut-être plus. Une tentative de recherche dans les journaux d’un conteneur qui n’est pas disponible dans `kubectl` échouera également ici. Pour en savoir plus sur la consultation des journaux, événements et métriques historiques, consultez la fonctionnalité [Voir dans Analytics](container-insights-log-search.md#search-logs-to-analyze-data).  

Une fois l’authentification réussie, le volet de la console Live Data (préversion) apparaît sous la grille des données de performances. Si l’indicateur d’état de récupération affiche une coche verte tout à droite du volet, cela signifie que des données peuvent être récupérées et que leur envoi en streaming dans votre console commence. 
    
Si l’objet que vous avez sélectionné était un conteneur, sélectionnez l’option **Événements** dans le volet. Si vous avez sélectionné un nœud, un pod ou un contrôleur, l’affichage des événements est automatiquement sélectionné. 

    ![Controller properties pane view events](./media/container-insights-livedata-overview/controller-properties-live-events.png)  

Le titre du volet indique le nom du pod avec lequel le conteneur est groupé.

### <a name="filter-events"></a>Filtrer les événements 

Lors de l’affichage d’événements, vous pouvez également limiter les résultats à l’aide du bouton **Filtrer** à droite de la barre de recherche. Selon la ressource que vous avez sélectionnée, un pod, un espace de noms ou un cluster sont répertoriés à partir desquels choisir.  

## <a name="view-metrics"></a>Afficher les mesures 

Vous pouvez voir les données de métrique en temps réel, à mesure qu’elles sont générées par le moteur de conteneur depuis la vue **Nœuds** ou **Contrôleurs**, uniquement quand un **Pod** est sélectionné. Pour voir les métriques, effectuez les étapes suivantes.

1. Dans le portail Azure, accédez au groupe de ressources de cluster AKS et sélectionnez votre ressource AKS.

2. Dans le tableau de bord du cluster AKS, sous **Supervision** à gauche, choisissez **Insights**. 

3. Sélectionnez l’onglet **Nœuds** ou **Contrôleurs**.

4. Sélectionnez un objet **Pod** dans la grille de performances, puis dans le volet des propriétés situé sur le côté droit, sélectionnez l’option **Afficher les données actives (préversion)** . Si le cluster AKS est configuré pour l’authentification unique avec Azure AD, vous êtes invité à vous authentifier lors de la première utilisation sur cette session de navigateur. Sélectionnez votre compte et finalisez l’authentification auprès d’Azure.  

    >[!NOTE]
    >Lorsque vous consultez les données depuis votre espace de travail Log Analytics en sélectionnant l’option **Afficher dans Analytics** du volet des propriétés, les résultats de la recherche dans les journaux peuvent éventuellement afficher des **Nœuds**, **Ensembles de démons**, **Jeux de réplicas**, **Travaux**, **Tâches Cron**, **Pods** et **Conteneurs** qui n’existent peut-être plus. Une tentative de recherche dans les journaux d’un conteneur qui n’est pas disponible dans `kubectl` échouera également ici. Pour en savoir plus sur la consultation des journaux, événements et métriques historiques, consultez la fonctionnalité [Voir dans Analytics](container-insights-log-search.md#search-logs-to-analyze-data).  

Une fois l’authentification réussie, le volet de la console Live Data (préversion) apparaît sous la grille des données de performances. Les données de métriques sont récupérées, et leur envoi en streaming vers votre console commence pour leur présentation dans les deux graphiques. Le titre du volet indique le nom du pod avec lequel le conteneur est groupé.

![Exemple de métriques de l’affichage Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Étapes suivantes

- Pour continuer à découvrir comment utiliser Azure Monitor et surveiller les autres aspects de votre cluster AKS, consultez [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).

- Consultez [Exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir des requêtes et des exemples prédéfinis permettant de créer des alertes, des visualisations, ou d’effectuer une analyse plus poussée de vos clusters.