---
title: 'Résoudre les problèmes de gestion du runtime d’intégration SSIS dans Azure Data Factory '
description: Cet article explique comment résoudre les problèmes liés à la gestion du runtime d’intégration SSIS (SSIS IR).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 3452fc2274eb646acb19c0e6a203ebadcb81cad5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684029"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Résoudre les problèmes de gestion du runtime d’intégration SSIS dans Azure Data Factory

Cet article explique comment résoudre les problèmes liés à la gestion du runtime d’intégration SSIS (SSIS IR).

## <a name="overview"></a>Vue d'ensemble

Si vous rencontrez un problème lors du provisionnement ou de l’annulation du provisionnement du runtime d’intégration SSIS, un message d’erreur s’affiche dans le portail Microsoft Azure Data Factory ou une erreur est retournée par une cmdlet PowerShell. L’erreur est toujours présentée sous la forme d’un code d’erreur avec un message d’erreur détaillé.

Si le code d’erreur est InternalServerError, le service a des problèmes temporaires et vous devez retenter l’opération ultérieurement. Si la nouvelle tentative échoue, contactez l’équipe du support Azure Data Factory.

Sinon, 3 dépendances externes majeures peuvent entraîner des erreurs : un serveur Azure SQL Database ou une instance managée, un script d’installation personnalisée et une configuration de réseau virtuel.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problèmes relatifs au serveur Azure SQL Database ou à Azure SQL Database Managed Instance

Si vous provisionnez le runtime d’intégration SSIS avec la base de données du catalogue SSIS, vous avez besoin d’un serveur Azure SQL Database ou d’une instance managée. Le runtime d’intégration SSIS doit être capable d’accéder au serveur Azure SQL Database ou à l’instance managée. De plus, le compte du serveur Azure SQL Database ou de l’instance managée doit avoir l’autorisation de créer une base de données de catalogue SSIS (SSISDB). En cas d’erreur, un code d’erreur avec un message d’exception SQL détaillé s’affiche dans le portail Data Factory. Utilisez les informations dans la liste suivante pour résoudre les codes d’erreurs.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ce message d’erreur peut s’afficher lors du provisionnement d’un nouveau runtime d’intégration SSIS ou lors de l’exécution d’un runtime d’intégration. Si vous rencontrez cette erreur lors du provisionnement du runtime d’intégration, vous pouvez obtenir un message SqlException détaillé dans le message d’erreur qui indique l’un des problèmes suivants :

* Problème de connexion réseau. Vérifiez si le serveur SQL Server ou le nom d’hôte de l’instance managée est accessible. Vérifiez également qu’aucun pare-feu ni groupe de sécurité réseau ne bloque l’accès du runtime d’intégration SSIS au serveur.
* La connexion a échoué pendant l’authentification SQL. Le compte fourni ne peut pas se connecter à la base de données SQL Server. Vérifiez que le bon compte d’utilisateur a été fourni.
* La connexion a échoué lors de l’authentification Microsoft Azure Active Directory (Azure AD) (identité managée). Ajoutez l’identité managée de votre fabrique à un groupe AAD et vérifiez que l’identité a accès à votre serveur de base de données de catalogue.
* Expiration de la connexion. Cette erreur est toujours due à une configuration liée à la sécurité. Nous vous recommandons :
  1. Créez une machine virtuelle.
  1. Joignez la machine virtuelle au même réseau virtuel Microsoft Azure que le runtime d’intégration si ce dernier se trouve dans un réseau virtuel.
  1. Installez SSMS et vérifiez l’état du serveur Azure SQL Database ou de l’instance managée.

Pour les autres problèmes, corrigez le problème indiqué dans le message d’erreur d’exception SQL détaillé. Si vous rencontrez encore des problèmes, contactez l’équipe du support Azure SQL Database Managed Instance ou du support Azure SQL Database.

Si l’erreur s’affiche lorsque le runtime d’intégration est en cours d’exécution, les modifications du groupe de sécurité réseau ou du pare-feu empêchent probablement le nœud Worker du runtime d’intégration SSIS d’accéder au serveur Azure SQL Database ou à l’instance managée. Débloquez le nœud Worker du runtime d’intégration SSIS pour lui permettre d’accéder au serveur Azure SQL Database ou à l’instance managée.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Voici à quoi peut ressembler ce type de message d’erreur : « La base de données « SSISDB » a atteint son quota de taille. Partitionnez ou supprimez des données, supprimez des index ou consultez la documentation pour connaître les résolutions possibles. 

Voici les solutions possibles :
* Augmentez le quota de taille de votre base de données SSIS.
* Modifiez la configuration de la base de données SSIS pour réduire la taille, par exemple :
   * En raccourcissant la période de conservation et en réduisant le nombre de versions des projets
   * En raccourcissant la période de conservation des journaux.
   * En modifiant le niveau par défaut des journaux.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Cette erreur signifie que le serveur Azure SQL Database ou l’instance managée inclut déjà une base de données SSIS qui est utilisée par un autre runtime d’intégration. Vous devez fournir un autre serveur Azure SQL Database ou une autre instance managée, ou sinon, supprimer la base de données SSIS existante et redémarrer le nouveau runtime d’intégration.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ce problème peut se produire pour l’une des raisons suivantes :

