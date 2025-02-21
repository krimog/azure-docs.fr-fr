---
title: Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory
description: Découvrez comment créer un runtime d’intégration Azure-SSIS dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ce688248a205981f4a4c60ad01231c0b8f6bae3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677357"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory

Cet article décrit les différentes étapes de provisionnement d’un IR (runtime d’intégration) Azure-SSIS (SQL Server Integration Services) dans Azure Data Factory. Un IR Azure-SSIS prend en charge :

- L’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database ou une instance managée (modèle de déploiement de projet)
- L’exécution de packages déployés dans les systèmes de fichiers, les partages de fichiers ou Azure Files (modèle de déploiement de package) 

Une fois qu’un IR Azure-SSIS est provisionné, vous pouvez utiliser des outils familiers pour déployer et exécuter vos packages dans Azure. Ces outils incluent SQL Server Data Tools, SQL Server Management Studio et des outils en ligne de commande tels que `dtinstall`, `dtutil` et `dtexec`.

Le tutoriel [Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory](tutorial-create-azure-ssis-runtime-portal.md) montre comment créer un IR Azure-SSIS à l’aide du portail Azure ou de l’application Data Factory. Ce tutoriel montre également comment utiliser, si vous le souhaitez, un serveur Azure SQL Database ou une instance managée pour héberger le catalogue SSISDB. Cet article s’appuie sur le tutoriel et explique comment effectuer les tâches facultatives suivantes :

- Utiliser un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB. Vous devez au préalable configurer les autorisations et paramètres de réseau virtuel pour que votre runtime d’intégration Azure-SSIS soit joint à un réseau virtuel.

- Utiliser l’authentification Azure Active Directory (Azure AD) avec l’identité managée pour que votre fabrique de données se connecte à un serveur Azure SQL Database ou une instance managée. Vous devez au préalable ajouter l’identité managée pour votre fabrique de données comme utilisateur de base de données capable de créer une instance SSISDB.

- Joindre votre IR Azure-SSIS à un réseau virtuel ou configurer un IR auto-hébergé en tant que proxy pour votre IR Azure-SSIS afin d’accéder aux données locales.

