---
title: Créer un abonnement Azure supplémentaire | Microsoft Docs
description: Découvrez comment ajouter un nouvel abonnement Azure dans le Portail Azure.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: acb41de8344d1467e86c38c7c99e8ad4ab517ead
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375824"
---
# <a name="create-an-additional-azure-subscription"></a>Créer un abonnement Azure supplémentaire

Vous pouvez créer un abonnement supplémentaire pour votre compte de facturation [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Contrat Client Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou [Contrat Partenaire Microsoft](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) dans le portail Azure. Vous pouvez souhaiter avoir un abonnement supplémentaire pour ne pas dépasser les limites d’un abonnement, pour créer des environnements de sécurité distincts, ou pour isoler des données à des fins de conformité.

Si vous avez un compte de facturation Microsoft Online Service Program (MOSP), vous pouvez créer des abonnements supplémentaires dans le [portail d’inscription Azure](https://account.azure.com/signup?offer=ms-azr-0003p).

Pour plus d’informations sur les comptes de facturation et savoir comment identifier le type de votre compte de facturation, consultez [Afficher les comptes de facturation dans le portail Azure](billing-view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Autorisations requises pour créer des abonnements Azure

Pour créer des abonnements, vous avez besoin des autorisations suivantes :

|Compte de facturation  |Autorisation  |
|---------|---------|
|Contrat Entreprise (EA) |  Rôle Propriétaire du compte sur l’inscription au Contrat Entreprise. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](billing-understand-ea-roles.md).    |
|Contrat client Microsoft (MCA) |  Rôle Propriétaire ou Contributeur sur la section Factures, le profil de facturation ou le compte de facturation. Ou rôle Créateur de l’abonnement Azure dans la section Factures.  Pour plus d’informations, consultez [Rôles et tâches liés à la facturation des abonnements](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Contrat Partenaire Microsoft (MPA) |   Rôle Administrateur général ou Agent d’administration dans l’organisation partenaire. Pour plus d’informations, consultez [Espace partenaires - Affecter des rôles et des autorisations aux utilisateurs](https://docs.microsoft.com/partner-center/permissions-overview)  |

## <a name="create-a-subscription-in-the-azure-portal"></a>Créer un abonnement dans le Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez **Abonnements**.

   ![Capture d’écran montrant la recherche d’abonnements dans le portail](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Sélectionnez **Ajouter**.

   ![Capture d’écran montrant le bouton Ajouter dans l’affichage Abonnements](./media/billing-create-subscription/subscription-add.png)

1. Si vous avez accès à plusieurs comptes de facturation, sélectionnez celui pour lequel vous souhaitez créer l’abonnement.

1. Remplissez le formulaire et cliquez sur **Créer**. Les tableaux ci-dessous décrivent les champs du formulaire pour chaque type de compte de facturation.

**Contrat Entreprise**

|Champ  |Définition  |
|---------|---------|
|Nom     | Nom d’affichage qui vous aide à identifier facilement l’abonnement dans le portail Azure.  |
|Offre     | Sélectionnez EA Dev/Test si vous envisagez d’utiliser cet abonnement pour le développement ou pour tester les charges de travail. Sinon, choisissez Microsoft Azure Enterprise. Pour pouvoir créer des abonnements EA Dev/Test, vous devez activer l’offre DevTest pour votre compte d’inscription.|

**Contrat client Microsoft**

|Champ  |Définition  |
|---------|---------|
|Profil de facturation     | Les frais de votre abonnement sont facturés au profil de facturation que vous avez sélectionné. Si vous avez accès à un seul profil de facturation, la sélection est grisée.     |
|Section de facture     | Les frais de votre abonnement sont indiqués dans cette section de la facture du profil de facturation. Si vous avez accès à une seule section de facturation, la sélection est grisée.  |
|Planification     | Sélectionnez Plan Microsoft Azure pour Dev/Test si vous envisagez d’utiliser cet abonnement pour le développement ou pour tester les charges de travail. Sinon, choisissez Plan Microsoft Azure. Si une seule offre est activée pour le profil de facturation, la sélection est grisée.  |
|Nom     | Nom d’affichage qui vous aide à identifier facilement l’abonnement dans le portail Azure.  |

**Contrat Partenaire Microsoft**

|Champ  |Définition  |
|---------|---------|
|Client    | L’abonnement est créé pour le client que vous sélectionnez. Si vous n’avez qu’un seul client, la sélection est grisée.  |
|Reseller    | Revendeur qui fournira des services au client. Il s’agit d’un champ facultatif, qui s’applique uniquement aux fournisseurs indirects dans le modèle CSP à deux niveaux. |
|Nom     | Nom d’affichage qui vous aide à identifier facilement l’abonnement dans le portail Azure.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Créer programmatiquement un abonnement Azure supplémentaire

Vous pouvez également créer des abonnements supplémentaires par programmation. Pour plus d’informations, consultez [Créer des abonnements Azure par programmation](../azure-resource-manager/programmatically-create-subscription.md).

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter ou changer des administrateurs d’abonnements Azure](billing-add-change-azure-subscription-administrator.md)
- [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Créer des groupes d’administration pour l’organisation et l’administration des ressources](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Annuler votre abonnement Azure](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
