---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237339"
---
Le service Stockage Azure vous est facturé en fonction de l’utilisation de votre compte de stockage. Tous les objets d’un compte de stockage sont facturés ensemble en tant que groupe. 

Les coûts de stockage sont calculés en fonction des facteurs suivants : région/emplacement, type de compte, niveau d’accès, capacité de stockage, schéma de réplication, transactions de stockage et sortie des données.

* **Région** fait référence à la région géographique dans laquelle votre compte est basé.
* **Type de compte** fait référence au type de compte de stockage que vous utilisez. 
* **Niveau d’accès** fait référence au modèle d’utilisation des données que vous avez spécifié pour votre compte de stockage d’utilisation générale v2 ou Blob.
* La **capacité** de stockage fait référence aux unités du compte de stockage que vous utilisez pour stocker des données.
* La **réplication** détermine le nombre de copies de vos données qui sont conservées simultanément et où.
* Les **transactions** font référence à toutes les opérations de lecture et d’écriture sur Stockage Azure.
* **Sortie de données** fait référence aux données transférées hors d’une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n’est pas exécutée dans la même région, vous êtes facturé pour l’acheminement des données Pour plus d’informations sur l’utilisation de groupes de ressources pour regrouper vos données et vos services dans la même région afin de limiter les coûts liés aux sorties de données, consultez [Qu’est-ce qu’un groupe de ressources Azure ?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

La page [Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/) fournit des informations de tarification détaillées basées sur le type de compte, la capacité de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour les acheminements de données. Vous pouvez utiliser le [Calcul des coûts Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) pour faciliter l'estimation des coûts.

