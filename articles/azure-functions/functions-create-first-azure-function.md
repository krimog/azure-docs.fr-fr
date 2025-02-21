---
title: Créer votre première fonction serverless à partir du portail Azure
description: Apprenez à créer votre première fonction Azure pour une exécution sans serveur à l’aide du portail Azure.
author: ggailey777
manager: gwallace
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: eb3df5ace97e5a09b2869c0857d49926f354f2b0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329666"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Créer votre première fonction à l’aide du Portail Azure

Azure Functions vous permet d’exécuter votre code dans un environnement [sans serveur](https://azure.microsoft.com/solutions/serverless/) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable. Dans cet article, vous allez découvrir comment utiliser Functions pour créer une fonction « hello world » dans le portail Azure.

![Créer une Function App dans le Portail Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Les développeurs C# doivent envisager de [créer votre première fonction dans Visual Studio 2019](functions-create-your-first-function-visual-studio.md) plutôt que sur le portail. 

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com> avec votre compte Azure.

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. Une Function App vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Créez ensuite une fonction dans la nouvelle Function App.

## <a name="create-function"></a>Créer une fonction déclenchée via HTTP

1. Développez votre nouvelle application de fonction, puis sélectionnez le bouton **+** en regard de **Fonctions**, choisissez **Dans le portail** et sélectionnez **Continuer**.

    ![Démarrage rapide de Functions pour le choix d’une plateforme.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Choisissez **WebHook + API**, puis sélectionnez **Créer**.

    ![Démarrage rapide de fonctions dans le portail Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Une fonction est créée à l’aide d’un modèle spécifique à une langue pour une fonction déclenchée via HTTP.

Vous pouvez maintenant exécuter la nouvelle fonction en envoyant une requête HTTP.

## <a name="test-the-function"></a>Tester la fonction

1. Dans votre nouvelle fonction, cliquez sur **</> Obtenir l’URL de la fonction** en haut à droite, sélectionnez **par défaut (touche de fonction)** , puis cliquez sur **Copier**. 

    ![Copier l’URL de fonction à partir du portail Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Collez l’URL de fonction dans la barre d’adresse de votre navigateur. Ajoutez la valeur de la chaîne de requête `&name=<yourname>` à la fin de cette URL et appuyez sur la touche `Enter` de votre clavier pour exécuter la requête. Vous devez voir la réponse renvoyée par la fonction affichée dans le navigateur.  

    L’exemple suivant montre la réponse dans le navigateur :

    ![Réponse de la fonction dans le navigateur.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    L’URL de demande inclut une clé qui est requise, par défaut, pour accéder à votre fonction sur HTTP.

3. Lorsque votre fonction s’exécute, des informations de suivi sont écrites dans les journaux d’activité. Pour afficher la sortie de suivi de l’exécution précédente, revenez à votre fonction dans le portail et cliquez sur la flèche figurant en bas de l’écran pour développer **Journaux d’activité**.

   ![Affichage des journaux de fonction dans le portail Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une application de fonction avec une simple fonction déclenchée via HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour plus d’informations, consultez [Liaisons HTTP Azure Functions](functions-bindings-http-webhook.md).
