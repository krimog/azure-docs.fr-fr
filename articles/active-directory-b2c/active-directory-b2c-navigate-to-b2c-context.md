---
title: Basculement vers un locataire B2C dans Azure Active Directory B2C | Microsoft Docs
description: Comment basculer dans le contexte de votre locataire Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cbf0e928ae05e723902d41a340aebf4f5781fde5
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654124"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Basculement vers votre locataire Azure AD B2C

Pour configurer Azure AD B2C, vous devez vous trouver dans le contexte de votre locataire Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Se connecter au locataire Azure AD B2C

Pour accéder à votre locataire Azure AD B2C, vous devez être connecté au portail Azure en tant qu’administrateur général du locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Basculez les locataires en cliquant sur votre adresse de messagerie ou sur l’image dans le coin supérieur droit.
1. Dans la liste `Directory` qui s’affiche, sélectionnez le locataire Azure AD B2C que vous souhaitez gérer.

Le portail Azure est actualisé.  Vous êtes à présent connecté au portail Azure dans le contexte de votre client Azure AD B2C.

## <a name="navigate-to-the-b2c-features-pane"></a>Accéder au panneau de fonctionnalités B2C

1. Cliquez sur **Parcourir** dans le volet de navigation de gauche.
1. Cliquez sur **Tous les services**, puis recherchez `Azure AD B2C` dans le volet de navigation de gauche.  (Pour l’épingler à votre Tableau d’accueil sur la gauche, cliquez sur l’étoile située à gauche d’Azure AD B2C.)
1. Cliquez sur **Azure AD B2C** pour accéder au panneau de fonctionnalités B2C.

    ![Capture d’écran du volet d’accès aux fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Vous devez être administrateur général du client B2C pour accéder au volet des fonctionnalités B2C. L’administrateur général ou l’utilisateur de tout autre client ne pourra pas y accéder.  Vous pouvez basculer vers votre client B2C en utilisant le sélecteur de client dans le coin supérieur droit du portail Azure.
