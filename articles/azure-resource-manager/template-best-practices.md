---
title: Meilleures pratiques relatives aux modèles Azure Resource Manager
description: Décrit les approches recommandées pour la création de modèles Azure Resource Manager. Fournit des suggestions pour éviter des problèmes qui se produisent couramment lors de l’utilisation de modèles.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: tomfitz
ms.openlocfilehash: bd3167b7f0daf7ebd595b2c33b1147140415c3de
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983819"
---
# <a name="azure-resource-manager-template-best-practices"></a>Meilleures pratiques relatives aux modèles Azure Resource Manager

Cet article fournit des suggestions sur la façon de construire votre modèle Resource Manager. Celles-ci vous aident à éviter des problèmes qui se produisent couramment lors de l’utilisation d’un modèle pour déployer une solution.

Pour obtenir des suggestions sur la façon de gérer vos abonnements Azure, consultez [Structure d’entreprise Azure : gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Pour obtenir des suggestions sur la création de modèles qui fonctionnent dans tous les environnements cloud Azure, voir [Développer des modèles Azure Resource Manager de cohérence du cloud](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limites de modèle

Limitez la taille de votre modèle à 4 Mo et celle de chaque fichier de paramètres à 64 ko. La limite de 4 Mo s’applique à l’état final du modèle une fois développé avec les définitions des ressources itératives et les valeurs des variables et des paramètres. 

Vous devez également respecter les limites suivantes :

* 256 paramètres
* 256 variables
* 800 ressources (incluant le nombre de copies)
* 64 valeurs de sortie
* 24 576 caractères dans une expression de modèle

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](resource-group-linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).

## <a name="resource-group"></a>Resource group

Lorsque vous déployez des ressources dans un groupe de ressources, celui-ci stocke des métadonnées relatives aux ressources. Les métadonnées sont stockées à l'emplacement du groupe de ressources.

Si la région du groupe de ressources est temporairement indisponible, vous ne pourrez pas mettre à jour les ressources du groupe, car les métadonnées ne seront pas disponibles. Les ressources des autres régions continueront de fonctionner comme prévu, mais vous ne pourrez pas les mettre à jour. Pour réduire les risques, placez votre groupe de ressources et vos ressources dans la même région.

## <a name="parameters"></a>parameters

Les informations de cette section peuvent être utiles lorsque vous travaillez avec des [paramètres](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Suggestions générales pour les paramètres

* Réduisez l’utilisation des paramètres. Utilisez plutôt des variables ou des valeurs littérales pour les propriétés qu’il n’est pas nécessaire de spécifier lors du déploiement.

* Utilisez la casse mixte pour les noms de paramètre.

* Utilisez des paramètres pour les éléments qui varient en fonction de l’environnement, tels que la référence SKU, la taille ou la capacité.

* Utilisez des les paramètres pour les noms de ressources que vous souhaitez spécifier afin d’en faciliter l’identification.

* Fournissez une description dans les métadonnées pour chaque paramètre :

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Définissez les valeurs par défaut pour les paramètres non sensibles. La spécification d’une valeur par défaut facilite le déploiement du modèle, et les utilisateurs de celui-ci peuvent voir un exemple de valeur appropriée. Toute valeur par défaut pour un paramètre doit être valide pour tous les utilisateurs dans la configuration de déploiement par défaut. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Pour spécifier un paramètre facultatif, n’utilisez pas de chaîne vide comme valeur par défaut. Au lieu de cela, utilisez une valeur littérale ou une expression de langage pour construire une valeur.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* N’utilisez pas de paramètre pour la version d’API d’un type de ressource. Les propriétés de ressource et les valeurs peuvent varier selon le numéro de version. IntelliSense dans un éditeur de code n’est pas en mesure de déterminer le schéma correct lorsque la version de l’API est définie sur un paramètre. Au lieu de cela, codez en dur la version de l’API dans le modèle.

* Utilisez `allowedValues` avec parcimonie. Ne l’utilisez que lorsque vous devez vous assurer que certaines valeurs ne sont pas incluses dans les options autorisées. Si vous utilisez `allowedValues` trop abondamment, vous risquez de bloquer des déploiements valides en ne tenant pas votre liste à jour.

* Quand un nom de paramètre dans votre modèle correspond à un paramètre dans la commande de déploiement PowerShell, Resource Manager résout ce conflit d’affectation de noms en ajoutant le suffixe **FromTemplate** au paramètre du modèle. Par exemple, si vous incluez dans votre modèle un paramètre nommé **ResourceGroupName**, celui-ci est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Pendant le déploiement, vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Suggestions en matière de sécurité pour les paramètres

* Utilisez toujours des paramètres pour les noms d’utilisateur et les mots de passe (ou secrets).

* Utilisez `securestring` pour tous les mots de passe et secrets. Si vous transmettez des données sensibles dans un objet JSON, utilisez le type `secureObject`. Il est impossible de lire les paramètres du modèle de types chaîne et objet sécurité après le déploiement de la ressource. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Ne fournissez pas de valeurs par défaut pour les noms d’utilisateur, mots de passe ou toute valeur nécessitant un type `secureString`.

* Ne fournissez pas de valeurs par défaut pour les propriétés qui augmentent la surface d’attaque de l’application.

### <a name="location-recommendations-for-parameters"></a>Suggestions en matière d’emplacement pour les paramètres

* Utilisez un paramètre pour spécifier l’emplacement des ressources, et définissez la valeur par défaut sur `resourceGroup().location`. La fourniture d’un paramètre d’emplacement permet aux utilisateurs du modèle de spécifier un emplacement vers lequel ils sont autorisés à déployer.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Ne spécifiez pas `allowedValues` pour le paramètre d’emplacement. Les emplacements que vous spécifiez pourraient ne pas être disponibles dans tous les clouds.

* Utilisez la valeur de paramètre d’emplacement pour les ressources susceptibles de se trouver dans le même emplacement. Cette approche réduit le nombre de fois où les utilisateurs sont invités à fournir des informations d’emplacement.

* Pour des ressources qui ne sont pas disponibles dans tous les emplacements, utilisez un paramètre distinct ou spécifiez une valeur d’emplacement littérale.

## <a name="variables"></a>variables

Les informations suivantes peuvent être utiles lorsque vous travaillez avec des [variables](template-variables.md) :

* Utilisez la case mixte pour les noms de variables.

* Utilisez des variables pour les valeurs que vous devez utiliser plusieurs fois dans un modèle. Si une valeur est utilisée une seule fois, une valeur codée en dur rend votre modèle plus facile à lire.

* Utilisez des variables pour les valeurs que vous construisez à partir d’un arrangement complexe de fonctions de modèle. Votre modèle est plus facile à lire quand l’expression complexe apparaît uniquement dans des variables.

* N’utilisez pas de variables pour `apiVersion` sur une ressource. La version d’API détermine le schéma de la ressource. Souvent, vous ne pouvez pas modifier la version sans modifier les propriétés de la ressource.

* Vous ne pouvez pas utiliser la fonction [référence](resource-group-template-functions-resource.md#reference) dans la section **variables** du modèle. La fonction **référence** dérive sa valeur de l’état d’exécution de la ressource. Toutefois, les variables sont résolues lors de l’analyse initiale du modèle. Construisez des valeurs qui ont besoin de la fonction **référence** directement dans la section **ressources** ou **outputs** du modèle.

* Ajoutez des variables pour les noms de ressource qui doivent être uniques.

* Utilisez une [boucle de copie dans les variables](resource-group-create-multiple.md#variable-iteration) pour créer un modèle répété d’objets JSON.

* Supprimez les variables inutilisées.

## <a name="resource-dependencies"></a>Dépendances des ressources

Lorsque vous décidez des [dépendances](resource-group-define-dependencies.md) à définir, respectez les recommandations suivantes :

* Utilisez la fonction **référence** et transmettez le nom de la ressource pour définir une dépendance implicite entre des ressources qui doivent partager une propriété. N’ajoutez pas d’élément `dependsOn` explicite lorsque vous avez déjà défini une dépendance implicite. Cette approche permet de réduire le risque d’avoir des dépendances inutiles.

* Définissez une ressource enfant comme dépendante de sa ressource parent.

* Les ressources avec l’[élément condition](conditional-resource-deployment.md) défini sur false sont automatiquement supprimées de l’ordre de dépendance. Définissez les dépendances comme si la ressource était toujours déployée.

* Ajoutez les dépendances l’une après l’autre sans les définir explicitement. Par exemple, votre machine virtuelle dépend d’une interface de réseau virtuel, et l’interface de réseau virtuelle dépend d’un réseau virtuel et d’adresses IP publiques. Par conséquent, la machine virtuelle est déployée après les trois ressources. Cependant, ne définissez pas explicitement la machine virtuelle comme dépendante de ces trois ressources. Cette approche permet de clarifier l’ordre des dépendances et de simplifier les modifications ultérieures du modèle.

* Si une valeur peut être déterminée avant le déploiement, essayez de déployer la ressource sans dépendance. Par exemple, si une valeur de configuration a besoin du nom d’une autre ressource, vous n’avez pas forcément besoin d’une dépendance. Cette recommandation n’est pas toujours applicable, car certaines ressources vérifient l’existence de l’autre ressource. Si vous recevez une erreur, ajoutez une dépendance.

## <a name="resources"></a>Ressources

Les informations suivantes peuvent être utiles lorsque vous travaillez avec des [ressources](resource-group-authoring-templates.md#resources) :

* Spécifiez des **commentaires** pour chaque ressource dans le modèle pour aider les autres contributeurs à comprendre l’objectif de la ressource :
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Si vous utilisez un *point de terminaison public* dans votre modèle (par exemple, un point de terminaison public Azure Blob Storage), *ne codez pas en dur* l’espace de noms. Utilisez la fonction **référence** pour récupérer l’espace de noms dynamiquement. Cette approche vous permet de déployer le modèle dans différents environnements d’espace de noms publics sans modifier manuellement le point de terminaison dans le modèle. Définissez la version d’API sur la version que vous utilisez pour le compte de stockage dans votre modèle :
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Si le compte de stockage est déployé dans le même modèle que celui que vous créez et que le nom du compte de stockage n’est pas partagé avec une autre ressource dans le modèle, vous n’avez pas besoin de spécifier l’espace de noms du fournisseur ou la valeur apiVersion quand vous référencez la ressource. L’exemple suivant montre la syntaxe simplifiée :
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Vous pouvez également référencer un compte de stockage existant dans un autre groupe de ressources :

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Affectez des adresses IP publiques à une machine virtuelle uniquement si une application le nécessite. Pour vous connecter à une machine virtuelle (VM) pour le débogage, ou à des fins d’administration ou de gestion, utilisez les règles NAT entrantes, une passerelle de réseau virtuel ou une jumpbox.
   
     Pour plus d’informations sur la connexion aux machines virtuelles, consultez :
   
   * [Exécuter des machines virtuelles pour une architecture à plusieurs niveaux dans Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurer l’accès WinRM pour les machines virtuelles dans Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide de PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Autoriser l’accès externe à votre machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* La propriété **domainNameLabel** pour les adresses IP publiques doit être unique. La valeur **domainNameLabel** doit comporter entre 3 et 63 caractères et respecter les règles spécifiées par cette expression régulière : `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Comme la fonction **uniqueString** génère une chaîne de 13 caractères, le paramètre **dnsPrefixString** est limité à 50 caractères :

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Lors de l’ajout d’un mot de passe à une extension de script personnalisé, utilisez la propriété **commandToExecute** dans la propriété **protectedSettings** :
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Pour garantir que les clés secrètes sont chiffrées lorsqu’elles sont transmises comme paramètres à des machines virtuelles et à des extensions, utilisez la propriété **protectedSettings** des extensions appropriées.
   > 
   > 

## <a name="outputs"></a>Outputs

Si vous utilisez un modèle pour créer des adresses IP publiques, il doit comporter une [section outputs](template-outputs.md) qui renvoie les détails de l’adresse IP et le nom de domaine complet (FQDN). Vous pouvez utiliser des valeurs de sortie pour récupérer facilement plus d’informations sur les adresses IP publiques et sur les noms de domaine complets après le déploiement.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la structure du fichier de modèle Resource Manager, voir [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir des suggestions sur la création de modèles qui fonctionnent dans tous les environnements cloud Azure, voir [Développer des modèles Azure Resource Manager de cohérence du cloud](templates-cloud-consistency.md).
