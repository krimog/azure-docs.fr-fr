---
title: Stratégies de protection de référence de l’accès conditionnel - Azure Active Directory
description: Stratégies de référence de l’accès conditionnel pour protéger les entreprises contre les attaques courantes
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd46b8cb734355a8394b7480c6def341cf9700d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430332"
---
# <a name="what-are-baseline-policies"></a>Que sont les stratégies de référence ?

Les stratégies de référence forment un ensemble de stratégies prédéfinies qui aident les organisations à se protéger contre de nombreuses attaques courantes. Ces attaques courantes peuvent inclure l’hameçonnage, la réinsertion et la pulvérisation de mot de passe. Les stratégies de référence sont disponibles dans toutes les éditions d’Azure AD. Microsoft propose ces stratégies de protection de référence à tout le monde, car les attaques basées sur l’identité ont connu une hausse au cours des dernières années. L’objectif de ces quatre stratégies est de veiller à ce que toutes les organisations aient un niveau de référence de sécurité activé sans aucun coût supplémentaire.  

La gestion de stratégies d’accès conditionnel personnalisé requiert une licence Azure AD Premium.

## <a name="baseline-policies"></a>Stratégies de base de référence

![Stratégies de référence d’accès conditionnel dans le portail Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Il existe quatre stratégies de référence :

* Exiger l’authentification multifacteur pour les administrateurs (préversion)
* Protection de l’utilisateur final (préversion)
* Bloquer l’authentification héritée (préversion)
* exiger l’authentification MFA pour le management des services (préversion)

Ces quatre stratégies de base influent sur les flux d’authentification hérités comme POP, IMAP ou clients Office pour ordinateur de bureau plus ancien.

### <a name="require-mfa-for-admins-preview"></a>Exiger l’authentification multifacteur pour les administrateurs (préversion)

En raison de l’importance et de l’accès de ces administrateurs de comptes, vous devez leur accorder une attention particulière. Une méthode courante pour améliorer la protection de comptes privilégiés consiste à demander une forme de vérification de compte plus stricte lorsqu’ils sont utilisés pour se connecter. Dans Azure Active Directory, vous pouvez obtenir une vérification plus stricte des comptes en demandant aux administrateurs de se procurer et d’utiliser une authentification multifacteur (MFA) Azure.

Exiger l’authentification multifacteur pour les administrateurs (préversion) est une stratégie de référence qui implique une authentification multifacteur (MFA) pour les rôles d’annuaire suivants, considérés comme les rôles d’Azure AD les plus privilégiés :

* Administrateur général
* Administrateur SharePoint
* Administrateur Exchange
* Administrateur de l’accès conditionnel
* Administrateur de sécurité
* Administrateur de mot de passe/Administrateur du support technique
* Administrateur de facturation
* Administrateur d’utilisateurs

Si votre organisation utilise ces comptes dans des scripts ou du code, envisagez de les remplacer par des [identités managées](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Protection de l’utilisateur final (préversion)

Les administrateurs hautement privilégiés ne sont pas les seuls ciblés lors d’attaques. Les malfaiteurs ont tendance à cibler les utilisateurs normaux. Après s’être introduits, ils peuvent demander l’accès à des informations confidentielles au nom du titulaire du compte d’origine, ou télécharger l’ensemble du répertoire et effectuer une attaque par hameçonnage dans toute l’organisation. Une méthode courante pour améliorer la protection de tous les utilisateurs finaux consiste à demander une forme de vérification de compte plus stricte lorsqu’une connexion risquée est détectée.

**Protection de l’utilisateur final (préversion)** est une stratégie de référence qui protège tous les utilisateurs dans un répertoire. L’activation de cette stratégie nécessite que tous les utilisateurs s’inscrive à l’authentification multifacteur Azure dans un délai de 14 jours. Une fois inscrits, les utilisateurs seront invités à effectuer l’authentification multifacteur uniquement lors de tentatives de connexion risquées. Les comptes d'utilisateur compromis sont bloqués jusqu'à la réinitialisation du mot de passe et l’élimination du risque. 

[!NOTE]
Tous les utilisateurs précédemment identifiés comme présentant un risque sont bloqués jusqu'à la réinitialisation du mot de passe et risquent d'être rejetés lors de l'activation de la stratégie.

### <a name="block-legacy-authentication-preview"></a>Bloquer l’authentification héritée (préversion)

Les protocoles d’authentification hérités (par exemple, IMAP, SMTP, POP3) sont normalement utilisés par les anciens clients de messagerie à des fins d’authentification. Les protocoles hérités ne prennent pas en charge l’authentification multifacteur. Même si vous avez une stratégie exigeant une authentification multifacteur pour votre répertoire, un malfaiteur peut s’authentifier à l’aide d’un de ces protocoles hérités et contourner l’authentification multifacteur.

La meilleure façon de protéger votre compte de requêtes d’authentification malveillantes effectuées par les protocoles hérités est de les bloquer.

La stratégie de référence **Bloquer l’authentification héritée (préversion)** bloque les requêtes d’authentification établies en utilisant les protocoles hérités. Tous les utilisateurs doivent employer l’authentification moderne pour se connecter. Utilisées conjointement avec les autres stratégies de référence, les requêtes provenant des protocoles hérités seront bloquées. En outre, tous les utilisateurs devront employer l’authentification multifacteur chaque fois qu’elle sera nécessaire. Cette stratégie ne bloque pas Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Exiger l’authentification multifacteur pour le management des services (préversion)

Les organisations utilisent divers services Azure et les gèrent à partir d’outils reposant sur Azure Resource Manager :

* Portail Azure
* Azure PowerShell
* D’Azure CLI

Le fait d’utiliser ces outils pour gérer des ressources est une action impliquant des privilèges élevés. Ces outils peuvent modifier les configurations à l’échelle de l’abonnement, telles que les paramètres de service et la facturation de l’abonnement.

Pour protéger les actions privilégiées, la stratégie **Exiger l’authentification multifacteur pour le management des services (préversion)** nécessite l’authentification multifacteur pour tout utilisateur accédant au portail Azure, à Azure PowerShell ou à Azure CLI.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
