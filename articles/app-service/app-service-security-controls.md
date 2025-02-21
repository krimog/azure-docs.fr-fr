---
title: Contrôles de sécurité pour Azure App Service
description: Check-list des contrôles de sécurité pour l’évaluation d’Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: app-service
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 65b1c7f4649ff90c1305d4007428ea02bb003369
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886309"
---
# <a name="security-controls-for-azure-app-service"></a>Contrôles de sécurité pour Azure App Service

Cet article décrit les contrôles de sécurité intégrés à Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | Actuellement disponible en préversion pour App Service.| [Restrictions d’accès dans Azure App Service](app-service-ip-restrictions.md)
| Prise en charge de l’injection de réseau virtuel| OUI | Les environnements de services applicatifs sont des implémentations privées d’App Service dédiées à un client unique injecté dans le réseau virtuel d’un client. | [Introduction aux environnements App Service](environment/intro.md)
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Pour la variante publique multi-locataires d’App Service, les clients peuvent configurer les listes de contrôle d’accès (Restrictions d’IP) du réseau pour verrouiller le trafic entrant autorisé.  Les environnements App Service sont déployés directement dans des réseaux virtuels et peuvent donc être sécurisés avec les groupes de sécurité réseau. | [Restrictions d’accès dans Azure App Service](app-service-ip-restrictions.md)
| Prise en charge du tunneling forcé| OUI | Les environnements App Service peuvent être déployés sur le réseau virtuel d’un client où le tunneling forcé est configuré. | [Configurer votre environnement App Service avec le tunneling forcé](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | App Service s’intègre avec Application Insights pour les langages qui prennent en charge Application Insights (.NET Framework complet, .NET Core, Java et Node.JS).  Pour en savoir plus, veuillez consulter [Analyser les performances d’Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service envoie également les mesures de l’application dans Azure Monitor. | [Surveiller les applications dans Azure App Service](web-sites-monitor.md)
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les opérations de gestion effectuées sur les objets App Service se font via [Azure Resource Manager](../azure-resource-manager/index.yml). Les journaux d’historique de ces opérations sont disponibles à la fois dans le portail et par le biais de l’interface de ligne de commande (CLI). | [Opérations du fournisseur de ressources Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az monitor activity-log](/cli/azure/monitor/activity-log)
| Journalisation et audit du plan de données | Non | Le plan de données pour App Service est un partage de fichiers distant contenant le contenu du site web déployé par un client.  Il n’y a pas d’audit du partage de fichiers distant. |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes |  Documentation
|---|---|--|
| Authentication| OUI | Les clients peuvent générer des applications sur App Service qui s’intègrent automatiquement à [Azure Active Directory (Azure AD)](../active-directory/index.yml), ainsi qu’à d’autres fournisseurs d’identité compatibles avec OAuth. Pour un accès en gestion aux ressources App Service, tous les accès sont contrôlés par une combinaison de principaux authentifiés par Azure AD et de rôles RBAC d’Azure Resource Manager. | [Authentification et autorisation dans Azure App Service](overview-authentication-authorization.md)
| Authorization| OUI | Pour la gestion de l’accès aux ressources App Service, tous les accès sont contrôlés par une combinaison de principaux authentifiés par Azure AD et de rôles RBAC d’Azure Resource Manager.  | [Authentification et autorisation dans Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Le contenu des fichiers du site web est stocké dans le Stockage Azure, qui chiffre automatiquement le contenu au repos. <br><br>Les secrets fournis par le client sont chiffrées au repos. Les secrets sont chiffrés au repos lorsqu’ils sont stockés dans les bases de données de configuration d’App Service.<br><br>Les disques joints localement peuvent optionnellement être utilisés comme stockage temporaire par les sites web (D:\local et %TMP%). Les disques joints localement ne sont pas chiffrés au repos. | [Chiffrement du stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md)
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | OUI | Les clients peuvent choisir de stocker les secrets d’application dans Key Vault et de les récupérer au moment de l’exécution. | [Utiliser des références Key Vault pour App Service et Azure Functions (préversion)](app-service-key-vault-references.md)
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Les clients peuvent configurer les sites web pour exiger et utiliser le HTTPS pour le trafic entrant.  | [Guide pratique pour limiter une instance d’Azure App Service au HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (billet de blog)
| Appels d’API chiffrés| OUI | Les appels de gestion pour configurer App Service se font via des appels [Azure Resource Manager](../azure-resource-manager/index.yml) par le biais du protocole HTTPS. |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration d’App Service peut être exporté sous forme de modèle Azure Resource Manager et géré avec le temps. Pour les opérations d’exécution, les clients peuvent gérer plusieurs versions en direct d’une application à l’aide des emplacements de déploiement App Service. | 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [contrôles de sécurité intégrés sur les services Azure](../security/fundamentals/security-controls.md).