---
title: Langages pris en charge dans Azure Functions
description: Découvrez les langages qui sont pris en charge (Disponibilité générale) et ceux qui sont en version expérimentale ou en préversion.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148820"
---
# <a name="supported-languages-in-azure-functions"></a>Langages pris en charge dans Azure Functions

Cet article explique les niveaux de prise en charge offerts pour les langages que vous pouvez utiliser avec Azure Functions.

## <a name="levels-of-support"></a>Niveaux de prise en charge

Il existe trois niveaux de prise en charge :

* **Disposition générale (GA)** : entièrement pris en charge et approuvé pour la production.
* **Préversion** : pas encore pris en charge, mais le statut de disponibilité générale est prévu à l’avenir.
* **Expérimental** : pas pris en charge et peut être abandonné à l’avenir. Aucune garantie quant à une éventuelle préversion ou un passage à l’état de disponibilité générale.

## <a name="languages-in-runtime-1x-and-2x"></a>Langages dans les runtimes 1.x et 2.x

[Deux versions du runtime Azure Functions](functions-versions.md) sont disponibles. Le tableau suivant montre les langages qui sont pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Langages expérimentaux

Les langages expérimentaux dans la version 1.x ne sont pas très propices à la mise à l’échelle et ne prennent pas en charge toutes les liaisons.

N’utilisez pas les fonctionnalités expérimentales pour tout ce qui est important, car il n’existe aucune prise en charge officielle pour celles-ci. Évitez d’ouvrir des cas de support pour les problèmes liés aux langages expérimentaux. 

Le runtime en version 2.x ne prend pas en charge les langages expérimentaux. La prise en charge de nouveaux langages est ajoutée uniquement lorsque le langage peut être pris en charge dans les environnements de production. 

### <a name="language-extensibility"></a>Extensibilité de langage

Le runtime 2.x est conçu pour offrir une [extensibilité de langage](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Les langages JavaScript et Java dans le runtime 2.x sont générés avec cette extensibilité.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon d’utiliser l’un des langages GA ou Préversion dans Azure Functions, consultez les ressources suivantes :

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
