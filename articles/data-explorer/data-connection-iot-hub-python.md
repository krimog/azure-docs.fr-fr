---
title: Créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de Python
description: Dans cet article, vous allez apprendre à créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1e6cd94e18382b3e66fc6a9c284539bc178e199c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031042"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de Python (préversion)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Azure Data Explorer offre une ingestion (chargement de données) à partir de hubs d’événements, de hubs IoT et d’objets blob écrits dans des conteneurs d’objets blob. Dans cet article, vous créez une connexion de données au hub IoT pour Azure Data Explorer à l’aide de Python.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

* Créez [un cluster et une base de données](create-cluster-database-csharp.md).

* Créez [une table et un mappage de colonnes](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* Définissez [des stratégies de base de données et de table](database-table-policies-csharp.md) (facultatif).

* Créez un [hub IoT avec une stratégie d’accès partagé configurée](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Ajouter une connexion de données au hub IoT 

L’exemple suivant montre comment ajouter par programmation une connexion de données au hub IoT. Consultez [Connecter la table Azure Data Explorer au hub IoT](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) pour ajouter une connexion de données au hub IoT à l’aide du Portail Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| client_secret | *xxxxxxxxxxxxxx* | Clé secrète client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resource_group_name | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| nom_cluster | *mykustocluster* | Nom de votre cluster.|
| database_name | *mykustodatabase* | Nom de la base de données cible dans votre cluster.|
| data_connection_name | *myeventhubconnect* | Nom souhaité de votre connexion de données.|
| table_name | *StormEvents* | Nom de la table cible dans la base de données cible.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nom de votre mappage de colonnes associé à la table cible.|
| data_format | *csv* | Format de données du message.|
| iot_hub_resource_id | *ID de ressource* | ID de ressource de votre hub IoT qui contient les données à des fins d’ingestion.|
| shared_access_policy_name | *iothubforread* | Nom de la stratégie d’accès partagé qui définit les autorisations pour que les périphériques et les services se connectent au hub IoT. |
| consumer_group | *$Default* | Groupe de consommateurs de votre hub d’événements.|
| location | *USA Centre* | Emplacement de la ressource de connexion de données.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]