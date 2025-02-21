---
title: Démarrage rapide en matière de stratégie d’expiration pour les groupes Office 365 – Azure Active Directory | Microsoft Docs
description: Expiration des groupes Office 365 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01cedadc115496fcf00df986b4ad4b9c5aab5139
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606181"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Démarrage rapide : Configurer l’expiration des groupes Office 365 dans Azure Active Directory

Dans le cadre de ce démarrage rapide, vous allez définir la stratégie d’expiration de vos groupes Office 365. Lorsque les utilisateurs peuvent configurer leurs propres groupes, les groupes inutilisés risquent de se multiplier. L’une des façons de gérer les groupes inutilisés consiste à configurer l’expiration de ces groupes afin de minimiser la tâche de suppression manuelle des groupes.

La stratégie d’expiration est simple :

* Les propriétaires de groupe sont invités à renouveler un groupe sur le point d’expirer.
* Un groupe non renouvelé est supprimé.
* Un groupe Office 365 supprimé peut être restauré dans les 30 jours par son propriétaire ou par un administrateur Azure AD.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisite"></a>Configuration requise

 Au sein de l'organisation, le rôle aux privilèges les moins élevés requis pour configurer l'expiration du groupe est Administrateur d'utilisateurs.

## <a name="turn-on-user-creation-for-groups"></a>Activer la création d’utilisateurs pour les groupes

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l'aide d'un compte Administrateur d'utilisateurs.

2. Sélectionnez **Groupes**, puis sélectionnez **Général**.
  
   ![Page des paramètres de groupe en libre-service](./media/groups-quickstart-expiration/self-service-settings.png)

3. Définissez l’option **Les utilisateurs peuvent créer des groupes Office 365** sur **Oui**.

4. Lorsque vous avez terminé, sélectionnez **Enregistrer** pour enregistrer les paramètres des groupes.

## <a name="set-group-expiration"></a>Définir l’expiration d’un groupe

1. Connectez-vous au [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory** > **Groupes** > **Expiration** pour ouvrir les paramètres d’expiration.
  
   ![Page des paramètres d’expiration pour un groupe](./media/groups-quickstart-expiration/expiration-settings.png)

2. Définissez l’intervalle d’expiration. Sélectionnez une valeur prédéfinie ou entrez une valeur personnalisée supérieure à 31 jours. 

3. Fournissez une adresse e-mail à laquelle les notifications d’expiration doivent être envoyées si un groupe est dépourvu de propriétaire.

4. Pour ce démarrage rapide, définissez l’option **Activer l’expiration de ces groupes Office 365** sur **Tous**.

5. Lorsque vous avez terminé, sélectionnez **Enregistrer** pour enregistrer les paramètres d’expiration.

Et voilà ! Dans le cadre de ce démarrage rapide, vous avez configuré la stratégie d’expiration des groupes Office 365 sélectionnés.

## <a name="clean-up-resources"></a>Supprimer des ressources

### <a name="to-remove-the-expiration-policy"></a>Pour supprimer la stratégie d’expiration

1. Vérifiez que vous êtes connecté au [Portail Azure](https://portal.azure.com) à l’aide du compte d’administrateur général de votre locataire.
2. Sélectionnez **Azure Active Directory** > **Groupes** > **Expiration**.
3. Définissez l’option **Activer l’expiration de ces groupes Office 365** sur **Aucun**.

### <a name="to-turn-off-user-creation-for-groups"></a>Pour désactiver la création d’utilisateurs pour les groupes

1. Sélectionnez **Azure Active Directory** > **Groupes** > **Général**. 
2. Définissez l’option **Les utilisateurs peuvent créer des groupes Office 365 dans les portails Azure** sur **Non**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur l'expiration, ainsi que sur les instructions PowerShell et les contraintes techniques, consultez l'article suivant :

> [!div class="nextstepaction"]
> [Stratégie d'expiration - PowerShell](groups-lifecycle.md)
