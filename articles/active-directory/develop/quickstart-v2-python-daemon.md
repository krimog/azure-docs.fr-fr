---
title: Démon Python de plateforme d’identité Microsoft | Azure
description: Découvrez comment un processus Python peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison de plateforme d’identité Microsoft à l’aide de l’identité de l’application
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68715186c624cc5ce8702073129752487a8cea63
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964023"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Démarrage rapide : acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Python à l’aide de l’identité de l’application

Dans ce démarrage rapide, écrivez une application Python qui obtient un jeton d’accès à l’aide de l’identité de l’application, puis appelle l’API Microsoft Graph pour afficher une [liste d’utilisateurs](https://docs.microsoft.com/graph/api/user-list) dans l’annuaire. Ce scénario est utile dans les situations où un travail sans périphérique de contrôle et sans assistance ou un service Windows doit s’exécuter avec une identité d’application au lieu d’une identité d’utilisateur.

> [!div renderon="docs"]
> ![Montre le fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Prérequis

Pour exécuter cet exemple, vous avec besoin de ce qui suit :

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) or [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide

> [!div renderon="docs" class="sxs-lookup"]
>
> Vous disposez de deux options pour démarrer votre application de démarrage rapide : Express (Option 1 ci-dessous) et Manuel (Option 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au nouveau volet [Portail Azure - Inscriptions des applications](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs).
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application. 
> 1. Dans la section **Nom**, entrez un nom d’application pertinent qui sera visible par les utilisateurs de l’application, par exemple `Daemon-console`, puis sélectionnez **Inscrire** pour créer l’application.
> 1. Après l’inscription, sélectionnez le menu **Certificats et secrets**.
> 1. Sous **Secrets client** , sélectionnez **+ Nouveau secret client**. Donnez-lui un nom et sélectionnez **Ajouter**. Copiez le secret dans un emplacement sûr. Vous en aurez besoin plus tard dans votre code.
> 1. À présent, sélectionnez le menu **Autorisations de l’API**, sélectionnez le bouton **+ Ajouter une autorisation** et sélectionnez **Microsoft Graph**.
> 1. Sélectionnez **Autorisations de l’application**.
> 1. Sous le nœud **Utilisateur**, sélectionnez **User.Read.All**, puis sélectionnez **Ajouter des autorisations**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Télécharger et configurer votre application de démarrage rapide
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code fonctionne dans ce guide de démarrage rapide, vous devez créer un secret client et ajouter l’autorisation d’application **User.Read.All** de l’API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-your-python-project"></a>Étape 2 : Télécharger votre projet Python

[Télécharger le projet de démon Python](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

#### <a name="step-3-configure-your-python-project"></a>Étape 3 : Configurer votre projet Python

1. Extrayez le fichier zip dans un dossier local proche de la racine du disque, par exemple, **C:\Azure-Samples**.
1. Accédez au sous-dossier **1-Call-MsGraph-WithSecret**.
1. Modifiez **parameters.json** et remplacez les valeurs des champs `authority`, `client_id` et `secret` par l’extrait de code suivant :

    ```json
    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    "client_id": "Enter_the_Application_Id_Here",
    "secret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Générer un nouveau secret client]()
    
    > [!div class="sxs-lookup" renderon="portal"]
    > > [!NOTE]
    > > Ce guide de démarrage rapide prend en charge Enter_the_Supported_Account_Info_Here.
    
    > [!div renderon="docs"]
    >> Où :
    >> * `Enter_the_Application_Id_Here` : est l’**ID d’application (client)** pour l’application que vous avez inscrite.
    >> * `Enter_the_Tenant_Id_Here` : remplacez cette valeur par l’**ID du locataire** ou le **nom du locataire** (par exemple, contoso.microsoft.com)
    >> * `Enter_the_Client_Secret_Here` : remplacez cette valeur par le secret client créé à l’étape 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Pour connaître les valeurs de l’**ID d’application (client)** et de l’**ID de l’annuaire (locataire)** , consultez la page **Vue d’ensemble** de l’application dans le Portail Azure. Pour générer une nouvelle clé, accédez à la page **Certificats et secrets**.
    
#### <a name="step-4-admin-consent"></a>Étape 4 : Consentement de l’administrateur

Si vous essayez d’exécuter l’application à ce stade, vous recevez l’erreur *HTTP 403 - Interdit* : `Insufficient privileges to complete the operation`. Cette erreur se produit parce que toute *autorisation d’application uniquement* nécessite le consentement de l’administrateur. Autrement dit, un administrateur général de votre annuaire doit donner son consentement à votre application. Sélectionnez l’une des options ci-dessous en fonction de votre rôle :

##### <a name="global-tenant-administrator"></a>Administrateur de locataires général

> [!div renderon="docs"]
> Si vous avez le rôle d’administrateur de locataires général, accédez à la page **Autorisations de l’API** dans Inscriptions d’applications (préversion) sur le portail Azure et sélectionnez **Accorder le consentement de l’administrateur pour {nom du locataire}** (où {nom du locataire} correspond au nom de votre annuaire).

> [!div renderon="portal" class="sxs-lookup"]
> Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement de l’administrateur pour Entrer_le_nom_du_locataire_ici**
> > [!div id="apipermissionspage"]
> > [Accéder à la page Autorisations de l’API]()

##### <a name="standard-user"></a>Utilisateur standard

Si vous êtes un utilisateur standard de votre locataire, vous devez demander à un administrateur général de vous accorder son consentement pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Où :
>> * `Enter_the_Tenant_Id_Here` : remplacez cette valeur par l’**ID du locataire** ou le **nom du locataire** (par exemple, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` : est l’**ID d’application (client)** pour l’application que vous avez inscrite.

#### <a name="step-5-run-the-application"></a>Étape 5 : Exécution de l'application

Vous devez installer les dépendances de cet exemple une seule fois

```console
pip install -r requirements.txt
```

Ensuite, exécutez l’application via l’invite de commandes ou la console :

```console
python confidential_client_secret_sample.py parameters.json
```

Vous devriez voir sur la console sortie un fragment Json représentant une liste d’utilisateurs dans votre annuaire Azure Active Directory.

> [!IMPORTANT]
> Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. Le secret client étant ajouté en texte brut à vos fichiers projet, il est recommandé, pour des raisons de sécurité, d’utiliser un certificat au lieu d’un secret client avant de considérer l’application comme application de production. Pour plus d’informations sur la façon d’utiliser un certificat, voir [ces instructions](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) dans le dépôt GitHub pour cet exemple, mais dans le second dossier **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Plus d’informations

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Comme vous l’avez vu, ce guide de démarrage rapide demande des jetons à l’aide de l’identité de l’application au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le terme de *[flux d’informations d’identification du client OAuth](v2-oauth2-client-creds-grant-flow.md)* . Pour plus d’informations sur l’utilisation de MSAL Python avec des applications démon, voir [cet article](scenario-daemon-overview.md).

 Vous pouvez installer MSAL Python en exécutant la commande pip suivante.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```Python
import msal
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Où : ||
> |---------|---------|
> | `config["secret"]` | Est le secret client créé pour l’application dans le portail Azure. |
> | `config["client_id"]` | Est l’**ID d’application (client)** de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
> | `config["authority"]`    | Point de terminaison STS pour l’utilisateur à authentifier. Généralement <https://login.microsoftonline.com/{tenant}> pour un cloud public, où {tenant} est le nom ou l’ID de votre locataire.|

Pour plus d’informations, consultez la [documentation de référence sur `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Demande de jetons

Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `AcquireTokenForClient` :

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Où :| |
> |---------|---------|
> | `config["scope"]` | Contient les étendues demandées. Pour les clients confidentiels, utilisez un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies de manière statique dans l’objet application défini dans le portail Azure (pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com`). Pour les API web personnalisées, `{Application ID URI}` est défini sous la section **Exposer une API** dans Inscriptions d’applications (préversion) sur le portail Azure. |

Pour plus d’informations, consultez la [documentation de référence sur `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les applications démon, consultez la page de destination du scénario

> [!div class="nextstepaction"]
> [Application démon qui appelle des API web](scenario-daemon-overview.md)

Pour le tutoriel d’application démon, consultez :

> [!div class="nextstepaction"]
> [Didacticiel de la console Python du démon](https://github.com/Azure-Samples/ms-identity-python-daemon)

En savoir plus sur les autorisations et le consentement :

> [!div class="nextstepaction"]
> [Autorisations et consentement](v2-permissions-and-consent.md)

Pour en savoir plus sur le flux d’authentification applicable à ce scénario, consultez les informations sur le flux d’informations d’identification du client OAuth 2.0 :

> [!div class="nextstepaction"]
> [Flux d’informations d’identification du client OAuth](v2-oauth2-client-creds-grant-flow.md)

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
