---
title: Transformer des données à l’aide d’une activité Hadoop Hive dans Azure Data Factory
description: Découvrez comment vous pouvez utiliser l'activité Hive d’une fabrique de données Azure pour exécuter des requêtes Hive sur un cluster HDInsight à la demande/ou votre propre cluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: c423192624ecc76e839f9fee434956f4d57aefdc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683911"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformer des données à l’aide d’une activité Hadoop Hive dans Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-hive-activity.md)
> * [Version actuelle](transform-data-using-hadoop-hive.md)

L’activité Hive HDInsight d’un [pipeline](concepts-pipelines-activities.md) Data Factory exécute des requêtes Hive sur [votre propre](compute-linked-services.md#azure-hdinsight-linked-service) cluster ou sur un cluster [à la demande](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

Si vous découvrez Azure Data Factory, lisez la [présentation d’Azure Data Factory](introduction.md) et suivez le [Didacticiel : Transformer des données](tutorial-transform-data-spark-powershell.md) avant de lire cet article. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Détails de la syntaxe
| Propriété            | Description                                                  | Obligatoire |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nom de l’activité                                         | OUI      |
| description         | Texte décrivant la raison motivant l’activité.                | Non       |
| Type                | Pour l’activité Hive, le type d’activité est HDinsightHive.        | OUI      |
| linkedServiceName   | Référence au cluster HDInsight enregistré en tant que service lié dans Data Factory. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | OUI      |
| scriptLinkedService | Référence à un service lié de stockage Azure utilisé pour stocker le script Hive à exécuter. Si vous ne spécifiez pas ce service lié, le service lié Stockage Azure défini dans le service lié HDInsight est utilisé. | Non       |
| scriptPath          | Indiquez le chemin du fichier de script stocké dans le stockage Azure référencé par scriptLinkedService. Le nom de fichier respecte la casse. | OUI      |
| getDebugInfo        | Spécifie quand les fichiers journaux sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par scriptLinkedService. Valeurs autorisées : None, Always ou Failure. Valeur par défaut : Aucune. | Non       |
| arguments           | Spécifie un tableau d’arguments pour un travail Hadoop. Les arguments sont passés sous la forme d’arguments de ligne de commande à chaque tâche. | Non       |
| defines             | Spécifier les paramètres sous forme de paires clé/valeur pour le référencement au sein du script Hive. | Non       |
| queryTimeout        | Valeur du délai d'expiration de la requête (en minutes). Applicable lorsque le cluster HDInsight est activé avec le Pack Sécurité Entreprise. | Non       |

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité pig](transform-data-using-hadoop-pig.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité d’exécution du lot Machine Learning](transform-data-using-machine-learning.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
