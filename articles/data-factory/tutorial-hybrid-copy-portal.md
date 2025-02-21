---
title: Copier des données de SQL Server sur le stockage Blob avec Azure Data Factory
description: Découvrez comment copier les données d’un magasin de données local dans le cloud en utilisant un runtime d’intégration auto-hébergé dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: abnarain
ms.openlocfilehash: 09768e3b9bd1c2e6c9d4a5dbe95bb270b07266c0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683539"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copier des données depuis une base de données SQL Server locale vers un stockage Blob Azure
Dans ce didacticiel, vous allez utiliser l’interface utilisateur d’Azure Data Factory pour créer un pipeline Data Factory qui copie les données d’une base de données SQL Server locale vers un stockage Blob Azure. Vous allez créer et utiliser un runtime d’intégration auto-hébergé, qui déplace les données entre les banques de données locales et cloud.

> [!NOTE]
> Cet article ne fournit pas de présentation détaillée de Data Factory. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](introduction.md). 

Dans ce tutoriel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

## <a name="prerequisites"></a>Prérequis
### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit avoir le rôle de *Contributeur* ou de *Propriétaire*, ou être *administrateur* de l’abonnement Azure. 

Pour afficher les autorisations dont vous disposez dans l’abonnement, accédez au portail Azure. Dans l’angle supérieur droit, sélectionnez votre nom d’utilisateur, puis **Autorisations**. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour obtenir des exemples d’instructions sur l’ajout d’un utilisateur à un rôle, voir [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du Portail Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 et 2017
Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données *source*. Le pipeline de la fabrique de données que vous allez créer dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob (récepteur). Créez ensuite un tableau nommé **emp** dans votre base de données SQL Server, puis insérez-y quelques exemples d’entrées. 

1. Exécutez SQL Server Management Studio. S’il n’est pas déjà installé sur votre machine, accédez à [Télécharger SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Connectez-vous à votre instance SQL Server à l’aide de vos informations d’identification. 

1. Créez un exemple de base de données. Dans l’arborescence, cliquez avec le bouton droit sur **Bases de données**, puis sur **Nouvelle base de données**. 
1. Dans la fenêtre **Nouvelle base de données**, entrez un nom pour la base de données, puis cliquez sur **OK**. 

1. Pour créer la table **emp** et y insérer quelques données d’exemple, exécutez le script de requête suivant sur la base de données. Dans l’arborescence, cliquez avec le bouton droit sur la base de données créée, puis sur **Nouvelle requête**.

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

### <a name="azure-storage-account"></a>Compte Azure Storage
Dans ce didacticiel, vous utilisez un compte de stockage Azure à usage général (stockage Blob plus spécifiquement) comme banque de données réceptrice/de destination. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md). Le pipeline de la fabrique de données que vous allez créer dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob (récepteur). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce didacticiel, vous utilisez le nom et la clé de votre compte de stockage. Pour obtenir le nom et la clé de votre compte de stockage, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre nom d’utilisateur et votre mot de passe Azure. 

1. Dans le volet gauche, sélectionnez **Tous les services**. Filtrez à l’aide du mot-clé **Stockage**, puis sélectionnez **Comptes de stockage**.

    ![Recherche de compte de stockage](media/doc-common-process/search-storage-account.png)

1. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire). Sélectionnez ensuite votre compte de stockage. 

1. Dans la fenêtre **Compte de stockage**, sélectionnez **Clés d’accès**.

1. Dans les zones **Nom du compte de stockage** et **key1**, copiez les valeurs, puis collez-les dans le bloc-notes ou un autre éditeur pour une utilisation ultérieure dans le tutoriel. 

#### <a name="create-the-adftutorial-container"></a>Créer le conteneur adftutorial 
Dans cette section, vous allez créer un conteneur d’objets blob nommé **adftutorial** dans votre stockage Blob. 

1. Dans la fenêtre **Compte de stockage**, accédez à **Vue d’ensemble**, puis sélectionnez **Objets blob**. 

    ![Sélection de l’option Objets blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Dans la fenêtre **Service Blob**, sélectionnez **Conteneur**. 

1. Dans la fenêtre **Nouveau conteneur**, sous **Nom**, entrez **adftutorial**. Sélectionnez ensuite **OK**. 

1. Cliquez sur **adftutorial** dans la liste des conteneurs.

1. Gardez la fenêtre **conteneur** de **adftutorial** ouverte. Elle vous permet de vérifier la sortie à la fin du tutoriel. Data Factory crée automatiquement le dossier de sortie de ce conteneur, de sorte que vous n’avez pas besoin d’en créer.

## <a name="create-a-data-factory"></a>Créer une fabrique de données
À cette étape, vous allez créer une fabrique de données et démarrer l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données. 

1. Ouvrez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analyse** > **Data Factory** :
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 

   Le nom de la fabrique de données doit être un *nom global unique*. Si le message d’erreur suivant s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.

   ![Nouveau nom de fabrique de données](./media/doc-common-process/name-not-available-error.png)

1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
   
   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.
        
     Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).
