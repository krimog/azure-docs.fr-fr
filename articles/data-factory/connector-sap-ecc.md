---
title: Copier des données de SAP ECC avec Azure Data Factory
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de SAP ECC vers des magasins de données récepteurs pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 526f85ca4b8854a36232c75a55847a73a8d372cc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680310"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copier des données de SAP ECC avec Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de SAP ECC (SAP Enterprise Central Component). Pour plus d’informations, consultez l’article [Vue d’ensemble d’activité de copie](copy-activity-overview.md).

>[!TIP]
>Pour en savoir plus sur la prise en charge générale de l'intégration de données SAP par ADF, consultez le [livre blanc Intégration de données SAP à l'aide d'Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) qui contient une présentation détaillée, une comparaison et des conseils.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SAP ECC est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de SAP ECC vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SAP ECC prend en charge ce qui suit :

- Copie de données à partir de SAP ECC sur SAP NetWeaver version 7.0 et ultérieures.
- Copie de données à partir de tous les objets exposés par les services SAP ECC OData, tels que :

  - Tables ou affichages SAP.
  - Objets de l’interface de programmation d’applications métier [BAPI].
  - Extracteurs de données.
  - Des données ou des documents intermédiaires (IDOC) envoyés à SAP Process Integration (PI) qui peuvent être reçus en tant que OData par le biais d’adaptateurs relatifs.

- Copie de données en utilisant une authentification de base.

>[!TIP]
>Pour copier des données à partir de SAP ECC via une table ou un affichage SAP, utilisez le connecteur de [Table SAP](connector-sap-table.md), qui est plus rapide et plus évolutif.

## <a name="prerequisites"></a>Prérequis

En règle générale, SAP ECC expose des entités par le biais de services OData via la passerelle SAP. Pour utiliser ce connecteur SAP ECC, vous devez :

- **Configurer la passerelle SAP**. Sur les serveurs avec une version de SAP NetWeaver supérieure à la version 7.4, la passerelle SAP est déjà installée. Pour les versions antérieures, vous devez installer la passerelle SAP incorporée ou le système hub de passerelle SAP avant d’exposer les données SAP ECC par le biais des services OData. Pour configurer la passerelle SAP, consultez le [guide d’installation](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Activer et configurer le service SAP OData**. Vous pouvez activer le service OData par le biais de TCODE SICF en quelques secondes. Vous pouvez également configurer les objets qui doivent être exposés. Pour plus d’informations, consultez la page [guide pas à pas](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des détails sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur SAP ECC.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP ECC sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `type` doit être définie sur `SapEcc`. | OUI |
| `url` | L’URL du service OData SAP ECC. | OUI |
| `username` | Le nom d’utilisateur utilisé pour se connecter à SAP ECC. | Non |
| `password` | Le mot de passe en texte en clair utilisé pour se connecter à SAP ECC. | Non |
| `connectVia` | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Pour plus d’informations, consultez la section [Prérequis](#prerequisites). Si vous ne spécifiez pas de runtime, le runtime d’intégration Azure par défaut est utilisé. | Non |

### <a name="example"></a>Exemples

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données](concepts-datasets-linked-services.md). La section suivante fournit la liste des propriétés prises en charge par le jeu de données de SAP ECC.

Pour copier des données de SAP ECC, définissez la propriété `type` du jeu de données sur `SapEccResource`.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `path` | Chemin de l’entité OData SAP ECC. | OUI |

### <a name="example"></a>Exemples

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). La section suivante fournit la liste des propriétés prises en charge par la source de SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC en tant que source

Pour copier des données de SAP ECC, définissez la propriété `type` dans la section `source` de l’activité de copie sur `SapEccSource`.

Les propriétés prises en charge dans la section `source` de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `SapEccSource` de la section `type` d’activité de copie doit être définie sur `source`. | OUI |
| `query` | Les options de requête OData pour filtrer les données. Par exemple :<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Le connecteur SAP ECC copie les données à partir de l’URL combinée :<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Pour plus d’informations, consultez [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Non |

### <a name="example"></a>Exemples

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mappage de type de données pour SAP ECC

Lorsque vous copiez des données de SAP ECC, les mappages suivants sont utilisés entre les types de données OData pour les données SAP ECC et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données OData | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Les types de données complexes ne sont actuellement pas pris en charge.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité de recherche

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie d’Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
