---
title: Présentation des balises FQDN pour le pare-feu Azure
description: En savoir plus sur les balises FQDN dans le pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/23/2019
ms.author: victorh
ms.openlocfilehash: d78f67b96776fa3fb1d13fbf7b385e41ca3aa59a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665559"
---
# <a name="fqdn-tags-overview"></a>Présentation des balises FQDN

Une balise FQDN représente un groupe de noms de domaine complets (FQDN) associés à des services Microsoft bien connus. Vous pouvez utiliser une balise FQDN dans les règles d’application pour autoriser le trafic sortant requis via votre pare-feu.

Par exemple, pour autoriser manuellement le trafic Windows Update via votre pare-feu, vous devez créer plusieurs règles d’application conformément à la documentation Microsoft. À l’aide des balises FQDN, vous pouvez créer une règle d’application et inclure la balise **Windows Update**. Le trafic vers les points de terminaison Microsoft Windows Update peut désormais transiter via votre pare-feu.

Vous ne pouvez pas créer vos propres balises FQDN, ni spécifier quels noms de domaine complets sont inclus dans une balise. Microsoft gère les noms de domaine complets inclus dans la balise FQDN et met à jour la balise en fonction de l’évolution des noms de domaine complets. 

<!--- screenshot of application rule with a FQDN tag.-->

Le tableau suivant présente les balises FQDN que vous pouvez actuellement utiliser. Microsoft gère ces balises, et vous pouvez vous attendre à ce que de nouvelles balises soient ajoutées régulièrement.

## <a name="current-fqdn-tags"></a>Balises FQDN actuelles

|Balise FQDN  |Description  |
|---------|---------|
|Windows Update     |Autoriser un accès sortant à Microsoft Update, comme décrit dans [Comment configurer un pare-feu pour les mises à jour logicielles](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostics Windows|Autoriser l’accès sortant à tous les [points de terminaison Windows Diagnostics](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Autoriser l’accès sortant à [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Autoriser l’accès sortant au trafic de plateforme ASE. Cette balise ne concerne pas les points de terminaison SQL et de stockage propres au client créés par ASE. Ceux-ci doivent être activés via les [points de terminaison de service](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou ajoutés manuellement.<br><br>Pour plus d’informations sur l’intégration du Pare-feu Azure avec l’ASE, consultez [Verrouillage d’un App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Sauvegarde Azure|Autoriser l’accès sortant aux services de sauvegarde Azure.|
|Azure HDInsight|Autoriser l’accès sortant au trafic de plateforme HDInsight. Cette balise ne concerne pas le trafic SQL et de stockage propre au client provenant de HDInsight. Activer à l’aide de [points de terminaison de service](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou ajouter manuellement.|

> [!NOTE]
> Lorsque vous sélectionnez une balise FQDN dans une règle d’application, le champ protocol:port doit être défini sur **https**.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer un pare-feu Azure, consultez le [didacticiel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)
