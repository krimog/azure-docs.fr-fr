---
title: 'Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC) et d’un modèle Resource Manager | Microsoft Docs'
description: Découvrez comment accorder un accès utilisateur aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC) en utilisant le modèle Azure Resource Manager.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: edb20221862e6439b3bc574995f4037cbc95f8f9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668880"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Didacticiel : Accorder un accès utilisateur aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC) et d’un modèle Resource Manager

Le [contrôle d'accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l'accès aux ressources Azure. Dans ce tutoriel, vous allez créer un groupe de ressources et autoriser un utilisateur à créer et à gérer des machines virtuelles dans le groupe de ressources. Ce tutoriel porte essentiellement sur le déploiement d’un modèle Resource Manager en vue d’accorder l’accès. Pour plus d’informations sur le développement de modèles Resource Manager, consultez la [documentation Resource Manager](/azure/azure-resource-manager/) et les [informations de référence sur les modèles](/azure/templates/microsoft.authorization/allversions
).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accorder l’accès à un utilisateur au niveau du groupe de ressources
> * Valider le déploiement
> * Nettoyer

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour ajouter et supprimer des attributions de rôles, vous devez disposer :

* d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)

## <a name="grant-access"></a>Accorder l'accès

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). D’autres modèles d’autorisation Azure sont disponibles [ici](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Pour déployer le modèle, sélectionnez **Try it** afin d’ouvrir Azure Cloud Shell, puis collez le script PowerShell suivant dans la fenêtre de l’interpréteur de commandes. Pour coller le code, cliquez avec le bouton droit sur la fenêtre de l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Valider le déploiement

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Ouvrez le groupe de ressources créé dans la dernière procédure. Le nom par défaut est le nom du projet avec **rg** ajouté.
1. Sélectionnez **Contrôle d’accès (IAM)** à partir du menu de gauche.
1. Sélectionnez **Attributions de rôles**. 
1. Dans **Nom**, entrez l’adresse e-mail que vous avez entrée dans la dernière procédure. Vous devez voir que l’utilisateur ayant l’adresse e-mail a le rôle **Contributeur de machines virtuelles**.

## <a name="clean-up"></a>Nettoyer

Pour supprimer le groupe de ressources créé dans la dernière procédure, sélectionnez **Try it** pour ouvrir Azure Cloud Shell, puis collez le script PowerShell suivant dans la fenêtre de l’interpréteur de commandes.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du contrôle RBAC et d’Azure PowerShell](tutorial-role-assignments-user-powershell.md)