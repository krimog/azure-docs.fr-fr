---
title: Personnaliser les règles de pare-feu d’applications web dans Azure Application Gateway - Portail Azure
description: Cet article fournit des informations sur la personnalisation des règles de pare-feu d’applications web dans Application Gateway avec le Portail Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/24/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: ab2722ed4a4aa4fe95be15cd536bfd5d959f9139
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511542"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personnaliser les règles du pare-feu d’applications web à l’aide du Portail Azure

Le pare-feu d’applications web (WAF) Azure Application Gateway fournit une protection pour les applications web. Ces protections sont fournies par le jeu de règles (Core Rule Set, CRS) de l’Open Web Application Security Project (OWASP). Certaines règles peuvent entraîner des faux positifs et bloquer le trafic réel. Par conséquent, Application Gateway permet de personnaliser des règles et des groupes de règles. Pour plus d’informations sur les règles et groupes de règles spécifiques, consultez la [Liste de règles et groupes de règles CRS de pare-feu d’applications web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Si votre passerelle Application Gateway n’utilise pas la couche WAF, l’option de mise à niveau de la passerelle Application Gateway vers la couche WAF s’affiche dans le volet de droite. 

![Activer WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Afficher les règles et groupes de règles

**Pour afficher les règles et groupes de règles**
1. Accédez à la passerelle Application Gateway, puis sélectionnez **Pare-feu d’applications web**.  
2. Sélectionnez votre **stratégie WAF**.
2. Sélectionnez **Règles managées**.

   Un tableau s’affiche sur la page de tous les groupes de règles fournis avec l’ensemble de règles choisi. Toutes les cases à cocher de la règle sont sélectionnées.

## <a name="disable-rule-groups-and-rules"></a>Désactiver les règles et les groupes de règles

> [!IMPORTANT]
> Soyez prudent lors de la désactivation des groupes de règles ou des règles. Cela peut aggraver les risques pour la sécurité.

Lorsque vous désactivez des règles, vous pouvez désactiver un groupe de règles entier, ou des règles spécifiques sous un ou plusieurs groupes de règles. 

**Pour désactiver des groupes de règles ou des règles spécifiques**

   1. Recherchez les règles ou les groupes de règles que vous voulez désactiver.
   2. Cochez les cases correspondant aux règles que vous voulez désactiver. 
   3. Sélectionnez l’action en haut de la page (activer/désactiver) pour les règles sélectionnées.
   2. Sélectionnez **Enregistrer**. 

![Enregistrer les modifications][3]

## <a name="mandatory-rules"></a>Règles obligatoires

La liste suivante contient les conditions qui provoquent le blocage de la requête par le pare-feu d’applications web (WAF) en mode Prévention. En mode Détection, elles sont enregistrées en tant qu’exceptions.

Elles ne peuvent pas être configurées ni désactivées :

* L’échec d’analyse du corps de la requête entraîne le blocage de cette dernière, sauf si l’inspection du corps est désactivée (XML, JSON, données de formulaire)
* La longueur des données du corps de la requête (sans fichiers) est supérieure à la limite configurée
* Le corps de la requête (avec fichiers) est supérieur à la limite
* Une erreur interne s’est produite dans le moteur WAF

Propre à CRS 3.x :

* Le score des anomalies entrantes a dépassé le seuil

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré vos règles désactivées, vous pouvez apprendre à afficher vos journaux d’activité WAF. Pour plus d’informations, consultez [Diagnostics Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
