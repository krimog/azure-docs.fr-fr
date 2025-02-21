---
title: Nouveautés du service
titleSuffix: Azure Cognitive Search
description: Annonces de fonctionnalités nouvelles et améliorées, notamment le changement de Recherche Azure en Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a2967ab40da8594b2d6372d5da816a6c76c9109
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721716"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nouveauté dans Recherche cognitive Azure

Découvrir les nouveautés du service. Marquez cette page pour rester aux faits des nouveautés du service.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nouveau nom du service Recherche Azure

Recherche Azure est maintenant renommé  **Recherche cognitive Azure** pour refléter l’utilisation étendue de compétences cognitives et du traitement par IA dans les opérations de base. Alors que les compétences cognitives ajoutent de nouvelles fonctionnalités, l’utilisation de l’IA est strictement facultative. Vous pouvez continuer à utiliser la Recherche cognitive Azure sans l’IA pour générer des solutions de recherche en texte intégral riches sur du contenu basé sur du texte, privé et hétérogène, dans un index que vous créez et gérez dans le cloud. 

Les versions d’API, les packages NuGet, les espaces de noms et les points de terminaison ne sont pas modifiés. Vos solutions de recherche existantes ne sont pas affectées par le changement de nom du service.

## <a name="feature-announcements"></a>Annonces de fonctionnalités

4 novembre 2019 : conférence Ignite

+ L’[indexation incrémentielle (préversion)](cognitive-search-incremental-indexing-conceptual.md) vous permet de traiter ou de retraiter seulement les étapes qui sont absolument nécessaires quand vous apportez des modifications à un pipeline d’enrichissement. Cela s’avère particulièrement utile si vous avez du contenu d’image que vous avez analysé précédemment. La sortie de l’analyse coûteuse est stockée, puis utilisée comme base pour une indexation ou un enrichissement supplémentaires.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L’[Extraction de document (préversion)](cognitive-search-skill-document-extraction.md) est une compétence cognitive utilisée lors de l’indexation, qui vous permet d’extraire le contenu d’un fichier à partir d’un ensemble de compétences. Auparavant, le craquage de document avait exclusivement lieu avant l’exécution de l’ensemble de compétences. Avec l’ajout de cette compétence, vous pouvez également effectuer cette opération dans le cadre de l’exécution de l’ensemble de compétences.

+ La [Traduction de texte (préversion)](cognitive-search-skill-text-translation.md) est une compétence cognitive utilisée lors de l’indexation, qui évalue le texte et qui, pour chaque enregistrement, retourne le texte traduit dans la langue cible spécifiée.

+ Les [Modèles Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) peuvent lancer rapidement vos visualisations et l’analyse de contenu enrichi dans un magasin de connaissances dans Power BI Desktop. Ce modèle est conçu pour les projections de tables Azure créées à l’aide de l’[Assistant Importer des données](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (préversion)](search-howto-index-azure-data-lake-storage.md), l’[API Gremlin de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) et l’[API Cassandra de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) sont désormais pris en charge dans les indexeurs. Vous pouvez vous inscrire avec [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). Vous recevez un e-mail de confirmation une fois que vous avez été accepté dans le programme de préversion.

## <a name="service-updates"></a>Mises à jour de service

[Les annonces de mise à jour de service](https://azure.microsoft.com/updates/?product=search&status=all) pour Recherche cognitive Azure sont disponibles sur le site web Azure.