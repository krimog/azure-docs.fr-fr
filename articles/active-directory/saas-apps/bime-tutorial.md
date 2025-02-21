---
title: "Didacticiel : Intégration d'Azure Active Directory à Bime | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Bime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52a34c688b7d8e12dac1d76b3e03fb1b50583c6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106255"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Didacticiel : Intégration d'Azure Active Directory à Bime

Dans ce didacticiel, vous allez apprendre à intégrer Bime à Azure Active Directory (Azure AD).
L’intégration de Bime dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Bime.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Bime (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Bime, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Bime pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Bime prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="adding-bime-from-the-gallery"></a>Ajout de Bime à partir de la galerie

Pour configurer l’intégration de Bime à Azure AD, vous devez ajouter Bime, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Bime à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Bime**, sélectionnez **Bime** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Bime dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Bime, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Bime associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Bime, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Bime](#configure-bime-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Bime](#create-bime-test-user)** pour avoir un équivalent de Britta Simon dans Bime lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Bime, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Bime**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Bime](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<tenant-name>.Bimeapp.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<tenant-name>.Bimeapp.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![Modifier le certificat de signature SAML](common/edit-certificate.png)

6. Dans la section **Certificat de signature SAML**, copiez l’**EMPREINTE** et enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-thumbprint.png)

7. Dans la section **Configurer Bime**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-bime-single-sign-on"></a>Configurer l’authentification unique Bime

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Bime en tant qu’administrateur.

2. Dans la barre d’outils, cliquez sur **Admin**, puis sur **Account**.
  
    ![Administrateur](./media/bime-tutorial/ic775558.png "Administrateur")

3. Dans la page de configuration du compte, procédez comme suit :
  
    ![Configurer l’authentification unique](./media/bime-tutorial/ic775559.png "Configurer l’authentification unique")

    a. Sélectionnez **Activer l’authentification SAML**.

    b. Dans la zone de texte **Remote Login URL** (URL de connexion à distance), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Certificate Fingerprint** (Empreinte digitale du certificat), collez la valeur **Empreinte** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Bime.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Bime**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Bime**.

    ![Lien Bime dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-bime-test-user"></a>Créer un utilisateur de test Bime

Pour permettre aux utilisateurs Azure AD de se connecter à Bime, vous devez les attribuer dans Bime. En l’occurrence, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre locataire **Bime** .

2. Dans la barre d’outils, cliquez sur **Admin**, puis sur **Users**.

    ![Administrateur](./media/bime-tutorial/ic775561.png "Administrateur")

3. Dans la **Users List**, cliquez sur **Add New User** (« + »).

    ![Utilisateurs](./media/bime-tutorial/ic775562.png "Utilisateurs")

4. Dans la page de boîte de dialogue **User Details** , procédez comme suit :

    ![Détails de l’utilisateur](./media/bime-tutorial/ic775563.png "Détails de l’utilisateur")

    a. Dans la zone de texte **First name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **E-mail**, entrez l’e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bime pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Bime dans le volet d’accès, vous devez être connecté automatiquement à l’application Bime pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

