---
title: Schéma d’événement Machine Learning d’Azure Event Grid
description: Décrit les propriétés qui sont fournies pour les événements Espace de travail Machine Learning avec Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 6c2a8fa57bf6e3a552da57588bdbe752ef0d22e2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608593"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Schéma des événements Azure Event Grid pour Azure Machine Learning

Cet article décrit les propriétés et le schéma des événements de l’espace de travail Machine Learning. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

Pour obtenir la liste des exemples de scripts et des didacticiels, consultez [Source d’événement AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Types d’événement disponibles

Azure Machine Learning émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | Déclenché lors de l’inscription réussie d’un nouveau modèle ou d’une nouvelle version de modèle. |
| Microsoft.MachineLearningServices.ModelDeployed | Déclenché lorsque le ou les modèles ont été déployés avec succès sur un point de terminaison. |
| Microsoft.MachineLearningServices.RunCompleted | Déclenché lorsqu’une exécution s’est terminée avec succès. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Déclenché lorsqu’un superviseur de dérive de données détecte une dérive. |

## <a name="the-contents-of-an-event-response"></a>Le contenu d’une réponse à un événement

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison d’abonnement.

Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Événement Microsoft.MachineLearningServices.ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Événement Microsoft.MachineLearningServices.ModelDeployed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Événement Microsoft.MachineLearningServices.RunCompleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Événement Microsoft.MachineLearningServices.DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| topic | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| data | object | Données d’événement de stockage Blob. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes pour chaque type d’événement :

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| ModelName | string | Le nom du modèle qui a été inscrit. |
| ModelVersion | int | La version du modèle qui a été inscrit. |
| ModelTags | object | Les balises du modèle qui a été inscrit. |
| ModelProperties | object | Propriétés du modèle qui a été inscrit. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| ServiceName | string | Nom du service déployé. |
| ServiceComputeType | string | Type de calcul (par exemple ACI, AKS) du service déployé. |
| ModelIds | string | Liste d’ID de modèles séparés par des virgules. Les ID des modèles déployés dans le service. |
| ServiceTags | object | Balises du service déployé. |
| ServiceProperties | object | Propriétés du service déployé. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| ExperimentId | string | ID de l’expérience à laquelle l’exécution appartient. |
| ExperimentName | string | Nom de l’expérience à laquelle l’exécution appartient. |
| RunId | string | ID de l’exécution qui a été effectuée. |
| RunType | string | Type d’exécution de l’exécution terminée. |
| RunTags | object | Balises de l’exécution terminée. |
| RunProperties | object | Propriétés de l’exécution terminée. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| DataDriftId | string | ID du superviseur de dérive de données qui a déclenché l’événement. |
| DataDriftName | string | Nom du superviseur de dérive de données qui a déclenché l’événement. |
| RunId | string | ID de l’exécution qui a détecté une dérive des données. |
| BaseDatasetId | string | ID du jeu de données de base utilisé pour détecter la dérive. |
| TargetDatasetId | string | ID du jeu de données cible utilisé pour détecter la dérive. |
| DriftCoefficient | double | Résultat du coefficient qui a déclenché l’événement. |
| StartTime | datetime | Heure de début de la série chronologique du jeu de données cible qui a entraîné la détection de dérive.  |
| EndTime | datetime | Heure de fin de la série chronologique du jeu de données cible qui a entraîné la détection de dérive. |


## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md)
* Pour une introduction à l’utilisation d’Azure Event Grid avec Azure Machine Learning, consultez [Consommer des événements Azure Machine Learning](/azure/machine-learning/service/concept-event-grid-integration)
* Pour obtenir un exemple d’utilisation d’Azure Event Grid avec Azure Machine Learning, consultez [Créer des flux de travail Machine Learning basés sur les événements](/azure/machine-learning/service/event-schema-machine-learning)