---
title: Vérifier l’utilisation des ressources Azure par rapport aux limites | Microsoft Docs
description: Découvrez comment vérifier l’utilisation de vos ressources Azure par rapport aux limites de l’abonnement Azure.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jeconnoc
ms.openlocfilehash: b8bcbabe4b355e4e9cd617e9003902885b8edd88
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872455"
---
# <a name="check-resource-usage-against-limits"></a>Vérifier l’utilisation des ressources par rapport aux limites

Dans cet article, vous apprendrez comment afficher le nombre de chaque type de ressource réseau que vous avez déployé dans votre abonnement et à connaître les [limites de votre abonnement](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). La possibilité d’afficher l’utilisation des ressources par rapport aux limites est utile pour suivre l’utilisation actuelle et planifier l’utilisation ultérieure. Vous pouvez utiliser le [portail Azure](#azure-portal), [PowerShell](#powershell) ou [Azure CLI](#azure-cli) pour suivre l’utilisation.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail](https://portal.azure.com) Azure.
2. En haut à gauche du portail Azure, sélectionnez **Tous les services**.
3. Dans la zone **Filtre**, entrez *Abonnements*. Quand la mention **Abonnements** apparaît dans les résultats de recherche, sélectionnez-la.
4. Sélectionnez le nom de l’abonnement pour lequel vous souhaitez afficher les informations d’utilisation.
5. Sous **PARAMÈTRES**, sélectionnez **Utilisation + quota**.
6. Vous pouvez sélectionner les options suivantes :
   - **Types de ressources** : vous pouvez sélectionner tous les types de ressources ou sélectionner les types de ressources spécifiques que vous souhaitez afficher.
   - **Fournisseurs** : vous pouvez sélectionner tous les fournisseurs de ressources ou sélectionner **Compute**, **Réseau** ou **Stockage**.
   - **Emplacements** : vous pouvez sélectionner tous les emplacements Azure ou sélectionner des emplacements spécifiques.
   - Vous pouvez choisir d’afficher toutes les ressources ou uniquement les ressources où au moins une ressource est déployée.

     Dans l’image suivante, l’exemple montre toutes les ressources réseau avec au moins une ressource déployée dans USA Est :

       ![Afficher les données d’utilisation](./media/check-usage-against-limits/view-usage.png)

     Vous pouvez trier les colonnes en sélectionnant l’en-tête de colonne. Les limites affichées sont celles de votre abonnement. Si vous avez besoin d’augmenter la limite par défaut, sélectionnez **Demander une augmentation**, terminez la tâche, puis envoyez la demande de support. Toutes les ressources ont une limite maximale répertoriée dans les [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Azure. Si votre limite actuelle a déjà atteint le chiffre maximal, la limite ne peut pas être augmentée.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez exécuter les commandes qui suivent dans [Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif gratuit. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Si vous exécutez PowerShell sur votre ordinateur, vous devez utiliser le module Azure PowerShell version 1.0.0 ou ultérieure. Exécutez `Get-Module -ListAvailable Az` sur votre ordinateur pour trouver la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Login-AzAccount` pour vous connecter à Azure.

Consultez votre utilisation par rapport aux limites avec [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). L’exemple suivant obtient l’utilisation des ressources où au moins une ressource est déployée dans l’emplacement USA Est :

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Le résultat que vous recevez est formaté de la même manière que ce qui suit :

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>D’Azure CLI

Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Azure CLI version 2.0.32 ou ultérieure est nécessaire pour cet article. Exécutez `az --version` pour rechercher la version installée. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI en local, vous devez également exécuter `az login` pour vous connecter à Azure.

Consultez votre utilisation par rapport aux limites avec [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). L’exemple suivant obtient l’utilisation des ressources dans l’emplacement USA Est :

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Le résultat que vous recevez est formaté de la même manière que ce qui suit :

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
