---
title: Authentification unique SAML pour les applications locales avec le proxy d’application Azure AD | Microsoft Docs
description: Découvrez comment fournir l’authentification unique pour les applications locales qui sont sécurisées avec l’authentification SAML. Fournissez un accès à distance aux applications locales avec le proxy d’application.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff5152a2d65ea3921f0fef7f3a020bcea80d1769
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421254"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Authentification unique SAML pour les applications locales avec le proxy d’application

Vous pouvez fournir l’authentification unique (SSO) aux applications locales qui sont sécurisées avec l’authentification SAML, et fournir un accès à distance à ces applications via le proxy d’application. Avec l’authentification unique SAML, Azure Active Directory (Azure AD) s’authentifie auprès de l’application en utilisant le compte Azure AD de l’utilisateur. Azure AD communique les informations d’authentification à l’application via un protocole de connexion. Vous pouvez également mapper les utilisateurs à des rôles d’application basés sur les règles, que vous définissez dans vos revendications SAML. Si vous activez le proxy d’application en plus de l’authentification unique SAML, vos utilisateurs bénéficient d’un accès externe à l’application et d’une expérience d’authentification unique très fluide.

Les applications doivent pouvoir consommer des jetons SAML émis par **Azure Active Directory**. Cette configuration ne s’applique pas aux applications qui utilisent un fournisseur d’identité local. Pour ces scénarios, nous vous recommandons de consulter [Ressources pour la migration d’applications vers Azure Active Directory](migration-resources.md).

L’authentification unique SAML avec le proxy d’application fonctionne également avec la fonctionnalité de chiffrement de jetons SAML. Pour plus d’informations, consultez [Configurer le chiffrement des jetons SAML Azure AD](howto-saml-token-encryption.md).

Les diagrammes de protocole ci-dessous décrivent la séquence de l’authentification unique pour un flux initié par le fournisseur de services et un flux d’identité initié par le fournisseur d’identité. Le proxy d’application fonctionne avec l’authentification unique SAML en mettant en cache la demande et la réponse SAML vers et depuis l’application locale.

  ![Flux du fournisseur de services SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Flux du fournisseur de services SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Créer une application et configurer l’authentification unique SAML

1. Dans le portail Azure, sélectionnez **Azure Active Directory > Applications d’entreprise**, puis sélectionnez **Nouvelle application**.

2. Sous **Ajouter votre propre application**, sélectionnez **Application ne figurant pas dans la galerie**.

3. Entrez le nom d’affichage de votre nouvelle application, puis sélectionnez **Ajouter**.

4. Dans la page **Vue d’ensemble** de l’application, sélectionnez **Authentification unique**.

5. Sélectionnez **SAML** comme méthode d’authentification unique.

6. Configurez d’abord l’authentification unique SAML pour qu’elle fonctionne sur le réseau d’entreprise. Dans la page **Configurer l’authentification unique avec SAML**, accédez au titre **Configuration SAML de base** et sélectionnez l’icône **Modifier** (un crayon). Suivez les étapes de [Entrer la configuration SAML de base](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) pour configurer l’authentification basée sur SAML pour l’application.

7. Ajoutez au moins un utilisateur à l’application et vérifiez que le compte de test a accès à l’application. En étant connecté au réseau d’entreprise, utilisez le compte de test pour vérifier si vous avez une authentification unique pour l’application. 

   > [!NOTE]
   > Une fois que vous avez configuré le proxy d’application, vous revenez et vous mettez à jour l’**URL de réponse** de SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publier une application locale avec le proxy d’application

Pour fournir l’authentification unique aux applications locales, vous devez activer le proxy d’application et installer un connecteur. Consultez le tutoriel [Ajouter une application locale pour l’accès à distance via le proxy d’application dans Azure AD](application-proxy-add-on-premises-application.md) pour découvrir comment préparer votre environnement local, installer et inscrire un connecteur, et tester le connecteur. Suivez ensuite ces étapes pour publier votre nouvelle application avec le proxy d’application. Pour les autres paramètres non mentionnés ci-dessous, reportez-vous à la section [Ajouter une application locale à Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) du tutoriel.

1. Avec l’application encore ouverte dans le portail Azure, sélectionnez **Proxy d’application**. Spécifiez l’**URL interne** pour l’application. Si vous utilisez un domaine personnalisé, vous devez également charger le certificat SSL pour votre application. 
   > [!NOTE]
   > Une bonne pratique consiste à utiliser des domaines personnalisés chaque fois que cela est possible, afin d’optimiser l’expérience utilisateur. Pour plus d’informations, consultez [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md).

2. Sélectionnez **Azure Active Directory** comme méthode de **pré-authentification**pour votre application.

3. Copiez l’**URL externe** de l’application. Vous aurez besoin de cette URL pour terminer la configuration de SAML.

4. En utilisant le compte de test, essayez d’ouvrir l’application avec l’**URL externe** pour vérifier que le proxy d’application est correctement configuré. S’il y a des problèmes, consultez [Résoudre les problèmes de proxy d’application et les messages d’erreur](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Mettre à jour la configuration SAML

1. Avec l’application encore ouverte dans le portail Azure, sélectionnez **Authentification unique**. 

2. Dans la page **Configurer l’authentification unique avec SAML**, accédez au titre **Configuration SAML de base** et sélectionnez l’icône **Modifier** (un crayon). L’**URL externe** que vous avez configurée dans le proxy d’application remplit automatiquement les champs **Identificateur**, **URL de réponse** et **URL de déconnexion**. Ne modifiez pas ces URL, car elles sont nécessaires au bon fonctionnement du proxy d’application.

3. Modifiez l’**URL de réponse** configurée précédemment afin que son domaine soit accessible par le proxy d’application. Par exemple, si votre **URL externe** est `https://contosotravel-f128.msappproxy.net` et que l’**URL de réponse** d’origine était `https://contosotravel.com/acs`, vous devez mettre à jour l’**URL de réponse** en `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Entrer les données de la configuration SAML de base](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Cochez la case en regard de l’**URL de réponse** mise à jour pour la marquer comme valeur par défaut.

   * Si l’**URL de réponse** nécessaire est déjà indiquée, marquez cette **URL de réponse** comme valeur par défaut et supprimez l’**URL de réponse** précédemment configurée.

   * Pour un flux initié par le fournisseur de services, vérifiez que l’application back-end spécifie l’**URL de réponse** ou l’URL du service consommateur d’assertion correcte pour recevoir le jeton d’authentification.

    > [!NOTE]
    > Si l’application back-end s’attend à ce que l’**URL de réponse** soit l’URL interne, vous devez utiliser des [domaines personnalisés](application-proxy-configure-custom-domain.md) pour que l’URL interne et l’URL externe correspondent, ou installer l’extension de connexion sécurisée à Mes applications sur les appareils des utilisateurs. Cette extension sera automatiquement redirigée vers le service de proxy d’application qui convient. Pour installer l’extension, consultez [Extension de connexion sécurisée Mes applications](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Test de l'application

Une fois toutes ces étapes effectuées, votre application doit être opérationnelle. Pour tester l’application :

1. Ouvrez un navigateur et accédez à l’**URL externe** que vous avez créée lors de la publication de l’application. 
1. Connectez-vous au compte de test que vous avez attribué à l’application. Vous devriez pouvoir charger l’application et vous connecter à l’aide de l’authentification unique.

## <a name="next-steps"></a>Étapes suivantes

- [Comment le proxy d’application Azure AD fournit-il une authentification unique ?](application-proxy-single-sign-on.md)
- [Résoudre les problèmes du proxy d’application](application-proxy-troubleshoot.md)
