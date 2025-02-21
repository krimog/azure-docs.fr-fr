---
title: Utiliser des modèles de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure
description: Utilisez des modèles de démarrage rapide Azure pour créer le cluster de basculement Windows, y joindre des machines virtuelles SQL Server, créer l’écouteur et configurer l’équilibreur de charge interne dans Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2b44b51da11bc1c51fcbc60992a9b5b870daf02e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100576"
---
# <a name="use-azure-quickstart-templates-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Utiliser des modèles de démarrage rapide Azure pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure
Cet article explique comment utiliser les modèles de démarrage rapide Azure pour automatiser partiellement le déploiement d’une configuration de groupe de disponibilité Always On pour des machines virtuelles SQL Server dans Azure. Deux modèles de démarrage rapide Azure sont utilisés dans ce processus : 

   | Modèle | Description |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crée le cluster de basculement Windows et joint les machines virtuelles SQL Server à ce dernier. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crée l’écouteur de groupe de disponibilité et configure l’équilibreur de charge interne. Ce modèle peut être utilisé seulement si le cluster de basculement Windows a été créé avec le modèle **101-sql-vm-ag-setup**. |
   | &nbsp; | &nbsp; |

D’autres étapes de la configuration du groupe de disponibilité sont obligatoirement manuelles, notamment la création du groupe de disponibilité et celle de l’équilibreur de charge interne. Cet article décrit la séquence des étapes automatisées et manuelles.
 

