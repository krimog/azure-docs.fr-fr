---
title: Plages d’adresses IP autorisées par le serveur d’API dans Azure Kubernetes Service (AKS)
description: Apprenez à sécuriser votre cluster à l’aide d’une plage d’adresses IP pour l’accès au serveur d’API dans Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/05/2019
ms.author: mlearned
ms.openlocfilehash: aa0cf1ef3f758d7aba4639d779bde90249d039cb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815674"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Sécuriser l’accès au serveur d’API à l’aide de plages d’adresses IP autorisées dans Azure Kubernetes Service (AKS)

Dans Kubernetes, le serveur d’API reçoit des requêtes pour effectuer des actions dans le cluster, comme la création de ressources ou la mise à l’échelle du nombre de nœuds. Le serveur d’API est le moyen centralisé pour interagir avec et gérer un cluster. Pour améliorer la sécurité du cluster et minimiser les attaques, le serveur d’API doit uniquement être accessible à partir d’un ensemble limité de plages d’adresses IP.

Cet article vous montre comment utiliser des plages d’adresses IP autorisées pour le serveur d’API pour définir quelles adresses IP et CIDR peuvent accéder plan de contrôle.

> [!IMPORTANT]
> Sur les nouveaux clusters, les plages d’adresses IP autorisées du serveur d’API sont uniquement prises en charge sur l’équilibreur de charge SKUS *Standard*. Les clusters existants avec l’équilibreur de charge SKU *De base* et les plages d’adresses IP autorisées du serveur d’API sont configurés pour continuer à fonctionner normalement. Ces clusters continueront de fonctionner pendant leur mise à niveau.

## <a name="before-you-begin"></a>Avant de commencer

Les plages d’adresses IP autorisées pour le serveur d’API ne fonctionnent que pour les nouveaux clusters AKS que vous créez. Cet article vous montre comment créer un cluster AKS à l’aide d’Azure CLI.

La version 2.0.76 d’Azure CLI (ou ultérieure) doit être installée et configurée. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Vue d’ensemble des plages d’adresses IP pour le serveur d’API

Le serveur d’API Kubernetes détermine la façon dont les API Kubernetes sous-jacentes sont exposées. Ce composant fournit l’interaction des outils de gestion, tels que `kubectl` ou le tableau de bord Kubernetes. AKS fournit un maître de cluster monolocataire doté d’un serveur d’API dédié. Par défaut, le serveur d’API reçoit une adresse IP publique, et vous devez contrôler l’accès à l’aide de contrôles d’accès en fonction du rôle (RBAC).

Pour sécuriser l’accès au plan de contrôle AKS/serveur d’API, normalement accessible, vous pouvez activer et utiliser des plages d’adresses IP autorisées. Ces plages d’adresses IP autorisées autorisent uniquement les plages d’adresses IP définies à communiquer avec le serveur d’API. Une demande adressée au serveur d’API depuis une adresse IP qui ne fait pas partie de ces plages d’adresses IP autorisées est bloquée. Continuez à utiliser RBAC pour autoriser des utilisateurs et les actions qu’ils demandent.

Pour plus d’informations sur le serveur d’API et les autres composants de cluster, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Créer un cluster AKS dont les plages d’adresses IP autorisées du serveur d’API sont activées

Les plages d’adresses IP autorisées pour le serveur d’API ne fonctionnent que pour les nouveaux clusters AKS. Créez un cluster au moyen de la commande [az aks create][az-aks-create] et spécifiez le paramètre *--api-server-authorized-ip-ranges* pour fournir une liste de plages d’adresses IP autorisées. Ces plages d’adresses IP sont généralement des plages d’adresses utilisées par vos réseaux locaux ou IP publiques. Lorsque vous spécifiez une plage CIDR, commencez par la première adresse IP dans la plage. Par exemple, *137.117.106.90/29* est une plage valide, mais assurez-vous que vous spécifiez la première adresse IP dans la plage, par exemple *137.117.106.88/29*.

