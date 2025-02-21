---
title: Que sont les dépendances de service dans l’accès conditionnel Azure Active Directory ? | Microsoft Docs
description: Découvrez comment les conditions sont utilisées dans l’accès conditionnel Azure Active Directory pour déclencher une stratégie.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c7f2abda282d0219dd8787a9f6a2b6c1cda15df
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257910"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Que sont les dépendances de service dans l’accès conditionnel Azure Active Directory ? 

Avec les stratégies d’accès conditionnel, vous pouvez spécifier des conditions d’accès aux sites web et aux services. Par exemple, vous pouvez exiger une authentification multifacteur (MFA) ou des [appareils gérés](require-managed-devices.md) dans vos conditions d’accès. 

Lorsque vous accédez directement à un site ou un service, l’impact d’une stratégie associée est généralement facile à évaluer. Par exemple, si vous avez configuré une stratégie qui exige une authentification MFA pour SharePoint Online, chaque connexion au portail web SharePoint nécessitera une authentification multifacteur. Toutefois, il n’est pas toujours simple d’évaluer l’impact d’une stratégie, car certaines applications cloud sont dépendantes d’autres applications cloud. Par exemple, Microsoft Teams peut accorder un accès aux ressources dans SharePoint Online. Par conséquent, lorsque vous accédez à Microsoft Teams dans notre scénario actuel, vous êtes également soumis à la stratégie MFA de SharePoint.   

## <a name="policy-enforcement"></a>Application de stratégies 

Si vous avez configuré une dépendance de service, la stratégie peut être appliquée via une liaison anticipée ou tardive. 

- Une **application de stratégie à liaison anticipée** signifie qu’un utilisateur doit respecter la stratégie du service dépendant avant d’accéder à l’application appelante. Par exemple, un utilisateur doit satisfaire la stratégie SharePoint avant de se connecter à MS Teams. 
- Une **application de stratégie à liaison tardive** se produit après la connexion de l’utilisateur à l’application appelante. L’application de la stratégie est différée et survient lorsque l’application appelante demande un jeton pour le service en aval. L’un des exemples montre MS Teams qui accède à Planner et Office.com qui accède à SharePoint. 

Le diagramme ci-dessous illustre les dépendances du service MS Teams. Les flèches pleines représentent l’application à liaison anticipée et la flèche en pointillé pour Planner indique l’application à liaison tardive. 

![Dépendances du service MS Teams](./media/service-dependencies/01.png)

Une bonne pratique consiste à définir des stratégies communes sur l’ensemble des applications et services associés lorsque c’est possible. Une posture de sécurité cohérente vous offre la meilleure expérience utilisateur. Par exemple, en définissant une stratégie commune sur Exchange Online, SharePoint Online, Microsoft Teams et Skype Entreprise, vous pouvez considérablement réduire le nombre d’invites inattendues pouvant être générées par les différentes stratégies appliquées aux services en aval. 

Le tableau ci-dessous répertorie les dépendances de service supplémentaires et où les applications clientes doivent satisfaire les exigences  

| Applications clientes         | Service en aval                          | Application |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gestion Microsoft Azure (portail et API) | Liaison anticipée |
| Microsoft Classroom | Microsoft Exchange                                    | Liaison anticipée |
|                     | SharePoint                                  | Liaison anticipée |
| Microsoft Teams     | Microsoft Exchange                                    | Liaison anticipée |
|                     | MS Planner                                  | Liaison tardive  |
|                     | SharePoint                                  | Liaison anticipée |
|                     | Skype Entreprise Online                   | Liaison anticipée |
| Portail Office       | Microsoft Exchange                                    | Liaison tardive  |
|                     | SharePoint                                  | Liaison tardive  |
| Outlook Groups      | Microsoft Exchange                                    | Liaison anticipée |
|                     | SharePoint                                  | Liaison anticipée |
| PowerApps           | Gestion Microsoft Azure (portail et API) | Liaison anticipée |
|                     | Microsoft Azure Active Directory              | Liaison anticipée |
| Projet             | Dynamics CRM                                | Liaison anticipée |
| Skype Entreprise  | Microsoft Exchange                                    | Liaison anticipée |
| Visual Studio       | Gestion Microsoft Azure (portail et API) | Liaison anticipée |
| Microsoft Forms     | Microsoft Exchange                                    | Liaison anticipée |
|                     | SharePoint                                  | Liaison anticipée |
| Microsoft To-Do     | Microsoft Exchange                                    | Liaison anticipée |

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à implémenter l’accès conditionnel dans votre environnement, consultez [Planifier votre déploiement d’accès conditionnel dans Azure Active Directory](plan-conditional-access.md).
