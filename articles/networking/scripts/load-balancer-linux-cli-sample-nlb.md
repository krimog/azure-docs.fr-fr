---
title: Exemple de script CLI Azure - Équilibrage de charge du trafic vers les machines virtuelles pour haute disponibilité | Microsoft Docs
description: Exemple de script CLI Azure - Équilibrage de charge du trafic vers les machines virtuelles pour haute disponibilité
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 916e9e1f7287757f19e93ecf7b8cc38f556e2dc8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564644"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Équilibrer le trafic sur les machines virtuelles pour la haute disponibilité

Cet exemple de script crée tous les éléments nécessaires pour exécuter plusieurs machines virtuelles Ubuntu configurées dans une configuration haute disponibilité avec équilibrage de la charge. Une fois que vous avez exécuté le script, vous obtenez trois machines virtuelles jointes à un groupe à haute disponibilité Azure et accessibles par le biais d’Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Crée un réseau virtuel et un sous-réseau Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Crée une adresse IP publique avec une adresse IP statique et un nom DNS associé. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Crée un équilibreur de charge Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Crée une sonde d’équilibreur de charge. Les sondes d’équilibreurs de charge permettent de surveiller chaque machine virtuelle d’un jeu d’équilibrage de charge. Si une machine virtuelle n’est plus accessible, le trafic n’est pas acheminé vers cette machine virtuelle. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Crée une règle d’équilibeur de charge. Dans cet exemple, une règle est créée pour le port 80. Le trafic HTTP qui arrive à l’équilibreur de charge est acheminé vers le port 80 de l’une des machines virtuelles du jeu d’équilibrage de charge. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) | Crée une règle de traduction d’adresses réseau (NAT) pour l’équilibreur de charge.  Les règles NAT mappent un port de l’équilibreur de charge avec un port d’une machine virtuelle. Dans cet exemple, une règle NAT est créée pour le trafic SSH en direction de chaque machine virtuelle au sein du jeu d’équilibrage de charge.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Crée un groupe de sécurité réseau qui représente une frontière de sécurité entre Internet et la machine virtuelle. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Crée une règle de groupe de sécurité réseau permettant d’autoriser le trafic entrant. Dans cet exemple, le port 22 est ouvert pour le trafic SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Crée une carte réseau virtuelle et l’associe au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Crée un groupe à haute disponibilité. Les groupes à haute disponibilité garantissent le temps de fonctionnement des applications en répartissant les machines virtuelles sur les ressources physiques de sorte que, en cas d’échec, l’ensemble du groupe ne soit pas affecté. |
| [az vm create](/cli/azure/vm) | Crée la machine virtuelle et l’associe à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous pouvez trouver des exemples supplémentaires de scripts CLI de la mise en réseau Azure dans la [documentation de la mise en réseau Azure](../cli-samples.md).
