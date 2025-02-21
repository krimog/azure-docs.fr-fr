---
title: Exemple de blueprint ISO 27001 - Vue d’ensemble
description: Vue d’ensemble de l’exemple de blueprint ISO 27001. Cet exemple de blueprint aide les clients à évaluer des contrôles ISO 27001 spécifiques.
ms.date: 07/22/2019
ms.topic: sample
ms.openlocfilehash: 2e6e3d8d1abec1262f60ca25a549f35658ed54d3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037324"
---
# <a name="overview-of-the-iso-27001-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint ISO 27001

L’exemple de blueprint ISO 27001 fournit des garde-fous de gouvernance avec [Azure Policy](../../../policy/overview.md), qui vous permettent d’évaluer des contrôles ISO 27001 spécifiques. Avec ce blueprint, les clients peuvent déployer un ensemble de stratégies pour toute architecture déployée par Azure devant implémenter des contrôles ISO 27001. Deux exemples de blueprint ISO 27001 supplémentaires sont disponibles pour vous aider à déployer une [architecture fondamentale](../iso27001-shared/index.md)et une [charge de travail ASE/SQL](../iso27001-ase-sql-workload/index.md).

## <a name="control-mapping"></a>Correspondance des contrôles

La section sur la correspondance des contrôles fournit des détails sur les stratégies incluses dans ce blueprint et décrit comment ces stratégies permettent de répondre aux différents contrôles dans ISO 27001. Quand des ressources sont attribuées à une architecture, elles sont évaluées par Azure Policy pour savoir si elles sont en conformité avec les stratégies affectées. Pour plus d’informations, consultez [Présentation d’Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez parcouru la vue d’ensemble et l’architecture de l’exemple de blueprint ISO 27001.
Continuez avec les articles suivants pour découvrir les correspondances de contrôles et la manière de déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprint ISO 27001 - Correspondance des contrôles](./control-mapping.md)
> [Blueprint ISO 27001 - Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).
