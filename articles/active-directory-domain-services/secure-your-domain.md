---
title: Sécuriser Azure AD Domain Services | Microsoft Docs
description: Découvrez comment désactiver les chiffrements faibles, les anciens protocoles et la synchronisation de hachage de mot de passe pour un domaine managé Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: 6d60663e345f297246a423e1b4e7a4fb45f352a5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754334"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Désactiver les chiffrements faibles et la synchronisation de hachage de mot de passe pour sécuriser un domaine managé Azure AD Domain Services

Par défaut, Azure Active Directory Domain Services (Azure AD DS) permet l’utilisation de chiffrements comme NTLM v1 et TLS v1. Certaines applications héritées peuvent avoir besoin de ces chiffrements, mais étant considérés comme faibles, vous pouvez les désactiver si vous n’en avez pas l’utilité. Si vous disposez d’une connectivité hybride locale reposant sur Azure AD Connect, vous pouvez aussi désactiver la synchronisation des hachages de mot de passe NTLM.

Cet article vous montre comment désactiver les chiffrements NTLM  1 et TLS  1 ainsi que la synchronisation de hachage de mot de passe NTLM.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des ressources suivantes :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, [créez et configurez une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Installez et configurez Azure PowerShell.
    * Si nécessaire, suivez les instructions pour [installer le module Azure PowerShell et vous connecter à votre abonnement Azure](/powershell/azure/install-az-ps).
    * Veillez à vous connecter à votre abonnement Azure à l’aide de l’applet de commande [Connect-AzAccount][Connect-AzAccount].
* Installez et configurez Azure AD PowerShell.
    * Si nécessaire, suivez les instructions pour [installer le module Azure AD PowerShell et vous connecter à Azure AD](/powershell/azure/active-directory/install-adv2).
    * Veillez à vous connecter à votre locataire Azure AD à l’aide de l’applet de commande [Connect-AzureAD][Connect-AzureAD].

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Désactiver les chiffrements faibles et la synchronisation de hachage de mot de passe NTLM

Pour désactiver les suites de chiffrement faible et la synchronisation de hachage d’informations d’identification NTLM, connectez-vous à votre compte Azure, puis obtenez la ressource Azure AD DS à l’aide de l’applet de commande [Get-AzResource][Get-AzResource] :

> [!TIP]
> Si vous obtenez une erreur avec la commande [Get-AzResource][Get-AzResource] indiquant que la ressource *Microsoft.AAD/DomainServices* n’existe pas, [élevez votre accès pour gérer tous les abonnements et groupes d’administration Azure][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Ensuite, définissez *DomainSecuritySettings* pour configurer les options de sécurité suivantes :

1. Désactivez la prise en charge de NTLM v1.
2. Désactiver la synchronisation des hachages de mots de passe NTLM à partir de votre instance Active Directory locale.
3. Désactivez TLS v1.

> [!IMPORTANT]
> Les utilisateurs et les comptes de service ne peuvent pas établir de liaisons simples LDAP si vous désactivez la synchronisation de hachage de mot de passe NTLM dans votre domaine managé Azure AD DS. Si vous devez établir des liaisons simples LDAP, ne définissez pas l’option de configuration de sécurité *"SyncNtlmPasswords"="Disabled";* dans la commande suivante.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Enfin, appliquez les paramètres de sécurité définis au domaine managé Azure AD DS à l’aide de l’applet de commande [Set-AzResource][Set-AzResource]. Spécifiez la ressource Azure AD DS de la première étape ainsi que les paramètres de sécurité de l’étape précédente.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

L’application des paramètres de sécurité au domaine managé Azure AD DS prend quelques instants.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le processus de synchronisation, consultez [Synchronisation des objets et des informations d’identification dans un domaine managé Azure AD Domain Services][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD