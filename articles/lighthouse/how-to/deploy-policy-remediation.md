---
title: Déployer une stratégie pouvant être corrigée
description: Découvrez comment intégrer un client à la gestion des ressources déléguées Azure, permettant ainsi que ses ressources soient accessibles et gérables via votre propre locataire.
ms.date: 10/11/2019
ms.topic: overview
ms.openlocfilehash: 662daeb305856fb36bfb84f98e80bedf48b22756
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132477"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Déployer une stratégie pouvant être corrigée dans un abonnement délégué

[Azure Lighthouse](../overview.md) permet aux fournisseurs de services de créer et de modifier des définitions de stratégies au sein d’un abonnement délégué. Toutefois, pour déployer des stratégies qui utilisent une [tâche de correction](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (autrement dit, des stratégies avec l’effet [deployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) ou [modify](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)), vous devez créer une [identité managée](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) dans le locataire du client. Cette identité managée peut être utilisée par Azure Policy pour déployer le modèle dans la stratégie. Il existe des étapes à suivre pour activer ce scénario, à la fois quand vous intégrez le client pour la gestion des ressources déléguées Azure et quand vous déployez la stratégie elle-même.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Créer un utilisateur qui peut attribuer des rôles à une identité managée dans le locataire du client

Quand vous intégrez un client pour la gestion des ressources déléguées Azure, vous utilisez un [modèle Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) avec un fichier de paramètres qui définit les utilisateurs, les groupes d’utilisateurs et les principaux de service de votre locataire gestionnaire qui pourront accéder aux ressources déléguées dans le locataire du client. Dans votre fichier de paramètres, chacun de ces utilisateurs (**principalId**) se voit attribuer un [rôle intégré](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roleDefinitionId**) qui définit le niveau d’accès.

Pour permettre à un **principalId** de créer une identité managée dans le locataire du client, vous devez définir son **roleDefinitionId** sur **Administrateur de l’accès utilisateur**. Bien que ce rôle ne soit généralement pas pris en charge, il peut être utilisé dans ce scénario spécifique, permettant aux utilisateurs disposant de cette autorisation d’affecter un ou plusieurs rôles intégrés spécifiques à des identités managées. Ces rôles sont définis dans la propriété **delegatedRoleDefinitionIds**. Vous pouvez inclure ici n’importe quel rôle intégré, sauf Administrateur de l’accès utilisateur ou Propriétaire.

Une fois le client intégré, le **principalId** créé dans cette autorisation pourra affecter ces rôles intégrés à des identités managées dans le locataire du client. Toutefois, il n’aura aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur.

L’exemple ci-dessous montre un **principalId** qui aura le rôle Administrateur de l’accès utilisateur. Cet utilisateur pourra attribuer deux rôles intégrés à des identités managées dans le locataire du client : Contributeur et Contributeur Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Déployer des stratégies pouvant être corrigées

Une fois que vous avez créé l’utilisateur avec les autorisations nécessaires comme décrit ci-dessus, cet utilisateur peut déployer des stratégies dans le locataire du client qui utilisent des tâches de correction.

Par exemple, supposez que vous souhaitez activer les diagnostics sur des ressources Azure Key Vault dans le locataire du client, comme illustré dans cet [exemple](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). Un utilisateur du client gestionnaire disposant des autorisations appropriées (comme décrit ci-dessus) déploierait un [modèle Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) pour activer ce scénario.

Notez que la création de l’attribution de stratégie à utiliser avec un abonnement délégué doit être effectuée par le biais d’API, et non dans le portail Azure. Dans ce cas, l’**apiVersion** doit être définie sur **2019-04-01-Preview**, qui comprend la nouvelle propriété **delegatedManagedIdentityResourceId**. Cette propriété vous permet d’inclure une identité managée qui réside dans le locataire du client (dans un abonnement ou un groupe de ressources qui a été intégré à la gestion des ressources déléguées Azure).

L’exemple suivant montre une attribution de rôle avec un **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Un [exemple similaire](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) est disponible pour illustrer comment déployer une stratégie qui ajoute ou supprime une balise (à l’aide de l’effet modify) à un abonnement délégué.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- En savoir plus sur les [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
