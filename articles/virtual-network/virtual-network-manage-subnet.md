---
title: Ajouter, changer ou supprimer un sous-réseau de réseau virtuel Azure
titlesuffix: Azure Virtual Network
description: Découvrez comment ajouter, modifier ou supprimer un sous-réseau de réseau virtuel dans Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: 4af8c6dbbcbb877351018eb881f4d3b0b447bbce
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098982"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel

Découvrez comment ajouter, modifier ou supprimer un sous-réseau de réseau virtuel. Toutes les ressources Azure déployées dans un réseau virtuel sont déployées dans un sous-réseau au sein d’un réseau virtuel. Si vous découvrez les réseaux virtuels, vous trouverez plus d’informations à leur sujet dans l’article [Vue d’ensemble des réseaux virtuels](virtual-networks-overview.md) ou en suivant un [tutoriel](quick-create-portal.md). Pour savoir comment créer, modifier ou supprimer un réseau virtuel, consultez [Gérer un réseau virtuel](manage-virtual-network.md).

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 1.0.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel requiert Azure CLI version 2.0.31 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

Le compte auquel vous vous connectez, ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans [Autorisations](#permissions).

## <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau auquel vous souhaitez ajouter un sous-réseau.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Sélectionnez **+Sous-réseau**.
5. Entrez des valeurs pour les paramètres suivants :
   - **Nom** : Le nom doit être unique au sein du réseau virtuel. Pour une compatibilité maximale avec d’autres services Azure, nous recommandons d’utiliser une lettre comme premier caractère du nom. Par exemple, Azure Application Gateway ne se déploiera pas dans un sous-réseau portant un nom qui commence par un nombre.
   - **Plage d’adresses** : La plage doit être unique dans l’espace d’adressage du réseau virtuel. La plage ne peut pas chevaucher d’autres plages d’adresses de sous-réseau au sein du réseau virtuel. L’espace d’adressage doit être spécifié en utilisant la notation de routage CIDR (Classless InterDomain Routing). Par exemple, dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, vous pouvez définir l’espace d’adressage de sous-réseau 10.0.0.0/24. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, définir un sous-réseau avec une plage d’adresses /29 produit trois adresses IP utilisables dans le sous-réseau. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Vous pouvez modifier la plage d’adresses après l’ajout du sous-réseau sous certaines conditions. Pour savoir comment modifier une plage d’adresses de sous-réseau, consultez [Modifier les paramètres de sous-réseau](#change-subnet-settings).
   - **Groupe de sécurité réseau** : Vous pouvez associer un groupe de sécurité réseau existant au sous-réseau pour filtrer le trafic réseau entrant et sortant du sous-réseau, ou n’associer aucun groupe. Le groupe de sécurité réseau doit exister dans le même abonnement et au même emplacement que le réseau virtuel. Découvrez-en plus sur les [groupes de sécurité réseau](security-overview.md) et la [création d’un groupe de sécurité réseau](tutorial-filter-network-traffic.md).
   - **Table de routage** : Vous pouvez associer une table de routage existante à un sous-réseau pour contrôler le routage du trafic réseau vers d’autres réseaux, ou n’associer aucune table. La table de routage doit exister dans le même abonnement et au même emplacement que le réseau virtuel. Découvrez-en plus sur le [routage Azure](virtual-networks-udr-overview.md) et la [ création d’une table de routage](tutorial-create-route-table-portal.md).
   - **Points de terminaison de service :** Un ou plusieurs points de terminaison de service peuvent être activés pour un sous-réseau. Pour activer un point de terminaison de service pour un service spécifique, sélectionnez le ou les services pour lesquels vous souhaitez activer des points de terminaison de service dans la liste **Services**. L’emplacement d’un point de terminaison est configuré automatiquement. Par défaut, les points de terminaison de service sont configurés dans la région du réseau virtuel. Pour le Stockage Azure, les points de terminaison sont configurés automatiquement dans des [régions appairées Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) pour permettre la prise en charge de scénarios de basculement régionaux.
   - **Délégation de sous-réseau :** Un sous-réseau peut avoir zéro ou plusieurs délégations activées. La délégation de sous-réseau donne des autorisations explicites au service pour créer des ressources propres au service dans le sous-réseau à l’aide d’un identificateur unique pendant le déploiement du service. Pour déléguer à un service, sélectionnez le service souhaité dans la liste **Services**.

       Pour supprimer un point de terminaison de service, désélectionnez le service dont vous souhaitez supprimer le point de terminaison de service. Pour en savoir plus sur les points de terminaison de service et sur les services pour lesquels ils peuvent être activés, consultez [Vue d’ensemble des points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md). Après avoir activé un point de terminaison de service pour un service spécifique, vous devez également activer l’accès réseau du sous-réseau pour une ressource créée avec le service. Par exemple, si vous activez le point de terminaison de service *Microsoft.Storage*, vous devez également activer l’accès réseau à tous les comptes de stockage Azure auxquels vous souhaitez accorder l’accès réseau. Pour plus d’informations sur l’activation de l’accès réseau à des sous-réseaux pour lesquels un point de terminaison de service est activé, consultez la documentation relative au service particulier pour lequel vous avez activé le point de terminaison de service.

     Pour vérifier qu’un point de terminaison de service est activé pour un sous-réseau, affichez les [routages effectifs](diagnose-network-routing-problem.md) sur les interfaces réseau dans le sous-réseau. Quand un point de terminaison est configuré, vous voyez un routage *par défaut* avec les préfixes d’adresse du service, et le type de tronçon suivant (nextHopType) **VirtualNetworkServiceEndpoint**. Pour en savoir plus sur le routage, consultez la [vue d’ensemble du routage](virtual-networks-udr-overview.md).
6. Pour ajouter le sous-réseau au réseau virtuel que vous avez sélectionné, cliquez sur **OK**.

**Commandes**

- Azure CLI : [az network vnet subnet create](/cli/azure/network/vnet/subnet)
- PowerShell : [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Modifier les paramètres de sous-réseau

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau qui contient le sous-réseau dont vous souhaitez modifier les paramètres.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Dans la liste des sous-réseaux, sélectionnez le sous-réseau dont vous souhaitez modifier les paramètres. Vous pouvez modifier les paramètres suivants :

    - **Plage d’adresses :** Si aucune ressource n’a été déployée dans le sous-réseau, vous pouvez changer la plage d’adresses. Si des ressources existent déjà dans le sous-réseau, vous devez soit déplacer les ressources vers un autre sous-réseau, soit les supprimer d’abord du sous-réseau. La procédure à suivre pour supprimer ou déplacer une ressource varie en fonction de celle-ci. Pour savoir comment supprimer ou déplacer des ressources dans des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer ou déplacer. Consultez les contraintes pour la **plage d’adresses** à l’étape 5 de la section [Ajouter un sous-réseau](#add-a-subnet).
    - **Utilisateurs** : Vous pouvez contrôler l’accès au sous-réseau en utilisant des rôles intégrés ou vos propres rôles personnalisés. Pour en savoir plus sur l’attribution de rôles et d’utilisateurs pour l’accès au sous-réseau, consultez [Utiliser l’attribution de rôles pour gérer l’accès à vos ressources Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Groupe de sécurité réseau** et **Table de routage** : Consultez l’étape 5 de la section [Ajouter un sous-réseau](#add-a-subnet).
    - **Points de terminaison de service** : Consultez les points de terminaison de service à l’étape 5 de la section [Ajouter un sous-réseau](#add-a-subnet). Quand vous activez un point de terminaison de service pour un sous-réseau existant, assurez-vous qu’aucune tâche critique n’est en cours d’exécution sur l’une des ressources du sous-réseau. Les points de terminaison de service changent les routages sur chaque interface réseau dans le sous-réseau. Le routage par défaut avec l’adresse de préfixe *0.0.0.0/0* et le type de tronçon suivant *Internet* est remplacé par un nouveau routage avec les préfixes d’adresse du service et le type de tronçon suivant *VirtualNetworkServiceEndpoint*. Le changement de routage peut entraîner l’arrêt des connexions TCP ouvertes. Le point de terminaison de service est activé après que les flux de trafic vers le service sur toutes les interfaces réseau ont été mis à jour avec le nouveau routage. Pour en savoir plus sur le routage, consultez la [vue d’ensemble du routage](virtual-networks-udr-overview.md).
    - **Délégation de sous-réseau :** Consultez les points de terminaison de service à l’étape 5 de la section [Ajouter un sous-réseau](#add-a-subnet). Vous pouvez modifier la délégation de sous-réseau pour qu’elle ait zéro ou plusieurs délégations activées. Si une ressource pour un service est déjà déployée dans le sous-réseau, la délégation de sous-réseau ne peut être ni ajoutée ni supprimée tant que toutes les ressources pour le service ne sont pas supprimées. Pour déléguer à un autre service, sélectionnez le service souhaité dans la liste **Services**.
5. Sélectionnez **Enregistrer**.

**Commandes**

- Azure CLI : [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell : [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Supprimer un sous-réseau

Vous pouvez supprimer un sous-réseau uniquement si aucune ressource ne s’y trouve. S’il existe des ressources dans le sous-réseau, vous devez les supprimer avant de pouvoir supprimer le sous-réseau. La procédure à suivre pour supprimer une ressource varient en fonction de celle-ci. Pour savoir comment supprimer des ressources dans des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau qui contient le sous-réseau à supprimer.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Dans la liste des sous-réseaux, sélectionnez **...** à droite du sous-réseau à supprimer.
5. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

**Commandes**

- Azure CLI : [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell : [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Autorisations

Pour effectuer des tâches sur des sous-réseaux, votre compte doit avoir le rôle de [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou avoir un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) assigné aux actions appropriées répertoriées dans le tableau suivant :

|Action                                                                   |   Nom                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Lire un sous-réseau de réseau virtuel              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Créer ou mettre à jour un sous-réseau de réseau virtuel  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Supprimer un sous-réseau de réseau virtuel            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Joindre un réseau virtuel                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Activer un point de terminaison de service pour un sous-réseau     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obtenir les machines virtuelles d’un sous-réseau       |

## <a name="next-steps"></a>Étapes suivantes

- Créez un réseau virtuel et des sous-réseaux avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou à l’aide des [modèles Azure Resource Manager](template-samples.md)
- Créez et appliquez une [stratégie Azure](policy-samples.md) pour des réseaux virtuels
