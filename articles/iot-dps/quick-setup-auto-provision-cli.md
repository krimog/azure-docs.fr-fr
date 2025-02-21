---
title: Configurer un service Device Provisioning avec Azure CLI | Microsoft Docs
description: 'Démarrage rapide d’Azure : Configurer le service Azure IoT Hub Device Provisioning avec Azure CLI'
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3062fb640985498ba35e23f6310828a2bd59bfed
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "60363714"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Configurer le service IoT Hub Device Provisioning avec Azure CLI

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide de démarrage rapide utilise Azure CLI pour créer un hub IoT et un service IoT Hub Device Provisioning et pour lier les deux services. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Le hub IoT et le service d’approvisionnement que vous créez dans ce guide de démarrage rapide seront détectables publiquement comme points de terminaison DNS. Veillez à éviter toute information sensible si vous décidez de modifier les noms utilisés pour ces ressources.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *my-sample-resource-group* à l’emplacement *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> L’exemple crée le groupe de ressources dans l’emplacement USA Ouest. Vous pouvez afficher une liste des emplacements disponibles en exécutant la commande `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Créez un hub IoT avec la commande [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

L’exemple suivant crée un hub IoT nommé *my-sample-hub* à l’emplacement *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Créer un service d’approvisionnement

Créez un service d’approvisionnement avec la commande [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create). 

L’exemple suivant crée un service d’approvisionnement nommé *my-sample-dps* à l’emplacement *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> L’exemple crée le service d’approvisionnement dans l’emplacement USA Ouest. Vous pouvez afficher une liste des emplacements disponibles en exécutant la commande `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou en vous rendant sur la page [Statut Azure](https://azure.microsoft.com/status/) et en recherchant « Device Provisioning ». Dans les commandes, les emplacements peuvent être spécifiés dans un format d’un mot ou de plusieurs mots. Par exemple : westus, West US, WEST US, etc. La valeur ne respecte pas la casse. Si vous utilisez un format de plusieurs mots pour spécifier l’emplacement, entourez la valeur d’apostrophes. Par exemple, `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Obtenir la chaîne de connexion pour le hub IoT

Il faut que la chaîne de connexion de votre hub IoT lie ce dernier au service Device Provisioning. Utilisez la commande [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) pour obtenir la chaîne de connexion et utilisez sa sortie pour définir une variable à utiliser quand vous lierez les deux ressources. 

L’exemple suivant définit la variable *hubConnectionString* en valeur de la chaîne de connexion pour la clé primaire de la stratégie *iothubowner* du hub. Vous pouvez spécifier une stratégie différente avec le paramètre `--policy-name`. La commande utilise les options de [requête](/cli/azure/query-azure-cli) et de [sortie](/cli/azure/format-output-azure-cli#tsv-output-format) d’Azure CLI pour extraire la chaîne de connexion de la sortie de la commande.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Vous pouvez utiliser la commande `echo` pour voir la chaîne de connexion.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Ces deux commandes sont valides pour un hôte exécuté par Bash. Si vous utilisez un interpréteur de commande CMD/ Windows local ou un hôte PowerShell, vous devez modifier les commandes et utiliser la syntaxe appropriée à l’environnement.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Lier le hub IoT et le service d’approvisionnement

Liez le hub IoT et votre service d’approvisionnement avec la commande [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

L’exemple suivant lie un hub IoT nommé *my-sample-hub* à l’emplacement *westus* et un service Device Provisioning nommé *my-sample-dps*. Il se sert de la chaîne de connexion pour *my-sample-hub* stockée dans la variable *hubConnectionString* dans l’étape précédente.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Vérifier le service d’approvisionnement

Obtenez les informations de votre service d’approvisionnement avec la commande [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

L’exemple suivant obtient les informations d’un service d’approvisionnement nommé *my-sample-dps*. Le hub IoT lié est affiché dans la collection *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Si vous n’envisagez pas de continuer, vous pouvez utiliser les commandes suivantes pour supprimer le service d’approvisionnement, le hub IoT et le groupe de ressources et toutes ses ressources.

Pour supprimer le service d’approvisionnement, exécutez la commande [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) :

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Pour supprimer le hub IoT, exécutez la commande [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) :

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Pour supprimer un groupe de ressources et toutes ses ressources, exécutez la commande [az group delete](/cli/azure/group#az-group-delete) :

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez déployé un IoT Hub et une instance de service d’approvisionnement d’appareil, puis vous avez lié les deux ressources. Pour savoir comment utiliser cette configuration pour approvisionner un appareil simulé, référez-vous au démarrage rapide relatif à la création d’appareil simulé.

> [!div class="nextstepaction"]
> [Démarrage rapide pour créer un appareil simulé](./quick-create-simulated-device.md)

