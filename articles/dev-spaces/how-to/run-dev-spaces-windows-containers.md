---
title: Utiliser Azure Dev Spaces pour interagir avec des conteneurs Windows
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/25/2019
ms.topic: conceptual
description: Découvrez comment exécuter Azure Dev Spaces sur un cluster existant avec des conteneurs Windows
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, conteneurs Windows
ms.openlocfilehash: 90d7c8e5fc08405178ab6596b765f289b9bd716f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582770"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Utiliser Azure Dev Spaces pour interagir avec des conteneurs Windows

Vous pouvez activer Azure Dev Spaces sur des espaces de noms Kubernetes nouveaux et existants. Azure Dev Spaces exécute et instrumente des services qui s’exécutent sur des conteneurs Linux. Ces services peuvent également interagir avec les applications qui s’exécutent sur des conteneurs Windows dans le même espace de noms. Cet article explique comment utiliser Azure Dev Spaces pour exécuter des services dans un espace de noms avec des conteneurs Windows existants.

## <a name="set-up-your-cluster"></a>Configurer votre cluster

Cet article part du principe que vous disposez déjà d’un cluster avec des pools de nœuds Linux et Windows. Si vous avez besoin de créer un cluster avec des pools de nœuds Linux et Windows, vous pouvez suivre les instructions fournies [ici][windows-container-cli].

Connectez-vous à votre cluster à l’aide de [kubectl][kubectl], le client de ligne de commande Kubernetes. Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][az-aks-get-credentials]. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get][kubectl-get] pour retourner une liste des nœuds du cluster.

```azurecli-interactive
kubectl get nodes
```

L’exemple de sortie suivant montre un cluster avec un nœud Windows et un nœud Linux. Avant de poursuivre, assurez-vous que l’État de chaque nœud est *Prêt*.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Appliquez une [teinte][using-taints] à vos nœuds Windows. La teinte sur vos nœuds Windows empêche Dev Spaces de planifier l’exécution de conteneurs Linux sur vos nœuds Windows. L’exemple de commande suivant applique une teinte au nœud Windows *aksnpwin987654* de l’exemple précédent.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Lorsque vous appliquez une teinte à un nœud, vous devez configurer une tolérance correspondante dans le modèle de déploiement de votre service pour exécuter celui-ci sur ce nœud. L’exemple d’application est déjà configuré avec une [tolérance correspondant][sample-application-toleration-example] à la teinte que vous avez configurée dans la commande précédente.

## <a name="run-your-windows-service"></a>Exécuter votre service Windows

Exécutez votre service Windows sur votre cluster AKS, et vérifiez que son état est *En cours d’exécution*. Cet article utilise un [exemple d’application][sample-application] pour montrer un service Windows et un service Linux s’exécutant sur votre cluster.

Clonez l’exemple d’application à partir de GitHub, puis accédez au répertoire `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` :

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

L’exemple d’application utilise [Helm][helm-installed] pour exécuter le service Windows sur votre cluster. Installez Helm sur votre cluster et accordez-lui les autorisations appropriées :

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Accédez au répertoire `charts` et exécutez le service Windows :

```console
cd charts/
helm install . --namespace dev
```

La commande ci-dessus utilise Helm pour exécuter votre service Windows dans l’espace de noms *dev*. Si vous n’avez pas d’espace de noms *dev*, celui-ci est créé.

Utilisez la commande `kubectl get pods` pour vérifier que votre service Windows est en cours d’exécution dans votre cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Activer Azure Dev Spaces

Activez Azure Dev Spaces dans l’espace de noms que vous avez utilisé pour exécuter votre service Windows. La commande suivante active Azure Dev Spaces dans l’espace de noms *dev* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Mettre à jour votre service Windows pour Azure Dev Spaces

Lorsque vous activez Azure Dev Spaces sur un espace de noms existant avec des conteneurs déjà en cours d’exécution, par défaut, Azure Dev Spaces essaie et instrumente tout nouveau conteneur s’exécutant dans cet espace de noms. Azure Dev Spaces essaie et instrumente également tout nouveau conteneur créé pour le service déjà en cours d’exécution dans l’espace de noms. Pour empêcher Azure Dev Spaces d’instrumenter un conteneur s’exécutant dans votre espace de noms, ajoutez l’en-tête *no-proxy* au fichier `deployment.yaml`.

Ajoutez `azds.io/no-proxy: "true"` au fichier `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Utilisez `helm list` pour indiquer le déploiement de votre service Windows :

```cmd
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

Dans l’exemple ci-dessus, le nom de votre déploiement est *Gilded-Jackal*. Mettez à jour votre service Windows avec la nouvelle configuration en utilisant `helm upgrade` :

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

Comme vous avez mis à jour votre fichier `deployment.yaml`, Azure Dev Spaces n’essaie et n’instrumente pas votre service.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Exécuter votre application Linux avec Azure Dev Spaces

Accédez au répertoire `webfrontend` et utilisez les commandes `azds prep` et `azds up` pour exécuter votre application Linux sur votre cluster.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

La commande `azds prep --public` génère le graphique Helm et les fichiers Dockerfile pour votre application. La commande `azds up` exécute votre service dans l’espace de noms.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Vous pouvez voir le service en cours d’exécution en ouvrant l’URL publique affichée dans la sortie de la commande azds up. Dans cet exemple, l’URL publique est `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Accédez au service dans un navigateur, puis cliquez sur *À propos de* dans la partie supérieure. Vérifiez que vous voyez un message du service *mywebapi* contenant la version de Windows que le conteneur utilise.

![Exemple d’application présentant la version de Windows de mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Développement en équipe dans Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
