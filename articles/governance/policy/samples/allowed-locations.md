---
title: Exemple - Emplacements autorisés
description: Cet exemple de définition de stratégie exige que toutes les ressources soient déployées sur les emplacements approuvés.
ms.date: 01/26/2019
ms.topic: sample
ms.openlocfilehash: 7561e57a00f440e50701fa75bd54676ad014e1d5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74071936"
---
# <a name="sample---allowed-region-locations"></a>Exemple - Emplacements régionaux autorisés

Cette stratégie vous permet de restreindre les emplacements que votre organisation peut spécifier lors du déploiement de ressources. Utilisez-la pour appliquer vos exigences de conformité géographique. Exclut les groupes de ressources, Microsoft.azureactivedirectory/b2cdirectories et les ressources qui utilisent la région « globale ». Vous spécifiez un tableau d’emplacements autorisés.

Vous pouvez déployer cette exemple de stratégie à l’aide des éléments suivants :

- [Portail Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interface de ligne de commande Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Exemple de stratégie

### <a name="policy-definition"></a>Définition de stratégie

Définition de stratégie JSON composée complète, utilisée par l’API REST, les boutons « Déployer sur Azure » et manuellement dans le portail.

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Si vous créez manuellement une stratégie dans le portail, utilisez les parties **properties.parameters** et **properties.policyRule** du code ci-dessus. Placez les deux sections entre accolades `{}` pour en faire du code JSON valide.

### <a name="policy-rules"></a>Règles de stratégie

Code JSON définissant les règles de la stratégie, utilisé par l’interface de ligne de commande Azure (Azure CLI) et Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Paramètres de stratégie

Code JSON définissant les paramètres de la stratégie, utilisé par l’interface de ligne de commande Azure (Azure CLI) et Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>parameters

|Nom |Type |Champ |Description |
|---|---|---|---|
|listOfAllowedLocations |Array |locations |Liste des emplacements autorisés|

Lors de la création d’une affectation via PowerShell ou l’interface de ligne de commande Azure (Azure CLI), les valeurs de paramètres peuvent être passées au format JSON dans une chaîne ou via un fichier à l’aide de `-PolicyParameter` (PowerShell) ou de `--params` (Azure CLI).
PowerShell prend également en charge `-PolicyParameterObject` qui nécessite de passer une table de hachage Nom/Valeur à l’applet de commande, où **Nom** est le nom du paramètre et **Valeur** est la valeur unique ou le tableau des valeurs transmises pendant l’affectation.

Dans cet exemple de paramètre, seuls les emplacements _eastus2_ ou _westus_ seront autorisés.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Portail Azure

[![Déployer l’exemple de stratégie dans Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![Déployer l’exemple de stratégie dans Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Déployer avec Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Supprimer avec Azure PowerShell

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Explication Azure PowerShell

Les scripts de déploiement et de suppression utilisent les commandes suivantes. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Crée une définition Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Obtient un groupe de ressources unique. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Crée une affectation Azure Policy. Dans cet exemple, nous lui fournissons une définition, mais elle peut également en prendre l’initiative. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Supprime une affectation Azure Policy existante. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Supprime une définition Azure Policy existante. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Supprimer avec Azure CLI

Exécutez les commandes suivantes pour supprimer l’affectation et la définition précédentes :

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Explication Azure CLI

| Commande | Notes |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Crée une définition Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Obtient un groupe de ressources unique. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Crée une affectation Azure Policy. Dans cet exemple, nous lui fournissons une définition, mais elle peut également en prendre l’initiative. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Supprime une affectation Azure Policy existante. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Supprime une définition Azure Policy existante. |

## <a name="rest-api"></a>API REST

Il existe plusieurs outils permettant d’interagir avec l’API REST Resource Manager, comme [ARMClient](https://github.com/projectkudu/ARMClient) ou PowerShell.

### <a name="deploy-with-rest-api"></a>Déploiement avec l’API REST

- Créer la définition de stratégie (étendue de l’abonnement). Utilisez le code JSON de [définition de stratégie](#policy-definition) pour le corps de la requête.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Créer l’affectation de stratégie (étendue de groupe de ressources)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  Utilisez l’exemple de code JSON suivant pour le corps de la requête :

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Supprimer avec l’API REST

- Supprimer l’affectation de stratégie

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- Supprimer la définition de stratégie

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Explication de l’API REST

| Service | Groupe | Opération | Notes |
|---|---|---|---|
| Gestion des ressources | Définitions de stratégies | [Créer](/rest/api/resources/policydefinitions/createorupdate) | Crée une définition Azure Policy au niveau d’un abonnement. Alternative : [Créer au niveau du groupe d’administration](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gestion des ressources | Affectations de stratégies | [Créer](/rest/api/resources/policyassignments/create) | Crée une affectation Azure Policy. Dans cet exemple, nous lui fournissons une définition, mais elle peut également en prendre l’initiative. |
| Gestion des ressources | Affectations de stratégies | [Supprimer](/rest/api/resources/policyassignments/delete) | Supprime une affectation Azure Policy existante. |
| Gestion des ressources | Définitions de stratégies | [Supprimer](/rest/api/resources/policydefinitions/delete) | Supprime une définition Azure Policy existante. Alternative : [Supprimer au niveau du groupe d’administration](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres [exemples Azure Policy](index.md)
- Consulter [Structure de définition Azure Policy](../concepts/definition-structure.md)