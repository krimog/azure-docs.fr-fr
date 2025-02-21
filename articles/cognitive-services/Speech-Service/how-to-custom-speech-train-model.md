---
title: Entraîner un modèle pour Custom Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: L’entraînement de la reconnaissance vocale est nécessaire pour améliorer la précision de la reconnaissance du modèle de référence de Microsoft ou d’un modèle personnalisé que vous avez l’intention de créer. L’entraînement d’un modèle s’appuie sur des transcriptions étiquetées à la main et sur le texte associé. Ces jeux de données et les données audio chargées précédemment servent à affiner et entraîner le modèle de reconnaissance vocale pour reconnaître les mots, les expressions, les acronymes, les noms et d’autres termes propres à un produit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 21a0cd5e257f57a9371a30c6fe57afb88e174b53
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801470"
---
# <a name="train-a-model-for-custom-speech"></a>Entraîner un modèle pour Custom Speech

L’entraînement de la reconnaissance vocale est nécessaire pour améliorer la précision de la reconnaissance du modèle de référence de Microsoft ou d’un modèle personnalisé que vous avez l’intention de créer. L’entraînement d’un modèle s’appuie sur des transcriptions étiquetées à la main et sur le texte associé. Ces jeux de données et les données audio chargées précédemment servent à affiner et entraîner le modèle de reconnaissance vocale pour reconnaître les mots, les expressions, les acronymes, les noms et d’autres termes propres à un produit. Plus vous fournirez de jeux de données propres au domaine (données en rapport avec ce que les utilisateurs diront et ce qui, selon vous, sera reconnu), plus votre modèle sera précis, ce qui aura pour effet d’améliorer la reconnaissance. Gardez à l’esprit qu’en incorporant des données sans rapport dans votre entraînement, vous pouvez amoindrir la précision de votre modèle ou nuire à celle-ci.

## <a name="use-training-to-resolve-accuracy-issues"></a>Utiliser l’entraînement pour résoudre les problèmes de précision

Si vous rencontrez des problèmes de reconnaissance avec votre modèle, l’utilisation de transcriptions étiquetées à la main et de données associées peuvent contribuer à améliorer la précision. Servez-vous de ce tableau pour savoir quel jeu de données utiliser pour résoudre vos problèmes :

| Cas d’utilisation | Type de données |
|----------|-----------|
| Améliorez la précision de la reconnaissance du vocabulaire ou de la grammaire spécifiques d’un secteur, par exemple, la terminologie médicale ou le jargon informatique | Texte associé (phrases/énoncés) |
| Définissez la forme phonétique et affichée d’un mot ou d’un terme dont la prononciation n’est pas standard, par exemple, les noms de produits ou les acronymes. | Texte associé (prononciation) |
| Améliorez la précision de la reconnaissance des styles de discours, des accents ou des bruits de fond spécifiques | Transcriptions audio + étiquetées à la main |
> [!IMPORTANT]
> Si vous n’avez pas chargé de jeu de données, consultez [Préparer et tester les données](how-to-custom-speech-test-data.md). Ce document donne des instructions pour charger des données et des recommandations visant à créer des jeux de données de grande qualité.

## <a name="train-and-evaluate-a-model"></a>Entraîner et évaluer un modèle

La première étape pour entraîner un modèle consiste à charger des données d’entraînement. Consultez [Préparer et tester les données](how-to-custom-speech-test-data.md) pour préparer des transcriptions étiquetées à la main et du texte associé (énoncés et prononciations) en suivant des instructions pas à pas. Après avoir chargé des données d’entraînement, suivez ces instructions pour commencer à entraîner votre modèle :

1. Connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech).
2. Accédez à **Speech-to-text > Custom Speech > Training**.
3. Cliquez sur **Train model**.
4. Ensuite, nommez et décrivez votre entraînement dans les champs **Name** et **Description**.
5. À partir du menu déroulant **Scenario and Baseline model** (Scénario et modèle de référence), sélectionnez le scénario qui correspond le mieux à votre domaine. Si vous ne savez pas quel scénario choisir, sélectionnez **General**. Le modèle de référence est le point de départ de votre entraînement. Si vous n’avez pas de préférence, vous pouvez utiliser la dernière version.
6. Dans la page **Select training data** (Sélectionner les données d’entraînement), choisissez le ou les jeux de données de transcriptions audio + étiquetées à la main à utiliser pour l’entraînement.
7. Une fois l’entraînement terminé, vous pouvez choisir d’effectuer le test de précision sur le modèle nouvellement entraîné. Cette étape est facultative.
8. Sélectionnez **Create** pour générer votre modèle personnalisé.

La table Training (Entraînement) comporte une nouvelle entrée correspondant à ce nouveau modèle. Elle indique également l’état :  Processing, Succeeded, Failed (En cours de traitement, Réussite, Échec).

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Évaluer la précision d’un modèle entraîné

Vous pouvez inspecter les données et évaluer la précision du modèle à l’aide de ces documents :

* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer les données](how-to-custom-speech-evaluate-data.md)


Si vous choisissez de tester la précision, il est important de sélectionner un jeu de données acoustique différent de celui que vous avez utilisé avec votre modèle pour vous faire une idée réaliste des performances du modèle.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer un modèle](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester les données](how-to-custom-speech-test-data.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer les données](how-to-custom-speech-evaluate-data.md)
* [Entraîner un modèle](how-to-custom-speech-train-model.md)
