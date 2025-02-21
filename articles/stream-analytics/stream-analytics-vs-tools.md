---
title: Afficher les tâches Azure Stream Analytics dans Visual Studio
description: Cet article explique comment afficher et gérer les travaux Azure Stream Analytics dans Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ae532ed19c2273e43aa739e84d5a68cadb717b86
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173024"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Utiliser Visual Studio pour afficher les tâches Azure Stream Analytics

Le service Azure Stream Analytics Tools pour Visual Studio aide les développeurs à gérer leurs tâches Stream Analytics directement à partir de l’environnement de développement intégré. Les outils Azure Stream Analytics permettent d’effectuer les opérations suivantes :
- [Créer des tâches](stream-analytics-quick-create-vs.md)
- Démarrer, arrêter et [surveiller des tâches](stream-analytics-monitor-jobs-use-vs.md)
- Vérifier les résultats d’une tâche
- Exporter des tâches existants vers un projet
- Tester des connexions entrantes et sortantes
- [Exécuter des requêtes localement](stream-analytics-vs-tools-local-run.md)

Découvrez comment [Installer Azure Stream Analytics Tools pour Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explorer la vue des tâches

Vous pouvez utiliser la vue des tâches pour interagir avec les tâches Azure Stream Analytics à partir de Visual Studio.

### <a name="open-the-job-view"></a>Ouvrir la vue de la tâche

1. Dans l’**Explorateur de serveurs**, sélectionnez **Tâches Stream Analytics**, puis **Actualiser**. Votre tâche doit apparaître sous **Tâches Stream Analytics**.

    ![Liste de l’Explorateur de serveurs de Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Développez le nœud de votre travail et double-cliquez sur le nœud **Vue des travaux** pour ouvrir une vue des travaux.
    
   ![Nœud de tâche développé](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Démarrage et arrêt des travaux

Les tâches Azure Stream Analytics peuvent être entièrement gérées à partir de la vue des tâches dans Visual Studio. Utilisez les contrôles pour démarrer, arrêter ou supprimer un travail.
    
   ![Contrôles de tâche Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Vérifier les résultats d’une tâche

Le service Azure Stream Analytics Tools pour Visual Studio prend actuellement en charge une préversion de sortie pour Azure Data Lake Storage et Stockage Blob. Pour afficher le résultats, double-cliquez simplement sur le nœud de sortie du diagramme de tâche dans la **vue des tâches**, puis entrez les informations d’identification appropriées.

   ![Sortie de blob de tâche Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Exporter des tâches vers un projet

Il existe deux méthodes pour exporter un travail existant vers un projet.

1. Dans l’**Explorateur de serveurs**, sous le nœud Tâches Stream Analytics, cliquez avec le bouton droit sur le nœud de tâche. Sélectionnez **Exporter vers un nouveau projet Stream Analytics**.
    
   ![Exporter une tâche vers un projet](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Le projet généré apparaît dans **l’Explorateur de solutions**.
    
   ![Explorateur de solutions](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Dans la vue des travaux, sélectionnez **Générer le projet**.
    
   ![Générer un projet à partir de la vue des tâches](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Tester les connexions

Vous pouvez tester les connexions entrants et sortantes à partir de la **vue des tâches** en sélectionnant une option dans la liste déroulante **Tester la connexion**.

   ![Liste déroulante Tester la connexion](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Les résultats de l’opération **Tester la connexion** s’affichent dans la fenêtre de **sortie**.

   ![Résultats de l’opération Tester la connexion](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller et gérer les tâches Azure Stream Analytics à l’aide de Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Démarrage rapide : Créer un travail Stream Analytics à l'aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Tutoriel : Déployer un travail Azure Stream Analytics avec CI/CD à l’aide d’Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Intégrer et développer en continu avec les outils Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