Cet article montre comment provisionner un IR Azure-SSIS à l’aide du portail Azure, d’Azure PowerShell et d’un modèle Azure Resource Manager.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas encore d’abonnement, vous pouvez créer un compte d’[essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Serveur Azure SQL Database ou instance managée (facultatif)** . Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Data Factory crée à son tour une instance SSISDB sur ce serveur de base de données. 

  Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux des exécutions dans SSISDB sans dépasser les régions Azure.

  Gardez à l’esprit les points suivants :

  - En fonction du serveur de base de données sélectionné, l’instance SSISDB peut être créée à pour vous en tant que base de données unique, dans le cadre d’un pool élastique ou dans une instance managée. Elle peut être accessible dans un réseau public ou en la joignant à un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger le catalogue SSISDB, consultez [Comparer une base de données Azure SQL Database unique, un pool élastique et une instance managée](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer l’IR auto-hébergé, vous devez joindre votre IR Azure-SSIS à un réseau virtuel. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
  - Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Ce paramètre ne s’applique pas quand vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
  - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos informations d’identification d’administrateur de serveur ou de l’authentification Azure AD avec l’identité managée de votre fabrique de données. Pour cette dernière, vous devez ajouter l’identité managée de votre fabrique de données à un groupe Azure AD avec autorisations d’accès au serveur de base de données. Pour plus d’informations, consultez [Activer l’authentification Azure AD pour le runtime d’intégration (IR) Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
  - Vérifiez que votre serveur de base de données ne dispose pas déjà d’une instance SSISDB. Le provisionnement d’un IR Azure-SSIS ne prend pas en charge l’utilisation d’une instance SSISDB existante.
- **Réseau virtuel Azure Resource Manager (facultatif)** . Vous devez disposer d’un réseau virtuel Azure Resource Manager si au moins l’une des conditions suivantes est remplie :
    - Vous hébergez le catalogue SSISDB sur un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé.
    - Vous souhaitez vous connecter à des magasins de données locaux à partir de packages SSIS en cours d’exécution sur votre IR Azure-SSIS sans configurer d’IR auto-hébergé.
- **Azure PowerShell (facultatif)** . Suivez les instructions de la rubrique [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/install-az-ps) si vous souhaitez exécuter un script PowerShell pour provisionner votre runtime d’intégration Azure-SSIS.

### <a name="regional-support"></a>Prise en charge régionale

Pour obtenir la liste des régions Azure dans lesquelles Data Factory et un IR Azure-SSIS sont disponibles, consultez la page sur la [disponibilité d’Azure Data Factory et d’un IR Azure-SSIS par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Comparaison d’une base de données SQL Database unique, d’un pool élastique et d’une instance managée

Le tableau suivant compare certaines fonctionnalités d’un serveur Azure SQL Database et d’une instance managée concernant l’IR Azure-SSIS :

| Fonctionnalité | Base de données unique/pool élastique| Instance gérée |
|---------|--------------|------------------|
| **Planification** | L’agent SQL Server n’est pas disponible.<br/><br/>Consultez [Planifier un package dans le cadre d’un pipeline Azure Data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| L’agent Managed Instance est disponible. |
| **Authentification** | Vous pouvez créer une instance SSISDB avec un utilisateur de base de données autonome représentant un groupe Azure AD avec l’identité managée de votre fabrique de données en tant que membre du rôle **db_owner**.<br/><br/>Consultez la section sur l’[activation de l’authentification Azure AD pour créer une instance SSISDB sur un serveur Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Vous pouvez créer une instance SSISDB avec un utilisateur de base de données autonome représentant l’identité managée de votre fabrique de données. <br/><br/>Consultez la page sur l’[activation de l’authentification Azure AD pour créer une instance SSISDB dans une instance managée Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Niveau de service** | Quand vous créez un IR Azure-SSIS avec votre serveur Azure SQL Database, vous pouvez sélectionner le niveau de service pour SSISDB. Il existe plusieurs niveaux de service. | Quand vous créez un IR Azure-SSIS avec votre instance managée, vous ne pouvez pas sélectionner le niveau de service pour SSISDB. Toutes les bases de données dans votre instance managée partagent la même ressource allouée à cette instance. |
| **Réseau virtuel** | Votre IR Azure-SSIS peut joindre des réseaux virtuels Azure Resource Manager uniquement si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou si vous avez besoin d’accéder à des magasins de données locaux sans configurer d’IR auto-hébergé. | Votre IR Azure-SSIS peut joindre uniquement des réseaux virtuels Azure Resource Manager. Le réseau virtuel est requis quand vous n’activez pas de point de terminaison public pour votre instance managée.<br/><br/>Si vous joignez votre IR Azure-SSIS au même réseau virtuel que votre instance managée, vérifiez qu’il est sur un sous-réseau différent de celui de votre instance managée. Si vous joignez votre IR Azure-SSIS à un réseau virtuel différent de celui de votre instance managée, nous recommandons un appairage de réseaux virtuels ou une connexion de réseau à réseau. Consultez [Connecter votre application à l’instance managée Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transactions distribuées** | Cette fonctionnalité est prise en charge par le biais des transactions élastiques. Les transactions MSDTC (Microsoft Distributed Transaction Coordinator) ne sont pas prises en charge. Si vos packages SSIS utilisent MSDTC pour coordonner les transactions distribuées, envisagez la migration vers les transactions élastiques pour Azure SQL Database. Pour plus d’informations, consultez [Transactions distribuées entre bases de données cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Non pris en charge. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Utiliser le portail Azure pour créer un runtime d’intégration

Dans le cadre de cette section, vous allez utiliser le portail Azure (plus précisément, l’interface utilisateur ou l’application Data Factory) pour créer un runtime d’intégration Azure-SSIS.

### <a name="create-a-data-factory"></a>Créer une fabrique de données

Pour créer votre fabrique de données à l’aide du portail Azure, suivez les instructions pas à pas dans [Créer une fabrique de données à l’aide de l’interface utilisateur](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Durant la procédure, sélectionnez **Épingler au tableau de bord** pour y accéder rapidement après sa création. 

Une fois la fabrique de données créée, ouvrez la page de vue d’ensemble dans le portail Azure. Sélectionnez la vignette **Créer et surveiller** pour ouvrir la page **Commençons** dans un onglet séparé. Vous pourrez continuer à créer votre IR Azure-SSIS à partir de cette page.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure-SSIS

1. Dans la page de **prise en main**, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**.

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Dans la page **Paramètres généraux** de **Configuration du runtime d’intégration**, effectuez les étapes suivantes.

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Dans **Nom**, saisissez le nom de votre runtime d’intégration.

    b. Dans **Description**, saisissez la description de votre runtime d’intégration.

    c. Pour **Emplacement**, sélectionnez l’emplacement de votre runtime d’intégration. Seuls les emplacements pris en charge sont affichés. Nous vous conseillons de sélectionner le même emplacement que votre serveur de base de données pour héberger SSISDB.

    d. Pour **Taille du nœud**, sélectionnez la taille du nœud du cluster de votre runtime d’intégration. Seules les tailles de nœuds prises en charge sont affichées. Sélectionnez une taille de nœud importante (scale-up) si vous voulez exécuter de nombreux packages nécessitant beaucoup de calcul ou mémoire.

    e. Pour **Nombre de nœud**, sélectionnez le nombre de nœuds du cluster de votre runtime d’intégration. Seuls les nombres de nœuds pris en charge sont affichés. Sélectionnez un grand cluster avec de nombreux nœuds (scale-out) si vous voulez exécuter de nombreux packages en parallèle.

    f. Pour **Édition/Licence**, sélectionnez l’édition SQL Server pour votre runtime d’intégration : Standard ou Entreprise. Sélectionnez Entreprise si vous voulez utiliser les fonctionnalités avancées de votre runtime d’intégration.

    g. Pour **Économiser de l’argent**, sélectionnez l’option Azure Hybrid Benefit pour votre runtime d’intégration : **Oui** ou **Non**. Sélectionnez **Oui** si vous souhaitez utiliser votre propre licence SQL Server avec Software Assurance pour faire des économies grâce à une utilisation hybride.

    h. Sélectionnez **Suivant**.

3. Dans la page **Paramètres SQL**, effectuez les étapes suivantes.

   ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Cochez la case **Créer un catalogue SSIS...** pour choisir le modèle de déploiement approprié pour que les packages s’exécutent sur votre IR Azure-SSIS. Vous allez choisir le modèle de déploiement de projet dans lequel les packages sont déployés dans SSISDB hébergé par votre serveur de base de données ou le modèle de déploiement de package dans lequel les packages sont déployés dans les systèmes de fichiers, les partages de fichiers ou Azure Files. 
    
   Si vous cochez la case, vous devez prévoir votre propre serveur de base de données pour héberger l’instance SSISDB que nous allons créer et gérer pour vous.
   
   b. Pour **Abonnement**, sélectionnez l’abonnement Azure comprenant votre serveur de base de données pour héberger la base de données SSISDB. 

   c. Pour **Emplacement**, sélectionnez l’emplacement de votre serveur de base de données pour héberger la base de données SSISDB. Nous vous recommandons de sélectionner le même emplacement que celui de runtime d’intégration. 

   d. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. 
    
   En fonction du serveur de base de données sélectionné, l’instance SSISDB peut être créée à pour vous en tant que base de données unique, dans le cadre d’un pool élastique ou dans une instance managée. Elle peut être accessible dans un réseau public ou en la joignant à un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger le catalogue SSISDB, consultez [Comparer une base de données Azure SQL Database unique, un pool élastique et une instance managée](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
    
   Si vous sélectionnez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer l’IR auto-hébergé, vous devez joindre votre IR Azure-SSIS à un réseau virtuel. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   e. Cochez la case **Utiliser l’authentification AAD avec l’identité managée pour votre ADF** afin de choisir la méthode d’authentification de votre serveur de base de données pour héberger le catalogue SSISDB. Vous choisissez l’authentification SQL ou l’authentification Azure AD avec l’identité managée de votre fabrique de données. 
    
   Si vous cochez la case, vous devez ajouter l’identité managée de votre fabrique de données à un groupe Azure AD avec autorisations d’accès à votre serveur de base de données. Pour plus d’informations, consultez [Activer l’authentification Azure AD pour le runtime d’intégration (IR) Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

   f. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL de votre serveur de base de données pour héberger le catalogue SSISDB. 

   g. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL de votre serveur de base de données pour héberger le catalogue SSISDB. 

   h. Pour **Niveau de service de base de données de catalogue**, sélectionnez le niveau de service de votre serveur de base de données pour héberger le catalogue SSISDB. Sélectionnez le niveau De base, Standard ou Premium, ou sélectionnez un nom de pool élastique. 

   i. Sélectionnez **Test Connection** (Tester la connexion). Si le test réussit, sélectionnez **Suivant**. 

4. Dans la page **Paramètres avancés**, effectuez les étapes suivantes.

   ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre si vous souhaitez utiliser plusieurs cœurs pour exécuter un seul package volumineux nécessitant beaucoup de calcul ou de mémoire. Sélectionnez un nombre élevé si vous souhaitez exécuter un ou plusieurs petits packages dans un seul cœur.

   b. Pour l’**URI SAS du conteneur d’installation personnalisée**, vous pouvez entrer l’URI (Uniform Resource Identifier) de la signature d’accès partagé (SAS) du conteneur de stockage d’objets blob Azure dans lequel votre script d’installation et les fichiers associés sont stockés. Pour plus d’informations, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Cochez la case **Sélectionner un réseau virtuel que le runtime d’intégration Azure-SSIS doit rejoindre et autoriser ADF à créer certaines ressources réseau** pour indiquer si vous souhaitez joindre le runtime d’intégration à un réseau virtuel. 

   Cochez la case si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales. (Autrement dit, vous avez des sources ou des destinations de données locales dans vos packages SSIS, mais vous ne configurez par d’IR auto-hébergé.) Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   Si vous cochez la case, effectuez les étapes suivantes.

   ![Paramètres avancés avec un réseau virtuel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   a. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel.

   b. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration.

   c. Pour **Type**, sélectionnez le type de votre réseau virtuel : classique ou Azure Resource Manager. Nous vous recommandons de sélectionner un réseau virtuel Azure Resource Manager, car les réseaux virtuels classiques seront bientôt dépréciés.

   d. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Ce réseau virtuel doit être le même que celui utilisé pour le serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée dans un réseau virtuel pour héberger le catalogue SSISDB. Ce réseau virtuel peut également être le même que celui qui est connecté à votre réseau local.

   e. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Il doit être différent du sous-réseau utilisé pour l’instance managée dans un réseau virtuel pour héberger le catalogue SSISDB.

6. Cochez la case **Configurer le runtime d’intégration auto-hébergé en tant que proxy pour le runtime d’intégration Azure-SSIS** pour indiquer si vous voulez configurer un IR auto-hébergé en tant que proxy pour l’IR Azure-SSIS. Pour plus d’informations, consultez [Configurer un runtime d’intégration auto-hébergé en tant que proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

   Si vous cochez la case, effectuez les étapes suivantes.

   ![Paramètres avancés avec un IR auto-hébergé](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   a. Sous **Runtime d’intégration auto-hébergé**, sélectionnez votre IR auto-hébergé existant en tant que proxy pour l’IR Azure-SSIS.

   b. Sous **Service lié de stockage de préproduction**, sélectionnez votre service lié de stockage Blob Azure existant. Vous pouvez également en créer un pour la préproduction.

   c. Sous **Chemin d’accès intermédiaire**, spécifiez un conteneur d’objets blob dans le compte de stockage Blob Azure sélectionné. Vous pouvez également laisser ce champ vide pour utiliser un conteneur par défaut pour la préproduction.

7. Sélectionnez **Validation de réseau virtuel** > **Suivant**. 

8. Dans la page **Résumé**, passez en revue tous les paramètres de provisionnement, ajoutez les liens de documentation recommandés aux favoris, puis sélectionnez **Terminer** pour lancer la création de votre runtime d’intégration.

    > [!NOTE]
    > En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, l’IR Azure-SSIS peut prendre jusqu’à 20-30 minutes pour joindre un réseau virtuel.
    >
    > Si vous utilisez le catalogue SSISDB, le service Data Factory se connecte à votre serveur de base de données pour le préparer. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
    > 
    > Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants assurent la connectivité aux fichiers Excel et Access ainsi qu’à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Pour plus d’informations sur les autres composants que vous pouvez installer, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

7. Dans l’onglet **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Sélectionnez **Actualiser** pour actualiser l’état.

   ![État de la création](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté.

   ![Runtime d’intégration Azure SSIS - actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtimes d’intégration Azure SSIS sur le portail

1. Dans l’interface utilisateur Azure Data Factory, basculez vers l’onglet **Modifier** et sélectionnez **Connexions**. Basculez ensuite vers l’onglet **Runtimes d’intégration** pour voir les runtimes d’intégration existants dans votre fabrique de données.

   ![Afficher les runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Sélectionnez **Nouveau** pour créer un runtime d’intégration Azure SSIS.

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. Dans la **fenêtre de configuration de runtime d’intégration**, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**.

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Consultez la section [Provisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Utiliser Azure PowerShell pour créer un runtime d’intégration

Dans le cadre de cette section, vous allez utiliser Azure PowerShell pour créer un runtime d’intégration Azure-SSIS.

### <a name="create-variables"></a>Créer des variables

Copiez et collez le script suivant. Spécifiez des valeurs pour les variables. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
```

### <a name="sign-in-and-select-a-subscription"></a>Se connecter et sélectionner un abonnement

Ajoutez le script suivant pour vous connecter et sélectionner votre abonnement Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Valider la connexion au serveur de base de données

Ajoutez le script suivant pour valider votre serveur Azure SQL Database ou votre instance managée.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Configurer le réseau virtuel

Ajoutez le script suivant afin de configurer automatiquement les autorisations et paramètres du réseau virtuel pour le runtime d’intégration Azure-SSIS à joindre.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

Si votre groupe de ressources existe déjà, ne copiez pas ce code dans votre script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Créer une fabrique de données

Exécutez la commande suivante pour créer la fabrique de données.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Créer un runtime d’intégration

Exécutez les commandes suivantes pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure.

Si vous n’utilisez pas de catalogue SSISDB, vous pouvez omettre les paramètres `CatalogServerEndpoint`, `CatalogPricingTier` et `CatalogAdminCredential`.

Si vous n’utilisez pas de serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales, vous pouvez omettre les paramètres `VNetId` et `Subnet` ou passer des valeurs vides pour ces paramètres. Vous pouvez également les omettre si vous configurez le runtime d’intégration auto-hébergé en tant que proxy pour votre IR Azure-SSIS afin d’accéder à des données locales. Dans le cas contraire, vous ne pouvez pas les omettre et vous devez passer des valeurs valides de votre configuration de réseau virtuel. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Si vous utilisez une instance managée pour héberger un catalogue SSISDB, vous pouvez omettre le paramètre `CatalogPricingTier` ou passer une valeur vide pour ce paramètre. Dans le cas contraire, vous ne pouvez pas l’omettre et vous devez passer une valeur valide à partir de la liste des niveaux tarifaires pris en charge pour Azure SQL Database. Pour en savoir plus, consultez [Limites de ressources SQL Database](../sql-database/sql-database-resource-limits.md).

Si vous utilisez l’authentification Azure AD avec l’identité managée pour que votre fabrique de données se connecte au serveur de base de données, vous pouvez omettre le paramètre `CatalogAdminCredential`. Cependant, vous devez ajouter l’identité managée de votre fabrique de données à un groupe Azure AD avec autorisations d’accès au serveur de base de données. Pour plus d’informations, consultez [Activer l’authentification Azure AD pour le runtime d’intégration (IR) Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Sinon, vous ne pouvez pas l’omettre et vous devez passer un objet valide formé à partir de votre nom d’utilisateur et mot de passe d’administrateur de serveur pour l’authentification SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-the-integration-runtime"></a>Démarrer le runtime d’intégration

Exécutez les commandes suivantes pour démarrer le runtime d’intégration Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, l’IR Azure-SSIS peut prendre jusqu’à 20-30 minutes pour joindre un réseau virtuel.
>
> Si vous utilisez le catalogue SSISDB, le service Data Factory se connecte à votre serveur de base de données pour le préparer. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
> 
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants assurent la connectivité aux fichiers Excel et Access ainsi qu’à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Pour plus d’informations sur les autres composants que vous pouvez installer, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script complet

Voici le script complet qui crée un runtime d’intégration Azure-SSIS.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Utiliser un modèle Azure Resource Manager pour créer un runtime d’intégration

Dans le cadre de cette section, vous allez utiliser un modèle Azure Resource Manager pour créer le runtime d’intégration Azure-SSIS. Voici un exemple de procédure :

1. Créez un fichier JSON avec le modèle Azure Resource Manager suivant. Remplacez les valeurs entre chevrons (espaces réservés) par vos propres valeurs.

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Pour déployer le modèle Azure Resource Manager, exécutez la commande `New-AzResourceGroupDeployment` comme indiqué dans l’exemple suivant. Dans l’exemple, `ADFTutorialResourceGroup` est le nom de votre groupe de ressources. `ADFTutorialARM.json` est le fichier qui contient la définition JSON pour votre fabrique de données et le runtime d’intégration Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Cette commande crée votre fabrique de données et un runtime d’intégration Azure-SSIS dans cette fabrique, mais elle ne démarre pas le runtime d’intégration.

3. Pour démarrer l’IR Azure-SSIS, exécutez la commande `Start-AzDataFactoryV2IntegrationRuntime` :

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, l’IR Azure-SSIS peut prendre jusqu’à 20-30 minutes pour joindre un réseau virtuel.
>
> Si vous utilisez le catalogue SSISDB, le service Data Factory se connecte à votre serveur de base de données pour le préparer. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
> 
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants assurent la connectivité aux fichiers Excel et Access ainsi qu’à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Pour plus d’informations sur les autres composants que vous pouvez installer, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS

Si vous utilisez le catalogue SSISDB, vous pouvez y déployer vos packages et les exécuter sur l’IR Azure-SSIS à l’aide des outils SQL Server Data Tools ou SQL Server Management Studio. Ces outils se connectent à votre serveur de base de données via son point de terminaison : 

- Pour un serveur Azure SQL Database avec un point de terminaison privé, le format du point de terminaison de serveur est `<server name>.database.windows.net`.
- Pour une instance managée avec un point de terminaison privé, le format du point de terminaison de serveur est `<server name>.<dns prefix>.database.windows.net`.
- Pour une instance managée avec un point de terminaison public, le format du point de terminaison de serveur est `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Si vous n’utilisez pas de catalogue SSISDB, vous pouvez déployer vos packages dans des systèmes de fichiers, des partages de fichiers ou Azure Files. Vous pouvez ensuite les exécuter sur l’IR Azure-SSIS à l’aide des outils en ligne de commande `dtinstall`, `dtutil` et `dtexec`. Pour plus d’informations, consultez [Déployer des packages SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Dans les deux cas, vous pouvez également exécuter vos packages déployés sur l’IR Azure-SSIS à l’aide de l’activité Exécuter le package SSIS dans des pipelines Data Factory. Pour plus d’informations, consultez [Appeler une exécution de package SSIS comme activité Data Factory de première classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation :

- [Runtime d’intégration Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations sur les runtimes d’intégration en général, notamment sur le runtime d’intégration Azure-SSIS.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations sur la façon de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel.
