---
title: Authentification répartie dans Android | Azure
description: Vue d’ensemble de l’authentification répartie et de l’autorisation pour Android dans la plateforme d’identité Microsoft
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb1ed81c03e7c5ba30b813897dac5796c550ed23
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678853"
---
# <a name="brokered-auth-in-android"></a>Authentification répartie dans Android

## <a name="introduction"></a>Introduction

Vous devez utiliser l’un des répartiteurs d’authentification de Microsoft pour participer à l’authentification unique (SSO) des appareils et répondre aux stratégies d’accès conditionnel de l’organisation. L’intégration d'un répartiteur vous offre les avantages suivants :

- Authentification unique des appareils
- Accès conditionnel pour :
  - Intune App Protection
  - Inscription des appareils (Workplace Join)
  - Gestion des appareils mobiles
- Gestion des comptes d'appareil
  -  via Android AccountManager et les paramètres de compte
  - « Compte professionnel » - type de compte personnalisé

Sur Android, le répartiteur d'authentification Microsoft est un composant fourni avec l'[application Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) et le [Portail d’entreprise Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Seule une application hébergeant le répartiteur est active en tant que répartiteur à la fois. L’application active en tant que répartiteur est déterminée par l’ordre d’installation sur l’appareil. La première application à être installée ou la dernière application présente sur l'appareil fait office de répartiteur actif.

Le diagramme suivant illustre la relation entre votre application, la bibliothèque d'authentification Microsoft (MSAL) et les répartiteurs d'authentification de Microsoft.

![Diagramme de déploiement des répartiteurs](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installation d’applications hébergeant un répartiteur

Les applications hébergeant un répartiteur peuvent à tout moment être installées par le propriétaire de l’appareil à partir de son magasin d’applications (généralement Google Play Store). Toutefois, certaines API (ressources) sont protégées par des stratégies d’accès conditionnel qui impliquent que les appareils soient :

- inscrits (rattachés à l'espace de travail) et/ou
- inscrits dans Gestion des appareils ou
- inscrits dans Intune App Protection

Si aucune application de répartiteur n'est installée sur un appareil, MSAL demande à l’utilisateur d’en installer une dès que l’application tente d’obtenir un jeton de manière interactive. L’application doit alors guider l’utilisateur à travers les étapes visant à assurer la conformité de l'appareil avec la stratégie requise.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effets de l’installation et de la désinstallation d’un répartiteur

### <a name="when-a-broker-is-installed"></a>Lors de l’installation d’un répartiteur

Lorsqu’un répartiteur est installé sur un appareil, toutes les demandes de jetons interactives ultérieures (appels à `acquireToken()`) sont gérées par le répartiteur plutôt que localement par MSAL. Aucun état SSO précédemment disponible pour MSAL n’est disponible pour le répartiteur. Dès lors, l’utilisateur doit s’authentifier à nouveau ou sélectionner un compte dans la liste des comptes existants connus de l’appareil.

L’installation d’un répartiteur n'implique pas que l’utilisateur se connecte à nouveau. Ce n'est que lorsque l'utilisateur doit résoudre une exception `MsalUiRequiredException` que la demande suivante est transmise au répartiteur. L'exception `MsalUiRequiredException` est levée pour plusieurs raisons et doit être résolue de manière interactive. Voici quelques raisons courantes :

- L’utilisateur a modifié le mot de passe associé à son compte.
- Le compte de l’utilisateur ne répond plus à une stratégie d’accès conditionnel.
- L’utilisateur a révoqué son consentement pour l'association de l'application à son compte.

### <a name="when-a-broker-is-uninstalled"></a>Lors de la désinstallation d’un répartiteur

En présence d'une seule application hébergeant un répartiteur, si celle-ci est supprimée, l’utilisateur doit se connecter à nouveau. La désinstallation du répartiteur actif supprime le compte et les jetons associés de l’appareil.

Si le Portail d’entreprise Intune est installé et fait office de répartiteur, et que Microsoft Authenticator est également installé, en cas de désinstallation du Portail d’entreprise Intune (répartiteur actif), l’utilisateur doit se connecter à nouveau. Ce faisant, l'application Microsoft Authenticator devient le répartiteur actif.

## <a name="integrating-with-a-broker"></a>Intégration d'un répartiteur

### <a name="generating-a-redirect-uri-for-a-broker"></a>Génération d’un URI de redirection pour un répartiteur

Vous devez inscrire un URI de redirection compatible avec le répartiteur. L’URI de redirection du répartiteur doit contenir le nom du package de votre application, ainsi que la représentation encodée en base64 de la signature de votre application.

Le format de l’URI de redirection est le suivant : `msauth://<yourpackagename>/<base64urlencodedsignature>`

Générez votre signature url encodée en base64 à l’aide des clés de signature de votre application. Voici quelques exemples de commandes utilisant vos clés de signature de débogage :

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Pour plus d’informations sur la signature de votre application, consultez [Signer votre application](https://developer.android.com/studio/publish/app-signing).

> [!IMPORTANT]
> Utilisez votre clé de signature de production pour la version de production de votre application.

### <a name="configure-msal-to-use-a-broker"></a>Configurer MSAL pour utiliser un répartiteur

Pour utiliser un répartiteur dans votre application, vous devez attester que vous avez configuré votre redirection de répartiteur. Par exemple, incluez votre URI de redirection activé par le répartiteur, et indiquez que vous l’avez enregistré, en incluant ce qui suit dans votre fichier de configuration MSAL :

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> La nouvelle interface utilisateur d’inscription d’application dans le portail Azure vous aide à générer l’URI de redirection du répartiteur. Si vous avez enregistré votre application à l’aide de l'expérience antérieure ou du portail d’inscription des applications Microsoft, vous devrez peut-être générer l’URI de redirection et mettre à jour la liste des URI de redirection manuellement dans le portail.

### <a name="broker-related-exceptions"></a>Exceptions liées au répartiteur

MSAL communique avec le répartiteur de deux manières :

- Service lié au répartiteur
- Android AccountManager

MSAL utilise tout d’abord le service lié au répartiteur, car l’appel de ce service ne requiert aucune autorisation Android. Si la liaison au service lié échoue, MSAL utilise l’API Android AccountManager. MSAL procède ainsi si l’autorisation `"READ_CONTACTS"` a déjà été accordée à votre application.

Si vous recevez une exception `MsalClientException` accompagnée du code d’erreur `"BROKER_BIND_FAILURE"`, deux options s'offrent à vous :

- Demandez à l’utilisateur de désactiver l’optimisation de l’alimentation pour l’application Microsoft Authenticator et le Portail d’entreprise Intune.
- Demandez à l’utilisateur d’accorder l’autorisation `"READ_CONTACTS"`.
