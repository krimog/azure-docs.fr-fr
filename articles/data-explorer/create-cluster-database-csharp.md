---
title: Créer un cluster et une base de données Azure Data Explorer en utilisant C#
description: Découvrir comment créer un cluster et une base de données Azure Data Explorer en utilisant C#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b5839ef7d9f1e5498beabfcdf0f1605fc1378498
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800411"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Créer un cluster et une base de données Azure Data Explorer en utilisant C#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modèle ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Dans cet article, vous créez un cluster et une base de données en utilisant C#.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="install-c-nuget"></a>Installer le package NuGet C#

* Installez le [package NuGet Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installez le [package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pour l’authentification.

## <a name="authentication"></a>Authentication
Pour exécuter les exemples de cet article, nous avons besoin d’une application Azure AD et d’un principal de service pouvant accéder aux ressources. Consultez [Créer une application Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) pour créer une application Azure AD gratuite et ajouter une attribution de rôle à l’étendue de l’abonnement. Cela montre également comment obtenir `Directory (tenant) ID`, `Application ID` et `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

1. Créez votre cluster en utilisant le code suivant :

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nom souhaité de votre cluster.|
   | skuName | *Standard_D13_v2* | Référence SKU utilisée pour votre cluster. |
   | Niveau | *Standard* | Niveau de référence SKU. |
   | capacité | *number* | Nombre d’instances du cluster. |
   | resourceGroupName | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |

    > [!NOTE]
    > **Créer un cluster** étant une opération à long terme, il est fortement recommandé d’utiliser CreateOrUpdateAsync à la place de CreateOrUpdate. 

1. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Si le résultat contient `ProvisioningState` avec la valeur `Succeeded`, alors le cluster a correctement été créé.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

1. Créez votre base de données en utilisant le code suivant :

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nom du cluster dans lequel la base de données est créée.|
   | databaseName | *mykustodatabase* | Nom de votre base de données.|
   | resourceGroupName | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | softDeletePeriod | *3650:00:00:00* | Durée pendant laquelle les données restent disponibles pour les requêtes. |
   | hotCachePeriod | *3650:00:00:00* | Durée pendant laquelle les données sont conservées dans le cache. |

2. Exécutez la commande suivante pour voir la base de données que vous avez créée :

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Supprimer des ressources

* Si vous envisagez de suivre nos autres articles, conservez les ressources que vous avez créées.
* Pour nettoyer les ressources, supprimez le cluster. Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande suivante pour supprimer votre cluster :

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données à l'aide du SDK .NET Standard d'Azure Data Explorer (préversion)](net-standard-ingest-data.md)
