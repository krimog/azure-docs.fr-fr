---
title: Bien démarrer avec les stratégies personnalisées - Azure Active Directory B2C
description: Découvrez comment bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 411710280a631d341adeb55bc4b587a613ee3c4c
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643623"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Les [stratégies personnalisées](active-directory-b2c-overview-custom.md) sont des fichiers de configuration qui définissent le comportement de votre locataire Azure Active Directory B2C (Azure AD B2C). Dans cet article, vous allez créer une stratégie personnalisée qui prend en charge l’inscription ou la connexion de comptes locaux à l’aide d’une adresse e-mail et d’un mot de passe. Vous préparerez également votre environnement pour l’ajout de fournisseurs d’identité.

## <a name="prerequisites"></a>Prérequis

- Si vous n’en avez pas, [créez un locataire Azure AD B2C](tutorial-create-tenant.md) qui est lié à votre abonnement Azure.
- [Inscrivez votre application](tutorial-register-applications.md) dans le locataire que vous avez créé afin qu’il puisse communiquer avec Azure AD B2C.
- Effectuez les étapes décrites dans [Configurer l’inscription et la connexion avec un compte Facebook](active-directory-b2c-setup-fb-app.md) pour configurer une application Facebook.

## <a name="add-signing-and-encryption-keys"></a>Ajouter des clés de signature et de chiffrement

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Utilisez le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.

### <a name="create-the-signing-key"></a>Créer la clé de signature

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Generate`.
1. Dans **Nom**, entrez `TokenSigningKeyContainer`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
1. Pour **Type de clé**, sélectionnez **RSA**.
1. Pour **Utilisation de la clé**, sélectionnez **Signature**.
1. Sélectionnez **Create** (Créer).

### <a name="create-the-encryption-key"></a>Créer la clé de chiffrement

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Generate`.
1. Dans **Nom**, entrez `TokenEncryptionKeyContainer`. Il est possible que le préfixe `B2C_1A`_ soit ajouté automatiquement.
1. Pour **Type de clé**, sélectionnez **RSA**.
1. Pour **Utilisation de la clé**, sélectionnez **Chiffrement**.
1. Sélectionnez **Create** (Créer).

### <a name="create-the-facebook-key"></a>Créer la clé Facebook

Ajoutez le [secret d’application](active-directory-b2c-setup-fb-app.md) de votre application Facebook en tant que clé de stratégie. Vous pouvez utiliser le secret d’application de l’application que vous avez créée dans le cadre des conditions préalables de cet article.

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Pour **Nom**, entrez `FacebookSecret`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
1. Dans **Secret**, entrez le *Secret d’application* de votre application Facebook depuis developers.facebook.com. Cette valeur correspond au secret, pas à l’ID d’application.
1. Pour **Utilisation de la clé**, sélectionnez **Signature**.
1. Sélectionnez **Create** (Créer).

## <a name="register-identity-experience-framework-applications"></a>Inscrire les applications de l’infrastructure d’expérience d’identité

Azure AD B2C exige que vous inscriviez deux applications qu’il utilise pour inscrire et connecter des utilisateurs avec des comptes locaux : *IdentityExperienceFramework*, API web, et *ProxyIdentityExperienceFramework*, application native avec autorisation déléguée à l’application IdentityExperienceFramework. Vos utilisateurs peuvent s’inscrire avec une adresse e-mail ou un nom d’utilisateur et un mot de passe pour accéder à vos applications inscrites par le locataire, ce qui crée un « compte local ». Les comptes locaux existent uniquement dans votre locataires Azure AD B2C.

Vous ne devez inscrire ces deux applications dans votre locataire Azure AD B2C qu’une seule fois.

### <a name="register-the-identityexperienceframework-application"></a>Inscrire l’application IdentityExperienceFramework

Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Sélectionnez **Tous les services** dans le coin supérieur gauche du portail Azure.
1. Dans la zone de recherche, entrez `Azure Active Directory`.
1. Dans la liste des résultats, sélectionnez sur **Azure Active Directory**.
1. Sous **Gérer** dans le menu de gauche, sélectionnez **Inscriptions d’applications (hérité)** .
1. Sélectionnez **Nouvelle inscription d’application**.
1. Pour **Nom**, entrez `IdentityExperienceFramework`.
1. Pour **Type d’application**, choisissez **Application/API web**.
1. Pour **URL de connexion**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est le nom de domaine de votre locataire Azure AD B2C. Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).
1. Sélectionnez **Create** (Créer). Après sa création, copiez l’ID d’application et enregistrez-le pour une utilisation ultérieure.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Pour **Nom**, entrez `IdentityExperienceFramework`.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement**.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est le nom de domaine de votre locataires Azure AD B2C.
1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.

