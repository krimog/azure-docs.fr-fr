---
title: Gérer des fermes
description: Décrit comment gérer des fermes
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c83ac8a42a91c9db06726e917ed142e20995b987
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797318"
---
# <a name="manage-farms"></a>Gérer des fermes

Vous pouvez gérer vos fermes dans Azure FarmBeats. Cet article explique comment créer des fermes et installer des appareils, des capteurs et des drones qui vous permettent de gérer vos fermes.

## <a name="create-farms"></a>Créer des fermes

Procédez comme suit :

1. Connectez-vous à Farm Accelerator. La page **Farms** s’affiche.
    La page **Farms** présente la liste des fermes déjà créées dans l’abonnement.

    Cette page se présente comme suit :

    ![Projet FarmBeats](./media/create-farms/create-farm-main-page-1.png)


2. Sélectionnez **Create Farm** et renseignez les champs **Name**, **Crops** (Cultures) et **Address**.
3. Dans le champ obligatoire **Define Farm Boundary** (Définir les limites de la ferme), sélectionnez **Mark on Map** (Marquer sur la carte) ou **Paste GeoJSON code** (Coller le code GeoJSON).

Voici les deux façons de définir les limites d’une ferme :

1. **Mark on Map** : utilisez l’outil de contrôle de carte pour tracer et marquer les limites de la ferme. Pour marquer les limites, cliquez sur ![Projet FarmBeats](./media/create-farms/pencil-icon-1.png) et tracez les limites exactes.

    ![Projet FarmBeats](./media/create-farms/create-farm-mark-on-map-1.png)

2. **Paste GeoJson Code** : GeoJSON est un format permettant d’encoder des structures de données géographiques avec JSON (JavaScript Object Notation). Cette option affiche une zone de texte dans laquelle vous pouvez entrer une chaîne GeoJSON pour marquer les limites de la ferme. Vous pouvez également créer du code GeoJSON à partir de GeoJSON.io.
Aidez-vous des info-bulles pour entrer les informations.

    ![Projet FarmBeats](./media/create-farms/create-new-farm-1.png)

4.  Sélectionnez **Submit** pour créer une ferme. Une ferme est créée et s’affiche dans la page **Farms**.

## <a name="view-farm"></a>Voir une ferme

La page de liste Farms présente la liste des fermes créées. Sélectionnez une ferme pour voir la liste des éléments suivants :

 - **Device count** (Nombre d’appareils) : affiche le nombre d’appareils déployés dans la ferme et leur état.
 - **Map** (Carte) : carte de la ferme avec les appareils qui y sont déployés.
 - **Telemetry** (Données de télémétrie) : affiche les données de télémétrie provenant des capteurs déployés dans la ferme.
 - **Latest Precision Maps** (Dernières cartes de précision) : affiche les dernières cartes d’indices satellite (EVI, NDWI), d’humidité du sol et d’emplacement des capteurs.

## <a name="edit-farm"></a>Modifier une ferme

La page **Farms** présente la liste des fermes créées.

1.  Sélectionnez la ferme que vous souhaitez voir et modifier.
2.  Sélectionnez **Edit Farm** (Modifier la ferme) pour modifier les informations sur la ferme. Dans la fenêtre **Farm Details**, vous pouvez modifier les champs **Name**, **Crops**, **Address** et **Farm Boundary**.

    ![Projet FarmBeats](./media/create-farms/edit-farm-1.png)

3. Sélectionnez **Submit** pour enregistrer les informations modifiées.

## <a name="delete-farm"></a>Supprimer une ferme

La page **Farms** présente la liste des fermes créées. Pour supprimer une ferme, effectuez les étapes suivantes :

1.  Sélectionnez une ferme dans la liste pour supprimer les informations sur la ferme.
2.  Sélectionnez **Delete Farm** pour supprimer la ferme.

    ![Projet FarmBeats](./media/create-farms/delete-farm-1.png)

    > [!NOTE]
    > Quand vous supprimez une ferme, les appareils et cartes associés ne sont pas supprimés. Les informations sur la ferme associées à l’appareil et aux cartes ne seront plus pertinentes. Vous pouvez continuer à visualiser les appareils, les données de télémétrie et les cartes à partir du service FarmBeats.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre ferme, découvrez comment [obtenir les données transmises par les capteurs](get-sensor-data-from-sensor-partner.md) de votre ferme.
