---
title: Solution Azure VMware de CloudSimple - Vue d’ensemble sur les nœuds
description: Découvrez les nœuds et concepts CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 729b832c068dcd401fa6e9d2f4af9193d3859a83
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845546"
---
# <a name="cloudsimple-nodes-overview"></a>Vue d'ensemble des nœuds CloudSimple

Les nœuds sont les blocs de construction d'un cloud privé. Un nœud est :

* Un hôte de calcul complet dédié sur lequel l'hyperviseur VMware ESXi est installé  
* Une unité de calcul que vous pouvez acheter ou réserver pour créer des clouds privés
* Disponible à l'achat ou à la réservation dans une région où le service CloudSimple est disponible

Vous créez un cloud privé à partir des nœuds achetés. Pour créer un cloud privé, il vous faut au moins trois nœuds de la même référence SKU. Pour développer un cloud privé, ajoutez-y des nœuds.  Vous pouvez ajouter des nœuds à un cluster existant ou créer un nouveau cluster en achetant des nœuds sur le Portail Azure et en les associant au service CloudSimple.  Tous les nœuds achetés sont visibles sous le service CloudSimple.  

## <a name="purchased-nodes"></a>Nœuds achetés

Les nœuds achetés peuvent faire l'objet d'un paiement à l'utilisation. L'achat de nœuds vous permet de procéder rapidement et à la demande à une mise à l'échelle de votre cluster VMware. Si nécessaire, vous pouvez ajouter des nœuds, ou supprimer un nœud acheté pour faire descendre en puissance votre cluster VMware. Les nœuds achetés sont facturés sur une base mensuelle et imputés à l'abonnement avec lequel ils sont achetés.

* Si vous payez votre abonnement Azure par carte de crédit, celle-ci est immédiatement débitée.
* Si vous réglez sur facture, les frais sont portés sur votre facture suivante.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Références SKU de nœuds pour solution VMware de CloudSimple

Les types de nœuds suivants sont disponibles à l'achat ou à la réservation.

| SKU           | CS28 - Nœud                 | CS36 - Nœud                 | CS36m - Nœud                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Région        | USA Est, USA Ouest            | USA Est, USA Ouest            | Europe Ouest                 |
| UC           | 2 x 2,2 GHz, 28 cœurs (56 HT) | 2 x 2,3 GHz, 36 cœurs (72 HT) | 2 x 2,3 GHz, 36 cœurs (72 HT) |
| RAM           | 256 Go                      | 512 Go                      | 576 Go                      |
| Disque de cache    | NVMe de 1,6 To                 | NVMe de 3,2 To                 | NVMe de 3,2 To                 |
| Capacité de disque | 5,625 To de données brutes                | 11,25 To de données brutes                | 15,36 To de données brutes                |
| Type de stockage  | Flash                   | Flash                   | Flash                   |

## <a name="limits"></a>limites

Les limites de nœud suivantes s'appliquent aux clouds privés.

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour la création d'un cloud privé | 3 |
| Nombre maximal de nœuds dans un cluster sur un cloud privé | 16 |
| Nombre maximal de nœuds dans un cloud privé | 64 |
| Nombre minimal de nœuds sur un nouveau cluster | 3 |

## <a name="next-steps"></a>Étapes suivantes

* Apprenez à [acheter des nœuds](create-nodes.md)
* Découvrez-en plus sur le [Cloud privé](cloudsimple-private-cloud.md)
