---
title: Définir un profil technique OAuth1 dans une stratégie personnalisée dans Azure Active Directory B2C | Microsoft Docs
description: Définir un profil technique OAuth1 dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 97fa5757f8b77e29545f6d6f6b885334c7b526f1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063988"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique OAuth1 dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) prend en charge le fournisseur d’identité du [protocole OAuth 1.0](https://tools.ietf.org/html/rfc5849). Cet article décrit les caractéristiques d’un profil technique permettant d’interagir avec un fournisseur de revendications qui prend en charge ce protocole normalisé. Un profil technique OAuth1 vous permet d’opérer une fédération avec un fournisseur d’identité basée sur OAuth1, tel que Twitter. Fédérer avec le fournisseur d’identité permet aux utilisateurs de se connecter avec leurs identités existantes de réseaux sociaux ou d’entreprise.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `OAuth1`. Par exemple, le protocole pour profil technique **Twitter-OAUTH1** est `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Revendications d’entrée

Les éléments **InputClaims** et **InputClaimsTransformations** sont vides ou absents.

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications retournée par le fournisseur d'identité OAuth1. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d'identité. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité, pour autant que vous définissiez l’attribut **DefaultValue**.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

L’exemple suivant montre les revendications retournées par le fournisseur d’identité Twitter  :

- La revendication **user_id** mappée à la revendication **issuerUserId**.
- La revendication **screen_name** mappée à la revendication **displayName**.
- La revendication **email** sans mappage de nom.

Le profil technique retourne également des revendications qui ne sont pas retournées par le fournisseur d’identité :

- La revendication **identityProvider** contenant le nom du fournisseur d’identité.
- La revendication **authenticationSource** avec la valeur par défaut `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | OUI | Identificateur d’application du fournisseur d’identité. |
| ProviderName | Non | Nom du fournisseur d'identité. |
| request_token_endpoint | OUI | URL du point de terminaison de jeton de requête conformément à la norme RFC 5849. |
| authorization_endpoint | OUI | URL du point de terminaison d’autorisation conformément à la norme RFC 5849. |
| access_token_endpoint | OUI | URL du point de terminaison de jeton conformément à la norme RFC 5849. |
| ClaimsEndpoint | Non | URL du point de terminaison d’informations utilisateur. |
| ClaimsResponseFormat | Non | Format de réponse des revendications.|

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_secret | OUI | Clé secrète client de l’application du fournisseur d’identité.   |

## <a name="redirect-uri"></a>URI de redirection

Lorsque vous configurez l’URL de redirection de votre fournisseur d’identité, entrez `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Veillez à remplacer **{tenant}** par le nom de votre locataire (par exemple, contosob2c.onmicrosoft.com), et **{policyId}** par l’identificateur de votre stratégie (par exemple, b2c_1_policy). L’URI de redirection doit être en minuscules. Ajoutez un URI de redirection pour toutes les stratégies qui utilisent la connexion de fournisseur d’identité.

Si vous utilisez le domaine **b2clogin.com** à la place de **login.microsoftonline.com**, veillez à utiliser b2clogin.com au lieu de login.microsoftonline.com.

Exemples :

- [Ajouter Twitter en tant que fournisseur d’identité OAuth1 à l’aide de stratégies personnalisées](active-directory-b2c-custom-setup-twitter-idp.md)













