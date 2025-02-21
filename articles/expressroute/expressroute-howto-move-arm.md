---
title: 'Migrer les circuits d’un déploiement classique vers Resource Manager - ExpressRoute : PowerShell : Azure | Microsoft Docs'
description: Cette page décrit comment déplacer un circuit classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 34dd7ed4c6352ed90793288f918d78b7fb07af80
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748246"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Déplacer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell

Pour utiliser un circuit ExpressRoute pour les modèles de déploiement classique et Resource Manager, vous devez déplacer ce circuit vers le modèle de déploiement Resource Manager. Les sections suivantes vous aident à déplacer votre circuit à l’aide de PowerShell.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Vérifiez que vous avez installé les modules Azure PowerShell classique et 	Az localement sur votre ordinateur. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview).
* Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md). Vous devez avoir bien compris les limites et les limitations.
* Vérifiez que le circuit est totalement opérationnel dans le modèle de déploiement classique.
* Assurez-vous que vous disposez d’un groupe de ressources créé dans le modèle de déploiement Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Déplacer un circuit ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Étape 1 : Collecter des informations sur le circuit à partir du modèle de déploiement classique

Connectez-vous à l’environnement classique Azure et collectez la clé de service.

1. Connectez-vous à votre compte Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Sélectionnez l’abonnement Azure approprié.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importez les modules PowerShell pour Azure et ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Utilisez l’applet de commande ci-dessous pour obtenir les clés de service pour tous les circuits imprimés ExpressRoute. Après avoir récupéré les clés, copiez la **clé de service** du circuit que vous souhaitez déplacer vers le modèle de déploiement Resource Manager.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Étape 2 : Se connecter et créer un groupe de ressources

Connectez-vous à l’environnement Resource Manager et créez un groupe de ressources.

1. Connectez-vous à votre environnement Azure Resource Manager.

   ```powershell
   Connect-AzAccount
   ```

2. Sélectionnez l’abonnement Azure approprié.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Modifiez l’extrait de code ci-dessous pour créer un groupe de ressources si vous n’en avez pas déjà un.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Étape 3 : Transférer le circuit ExpressRoute vers le modèle de déploiement Resource Manager

Vous êtes maintenant prêt à déplacer votre circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager. Avant de continuer, passez en revue les informations fournies sous [Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md).

Pour déplacer votre circuit, modifiez et exécutez l’extrait de code suivant :

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

En mode classique, un circuit ExpressRoute n’est pas par essence lié à une région. Toutefois, dans Resource Manager, chaque ressource doit être mappée à une région Azure. La région spécifiée dans l’applet de commande Move-AzExpressRouteCircuit peut techniquement correspondre à n’importe quelle région. Pour des besoins d’organisation, vous pouvez souhaiter choisir une région qui représente votre emplacement d’appairage (peering).

> [!NOTE]
> Une fois le déplacement terminé, le nouveau nom répertorié dans l’applet de commande précédente sera utilisé pour traiter la ressource. Le circuit sera essentiellement renommé.
> 

## <a name="modify-circuit-access"></a>Modifier l’accès d’un circuit

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Pour activer l’accès du circuit ExpressRoute pour les deux modèles de déploiement

Après avoir déplacé votre circuit ExpressRoute classique vers le modèle de déploiement Resource Manager, vous pouvez activer l’accès aux deux modèles de déploiement. Exécutez les applets de commande suivantes pour activer l’accès aux deux modèles de déploiement :

1. Obtenez les informations sur le circuit.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Définissez « Autoriser les opérations classiques » sur TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Mettez à jour le circuit. Une fois cette opération terminée avec succès, vous serez en mesure d’afficher le circuit dans le modèle de déploiement classique.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Exécutez l’applet de commande suivante pour obtenir les informations concernant le circuit ExpressRoute. Vous devez être en mesure de voir la clé de service répertoriée.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Vous pouvez maintenant gérer les liens au circuit ExpressRoute à l’aide des commandes du modèle de déploiement classique pour les réseaux virtuels classiques, et des commandes Resource Manager pour les réseaux virtuels Resource Manager. Les articles suivants vous aident à gérer les liens vers le circuit ExpressRoute :

    * [Liaison de réseaux virtuels à des circuits ExpressRoute dans le modèle de déploiement Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Liaison de réseaux virtuels à des circuits ExpressRoute dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Pour désactiver l’accès du circuit ExpressRoute au modèle de déploiement classique

Exécutez les applets de commande suivantes pour désactiver l’accès au modèle de déploiement classique.

1. Obtenez les informations concernant le circuit ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Définissez « Autoriser les opérations classiques » sur FALSE.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Mettez à jour le circuit. Une fois cette opération terminée avec succès, vous ne serez pas en mesure d’afficher le circuit dans le modèle de déploiement classique.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Étapes suivantes

* [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
* [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
