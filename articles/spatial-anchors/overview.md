---
title: Vue d’ensemble d’Azure Spatial Anchors | Microsoft Docs
description: Découvrez en quoi Azure Spatial Anchors vous aide à développer des expériences de réalité mixte multiplateformes.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2451922f0eb49a5ccee036db72eb046760287dca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60240482"
---
# <a name="azure-spatial-anchors-overview"></a>Vue d’ensemble d’Azure Spatial Anchors

Bienvenue dans Azure Spatial Anchors. Azure Spatial Anchors permet aux développeurs de générer des applications de réalité mixte sensibles à l’espace à l’aide de fonctionnalités essentielles. Ces applications peuvent prendre en charge Microsoft HoloLens, les appareils iOS prenant en charge ARKit ainsi que les appareils Android prenant en charge ARCore. Azure Spatial Anchors permet aux développeurs de travailler sur des plateformes de réalité mixte afin de percevoir les espaces, de désigner des points d’intérêt précis et de rappeler ces mêmes points d’intérêt sur les appareils pris en charge.
Ces points d’intérêt précis sont appelés ancres spatiales.

![Multiplateforme](./media/cross-platform.png)

## <a name="examples"></a>Exemples

Voici quelques exemples de cas d’usage rendus possibles par Spatial Anchors :

- [Expériences multi-utilisateurs](tutorials/tutorial-share-anchors-across-devices.md). Spatial Anchors permet à des personnes situées dans un même lieu d’utiliser facilement des applications de réalité mixte multi-utilisateurs. Par exemple, deux personnes peuvent démarrer un jeu d’échecs de réalité mixte en plaçant un échiquier virtuel sur une table. Ensuite, en pointant leur appareil vers la table, ils peuvent voir l’échiquier virtuel et interagir avec celui-ci.

- [Guidage](concepts/anchor-relationships-way-finding.md). Les développeurs peuvent aussi connecter plusieurs ancres spatiales et créer des relations entre elles. Par exemple, une application peut présenter une expérience constituée de plusieurs points d’intérêt avec lesquels un utilisateur doit interagir pour effectuer une tâche. Ces points d’intérêt peuvent être créés de manière connectée. Par la suite, quand l’utilisateur termine la tâche multi-étape, l’application peut demander les ancres situées à proximité de l’ancre active pour diriger l’utilisateur vers la prochaine étape de la tâche.

- [Conservation du contenu virtuel dans le monde réel](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Une application peut permettre à un utilisateur de placer un calendrier virtuel sur le mur d’une salle de conférence, que des personnes pourront voir à l’aide d’une application de téléphone ou d’un appareil HoloLens. Dans un environnement industriel, un utilisateur pourrait obtenir des informations contextuelles sur une machine en pointant vers celle-ci l’appareil photo d’un appareil pris en charge.

Azure Spatial Anchors se compose d’un service managé et de kits SDK clients pour les plateformes d’appareils prises en charge. Les sections suivantes indiquent comment commencer à générer des applications à l’aide d’Azure Spatial Anchors.

## <a name="next-steps"></a>Étapes suivantes

Créez votre première application avec Spatial Anchors.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
