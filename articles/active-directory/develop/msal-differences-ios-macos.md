---
title: Microsoft Authentication Library (MSAL) pour iOS et macOS  | Azure
description: Décrit les différences d’utilisation de la bibliothèque MSAL (Microsoft Authentication Library) entre iOS et macOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb6e55e83412535f29284db48fb37fd514b35d4
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269371"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Différences entre les bibliothèques MSAL pour iOS et macOS

Cet article explique les différences de fonctionnalités entre les versions de la bibliothèque MSAL (Microsoft Authentication Library) pour iOS et macOS.

> [!NOTE]
> Sur le Mac, MSAL prend uniquement en charge les applications macOS.

## <a name="general-differences"></a>Différences générales

MSAL pour macOS est un sous-ensemble des fonctionnalités disponibles pour iOS.

MSAL pour macOS ne prend pas en charge :

- Des types de navigateurs différents, tels que `ASWebAuthenticationSession`, `SFAuthenticationSession`, `SFSafariViewController`.
- L’authentification répartie par le biais de l’application Microsoft Authenticator.

Le partage de trousseau entre les applications du même éditeur est plus limité sur macOS 10.14 et versions antérieures. Utilisez des [listes de contrôle d’accès](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) pour spécifier les chemins des applications qui doivent partager le trousseau. L’utilisateur peut recevoir des invites de trousseau supplémentaires.

Sur macOS 10.15+, le comportement de MSAL est le même avec iOS et macOS. MSAL utilise des [groupes d’accès au trousseau](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) pour le partage de trousseau. 

### <a name="conditional-access-authentication-differences"></a>Différences liées à l’authentification d’accès conditionnel

Pour les scénarios d’accès conditionnel, il y a moins d’invites utilisateur lors de l’utilisation de MSAL pour iOS. En effet, iOS utilise l’application répartiteur (Microsoft Authenticator), ce qui évite de devoir présenter une invite à l’utilisateur dans certains cas.

### <a name="project-setup-differences"></a>Différences liées à la configuration de projet

**macOS**

- Quand vous configurez votre projet sur macOS, vérifiez que votre application est signée avec un certificat de développement ou de production valide. MSAL fonctionne quand même en mode non signé, mais il se comporte différemment en ce qui concerne la persistance du cache. L’application ne doit être exécutée non signée qu’à des fins de débogage. Si vous distribuez l’application non signée, le comportement est le suivant :
1. Sur 10.14 et versions antérieures, MSAL invite l’utilisateur à entrer un mot de passe de trousseau chaque fois qu’il redémarre l’application.
2. Sur 10.15+, MSAL invite l’utilisateur à fournir des informations d’identification pour chaque acquisition de jeton. 

- Les applications macOS n’ont pas besoin d’implémenter l’appel AppDelegate.

**iOS**

- Des étapes supplémentaires sont nécessaires afin de configurer votre projet pour qu’il prenne en charge le workflow du répartiteur d’authentification. Ces étapes sont décrites dans le tutoriel.
- Les projets iOS doivent inscrire des schémas personnalisés dans le fichier info.plist. Cela n’est pas obligatoire sur macOS.
