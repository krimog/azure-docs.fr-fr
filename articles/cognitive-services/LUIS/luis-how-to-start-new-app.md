---
title: Créer une nouvelle application - LUIS
titleSuffix: Azure Cognitive Services
description: Créez et gérez vos applications sur la page web de Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500307"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Créer une application LUIS dans le portail LUIS
Il existe deux façons de créer une application LUIS. Vous pouvez créer une application LUIS dans le portail [LUIS](https://www.luis.ai) ou par le biais des [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) de création LUIS.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>Utilisation du portail LUIS

Vous pouvez créer une application dans le portail LUIS de plusieurs manières :

* Démarrez avec une application vide et créer des intentions, des énoncés et des entités.
* Démarrez avec une application vide et ajoutez un [domaine prédéfini](luis-how-to-use-prebuilt-domains.md).
* Importez une application LUIS à partir d’un fichier JSON qui contient déjà des intentions, des énoncés et des entités.

## <a name="using-the-authoring-apis"></a>Utilisation des API de création
Vous pouvez créer une application avec les API de création de deux manières :

* [Démarrez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) avec une application vide et créer des intentions, des énoncés et des entités.
* [Démarrez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) avec un domaine prédéfini.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Créer une application dans LUIS

1. Dans la page **Mes applications**, sélectionnez **+ Créer**.

    ![Liste des applications LUIS](./media/luis-create-new-app/apps-list.png)


2. Dans la boîte de dialogue, nommez votre application « TravelAgent ».

    ![Boîte de dialogue Créer une application](./media/luis-create-new-app/create-app.png)

3. Choisissez la culture de votre application (pour l’application TravelAgent, sélectionnez Anglais), puis sélectionnez **Terminé**. 

    > [!NOTE]
    > La culture ne peut pas être modifiée une fois que l’application est créée. 

## <a name="import-an-app-from-file"></a>Importer une application à partir d’un fichier

1. Sur la page **Mes applications**, sélectionnez **Import new app** (Importer une nouvelle application).
1. Dans la boîte de dialogue contextuelle, sélectionnez un fichier JSON d’application valide, puis sélectionnez **Effectué**.

### <a name="import-errors"></a>Erreurs d’importation

Plusieurs erreurs sont possibles : 

* Il existe déjà une application portant ce nom. Pour y remédier, réimportez l’application, puis définissez un autre **Nom facultatif**. 

## <a name="export-app-for-backup"></a>Exporter une application à des fins de sauvegarde

1. Sur la page **Mes applications**, sélectionnez **Exporter**.
1. Sélectionnez **Exporter au format JSON**. Votre navigateur télécharge la version active de l’application.
1. Ajoutez ce fichier à votre système de sauvegarde pour archiver le modèle.

## <a name="export-app-for-containers"></a>Exporter une application pour les conteneurs

1. Sur la page **Mes applications**, sélectionnez **Exporter**.
1. Sélectionnez **Exporter en tant que conteneur**, puis sélectionnez l’emplacement publié (de production ou de préproduction) à exporter.
1. Utilisez ce fichier avec votre [conteneur LUIS](luis-container-howto.md). 

    Si vous souhaitez exporter un modèle entraîné, mais pas encore publié, pour l’utiliser avec le conteneur LUIS, faites-le sur la page **Versions**. 

## <a name="delete-app"></a>Supprimer l’application

1. Sur la page **Mes applications**, sélectionnez les points de suspension (...) à la fin de la ligne de l’application.
1. Sélectionnez **Supprimer** dans le menu.
1. Sélectionnez **OK** dans la fenêtre de confirmation.

## <a name="next-steps"></a>Étapes suivantes

Votre première tâche dans l’application consiste à [ajouter des intentions](luis-how-to-add-intents.md).
