---
title: Regrouper les comptes de service administrés pour Azure AD Domain Services | Microsoft Docs
description: Découvrez comment créer un compte de service administré de groupe (gMSA) pour une utilisation avec des domaines managés Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: 1cfddf14d60b7d73bae283a18732c7c99ae22b4d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898231"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Créer un compte de service administré de groupe (gMSA) dans Azure AD Domain Services

Les applications et les services ont souvent besoin d’une identité pour s’authentifier auprès d’autres ressources. Par exemple, un service web peut avoir besoin de s’authentifier auprès d’un service de base de données. Si une application ou un service possède plusieurs instances, comme une batterie de serveurs web, la création et la configuration manuelles des identités pour ces ressources prend beaucoup de temps. À la place, il est possible de créer un compte de service administré de groupe (gMSA) dans un domaine managé Azure Active Directory Domain Services (Azure AD DS). Le système d’exploitation Windows gère automatiquement les informations d’identification d’un gMSA, ce qui simplifie la gestion de grands groupes de ressources.

Cet article vous explique comment créer un gMSA dans un domaine managé Azure AD DS.

## <a name="before-you-begin"></a>Avant de commencer

Pour faire ce qui est décrit dans cet article, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, suivez le tutoriel pour [créer et configurer une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Une machine virtuelle de gestion Windows Server jointe au domaine managé Azure AD DS.
    * Si nécessaire, suivez le tutoriel [Créer une machine virtuelle de gestion][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Vue d’ensemble des comptes de service administrés

Un compte de service administré autonome (sMSA) est un compte de domaine dont le mot de passe est managé automatiquement. Cette approche simplifie la gestion des noms de principal du service (SPN) et permet de déléguer la gestion à d’autres administrateurs. Vous n’avez pas besoin de créer et de faire tourner manuellement les informations d’identification du compte.

Un compte de service administré de groupe (gMSA) offre la même simplification de la gestion, mais pour plusieurs serveurs du domaine. Un gMSA permet à toutes les instances d’un service hébergé sur une batterie de serveurs d’utiliser le même principal de service pour que les protocoles d’authentification mutuelle fonctionnent. Lorsqu’un compte de service administré de groupe (gMSA) est utilisé comme principal de service, le système d’exploitation Windows gère à nouveau le mot de passe du compte au lieu de compter sur l’administrateur.

Pour en savoir plus, consultez [Vue d’ensemble des comptes de service administrés de groupe (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Utilisation des comptes de service dans Azure AD DS

Comme les domaines managés par Azure AD DS sont verrouillés et managés par Microsoft, certaines considérations sont à prendre en compte lors de l’utilisation de comptes de service :

* Créez des comptes de service dans les unités d’organisation personnalisées d’un domaine managé.
    * Vous ne pouvez pas créer de compte de service dans les unités d’organisation intégrées *Utilisateurs AADDC* et *Ordinateurs AADDC*.
    * À la place, [Créez une unité d’organisation personnalisée][create-custom-ou] dans le domaine managé Azure AD DS, puis créez des comptes de service dans cette unité d’organisation.
* La clé racine des services de distribution de clés (KDS) est précréée.
    * La clé racine KDS est utilisée pour générer et récupérer des mots de passe pour les comptes de service administrés de groupe (gMSA). Dans Azure AD DS, la racine KDS est créée pour vous.
    * Vous n’avez pas les privilèges nécessaires pour en créer un autre ou afficher la clé racine KDS par défaut.

## <a name="create-a-gmsa"></a>Créer un compte de service administré de groupe (gMSA)

Tout d’abord, créez une unité d’organisation personnalisée à l’aide de la cmdlet [New-ADOrganizationalUnit][New-AdOrganizationalUnit]. Pour plus d’informations sur la création et la gestion d’unités d’organisation personnalisées, consultez [Unités d’organisation personnalisées dans Azure AD DS][create-custom-ou].

L’exemple suivant crée une unité d’organisation personnalisée nommée *myNewOU* dans le domaine managé Azure AD DS nommé *contoso.com*. Utilisez votre unité d’organisation et votre nom de domaine managé :

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

Créez maintenant un compte de service administré de groupe (gMSA) à l’aide de la cmdlet [New-ADServiceAccount][New-ADServiceAccount]. Les exemples de paramètres suivants sont définis :

* **-Name** est défini sur *WebFarmSvc*
* Le paramètre **-Path** spécifie l’unité d’organisation personnalisée pour le compte de service administré de groupe (gMSA) créé à l’étape précédente.
* Les entrées DNS et les noms du principal de service sont définis pour *WebFarmSvc.contoso.com*
* Les principaux dans *CONTOSO-SERVER$* sont autorisés à récupérer le mot de passe pour utiliser l’identité.

Spécifiez vos propres noms et noms de domaine.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
        http/WebFarmSvc.contoso.com/contoso, `
        http/WebFarmSvc/contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

Les applications et les services peuvent maintenant être configurés pour utiliser le compte de service administré de groupe (gMSA) en fonction des besoins.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les comptes de service administrés de groupe (gMSA), consultez [Prise en main des comptes de service administrés de groupe][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
