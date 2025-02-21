---
title: Tutoriel - Réduire les coûts Azure avec des recommandations d’optimisation | Microsoft Docs
description: Ce tutoriel vous permet de réduire les coûts Azure quand vous agissez sur les recommandations d’optimisation.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 9306e44655bd172343f20ac4fda2b2c56afcfb88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164493"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Didacticiel : Optimiser les coûts à partir de recommandations

Azure Cost Management fonctionne avec Azure Advisor pour fournir des recommandations d’optimisation des coûts. Azure Advisor vous permet d’optimiser et d’améliorer l’efficacité en identifiant les ressources inactives et sous-utilisées. Ce tutoriel vous présente un exemple dans lequel vous identifiez des ressources Azure sous-utilisées, puis vous prenez des mesures pour réduire les coûts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Consulter les recommandations d’optimisation des coûts pour voir les inefficacités d’utilisation potentielles
> * Agir sur une recommandation pour redimensionner une machine virtuelle et bénéficier d’une option plus rentable
> * Vérifier l’action pour vous assurer que la machine virtuelle a été correctement redimensionnée

## <a name="prerequisites"></a>Prérequis
Des recommandations sont disponibles pour diverses étendues et divers types de comptes Azure, notamment pour les clients [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](understand-cost-mgt-data.md). Vous devez au moins disposer d’un accès en lecture à une ou plusieurs des étendues suivantes pour afficher les données de coût. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Abonnement
- Groupe de ressources

Vous devez disposer de machines virtuelles actives avec au moins 14 jours d’activité.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Afficher des recommandations d’optimisation des coûts

Pour voir les recommandations d’optimisation de coût pour un abonnement, ouvrez l’étendue souhaitée dans le portail Azure et sélectionnez **Recommandations du conseiller**.

Pour examiner les recommandations pour un groupe d'administration, ouvrez l’étendue souhaitée sur le portail Azure et sélectionnez **Analyse du coût** dans le menu. Utilisez la pastille **Étendue** pour basculer vers une autre étendue, telle qu’un groupe d’administration. Sélectionnez **Recommandations du conseiller** dans le menu. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

