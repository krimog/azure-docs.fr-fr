---
title: Écrire des fonctions C# définies par l’utilisateur pour des travaux de périphérie Azure Stream Analytics dans Visual Studio (Préversion)
description: Apprenez à écrire des fonctions C# définies par l’utilisateur pour des travaux de périphérie Stream Analytics dans Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: cadc603a94d5d17ad2df419f8507c37f9e3272f8
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173307"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-edge-job-preview"></a>Didacticiel : Écrire une fonction C# définie par l’utilisateur pour un travail Edge Azure Stream Analytics (préversion)

Les fonctions C# définies par l’utilisateur créées dans Visual Studio vous permettent d’étendre le langage de requête Azure Stream Analytics avec vos propres fonctions. Vous pouvez réutiliser le code existant (y compris les DLL) et utiliser une logique mathématique ou complexe avec C#. Il existe trois façons d’implémenter des fonctions définies par l’utilisateur : fichiers code-behind dans un projet Stream Analytics, fonctions définies par l’utilisateur issues d’un projet C# local ou fonctions définies par l’utilisateur issues d’un package existant à partir d’un compte de stockage. Ce tutoriel utilise la méthode code-behind pour implémenter une fonction C# de base. La fonctionnalité de fonction définie par l’utilisateur pour les travaux de périphérie Stream Analytics étant disponible en préversion, elle ne doit pas être utilisée dans les charges de travail de production.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une fonction définie par l’utilisateur C# à l’aide de code-behind.
> * Tester votre travail de périphérie Stream Analytics localement.
> * Publier votre travail de périphérie sur Azure.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, veillez à effectuer les prérequis suivants :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installez les [outils Stream Analytics pour Visual Studio](stream-analytics-tools-for-visual-studio-install.md) et les charges de travail **Développement Azure** ou **Stockage et traitement de données**.
* Consultez le [guide de développement sur la périphérie Stream Analytics](stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Créez un conteneur dans votre compte Stockage Azure.

Le conteneur que vous créez servira à stocker le package C# compilé et à déployer celui-ci sur votre appareil IoT Edge. Utilisez un conteneur dédié pour chaque travail Stream Analytics. La réutilisation du même conteneur pour plusieurs travaux de périphérie Stream Analytics n’est pas prise en charge. Si vous avez déjà un compte de stockage avec des conteneurs existants, vous pouvez les utiliser. Sinon, vous devez [créer un conteneur](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Créer un projet de périphérie Stream Analytics dans Visual Studio

1. Démarrez Visual Studio.

2. Sélectionnez **Fichier > Nouveau > Projet**.

3. Dans la liste des modèles sur la gauche, sélectionnez **Stream Analytics**, puis **Application de périphérie Azure Stream Analytics**.

4.  Entrez les éléments **Nom**, **Emplacement** et **Nom de la solution** de votre projet, puis sélectionnez **OK**.

    ![Créer un projet de périphérie Azure Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Configurer le chemin du package d’assembly

1. Ouvrez Visual Studio et accédez à **l’Explorateur de solutions**.

2. Double-cliquez sur le fichier de configuration du travail, `EdgeJobConfig.json`.

3. Développez la section **Configuration du code défini par l’utilisateur** et remplissez la configuration avec les valeurs suggérées suivantes :

    |**Paramètre**  |**Valeur suggérée**  |
    |---------|---------|
    |Source de l’assembly  |  Référence de projet local ou code-behind   |
    |Ressource  |  Choisissez des données du compte actuel   |
    |Subscription  |  Choisissez votre abonnement.   |
    |Compte de stockage  |  Choisissez votre compte de stockage.   |
    |Conteneur  |  Choisissez le conteneur que vous avez créé dans votre compte de stockage.   |

    ![Configuration du travail de périphérie Azure Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>Écrire une fonction C# définie par l’utilisateur avec du code-behind
Un fichier code-behind est un fichier C# associé à un script de requête ASA Edge. Visual Studio Tools compresse automatiquement le fichier code-behind et le charge sur votre compte de stockage Azure au moment de l’envoi. Toutes les classes doivent être définies en tant que classes *publiques* et tous les objets doivent être définis en tant qu’objets *publics statiques*.

1. Dans **l’Explorateur de solutions**, développez **Script.asql** pour rechercher le fichier code-behind **Script.asaql.cs**.

2. Remplacez le code par l’exemple suivant :

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implémenter la fonction définie par l’utilisateur

1. Dans **l’Explorateur de solutions**, ouvrez le fichier **Script.asaql**.

2. Remplacez la requête existante par ce qui suit :

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Test local

1. Téléchargez [l’exemple de fichier de données du simulateur de température](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json) de périphérie.

2. Dans **l’Explorateur de solutions**, développez **Entrées**, cliquez avec le bouton droit sur **Input.json**, puis sélectionnez **Ajouter une entrée locale**.

   ![Ajouter une entrée locale au travail Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Spécifiez le chemin du fichier de l’entrée locale pour les exemples de données que vous avez téléchargées et cliquez sur **Enregistrer**.

    ![Configuration de l’entrée locale pour le travail Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Cliquez sur **Exécuter localement** dans l’éditeur de script. Une fois que l’exécution locale a enregistré les résultats de la sortie, appuyez sur n’importe quelle touche pour les afficher sous forme de tableau. 

    ![Exécuter le travail Azure Stream Analytics localement avec Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Vous pouvez également sélectionner **Ouvrir le dossier de résultats** pour voir les fichiers bruts au format JSON et CSV.

    ![Afficher les résultats d’un travail Azure Stream Analytics local avec Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Déboguer une fonction définie par l’utilisateur
Vous pouvez déboguer votre fonction C# définie par l’utilisateur localement de la même façon que du code C# standard. 

1. Ajoutez des points d’arrêt à votre fonction C#.

    ![Ajouter des points d’arrêt à la fonction définie par l’utilisateur Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Appuyez sur **F5** pour démarrer le débogage. Le programme s’arrête sur vos points d’arrêt comme prévu.

    ![Afficher les résultats de débogage d’une fonction Stream Analytics définie par l’utilisateur](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publier votre travail sur Azure
Une fois que vous avez testé votre requête localement, sélectionnez **Envoyer sur Azure** dans l’éditeur de script pour publier le travail sur Azure.

![Envoyer votre travail de périphérie Stream Analytics sur Azure à partir de Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Déployer sur des appareils IoT Edge
Votre travail Stream Analytics est maintenant prêt à être déployé en tant que module IoT Edge. Suivez le [guide de démarrage rapide sur IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) pour créer un hub IoT, inscrire un appareil IoT Edge, et installer et démarrer le runtime IoT Edge sur votre appareil. Ensuite, suivez le tutoriel [Déployer la tâche](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) pour déployer votre travail Stream Analytics en tant que module IoT Edge. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une fonction C# simple définie par l’utilisateur à l’aide de code-behind, publié votre travail sur Azure, puis déployé celui-ci sur des appareils IoT Edge à l’aide du portail IoT Hub. 

Pour en savoir plus sur les différentes façons d’utiliser des fonctions C# définies par l’utilisateur pour des travaux de périphérie Stream Analytics, passez à cet article :

> [!div class="nextstepaction"]
> [Écrire des fonctions C# pour Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
