---
title: Qualifications intégrées pour l’extraction de données, le langage naturel et le traitement des images pendant l’indexation.
titleSuffix: Azure Cognitive Search
description: Les compétences cognitives pour l’extraction de données, le langage naturel et le traitement des images renforcent la sémantique et la structure du contenu brut dans un pipeline Recherche cognitive Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2f03752c5fda366fc16b681d71ba68486527a30f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466802"
---
# <a name="built-in-cognitive-skills-for-content-enrichment-azure-cognitive-search"></a>Compétences cognitives intégrées pour l’enrichissement de contenu (Recherche cognitive Azure)

Cet article décrit les compétences cognitives fournies avec Recherche cognitive Azure. Une *compétence cognitive* est une opération qui transforme du contenu d’une certaine façon. Souvent, il s’agit d’un composant qui extrait des données ou déduit une structure, renforçant ainsi notre compréhension des données d’entrée. Presque toujours, la sortie est basée sur du texte. Un *ensemble de compétences* est la collection des compétences qui définissent le pipeline d’enrichissement. 

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant plusieurs algorithmes d’IA, vous devez [attacher une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais s’appliquent durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images dans le cadre de la phase de craquage de document de la Recherche cognitive Azure. L’extraction de texte à partir des documents est gratuite.
>
> L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l’extraction d’images sont présentés sur la [page de tarification du service Recherche cognitive Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="built-in-skills"></a>Compétences prédéfinies

Diverses compétences sont flexibles du point de vue de ce qu’elles consomment ou produisent. En règle générale, la plupart des compétences sont basées sur des modèles préformés, ce qui signifie que vous ne pouvez pas effectuer l’apprentissage du modèle à l’aide de vos propres données d’apprentissage. Le tableau suivant énumère et décrit les compétences fournies par Microsoft. 

| Compétence | Description |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Cette compétence utilise un modèle préformé pour détecter des phrases importantes en fonction de la position du terme, des règles linguistiques, de la proximité d’autres termes et du caractère inhabituel du terme dans la source de données. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Cette compétence utilise un modèle préformé pour détecter la langue utilisée (un ID de langue par document). Quand plusieurs langues sont utilisées dans les mêmes segments de texte, la sortie est le LCID de la langue utilisée principalement.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolide en un champ unique du texte issu d’une collection de champs.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Cette compétence utilise un modèle préentraîné pour établir des entités pour un ensemble fixe de catégories : personnes, emplacement, organisation, e-mails, URL, champs d’horodatage. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Cette compétence utilise un modèle préformé pour évaluer un sentiment positif ou négatif enregistrement par enregistrement. Le score est compris entre 0 et 1. Les scores neutres se produisent dans les cas Null où aucun sentiment n’est détectable et quand le texte est considéré comme neutre.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Fractionne le texte en pages afin que vous puissiez enrichir ou augmenter le contenu de façon incrémentielle. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Cette qualification utilise un modèle préformé pour traduire le texte d’entrée en plusieurs langues pour les cas d’usage de normalisation ou de localisation. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Cette compétence utilise un algorithme de détection d’image pour identifier le contenu d’une image et générer un texte descriptif. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconnaissance optique de caractères. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Permet le filtrage, l’attribution d’une valeur par défaut et la fusion de données selon une condition.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrait le contenu d’un fichier dans le pipeline d’enrichissement. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mappe la sortie sur un type complexe (type de données de plusieurs parties, qui peut être utilisé pour un nom complet, une adresse sur plusieurs lignes ou une combinaison d’un nom et d’un identificateur personnel.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permet l’extensibilité du pipeline d’enrichissement de l’intelligence artificielle en passant un appel HTTP dans une API web personnalisée |


Pour obtenir des conseils sur la création d’une [compétence personnalisée](cognitive-search-custom-skill-web-api.md), consultez [Guide pratique pour définir une interface personnalisée](cognitive-search-custom-skill-interface.md) et [Exemple : Création d’une compétence personnalisée pour l’enrichissement de l’IA](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Voir aussi

+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Définition d’une interface de compétences personnalisée](cognitive-search-custom-skill-interface.md)
+ [Tutoriel : Indexation enrichie avec l’IA](cognitive-search-tutorial-blob.md)