> [!IMPORTANT]
> Par défaut, votre cluster utilise l’[équilibreur de charge de la référence SKU Standard][standard-sku-lb], vous pouvez utiliser celui-ci pour configurer la passerelle sortante. Lorsque vous activez des plages d’adresses IP autorisées pour le serveur d’API lors de la création du cluster, l’adresse IP publique de votre cluster est également autorisée par défaut en plus des plages que vous indiquez. Si vous spécifiez *""* ou aucune valeur pour *--api-server-authorized-ip-ranges*, les plages d’adresses IP autorisées du serveur d’API sont désactivées.

L’exemple suivant crée un cluster à nœud unique nommé *myAKSCluster* dans le groupe de ressources *myResourceGroup*, avec les plages d’adresses IP autorisées du serveur d’API activées. Les plages d’adresses IP autorisées sont *73.140.245.0/24* :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Vous devez ajouter ces plages à une liste verte :
> - Adresse IP publique du pare-feu
> - Toute plage qui représente les réseaux à partir desquels vous allez administrer le cluster
> - Si vous utilisez Azure Dev Spaces sur votre cluster AKS, vous devez autoriser des [plages supplémentaires en fonction de votre région][dev-spaces-ranges].

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Spécifier les adresses IP sortantes pour l’équilibreur de charge de la référence SKU Standard

Lors de la création d’un cluster AKS, si vous indiquez des préfixes ou des adresses IP sortantes pour le cluster, ceux-ci sont également autorisés. Par exemple :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Dans l’exemple ci-dessus, toutes les adresses IP fournies dans le paramètre *--load-balancer-outbound-ip-prefixes* sont autorisées avec les adresses IP du paramètre *--api-server-authorized-ip-ranges*.

Vous pouvez également spécifier le paramètre *--load-balancer-outbound-ip-prefixes* pour autoriser les préfixes des adresses IP sortantes de l’équilibreur de charge.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Autoriser uniquement l’adresse IP publique sortante de l’équilibreur de charge de la référence SKU Standard

Lorsque vous activez les plages d’adresses IP autorisées du serveur d’API à la création du cluster, l’adresse IP publique sortante pour l’équilibreur de charge de la référence SKU Standard de votre cluster est également autorisée par défaut, en plus des plages que vous indiquez. Pour autoriser uniquement l’adresse IP publique sortante de l’équilibreur de charge de la référence SKU Standard, utilisez *0.0.0.0/32* lors de la spécification du paramètre *--api-server-authorized-ip-ranges*.

Dans l’exemple suivant, seule l’adresse IP publique sortante de l’équilibreur de charge de la référence SKU Standard est autorisée, et vous ne pouvez accéder au serveur d’API qu’à partir des nœuds du cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Mettre à jour les plages d’adresses IP autorisées du serveur d’API sur un cluster

Pour mettre à jour les plages d’adresses IP autorisées du serveur d’API sur un cluster existant, utilisez la commande [az aks update][az-aks-update] avec les paramètres *--api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefixes*, *--load-balancer-outbound-ips* ou *--load-balancer-outbound-ip-prefixes*.

L’exemple suivant met à jour les plages d’adresses IP autorisées du serveur d’API sur le cluster nommé *myAKSCluster* dans le groupe de ressources *myResourceGroup*. La plage d’adresses IP à autoriser est *73.140.245.0/24* :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Vous pouvez également utiliser *0.0.0.0/32* quand vous spécifiez le paramètre *--api-server-authorized-ip-ranges* pour autoriser uniquement l’adresse IP publique de l’équilibreur de charge de la référence SKU Standard.

## <a name="disable-authorized-ip-ranges"></a>Désactiver les plages d’adresses IP autorisées

Pour désactiver les plages d’adresses IP autorisées, utilisez [az aks update][az-aks-update] et spécifiez une plage vide pour désactiver les plages d’adresses IP autorisées du serveur d’API. Par exemple :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez activé des plages d’adresses IP autorisées pour le serveur d’API. Cette approche est une partie de la façon dont vous pouvez exécuter un cluster AKS sécurisé.

Pour plus d’informations, consultez [Concepts de sécurité pour les applications et les clusters dans AKS][concepts-security] et [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans Azure Kubernetes Service][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
