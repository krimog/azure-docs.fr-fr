---
title: Déployer sur Azure Container Instances à partir d’Azure Container Registry
description: Découvrez comment déployer des conteneurs dans Azure Container Instances à l’aide d’images conteneur stockées dans un registre de conteneurs Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 502f178b66e7ba233552d7db4e095363c8bb8628
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325557"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Déployer sur Azure Container Instances à partir d’Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) est un service de registre de conteneurs managé basé sur Azure, utilisé pour stocker des images de conteneurs Docker privés. Cet article explique comment déployer des images conteneur stockées dans un registre de conteneurs Azure sur Azure Container Instances.

## <a name="prerequisites"></a>Prérequis

**Azure Container Registry** : Vous avez besoin d’un registre de conteneurs Azure et au moins d’une image conteneur dans le registre pour effectuer les étapes décrites dans cet article. Si vous avez besoin d’un registre, consultez [Créer un registre de conteneurs à l’aide de Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI** : Les exemples de ligne de commande dans cet article utilisent [Azure CLI](/cli/azure/) et sont mis en forme pour le shell Bash. Vous pouvez [installer Azure CLI](/cli/azure/install-azure-cli) localement, ou bien utilisez [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurer l’authentification du registre

Dans les scénarios de production, l’accès à un registre de conteneur Azure doit être fourni à l’aide de [principaux de service](../container-registry/container-registry-auth-service-principal.md). Les principaux du service vous permettent de fournir un [contrôle d’accès en fonction du rôle](../container-registry/container-registry-roles.md) à vos images de conteneur. Par exemple, vous pouvez configurer un principal de service avec uniquement un accès d’extraction à un registre.

Dans la section suivante, vous créez un coffre de clés Azure et un principal de service et vous stockez des informations d’identification du principal de service dans le coffre. 

### <a name="create-key-vault"></a>Création d’un coffre de clés

Si vous n’avez pas encore un coffre dans [Azure Key Vault](../key-vault/key-vault-overview.md), créez-en un avec Azure CLI à l’aide des commandes suivantes.

Mettez à jour la variable `RES_GROUP` avec le nom d’un groupe de ressources existant où créer le coffre de clés, et `ACR_NAME` avec le nom de votre registre de conteneurs. Spécifiez un nom pour votre nouveau coffre de clés dans `AKV_NAME`. Le nom du coffre doit être unique au sein de Azure et doit être composé de 3 à 24 caractères alphanumériques, en commençant par une lettre, se terminant par une lettre ou un chiffre, et sans contenir de traits d’union consécutifs.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Créer un principal de service et stocker les informations d’identification

Vous devez maintenant créer un principal de service et stocker ses informations d’identification dans votre coffre de clés.

La commande suivante utilise [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] pour créer le principal du service, et [az keyvault secret set][az-keyvault-secret-set] pour stocker le **mot de passe** du principal de service dans le coffre.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

L’argument `--role` dans la commande précédente configure le principal de service avec le rôle *acrpull*, ce qui lui accorde uniquement un accès d’extraction au registre. Pour accorder les accès push et pull (envoi et tirage), affectez à l’argument `--role` la valeur *acrpush*.

Ensuite, stockez le *appId* du principal de service dans le coffre, qui est le **nom d’utilisateur** que vous passez à Azure Container Registry pour l’authentification.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Vous avez créé un coffre de clés Azure et il contient deux secrets :

* `$ACR_NAME-pull-usr`: ID de principal du service, pour une utilisation comme **nom d’utilisateur** du registre de conteneurs.
* `$ACR_NAME-pull-pwd`: mot de passe du principal du service, pour une utilisation comme **mot de passe** du registre de conteneurs.

Vous pouvez maintenant référencer ces secrets par nom lorsque vous ou vos applications et services extrayez des images du registre.

## <a name="deploy-container-with-azure-cli"></a>Déployer le conteneur avec Azure CLI

Maintenant que les informations d’identification du principal de service sont stockées dans les secrets de Azure Key Vault, vos applications et vos services peuvent les utiliser pour accéder à votre registre privé.

Obtenez d’abord le nom du serveur de connexion du registre avec la commande [az acr show][az-acr-show]. Le nom du serveur de connexion est tout en minuscules et est similaire à `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Exécutez la commande [az container create][az-container-create]suivante pour déployer une instance de conteneur. La commande utilise les informations d’identification du principal du service stockées dans Azure Key Vault pour vous authentifier auprès de votre registre de conteneur et suppose que vous avez précédemment poussé l’image [aci-helloworld](container-instances-quickstart.md) dans votre registre. Mettez à jour la valeur `--image` si vous souhaitez utiliser une autre image à partir de votre registre.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

La valeur `--dns-name-label` doit être unique dans Azure, afin que la commande précédente ajoute un nombre aléatoire à l’étiquette de nom du DNS du conteneur. La sortie pour la commande affiche le nom de domaine complet du conteneur (FQDN), par exemple :

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Une fois le conteneur démarré avec succès, vous pouvez naviguer vers son FQDN dans votre navigateur pour vérifier que l’application s’exécute correctement.

## <a name="deploy-with-azure-resource-manager-template"></a>Déploiement avec un modèle Azure Resource Manager

Vous pouvez spécifier les propriétés de votre registre Azure Container Registry dans un modèle Azure Resource Manager en incluant la `imageRegistryCredentials` propriété dans la définition du groupe de conteneurs :

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Pour plus d’informations sur le référencement des secrets de Azure Key Vault dans un modèle Resource Manager, consultez [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Déploiement avec le Portail Azure

Si vous gérez des images de conteneur dans un registre Azure Container Registry, vous pouvez facilement créer un conteneur dans Azure Container Instances via le portail Azure. Lorsque vous utilisez le portail pour déployer une instance de conteneur à partir d’un registre de conteneurs, vous devez activer le [compte administrateur](../container-registry/container-registry-authentication.md#admin-account) du registre. Le compte d’administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. 

1. Dans le portail Azure, accédez à votre registre de conteneurs.

1. Pour confirmer que le compte administrateur est activé, sélectionnez **Touches d’accès**, et, sous **Utilisateur administrateur**, sélectionnez **Activer**.

1. Sélectionnez **Référentiels**, puis le référentiel dont proviendra le déploiement, cliquez avec le bouton droit sur la balise de l’image conteneur que vous souhaitez déployer et sélectionnez **Exécuter l’instance**.

    ![« Exécuter l’instance » dans Azure Container Registry sur le Portail Azure][acr-runinstance-contextmenu]

1. Entrez un nom pour le conteneur et un nom pour le groupe de ressources. Vous pouvez également modifier les valeurs par défaut si vous le souhaitez.

    ![Créer un menu pour Azure Container Instances][acr-create-deeplink]

1. Une fois le déploiement terminé, vous pouvez naviguer vers le groupe de conteneurs à partir du panneau Notifications pour trouver son adresse IP et autres propriétés.

    ![Affichage des détails pour le groupe de conteneurs d’Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’information sur l’authentification Azure Container Registry, consultez [S’authentifier avec un registre de conteneurs Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
