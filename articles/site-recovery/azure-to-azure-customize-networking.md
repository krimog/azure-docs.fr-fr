---
title: Personnaliser les configurations de mise en réseau pour une machine virtuelle de basculement | Microsoft Docs
description: Fournit une vue d’ensemble de la personnalisation des configurations de mise en réseau pour une machine virtuelle de basculement dans le cadre de la réplication des machines virtuelles Azure avec Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 8038f7c909cfeaf15039afa7335dd6b0460a2622
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293469"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personnaliser les configurations réseau de la machine virtuelle Azure cible

Cet article fournit des conseils sur la façon de personnaliser les configurations de mise en réseau sur la machine virtuelle Azure quand vous répliquez et récupérez des machines virtuelles Azure d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Avant de commencer

Découvrez comment Site Recovery permet la récupération d’urgence pour [ce scénario](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Ressources réseau prises en charge

Vous pouvez fournir la configuration des ressources clés suivantes pour la machine virtuelle de basculement lors de la réplication des machines virtuelles Azure :

- [Équilibreur de charge interne](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Adresse IP publique](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) pour le sous-réseau et pour la carte réseau

 > [!IMPORTANT]
  > Pour le moment, ces paramètres sont pris en charge uniquement dans l’opération de basculement, et pas pour le test de basculement.

## <a name="prerequisites"></a>Prérequis

- Veillez à planifier à l’avance vos configurations côté récupération.
- Créez à l’avance les ressources de mise en réseau. Fournissez-les en entrée afin que le service Azure Site Recovery puisse respecter ces paramètres et s’assurer que la machine virtuelle de basculement adhérera à ces paramètres.

## <a name="customize-failover-networking-configurations"></a>Personnaliser les configurations de mise en réseau du basculement

1. Accédez à **Éléments répliqués**. 
2. Sélectionnez la machine virtuelle Azure de votre choix.
3. Sélectionnez **Calcul et réseau**, puis **Modifier**. Vous remarquerez que les paramètres de configuration de la carte réseau incluent les ressources correspondantes à la source. 

     ![Personnaliser les configurations de mise en réseau du basculement](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Sélectionnez **Modifier** à côté de la carte réseau que vous souhaitez configurer. Dans le panneau suivant qui s’affiche, sélectionnez les ressources précréées correspondantes dans la cible.

    ![Modifiez la configuration de la carte réseau](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Sélectionnez **OK**.

Site Recovery respecte à présent ces paramètres et s’assure que, lors du basculement, la machine virtuelle est connectée à la ressource sélectionnée via la carte réseau correspondante.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="unable-to-view-or-select-a-resource"></a>Impossible d’afficher ou de sélectionner une ressource

Si vous ne pouvez pas sélectionner ou afficher une ressource de mise en réseau, effectuez les vérifications suivantes et passez en revue les conditions suivantes :

- Le champ cible d’une ressource réseau est activé uniquement si la machine virtuelle source avait une entrée correspondante. Cela est basé sur le principe selon lequel, pour un scénario de récupération d’urgence, vous souhaiterez la version exacte ou une version réduite de votre source.
- Pour chaque ressource réseau, certains filtres sont appliqués dans la liste déroulante pour garantir que la machine virtuelle de basculement pourra s’attacher à la ressource sélectionnée et que la fiabilité du basculement sera maintenue. Ces filtres sont basés sur les conditions de mise en réseau qui auraient été vérifiées lors de la configuration de la machine virtuelle source.

Validations de l’équilibreur de charge interne :

- L’abonnement et la région de l’équilibrer la charge et de la machine virtuelle cible doivent être identiques.
- Le réseau virtuel associé à l’équilibreur de charge interne et celui de la machine virtuelle cible doivent être identiques.
- La référence SKU d’adresse IP publique de la machine virtuelle cible et la référence SKU de l’équilibreur de charge interne doivent être identiques.
- Si la machine virtuelle cible est configurée de manière à être placée dans une zone de disponibilité, vérifiez si l’équilibreur de charge est redondant interzone ou fait partie d’une zone de disponibilité quelconque. (Les équilibreurs de charge de référence SKU de base ne prennent pas en charge les zones et n’apparaîtront pas dans la liste déroulante dans ce cas.)
- Assurez-vous que l’équilibreur de charge interne possède un pool principal et une configuration frontale créés au préalable.


Adresse IP publique :
    
- L’abonnement et la région de l’adresse IP publique et de la machine virtuelle cible doivent être identiques.
- La référence SKU d’adresse IP publique de la machine virtuelle cible et la référence SKU de l’équilibreur de charge interne doivent être identiques.

Groupe de sécurité réseau :
- L’abonnement et la région du groupe de sécurité réseau et de la machine virtuelle cible doivent être identiques.


> [!WARNING]
> Si la machine virtuelle cible est associée à un groupe à haute disponibilité, vous devez associer l’adresse IP publique et l’équilibreur de charge interne de la même référence SKU avec ceux de l’adresse IP publique et de l’équilibreur de charge interne de l’autre machine virtuelle dans le groupe à haute disponibilité. Dans le cas contraire, le basculement peut échouer.
