---
title: 'Didacticiel : Détecter et afficher les données relatives au visage dans une image à l’aide du kit SDK .NET'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application Windows qui utilise l’API Visage pour détecter et encadrer des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 93932fac9a5e5d4c21adc99bd31e9366a9709cc2
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859118"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Didacticiel : Créer une application WPF pour afficher les données relatives au visage dans une image

Dans ce tutoriel, vous allez apprendre à utiliser l’API Visage Azure via le kit SDK .NET client pour détecter des visages dans une image et présenter les données dans l’IU. Vous allez créer une application WPF (Windows Presentation Framework) qui détecte les visages, trace un cadre autour de chaque visage et affiche une description du visage dans la barre d’état. 

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> - Créer une application WPF
> - Installer la bibliothèque de client de l’API Visage
> - Utiliser la bibliothèque de client pour détecter les visages dans une image
> - Tracer un cadre autour de chaque visage détecté
> - Afficher une description du visage mis en surbrillance dans la barre d’état

![Capture d’écran affichant des visages détectés encadrés par des rectangles](../Images/getting-started-cs-detected.png)

L’exemple de code complet est disponible dans le dépôt [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) sur GitHub.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 


## <a name="prerequisites"></a>Prérequis

- Clé d’abonnement à l’API Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service API Visage et obtenir votre clé. Ensuite, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la chaîne de point de terminaison de la clé et du service, nommées respectivement `FACE_SUBSCRIPTION_KEY` et `FACE_ENDPOINT`.
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Suivez ces étapes pour créer un projet d’application WPF.

1. Dans Visual Studio, ouvrez la boîte de dialogue Nouveau projet. Développez **Installé** et **Visual C#** , puis sélectionnez **Application WPF (.NET Framework)** .
1. Nommez l’application **FaceTutorial**, puis cliquez sur **OK**.
1. Récupérez les packages NuGet requis. Cliquez avec le bouton droit sur votre projet dans l’Explorateur de solutions et sélectionnez **Gérer les packages NuGet**. Ensuite, recherchez et installez le package suivant :
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Ajouter le code initial

Dans cette section, vous allez ajouter le framework de base de l’application sans les fonctionnalités spécifiques aux visages.

### <a name="create-the-ui"></a>Créer l’interface utilisateur

Ouvrez *MainWindow.xaml*, puis remplacez le contenu par le code suivant. Ce code crée la fenêtre d’IU. Les méthodes `FacePhoto_MouseMove` et `BrowseButton_Click` sont des gestionnaires d’événements que vous allez définir plus tard.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Créer la classe main

Ouvrez *MainWindow.xaml.cs*, puis ajoutez les espaces de noms de la bibliothèque de client ainsi que les autres espaces de noms nécessaires. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Insérez ensuite le code suivant dans la classe **MainWindow**. Ce code crée une instance de **FaceClient** à l’aide de la clé d’abonnement et du point de terminaison.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Ensuite, ajoutez le constructeur **MainWindow**. Il vérifie votre chaîne d’URL de point de terminaison, puis l’associe à l’objet client.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Enfin, ajoutez les méthodes **BrowseButton_Click** et **FacePhoto_MouseMove** à la classe. Ces méthodes correspondent aux gestionnaires d’événements déclarés dans *MainWindow.xaml*. La méthode **BrowseButton_Click** crée un **OpenFileDialog**, ce qui permet à l’utilisateur de sélectionner une image .jpg. Elle affiche ensuite l’image dans la fenêtre principale. Vous insérerez le code restant pour **BrowseButton_Click** et **FacePhoto_MouseMove** plus tard. Notez également la référence `faceList`, une liste d’objets **DetectedFace**. Cette référence est l’emplacement où votre application va stocker et appeler les données de visage réelles.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Essayer l’application

Appuyez sur **Démarrer** dans le menu pour tester votre application. Quand la fenêtre d’application s’ouvre, cliquez sur **Parcourir** dans l’angle inférieur gauche. Une boîte de dialogue **d’ouverture de fichier** doit apparaître. Sélectionnez une image dans votre système de fichiers et vérifiez qu’elle s’affiche dans la fenêtre. Fermez ensuite l’application, puis passez à l’étape suivante.

![Capture d’écran montrant l’image non modifiée des visages](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Charger l’image et détecter des visages

Votre application détecte des visages en appelant la méthode **FaceClient.Face.DetectWithStreamAsync**, laquelle inclut dans un wrapper l’API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour charger une image locale.

Insérez la méthode suivante dans la classe **MainWindow**, sous la méthode **FacePhoto_MouseMove**. Cette méthode définit une liste d’attributs de visage à récupérer et lit le fichier image soumis dans un **Stream**. Elle passe ensuite ces deux objets à l’appel de méthode **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Tracer des rectangles autour des visages

Ajoutez ensuite le code pour tracer un rectangle autour de chaque visage détecté dans l’image. Dans la classe **MainWindow**, insérez le code suivant à la fin de la méthode **BrowseButton_Click**, après la ligne `FacePhoto.Source = bitmapSource`. Ce code remplit une liste de visages détectés à partir de l’appel de **UploadAndDetectFaces**. Il trace ensuite un rectangle autour de chaque visage et affiche l’image modifiée dans la fenêtre principale.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Décrire les visages

Ajoutez la méthode suivante à la classe **MainWindow**, sous la méthode **UploadAndDetectFaces**. Cette méthode convertit les attributs de visage récupérés en chaîne décrivant le visage.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Afficher la description du visage

Ajoutez le code suivant à la méthode **FacePhoto_MouseMove**. Ce gestionnaire d’événements affiche la chaîne de description du visage dans `faceDescriptionStatusBar` quand l’utilisateur pointe le curseur sur le rectangle d’un visage détecté.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Exécution de l'application

Exécutez cette application et recherchez une image contenant un visage. Patientez pendant quelques secondes pour laisser à l’API Visage le temps de répondre. Un rectangle rouge doit apparaître autour de chaque visage de l’image. Si vous déplacez la souris sur le rectangle d’un visage, la description de ce visage doit apparaître dans la barre d’état.

![Capture d’écran affichant des visages détectés encadrés par des rectangles](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert le processus de base pour l’utilisation du SDK .NET du service Visage, et créé une application pour détecter et encadrer des visages dans une image. Explorez à présent les détails de la détection des visages.

> [!div class="nextstepaction"]
> [Comment détecter des visages dans une image](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
