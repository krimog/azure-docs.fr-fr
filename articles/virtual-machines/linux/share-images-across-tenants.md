---
title: Partager des images de la bibliothèque entre locataires dans Azure | Microsoft Docs
description: Découvrez comment partager des images de machine virtuelle entre des locataires Azure à l’aide des galeries d’images partagées.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbf42de406ecb0caed67b77743f376284ab64608
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706553"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partager des images de machine virtuelle de la galerie entre des locataires Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Vous ne pouvez pas utiliser le portail pour déployer une machine virtuelle à partir d’une image dans un autre locataire Azure. Pour créer une machine virtuelle à partir d’une image partagée entre des locataires, vous devez utiliser l’interface Azure CLI ou [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Créer une machine virtuelle à l’aide d’Azure CLI

Connectez le principal du service pour le locataire 1 à l’aide de l’ID d’application, de la clé d’application et de l’ID du locataire 1. Vous pouvez utiliser `az account show --query "tenantId"` pour obtenir les ID des locataires si nécessaire.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Connectez le principal du service pour le locataire 2 à l’aide de l’ID d’application, de la clé d’application et de l’ID du locataire 2 :

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Créez la machine virtuelle. Remplacez les informations dans l’exemple par vos propres données.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).