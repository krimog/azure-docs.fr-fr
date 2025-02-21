---
title: Basculement et mise à jour corrective - Azure Cache pour Redis | Microsoft Docs
description: Découvrez-en plus sur le basculement, la mise à jour corrective et le processus de mise à jour pour Azure Cache pour Redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 22c48441795e8aff9aba6540f15130452bcec2f7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819168"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Basculement et mise à jour corrective pour Azure Cache pour Redis

Pour générer des applications clientes résilientes et réussies, il est essentiel de comprendre ce qu’est un basculement dans le contexte du service Azure Cache pour Redis. Un basculement peut faire partie des opérations de gestion planifiées ou peut être dû à des défaillances matérielles ou réseau non planifiées. Une utilisation courante du basculement du cache est fournie lorsque le service de gestion corrige les fichiers binaires Azure Cache pour Redis. Cet article explique ce qu’est un basculement, comment il se produit pendant la mise à jour corrective et comment générer une application cliente résiliente.

## <a name="what-is-a-failover"></a>Qu’est-ce qu’un basculement ?

Commençons par un aperçu du basculement pour Azure Cache pour Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Un rapide résumé de l’architecture du cache

Un cache se compose de plusieurs machines virtuelles avec des adresses IP privées distinctes. Chaque machine virtuelle, également appelée nœud, est connectée à un équilibreur de charge partagé avec une seule adresse IP virtuelle. Chaque nœud exécute le processus serveur Redis et est accessible par le biais du nom d’hôte et des ports Redis. Chaque nœud est considéré comme un nœud maître ou réplica. Quand une application cliente se connecte à un cache, son trafic passe par cet équilibreur de charge et est automatiquement routé au nœud maître.

Dans un cache de base, le nœud unique est toujours un maître. Un cache Standard ou Premium comprend deux nœuds : l’un est choisi comme maître et l’autre est le réplica. Étant donné que les caches Standard et Premium ont plusieurs nœuds, un nœud peut être indisponible pendant que l’autre continue à traiter les requêtes. Les caches en cluster sont constitués de plusieurs partitions, chacune ayant un nœud maître et un nœud réplica distincts. Une partition peut être en panne pendant que les autres restent disponibles.

> [!NOTE]
> Un cache de base n’a pas plusieurs nœuds et n’offre pas de contrat de niveau de service (SLA) en matière de disponibilité. Les caches de base sont recommandés uniquement à des fins de développement et de test. Utilisez un cache Standard ou Premium pour un déploiement sur plusieurs nœuds afin d’augmenter la disponibilité.

### <a name="explanation-of-a-failover"></a>Explication d’un basculement

Un basculement se produit quand un nœud de réplica se promeut en nœud maître et quand l’ancien nœud maître ferme des connexions existantes. Une fois le nœud maître rétabli, il remarque le changement de rôle et se rétrograde pour devenir un réplica. Il se connecte ensuite au nouveau maître et synchronise les données. Un basculement peut être planifié ou non.

Un *basculement planifié* a lieu pendant les mises à jour du système (par exemple, mises à jour correctives Redis ou mises à niveau du système d’exploitation) et les opérations de gestion (par exemple, mise à l’échelle et redémarrage). Étant donné que les nœuds sont avertis à l’avance de la mise à jour, ils peuvent échanger des rôles de manière coopérative et indiquer rapidement la modification à l’équilibreur de charge. Un basculement planifié se termine généralement en moins de 1 seconde.

Un *basculement non planifié* peut se produire en raison d’une défaillance matérielle, d’une défaillance du réseau ou d’autres pannes inattendues du nœud maître. Le nœud réplica se promeut en maître, mais le processus prend plus de temps. Un nœud réplica doit tout d’abord détecter que son nœud maître n’est pas disponible avant de pouvoir lancer le processus de basculement. Le nœud réplica doit également vérifier que cette défaillance non planifiée n’est pas temporaire ni locale afin d’éviter un basculement inutile. Ce retard de détection signifie qu’un basculement non planifié se termine généralement dans un délai de 10 à 15 secondes.

## <a name="how-does-patching-occur"></a>Comment la mise à jour corrective a-t-elle lieu ?

Le service Azure Cache pour Redis met régulièrement à jour votre cache avec les fonctionnalités et les correctifs les plus récents de la plateforme. Pour corriger un cache, le service effectue les étapes suivantes :

1. Le service de gestion sélectionne un nœud à corriger.
1. Si le nœud sélectionné est un nœud maître, son nœud réplica se promeut de manière coopérative. Cette promotion est considérée comme un basculement planifié.
1. Le nœud sélectionné redémarre pour accepter les nouveaux changements, puis revient comme nœud réplica.
1. Le nœud réplica se connecte au nœud maître et synchronise les données.
1. Une fois la synchronisation des données terminée, le processus de mise à jour corrective se répète pour les nœuds restants.

