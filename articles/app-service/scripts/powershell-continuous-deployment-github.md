---
title: Exemple de script Azure PowerShell - Créer une application avec un déploiement continu GitHub | Microsoft Docs
description: Exemple de script Azure PowerShell - Créer une application web avec un déploiement continu à partir de GitHub
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bc8be33ede80070b8e9928e01d07b6066f1c887c
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113573"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Créer une application web avec un déploiement continu à partir de GitHub

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis configure un [déploiement continu](../deploy-continuous-deployment.md) à partir d’un dépôt GitHub. Pour le déploiement GitHub sans déploiement continu, consultez [Créer une application web et déployer du code à partir de GitHub](powershell-deploy-github.md).

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure. Vérifiez également les points suivants :

- Le code d’application se trouve dans un référentiel GitHub public ou privé dont vous êtes propriétaire. Pour obtenir des builds automatiques, structurez votre dépôt en fonction du tableau [Préparer votre dépôt](../deploy-continuous-deployment.md#prepare-your-repository).
- Vous avez [créé un jeton d’accès personnel dans votre compte GitHub](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Crée un plan App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crée une application web. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifie une ressource dans un groupe de ressources. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../samples-powershell.md) (Exemples Azure PowerShell).
