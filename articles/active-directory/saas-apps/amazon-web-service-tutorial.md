---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à Amazon Web Services (AWS) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed1f1b6973d96fdc0bce560877720adfc5e33081
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170888"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à Amazon Web Services (AWS)

Dans ce tutoriel, vous allez apprendre à intégrer Amazon Web Services à Azure Active Directory (Azure AD). Lorsque vous intégrez Amazon Web Services (AWS) à Azure AD, vous pouvez :

* Dans Azure AD, contrôler qui a accès à Amazon Web Services (AWS).
* Autoriser vos utilisateurs à se connecter automatiquement à Amazon Web Services (AWS) avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagramme de la relation entre Azure AD et AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Vous pouvez configurer plusieurs identificateurs pour plusieurs instances. Par exemple :

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Avec ces valeurs, Azure AD supprime la valeur de **#** et envoie la valeur correcte `https://signin.aws.amazon.com/saml` en tant qu’URL d’audience dans le jeton SAML.

Nous vous recommandons cette approche pour les raisons suivantes :

- Chaque application vous fournit un certificat X509 unique. Chaque instance d’une instance de l’application AWS peut alors avoir une date d’expiration de certificat différente que vous pouvez gérer en fonction de chaque compte AWS. La substitution de certificat globale est plus aisée dans ce cas.

- Vous pouvez activer l’attribution d’utilisateurs avec une application AWS dans Azure AD, puis notre service extrait tous les rôles de ce compte AWS. Vous n’êtes pas obligé d’ajouter ou de mettre à jour manuellement les rôles AWS sur l’application.

- Vous pouvez attribuer le propriétaire de l’application individuellement pour l’application. Cette personne peut gérer l’application directement dans Azure AD.

> [!Note]
> Veillez à utiliser uniquement une application de la galerie.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement AWS pour lequel l’authentification unique (SSO) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Amazon Web Services (AWS) prend en charge l’authentification unique démarrée par **le fournisseur de services et le fournisseur d’identité**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Ajout d’Amazon Web Services (AWS) à partir de la galerie

Pour configurer l’intégration d’Amazon Web Services (AWS) avec Azure AD, vous devez ajouter Amazon Web Services (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Amazon Web Services (AWS)** dans la zone de recherche.
1. Sélectionnez **Amazon Web Services (AWS)** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Configurer et tester l’authentification unique Azure AD pour Amazon Web Services (AWS)

Configurez et testez l’authentification unique Azure AD avec Amazon Web Services (AWS) pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Amazon Web Services (AWS) associé.

Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Amazon Web Services (AWS)](#configure-amazon-web-services-aws-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** pour avoir un équivalent de B.Simon dans Amazon Web Services (AWS) lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Amazon Web Services (AWS)** , recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en sélectionnant **Enregistrer**.

1. Quand vous configurez plusieurs instances, indiquez la valeur d’identificateur. À partir de la deuxième instance, utilisez le format suivant, y compris un signe **#** pour spécifier une valeur SPN unique.

    `https://signin.aws.amazon.com/saml#2`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Amazon Web Services (AWS)** , copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Amazon Web Services (AWS).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Amazon Web Services (AWS)** .
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-amazon-web-services-aws-sso"></a>Configurer l’authentification unique Amazon Web Services (AWS)

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise AWS en tant qu’administrateur.

2. Sélectionnez l’**icône d’accueil d’AWS**.

    ![Capture d’écran du site d’entreprise AWS, avec mise en évidence de l’icône d’accueil d’AWS][11]

3. Sélectionnez **Gestion des identités et des accès**.

    ![Capture d’écran de la page des services AWS, avec mise en évidence de l’option IAM][12]

4. Sélectionnez **Identity Providers** (Fournisseurs d’identité) > **Create Provider** (Créer un fournisseur).

    ![Capture d’écran de la page IAM, avec mise en évidence des éléments Identity Providers et Create Provider][13]

5. Dans la page **Configure Provider** (Configurer le fournisseur), effectuez les étapes suivantes :

    ![Capture d’écran de Configure Provider][14]

    a. Pour **Type de fournisseur**, sélectionnez **SAML**.

    b. Dans **Provider Name** (Nom de fournisseur), tapez un nom de fournisseur (par exemple : *WAAD*).

    c. Pour charger le **fichier de métadonnées** téléchargé à partir du portail Azure, sélectionnez **Choisir un fichier**.

    d. Sélectionnez **Étape suivante**.

6. Dans la page **Verify Provider Information** (Vérifier les informations du fournisseur), sélectionnez **Create**.

    ![Capture d’écran de Verify Provider Information, avec mise en évidence de l’option Créer][15]

7. Sélectionnez **Roles** > **Create role**.

    ![Capture d’écran de la page Roles][16]

8. Dans la page **Create role** (Créer un rôle), effectuez les étapes suivantes :  

    ![Capture d’écran de la page Create role][19]

    a. Dans **Select type of trusted entity** (Sélectionnez un type d’entité approuvé), Sélectionnez **SAML 2.0 federation** (Fédération SAML 2.0).

    b. Dans **Choose a SAML 2.0 Provider** (Choisir un fournisseur SAML 2.0), sélectionnez le **fournisseur SAML** que vous avez déjà créé (par exemple : *WAAD*).

    c. Sélectionnez **Allow programmatic and AWS Management Console access** (Autoriser l’accès par programme à AWS Management Console).
  
    d. Sélectionnez **Suivant : Autorisations**.

9. Dans la boîte de dialogue **Attach Permissions Policies** (Attacher des stratégies d’autorisation), attachez la stratégie appropriée pour votre organisation. Ensuite, sélectionnez **Next: Vérification**).  

    ![Capture d’écran de la boîte de dialogue Attach Permissions Policies][33]

