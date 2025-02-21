---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à ServiceNow | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65526fe501b190f9bf76c58ab1c14b5ec35fe49d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376020"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à ServiceNow

Dans ce tutoriel, vous allez apprendre à intégrer ServiceNow à Azure Active Directory (Azure AD). Quand vous intégrez ServiceNow à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ServiceNow.
* Permettre à vos utilisateurs de se connecter automatiquement à ServiceNow avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ServiceNow pour lequel l’authentification unique est activée.
* Pour ServiceNow, une instance ou un locataire de ServiceNow, version Calgary ou ultérieure.
* Pour ServiceNow Express, une instance de ServiceNow Express, version Helsinki ou ultérieure.
* Le locataire ServiceNow doit avoir le [plug-in d’authentification unique à plusieurs fournisseurs](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) activé. Pour ce faire, vous pouvez [envoyer une demande de service](https://hi.service-now.com).
* Pour la configuration automatique, activez le plug-in multifournisseur pour ServiceNow.
* Pour installer l’application ServiceNow Classic (Mobile), accédez au magasin approprié et recherchez l’application ServiceNow Classic. Ensuite, téléchargez-la.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. ServiceNow prend en charge l’authentification unique lancée par le **fournisseur de service** et le [provisionnement d’utilisateurs automatisé](servicenow-provisioning-tutorial.md).

Vous pouvez configurer l’application ServiceNow Classic (Mobile) avec Azure AD pour activer l’authentification unique. Il prend en charge les utilisateurs Android et iOS. Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

## <a name="add-servicenow-from-the-gallery"></a>Ajouter ServiceNow à partir de la galerie

Pour configurer l’intégration de ServiceNow à Azure AD, vous devez ajouter ServiceNow depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte Microsoft personnel.
1. Dans le volet de gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, entrez **ServiceNow** dans la zone de recherche.
1. Sélectionnez **ServiceNow** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurer et tester l’authentification unique Azure AD pour ServiceNow

Configurez et testez l’authentification unique Azure AD avec ServiceNow à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ServiceNow associé.

Pour configurer et tester l’authentification unique Azure AD avec ServiceNow, suivez les indications des sections ci-après :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec B.Simon.
    1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
    1. [Configurer l’authentification unique Azure AD pour ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Configurer ServiceNow](#configure-servicenow) pour configurer les paramètres de l’authentification unique côté application.
    1. [Créer un utilisateur de test ServiceNow](#create-servicenow-test-user) pour avoir un équivalent de B.Simon dans ServiceNow lié à sa représentation dans Azure AD.
    1. [Configurer l’authentification unique ServiceNow Express](#configure-servicenow-express-sso) pour configurer les paramètres de l’authentification unique côté application.  
3. [Tester l’authentification unique](#test-sso) pour vérifier si la configuration fonctionne.
4. [Tester l’authentification unique pour ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **ServiceNow**, puis recherchez la section **Gérer**. Sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Capture d’écran de la page Configurer l’authentification unique avec SAML, avec l’icône de stylet mise en évidence](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans **URL de connexion**, entrez une URL dont le modèle est le suivant : `https://<instance-name>.service-now.com/navpage.do`

    b. Dans **Identificateur (ID d’entité)** , entrez une URL dont le modèle est le suivant : `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** . Sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Capture d’écran de la section Certificat de signature SAML, avec mise en évidence de l’option Télécharger](common/certificatebase64.png)

   a. Sélectionnez le bouton Copier pour copier l’**URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes. Cette URL sera utilisée dans la suite du tutoriel.

    b. Sélectionnez **Télécharger** pour télécharger le **certificat (Base64)** , puis enregistrez le fichier du certificat sur votre ordinateur.

1. Dans la section **Configurer ServiceNow**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

   ![Capture d’écran de la section Configurer ServiceNow, avec les URL mises en évidence](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B.Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Pour **Nom**, entrez `B.Simon`.  
   1. Entrez username@companydomain.extension comme **Nom d’utilisateur**. Par exemple : `B.Simon@contoso.com`.
   1. Sélectionnez **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ServiceNow.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ServiceNow**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Capture d’écran de la section Gérer, avec Utilisateurs et groupes mis en évidence](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Capture d’écran d’Utilisateurs et groupes, avec Ajouter un utilisateur mis en évidence](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste des utilisateurs, puis choisissez **Sélectionner**.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Ensuite, choisissez **Sélectionner**.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurer l’authentification unique Azure AD pour ServiceNow Express

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ServiceNow**, sélectionnez **Authentification unique**.

    ![Capture d’écran de la page d’intégration de l’application ServiceNow, avec l’authentification unique mise en évidence](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** pour activer l’authentification unique.

    ![Capture d’écran de Sélectionner une méthode d’authentification unique, avec SAML mis en évidence](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de stylet pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Capture d’écran de la page Configurer l’authentification unique avec SAML, avec l’icône de stylet mise en évidence](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Pour **URL de connexion**, entrez une URL dont le modèle est le suivant : `https://<instance-name>.service-now.com/navpage.do`

    b. Pour **Identificateur (ID d’entité)** , entrez une URL dont le modèle est le suivant : `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le **certificat (Base64)** dont vous avez besoin parmi les options spécifiées. Enregistrez-le sur votre ordinateur.

    ![Capture d’écran de la section Certificat de signature SAML, avec mise en évidence de l’option Télécharger](common/certificatebase64.png)

6. Vous pouvez faire en sorte qu’Azure AD configure automatiquement ServiceNow pour l’authentification SAML. Pour activer ce service, accédez à la section **Configurer ServiceNow**, puis sélectionnez **Afficher les instructions détaillées** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Capture d’écran de la section Configurer ServiceNow, avec l’option Afficher les instructions détaillées mise en évidence](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Dans le formulaire **Configurer l’authentification**, entrez le nom de votre instance ServiceNow, l’utilisateur administrateur et le mot de passe de l’administrateur. Sélectionnez **Configurer maintenant**. Le rôle **security_admin** doit être attribué au nom de l’utilisateur administrateur dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow de manière à utiliser Azure AD comme fournisseur d’identité SAML, sélectionnez **Configurer manuellement l’authentification unique**. Copiez **l’URL de déconnexion, l’identificateur Azure AD et l’URL de connexion** à partir de la section Référence rapide.

    ![Capture d’écran du formulaire Configurer l’authentification, avec Configurer maintenant mis en évidence](./media/servicenow-tutorial/configure.png "Configurer l’URL de l’application")

## <a name="configure-servicenow"></a>Configurer ServiceNow

1. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

2. Activez le plug-in **Integration - Multiple Provider Single Sign-On Installer** (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs) en effectuant les étapes suivantes :

    a. Dans le volet gauche, recherchez la section **System Definition** (Définition du système) dans la zone de recherche, puis sélectionnez **Plugins** (Plug-ins).

    ![Capture d’écran de la section System Definition (Définition du système), avec System Definition et Plugins (Plug-ins) mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_03.png "Activer le plug-in")

    b. Recherchez **Integration - Multiple Provider Single Sign-On Installer** (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs).

     ![Capture d’écran de la page System Plugins (Plug-ins système), avec Integration - Multiple Provider Single Sign-On Installer (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs) mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_04.png "Activer le plug-in")

    c. Sélectionnez le plug-in. Cliquez avec le bouton droit et sélectionnez **Activate/Upgrade** (Activer/Mettre à niveau).

     ![Capture d’écran du menu contextuel du plug-in, avec Activate/Upgrade (Activer/Mettre à niveau) mis en évidence](./media/servicenow-tutorial/tutorial_activate.png "Activer le plug-in")

    d. Sélectionnez **Activer**.

     ![Capture d’écran de la boîte de dialogue Activate plugin (Activer le plug-in), avec Activate (Activer) mis en évidence](./media/servicenow-tutorial/tutorial_activate1.png "Activer le plug-in")

3. Dans le volet gauche, recherchez la section **Multi-Provider SSO** (Authentification unique multifournisseur) dans la barre de recherche, puis sélectionnez **Properties** (Propriétés).

    ![Capture d’écran de la section Multi-Provider SSO (Authentification unique multifournisseur), avec Multi-Provider SSO et Properties (Propriétés) mises en surbrillance](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurer l’URL de l’application")

4. Dans la boîte de dialogue **Multiple Provider SSO Properties** (Propriétés de l’authentification unique multifournisseur), effectuez les étapes suivantes :

    ![Capture d’écran de la boîte de dialogue Multiple Provider SSO Properties (Propriétés de l’authentification unique multifournisseur)](./media/servicenow-tutorial/ic7694981.png "Configurer l’URL de l’application")

    * Pour **Enable multiple provider SSO** (Activer l’authentification unique multifournisseur), sélectionnez **Yes** (Oui).
  
    * Pour **Enable Auto Importing of users from all identity providers into the user table** (Activer l’importation automatique des utilisateurs à partir de tous les fournisseurs d’identité dans la table utilisateur), sélectionnez **Yes** (Oui).

    * Pour **Enable debug logging got the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique multifournisseur), sélectionnez **Yes** (Oui).

    * Pour **The field on the user table that...** (Le champ dans la table utilisateur qui...), entrez **user_name**.
  
    * Sélectionnez **Enregistrer**.

6. Vous pouvez configurer ServiceNow automatiquement ou manuellement. Pour configurer ServiceNow automatiquement, effectuez les étapes suivantes :

    1. Revenez à la page d’authentification unique **ServiceNow** dans le portail Azure.

    1. Le service de configuration en un clic est fourni pour ServiceNow. Pour activer ce service, accédez à la section **Configuration de ServiceNow**, puis sélectionnez **Configurer ServiceNow** pour ouvrir la fenêtre **Configurer l’authentification**.

        ![Capture d’écran de Configurer ServiceNow, avec l’option Afficher les instructions détaillées mise en évidence](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. Dans le formulaire **Configurer l’authentification**, entrez le nom de votre instance ServiceNow, l’utilisateur administrateur et le mot de passe de l’administrateur. Sélectionnez **Configurer maintenant**. Le rôle **security_admin** doit être attribué au nom de l’utilisateur administrateur dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow de manière à utiliser Azure AD comme fournisseur d’identité SAML, sélectionnez **Configurer manuellement l’authentification unique**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la section Référence rapide.

        ![Capture d’écran du formulaire Configurer l’authentification, avec Configurer maintenant mis en évidence](./media/servicenow-tutorial/configure.png "Configurer l’URL de l’application")

    1. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

       * Dans la configuration automatique, tous les paramètres nécessaires sont configurés côté **ServiceNow**, mais le **Certificat X.509** n’est pas activé par défaut. Vous devrez le mapper manuellement à votre fournisseur d’identité dans ServiceNow. Procédez comme suit :

         1. Dans le volet gauche, recherchez la section **Multi-Provider SSO** (Authentification unique multifournisseur) dans la barre de recherche, puis sélectionnez **Identity Providers** (Fournisseurs d’identité).

            ![Capture d’écran de la section Multi-Provider SSO (Authentification unique multifournisseur), avec Identity Providers (Fournisseurs d’identité) mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

         1. Sélectionnez le fournisseur d’identité généré automatiquement.

            ![Capture d’écran des fournisseurs d’identité, avec le fournisseur d’identité généré automatiquement mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurer l’authentification unique")

         1.  Dans la section **Fournisseur d’identité**, procédez comme suit :

             ![Capture d’écran de la section Identity Provider (Fournisseur d’identité)](./media/servicenow-tutorial/automatic_config.png "Configurer l’authentification unique")

               * Pour **Name** (Nom), indiquez le nom de votre configuration (par exemple, **Microsoft Azure Federated Single Sign-On**).

               * Supprimez la valeur **Identity Provider's SingleLogoutRequest** (Demande de déconnexion unique du fournisseur d’identité) indiquée de la zone de texte.

               * Copiez la valeur **ServiceNow Homepage** (Page d’accueil de ServiceNow) et collez-la dans **URL de connexion** dans la section **Configuration SAML de base ServiceNow** du portail Azure.

                  > [!NOTE]
                  > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

              * Copiez la valeur **Entity ID / Issuer** (ID d’entité / Émetteur) et collez-la dans **Identificateur** dans la section **Configuration SAML de base ServiceNow** du portail Azure.

              * Confirmez que **NameID Policy** (Stratégie NameID) a la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

         1. Faites défiler la page jusqu’à la section **X.509 Certificate**  (Certificat X.509), puis sélectionnez **Edit** (Modifier).

             ![Capture d’écran de la section X.509 Certificate (Certificat X.509), avec Edit (Modifier) mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurer l’authentification unique")

         1. Sélectionnez le certificat, puis l’icône de flèche droite pour ajouter le certificat.

            ![Capture d’écran de Collection, avec le certificat et l’icône de flèche droite mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurer l’authentification unique")

          1. Sélectionnez **Enregistrer**.

          1. Dans le coin supérieur droit de la page, sélectionnez **Test Connection** (Tester la connexion).

             ![Capture d’écran de la page, avec Test Connection (Tester la connexion) mis en évidence](./media/servicenow-tutorial/tutorial_activate2.png "Activer le plug-in")

          1. Quand vous êtes invité à fournir vos informations d’identification, entrez-les. La page ci-après apparaît. L’erreur **SSO Logout Test Results** (Résultats des tests de déconnexion SSO) est attendue. Ignorez l’erreur et sélectionnez **Activate** (Activer).

             ![Capture d’écran de la page Test Results (Résultats des tests)](./media/servicenow-tutorial/servicenowactivate.png "Configurer l’authentification unique")
  
6. Pour configurer **ServiceNow** automatiquement, effectuez les étapes suivantes :

    1. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

    1. Dans le volet gauche, sélectionnez **Fournisseurs d’identité**.

        ![Capture d’écran de Multi-Provider SSO (Authentification unique multifournisseur), avec Identity Providers (Fournisseurs d’identité) mis en évidence](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

    1. Dans la boîte de dialogue **Identity Providers** (Fournisseurs d’identité), sélectionnez **New** (Nouveau).

        ![Capture d’écran de la boîte de dialogue Identity Providers (Fournisseurs d’identité), avec New (Nouveau) mis en évidence](./media/servicenow-tutorial/ic7694977.png "Configurer l’authentification unique")

    1. Dans la boîte de dialogue **Identity Providers** (Fournisseurs d’identité), sélectionnez **SAML**.

        ![Capture d’écran de la boîte de dialogue Identity Providers (Fournisseurs d’identité), avec SAML mis en évidence](./media/servicenow-tutorial/ic7694978.png "Configurer l’authentification unique")

    1. Dans **Import Identity Provider Metadata** (Importer les métadonnées du fournisseur d’identité), effectuez les étapes suivantes :

        ![Capture d’écran d’Import Identity Provider Metadata (Importer les métadonnées du fournisseur d’identité), avec URL et Import (Importer) mis en évidence](./media/servicenow-tutorial/idp.png "Configurer l’authentification unique")

        1. Entrez l’**URL des métadonnées de fédération d’application** que vous avez copiée à partir du portail Azure.

        1. Sélectionnez **Importer**.

    1. L’URL des métadonnées IdP est alors lue et tous les champs sont renseignés.

        ![Capture d’écran d’Identity Provider (Fournisseur d’identité)](./media/servicenow-tutorial/ic7694982.png "Configurer l’authentification unique")

        * Pour **Name** (Nom), indiquez le nom de votre configuration (par exemple, **Microsoft Azure Federated Single Sign-On**).

        * Supprimez la valeur **Identity Provider's SingleLogoutRequest** (Demande de déconnexion unique du fournisseur d’identité) indiquée de la zone de texte.

        * Copiez la valeur **ServiceNow Homepage** (Page d’accueil de ServiceNow). Collez-la dans **URL de connexion** dans la section **Configuration SAML de base ServiceNow** du portail Azure.

            > [!NOTE]
            > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

        * Copiez la valeur **Entity ID / Issuer** (ID d’entité / Émetteur). Collez-la dans **Identificateur** dans la section **Configuration SAML de base ServiceNow** du portail Azure.

        * Confirmez que **NameID Policy** (Stratégie NameID) a la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Sélectionnez **Advanced (Avancé)** . Dans la zone de texte **User Field** (Champ utilisateur), tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.

            > [!NOTE]
            > Vous pouvez configurer Azure AD de manière à émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML. Pour ce faire, accédez à la section **ServiceNow** > **Attributs** > **Authentification unique** du portail Azure et mappez le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ renseigné (par exemple, user_name).

        * Dans le coin supérieur droit de la page, sélectionnez **Test Connection** (Tester la connexion).

        * Quand vous êtes invité à fournir vos informations d’identification, entrez-les. La page ci-après apparaît. L’erreur **SSO Logout Test Results** (Résultats des tests de déconnexion SSO) est attendue. Ignorez l’erreur et sélectionnez **Activate** (Activer).

          ![Capture d’écran de la page Test Results (Résultats des tests)](./media/servicenow-tutorial/servicenowactivate.png "Configurer l’authentification unique")

### <a name="create-servicenow-test-user"></a>Créer un utilisateur de test ServiceNow

L’objectif de cette section est de créer un utilisateur appelé B.Simon dans ServiceNow. ServiceNow prend en charge le provisionnement automatique d’utilisateurs, qui est activé par défaut.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique de ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Configurer l’authentification unique ServiceNow Express

1. Connectez-vous à votre application ServiceNow Express en tant qu’administrateur.

2. Dans le volet gauche, sélectionnez **Single Sign-On** (Authentification unique).

    ![Capture d’écran de l’application ServiceNow Express, avec Single Sign-On (Authentification unique) mis en évidence ](./media/servicenow-tutorial/ic7694980ex.png "Configurer l’URL de l’application")

3. Dans la boîte de dialogue **Authentification unique**, sélectionnez l’icône de configuration en haut à droite et définissez les propriétés suivantes :

    ![Capture d’écran de la boîte de dialogue Single Sign-On (Authentification unique)](./media/servicenow-tutorial/ic7694981ex.png "Configurer l’URL de l’application")

    a. Activez **Enable multiple provider SSO** (Activer l’authentification unique à plusieurs fournisseurs) à droite.

    b. Activez **Enable debug logging for the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique à plusieurs fournisseurs) à droite.

    c. Dans **The field on the user table that...** (Le champ dans la table utilisateur qui...), entrez **user_name**.

4. Dans la boîte de dialogue **Single Sign-On** (Authentification unique), cliquez sur **Add New Certificate** (Ajouter un nouveau certificat).

    ![Capture d’écran de la boîte de dialogue Single Sign-On (Authentification unique), avec Add New Certificate (Ajouter un nouveau certificat) mis en évidence](./media/servicenow-tutorial/ic7694973ex.png "Configurer l’authentification unique")

5. Dans la boîte de dialogue **X.509 Certificates** (Certificats X.509), effectuez les étapes suivantes :

    ![Capture d’écran de la boîte de dialogue X.509 Certificates (Certificats X.509)](./media/servicenow-tutorial/ic7694975.png "Configurer l’authentification unique")

    a. Pour **Name** (Nom), attribuez un nom à votre configuration (par exemple : **TestSAML2.0**).

    b. Sélectionnez **Active**.

    c. Pour **Format**, sélectionnez **PEM**.

    d. Pour **Type**, sélectionnez **Trust Store Cert**.

    e. Dans le Bloc-notes, ouvrez le certificat encodé au format Base64 que vous avez téléchargé à partir du portail Azure. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PEM Certificate** (Certificat PEM).

    f. Sélectionnez **Update** (Mettre à jour).

6. Dans la boîte de dialogue **Single Sign-On** (Authentification unique), cliquez sur **Add New IdP** (Ajouter un nouveau fournisseur d’identité).

    ![Capture d’écran de la boîte de dialogue Single Sign-On (Authentification unique), avec Add New IdP (Ajouter un nouveau fournisseur d’identité) mis en évidence](./media/servicenow-tutorial/ic7694976ex.png "Configurer l’authentification unique")

7. Dans la boîte de dialogue **Add New Identity Provider** (Ajouter un nouveau fournisseur d’identité), sous **Configure Identity Provider** (Configurer un fournisseur d’identité), effectuez les étapes suivantes :

    ![Capture d’écran de la boîte de dialogue Add New Identity Provider (Ajouter un nouveau fournisseur d’identité)](./media/servicenow-tutorial/ic7694982ex.png "Configurer l’authentification unique")

    a. Pour **Name** (Nom), attribuez un nom à votre configuration (par exemple : **SAML 2.0**).

    b. Pour **Identity Provider URL** (URL du fournisseur d’identité), collez la valeur de l’ID du fournisseur d’identité que vous avez copiée à partir du portail Azure.

    c. Pour **Identity Provider’s AuthnRequest** (Demande d’authentification du fournisseur d’identité), collez la valeur de l’URL de la demande d’authentification que vous avez copiée à partir du portail Azure.

    d. Pour **Identity Provider’s SingleLogoutRequest** (Demande de déconnexion unique du fournisseur d’identité), collez la valeur de l’URL de déconnexion que vous avez copiée à partir du portail Azure.

    e. Pour **Identity Provider Certificate** (Certificat du fournisseur d’identité), sélectionnez le certificat que vous avez créé à l’étape précédente.

8. Sélectionnez **Advanced Settings** (Paramètres avancés). Sous **Additional Identity Provider Properties** (Autres propriétés du fournisseur d’identité), effectuez les étapes suivantes :

    ![Capture d’écran de la boîte de dialogue Add New Identity Provider (Ajouter un nouveau fournisseur d’identité), avec Advanced Settings (Paramètres avancés) mis en évidence](./media/servicenow-tutorial/ic7694983ex.png "Configurer l’authentification unique")

    a. Pour **Protocol Binding for the IDP’s SingleLogoutRequest** (Liaison de protocole pour la demande de déconnexion unique du fournisseur d’identité), entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Pour **NameID Policy** (Stratégie NameID), entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. Pour **AuthnContextClassRef Method** (Métode AuthnContextClassRef), entrez `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Désactivez **Create AuthnContextClass** (Créer AuthnContextClass).

9. Sous **Additional Service Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :

    ![Capture d’écran de la boîte de dialogue Add New Identity Provider (Ajouter un nouveau fournisseur d’identité), avec plusieurs propriétés mises en évidence](./media/servicenow-tutorial/ic7694984ex.png "Configurer l’authentification unique")

    a. Pour **ServiceNow Homepage** (Page d’accueil ServiceNow), entrez l’URL de la page d’accueil de votre instance ServiceNow.

    > [!NOTE]
    > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

    b. Pour **Entity ID / Issuer** (ID d’entité / Émetteur), entrez l’URL de votre locataire ServiceNow.

    c. Pour **Audience URI** (URI d’audience), entrez l’URL de votre locataire ServiceNow.

    d. Pour **Clock Skew** (Décalage de l’horloge), entrez **60**.

    e. Pour **User Field** (Champ utilisateur), entrez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.

    > [!NOTE]
    > Vous pouvez configurer Azure AD de manière à émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML. Pour ce faire, accédez à la section **ServiceNow** > **Attributs** > **Authentification unique** du portail Azure et mappez le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ renseigné (par exemple, user_name).

    f. Sélectionnez **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette ServiceNow dans le volet d’accès, vous devez être connecté automatiquement à l’application ServiceNow pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Tester l’authentification unique pour ServiceNow Classic (Mobile)

1. Ouvrez votre application **ServiceNow Classic (Mobile)** et effectuez les étapes suivantes :

    a. Sélectionnez le signe plus dans le coin inférieur droit.

    ![Capture d’écran de l’application ServiceNow Classic, avec le signe plus mis en évidence](./media/servicenow-tutorial/test03.png)

    b. Entrez le nom de votre instance ServiceNow, puis sélectionnez **Continue** (Continuer).

    ![Capture d’écran de la page Add Instance (Ajouter une instance), avec Continue (Continuer) mis en évidence](./media/servicenow-tutorial/test04.png)

    c. Dans la page **Log in** (Connexion), effectuez les étapes suivantes :

    ![Capture d’écran de la page Log in (Connexion), avec Use external login (Utilise une connexion externe) mis en évidence](./media/servicenow-tutorial/test01.png)

    *  Entrer un nom d’utilisateur dans **Username**, par exemple B.simon@contoso.com.

    *  Sélectionnez **USE EXTERNAL LOGIN** (Utiliser une connexion externe). Vous êtes redirigé vers la page Azure AD pour la connexion.
    
    *  Entrez vos informations d’identification. Si une authentification tierce ou toute autre fonctionnalité de sécurité est activée, l’utilisateur doit répondre en conséquence. La **page d’accueil** de l’application s’affiche.

        ![Capture d’écran de la page d’accueil de l’application](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement des utilisateurs](servicenow-provisioning-tutorial.md)

- [Essayer ServiceNow avec Azure AD](https://aad.portal.azure.com)
