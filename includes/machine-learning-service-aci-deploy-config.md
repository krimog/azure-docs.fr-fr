---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: b3c1ad48da0ef28c73e3af5d1e30414534f4e94d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799820"
---
Les entrées dans le document `deploymentconfig.json` correspondent aux paramètres pour [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `computeType` | N/D | La cible de calcul. Pour ACI, la valeur doit être `ACI`. |
| `containerResourceRequirements` | N/D | Conteneur pour les entités UC et mémoire. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Nombre de cœurs de processeur à allouer. Par défaut, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Quantité de mémoire (en Go) à allouer à ce service web. Par défaut, `0.5` |
| `location` | `location` | Région Azure dans laquelle déployer ce service web. Si elle n’est pas spécifiée, l’emplacement de l’espace de travail sera utilisé. Vous trouverez plus d’informations sur les régions disponibles ici : [Régions ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Indique s’il faut activer ou l’authentification pour ce service web. Par défaut, False |
| `sslEnabled` | `ssl_enabled` | Indique s’il faut activer le SSL pour ce service web. Valeur par défaut False. |
| `appInsightsEnabled` | `enable_app_insights` | Indique s’il faut activer AppInsights pour ce service Web. Par défaut, False |
| `sslCertificate` | `ssl_cert_pem_file` | Fichier de certificat requis si SSL est activé |
| `sslKey` | `ssl_key_pem_file` | Fichier de clé requis si SSL est activé |
| `cname` | `ssl_cname` | cname si SSL est activé |
| `dnsNameLabel` | `dns_name_label` | Étiquette du nom DNS pour le point de terminaison de scoring. Si elle n’est pas spécifiée, une étiquette de nom DNS unique sera générée pour le point de terminaison de scoring. |

Le code JSON suivant est un exemple de configuration de déploiement à utiliser avec l’interface CLI :

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```