* Le compte d’utilisateur configuré pour le runtime d’intégration SSIS n’est pas autorisé à créer la base de données. Vous pouvez autoriser l’utilisateur à créer la base de données.
* Le délai imparti expire lors de la création de la base de données, en raison par exemple de l’expiration d’un délai d’exécution ou d’opération de base de données. Vous devez retenter l’opération plus tard. Si la nouvelle tentative échoue, contactez l’équipe du support Azure SQL Database Managed Instance ou celle chargée des serveurs Azure SQL Database.

Pour les autres problèmes, reportez-vous aux informations détaillées du message d’erreur d’exception SQL et corrigez le problème signalé dans les détails de l’erreur. Si vous rencontrez encore des problèmes, contactez l’équipe du support Azure SQL Database Managed Instance ou du support Azure SQL Database.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Ce type de message d’erreur ressemble à ceci : « Nom d’objet non valide 'catalog.catalog_properties » Dans ce cas, soit vous avez déjà une base de données nommée SSISDB, mais celle-ci n’a pas été créée par le runtime d’intégration SSIS, soit l’état de la base de données n’est pas valide, en raison d’erreurs survenues lors du dernier provisionnement du runtime d’intégration SSIS. Vous pouvez supprimer une base de données existante portant le nom de SSISDB, ou configurer un nouveau serveur Azure SQL Database ou une nouvelle instance Azure SQL Database Managed Instance pour le runtime d’intégration.

## <a name="custom-setup-issues"></a>Problèmes lors de l’installation personnalisée

