---
title: Exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud – Étapes de déploiement
description: Étapes de déploiement pour l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud et informations détaillées sur les paramètres d’artefact de blueprint.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: 67dabde6afe2dbc17a5ca2bd32534108b8008a8a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038438"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Déployer l’exemple de blueprint de zone d’accueil de migration du framework d’adoption de Microsoft Cloud pour Azure

Pour déployer l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud pour Azure Blueprints, vous devez effectuer les étapes suivantes :

> [!div class="checklist1"]
> - Déployer l’exemple de blueprint [Base du framework d’adoption du cloud](../caf-foundation/index.md) (recommandé)

> [!div class="checklist2"]
> - Créer un blueprint à partir de l’exemple
> - Marquer la copie de l’exemple en tant que **Publié**
> - Affecter votre copie du blueprint à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="create-blueprint-from-sample"></a>Créer un blueprint à partir de l’exemple

Commencez par implémenter l’exemple de blueprint. Pour cela, créez un blueprint dans votre environnement en vous servant de l’exemple comme point de départ.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Dans la page **Démarrage**à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Recherchez l’exemple de blueprint **Zone d’accueil de migration du framework d’adoption du cloud** sous _Autres exemples_ et sélectionnez **Utiliser cet exemple**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :
   - **Nom du blueprint** : spécifiez un nom pour votre copie de l’exemple de zone d’accueil de migration du framework d’adoption du cloud.
   - **Emplacement de définition**  : utilisez les points de suspension et sélectionnez le groupe d’administration dans lequel vous souhaitez enregistrer votre copie de l’exemple.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Passez en revue la liste des artefacts qui composent l’exemple de blueprint. De nombreux artefacts ont des paramètres que nous définirons plus tard. Sélectionnez **Enregistrer comme brouillon** lorsque vous avez terminé de passer en revue l’exemple de blueprint.

## <a name="publish-the-sample-copy"></a>Publier la copie de l’exemple

Votre copie de l’exemple de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée. Vous pouvez personnaliser la copie de l’exemple de blueprint en fonction de votre environnement et de vos besoins, mais ces modifications peuvent l’éloigner des recommandations en matière de zone d’accueil de migration du framework d’adoption du cloud.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans la nouvelle page à droite, indiquez la **Version** de votre copie de l’exemple de blueprint. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Dans **Notes de changement**, indiquez par exemple « Première version publiée à partir de l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud ». Sélectionnez ensuite **Publier** en bas de la page.

## <a name="assign-the-sample-copy"></a>Affecter la copie de l’exemple

Une fois que la copie de l’exemple de blueprint a été **publiée**, elle peut être affectée à un abonnement dans le groupe d’administration où elle a été enregistrée. C’est à cette étape que les paramètres sont fournis pour que chaque déploiement de la copie de l’exemple de blueprint soit unique.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Affecter le blueprint** dans le haut de la page.

1. Indiquez les valeurs des paramètres pour l’affectation du blueprint :

   - Concepts de base
     - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui font partie du groupe d’administration où vous avez enregistré votre copie de l’exemple de blueprint. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chacun d’eux à l’aide des paramètres saisis.
     - **Nom de l’affectation** : Le nom est prérempli en fonction du nom du blueprint.
       Changez-le si nécessaire ou laissez-le tel quel.
     - **Emplacement** : Sélectionnez une région dans laquelle créer l’identité managée.
     - Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté.
       Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version de définition du blueprint** : Choisissez une version **publiée** de votre copie de l’exemple de blueprint.
    
   - Verrouiller l'affectation

     Sélectionnez le paramètre de verrouillage de blueprint pour votre environnement. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../../concepts/resource-locking.md).

   - Identité managée

     Choisissez l’option d’identité managée _affectée par le système_ par défaut ou l’option d’identité _affectée par l’utilisateur_.

   - Paramètres de blueprint

     Les paramètres définis dans cette section sont utilisés par de nombreux artefacts dans la définition du blueprint à des fins de cohérence.

       - **Organisation** : entrez le nom de votre organisation, par exemple Contoso ou Fabrikam. Ce nom doit être unique.
       - **AzureRegion** : sélectionnez une région Azure pour le déploiement.
       
   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont des [paramètres dynamiques](../../concepts/parameters.md#dynamic-parameters) puisqu’ils sont définis lors de l’affectation du blueprint. Pour obtenir la liste complète des paramètres d’artefact et leur description, consultez le [tableau des paramètres d’artefact](#artifact-parameters-table).

1. Une fois tous les paramètres entrés, sélectionnez **Affecter** au bas de la page. L’affectation du blueprint est créée et le déploiement de l’artefact démarre. Le déploiement prend environ cinq minutes. Pour vérifier l’état du déploiement, ouvrez l’affectation du blueprint.

> [!WARNING]
> Le service Azure Blueprints et les exemples de blueprint intégrés sont **gratuits**. Les ressources Azure sont [facturées par produit](https://azure.microsoft.com/pricing/). Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût d’exécution des ressources déployées par cet exemple de blueprint.

## <a name="artifact-parameters-table"></a>Tableau des paramètres d’artefact

Le tableau suivant fournit la liste des paramètres d’artefact de blueprint :

|Nom de l’artefact|Type d’artefact|Nom du paramètre|Description|
|-|-|-|-|
|Déployer la zone d’accueil vNET|Modèle Resource Manager|IPAddress_Space|**Verrouillé** : spécifiez les deux premiers octets, par exemple 10.0|
|Déployer Key Vault|Modèle Resource Manager|KV-AccessPolicy|**Verrouillé** : ID d’objet utilisateur ou de groupe auquel accorder des autorisations dans Key Vault|
|Déployer Log Analytics|Modèle Resource Manager|LogAnalytics_DataRetention|**Verrouillé** : nombre de jours pendant lesquels les données sont conservées dans Log Analytics|
|Déployer Log Analytics|Modèle Resource Manager|LogAnalytics_Location|**Verrouillé** : région utilisée lors de l’établissement de l’espace de travail|
|Déployer Azure Migrate|Modèle Resource Manager|Azure_Migrate_Location|**Verrouillé** : sélectionnez la région où déployer Azure Migrate|

## <a name="next-steps"></a>Étapes suivantes

Vous venez de passer en revue les étapes à suivre pour déployer l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud. Consultez à présent les articles suivants pour en savoir plus sur l’architecture :

> [!div class="nextstepaction"]
> [Blueprint de zone d’accueil de migration du framework d’adoption du cloud – Vue d’ensemble](./index.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).