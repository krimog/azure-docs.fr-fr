---
title: 'Exemple PowerShell : équilibrer la charge de plusieurs sites web avec Azure PowerShell | Microsoft Docs'
description: Cet exemple de script Azure PowerShell montre comment équilibrer la charge de plusieurs sites web sur la même machine virtuelle
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.openlocfilehash: 098d300efc0b151d07b087959750a209c392872f
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273613"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Exemple de script Azure PowerShell : Équilibrer la charge de plusieurs sites web

Cet exemple de script Azure PowerShell crée un réseau virtuel avec deux machines virtuelles qui sont membres d’un groupe à haute disponibilité. Un équilibrage de charge dirige le trafic pour les deux adresses IP distinctes vers les deux machines virtuelles. Après avoir exécuté le script, vous pouvez déployer le logiciel de serveur web pour les machines virtuelles et héberger plusieurs sites web, chacun avec sa propre adresse IP.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Crée un groupe de disponibilité Azure pour fournir une haute disponibilité. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crée une configuration de sous-réseau. Cette configuration est utilisée avec le processus de création du réseau virtuel. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Créer un réseau virtuel. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crée une adresse IP publique. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Crée une configuration IP frontale pour un équilibreur de charge. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Crée une configuration de pool d’adresses principales pour un équilibreur de charge. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Crée une sonde d’équilibrage de charge réseau. Une sonde d’équilibrage de charge réseau permet de surveiller chaque machine virtuelle dans le jeu d’équilibrage de charge réseau. Si une machine virtuelle n’est plus accessible, le trafic n’est pas acheminé vers cette machine virtuelle. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Crée une règle d’équilibrage de charge réseau. Dans cet exemple, une règle est créée pour le port 80. Comme le trafic HTTP arrive au niveau de l’équilibrage de charge réseau, il est acheminé vers le port 80 de l’une des machines virtuelles du jeu d’équilibrage de charge réseau. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Crée un équilibreur de charge. |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | Définit les fonctionnalités avancées d’une interface réseau virtuelle. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Crée une interface réseau. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crée une configuration de machine virtuelle. Cette configuration inclut des informations telles que le nom de la machine virtuelle, le système d’exploitation et les informations d’identification d’administration. La configuration est utilisée lors de la création de machines virtuelles. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Création d’une machine virtuelle |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous pouvez trouver des exemples supplémentaires de scripts PowerShell de mise en réseau dans la [documentation Vue d’ensemble de la mise en réseau Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
