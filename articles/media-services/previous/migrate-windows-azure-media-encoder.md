---
title: Migrer de Windows Azure Media Encoder vers Media Encoder Standard | Microsoft Docs
description: Cette rubrique explique comment migrer d'Azure Media Encoder vers le processeur multimédia Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 1f4760713eccd612014f6b75a1623dd9ad0c8c0f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595506"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrer de Windows Azure Media Encoder vers Media Encoder Standard

Cet article décrit les étapes de migration du processeur multimédia Windows Azure Media Encoder (WAME) hérité, dont la mise hors service est prévue pour le 30 novembre 2019, vers le processeur multimédia Media Encoder Standard.

Lorsqu'ils encodaient des fichiers avec WAME, les clients utilisaient généralement une chaîne prédéfinie nommée telle que `H264 Adaptive Bitrate MP4 Set 1080p`. Pour migrer, votre code doit être mis à jour afin d'utiliser le processeur multimédia **Media Encoder Standard** au lieu de WAME, et l'une des [valeurs système prédéfinies](media-services-mes-presets-overview.md) équivalentes, comme `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migration vers Media Encoder Standard

Voici un exemple typique de code C# utilisant le composant hérité. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Et voici la version mise à jour utilisant Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Scénarios avancés 

Si vous avez créé vos propres valeurs d'encodage prédéfinies pour WAME à l'aide de son schéma, il existe un [schéma équivalent pour Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Différences connues 

Plus robuste et plus fiable que l'encodeur WAME hérité, Media Encoder Standard offre également de meilleures performances et génère une sortie de meilleure qualité . En outre : 

* Media Encoder Standard génère des fichiers de sortie basés sur une convention d'affectation de noms différente de celle de WAME.
* Media Encoder Standard génère des artefacts tels que des fichiers contenant les [métadonnées des fichiers d'entrée](media-services-input-metadata-schema.md) et les [métadonnées des fichiers de sortie](media-services-output-metadata-schema.md).
* Comme indiqué sur la [page des tarifs](https://azure.microsoft.com/pricing/details/media-services/#encoding) (en particulier dans la section FAQ), lorsque vous encodez des vidéos à l'aide de Media Encoder Standard, vous êtes facturé en fonction de la durée des fichiers de sortie produits. Avec WAME, vous seriez facturé en fonction de la taille des fichiers vidéo d'entrée et de sortie.

## <a name="need-help"></a>Vous avez besoin d’aide ?

Vous pouvez ouvrir un ticket de support en accédant à [Nouvelle demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Étapes suivantes

* [Composants hérités](legacy-components.md)
* [Page des tarifs](https://azure.microsoft.com/pricing/details/media-services/#encoding)
