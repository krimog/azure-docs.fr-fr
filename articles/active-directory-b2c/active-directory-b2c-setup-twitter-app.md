---
title: Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Twitter dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cb5c5813bff8c0df5b119ea1cadf4f2e7c1696a
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811397"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C

## <a name="create-an-application"></a>Création d'une application

Pour utiliser Twitter comme fournisseur d’identité dans Azure AD B2C, vous devez créer une application Twitter. Si vous ne disposez pas encore d’un compte Twitter, vous pouvez en créer un à l’adresse [https://twitter.com/signup](https://twitter.com/signup).

1. Connectez-vous au site web [Développeurs Twitter](https://developer.twitter.com/en/apps) avec les informations d’identification de votre compte Twitter.
1. Sélectionnez **Créer une application**.
1. Entrez un **Nom de l’application** et une **Description de l’application**.
1. Dans **URL du site web**, entrez `https://your-tenant.b2clogin.com`. Remplacez `your-tenant` par le nom de votre locataire. Par exemple : https://contosob2c.b2clogin.com.
1. Dans le champ **Callback URL** (URL de rappel), entrez `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Remplacez `your-tenant` par le nom de votre locataire et `your-user-flow-Id` par l’identificateur de votre flux d’utilisateur. Par exemple : `b2c_1A_signup_signin_twitter`. Quand vous entrez le nom de votre locataire et l’ID de flux utilisateur, vous ne devez utiliser que des minuscules, même s’ils sont définis avec des majuscules dans Azure AD B2C.
1. Au bas de la page, lisez et acceptez les termes du contrat, puis sélectionnez **Créer**.
1. Dans la page **Détails de l’application**, sélectionnez **Modifier > Modifier les détails**, cochez la case **Activer la connexion avec Twitter**, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Clés et jetons** et prenez note des valeurs **Clé API de consommateur** et **Clé secrète API de consommateur** à utiliser ultérieurement.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurer Twitter en tant que fournisseur d’identité dans votre locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Twitter**.
1. Saisissez un **Nom**. Par exemple, *Twitter*.
1. Pour **ID client**, entrez la clé d’API Consommateur de l’application Twitter que vous avez créée précédemment.
1. Pour **Clé secrète client**, entrez la clé secrète d’API Consommateur que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.