1. Sous **Version**, sélectionnez **V2**.
1. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent se trouver dans d’autres régions.
1. Sélectionnez **Create** (Créer).

1. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image :
   
    ![Page d’accueil Data Factory](./media/doc-common-process/data-factory-home-page.png)
1. Sélectionnez la vignette **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé. 


## <a name="create-a-pipeline"></a>Créer un pipeline

1. Dans la page **Prise en main**, cliquez sur **Créer un pipeline**. Un pipeline est automatiquement créé pour vous. Le pipeline apparaît dans l’arborescence et son éditeur s’ouvre. 

   ![Page Prise en main](./media/doc-common-process/get-started-page.png)

1. Sous l’onglet **Général** au bas de la fenêtre **Propriétés**, entrez **SQLServerToBlobPipeline** comme **Nom**.

   ![Nom du pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. Dans la boîte à outils **Activités**, développez **Déplacer et transformer**. Faites glisser et déposez l’activité **Copier** sur l’aire de conception du pipeline. Définissez le nom de l’activité sur **CopySqlServerToAzureBlobActivity**.

1. Dans la fenêtre **Propriétés**, accédez à l’onglet **Source**, puis sélectionnez **+ Nouveau**.

1. Dans la boîte de dialogue **Nouveau jeu de données**, recherchez **SQL Server**. Sélectionnez **SQL Server**, puis **Continuer**. 

1. Dans la boîte de dialogue **Définir les propriétés**, sous **Nom**, entrez **SqlServerDataset**. Sous **Service lié**, sélectionnez **+ Nouveau**. Vous créez une connexion à un magasin de données source (base de données SQL Server) dans cette étape. 

1. Dans la boîte de dialogue **Nouveau service lié**, ajoutez **SqlServerLinkedService** comme **Nom**. Sous **Se connecter via le runtime d’intégration** sélectionnez **+ Nouveau**.  Dans cette section, vous allez créer un runtime d’intégration auto-hébergé et l’associer à un ordinateur local avec la base de données SQL Server. Le runtime d’intégration auto-hébergé est le composant qui copie les données de la base de données SQL Server sur votre machine dans le stockage Blob. 

1. Dans la boîte de dialogue **Configuration du runtime d’intégration**, sélectionnez **Auto-hébergé**, puis **Suivant**. 

1. Sous Nom, entrez **TutorialIntegrationRuntime**. Sélectionnez ensuite **Suivant**.

1. Pour Paramètres, sélectionnez **Cliquez ici pour lancer l’installation rapide pour cet ordinateur**. Cette action installe le runtime d’intégration sur votre machine et l’inscrit auprès de Data Factory. Vous pouvez également utiliser l’option d’installation manuelle pour télécharger le fichier d’installation, l’exécuter et utiliser la clé pour inscrire le runtime d’intégration. 

1. Dans la fenêtre **Installation rapide d'Integration Runtime (auto-hébergé)** , sélectionnez **Fermer**. 

    ![Installation rapide d'Integration Runtime (auto-hébergé)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Dans la boîte de dialogue **Nouveau service lié**, vérifiez que **TutorialIntegrationRuntime** est sélectionné sous **Se connecter via le runtime d’intégration**. Ensuite, effectuez les étapes suivantes :

    a. Dans le champ **Nom**, entrez **SqlServerLinkedService**.

    b. Entrez le nom de votre instance SQL Server dans le champ **Nom du serveur**. 

    c. Spécifiez le nom de la base de données avec la table **emp** dans le champ **Nom de la base de données**.

    d. Sous **Type d’authentification**, sélectionnez le type d’authentification approprié que Data Factory doit utiliser pour se connecter à votre base de données SQL Server.

    e. Dans les champs **Nom d’utilisateur** et **Mot de passe**, entrez le nom d’utilisateur et le mot de passe. Si vous avez besoin d’utiliser une barre oblique (\\) dans votre nom de serveur ou de compte d’utilisateur, faites-la précéder d’un caractère d’échappement (\\). Par exemple, utilisez *mydomain\\\\myuser*.

    f. Sélectionnez **Tester la connexion**. Cette étape permet de confirmer que Data Factory peut se connecter à votre base de données SQL Server à l’aide du runtime d’intégration auto-hébergé que vous avez créé.

    g. Sélectionnez **Terminer** pour enregistrer le service lié.

1. Vous devez être revenu à la fenêtre avec le jeu de données source ouvert. Sous l’onglet **Connexion** de la fenêtre **Propriétés**, procédez comme suit : 

    a. Vérifiez que vous voyez **SqlServerLinkedService** dans le champ **Service lié**.

    b. Sélectionnez **[dbo].[emp]** dans le champ **Table**.

1. Accédez à l’onglet avec **SQLServerToBlobPipeline** ou sélectionnez **SQLServerToBlobPipeline** dans l’arborescence. 

1. Accédez à l’onglet **Récepteur** au bas de la fenêtre **Propriétés**, puis sélectionnez **+ Nouveau**. 

1. Dans la boîte de dialogue **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**. Sélectionnez **Continuer**. 

1. Dans la boîte de dialogue **Sélectionner le format**, choisissez le type de format de vos données. Sélectionnez **Continuer**. 

    ![Sélection du format des données](./media/doc-common-process/select-data-format.png)

1. Dans la boîte de dialogue **Définir les propriétés**, entrez **AzureBlobDataset** comme nom. En regard de la zone de texte **Service lié**, sélectionnez **+ Nouveau**.

1. Dans la boîte de dialogue **Nouveau service lié (Stockage Blob Azure)** , entrez **AzureStorageLinkedService** comme nom, puis sélectionnez votre compte de stockage dans la liste **Nom du compte de stockage**. Testez la connexion, puis sélectionnez **Terminer** pour déployer le service lié.
1. Après la création du service lié, vous revenez à la page **Définir les propriétés**. Sélectionnez **Continuer**.

1. Vous devez être revenu à la fenêtre avec le jeu de données récepteur ouvert. Sous l’onglet **Connexion**, procédez comme suit : 

    a. Vérifiez que **AzureStorageLinkedService** est sélectionné dans le champ **Service lié**.
  
    b. Dans **Chemin d’accès du fichier**, entrez **adftutorial/fromonprem** pour la partie **Conteneur/répertoire**. Si le dossier de sortie n’existe pas dans le conteneur adftutorial, Data Factory crée automatiquement le dossier de sortie.
    
    c. Pour la partie **Fichier**, sélectionnez **Ajouter du contenu dynamique**.
    ![expression dynamique pour la résolution du nom de fichier](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Ajoutez `@CONCAT(pipeline().RunId, '.txt')`, puis sélectionnez **Terminer**. Cette action a pour effet de renommer le fichier PipelineRunID.txt. 

1. Accédez à l’onglet avec le pipeline ouvert ou sélectionnez le pipeline dans l’arborescence. Vérifiez que **AzureBlobDataset** est sélectionné dans le champ **Jeu de données récepteur**.

1. Pour valider les paramètres du pipeline, cliquez sur **Valider** dans la barre d’outils du pipeline. Pour fermer le **rapport de validation de pipeline**, sélectionnez **Fermer**. 

1. Pour publier les entités que vous avez créées dans Data Factory, sélectionnez **Publish All** (Tout publier).

1. Patientez jusqu’à ce que la fenêtre contextuelle **Réussite de la publication** s’affiche. Pour vérifier l’état de la publication, sélectionnez le lien **Afficher les notifications** en haut de la fenêtre. Pour fermer la fenêtre de notification, sélectionnez **Fermer**. 


## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline
Sélectionnez **Ajouter un déclencheur** dans la barre d’outils du pipeline, puis **Déclencher maintenant**.

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Accédez à l’onglet **Surveiller**. Vous voyez le pipeline que vous avez déclenché manuellement à l’étape précédente. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Vous ne voyez qu’une seule exécution d’activité, car le pipeline ne contient qu’une seule activité. Pour voir plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Pour revenir à la vue des exécutions du pipeline, sélectionnez **Exécutions de pipeline** en haut de la page.

## <a name="verify-the-output"></a>Vérifier la sortie
Le pipeline crée automatiquement le dossier de sortie nommé *fromonprem* dans le conteneur d’objets blob `adftutorial`. Vérifiez que le fichier *[pipeline().RunId].txt* se trouve dans le dossier de sortie. 


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créez des services liés SQL Server et de stockage. 
> * Créez des jeux de données SQL Server et de stockage Blob.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

Pour obtenir la liste des magasins de données pris en charge par Data Factory, consultez l’article sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Pour découvrir comment copier des données en bloc d’une source vers une destination, passez au didacticiel suivant :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy-portal.md)