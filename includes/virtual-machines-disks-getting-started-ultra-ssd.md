---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 838037804baad9105b4636934de957c2e5f3e810
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612054"
---
# <a name="using-azure-ultra-disks"></a>Utilisation de disques Ultra Azure

Les disques Ultra Azure permettent un stockage de disque à haut débit avec un nombre élevé d’IOPS et une faible latence homogène pour les machines virtuelles Azure IaaS. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Entre autres avantages, les disques Ultra permettent de changer dynamiquement les performances des disques SSD en fonction de vos charges de travail, sans avoir à redémarrer les machines virtuelles. Les disques Ultra sont adaptés aux charges de travail qui consomment beaucoup de données, par exemple SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions.

## <a name="ga-scope-and-limitations"></a>Étendue et limitations de la version en disponibilité générale

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Déterminer la taille de la machine virtuelle et la disponibilité des régions

Pour tirer parti des disques Ultra, vous devez déterminer la zone de disponibilité dans laquelle vous vous trouvez. Toutes les régions ne prennent pas en charge toutes les tailles de machine virtuelle avec des disques Ultra. Pour déterminer si votre région, votre zone et votre taille de machine virtuelle prend en charge les disques Ultra, exécutez l’une des commandes suivantes, assurez-vous d’abord de remplacer les valeurs **Région**, **vmSize**et **Abonnement** :

Interface CLI :

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell :

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

La réponse aura un format similaire au suivant, où X correspond à la zone à utiliser pour le déploiement dans la région que vous avez choisie. X peut être 1, 2 ou 3.

Conservez la valeur **Zones**, qui représente votre zone de disponibilité. Vous en aurez besoin pour déployer un disque Ultra.

|ResourceType  |Nom  |Location  |Zones  |Restriction  |Fonctionnalité  |Valeur  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> En l’absence de réponse de la commande, la taille de machine virtuelle sélectionnée n’est pas prise en charge avec les disques Ultra dans la région sélectionnée.

Maintenant que vous savez quelle zone déployer, suivez les étapes de déploiement décrites dans cet article pour déployer une machine virtuelle avec un disque Ultra attaché ou attacher un disque Ultra à une machine virtuelle existante.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Déployer un disque Ultra à l’aide d’Azure Resource Manager

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Pour obtenir la liste des tailles de machines virtuelles prises en charge, consultez la section [Étendue et limitations de la version en disponibilité générale](#ga-scope-and-limitations).

Pour créer une machine virtuelle avec plusieurs disques Ultra, consultez l’exemple [Créer une machine virtuelle avec plusieurs disques Ultra](https://aka.ms/ultradiskArmTemplate).

Si vous avez l’intention d’utiliser votre propre modèle, assurez-vous que la valeur **apiVersion** pour `Microsoft.Compute/virtualMachines` et `Microsoft.Compute/Disks` est définie sur `2018-06-01` (ou version ultérieure).

Définissez la référence SKU du disque sur **UltraSSD_LRS**, puis définissez la capacité du disque, le nombre d’IOPS, la zone de disponibilité et le début (en Mbits/s) pour créer votre disque Ultra.

Une fois que la machine virtuelle est provisionnée, vous pouvez partitionner et formater les disques de données, puis les configurer pour vos charges de travail.

## <a name="deploy-an-ultra-disk-using-cli"></a>Déployer un disque Ultra via l’interface CLI

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Pour obtenir la liste des tailles de machines virtuelles prises en charge, consultez la section [Étendue et limitations de la version en disponibilité générale](#ga-scope-and-limitations).

Pour attacher un disque Ultra, vous devez créer une machine virtuelle capable d’utiliser des disques Ultra.

Remplacez ou définissez les variables **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** par votre propres valeurs. Définissez **$zone** sur la valeur de votre zone de disponibilité, que vous avez obtenue grâce à la procédure décrite au [début de cet article](#determine-vm-size-and-region-availability). Ensuite, exécutez la commande CLI suivante pour créer une machine virtuelle capable de gérer les disques SSD Ultra :

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Créer un disque Ultra via l’interface CLI

Maintenant que vous disposez d’une machine virtuelle capable d’attacher des disques Ultra, vous pouvez créer un disque Ultra et l’attacher à cette machine.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Attacher un disque Ultra à une machine virtuelle via l’interface CLI

Si votre machine virtuelle se trouve dans une région/zone de disponibilité capable d’utiliser les disques Ultra, vous pouvez utiliser des disques Ultra sans avoir à créer de machine virtuelle.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ajuster les performances d’un disque Ultra via l’interface CLI

Les disques Ultra offrent une capacité unique qui vous permet d’ajuster leurs performances. La commande suivante illustre l’utilisation de cette fonctionnalité :

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Déployer un disque Ultra à l’aide de PowerShell

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Pour obtenir la liste des tailles de machines virtuelles prises en charge, consultez la section [Étendue et limitations de la version en disponibilité générale](#ga-scope-and-limitations). pour plus de détails sur ces tailles de machines virtuelles.

Pour pouvoir utiliser des disques Ultra, vous devez créer une machine virtuelle capable d’utiliser ce type de disque. Remplacez ou définissez les variables **$resourcegroup** et **$vmName** par vos propres valeurs. Définissez **$zone** sur la valeur de votre zone de disponibilité, que vous avez obtenue grâce à la procédure décrite au [début de cet article](#determine-vm-size-and-region-availability). Ensuite, exécutez la commande [New-AzVm](/powershell/module/az.compute/new-azvm) suivante pour créer une machine virtuelle capable de gérer les disques SSD Ultra :

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Créer un disque Ultra à l’aide de PowerShell

Maintenant que vous disposez d’une machine virtuelle capable d’utiliser des disques Ultra, vous pouvez créer un disque Ultra et l’attacher à cette machine :

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Attacher un disque Ultra à une machine virtuelle à l’aide de PowerShell

Si votre machine virtuelle se trouve dans une région/zone de disponibilité capable d’utiliser les disques Ultra, vous pouvez utiliser des disques Ultra sans avoir à créer de machine virtuelle.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajuster les performances d’un disque Ultra à l’aide de PowerShell

Les disques Ultra présentent une capacité unique, qui vous permet d’ajuster leurs performances. L’exemple de commande suivant ajuste les performances sans nécessiter le détachement du disque :

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer ce nouveau type de disque, [demandez l’accès via cette enquête](https://aka.ms/UltraDiskSignup).