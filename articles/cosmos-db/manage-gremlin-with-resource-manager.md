---
title: Modèles Azure Resource Manager pour l’API Gremlin Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer l’API Gremlin Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f50a6980cb26d6a89e2564b492a8be900ad57736
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606786"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API Gremlin Azure Cosmos DB à l’aide de modèles Azure Resource Manager

Cet article explique comment effectuer différentes opérations pour automatiser la gestion de bases de données, conteneurs et comptes Azure Cosmos DB avec des modèles Azure Resource Manager. Les exemples qu’il contient ne portent que sur les comptes d’API Gremlin. Pour obtenir des exemples liés à d’autres comptes de type d’API, voir les articles Utiliser des modèles Resource Manager avec l’API d’Azure Cosmos DB pour [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) et [Table](manage-table-with-resource-manager.md).

## Créer une API Azure Cosmos DB pour un compte, une base de données et une collection MongoDB<a id="create-resource"></a>

Créez des ressources Azure Cosmos DB à l’aide d’un modèle Azure Resource Manager. Ce modèle crée un compte Azure Cosmos pour l’API Gremlin avec deux graphiques qui partagent un débit de 400 RU/s au niveau de la base de données. Copiez le modèle et déployez-le comme indiqué ci-dessous ou consultez la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) et procédez au déploiement à partir du Portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

> [!NOTE]
> Les noms de comptes doivent être en minuscules et inférieures à 31 caractères.

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, **copiez** le script et sélectionnez **Essayer** pour ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

La commande `az cosmosdb show` montre le compte Azure Cosmos nouvellement créé, une fois que ce dernier a été configuré. Si vous choisissez d’utiliser une version d’Azure CLI installée localement au lieu d’utiliser Cloud Shell, consultez l’article [Interface de ligne de commande Azure (CLI)](/cli/azure/).

## Mettre à jour le débit (RU/s) sur une base de données <a id="database-ru-update"></a>

Le modèle suivant met à jour le débit d’une base de données. Copiez le modèle et déployez-le comme indiqué ci-dessous ou consultez la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-database-ru-update/) et procédez au déploiement à partir du Portail Azure. Vous pouvez également télécharger le modèle vers votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

[!code-json[cosmosdb-gremlin-database-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Déployer un modèle de base de données avec Azure CLI

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **Essayer** afin d’ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Mettre à jour le débit (RU/s) sur un graphique <a id="graph-ru-update"></a>

Le modèle suivant met à jour le débit d’un graphique. Copiez le modèle et déployez-le comme indiqué ci-dessous ou consultez la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-graph-ru-update/) et procédez au déploiement à partir du Portail Azure. Vous pouvez également télécharger le modèle vers votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

[!code-json[cosmosdb-gremlin-graph-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json)]

### <a name="deploy-graph-template-via-azure-cli"></a>Déployer un modèle de graphique avec Azure CLI

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **Essayer** afin d’ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName graphName=$graphName throughput=$throughput
```

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
- [Schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modèles Démarrage rapide Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)