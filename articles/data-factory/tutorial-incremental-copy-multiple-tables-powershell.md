---
title: 'Copier de façon incrémentielle plusieurs tables avec Azure Data Factory '
description: Dans ce tutoriel, vous allez créer un pipeline Azure Data Factory qui copie de façon incrémentielle des données delta de plusieurs tables d’une base de données SQL Server locale dans une base de données Azure SQL.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: b841acf45c20320fada895f20dfc4065837d5add
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683388"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Charger de façon incrémentielle des données provenant de plusieurs tables de SQL Server vers une base de données Azure SQL
Dans ce tutoriel, vous allez créer une fabrique de données Azure Data Factory avec un pipeline qui charge les données delta de plusieurs tables d’une base de données SQL Server locale vers une base de données Azure SQL.    

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Préparer les magasins de données source et de destination.
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Installer le runtime d’intégration. 
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et filigrane.
> * Créez, exécutez et surveillez un pipeline.
> * Passez en revue les résultats.
> * Ajouter ou mettre à jour des données dans les tables source.
> * Réexécuter et surveiller le pipeline.
> * Passer en revue les résultats finaux.

## <a name="overview"></a>Vue d’ensemble
Voici les étapes importantes à suivre pour créer cette solution : 

1. **Sélectionner la colonne de limite**.
    Sélectionnez une colonne pour chaque table du magasin de données sources, qui peut servir à identifier les enregistrements nouveaux ou mis à jour pour chaque exécution. Normalement, les données contenues dans cette colonne sélectionnée (par exemple, last_modify_time ou ID) continuent de croître à mesure que des lignes sont créées ou mises à jour. La valeur maximale de cette colonne est utilisée comme limite.

1. **Préparer un magasin de données pour stocker la valeur de limite**.   
    Dans ce tutoriel, la valeur de filigrane est stockée dans une base de données SQL.

