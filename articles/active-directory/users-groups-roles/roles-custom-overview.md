---
title: Afficher un aperçu des rôles d’administrateur Azure avec des autorisations personnalisables - Azure Active Directory | Microsoft Docs
description: Affichez un aperçu des rôles Azure AD personnalisés pour la délégation de la gestion des identités. Gérez les rôles Azure dans le Portail Azure, PowerShell ou l’API Graph.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c26e77fdf6e10e53f5acc0271986700c98fa690d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772550"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Rôles d’administrateurs personnalisés Azure Active Directory (préversion)

Cet article explique comment comprendre les rôles personnalisés Azure AD dans Azure Active Directory (Azure AD) avec le contrôle d’accès basé sur les rôles et les étendues de ressources. Les rôles personnalisés Azure AD regroupent les autorisations sous-jacentes des [rôles intégrés](directory-assign-admin-roles.md), ce qui vous permet de créer et d’organiser vos propres rôles personnalisés. Cette approche vous permet d’accorder l’accès de manière plus granulaire que les rôles intégrés, si nécessaire. La première version des rôles Azure AD personnalisés inclut la possibilité de créer un rôle pour attribuer des autorisations de gestion des inscriptions d’applications. Au fil du temps, des autorisations supplémentaires pour les ressources de l’organisation, telles que les applications d’entreprise, les utilisateurs et les appareils, sont ajoutées.  

En outre, les rôles personnalisés Azure AD prennent en charge les affectations par ressource en plus des affectations plus traditionnelles à l’échelle de l’organisation. Cette approche vous offre la possibilité d’accorder l’accès pour gérer certaines ressources (par exemple, une inscription d’application) sans accorder l’accès à toutes les ressources (toutes les inscriptions d’applications).

Le contrôle d’accès en fonction du rôle Azure AD est une fonctionnalité en préversion publique d’Azure AD et est disponible avec n’importe quel plan de licence Azure AD payant. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Présentation du contrôle d’accès en fonction du rôle Azure AD

L’octroi d’une autorisation à l’aide de rôles Azure AD personnalisés est un processus en deux étapes qui implique la création d’une définition de rôle personnalisé, puis son affectation à l’aide d’une attribution de rôle. Une définition de rôle personnalisé est une collection d’autorisations que vous ajoutez à partir d’une liste prédéfinie. Il s’agit des mêmes autorisations que celles utilisées dans les rôles intégrés.  

Une fois que vous avez créé votre définition de rôle, attribuez-la à un utilisateur en créant une attribution de rôle. Une attribution de rôle accorde à l’utilisateur les autorisations dans une définition de rôle au niveau d’une étendue spécifiée. Ce processus en deux étapes vous permet de créer une définition de rôle unique et de l’attribuer plusieurs fois à différentes étendues. Une étendue définit l’ensemble des ressources Azure AD auxquelles le membre du rôle a accès. L’étendue la plus courante est l’étendue à l’échelle de l’organisation (échelle-org). Un rôle personnalisé peut être attribué à une étendue d’échelle de l’organisation, ce qui signifie que le membre du rôle dispose des autorisations de rôle sur toutes les ressources de l’organisation. Un rôle personnalisé peut également être attribué au niveau de l’étendue d’objet. Un exemple d’étendue d’objet est une application unique. Le même rôle peut être attribué à un utilisateur sur toutes les applications de l’organisation, puis à un autre utilisateur avec une étendue limitée à l’application Contoso Expense Reports.  

Les rôles personnalisés et intégrés Azure AD utilisent des concepts similaires au [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). La [différence entre ces deux systèmes de contrôle d’accès en fonction du rôle](../../role-based-access-control/rbac-and-directory-admin-roles.md) est qu’Azure RBAC contrôle l’accès aux ressources Azure, telles que les machines virtuelles ou le stockage à l’aide de la gestion des ressources Azure, et que les rôles personnalisés Azure AD contrôlent l’accès aux ressources Azure AD à l’aide de l’API Graph. Les deux systèmes utilisent le concept de définition de rôle et d’attribution de rôle.

### <a name="role-assignments"></a>Affectations de rôles

Une attribution de rôle est l’objet qui associe une définition de rôle à un utilisateur dans une étendue particulière pour accorder l’accès aux ressources Azure AD. La création d’une attribution de rôle permet d’accorder un accès, qui peut être révoqué par la suppression d’une attribution de rôle. À son cœur, une attribution de rôle se compose de trois éléments :

- Utilisateur (une personne disposant d’un profil utilisateur dans Azure Active Directory)
- Définition de rôle
- Étendue des ressources

Vous pouvez [créer des attributions de rôles](roles-create-custom.md) à l’aide du portail Azure, d’Azure AD PowerShell ou de l’API Graph. Vous pouvez également [Afficher les attributions d’un rôle personnalisé](roles-view-assignments.md#view-the-assignments-of-a-role).

Le diagramme suivant montre un exemple d’attribution de rôle. Dans cet exemple, Chris Green s’est vu attribuer le rôle personnalisé d’administrateur d’inscription des applications dans l’étendue de l’inscription de l’application Contoso Widget Builder. Cette attribution accorde à Chris les autorisations du rôle administrateur d’inscription d’application sur cette inscription d’application spécifique uniquement.

![L’attribution de rôle est la manière dont les autorisations sont appliquées et comporte trois parties.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de sécurité

Un principal de sécurité représente l’utilisateur auquel l’accès aux ressources Azure AD doit être attribué. Un *utilisateur* est une personne disposant d’un profil utilisateur dans Azure Active Directory.

### <a name="role"></a>Role

Une définition de rôle, ou rôle, est une collection d’autorisations. Une définition de rôle répertorie les opérations qui peuvent être effectuées sur des ressources Azure AD, telles que créer, lire, mettre à jour et supprimer. Il existe deux types de rôles dans Azure AD :

- Les rôles intégrés créés par Microsoft qui ne peuvent pas être modifiés.
- Rôles personnalisés créés et gérés par votre organisation.

### <a name="scope"></a>Étendue

Une étendue est la restriction des actions autorisées à une ressource Azure AD particulière dans le cadre d’une attribution de rôle. Lorsque vous attribuez un rôle, vous pouvez spécifier une étendue qui limite l’accès de l’administrateur à une ressource spécifique. Par exemple, si vous souhaitez accorder à un développeur un rôle personnalisé, mais uniquement pour gérer une inscription d’application spécifique, vous pouvez inclure l’inscription d’application spécifique en tant qu’étendue dans l’attribution de rôle.

  > [!Note]
  > Des rôles personnalisés peuvent être attribués au niveau de l’étendue du répertoire et des ressources dans l’étendue. Ils ne peuvent pas encore être attribués au niveau de l’étendue de l’unité administrative.
  > Les rôles intégrés peuvent être attribués à l’étendue du répertoire et, dans certains cas, à l’étendue de l’unité administrative. Ils ne peuvent pas encore être affectés à l’étendue des ressources Azure AD.

## <a name="required-license-plan"></a>Plan de licence obligatoire

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Étapes suivantes

- Créer des attributions de rôles personnalisés à l’aide du [portail Azure, d’Azure AD PowerShell et de l’API Graph](roles-create-custom.md)
- [Afficher les attributions d’un rôle personnalisé](roles-view-assignments.md#view-assignments-of-single-application-scope)
