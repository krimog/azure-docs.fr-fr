---
title: Prise en main de Custom Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech est un ensemble d’outils en ligne qui vous permet d’évaluer et d’améliorer la précision de notre reconnaissance vocale pour vos applications, outils et produits. Pour bien démarrer, il vous suffit de quelques fichiers audio de test. Suivez les liens ci-dessous pour commencer à créer une expérience de reconnaissance vocale personnalisée.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 0f3b7826fdcc16d3094785507ec8263da39d6284
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881514"
---
# <a name="what-is-custom-speech"></a>Qu’est-ce que Custom Speech ?

[Custom Speech](https://aka.ms/customspeech) est un ensemble d’outils en ligne qui vous permet d’évaluer et d’améliorer la précision de la reconnaissance vocale de Microsoft pour vos applications, outils et produits. Pour bien démarrer, il vous suffit de quelques fichiers audio de test. Suivez les liens ci-dessous pour commencer à créer une expérience de reconnaissance vocale personnalisée.

## <a name="whats-in-custom-speech"></a>Qu’y a-t-il dans Custom Speech ?

Pour pouvoir tirer parti de Custom Speech, vous avez besoin d’un compte Azure et d’un abonnement Speech Services. Dès lors que vous disposez d’un compte, vous pouvez préparer vos données, entraîner et tester vos modèles, inspecter la qualité de la reconnaissance, évaluer la précision et en dernier lieu déployer et utiliser le modèle de reconnaissance vocale personnalisé.

Ce diagramme présente les différents éléments qui composent le [portail Custom Speech](https://aka.ms/customspeech). Utilisez les liens ci-dessous pour en savoir plus sur chaque étape.

![Présente les différents composants qui constituent le portail Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [S’abonner et créez un projet](#set-up-your-azure-account) – Créez un compte Azure et abonnez-vous à Speech Services. Cet abonnement unifié vous donne accès à la reconnaissance vocale, à la synthèse vocale, à la traduction vocale et au [portail Custom Speech](https://speech.microsoft.com/customspeech). Ensuite, servez-vous de votre abonnement Speech Services pour créer votre premier projet Custom Speech.

2. [Charger des données de test](how-to-custom-speech-test-data.md) – Chargez des données de test (fichiers audio) pour évaluer l’offre de reconnaissance vocale de Microsoft pour vos applications, outils et produits.

3. [Inspecter la qualité de la reconnaissance](how-to-custom-speech-inspect-data.md) - Utilisez le [portail Custom Speech](https://speech.microsoft.com/customspeech) pour lire le contenu audio chargé et inspecter la qualité de la reconnaissance vocale de vos données de test. Pour effectuer des mesures quantitatives, consultez [Inspecter des données](how-to-custom-speech-inspect-data.md).

4. [Évaluer la précision](how-to-custom-speech-evaluate-data.md) – Évaluez la précision du modèle de reconnaissance vocale. Le [portail Custom Speech](https://speech.microsoft.com/customspeech) indique un *taux d’erreur de mots*, qui permet de déterminer si un entraînement supplémentaire est nécessaire. Si vous êtes satisfait de la précision, vous pouvez directement utiliser les API Speech Services. Si vous souhaitez améliorer la précision de 5 à 20 % en moyenne relative, utilisez l’onglet **Training** (Entraînement) du portail pour charger des données d’entraînement supplémentaires, comme des transcriptions étiquetées à la main et le texte associé.

5. [Entraîner le modèle](how-to-custom-speech-train-model.md) – Améliorez la précision de votre modèle de reconnaissance vocale en fournissant des transcriptions écrites (entre 10 et 1 000 heures) et le texte associé (< 200 Mo) ainsi que vos données de test audio. Ces données vous aident à entraîner le modèle de reconnaissance vocale. Après l’entraînement, effectuez un nouveau test. Si vous êtes satisfait du résultat, vous pouvez déployer votre modèle.

6. [Déployer le modèle](how-to-custom-speech-deploy-model.md) – Créez un point de terminaison personnalisé pour votre modèle de reconnaissance vocale et utilisez-le dans vos applications, outils ou produits.

## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Vous avez besoin d’un abonnement Speech Services pour pouvoir créer un modèle personnalisé à partir du [portail Custom Speech](https://speech.microsoft.com/customspeech). Suivez ces instructions pour créer un abonnement standard Speech Services : [Créer un abonnement Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Veillez à créer des abonnements standard (S0) ; les abonnements d’essai gratuit (F0) ne sont pas pris en charge.

Une fois que vous avez créé un compte Azure et un abonnement Speech Services, vous devez vous connecter au [portail Custom Speech](https://speech.microsoft.com/customspeech) et connecter votre abonnement.

1. Obtenez votre clé d’abonnement Speech Services sur le portail Azure.
2. Connectez-vous au [portail Custom Speech](https://aka.ms/custom-speech).
3. Sélectionnez l’abonnement dont vous avez besoin et créez un projet Speech.
4. Si vous souhaitez modifier votre abonnement, utilisez l’icône représentant une **roue dentée** dans le volet de navigation du haut.

## <a name="how-to-create-a-project"></a>Comment créer un projet

Le contenu que représentent les données, les modèles, les tests et les points de terminaison est organisé en **projets** sur le [portail Custom Speech](https://speech.microsoft.com/customspeech). Chaque projet est propre à un domaine et à un pays/langue. Par exemple, vous pouvez créer un projet pour des centres d’appel dont la langue est l’anglais des États-Unis.

Pour créer votre premier projet, sélectionnez **Speech-to-text/Custom speech**, puis cliquez sur **New Project**. Suivez les instructions fournies par l’Assistant pour créer votre projet. Une fois le projet créé, vous devez disposer de quatre onglets : **Data**, **Testing**, **Training** et **Deployment**. Utilisez les liens fournis dans [Étapes suivantes](#next-steps) pour savoir comment utiliser chaque onglet.

## <a name="next-steps"></a>Étapes suivantes

* [Préparer et tester les données](how-to-custom-speech-test-data.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer les données](how-to-custom-speech-evaluate-data.md)
* [Entraîner un modèle](how-to-custom-speech-train-model.md)
* [Déployer un modèle](how-to-custom-speech-deploy-model.md)
