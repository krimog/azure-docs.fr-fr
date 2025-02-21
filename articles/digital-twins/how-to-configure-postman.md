---
title: Guide pratique pour configurer Postman pour Azure Digital Twins | Microsoft Docs
description: Guide pratique pour configurer Postman pour Azure Digital Twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 14e6a52f86586eaae019d9658c2f813a15fc3474
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949205"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Guide pratique pour configurer Postman pour Azure Digital Twins

Cet article décrit comment configurer le client Postman REST pour interagir avec et tester les API de gestion Azure Digital Twins. Il décrit plus précisément :

* Comment configurer une application Azure Active Directory pour qu’elle utilise le flux d’octroi implicite OAuth 2.0.
* Comment utiliser le client Postman REST pour effectuer des requêtes HTTP porteuses de jeton à vos API de gestion.
* Comment utiliser Postman pour effectuer des requêtes POST multipart sur vos API de gestion.

## <a name="postman-summary"></a>Postman en bref

Commencez avec Azure Digital Twins en utilisant un outil client REST comme [Postman](https://www.getpostman.com/) pour préparer votre environnement de test local. Le client Postman vous aide à créer rapidement des requêtes HTTP complexes. Téléchargez la version bureau du client Postman en accédant à l’adresse [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) est un outil de test REST qui trouve les principales fonctionnalités de requête HTTP dans une interface graphique utilisateur utile de type Bureau à base de plug-ins.

Avec le client Postman, les développeurs de solutions peuvent spécifier le type de requête HTTP (*POST*, *GET*, *UPDATE*, *PATCH* et *DELETE*), le point de terminaison d’API à appeler et l’utilisation de SSL. Postman prend aussi en charge l’ajout d’en-têtes, de paramètres, de données de formulaire et de corps de requête HTTP.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurer Azure Active Directory pour utiliser le flux d’octroi implicite OAuth 2.0

Configurez votre application Azure Active Directory pour utiliser le flux d’octroi implicite OAuth 2.0.

1. Ouvrez le volet **API autorisées** pour l’inscription de votre application. Sélectionnez le bouton **Ajouter une autorisation**. Dans le volet **Demander des autorisations d’API**, sélectionnez l’onglet **API utilisées par mon organisation**, puis recherchez :
    
    1. `Azure Digital Twins`. Sélectionnez l’API **Azure Digital Twins**.

        [![Rechercher Azure Digital Twins dans l’API](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Vous pouvez également rechercher `Azure Smart Spaces Service`. Sélectionnez l’API **Azure Smart Spaces Service**.

        [![Rechercher Azure Smart Spaces dans l’API](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Le nom et l’ID de l’API Azure AD qui s’affichent dépendent de votre locataire :
    > * Les comptes de locataire et client utilisés pour les tests doivent rechercher `Azure Digital Twins`.
    > * Les autres comptes Microsoft doivent rechercher `Azure Smart Spaces Service`.

1. L’API sélectionnée apparaît sous la forme **Azure Digital Twins** dans le même volet **Demander des autorisations d’API**. Sélectionnez la liste déroulante **Read (1)** (Lire (1)), puis activez la case à cocher **Read.Write** (Lire.Écrire). Sélectionnez le bouton **Ajouter des autorisations**.

    [![Ajouter des autorisations d’API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Selon les paramètres de votre organisation, vous devrez peut-être prendre des mesures supplémentaires pour autoriser l’accès de l’administrateur à cette API. Contactez votre administrateur pour obtenir plus d’informations. Une fois l’accès administrateur approuvé, la colonne **CONSENTEMENT ADMINISTRATEUR REQUIS** du volet **API autorisées** s’affichera comme suit pour vos API :

    [![Ajouter des autorisations d’API](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)


1. Sélectionnez **Manifeste** pour ouvrir le manifeste d’application pour votre application. Définissez *oauth2AllowImplicitFlow* sur `true`.

    [![Flux implicite Azure Active Directory](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Configurez une **URL de réponse** sur `https://www.getpostman.com/oauth2/callback`.

    [![URL de réponse Azure Active Directory](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Copiez et conservez l’**ID d’application** de votre application Azure Active Directory. Vous l’utiliserez lors des étapes qui suivent.

   [![ID d’application Azure Active Directory](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>Obtenir un jeton OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Configurez Postman pour obtenir un jeton Azure Active Directory. Après quoi, adressez une requête HTTP authentifiée à Azure Digital Twins en utilisant le jeton acquis :

1. Accédez à [www.getpostman.com](https://www.getpostman.com/) pour télécharger l’application.
1. Vérifiez que votre **URL d’autorisation** est correcte. Elle doit prendre la forme suivante :

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nom  | Remplacer par | Exemples |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nom de votre locataire ou organisation | `microsoft` |

1. Sélectionnez l’onglet **Autorisation**, **OAuth 2.0**, puis **Get New Access Token** (Obtenir un nouveau jeton d’accès).

    | Champ  | Valeur |
    |---------|---------|
    | Type d’autorisation | `Implicit` |
    | URL de rappel | `https://www.getpostman.com/oauth2/callback` |
    | URL d’authentification | Utilisez l’**URL d’autorisation** de l’**étape 2**. |
    | ID client | Utilisez l’**ID d’application** de l’application Azure Active Directory qui a été créée ou réutilisée dans la section précédente. |
    | Étendue | Laisser vide |
    | State | Laisser vide |
    | Authentification du client | `Send as Basic Auth header` |

1. Le client doit maintenant se présenter comme suit :

    [![Exemple de client Postman](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Sélectionnez **Jeton de demande**.

    >[!TIP]
    >Si vous recevez le message d’erreur « OAuth 2 couldn’t be completed » (Impossible d’exécuter OAuth 2), essayez ce qui suit :
    > * Fermez Postman, rouvrez-le et réessayez.
  
1. Faites défiler la page et sélectionnez **Use Token** (Utiliser le jeton).

## <a name="make-a-multipart-post-request"></a>Effectuer une requête POST multipart

Après avoir effectué les étapes précédentes, configurez Postman pour effectuer une requête HTTP POST authentifiée multipart :

1. Sous l’onglet **En-tête**, ajoutez une clé d’en-tête de requête HTTP **Content-Type** avec la valeur `multipart/mixed`.

   [![Type de contenu multipart/mixed](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Sérialisez les données non textuelles dans des fichiers. Les données JSON seraient enregistrées dans un fichier JSON.
1. Sous l’onglet **Corps**, sélectionnez `form-data`. 
1. Ajoutez chaque fichier en attribuant un nom de **clé** et en sélectionnant `file`.
1. Ensuite, sélectionnez chaque fichier à l’aide du bouton **Choisir un fichier**.

   [![Exemple de client Postman](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Le client Postman n’exige pas que les segments multipart aient un **Content-Type** ou **Content-Disposition** affecté manuellement.
   > * Vous n’avez pas besoin de spécifier ces en-têtes pour chaque partie.
   > * Vous devez sélectionner `multipart/mixed` ou un autre **Content-Type** approprié pour l’ensemble de la requête.

1. Pour finir, sélectionnez **Envoyer** pour envoyer votre requête HTTP POST multipart.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir sur les API de gestion Digital Twins et savoir comment les utiliser, consultez [Utiliser les API de gestion Azure Digital Twins](how-to-navigate-apis.md).

- Utilisez des requêtes multipart pour [ajouter des objets blob à des entités Azure Digital Twins](./how-to-add-blobs.md).

- Pour savoir comment vous authentifier auprès des API de gestion, consultez [S’authentifier auprès des API](./security-authenticating-apis.md).
