---
title: Copier ou sauvegarder des travaux Azure Stream Analytics
description: Cet article explique comment copier ou sauvegarder un travail Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 8ae97a3ef6e354bb07e257b4997341297e8abe51
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587038"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copier ou sauvegarder des travaux Azure Stream Analytics

Vous pouvez copier ou sauvegarder vos travaux Azure Stream Analytics déployés à l’aide de Visual Studio Code ou de Visual Studio. 

## <a name="before-you-begin"></a>Avant de commencer
* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Connectez-vous au [Portail Azure](https://portal.azure.com/).

* Installez [l’extension Azure Stream Analytics pour Visual Studio Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension) ou [les outils Azure Stream Analytics pour Visual Studio](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension).  



## <a name="visual-studio-code"></a>Visual Studio Code 

1. Cliquez sur l’icône **Azure** située sur la barre d’activités Visual Studio Code, puis développez le nœud **Stream Analytics**. Vos travaux doivent apparaître sous vos abonnements.

   ![Ouvrir l’Explorateur Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Pour exporter un travail vers un projet local, recherchez le travail que vous souhaitez exporter dans l’**Explorateur Stream Analytics** de Visual Studio Code. Puis, sélectionnez un dossier pour votre projet. 

    ![Exporter le travail ASA dans Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Le projet est exporté vers le dossier que vous sélectionnez et est ajouté à votre espace de travail actuel.

    ![Exporter le travail ASA dans Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Pour publier le travail dans une autre région ou dans une sauvegarde à l’aide d’un autre nom, sélectionnez **Select from your subscriptions to publish** (sélectionner parmi vos abonnements pour publier) dans l’éditeur de requête (\*.asaql) et suivez les instructions. 

    ![Publier sur Azure dans Visual Studio Code](./media/quick-create-vs-code/select-subscription.png)


## <a name="visual-studio"></a>Visual Studio 

1. Suivez les instructions [Exporter une tâche Azure Stream Analytics déployée vers un projet](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project). 

2. Ouvrez le fichier \*.asaql dans l’éditeur de requête, sélectionnez **Envoyer sur Azure** dans l’éditeur de script et suivez les instructions pour publier le travail dans une autre région ou une sauvegarde en utilisant un nouveau nom. 


## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio Code](quick-create-vs-code.md)
* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Déployer un travail Azure Stream Analytics avec CI/CD à l’aide de Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)