L’installation personnalisée fournit une interface permettant d’ajouter vos propres étapes d’installation lors du provisionnement ou de la reconfiguration de votre runtime d’intégration SSIS. Pour plus d’informations, consultez [Installation personnalisée du runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Vérifiez que votre conteneur inclut uniquement les fichiers nécessaires à l’installation personnalisée, car tous les fichiers du conteneur sont téléchargés sur le nœud Worker du runtime d’intégration SSIS. Nous vous recommandons de tester le script d’installation personnalisée sur un ordinateur local pour résoudre les problèmes d’exécution du script avant d’exécuter celui-ci dans le runtime d’intégration SSIS.

Le conteneur du script d’installation personnalisée est vérifié pendant l’exécution du runtime d’intégration, car le runtime d’intégration SSIS est régulièrement mis à jour. Cette mise à jour nécessite l’accès au conteneur pour télécharger le script d’installation personnalisée et l’installer à nouveau. Le processus vérifie également que le conteneur est accessible et que le fichier main.cmd existe.

Pour toute erreur impliquant une installation personnalisée, vous voyez un code d’erreur CustomSetupScriptFailure avec un sous-code tel que CustomSetupScriptBlobContainerInaccessible ou CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Cette erreur signifie que le runtime d’intégration SSIS ne peut pas accéder à votre conteneur d’objets blob Azure pour l’installation personnalisée. Vérifiez que l’URI SAS du conteneur est accessible et qu’il n’a pas expiré.

Arrêtez le runtime d’intégration s’il est en cours d’exécution, reconfigurez le runtime d’intégration avec le nouvel URI SAS du conteneur d’installation personnalisée, puis redémarrez le runtime d’intégration.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Cette erreur signifie que le runtime d’intégration SSIS ne trouve pas le script d’installation personnalisée (main. cmd) dans votre conteneur d’objets blob. Vérifiez que le fichier main.cmd se trouve dans le conteneur, qui est le point d’entrée de l’installation personnalisée.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Cette erreur signifie que l’exécution du script d’installation personnalisée (main.cmd) a échoué. Essayez le script sur votre ordinateur local ou vérifiez les journaux d’exécution de l’installation personnalisée dans votre conteneur d’objets blob.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Cette erreur signifie que le délai d’exécution du script d’installation personnalisée a expiré. Assurez-vous que votre script peut être exécuté en mode silencieux et qu’aucune entrée interactive n’est nécessaire et assurez-vous que votre conteneur blob contient uniquement les fichiers d’installation personnalisée nécessaires. Il est recommandé de commencer par tester le script sur l’ordinateur local. Vous devez également vérifier les journaux d’exécution de l’installation personnalisée dans votre conteneur d’objets blob. Le délai maximal pour l’installation personnalisée est actuellement de 45 minutes. Ce délai inclut le temps nécessaire au téléchargement de tous les fichiers de votre conteneur, ainsi que le temps nécessaire à leur installation sur le runtime d’intégration SSIS. Si un délai plus long est nécessaire, envoyez-nous un ticket de support.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Cette erreur signifie que la tentative de chargement des journaux d’exécution de l’installation personnalisée dans votre conteneur d’objets blob a échoué. Ce problème se produit soit parce que le runtime d’intégration SSIS n’a pas d’autorisations en écriture sur votre conteneur d’objets blob, soit en raison de problèmes de stockage ou de réseau. Si l’installation personnalisée réussit, cette erreur n’impacte pas le fonctionnement de SSIS, toutefois, les journaux seront manquants. Si l’installation personnalisée échoue avec une autre erreur et que le journal n’est pas chargé, nous signalons d’abord cette erreur afin que le journal puisse être chargé pour analyse. En outre, une fois ce problème résolu, nous signalons les éventuels problèmes plus spécifiques. Si ce problème n’est pas résolu après une nouvelle tentative, contactez l’équipe du support technique Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuration du réseau virtuel

Quand vous joignez le runtime d’intégration SSIS au réseau virtuel Azure, le runtime d’intégration SSIS utilise le réseau virtuel qui se trouve sous l’abonnement utilisateur. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

En cas de problème lié au réseau virtuel, une des erreurs suivantes s’affiche.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Cette erreur peut se produire pour diverses raisons. Pour la résoudre, consultez les sections [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) et [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Interdit

Ce type d’erreur peut ressembler à ceci : « subnetId n’est pas activé pour le compte actuel. Le fournisseur de ressources Microsoft.Batch n’est pas inscrit dans le même abonnement de réseau virtuel ».

Ces détails signifient qu’Azure Batch ne peut pas accéder à votre réseau virtuel. Inscrivez le fournisseur de ressources Microsoft.Batch dans le même abonnement que le réseau virtuel.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Ce type d’erreur peut ressembler à ce qui suit : 

- « Le réseau virtuel spécifié n’existe pas ou le service Batch ne peut pas y accéder ».
- « Le sous-réseau spécifié xxx n’existe pas ».

Ces erreurs signifient que le réseau virtuel n’existe pas, que le service Azure Batch ne peut pas y accéder ou que le sous-réseau fourni n’existe pas. Vérifiez que le réseau virtuel et le sous-réseau existent et qu’Azure Batch peut y accéder.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Ce type de message d’erreur ressemble à ceci : « Échec du provisionnement du runtime d’intégration dans le réseau virtuel. Si les paramètres du serveur DNS ou du groupe de sécurité réseau sont configurés, vérifiez que le serveur DNS est accessible et que le groupe de sécurité réseau est correctement configuré ».

Dans cette situation, vous avez probablement une configuration personnalisée du serveur DNS ou des paramètres de groupe de sécurité réseau qui empêche la résolution du nom du serveur Azure nécessaire au runtime d’intégration SSIS ou qui empêche son accès. Pour plus d'informations, consultez [Configuration du réseau virtuel de runtime d’intégration SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si vous rencontrez toujours des problèmes, contactez l’équipe du support technique Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Le runtime d’intégration SSIS est automatiquement mis à jour régulièrement. Un nouveau pool Azure Batch est créé lors de la mise à niveau et l’ancien pool Azure Batch est supprimé. En outre, les ressources liées au réseau virtuel pour l’ancien pool sont supprimées et les nouvelles ressources liées au réseau virtuel sont créées sous votre abonnement. Cette erreur signifie que la suppression de la ressource liée au réseau virtuel de l’ancien pool a échoué en raison du verrou de suppression qui est appliqué au niveau de l’abonnement ou du groupe de ressources. Étant donné que le client contrôle et définit le verrou de suppression, il doit supprimer le verrou de suppression dans ce cas.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Si le provisionnement du runtime d’intégration SSIS échoue, toutes les ressources qui ont été créées sont supprimées. Toutefois, s’il existe un verrou de suppression de ressource au niveau de l’abonnement ou du groupe de ressources, les ressources du réseau virtuel ne sont pas supprimées comme prévu. Pour corriger cette erreur, supprimez le verrou de suppression et redémarrez le runtime d’intégration.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Lorsque vous arrêtez le runtime d’intégration SSIS, toutes les ressources liées au réseau virtuel sont supprimées. Toutefois, la suppression peut échouer s’il existe un verrou de suppression de ressource au niveau de l’abonnement ou du groupe de ressources. Ici aussi, le client contrôle et définit le verrou de suppression. Par conséquent, il doit supprimer le verrou de suppression, puis arrêter le runtime d’intégration SSIS.

### <a name="nodeunavailable"></a>NodeUnavailable

Cette erreur se produit lorsque le runtime d’intégration est en cours d’exécution et elle signifie que celui-ci est devenu défectueux. Cette erreur est toujours due à une modification de la configuration du serveur DNS ou du groupe de sécurité réseau qui empêche le runtime d’intégration SSIS de se connecter à un service nécessaire. Étant donné que la configuration du serveur DNS et du groupe de sécurité réseau est contrôlée par le client, le client doit résoudre les problèmes bloquants de son côté. Pour plus d'informations, consultez [Configuration du réseau virtuel de runtime d’intégration SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si vous rencontrez toujours des problèmes, contactez l’équipe du support technique Azure Data Factory.
