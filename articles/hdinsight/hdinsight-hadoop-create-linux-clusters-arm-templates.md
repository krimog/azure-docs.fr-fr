---
title: Créer des clusters Apache Hadoop avec des modèles – Azure HDInsight
description: Apprenez à créer des clusters pour HDInsight avec des modèles Resource Manager.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5dd8b54e0d3febf7dbb3209a9f0bde76263aa726
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494825"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Créer des clusters Apache Hadoop dans HDInsight avec des modèles Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Vous trouverez dans cet article différentes façons de créer des clusters Azure HDInsight à l’aide de modèles Azure Resource Manager. Pour plus d’informations, consultez la page [Déploiement d’une application avec un modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Pour découvrir d’autres outils et fonctions de création de clusters, cliquez sur le sélecteur d’onglets situé en haut de cette page, ou consultez la section relative aux [méthodes de création de clusters](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Prérequis
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour suivre les instructions de cet article, il vous faut :

* Un [abonnement Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell et/ou Azure Classic CLI.

### <a name="resource-manager-templates"></a>Modèles Resource Manager
Un modèle Resource Manager facilite la création des ressources suivantes pour votre application, en une seule opération coordonnée :
* des clusters HDInsight et leurs ressources dépendantes (telles que le compte de stockage par défaut).
* Autres ressources (telles qu’Azure SQL Database pour utiliser [Apache Sqoop](https://sqoop.apache.org/)).

Dans le modèle, vous définissez les ressources nécessaires à l’application. Vous spécifiez également les paramètres de déploiement permettant d’entrer des valeurs pour différents environnements. Le modèle se compose d’un JSON et d’expressions permettant de construire des valeurs pour le déploiement.

Pour accéder à des exemples de modèles HDInsight, consultez la page [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Utilisez [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) interplateforme avec [l’extension Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou un éditeur de texte pour enregistrer le modèle dans un fichier sur votre station de travail. 

Pour plus d’informations sur les modèles Resource Manager, consultez les articles et exemples suivants :

* [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Déployer une application avec des modèles Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* Documentation de référence concernant le modèle [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Génération de modèles

Resource Manager vous permet d’exporter un modèle Resource Manager à partir de ressources existantes de votre abonnement à l’aide de différents outils. Vous pouvez utiliser le modèle généré pour découvrir la syntaxe du modèle, ou pour automatiser le redéploiement de votre solution en fonction des besoins. Pour plus d’informations, consultez [Modèles d’exportation](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Procéder à un déploiement à l’aide du portail

Vous pouvez déployer un modèle Resource Manager à l’aide du portail Azure. Pour plus d’informations, consultez [Déployer des ressources à partir d’un modèle personnalisé](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Déployer à l’aide de PowerShell

Vous pouvez déployer un modèle Resource Manager à l’aide d’Azure PowerShell. Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Déployer à l’aide d’Azure CLI

Vous pouvez déployer un modèle Resource Manager à l’aide d’Azure Classic CLI. Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Procéder à un déploiement à l’aide de l’API REST
Vous pouvez déployer un modèle Resource Manager à l’aide de l’API REST. Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Déployer avec Visual Studio
 Utilisez Visual Studio pour créer un projet de groupe de ressources et le déployer vers Azure par le biais de l’interface utilisateur. Vous sélectionnez le type de ressources à inclure dans votre projet. Ces ressources sont automatiquement ajoutées au modèle Resource Manager. Le projet fournit également un script PowerShell pour déployer le modèle.

Pour une introduction à l’utilisation de Visual Studio avec les groupes de ressources, consultez [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* Pour obtenir plus de modèles associés à HDInsight, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez la page [Déployer des ressources avec des bibliothèques .NET et un modèle](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour obtenir un exemple détaillé de déploiement d’une application, consultez [Approvisionner et déployer des microservices de manière prévisible dans Azure](../app-service/deploy-complex-application-predictably.md).
* Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](../solution-dev-test-environments.md).
* Pour en savoir plus sur les sections du modèle Azure Resource Manager, consultez [Création de modèles](../azure-resource-manager/resource-group-authoring-templates.md).
* Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager, voir [Fonctions des modèles](../azure-resource-manager/resource-group-template-functions.md).