Étant donné que la mise à jour corrective est un basculement planifié, le nœud réplica se promeut rapidement en maître et commence à traiter les requêtes et les nouvelles connexions. Les caches de base n’ont pas de nœud réplica et sont indisponibles tant que la mise à jour n’est pas terminée. Chaque partition d’un cache en cluster est corrigée séparément et ne ferme pas les connexions à une autre partition.

> [!IMPORTANT]
> Les nœuds sont corrigés un à un pour éviter toute perte de données. Les caches de base subissent une perte de données. Les caches en cluster sont corrigés une partition à la fois.

Si plusieurs caches se trouvent dans le même groupe de ressources et la même région, ils sont également corrigés un à un.  Les caches qui se trouvent dans des groupes de ressources différents ou des régions différentes peuvent être corrigés simultanément.

Étant donné que la synchronisation complète des données se produit avant la répétition du processus, il est peu probable que des données soient perdues lorsque vous utilisez un cache Standard ou Premium. Vous pouvez vous prémunir davantage contre la perte de données en [exportant](cache-how-to-import-export-data.md#export) les données et en activant la [persistance](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Charge de cache supplémentaire

Chaque fois qu’un basculement se produit, les caches Standard et Premium doivent répliquer les données d’un nœud à l’autre. Cette réplication entraîne une augmentation de la charge au niveau de la mémoire et du processeur du serveur. Si l’instance de cache est déjà très chargée, les applications clientes peuvent subir une latence accrue. Dans des cas extrêmes, les applications clientes peuvent recevoir des exceptions de délai d’expiration. Pour permettre d’atténuer l’impact de cette charge supplémentaire, [configurez](cache-configure.md#memory-policies) le paramètre `maxmemory-reserved` du cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>Quel est l’impact d’un basculement sur mon application cliente ?

Le nombre d’erreurs détectées par l’application cliente dépend du nombre d’opérations en attente sur cette connexion au moment du basculement. Toute connexion acheminée par le biais du nœud ayant fermé ses connexions constate des erreurs. De nombreuses bibliothèques de clients peuvent lever différents types d’erreurs en cas d’interruption des connexions, notamment des exceptions de délai d’expiration, des exceptions de connexion ou des exceptions de socket. Le nombre et le type d’exceptions dépendent de l’emplacement de la demande dans le chemin du code quand le cache ferme ses connexions. Par exemple, une opération qui envoie une requête mais qui ne reçoit pas de réponse quand le basculement se produit peut obtenir une exception de délai d’expiration. Les nouvelles requêtes sur l’objet de connexion fermé reçoivent des exceptions de connexion jusqu’à ce que la connexion soit rétablie.

La plupart des bibliothèques clientes tentent de se reconnecter au cache si elles sont configurées pour ce faire. Toutefois, des bogues imprévus peuvent parfois placer les objets de bibliothèque dans un état irrécupérable. Si les erreurs persistent plus longtemps qu’une durée préconfigurée, l’objet de connexion doit être recréé. Dans Microsoft.NET et d’autres langages orientés objet, il est possible de recréer la connexion sans redémarrer l’application à l’aide d’un [modèle Lazy\<T\>](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Comment faire rendre mon application résiliente ?

Étant donné que vous ne pouvez pas éviter complètement les basculements, écrivez vos applications clientes à des fins de résilience à des arrêts de connexion et des demandes ayant échoué. Bien que la plupart des bibliothèques de clients se reconnectent automatiquement au point de terminaison de cache, seules quelques-unes retentent les requêtes ayant échoué. Selon le scénario de l’application, il peut être utile d’utiliser la logique de nouvelle tentative avec retrait.

Pour tester la résilience d’une application cliente, utilisez un [redémarrage](cache-administration.md#reboot) comme déclencheur manuel pour les interruptions de connexion. En outre, nous vous recommandons de [planifier des mises à jour](cache-administration.md#schedule-updates) sur un cache. Demandez au service de gestion d’appliquer des correctifs de runtime Redis pendant les fenêtres hebdomadaires spécifiées. Ces fenêtres correspondent généralement à des périodes où le trafic de l’application cliente est faible pour éviter les incidents potentiels.

### <a name="client-network-configuration-changes"></a>Modifications de la configuration réseau du client

Certaines modifications de la configuration réseau côté client peuvent déclencher des erreurs de type « Aucune connexion disponible ». Ces modifications peuvent inclure :

- Le remplacement de l’adresse IP virtuelle d’une application cliente entre les emplacements intermédiaires et de production.
- Mise à l’échelle de la taille ou du nombre d’instances de votre application.

De telles modifications peuvent entraîner un problème de connectivité qui dure moins d’une minute. Il est probable que votre application cliente perde sa connexion à d’autres ressources réseau externes en plus du service Azure Cache pour Redis.

## <a name="next-steps"></a>Étapes suivantes

- [Planifiez les mises à jour](cache-administration.md#schedule-updates) pour votre cache.
- Testez la résilience des applications à l’aide d’un [redémarrage](cache-administration.md#reboot).
- [Configurez](cache-configure.md#memory-policies) des réservations et des stratégies de mémoire.