## <a name="prerequisites"></a>Prérequis 
Pour automatiser la configuration d’un groupe de disponibilité Always On à l’aide de modèles de démarrage rapide, vous devez avoir les prérequis suivants : 
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Un groupe de ressources avec un contrôleur de domaine. 
- Une ou plusieurs [machines virtuelles jointes à un domaine dans Azure qui exécutent SQL Server 2016 (ou version ultérieure) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision), qui se trouvent dans le même groupe à haute disponibilité ou la même zone de disponibilité et qui sont [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Deux adresses IP disponibles (non utilisées par une entité) : une pour l’équilibreur de charge interne et une pour l’écouteur de groupe de disponibilité dans le même sous-réseau que le groupe de disponibilité. Si un équilibreur de charge existant est utilisé, une seule adresse IP disponible suffit.  

## <a name="permissions"></a>Autorisations
Les autorisations suivantes sont nécessaires pour configurer le groupe de disponibilité Always On à l’aide de modèles de démarrage rapide Azure : 

- Un compte d’utilisateur de domaine existant qui dispose d’une autorisation de **création d’objet ordinateur** dans le domaine.  Par exemple, un compte d’administrateur de domaine dispose généralement d’une autorisation suffisante (par exemple : account@domain.com). _Ce compte doit également faire partie du groupe administrateur local sur chaque machine virtuelle pour créer le cluster._
- Le compte d’utilisateur du domaine qui contrôle le service SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Étape 1 : Créer le cluster de basculement et joindre des machines virtuelles SQL Server au cluster à l’aide du modèle de démarrage rapide 
Une fois que vos machines virtuelles SQL Server ont été inscrites auprès du fournisseur de ressources de machine virtuelle SQL, vous pouvez joindre vos machines virtuelles SQL Server à *SqlVirtualMachineGroups*. Cette ressource définit les métadonnées du cluster de basculement Windows. Les métadonnées englobent la version, l’édition, le nom de domaine complet, les comptes Active Directory nécessaires à la gestion du cluster et du service SQL Server ainsi que le compte de stockage en tant que témoin cloud. 

L’ajout de machines virtuelles SQL Server au groupe de ressources *SqlVirtualMachineGroup* amorce le service de cluster de basculement Windows pour créer le cluster et joint ces machines virtuelles SQL Server à ce cluster. Cette étape est automatisée avec le modèle de démarrage rapide **101-sql-vm-ag-setup**. Vous pouvez l’implémenter en effectuant les étapes suivantes :

1. Accédez au modèle de démarrage rapide [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup). Ensuite, sélectionnez **Déployer sur Azure** pour ouvrir le modèle de démarrage rapide sur la portail Azure.
1. Complétez les champs obligatoires pour configurer les métadonnées du cluster de basculement Windows. Vous pouvez laisser les champs facultatifs vides.

   Le tableau suivant indique les valeurs nécessaires pour le modèle : 

   | **Champ** | Valeur |
   | --- | --- |
   | **Abonnement** |  Abonnement dans lequel vos machines virtuelles SQL Server existent. |
   |**Groupe de ressources** | Groupe de ressources où résident vos machines virtuelles SQL Server. | 
   |**Nom du cluster de basculement** | Nom que vous souhaitez attribuer à votre nouveau cluster de basculement Windows. |
   | **Liste des machines virtuelles existantes** | Machines virtuelles SQL Server que vous voulez faire participer au groupe de disponibilité et intégrer à ce nouveau cluster. Séparez ces valeurs d’une virgule et d’un espace (par exemple : *SQLVM1, SQLVM2*). |
   | **Version de SQL Server** | Version de SQL Server de vos machines virtuelles SQL Server. Sélectionnez-la dans la liste déroulante. Actuellement, seules les images SQL Server 2016 et SQL Server 2017 sont prises en charge. |
   | **Nom de domaine complet existant** | FQDN existant du domaine dans lequel se trouvent vos machines virtuelles SQL Server. |
   | **Compte de domaine existant** | Un compte d’utilisateur de domaine existant qui dispose de l’autorisation de **création d’objet ordinateur** dans le domaine en tant que [CNO](/windows-server/failover-clustering/prestage-cluster-adds) est créé pendant le déploiement du modèle. Par exemple, un compte d’administrateur de domaine dispose généralement d’une autorisation suffisante (par exemple : account@domain.com). *Ce compte doit également faire partie du groupe administrateur local sur chaque machine virtuelle pour créer le cluster.*| 
   | **Mot de passe du compte de domaine** | Mot de passe du compte d’utilisateur de domaine mentionné précédemment. | 
   | **Compte de service SQL existant** | Compte d’utilisateur de domaine qui contrôle le [service SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) pendant le déploiement de groupe de disponibilité (par exemple : account@domain.com). |
   | **Mot de passe du service SQL** | Mot de passe utilisé par le compte d’utilisateur de domaine qui contrôle le service SQL Server. |
   | **Nom du témoin cloud** | Nouveau compte de stockage Azure qui sera créé et utilisé pour le témoin cloud. Vous pouvez modifier ce nom. |
   | **\_Emplacement des artefacts** | Ce champ est défini par défaut et ne doit pas être modifié. |
   | **\_Jeton SAS de l’emplacement des artifacts** | Ce champ est laissé vide intentionnellement. |
   | &nbsp; | &nbsp; |

1. Si vous acceptez les termes et conditions, cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**. Sélectionnez ensuite **Acheter** pour terminer le déploiement du modèle de démarrage rapide. 
1. Pour superviser votre déploiement, sélectionnez le déploiement à partir de l’icône de cloche **Notifications** située dans la bannière de navigation du haut ou accédez à **Groupe de ressources** sur le portail Azure. Sélectionnez **Déploiements** sous **Paramètres**, puis choisissez le déploiement **Microsoft.Template**. 

>[!NOTE]
> Les informations d’identification fournies durant le déploiement du modèle sont stockées uniquement pour la durée du déploiement. À l’issue du déploiement, ces mots de passe sont supprimés. Vous serez invité à les fournir à nouveau si vous ajoutez d’autres machines virtuelles SQL Server au cluster. 


## <a name="step-2-manually-create-the-availability-group"></a>Étape 2 : Créer manuellement le groupe de disponibilité 
Créez manuellement le groupe de disponibilité comme vous le feriez normalement, en utilisant [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Ne créez *pas* d’écouteur à ce stade, car le modèle de démarrage rapide **101-sql-vm-aglistener-setup** s’en chargera à l’étape 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Étape 3 : Créer manuellement l’équilibreur de charge interne
L’écouteur de groupe de disponibilité Always On a besoin d’une instance interne d’Azure Load Balancer. L’équilibreur de charge interne fournit une adresse IP « flottante » pour l’écouteur de groupe de disponibilité afin d’accélérer les opérations de basculement et de reconnexion. Si les machines virtuelles SQL Server d’un groupe de disponibilité font partie du même groupe à haute disponibilité, vous pouvez utiliser un équilibreur de charge de base. Dans le cas contraire, vous devez utiliser un équilibreur de charge standard. 

> [!IMPORTANT]
> L’équilibreur de charge interne doit se trouver dans le même réseau virtuel que les instances de machine virtuelle SQL Server. 

Vous devez simplement créer l’équilibreur de charge interne. À l’étape 4, le modèle de démarrage rapide **101-sql-vm-aglistener-setup** gère le reste de la configuration (comme le pool backend, la sonde d’intégrité et les règles d’équilibrage de charge). 

1. Dans le portail Azure, ouvrez le groupe de ressources contenant les machines virtuelles SQL Server. 
2. Dans le groupe de ressources, sélectionnez **Ajouter**.
3. Recherchez **l’équilibrage de charge**. Dans les résultats de la recherche publiés par **Microsoft**, sélectionnez **Équilibrage de charge**.
4. Dans le panneau **Équilibrage de charge**, sélectionnez **Créer**.
5. Dans la boîte de dialogue **Créer l’équilibreur de charge**, configurez l’équilibreur de charge comme suit :

   | Paramètre | Valeur |
   | --- | --- |
   | **Nom** |Entrez un nom textuel qui représente l’équilibreur de charge. Par exemple, entrez **sqlLB**. |
   | **Type** |**Interne** : La plupart des implémentations utilisent un équilibreur de charge interne, ce qui permet aux applications du même réseau virtuel de se connecter au groupe de disponibilité.  </br> **Externe** : Permet aux applications de se connecter au groupe de disponibilité via une connexion Internet publique. |
   | **Réseau virtuel** | Sélectionnez le réseau virtuel contenant les instances de SQL Server. |
   | **Sous-réseau** | Sélectionnez le sous-réseau contenant les instances de SQL Server. |
   | **Affectation d’adresses IP** |**Statique** |
   | **Adresse IP privée** | Spécifiez une adresse IP disponible à partir du sous-réseau. |
   | **Abonnement** |Si vous avez plusieurs abonnements, ce champ doit normalement apparaître. Sélectionnez l’abonnement que vous voulez associer à cette ressource. Normalement, il s’agit du même abonnement que pour toutes les ressources du groupe de disponibilité. |
   | **Groupe de ressources** |Sélectionnez le groupe de ressources contenant les instances de SQL Server. |
   | **Lieu** |Sélectionnez l’emplacement Azure contenant les instances de SQL Server. |
   | &nbsp; | &nbsp; |

6. Sélectionnez **Create** (Créer). 


>[!IMPORTANT]
> La ressource d’adresse IP publique de chaque machine virtuelle SQL Server doit avoir une référence SKU standard compatible avec l’équilibreur de charge standard. Pour déterminer la référence SKU de la ressource d’adresse IP publique de votre machine virtuelle, accédez à **Groupe de ressources**, sélectionnez votre ressource **Adresse IP publique** pour la machine virtuelle SQL Server, puis recherchez la valeur sous **Référence SKU** dans le volet **Vue d’ensemble**. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Étape 4 : Créer l’écouteur de groupe de disponibilité et configurer l’équilibreur de charge interne à l’aide du modèle de démarrage rapide

Créez l’écouteur de groupe de disponibilité et configurez l’équilibreur de charge interne automatiquement à l’aide du modèle de démarrage rapide **101-sql-vm-aglistener-setup**. Le modèle provisionne la ressource Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Le modèle de démarrage rapide **101-sql-vm-aglistener-setup** effectue les actions suivantes par le biais du fournisseur de ressources de machine virtuelle SQL :

- Crée une nouvelle ressource IP front-end (selon la valeur d’adresse IP fournie pendant le déploiement) pour l’écouteur. 
- Configure les paramètres réseau du cluster et de l’équilibreur de charge interne. 
- Configure le pool backend de l’équilibreur de charge interne, la sonde d’intégrité et les règles d’équilibrage de charge.
- Crée l’écouteur de groupe de disponibilité avec le nom et l’adresse IP donnés.
 
>[!NOTE]
> Vous pouvez utiliser **101-sql-vm-aglistener-setup** uniquement si le cluster de basculement Windows a été créé avec le modèle **101-sql-vm-ag-setup**.
   
   
Pour configurer l’équilibreur de charge interne et créer l’écouteur de groupe de disponibilité, procédez comme suit :
1. Accédez au modèle de démarrage rapide [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) et sélectionnez **Déployer sur Azure** pour démarrer le modèle de démarrage rapide sur le portail Azure.
1. Complétez les champs obligatoires pour configurer l’équilibreur de charge interne, puis créez l’écouteur de groupe de disponibilité. Vous pouvez laisser les champs facultatifs vides. 

   Le tableau suivant indique les valeurs nécessaires pour le modèle : 

   | **Champ** | Valeur |
   | --- | --- |
   |**Groupe de ressources** | Groupe de ressources dans lequel vos machines virtuelles SQL Server et votre groupe de disponibilité existent. | 
   |**Nom du cluster de basculement existant** | Nom du cluster auquel vos machines virtuelles SQL Server sont jointes. |
   | **Groupe de disponibilité SQL existant**| Nom du groupe de disponibilité duquel vos machines virtuelles SQL Server font partie. |
   | **Liste des machines virtuelles existantes** | Noms des machines virtuelles SQL Server qui font partie du groupe de disponibilité précédemment mentionné. Séparez les noms d’une virgule et d’un espace (par exemple : *SQLVM1, SQLVM2*). |
   | **Écouteur** | Nom DNS que vous voulez attribuer à l’écouteur. Par défaut, ce modèle spécifie le nom « aglistener », mais vous pouvez le modifier. Le nom ne doit pas dépasser 15 caractères. |
   | **Port de l’écouteur** | Port que vous voulez que l’écouteur utilise. En règle générale, il doit s’agir du port par défaut, à savoir, 1433. C’est le numéro de port indiqué par le modèle. Mais si votre port par défaut a été modifié, le port de l’écouteur doit plutôt utiliser cette valeur modifiée. | 
   | **Adresse IP de l’écouteur** | Adresse IP que vous voulez que l’écouteur utilise. Cette adresse IP étant créée pendant le déploiement du modèle, indiquez-en une qui n’est pas déjà utilisée.  |
   | **Sous-réseau existant** | Nom du sous-réseau interne de vos machines virtuelles SQL Server (par exemple : *default*). Vous pouvez déterminer cette valeur en accédant à **Groupe de ressources**, en sélectionnant votre réseau virtuel, en choisissant **Sous-réseaux** dans le volet **Paramètres**, puis en copiant la valeur figurant sous **Nom**. |
   | **Équilibreur de charge interne existant** | Nom de l’équilibreur de charge interne que vous avez créé à l’étape 3. |
   | **Port de la sonde** | Port de la sonde que vous voulez que l’équilibreur de charge interne utilise. Le modèle utilise 59999 par défaut, mais vous pouvez modifier cette valeur. |
   | &nbsp; | &nbsp; |

1. Si vous acceptez les termes et conditions, cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**. Sélectionnez **Acheter** pour terminer le déploiement du modèle de démarrage rapide. 
1. Pour superviser votre déploiement, sélectionnez le déploiement à partir de l’icône de cloche **Notifications** située dans la bannière de navigation du haut ou accédez à **Groupe de ressources** sur le portail Azure. Sélectionnez **Déploiements** sous **Paramètres**, puis choisissez le déploiement **Microsoft.Template**. 

>[!NOTE]
>Si votre déploiement échoue en cours de route, vous devez [supprimer l’écouteur tout juste créé](#remove-the-availability-group-listener) manuellement à l’aide de PowerShell avant de redéployer le modèle de démarrage rapide **101-sql-vm-aglistener-setup**. 

## <a name="remove-the-availability-group-listener"></a>Supprimer l’écouteur de groupe de disponibilité
Si, par la suite, vous avez besoin de supprimer l’écouteur de groupe de disponibilité que le modèle a configuré, vous devez passer par le fournisseur de ressources de machine virtuelle SQL. Comme l’écouteur est inscrit par l’intermédiaire du fournisseur de ressources de machine virtuelle SQL, le supprimer via SQL Server Management Studio ne suffit pas. 

La meilleure méthode consiste à le supprimer par l’intermédiaire du fournisseur de ressources de machine virtuelle SQL en utilisant l’extrait de code suivant dans PowerShell. Cela a pour effet de supprimer les métadonnées de l’écouteur de groupe de disponibilité au niveau du fournisseur de ressources de machine virtuelle SQL. De même, l’écouteur de groupe de disponibilité est supprimé physiquement du groupe de disponibilité. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Erreurs courantes
Cette section décrit certains problèmes connus et leurs éventuelles solutions. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>L’écouteur de groupe de disponibilité existe déjà pour le groupe de disponibilité « \<Nom-Groupe-Disponibilité> »
Le groupe de disponibilité sélectionné utilisé dans le modèle de démarrage rapide Azure pour l’écouteur de groupe de disponibilité contient déjà un écouteur. Soit il se trouve physiquement dans le groupe de disponibilité, soit ses métadonnées sont conservées dans le fournisseur de ressources de machine virtuelle SQL. Supprimez l’écouteur à l’aide de [PowerShell](#remove-the-availability-group-listener) avant de redéployer le modèle de démarrage rapide **101-sql-vm-aglistener-setup**. 

### <a name="connection-only-works-from-primary-replica"></a>La connexion fonctionne uniquement à partir du réplica principal
Ce comportement est probablement lié à un échec de déploiement du modèle **101-sql-vm-aglistener-setup** qui a laissé la configuration de l’équilibreur de charge interne dans un état incohérent. Vérifiez que le pool back-end liste le groupe à haute disponibilité et qu’il existe des règles pour la sonde d’intégrité et l’équilibreur de charge. S’il manque quelque chose, la configuration de l’équilibreur de charge interne est dans un état incohérent. 

Pour corriger ce comportement, supprimez l’écouteur à l’aide de [PowerShell](#remove-the-availability-group-listener), supprimez l’équilibreur de charge interne via le portail Azure, puis recommencez à l’étape 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest : seule la liste des machines virtuelles SQL peut être mise à jour
Cette erreur peut se produire quand vous déployez le modèle **101-sql-vm-aglistener-setup** si l’écouteur a été supprimé via SQL Server Management Studio (SSMS), mais qu’il n’a pas été supprimé au niveau du fournisseur de ressources de machine virtuelle SQL. La suppression de l’écouteur via SSMS n’a pas pour effet de supprimer les métadonnées de l’écouteur au niveau du fournisseur de ressources de machine virtuelle SQL. L’écouteur doit être supprimé du fournisseur de ressources via [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>Le compte de domaine n’existe pas
Cette erreur peut avoir deux causes. Soit le compte de domaine spécifié n’existe pas, soit il manque les données de [Nom d’utilisateur principal (UPN)](/windows/desktop/ad/naming-properties#userprincipalname). Le modèle **101-sql-vm-ag-setup** attend un compte de domaine sous la forme UPN (c’est-à-dire, *user@domain.com* ), mais certains comptes de domaine peuvent ne pas contenir cette information. Cela se produit généralement quand un utilisateur local a été migré pour devenir le premier compte d’administrateur de domaine au moment où le serveur a été promu contrôleur de domaine ou quand un utilisateur a été créé via PowerShell. 

Vérifiez que le compte existe. Si c’est le cas, vous vous trouvez peut-être dans le deuxième cas. Pour corriger ce problème, procédez comme suit :

1. Sur le contrôleur de domaine, ouvrez la fenêtre **Utilisateurs et ordinateurs Active Directory** à partir de l’option **Outils** dans **Gestionnaire de serveur**. 
2. Accédez au compte en sélectionnant **Utilisateurs** dans le volet gauche.
3. Cliquez avec le bouton droit sur le compte et sélectionnez **Propriétés**.
4. Sélectionnez l’onglet **Compte**. Si la zone **Nom d’ouverture de session de l’utilisateur** est vide, il s’agit de la cause de votre erreur. 

    ![Un compte d’utilisateur vide indique un UPN manquant](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Complétez la zone **Nom d’ouverture de session de l’utilisateur** de sorte que ce nom corresponde à celui de l’utilisateur, puis sélectionnez le domaine approprié dans la liste déroulante. 
6. Sélectionnez **Appliquer** pour enregistrer vos modifications et fermez la boîte de dialogue en sélectionnant **OK**. 

Après avoir apporté ces modifications, essayez encore une fois de déployer le modèle de démarrage rapide Azure. 



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble des machines virtuelles SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) sur les machines virtuelles SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs pour les machines virtuelles SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication pour les machines virtuelles SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Changement de modèles de licence pour une machine virtuelle SQL Server](virtual-machines-windows-sql-ahb.md)