![Recommandations d’Advisor sur la gestion des coûts présentées dans le portail Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

La liste des recommandations identifie les inefficacités d’utilisation ou affiche des recommandations d’achat qui peuvent vous aider à faire des économies supplémentaires. La valeur **Économies annuelles potentielles** totale affiche le montant total que vous pouvez économiser si vous arrêtez ou libérez toutes vos machines virtuelles qui répondent aux règles de recommandation. Si vous ne voulez pas les arrêter, envisagez de les déplacer vers une référence (SKU) de machine virtuelle moins coûteuse.

La catégorie **Impact** ainsi que la valeur **Économies annuelles potentielles** sont destinées à simplifier l’identification des recommandations qui ont la possibilité d’économiser autant que possible.

Les recommandations d’impact élevé incluent :
- [Acheter des instances de machine virtuelle réservées pour économiser sur les coûts du paiement à l’utilisation](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimiser le coût de la machine virtuelle en redimensionnant ou en arrêtant les instances sous-utilisées](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Utiliser Stockage Standard pour stocker des instantanés de disques managés](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Les recommandations d’impact moyen incluent :
- [Supprimer les pipelines Azure Data Factory qui échouent](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Réduire les coûts en éliminant les circuits ExpressRoute non provisionnés](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Réduire les coûts en supprimant ou en reconfigurant les passerelles de réseau virtuel inactives](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Agir sur une recommandation

Azure Advisor supervise l’utilisation de vos machines virtuelles pendant 14 jours, puis identifie les machines virtuelles sous-utilisées. Les machines virtuelles dont l’utilisation du processeur est inférieure ou égale à 5 % et celle du réseau est inférieure ou égale à 7 Mo pendant quatre jours ou plus sont considérées comme des machines virtuelles faiblement utilisées.

Le paramètre d’utilisation du processeur inférieure ou égale à 5 % est la valeur par défaut, mais vous pouvez ajuster les paramètres. Pour plus d’informations sur l’ajustement du paramètre, consultez l’article [Configurer la règle d’utilisation moyenne du processeur ou la recommandation en cas de faible utilisation de machines virtuelles](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Alors que certains scénarios peuvent, par définition, entraîner une faible utilisation, vous pouvez souvent faire des économies en remplaçant la taille de vos machines virtuelle par des tailles moins coûteuses. Vos économies réelles peuvent varier si vous choisissez une action de redimensionnement. Examinons un exemple de redimensionnement d’une machine virtuelle.

Dans la liste des recommandations, cliquez sur la recommandation **Ajuster la taille des machines virtuelles sous-exploitées ou les arrêter**. Dans la liste des machines virtuelles candidates, choisissez une machine virtuelle à redimensionner, puis cliquez dessus. Les informations détaillées de la machine virtuelle s’affichent pour vous permettre de vérifier les métriques d’utilisation. La valeur **Économies annuelles potentielles** représente ce que vous pouvez économiser si vous arrêtez ou supprimez la machine virtuelle. Le redimensionnement d’une machine virtuelle vous fera probablement économiser de l’argent, mais vous n’économiserez pas la totalité des économies annuelles potentielles.

![Exemple de détails de la recommandation](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Dans les informations détaillées de la machine virtuelle, consultez l’utilisation de la machine virtuelle pour vérifier qu’il s’agit d’un candidat approprié pour le redimensionnement.

![Exemple de détails d’une machine virtuelle montrant l’historique d’utilisation](./media/tutorial-acm-opt-recommendations/vm-details.png)

Notez la taille actuelle de la machine virtuelle. Après avoir vérifié que la machine virtuelle doit être redimensionnée, fermez les informations détaillées de la machine virtuelle afin d’afficher la liste des machines virtuelles.

Dans la liste des candidats à arrêter ou à redimensionner, sélectionnez **Redimensionner la machine virtuelle**.
![Exemple de recommandation avec l’option permettant de redimensionner la machine virtuelle](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Ensuite, une liste d’options de redimensionnement disponibles vous est présentée. Choisissez celle qui offre les meilleures performances et la meilleure rentabilité pour votre scénario. Dans l’exemple suivante, l’option choisie effectue le redimensionnement d’un **DS14\_V2** en **DS13\_V2**. Suivre la recommandation fait économiser 551,30 $/mois ou 6 615,60 $/an.

![Liste d’exemple de tailles de machine virtuelle disponibles parmi lesquelles vous pouvez en choisir une](./media/tutorial-acm-opt-recommendations/choose-size.png)

Après avoir choisi une taille appropriée, cliquez sur **Sélectionner** pour démarrer l’action de redimensionnement.

Le redimensionnement nécessite le redémarrage d’une machine virtuelle en cours d’exécution. Si la machine virtuelle se trouve dans un environnement de production, nous vous recommandons d’exécuter l’opération de redimensionnement après les heures de bureau. La planification du redémarrage peut réduire les perturbations provoquées par l’indisponibilité momentanée.

## <a name="verify-the-action"></a>Vérifier l’action

Quand le redimensionnement de la machine virtuelle se termine correctement, une notification Azure s’affiche.

![Notification de redimensionnement de machine virtuelle effectuée avec succès](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Consulter les recommandations d’optimisation des coûts pour voir les inefficacités d’utilisation potentielles
> * Agir sur une recommandation pour redimensionner une machine virtuelle et bénéficier d’une option plus rentable
> * Vérifier l’action pour vous assurer que la machine virtuelle a été correctement redimensionnée

Si vous n’avez pas déjà lu l’article relatif aux bonnes pratiques de Cost Management, il fournit des principes et des conseils généraux à prendre en compte pour simplifier la gestion des coûts.

> [!div class="nextstepaction"]
> [Bonnes pratiques de Cost Management](cost-mgt-best-practices.md)
