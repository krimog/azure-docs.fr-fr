---
title: Suivi des données lors du repérage dans Azure Sentinel à l’aide de signets de repérage | Microsoft Docs
description: Cet article décrit comment utiliser les signets de repérage d’Azure Sentinel pour effectuer le suivi des données.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: aa414e37470cc11b7dc83e7416590aa2babf6818
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240251"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Suivi des données lors du repérage avec Azure Sentinel

Le repérage des menaces nécessite généralement la vérification de montagnes de données de journal à la recherche de preuves de comportements malveillants. Pendant ce processus, les enquêteurs détectent des événements qu’ils souhaitent mémoriser, réexaminer et analyser afin de valider des hypothèses potentielles et de comprendre d’une compromission dans sa globalité.

Dans Azure Sentinel, les signets de repérage vous y aident, en conservant les requêtes que vous avez exécutées dans Log Analytics, ainsi que les résultats de requête que vous jugez pertinents. Vous pouvez également enregistrer vos observations contextuelles et référencer vos découvertes en ajoutant des balises et des notes. Les données avec signet sont visibles par vous et vos collègues pour faciliter la collaboration.

Vous pouvez revenir consulter vos données avec signet à tout moment dans l’onglet **Signet** du volet **Repérage**. Vous pouvez utiliser les options de filtrage et de recherche pour trouver rapidement des données spécifiques dans le cadre de l’examen en cours. Vous pouvez également afficher vos données avec signet directement dans la table **HuntingBookmark** d'Azure Monitor. Cela vous permet de filtrer les données avec signet, les synthétiser et les joindre à d’autres sources de données, ce qui facilite la recherche de preuves pour la confirmation.

Actuellement en préversion, si vous trouvez des éléments qui doivent être traités de manière urgente dans vos journaux, en quelques clics, vous pouvez créer un signet et le transformer en incident, ou ajouter le signet à un incident existant. Pour plus d’informations sur les incidents, consultez [Didacticiel : Examiner les incidents avec Azure Sentinel](tutorial-investigate-cases.md). 

Également en préversion, vous pouvez visualiser vos données avec signet en cliquant sur **Examiner** dans les détails du signet. Cette opération lance l’expérience d’examen dans laquelle vous pouvez afficher, examiner et communiquer visuellement vos résultats à l’aide d’un diagramme d’entité-graphique interactif et d’une chronologie.

## <a name="add-a-bookmark"></a>Ajouter un signet

1. Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage** pour exécuter des requêtes liées à un comportement suspect et anormal.

2. Sélectionnez une requête de repérage et à droite, dans les détails de la requête repérage, sélectionnez **Exécuter la requête**. 

