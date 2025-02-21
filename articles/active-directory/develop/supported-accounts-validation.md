---
title: Différences de validation par types de comptes pris en charge (SignInAudience) - Plateforme d’identités Microsoft / Azure Active Directory
description: Découvrez plus en détail les différences de validation de certaines propriétés pour différents types de comptes pris en charge lors de l’inscription de votre application auprès de la plateforme d’identités Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bea6e3ea93e8b630970a8d86fc246eddaf3f56c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392052"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Différences de validation par types de comptes pris en charge (signInAudience)

Lors de l’inscription d’une application auprès de la plateforme d’identités Microsoft pour les développeurs, vous êtes invité à sélectionner les types de comptes pris en charge par votre application. Dans l’objet d’application et le manifeste, cette propriété est `signInAudience`.

Les options disponibles sont les suivantes :

- *AzureADMyOrg* - Uniquement les comptes dans l’annuaire de l’organisation où l’application est inscrite (monolocataire)
- *AzureADMultipleOrgs* - Comptes dans un annuaire d’organisation (multilocataire)
- *AzureADandPersonalMicrosoftAccount* - Comptes dans un annuaire d’organisation (multilocataire) et comptes Microsoft personnels (par exemple, Skype, Xbox et Outlook.com)

Pour les applications inscrites, vous pouvez trouver la valeur des types de comptes pris en charge dans la section **Authentification** d’une application. Vous pouvez également la trouver sous la propriété `signInAudience` dans le **Manifeste**.

La valeur que vous sélectionnez pour cette propriété a des implications sur d’autres propriétés de l’objet d’application. Par conséquent, si vous changez cette propriété, vous devrez peut-être d’abord changer d’autres propriétés.

Consultez le tableau suivant pour connaître les différences de validation de certaines propriétés pour différents types de comptes pris en charge.

| Propriété | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| URI d’ID d’application (`identifierURIs`)  | Doit être unique dans le locataire <br><br> Les schémas urn://sont pris en charge <br><br> Les caractères génériques ne sont pas pris en charge <br><br> Les fragments et les chaînes de requête sont pris en charge <br><br> Longueur maximale de 255 caractères <br><br> Aucune limite* sur le nombre d’identifierURIs  | elle doit être globalement unique <br><br> Les schémas urn:// sont pris en charge <br><br> Les caractères génériques ne sont pas pris en charge <br><br> Les fragments et les chaînes de requête sont pris en charge <br><br> Longueur maximale de 255 caractères <br><br> Aucune limite* sur le nombre d’identifierURIs | elle doit être globalement unique <br><br> Les schémas urn:// ne sont pas pris en charge <br><br> Les caractères génériques, les fragments et les chaînes de requête ne sont pas pris en charge <br><br> Longueur maximale de 120 caractères <br><br> Maximum de 50 identifierURIs |
| Certificats (`keyCredentials`) | Clé de signature symétrique | Clé de signature symétrique | Chiffrement et clé de signature asymétrique | 
| Secrets client (`passwordCredentials`) | Aucune limite* | Aucune limite* | Si liveSDK est activé : 2 secrets client maximum | 
| URI de redirection (`replyURLs`) | Consultez [Limitations et restrictions des URL de réponse/URI de redirection](reply-url.md) pour plus d’informations. | | | 
| Autorisations des API (`requiredResourceAccess`) | Aucune limite* | Aucune limite* | Maximum de 30 autorisations par ressource autorisée (par exemple, Microsoft Graph) | 
| Étendues définies par cette API (`oauth2Permissions`) | Longueur maximale du nom de l’étendue de 120 caractères <br><br> Aucune limite* sur le nombre de niveaux définis | Longueur maximale du nom de l’étendue de 120 caractères <br><br> Aucune limite* sur le nombre de niveaux définis |  Longueur maximale du nom de l’étendue de 40 caractères <br><br> Maximum de 100 étendues définies | 
| Applications clientes autorisées (`preautorizedApplications`) | Aucune limite* | Aucune limite* | Nombre total maximal de 500 <br><br> Maximum de 100 applications clientes définies <br><br> Maximum de 30 étendues définies par client | 
| appRoles | Pris en charge <br> Aucune limite* | Pris en charge <br> Aucune limite* | Non pris en charge | 
| URL de déconnexion | http://localhost est autorisé <br><br> Longueur maximale de 255 caractères | http://localhost est autorisé <br><br> Longueur maximale de 255 caractères | <br><br> https://localhost est autorisé, http://localhost échoue pour MSA <br><br> Longueur maximale de 255 caractères <br><br> Le schéma HTTP n’est pas autorisé <br><br> Les caractères génériques ne sont pas pris en charge | 

*Il existe une limite globale d’environ 1000 éléments sur l’ensemble des propriétés de collection de l’objet d’application

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[inscription des applications](app-objects-and-service-principals.md)
- En savoir plus sur le [manifeste de l'application](reference-app-manifest.md)
