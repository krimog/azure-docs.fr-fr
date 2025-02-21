---
title: Exemples de transformations de revendications de comptes sociaux pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications de comptes sociaux pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cd4839e2c8ad6605a29f3c8b824375185384f78c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258143"
---
# <a name="social-accounts-claims-transformations"></a>Transformations de revendications de comptes sociaux

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans Azure Active Directory B2C (Azure AD B2C), les identités de comptes sociaux sont stockées dans un attribut `userIdentities` d’un type de revendication **alternativeSecurityIdCollection**. Chaque élément dans le type de revendication **alternativeSecurityIdCollection** spécifie l’émetteur (nom du fournisseur d’identité, tel que facebook.com), et l’`issuerUserId`, qui est un identificateur d’utilisateur unique pour l’émetteur.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Cet article fournit des exemples d’utilisation des transformations de revendications de comptes sociaux du schéma Infrastructure d’expérience d’identité dans Azure AD B2C. Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Crée une représentation JSON de la propriété alternativeSecurityId de l’utilisateur, qui peut être utilisée dans les appels à Azure Active Directory. Pour plus d’informations, voir le [schéma d’AlternativeSecurityId](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#alternativesecurityid-type).

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | ClaimType spécifiant l’identificateur d’utilisateur unique utilisé par le fournisseur d’identité sociale. |
| InputClaim | IdentityProvider | string | ClaimType spécifiant le nom du fournisseur d’identité de compte social, par exemple facebook.com. |
| OutputClaim | alternativeSecurityId | string | ClaimType généré après l’appel de l’élément ClaimsTransformation. Contient des informations sur l’identité d’un utilisateur de compte social. **issuer** est la valeur de la revendication `identityProvider`. **issuerUserId** est la valeur de la revendication `key` au format base64. |

Utilisez cette transformation de revendications pour générer un ClaimType `alternativeSecurityId`. Il est utilisé par tous les profils techniques de fournisseur de réseau social, tels que `Facebook-OAUTH`. La transformation de revendications suivante reçoit l’ID de compte de réseau social de l’utilisateur et le nom du fournisseur d’identité. La sortie de ce profil technique est une chaîne de format JSON utilisable dans des services d’annuaire Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **clé** : 12334
    - **identityProvider** : Facebook.com
- Revendications de sortie :
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Ajoute un `AlternativeSecurityId` à une revendication `alternativeSecurityIdCollection`.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | ClaimType à ajouter à la revendication de sortie. |
| InputClaim | collection | alternativeSecurityIdCollection | ClaimTypes utilisés par la transformation de revendications si disponible dans la stratégie. Si elle est fournie, la transformation de revendications ajoute l’`item` à la fin de la collection. |
| OutputClaim | collection | alternativeSecurityIdCollection | ClaimTypes générés après l’appel de cette ClaimsTransformation. Nouvelle collection contenant les éléments des entrées `collection` et `item`. |

L’exemple suivant lie une nouvelle identité sociale à un compte existant. Pour lier une nouvelle identité sociale :
1. Dans les profils techniques **AAD-UserReadUsingAlternativeSecurityId** et **AAD-UserReadUsingObjectId**, faites sortir la revendication **alternativeSecurityIds** de l’utilisateur.
1. Demandez à l’utilisateur à se connecter avec l’un des fournisseurs d’identité qui ne lui sont pas associés.
1. À l’aide de la transformation de revendications **CreateAlternativeSecurityId**, créez un type de revendication **alternativeSecurityId** nommé `AlternativeSecurityId2`.
1. Appelez la transformation de revendications **AddItemToAlternativeSecurityIdCollection** pour ajouter la revendication **AlternativeSecurityId2** à la revendication **AlternativeSecurityIds** existante.
1. Conservez la revendication **alternativeSecurityIds** sur le compte d’utilisateur.

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Revendications de sortie :
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Retourne la liste d’émetteurs de la revendication **alternativeSecurityIdCollection** dans un nouvelle revendication **stringCollection**.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Type de revendication à utiliser pour obtenir la liste des fournisseurs d’identité (émetteurs). |
| OutputClaim | identityProvidersCollection | stringCollection | ClaimTypes générés après l’appel de cette ClaimsTransformation. Liste des fournisseurs d’identité associés à la revendication d’entrée alternativeSecurityIdCollection. |

La transformation de revendications suivante lit la revendication **alternativeSecurityIds** de l’utilisateur et extrait la liste des noms de fournisseurs d’identité associés à ce compte. Utilisez la sortie **identityProvidersCollection** pour monter à l’utilisateur la liste des fournisseurs d’identité associés au compte. Ou bien, dans la page de sélection du fournisseur identité, filtrez la liste des fournisseurs d’identité en fonction de la revendication **identityProvidersCollection** sortie. L’utilisateur peut ainsi choisir de lier une nouvelle identité sociale qui n’est pas encore associée au compte.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Revendications d’entrée :
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Revendications de sortie :
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Supprime un **AlternativeSecurityId** d’une revendication **alternativeSecurityIdCollection**.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | IdentityProvider | string | ClaimType contenant le nom de fournisseur d’identité à retirer de la collection. |
| InputClaim | collection | alternativeSecurityIdCollection | ClaimTypes utilisés par la transformation de revendications. La transformation des revendications retire le fournisseur d’identité de la collection. |
| OutputClaim | collection | alternativeSecurityIdCollection | ClaimTypes générés après l’appel de cette ClaimsTransformation. Nouvelle collection, après suppression du fournisseur d’identité de la collection. |

L’exemple suivant dissocie l’une des identités sociales d’un compte existant. Pour dissocier une identité sociale :
1. Dans les profils techniques **AAD-UserReadUsingAlternativeSecurityId** et **AAD-UserReadUsingObjectId**, faites sortir la revendication **alternativeSecurityIds** de l’utilisateur.
2. Demandez à l’utilisateur de sélectionner le compte social à retirer de la liste des fournisseurs d’identité qui luis sont associés.
3. Appelez un profil technique de transformation de revendications qui appelle la transformation de revendications **RemoveAlternativeSecurityIdByIdentityProvider** qui a supprimé l’identité sociale sélectionnée, en utilisant le nom du fournisseur d’identité.
4. Conservez la revendication **alternativeSecurityIds** sur le compte d’utilisateur.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Revendications de sortie :
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
