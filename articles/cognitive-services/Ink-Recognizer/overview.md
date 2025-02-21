---
title: Qu’est-ce qu’Ink Recognizer ? - API Ink Recognizer
titleSuffix: Azure Cognitive Services
description: Intégrez Ink Recognizer à vos applications, sites web, outils et autres solutions pour identifier les données de trait d’encre et les utiliser comme entrée.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: c90d656539a2ed64b416dc9b8e7e11e205b98ee6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478374"
---
# <a name="what-is-the-ink-recognizer-api"></a>Qu’est-ce que l’API Ink Recognizer ?


Le service cognitif Ink Recognizer propose une API REST cloud pour analyser et reconnaître le contenu d’une entrée manuscrite numérique. Contrairement aux services qui utilisent la reconnaissance optique de caractères (OCR), l’API a besoin de données de trait d’encre numérique en tant qu’entrée. Les traits d’encre numériques sont des jeux chronologiques de points 2D (coordonnées X,Y) qui représentent le mouvement des outils d’entrée comme les stylets numériques ou les doigts. Le service reconnaît ensuite les formes et le contenu manuscrit d’après l’entrée, puis retourne une réponse JSON contenant toutes les entités reconnues.

![Organigramme décrivant l’envoi d’une entrée de trait d’encre à l’API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Caractéristiques

Avec l’API Ink Recognizer, vous pouvez facilement reconnaître du contenu manuscrit dans vos applications. 

|Fonctionnalité  |Description  |
|---------|---------|
| Reconnaissance d’écriture manuscrite | Reconnaissez le contenu manuscrit dans 63 [langues](language-support.md). | 
| Reconnaissance de disposition | Obtenez des informations structurelles sur le contenu d’une entrée manuscrite numérique. Divisez le contenu en régions d’écriture, paragraphes, lignes, mots, listes à puces. Vos applications peuvent alors utiliser ces informations de disposition pour créer des fonctionnalités supplémentaires comme la mise en forme de liste automatique et l’alignement des formes. |
| Reconnaissance de formes | Reconnaissez les [formes géométriques](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) les plus couramment utilisées lors de la prise de notes. |
| Reconnaissance de mélanges de formes et de texte | Reconnaissez les traits d’encre qui appartiennent à des formes ou à du contenu manuscrit, puis classez-les séparément.|

## <a name="workflow"></a>Workflow

L’API Ink Recognizer étant un service web RESTful, elle peut être facilement appelée à partir de n’importe quel langage de programmation capable d’exécuter des requêtes HTTP et d’analyser des réponses JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Après l’inscription :

1. Prenez vos données de trait d’encre et [mettez-les au format](concepts/send-ink-data.md#sending-ink-data) JSON valide.
1. Envoyez une requête à l’API Ink Recognizer avec vos données.
1. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de démarrage rapide sur les langages suivants pour commencer à effectuer des appels à l’API Ink Recognizer.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Pour observer le fonctionnement de l’API Ink Recognizer dans une application d’écriture manuscrite numérique, examinez les exemples d’applications suivants sur GitHub :
* [C# et UWP (plateforme Windows universelle)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# et WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Application de navigateur web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Application mobile Android et Java](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Application mobile Swift et iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
