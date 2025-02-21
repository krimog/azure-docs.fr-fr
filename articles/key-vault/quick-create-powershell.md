---
title: Démarrage rapide Azure - Définir et récupérer un secret depuis Key Vault à l’aide de PowerShell | Microsoft Docs
description: ''
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 5753384d12d31016dff7a33175899835c8bd1fec
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259254"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](key-vault-overview.md). Dans ce démarrage rapide, vous allez utiliser PowerShell pour créer un coffre de clés. Ensuite, vous stockerez un secret dans le coffre que vous venez de créer.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, ce tutoriel nécessite le module Azure PowerShell version 1.0.0 ou ultérieure. Tapez `$PSVersionTable.PSVersion` pour connaître la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzAccount` pour créer une connexion avec Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Créer un coffre de clés

Vous allez à présent créer un coffre de clés. Lorsque vous effectuez cette opération, vous devez disposer de certaines informations :

Bien que nous utilisions « Contoso KeyVault2 » comme nom pour notre coffre de clés dans ce démarrage rapide, vous devez utiliser un nom unique.

- **Nom du coffre** Contoso-Vault2.
- **Nom du groupe de ressources** ContosoResourceGroup.
- **Emplacement** USA Est.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

La sortie de cette cmdlet affiche les propriétés du coffre de clés que vous venez de créer. Notez les deux propriétés ci-dessous :

* **Nom du coffre** : dans l’exemple, il s’agit de **Contoso-Vault2**. Vous allez utiliser ce nom pour les autres applets de commande Key Vault.
* **URI du coffre** : dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

Une fois le coffre créé, votre compte Azure est le seul compte autorisé à exécuter des actions sur ce nouveau coffre.

![Sortie après l’exécution de la commande de création du coffre de clés](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Ajout d’un secret au coffre de clés

Pour ajouter un secret au coffre, vous devez effectuer deux opérations. Dans ce cas précis, vous ajoutez un mot de passe qu’une application est susceptible d’utiliser. Le mot de passe est appelé **ExamplePassword** et stocke la valeur **hVFkk965BuUv**.

Commencez par convertir la valeur **hVFkk965BuUv** en une chaîne sécurisée en tapant la commande suivante :

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Ensuite, tapez les commandes PowerShell ci-après pour créer dans Key Vault un secret appelé **ExamplePassword**, avec la valeur **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Vous venez de créer un coffre de clés, d’y stocker un secret et de récupérer ce dernier.

## <a name="clean-up-resources"></a>Supprimer des ressources

 D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, le coffre Key Vault et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés Key Vault, et vous y avez stocké un secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](key-vault-overview.md)
- Consulter la référence des [applets de commande Azure PowerShell Key Vault](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- En savoir plus sur les [clés, secrets et certificats](about-keys-secrets-and-certificates.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](key-vault-best-practices.md)