10. Dans la boîte de dialogue **Review** (Vérification), effectuez les étapes suivantes :

    ![Capture d’écran de la boîte de dialogue Review][34]

    a. Dans **Role name** (Nom du rôle), entrez votre nom de rôle.

    b. Dans **Role description** (Description du rôle), entrez la description.

    c. Sélectionnez **Create role**.

    d. Créez autant de rôles que nécessaire et mappez-les vers le fournisseur d’identité.

11. Utilisez les informations d’identification du compte de service AWS pour récupérer les rôles depuis le compte AWS dans la section d’attribution d’utilisateurs d’Azure AD. Pour ce faire, ouvrez la page d’accueil de la console AWS.

12. Sélectionnez **Services**. Sous **Security, Identity & Compliance** (Sécurité, identité et conformité), sélectionnez **IAM**.

    ![Capture d’écran de la page d’accueil de la console AWS, avec mise en évidence de Services et IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Dans la section IAM, sélectionnez **Policies** (Stratégies).

    ![Capture d’écran de la section IAM, avec mise en évidence de Policies](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Créez une stratégie en sélectionnant **Create policy** (Créer une stratégie) pour récupérer les rôles du compte AWS dans la section d’attribution d’utilisateurs Azure AD.

    ![Capture d’écran de la page Create role, avec mise en évidence de Create policy](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Créez votre propre stratégie pour extraire tous les rôles à partir de comptes AWS.

    ![Capture d’écran de la page Create policy, avec mise en évidence de JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. Dans **Create policy**, sélectionnez l’onglet **JSON**.

    b. Dans le document de stratégie, ajoutez le code JSON suivant :

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Sélectionnez **Review policy** (Vérifier la stratégie) pour valider la stratégie.

    ![Capture d’écran de la page Create policy](./media/amazon-web-service-tutorial/policy5.png)

16. Définissez la nouvelle stratégie.

    ![Capture d’écran de la page Create policy, avec mise en évidence des champs Name et Description](./media/amazon-web-service-tutorial/policy2.png)

    a. Pour **Name**, entrez **AzureAD_SSOUserRole_Policy**.

    b. Pour **Description**, entrez **This policy will allow to fetch the roles from AWS accounts** (Cette stratégie permettra de récupérer les rôles à partir de comptes AWS).

    c. Sélectionnez **Créer une stratégie**.

17. Créez un compte d’utilisateur dans le service IAM d’AWS.

    a. Dans la console IAM d’AWS, sélectionnez **Users** (Utilisateurs).

    ![Capture d’écran de la console IAM d’AWS, avec mise en évidence de Users](./media/amazon-web-service-tutorial/policy3.png)

    b. Pour créer un utilisateur, sélectionnez **Add user** (Ajouter un utilisateur).

    ![Capture d’écran du bouton Add user](./media/amazon-web-service-tutorial/policy4.png)

    c. Dans la section **Add user** :

    ![Capture d’écran de la page Add user avec mise en évidence de User name et Access](./media/amazon-web-service-tutorial/adduser1.png)

    * Entrez **AzureADRoleManager** (GestionnaireDeRôlesAzureAD) comme nom d’utilisateur.

    * Pour le type d’accès, sélectionnez **Programmatic access** (Accès par programmation). Ainsi, l’utilisateur peut appeler les API et extraire les rôles du compte AWS.

    * Sélectionnez **Next: Permissions** (Suivant : Autorisations).

18. Créez une stratégie pour cet utilisateur.

    ![Capture d’écran de l’élément Add user](./media/amazon-web-service-tutorial/adduser2.png)

    a. Sélectionnez **Attach existing policies directly** (Attacher directement les stratégies existantes).

    b. Recherchez la stratégie nouvellement créée dans la section du filtre **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD).

    c. Sélectionnez la stratégie, puis sélectionnez **Next: Vérification**).

19. Passez en revue la stratégie liée à l’utilisateur attaché.

    ![Capture d’écran de la page Add user, avec mise en évidence de l’élément Create user](./media/amazon-web-service-tutorial/adduser3.png)

    a. Vérifiez le nom d’utilisateur, le type d’accès et la stratégie mappée à l’utilisateur.

    b. Sélectionnez **Create user** (Créer un utilisateur).

20. Téléchargez les informations d’identification d’un utilisateur.

    ![Capture d’écran de l’élément Add user](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiez les **Access key ID** (ID de la clé d’accès) et **Secret access key** (Clé d’accès secrète) de l’utilisateur.

    b. Dans la section d’attribution d’utilisateurs d’Azure AD, entrez ces informations d’identification pour extraire les rôles à partir de la console AWS.

    c. Sélectionnez **Fermer**.

21. Dans le portail de gestion Azure AD, dans l’application AWS, accédez à **Provisioning** (Provisionnement).

    ![Capture d’écran de l’application AWS, avec mise en évidence de Provisioning](./media/amazon-web-service-tutorial/provisioning.png)

22. Entrez la clé d’accès et le secret dans les champs **clientsecret** (secret client) et **Secret Token** (Jeton secret) respectivement.

    ![Capture d’écran de la boîte de dialogue Admin Credentials (Informations d’identification d’administrateur)](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Entrez la clé d’accès utilisateur AWS dans le champ **clientsecret**.

    b. Entrez le secret d’utilisateur AWS dans le champ **Jeton secret**.

    c. Sélectionnez **Test Connection** (Tester la connexion).

    d. Enregistrez le paramètre en sélectionnant **Save** (Enregistrer).

23. Dans la section **Settings** (Paramètres), pour **Provisioning Status** (État du provisionnement), sélectionnez **On** (Activé). Ensuite, sélectionnez **Enregistrer**.

    ![Capture d’écran de la section Settings, avec mise en évidence de l’option On](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Le service de provisionnement importe uniquement les rôles d’AWS dans Azure AD. Ce service ne reprovisionne pas les utilisateurs et les groupes d’Azure AD vers AWS.

### <a name="create-amazon-web-services-aws-test-user"></a>Créer un utilisateur de test Amazon Web Services (AWS)

L’objectif de cette section est de créer un utilisateur appelé B.Simon dans Amazon Web Services (AWS). Comme Amazon Web Services (AWS) ne nécessite pas de création d’utilisateur dans son système pour l’authentification unique, vous n’avez aucune action à accomplir ici.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Amazon Web Services (AWS) dans le volet d’accès, vous devez être connecté automatiquement à l’application Amazon Web Services (AWS) pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problèmes connus

 * Dans la section **Provisionnement**, la sous-section **Mappages** affiche un message « Chargement en cours... » et n’affiche jamais les mappages d’attributs. Le seul workflow de provisionnement pris en charge aujourd’hui est l’importation des rôles à partir d’AWS dans Azure AD pour la sélection lors de l’affectation d’un groupe ou utilisateur. Pour cela, les mappages d’attributs sont prédéterminés et ne sont pas configurables.

 * La section **Provisionnement** prend uniquement en charge l’entrée d’un ensemble d’informations d’identification pour un locataire AWS à la fois. Tous les rôles importés sont écrits dans la propriété `appRoles` de l’[objet `servicePrincipal`](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) d’Azure AD pour le locataire AWS.

   Plusieurs locataires AWS (représentés par `servicePrincipals`) peuvent être ajoutés à Azure AD à partir de la galerie à des fins de provisionnement. Toutefois, il existe un problème connu, lié à l’impossibilité d’écrire automatiquement tous les rôles importés à partir des différents `servicePrincipals` AWS utilisés pour le provisionnement dans le `servicePrincipal` unique utilisé pour l’authentification unique.

   En guise de solution de contournement, vous pouvez utiliser l’[API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pour extraire tous les `appRoles` importés dans chaque `servicePrincipal` AWS où le provisionnement est configuré. Vous pouvez ensuite ajouter ces chaînes de rôle au `servicePrincipal` AWS où l’authentification unique est configurée.

* Les rôles doivent satisfaire aux exigences suivantes pour pouvoir être importés à partir d’AWS dans Azure AD :

  * Les rôles doivent avoir un seul fournisseur SAML défini dans AWS

  * La longueur combinée de l’ARN du rôle et de l’ARN du fournisseur SAML pour un rôle importé doit avoir 119 caractères ou moins

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Amazon Web Services (AWS) avec Azure AD](https://aad.portal.azure.com/)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png