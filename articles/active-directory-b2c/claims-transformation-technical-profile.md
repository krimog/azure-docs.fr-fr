---
title: Définir un profil technique de transformation de revendications dans une stratégie personnalisée dans Azure Active Directory B2C | Microsoft Docs
description: Définir un profil technique de transformation de revendications dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fd2f77200741c54d1ed10f0a0eec1da76462b0a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511613"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique de transformation de revendications dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profil technique de transformation de revendications permet d’appeler des transformation de revendications de sortie pour manipuler les valeurs de revendications, valider des revendications ou définir des valeurs par défaut pour un ensemble de revendications de sortie.

## <a name="protocol"></a>Protocole

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom qualifié complet d’Assembly de gestionnaire de protocole utilisé par Azure AD B2C : `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

L’exemple suivant montre un profil technique de transformation de revendications :

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** est obligatoire. Vous devez fournir au moins une revendication de sortie renvoyée par le profil technique. L’exemple suivant montre comment définir des valeurs par défaut dans les revendications de sortie :

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformations de revendications de sortie

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications ou en générer de nouvelles. Le profil technique suivant appelle la transformation de revendications **RemoveAlternativeSecurityIdByIdentityProvider**. Cette transformation de revendications un identification sociale de la collection **AlternativeSecurityIds**. Les revendications de sortie de ce profil technique sont **identityProvider2**, qui est défini sur `facebook.com`, et **AlternativeSecurityIds**, qui contient la liste des identités sociales associé à ce utilisateur après la suppression de l’identité facebook.com.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Le profil technique de transformation de revendications permet d’exécuter une transformation de revendications à partir de n’importe quelle étape d’orchestration du parcours de l’utilisateur. Dans l’exemple suivant, l’étape d’orchestration appelle un des profils techniques unlink, comme **UnLink-Facebook-OAUTH**. Ce profil technique appelle le profil technique de transformation de revendications **RemoveAlternativeSecurityIdByIdentityProvider**, ce qui génère une nouvelle revendication **AlternativeSecurityIds2** qui contient la liste des identités sociales de l’utilisateur, tout en supprimant l’identité Facebook des collections.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Utiliser un profil technique de validation

Un profil technique de transformation de revendications peut être utilisé pour valider des informations. Dans l’exemple suivant, le [profil technique autodéclaré](self-asserted-technical-profile.md) nommé **LocalAccountSignUpWithLogonEmail** demande à l’utilisateur d’entrer l’adresse e-mail à deux reprises, puis appelle le [profil technique de validation](validation-technical-profile.md) nommé **Validate-Email** pour valider les e-mails. Le profil technique **Validate-Email** appelle la transformation de revendications **AssertEmailAreEqual** pour comparer les deux revendications **email** et **emailRepeat**  et envoie une exception s’ils ne sont pas égaux selon la comparaison spécifiée.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Les profil technique de transformation de revendications appelle la transformation de revendications **AssertEmailAreEqual**, qui déclare que les e-mails fournis par l’utilisateur sont identiques.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Un profil technique autodéclaré peut appeler le profil technique de validation et afficher le message d’erreur spécifié dans les métadonnées **UserMessageIfClaimsTransformationStringsAreNotEqual**.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
