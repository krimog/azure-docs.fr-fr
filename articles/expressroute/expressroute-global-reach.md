---
title: Connecter des réseaux locaux à Microsoft Cloud à l’aide de Global Reach - Azure ExpressRoute | Microsoft Docs
description: Cet article présente ExpressRoute Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: e522c7ea306667f675af4bbe7486e25fb7f4ec9f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122800"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute vous permet de connecter vos réseaux locaux à Microsoft Cloud de manière privée et résiliente. Vous pouvez accéder à de nombreux services cloud Microsoft, comme Azure et Office 365, à partir de votre centre de données privé ou de votre réseau d’entreprise. Par exemple, vous pouvez avoir une succursale à San Francisco avec un circuit ExpressRoute dans la Silicon Valley, et une autre à Londres avec un circuit ExpressRoute dans cette même ville. Les deux succursales peuvent disposer d’une connectivité haut débit avec les ressources Azure situées dans les régions USA Ouest et Royaume-Uni Sud, mais elles ne peuvent pas échanger de données directement entre elles. En d’autres termes, 10.0.1.0/24 peut envoyer des données à 10.0.3.0/24 et 10.0.4.0/24, mais PAS à 10.0.2.0/24.

![without][1]

Avec **ExpressRoute Global Reach**, vous pouvez associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux. Dans l’exemple ci-dessus, grâce à l’ajout d’ExpressRoute Global Reach, votre bureau de San Francisco (10.0.1.0/24) peut échanger directement des données avec le bureau de Londres (10.0.2.0/24) par le biais des circuits ExpressRoute existants et du réseau mondial de Microsoft. 

![with][2]

## <a name="use-case"></a>Cas d’utilisation
ExpressRoute Global Reach est conçu pour compléter l’implémentation du réseau étendu (WAN) de votre fournisseur de services et connecter vos succursales à travers le monde. Par exemple, si votre fournisseur de services opère principalement aux États-Unis et a connecté toutes vos branches dans ce pays, mais qu’il n’opère ni au Japon ni à Hong Kong (R.A.S.), ExpressRoute Global Reach vous permet de travailler avec un fournisseur de services local, et Microsoft connecte vos branches situées là-bas à celles figurant aux États-Unis à l’aide d’ExpressRoute et de notre réseau mondial.

![Cas d’usage][3]

## <a name="availability"></a>Disponibilité 
ExpressRoute Global Reach est actuellement pris en charge dans les pays et dans le territoire suivants.

* Australie
* Canada
* France
* Allemagne
* Hong Kong (R.A.S.)
* Irlande
* Japon
* Corée du Sud
* Pays-bas
* Singapour
* Suisse
* Royaume-Uni
* États-Unis

Vos circuits ExpressRoute doivent être créés sur les [sites d’homologation ExpressRoute](expressroute-locations.md) dans le territoire ou les pays indiqués ci-dessus. Pour activer ExpressRoute Global Reach entre [différentes régions](expressroute-locations.md), vous devez disposer de circuits Premium.

## <a name="next-steps"></a>Étapes suivantes
1. [En savoir plus sur ExpressRoute Global Reach](expressroute-faqs.md)
2. [Configuration d’ExpressRoute Global Reach](expressroute-howto-set-global-reach.md)
3. [Lier un circuit ExpressRoute à un réseau virtuel Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "schéma sans portée globale"
[2]: ./media/expressroute-global-reach/2.png "schéma avec portée globale"
[3]: ./media/expressroute-global-reach/3.png "cas d’usage de global reach"