Ensuite, exposez l’API en ajoutant une étendue :

1. Sous **Gérer**, sélectionnez **Exposer une API**.
1. Sélectionnez **Ajouter une étendue**, puis sélectionnez **Enregistrer et continuer** pour accepter l’URI d’ID d’application par défaut.
1. Entrez les valeurs suivantes pour créer une étendue qui autorise l’exécution de stratégie personnalisée dans votre locataires Azure AD B2C :
    * **Nom de l’étendue** : `user_impersonation`
    * **Nom d’affichage du consentement administrateur** : `Access IdentityExperienceFramework`
    * **Description du consentement de l’administrateur** : `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Sélectionnez **Ajouter une étendue**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Inscrire l’application ProxyIdentityExperienceFramework

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Dans **Inscriptions d’applications (héritées)** , sélectionnez **Nouvelle inscription d’application**.
1. Pour **Nom**, entrez `ProxyIdentityExperienceFramework`.
1. Pour **Type d’application**, choisissez **Native**.
1. Pour **URI de redirection**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est votre locataire Azure AD B2C.
1. Sélectionnez **Create** (Créer). Après sa création, copiez l’ID d’application et enregistrez-le pour une utilisation ultérieure.
1. Sélectionnez **Paramètres**, puis sélectionnez **Autorisations requises**, puis **Ajouter**.
1. Choisissez **Sélectionner une API**, recherchez et sélectionnez **IdentityExperienceFramework**, puis cliquez sur **Sélectionner**.
1. Cochez la case en regard d’**Accéder à IdentityExperienceFramework**, cliquez sur **Sélectionner**, puis sur **Terminé**.
1. Sélectionnez **Accorder des autorisations** puis confirmez en sélectionnant **Oui**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Pour **Nom**, entrez `ProxyIdentityExperienceFramework`.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement**.
1. Sous **URI de redirection**, utilisez la liste déroulante pour sélectionner **client public/natif (Bureau et mobile)** .
1. Pour **URI de redirection**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est votre locataire Azure AD B2C.
1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.

Ensuite, spécifiez que l’application doit être traitée comme un client public :

1. Sous **Gérer**, sélectionnez **Authentification**.
1. Sélectionnez **Essayer la nouvelle expérience** (si elle est indiquée).
1. Sous **Paramètres avancés**, activez **Considérer l’application comme un client public** (sélectionnez **Oui**).
1. Sélectionnez **Enregistrer**.

Maintenant, accordez des autorisations à l’étendue de l’API que vous avez exposée précédemment dans l’inscription  *IdentityExperienceFramework* :

1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **Mes API**, puis sélectionnez l’application **IdentityExperienceFramework**.
1. Sous **Autorisation**, sélectionnez l’étendue **user_impersonation** que vous avez définie précédemment.
1. Sélectionnez **Ajouter des autorisations**. Comme vous l’indiquent les instructions, patientez quelques minutes avant de passer à l’étape suivante.
1. Sélectionnez **Accorder le consentement de l’administrateur pour (nom de votre locataire)** .
1. Sélectionnez le compte administrateur actuellement connecté ou connectez-vous avec un compte de votre locataire Azure AD B2C qui possède au minimum le rôle *Administrateur d’application cloud*.
1. Sélectionnez **Accepter**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention « Accordé pour ... » apparaît dans **État** pour les deux étendues. La propagation des autorisations peut prendre quelques minutes.

* * *

## <a name="custom-policy-starter-pack"></a>Pack de démarrage de stratégie personnalisée

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des profils techniques et des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies pour vous permettre de vous familiariser rapidement. Chacun de ces packs de démarrage contient le plus petit nombre possible de profils techniques et de parcours utilisateur nécessaires pour réaliser les scénarios décrits :

- **LocalAccounts** : vous permet d’utiliser uniquement des comptes locaux.
- **SocialAccounts** : vous permet d’utiliser uniquement des comptes sociaux (ou fédérés).
- **SocialAndLocalAccounts** : vous permet d’utiliser à la fois des comptes locaux et des comptes sociaux.
- **SocialAndLocalAccountsWithMFA** : active les options social, local et authentification multifacteur.

Chaque pack de démarrage contient :

- **Ficher de base** : il n’y a que peu de modifications à apporter à la base. Exemple : *TrustFrameworkBase.xml*
- **Fichier d’extension** : c’est sur ce fichier que portent la plupart des modifications de la configuration. Exemple : *TrustFrameworkExtensions.xml*
- **Fichiers de partie de confiance** : sont des fichiers propres à chaque tâche, appelés par l’application. Exemples : *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

Dans cet article, vous allez modifier les fichiers de stratégie personnalisée XML dans le pack de démarrage **SocialAndLocalAccounts**. Si vous avez besoin d’un éditeur XML, essayez [Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.

### <a name="get-the-starter-pack"></a>Obtenez le pack de démarrage

Récupérez les packs de démarrage de stratégie personnalisés à partir de GitHub, puis mettez à jour les fichiers XML dans le pack de démarrage SocialAndLocalAccounts avec votre nom de locataire Azure AD B2C.

1. [Téléchargez le fichier .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clonez le référentiel :

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Dans tous les fichiers du répertoire **SocialAndLocalAccounts**, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C.

    Par exemple, si le nom de votre locataire B2C est *contosotenant*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Ajouter des ID d’applications à la stratégie personnalisée

Ajoutez les ID d’applications au fichier d’extensions *TrustFrameworkExtensions.xml*.

1. Ouvrez `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** et trouvez l’élément `<TechnicalProfile Id="login-NonInteractive">`.
1. Remplacez les deux instances de `IdentityExperienceFrameworkAppId` par l’ID de l’application IdentityExperienceFramework que vous avez créée précédemment.
1. Remplacez les deux instances de `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application ProxyIdentityExperienceFramework que vous avez créée précédemment.
1. Enregistrez le fichier .

## <a name="upload-the-policies"></a>Charger les stratégies

1. Sélectionnez l’élément de menu **Identity Experience Framework** dans votre locataire B2C du portail Azure.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Dans cet ordre, téléchargez les fichiers de stratégie :
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Lorsque vous chargez les fichiers, Azure ajoute le préfixe `B2C_1A_` à chacun d’eux.

> [!TIP]
> Si votre éditeur XML prend en charge la validation, validez la conformité des fichiers au schéma XML `TrustFrameworkPolicy_0.3.0.0.xsd`, qui se trouve dans le dossier racine du pack de démarrage. La validation du schéma XML identifie les erreurs avant le chargement.

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Sous **Stratégies personnalisées**, sélectionnez **B2C_1A_signup_signin**.
1. Pour **sélectionner une application** dans la page de présentation de la stratégie personnalisée, sélectionnez l’application web nommée *webapp1* que vous avez inscrite précédemment.
1. Assurez-vous que l’**URL de réponse** soit `https://jwt.ms`.
1. Sélectionnez **Exécuter maintenant**.
1. Inscrivez-vous au moyen d’une adresse e-mail.
1. Sélectionnez **Exécuter maintenant** à nouveau.
1. Connectez-vous avec le même compte pour vérifier que votre configuration est correcte.

## <a name="add-facebook-as-an-identity-provider"></a>Ajouter Facebook en tant que fournisseur d’identité

1. Dans le fichier `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** , remplacez la valeur de `client_id` par l’ID d’application Facebook :

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Chargez le fichier *TrustFrameworkExtensions.xml* sur votre locataire.
1. Sous **Stratégies personnalisées**, sélectionnez **B2C_1A_signup_signin**.
1. Sélectionnez **Exécuter maintenant** et sélectionnez Facebook pour vous connecter avec Facebook et tester la stratégie personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, essayez d’ajouter Azure Active Directory (Azure AD) comme fournisseur d’identité. Le fichier de base utilisé dans ce guide de démarrage contient déjà une partie du contenu dont vous avez besoin pour ajouter d’autres fournisseurs d’identité comme Azure AD.

Pour plus d’informations sur la configuration de Azure AD et sur le fournisseur d’identité, consultez [Configurer l’inscription et la connexion avec un compte Azure Active Directory à l’aide de stratégies personnalisées Active Directory B2C](active-directory-b2c-setup-aad-custom.md).
