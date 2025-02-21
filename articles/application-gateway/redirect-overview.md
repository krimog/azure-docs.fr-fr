---
title: Vue d’ensemble de la redirection pour Azure Application Gateway
description: En savoir plus sur la capacité de redirection dans Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715796"
---
# <a name="application-gateway-redirect-overview"></a>Vue d’ensemble de la redirection Application Gateway

Vous pouvez utiliser Application Gateway pour rediriger le trafic.  La passerelle comporte un mécanisme de redirection générique, qui permet la redirection du trafic reçu au niveau d’un écouteur vers un autre écouteur ou vers un site externe. Cela simplifie la configuration de l’application, optimise l’utilisation des ressources et prend en charge de nouveaux scénarios de redirection, notamment la redirection globale et basée sur le chemin d’accès.

Un scénario courant de redirection pour de nombreuses applications web consiste à prendre en charge la redirection automatique de HTTP vers HTTPS pour vous assurer que toutes les communications entre l’application et ses utilisateurs auront lieu via un chemin d’accès chiffré. Dans le passé, les utilisateurs utilisaient des techniques telles que la création d’un pool backend dédié dont le seul objectif consistait à rediriger les demandes qu’il recevait sur HTTP vers HTTPS. Avec la prise en charge de la redirection dans Application Gateway, vous pouvez le faire en ajoutant simplement une nouvelle configuration de redirection à une règle d’acheminement et en spécifiant un autre écouteur avec le protocole HTTPS en tant qu’écouteur cible.

Les types suivants de redirection sont pris en charge :

- 301 Redirection permanente
- 302 Trouvé.
- 303 Voir autres
- 307 Redirection temporaire

La prise en charge de la redirection Application Gateway offre les fonctionnalités suivantes :

-  **Redirection globale**

   Effectue une redirection d’un écouteur à un autre sur la passerelle. Cela permet la redirection HTTP vers HTTPS sur un site.
- **Redirection par chemin d’accès**

   Ce type de redirection permet la redirection HTTP vers HTTPS uniquement sur une zone de site spécifique, par exemple une zone de panier d’achat indiquée par /cart/*.
- **Redirection vers un site externe**

![redirection](./media/redirect-overview/redirect.png)

Dans le cadre de cette modification, les clients doivent créer un nouvel objet de configuration de redirection, qui spécifie l’écouteur cible ou le site externe vers lequel une redirection est souhaitée. L’élément de configuration prend également en charge des options qui permettent d’ajouter le chemin d’URI et la chaîne de requête à l’URL redirigée. Vous pouvez aussi choisir le type de redirection. Une fois créée, cette configuration de redirection est attachée à l’écouteur source au moyen d’une nouvelle règle. Lorsque vous utilisez une règle de base, la configuration de redirection est associée à un écouteur source et correspond à une redirection globale. Dans le cadre d’une règle par chemin d’accès, la configuration de redirection est définie sur le mappage de chemins d’accès d’URL. Elle s’applique donc exclusivement à la zone de chemin d’accès d’un site.

### <a name="next-steps"></a>Étapes suivantes

[Configurer la redirection d’URL sur une passerelle d’application](tutorial-url-redirect-powershell.md)