3. Sélectionnez **Afficher les résultats de la requête**. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![afficher les résultats d’une requête à partir d'un repérage Azure Sentinel](./media/bookmarks/new-processes-observed-example.png)
    
    Cette action ouvre les résultats de la requête dans le volet **Journaux**.

4. Dans la liste des résultats de requête de journal, développez la ligne contenant les informations qui vous intéressent.

5. Sélectionnez les points de suspension (...) sur la gauche, puis **Ajouter un signet de repérage** :
    
    > [!div class="mx-imgBorder"]
    > ![Ajouter un signet de repérage à une requête](./media/bookmarks/add-hunting-bookmark.png)

6. À droite, dans le volet **Ajouter un signet de repérage**, vous pouvez mettre à jour le nom du signet et ajouter des balises et des notes pour identifier facilement les informations intéressantes à propos de l’élément.

7. Dans la section **Informations sur la requête**, utilisez les zones de liste déroulante afin d'extraire des informations des résultats de la requête pour les types d’entités **Compte**, **Hôte** et **Adresse IP**. Cette action mappe le type d’entité sélectionné à une colonne spécifique à partir du résultat de la requête. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![Mapper les types d’entité pour le signet de repérage](./media/bookmarks/map-entity-types-bookmark.png)
    
    Pour afficher le signet dans le graphe d’examen (actuellement en préversion), vous devez mapper au moins un type d’entité correspondant à **Compte**, **Hôte** ou **Adresse IP**. 

5. Cliquez sur **Ajouter** pour valider vos modifications et ajouter le signet. Toutes les données avec signet sont partagées avec d’autres enquêteurs et constituent une première étape vers une expérience d’examen collaborative.

 
> [!NOTE]
> Les résultats de la requête de journal prennent en charge les signets chaque fois que ce volet est ouvert à partir d’Azure Sentinel. Par exemple, vous sélectionnez **Général** > **Journaux** dans la barre de navigation, sélectionnez les liens d’événements dans le graphe d'examen ou sélectionnez un ID d’alerte dans les détails complets d’un incident (actuellement en préversion). Vous ne pouvez pas créer de signets lorsque le volet **Journaux** est ouvert à partir d’autres emplacements, par exemple directement depuis Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Afficher et mettre à jour des signets 

1. Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage**. 

2. Sélectionnez l’onglet **Signets** pour afficher la liste des signets.

3. Utilisez les options de filtre ou de la zone de recherche pour trouver un signet spécifique.

4. Sélectionnez des signets individuels et affichez les détails de ce signet dans le volet de détails de droite.

5. Apportez les modifications nécessaires. Celles-ci sont automatiquement enregistrées.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Exploration des signets dans le graphe d’examen

> [!IMPORTANT]
> L’exploration des signets dans le graphe d’examen et le graphe d’examen lui-même sont actuellement en préversion publique.
> Ces fonctionnalités sont fournies sans contrat de niveau de service et sont déconseillées pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage** > **Signets**, puis sélectionnez le(s) signet(s) que vous souhaitez examiner.

2. Dans les détails du signet, assurez-vous qu’au moins une entité est mappée. Par exemple, pour **ENTITÉS**, vous voyez des entrées pour **IP**, **Machine** ou **Compte**.

3. Cliquez sur **Examiner** pour afficher le signet dans le graphe d’examen.

Pour savoir comment utiliser le graphe d’examen, consultez [Utiliser le graphe d’examen pour approfondir les recherches](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Ajouter des signets à un incident nouveau ou existant

> [!IMPORTANT]
> L’ajout de signets à un incident nouveau ou existant est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage** > **Signets**, puis sélectionnez le(s) signet(s) que vous souhaitez ajouter à un incident.

2. Sélectionnez **Actions d'incident (préversion)** dans la barre de commandes :
    
    > [!div class="mx-imgBorder"]
    > ![Ajouter des signets à l'incident](./media/bookmarks/incident-actions.png)

3. Sélectionnez **Créer un incident** ou **Ajouter à un incident existant**, si besoin. Ensuite :
    
    - Pour un nouvel incident : Vous pouvez également mettre à jour les détails de l'incident, puis sélectionner **Créer**.
    - Pour ajouter un signet à un incident existant : Sélectionnez un incident, puis **Ajouter**. 

Pour afficher le signet dans l’incident : Accédez à **Sentinel** > **Gestion des menaces** > **Incidents**, puis sélectionnez l'incident avec votre signet. Sélectionnez **Afficher les détails**, puis l'onglet **Signets**.

## <a name="view-bookmarked-data-in-logs"></a>Afficher les données avec signet dans les journaux

Pour afficher les requêtes avec signet, les résultats ou l’historique, sélectionnez le signet dans l'onglet **Repérage** > **Signets** et utilisez les liens fournis dans le volet de détails : 

- Utilisez **Afficher la requête source** pour afficher la requête source dans le volet **Journaux**.

- **Afficher les journaux des signets** pour afficher toutes les métadonnées du signet, notamment l’auteur de la mise à jour, les valeurs mises à jour et l’heure de la mise à jour.

Vous pouvez également afficher les données de signets brutes pour tous les signets en sélectionnant **Journaux de signets** dans le barre de commandes de l'onglet **Repérage** > **Signets** :

> [!div class="mx-imgBorder"]
> ![Journaux des signets](./media/bookmarks/bookmark-logs.png)

Cet affichage montre tous vos signets avec les métadonnées associées. Vous pouvez utiliser des requêtes [Keyword Query Language](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) KQL pour filtrer jusqu’à la dernière version du signet spécifique que vous recherchez.

> [!NOTE]
> Il peut y avoir un délai important (mesuré en minutes) entre la création d’un signet et le moment où il apparaît dans l'onglet **Signets**.

## <a name="delete-a-bookmark"></a>Supprimer un signet
 
1.  Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage** > **Signets**, puis sélectionnez le(s) signet(s) que vous souhaitez supprimer. 

2. Cliquez sur les points de suspension (...) à la fin de la ligne, puis sélectionnez **Supprimer le signet**.
    
La suppression du signet supprime le signet de la liste dans l’onglet **Signet**. La table **HuntingBookmark** de Log Analytics contient toujours les précédentes entrées de signet, mais la valeur de la dernière entrée pour **SoftDelete** devient true, ce qui vous permet d’exclure les anciens signets. La suppression d’un signet ne supprime pas les entités de l’expérience d’examen qui sont associées à d’autres alertes ou signets. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exécuter un examen de repérage à l’aide de signets dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :


- [Proactively hunt for threats](hunting.md) (Rechercher des menaces de façon proactive)
- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des blocs-notes pour exécuter des campagnes de repérage automatisées)
