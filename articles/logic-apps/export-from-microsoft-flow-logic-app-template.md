---
title: Exporter des flux de Power Automate vers Azure Logic Apps
description: Migrez des flux de Power Automate vers Azure Logic Apps en les exportant sous forme de modèles Azure Resource Manager.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: e0dda5c2097243143d18851c47e7006c81769c87
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583256"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exporter des flux de Power Automate et les déployer sur Azure Logic Apps

Pour étendre les fonctionnalités de votre flux, vous pouvez le migrer de [Power Automate](https://flow.microsoft.com) vers [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Vous pouvez exporter votre flux en tant que modèle Azure Resource Manager pour une application logique, déployer ce modèle d’application logique dans un groupe de ressources Azure, puis ouvrir cette application logique dans le Concepteur d’application logique.

> [!NOTE]
> Les connecteurs Power Automate ne sont pas tous disponibles dans Azure Logic Apps. Vous pouvez importer des flux qui ont des [connecteurs équivalents](../connectors/apis-list.md) dans Azure Logic Apps. Par exemple, le déclencheur de bouton, le connecteur d’approbation et le connecteur de notification sont propres à Power Automate.
>
> Le déploiement sous forme de modèles d’applications logiques des flux basés sur OpenAPI et exportés à partir de Power Automate n’est pas pris en charge actuellement. 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Le flux que vous souhaitez exporter à partir de Power Automate

## <a name="export-a-flow"></a>Exporter un flux

1. Connectez-vous à [Power Automate](https://flow.microsoft.com), puis sélectionnez **Mes flux**. Recherchez et sélectionnez votre flux. Dans la barre d’outils, sélectionnez les points de suspension ( **...** ). Sélectionnez **Exporter** > **Modèle Logic Apps (.json)** .

   ![Exporter un flux](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Enregistrez votre modèle à l’emplacement de votre choix.

Pour plus d’informations, consultez [Grow up to Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Déployer le modèle à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

1. Dans le menu principal d’Azure, choisissez **Créer une ressource**. Dans la zone de recherche, entrez « template deployment ». Sélectionnez **Template deployment (deploy using custom templates)** , puis **Créer**.

   ![Sélectionnez « template deployment »](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Sous **Déploiement personnalisé**, sélectionnez **Créer votre propre modèle dans l’éditeur**.

   ![Sélectionnez « Créer votre propre modèle dans l’éditeur »](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Dans la barre d’outils **Modifier le modèle**, sélectionnez **Charger le fichier**. Recherchez et sélectionnez le modèle JSON que vous avez exporté à partir de Power Automate, puis sélectionnez **Ouvrir**.

   ![Sélectionnez « Charger le fichier »](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Une fois que l’éditeur a affiché le JSON, les paramètres et les ressources dans votre modèle, sélectionnez **Enregistrer**.
  
   ![Enregistrer un modèle](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Maintenant, spécifiez ces paramètres d’entrée pour le modèle :

   * Abonnement Azure à utiliser pour la facturation
   * Groupe de ressources Azure
   * Emplacement du groupe de ressources Azure
   * Nom de la ressource d’application logique
   * Emplacement de la ressource d’application logique, s’il est différent du groupe de ressources Azure
   * Nom des connexions créées précédemment que l’application logique peut réutiliser

      Si vous créez votre première application logique, toutes les connexions sont créées ; vous pouvez donc accepter les noms par défaut. Dans le cas contraire, vous pouvez spécifier les noms de connexions existantes, que vous pouvez utiliser dans plusieurs applications logiques.

   Après avoir fourni ces informations pour le modèle, passez en revue et acceptez les conditions générales de la Place de marché Azure pour créer les ressources Azure nécessaires et facturer votre abonnement Azure en conséquence, puis sélectionnez **Acheter**.
  
   ![Spécifiez les paramètres d’entrée pour le modèle](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure déploie votre modèle en tant qu’application logique dans votre groupe de ressources spécifié. Toutes les applications logiques migrées à partir de Power Automate sont déployées avec l’état désactivé.

1. Avant d’activer votre application logique, autorisez les nouvelles connexions en effectuant les étapes suivantes :

   1. Ouvrez l’application logique que vous avez créée. Dans le menu de l’application logique, sélectionnez **Concepteur d’application logique**.

      Chaque connexion nécessitant une autorisation affiche une icône d’avertissement :

      ![Icône Avertissement](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Pour chaque étape qui nécessite une connexion autorisée, développez cette étape, puis sélectionnez **Ajouter nouveau**.

      ![Ajoutez une nouvelle connexion](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Connectez-vous à chaque service ou fournissez les informations d’identification nécessaires pour autoriser la connexion.

1. Enregistrez votre application logique. Quand vous êtes prêt à activer votre application logique, dans le menu de l’application logique, sélectionnez **Vue d’ensemble**, puis **Activer**.

   ![Activez l’application logique](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Pour éviter d’exécuter des flux de travail en double, n’oubliez pas de désactiver ou de supprimer votre flux d’origine.

## <a name="deploy-template-by-using-visual-studio"></a>Déployer le modèle à l’aide de Visual Studio

Si vous avez configuré Visual Studio avec les [prérequis](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) pour la création d’applications logiques, vous pouvez déployer votre modèle exporté de Visual Studio vers Azure Logic Apps.

1. Dans Visual Studio, ouvrez le modèle de fichier que vous avez exporté à partir de Power Automate.

1. Dans Visual Studio, créez un projet de groupe de ressources Azure et sélectionnez le modèle **Application logique** en suivant les étapes décrites dans [Démarrage rapide : Créer des tâches, des processus et des workflows automatisés avec Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), par exemple :

   ![Créer un projet de groupe de ressources Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Dans l’Explorateur de solutions, ouvrez le fichier **LogicApp.json**, s’il ne l’est pas encore.

1. Copiez le contenu du modèle exporté et remplacez le contenu dans le fichier **LogicApp.json**.

1. Avant de déployer votre application logique, autorisez les nouvelles connexions en effectuant les étapes suivantes :

   1. Ouvrez le menu contextuel de **LogicApp.json** et sélectionnez **Ouvrir avec Concepteur d’application logique**.

      ![Ouvrez le modèle avec le Concepteur d’application logique](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Si vous y êtes invité, sélectionnez l’abonnement Azure et le groupe de ressources que vous souhaitez utiliser pour le déploiement de votre application logique.

      ![Sélectionnez l’abonnement Azure et le groupe de ressources](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Une fois que votre application logique apparaît dans le concepteur, toutes les connexions nécessitant une autorisation affichent des icônes d’avertissement :

      ![Connexions avec des icônes d’avertissement](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Pour chaque étape qui nécessite une connexion autorisée, développez cette étape, puis sélectionnez **Ajouter nouveau**.

      ![Ajoutez une nouvelle connexion](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Connectez-vous à chaque service ou fournissez les informations d’identification nécessaires pour autoriser la connexion.

   1. Enregistrez votre solution avant de déployer l’application logique.

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel du projet et sélectionnez **Déployer** > **Nouveau**. Si vous y êtes invité, connectez-vous à votre compte Azure.

1. À l’invite, confirmez l’abonnement Azure, le groupe de ressources Azure et tous les autres paramètres que vous souhaitez utiliser pour le déploiement, par exemple le [fichier de paramètres](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) à utiliser pour transmettre les valeurs de paramètres du modèle, puis sélectionnez **Déployer**.

   ![Confirmez les paramètres de déploiement](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Si la zone **Modifier les paramètres** s’affiche, indiquez le nom de votre ressource d’application logique dans Azure, puis sélectionnez **Enregistrer**.  

   ![Modifiez les paramètres de déploiement](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Lorsque le déploiement commence, l’état du déploiement de votre application s’affiche dans la fenêtre **Sortie** de Visual Studio. Si l’état n’apparaît pas, ouvrez la liste **Afficher la sortie à partir de** et sélectionnez votre groupe de ressources Azure. Par exemple :

   ![Fenêtre Sortie](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Si des connexions dans votre application logique nécessitent une intervention de votre part, une fenêtre PowerShell s’ouvre en arrière-plan et vous invite à taper les mots de passe ou les clés secrètes nécessaires. Le déploiement se poursuit dès que vous avez saisi ces informations.

   ![Authentifiez les connexions](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Une fois le déploiement terminé, votre application logique est publiée mais n’est pas activée dans le portail Azure.

1. Quand vous êtes prêt à activer votre application logique dans le portail Azure, recherchez-la et ouvrez-la dans le Concepteur d’application logique. Dans le menu de l’application logique, sélectionnez **Vue d’ensemble**, puis **Activer**.

1. Pour éviter d’exécuter des flux de travail en double, n’oubliez pas de désactiver ou de supprimer votre flux d’origine.

Pour plus d’informations sur ces étapes de déploiement, consultez [Démarrage rapide : Créer des tâches, des processus et des workflows automatisés avec Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [les autres connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
* En savoir plus sur [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