1. **Créer un pipeline avec les activités suivantes** : 
    
    a. Créez une activité ForEach qui effectue une itération dans une liste de noms de table source qui est transmise en tant que paramètre au pipeline. Pour chaque table source, elle appelle les activités suivantes pour effectuer le chargement delta pour cette table.

    b. Créez deux activités de recherche. Servez-vous de la première activité de recherche pour récupérer la dernière valeur de filigrane. Utilisez la deuxième activité de recherche pour récupérer la nouvelle valeur de filigrane. Ces valeurs de filigrane sont transmises à l’activité de copie.

    c. Créez une activité de copie qui copie les lignes du magasin de données source dont la valeur de la colonne de filigrane est supérieure à l’ancienne valeur de filigrane et inférieure à la nouvelle. Elle copie ensuite les données delta du magasin de données source dans un stockage Blob Azure sous la forme d’un nouveau fichier.

    d. Créez une activité StoredProcedure qui met à jour la valeur de filigrane pour le pipeline s’exécutant la prochaine fois. 

    Voici le diagramme général de la solution : 

    ![Chargement incrémentiel de données](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis
* **SQL Server**. Dans le cadre de ce tutoriel, vous allez utiliser une base de données SQL Server locale comme magasin de données source. 
* **Azure SQL Database**. Vous allez utiliser une base de données SQL comme magasin de données récepteur. Si vous ne disposez pas d’une base de données SQL, consultez [Créer une base de données Azure SQL Database](../sql-database/sql-database-get-started-portal.md) pour connaître la procédure à suivre pour en créer une. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Créer des tables sources dans votre base de données SQL Server

1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données SQL Server locale.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données et choisissez **Nouvelle requête**.

1. Exécutez la commande SQL suivante sur votre base de données pour créer des tables nommées `customer_table` et `project_table` :

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Créer des tables de destination dans votre base de données Azure SQL
1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données SQL Server.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données et choisissez **Nouvelle requête**.

1. Exécutez la commande SQL suivante sur votre base de données SQL pour créer des tables nommées `customer_table` et `project_table` :  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Créer une autre table dans la base de données Azure SQL pour stocker la valeur de limite supérieure
1. Exécutez la commande SQL suivante sur votre base de données SQL pour créer une table sous le nom `watermarktable` pour stocker la valeur de filigrane : 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Insérer des valeurs de limite supérieure initiale pour les deux tables source dans la table de limite supérieure.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Créer une procédure stockée dans la base de données Azure SQL 

Exécutez la commande suivante pour créer une procédure stockée dans votre base de données SQL. Cette procédure stockée met à jour la valeur de filigrane après chaque exécution du pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Créer des types de données et des procédures stockées supplémentaires dans Azure SQL Database
Exécutez la requête suivante pour créer deux procédures stockées et deux types de données dans votre base de données SQL. Ils sont utilisés pour fusionner les données des tables source dans les tables de destination. 

Afin de faciliter le démarrage du parcours, nous utilisons directement ces procédures stockées en transmettant les données delta par l’intermédiaire d’une variable de table, puis nous les fusionnons dans le magasin de destination. Faites attention qu’il ne s’attende pas à ce qu’un nombre « élevé » de lignes delta (plus de 100) soient stockées dans la variable de table.  

Si vous n’avez pas besoin de fusionner un grand nombre de lignes delta dans le magasin de destination, nous vous suggérons de d’abord utiliser l’activité de copie pour copier toutes les données delta dans une table de « mise en lots » temporaire dans le magasin de destination, puis de générer votre propre procédure stockée sans utiliser la variable de table pour les fusionner de la table de « mise en lots » dans la table « finale ». 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Installation et configuration d’Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Créer une fabrique de données
1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. Par exemple `"adfrg"`. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Attribuez une valeur différente à la variable `$resourceGroupName`, puis réexécutez la commande.

1. Définissez une variable pour l’emplacement de la fabrique de données. 

    ```powershell
    $location = "East US"
    ```
1. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Attribuez une valeur différente à la variable `$resourceGroupName`, puis réexécutez la commande.

1. Définissez une variable pour le nom de la fabrique de données. 

    > [!IMPORTANT]
    >  Mettez à jour le nom de la fabrique de données pour le rendre globalement unique. Par exemple, ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
1. Pour créer la fabrique de données, exécutez la cmdlet **Set-AzDataFactoryV2** suivante : 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Notez les points suivants :

* Le nom de la fabrique de données doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez :

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pour créer des instances Data Factory, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être membre des rôles Contributeur ou Propriétaire, ou administrateur de l’abonnement Azure.
* Pour obtenir la liste des régions Azure dans lesquelles Data Factory est actuellement disponible, sélectionnez les régions qui vous intéressent dans la page suivante, puis développez **Analytique** pour localiser **Data Factory** : [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/). Les magasins de données (Stockage Azure, SQL Database, etc.) et les services de calcul (Azure HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]


## <a name="create-linked-services"></a>Créez des services liés
Vous allez créer des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans cette section, vous créez des services liés à vos bases de données Azure SQL et à votre base de données SQL Server locale. 

### <a name="create-the-sql-server-linked-service"></a>Créer le service lié SQL Server
Dans cette étape, vous liez votre base de données SQL Server locale à la fabrique de données.

1. Créez un fichier JSON nommé **SqlServerLinkedService.json** dans le dossier C:\ADFTutorials\IncCopyMultiTableTutorial avec le contenu suivant. Sélectionnez la section appropriée en fonction de l’authentification utilisée pour vous connecter à SQL Server. Créez les dossiers locaux s’ils n’existent pas encore. 

    > [!IMPORTANT]
    > Sélectionnez la section appropriée en fonction de l’authentification utilisée pour vous connecter à SQL Server.

    Si vous utilisez l’authentification SQL, copiez la définition JSON suivante :

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Si vous utilisez l’authentification Windows, copiez la définition JSON suivante :

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Sélectionnez la section appropriée en fonction de l’authentification utilisée pour vous connecter à SQL Server.
    > - Remplacez &lt;integration runtime name> par le nom de votre runtime d’intégration.
    > - Remplacez &lt;servername>, &lt;databasename>, &lt;username> et &lt;password> par les valeurs de votre base de données SQL Server avant d’enregistrer le fichier.
    > - Si vous avez besoin d’utiliser une barre oblique (`\`) dans votre nom de serveur ou de compte d’utilisateur, utilisez le caractère d’échappement (`\`). Par exemple `mydomain\\myuser`.

1. Dans PowerShell, exécutez l’applet de commande suivante pour passer au dossier C:\ADFTutorials\IncCopyMultiTableTutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

1. Exécutez la cmdlet **Set-AzDataFactoryV2LinkedService** pour créer le service lié AzureStorageLinkedService. Dans l’exemple suivant, vous transmettez les valeurs des paramètres *ResourceGroupName* et *DataFactoryName* : 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Créer le service lié pour base de données SQL
1. Créez un fichier JSON nommé **AzureSQLDatabaseLinkedService.json** dans le dossier C:\ADFTutorials\IncCopyMultiTableTutorial avec le contenu suivant. (Créez le dossier ADF s’il n’existe pas déjà.) Remplacez &lt;servername&gt;, &lt;database name&gt;, &lt;user name&gt; et &lt;password&gt; par le nom de votre serveur de base de données SQL Server, le nom de votre base de données, le nom d’utilisateur et le mot de passe avant d’enregistrer le fichier. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
1. Dans PowerShell, exécutez la cmdlet **Set-AzDataFactoryV2LinkedService** pour créer le service lié AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous créez des jeux de données pour représenter la source de données, la destination des données et l’emplacement de stockage du filigrane.

### <a name="create-a-source-dataset"></a>Créer un jeu de données source

1. Créez un fichier JSON sous le nom **SourceDataset.json** dans le même dossier avec le contenu suivant : 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    L’activité de copie dans le pipeline utilise une requête SQL pour charger les données plutôt que de charger l’ensemble de la table.

1. Exécutez la cmdlet **Set-AzDataFactoryV2Dataset** pour créer le jeu de données SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Voici l’exemple de sortie de l’applet de commande :
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Créer un jeu de données récepteur

1. Créez un fichier JSON nommé **SinkDataset.json** dans le même dossier avec le contenu suivant. L’élément tableName est défini par le pipeline de manière dynamique lors de l’exécution. L’activité ForEach du pipeline effectue une itération dans une liste de noms de table et transmet le nom de table à ce jeu de données à chaque itération. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

1. Exécutez la cmdlet **Set-AzDataFactoryV2Dataset** pour créer le jeu de données SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Voici l’exemple de sortie de l’applet de commande :
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Créer un jeu de données pour un filigrane
Dans cette étape, vous allez créer un jeu de données pour stocker une valeur de limite supérieure. 

1. Créez un fichier JSON nommé **WatermarkDataset.json** dans le même dossier avec le contenu suivant : 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
1. Exécutez la cmdlet **Set-AzDataFactoryV2Dataset** pour créer le jeu de données WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Voici l’exemple de sortie de l’applet de commande :
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Créer un pipeline
Ce pipeline prend une liste de noms de tables comme paramètre. L’**activité ForEach** effectue une itération dans la liste des noms de table et effectue les opérations suivantes : 

1. Utilisez l’**activité Rechercher** pour récupérer la valeur de l’ancienne limite (la valeur initiale ou celle utilisée dans la dernière itération).

1. Utilisez l’**activité Rechercher** pour récupérer la valeur de la nouvelle limite (la valeur maximale de la colonne de limite dans la table source).

1. Utilisez l’**activité Copier** pour copier les données entre ces deux valeurs de limite depuis la base de données source vers la base de données de destination.

1. Utilisez l’**activité StoredProcedure** pour mettre à jour l’ancienne valeur de la limite à utiliser dans la première étape de l’itération suivante. 

### <a name="create-the-pipeline"></a>Créer le pipeline
1. Créez un fichier JSON nommé **IncrementalCopyPipeline.json** dans le même dossier avec le contenu suivant : 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
1. Exécutez la cmdlet **Set-AzDataFactoryV2Pipeline** pour créer le pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Voici l'exemple de sortie : 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Exécuter le pipeline

1. Créez un fichier de paramètres nommé **Parameters.json** dans le même dossier avec le contenu suivant :

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
1. Exécutez le pipeline IncrementalCopyPipeline en utilisant la cmdlet **Invoke-AzDataFactoryV2Pipeline**. Remplacez les espaces réservés par les noms de votre groupe de ressources et de votre fabrique de données.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services**, effectuez une recherche avec le mot clé *Fabriques de données*, puis sélectionnez **Fabriques de données**. 

1. Recherchez votre fabrique de données dans la liste des fabriques de données, puis sélectionnez-la pour ouvrir la page **Fabrique de données**. 

1. Dans la page **Fabrique de données**, sélectionnez **Créer et surveiller** pour lancer Azure Data Factory dans un onglet séparé.

1. Dans la page **Commençons**, sélectionnez **Superviser** sur le côté gauche. 
![Exécutions de pipeline](media/doc-common-process/get-started-page-monitor-button.png)    

1. Vous pouvez voir toutes les exécutions de pipeline et leurs états. Notez que dans l’exemple suivant, l’état d’exécution de pipeline est **Réussite**. Pour vérifier les paramètres transmis au pipeline, sélectionnez le lien dans la colonne **Paramètres**. Si une erreur s’est produite, un lien figure dans la colonne **Erreur**.

    ![Exécutions de pipeline](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
1. Si vous sélectionnez le lien dans la colonne **Actions**, vous voyez toutes les exécutions d’activité du pipeline. 

1. Pour revenir à la vue **Exécutions de pipeline**, sélectionnez **Toutes les exécutions de pipeline**. 

## <a name="review-the-results"></a>Passer en revue les résultats.
Dans SQL Server Management Studio, exécutez les requêtes suivantes sur la base de données SQL cible pour vérifier que les données ont été copiées à partir des tables source vers les tables de destination : 

**Requête** 
```sql
select * from customer_table
```

**Sortie**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Requête**

```sql
select * from project_table
```

**Sortie**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Requête**

```sql
select * from watermarktable
```

**Sortie**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Notez que les valeurs de filigrane des deux tables ont été mises à jour. 

## <a name="add-more-data-to-the-source-tables"></a>Ajouter plus de données aux tables sources

Exécutez la requête suivante sur la base de données SQL Server source pour mettre à jour une ligne existante dans customer_table. Insérez une nouvelle ligne dans project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Exécutez à nouveau le pipeline

1. Maintenant, exécutez à nouveau le pipeline à l’aide de la commande PowerShell suivante :

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
1. Analysez les exécutions du pipeline en suivant les instructions indiquées dans la section [Surveiller le pipeline](#monitor-the-pipeline). Quand l’état du pipeline est **En cours**, vous voyez un autre lien d’action sous **Actions** pour annuler l’exécution du pipeline. 

1. Sélectionnez **Actualiser** pour actualiser la liste jusqu’à ce que l’exécution du pipeline réussisse. 

1. Sélectionnez éventuellement le lien **Afficher les exécutions du pipeline** sous **Actions** pour voir toutes les exécutions d’activité associées à l’exécution de ce pipeline. 

## <a name="review-the-final-results"></a>Passer en revue les résultats finaux
Dans SQL Server Management Studio, exécutez les requêtes suivantes sur la base de données cible pour vérifier que les données nouvelles/mises à jour ont été copiées à partir des tables sources vers les tables de destination. 

**Requête** 
```sql
select * from customer_table
```

**Sortie**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Notez les nouvelles valeurs de **Name** et **LastModifytime** pour le **PersonID** du numéro 3. 

**Requête**

```sql
select * from project_table
```

**Sortie**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Notez que l’entrée **NewProject** a été ajoutée à project_table. 

**Requête**

```sql
select * from watermarktable
```

**Sortie**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Notez que les valeurs de filigrane des deux tables ont été mises à jour.
     
## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Préparer les magasins de données source et de destination.
> * Créer une fabrique de données.
> * Créer un runtime d’intégration auto-hébergé (IR).
> * Installer le runtime d’intégration.
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et filigrane.
> * Créez, exécutez et surveillez un pipeline.
> * Passez en revue les résultats.
> * Ajouter ou mettre à jour des données dans les tables source.
> * Réexécuter et surveiller le pipeline.
> * Passer en revue les résultats finaux.

Passez au tutoriel suivant pour en savoir plus sur la transformation des données en utilisant un cluster Spark sur Azure :

> [!div class="nextstepaction"]
>[Charger de façon incrémentielle des données d’Azure SQL Database dans le stockage Blob Azure à l’aide de la technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)


