---
title: Vue d’ensemble d’Azure Resource Graph
description: Découvrez comment le service Azure Resource Graph permet d’exécuter des requêtes complexes sur des ressources à grande échelle.
ms.date: 10/21/2019
ms.topic: overview
ms.openlocfilehash: f75a2b65b59a9f0367a6c52c2a1cf058df5b59c3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958743"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Vue d’ensemble du service Azure Resource Graph

Azure Resource Graph est un service Azure conçu pour étendre Gestion des ressources Azure. Il permet non seulement d’explorer de manière efficace et performante les ressources, mais aussi de lancer des requêtes à grande échelle sur un ensemble donné d’abonnements pour optimiser la gestion de votre environnement. Ces requêtes vous permettent d’exécuter les fonctionnalités suivantes :

- Interroger les ressources avec des fonctions complexes de filtrage, de regroupement et de tri en fonction des propriétés des ressources.
- Possibilité d’effectuer une exploration itérative selon les exigences de gouvernance des ressources.
- Évaluer l’impact de l’application de stratégies dans un environnement cloud étendu.
- Capacité à [décrire en détail les modifications apportées aux propriétés des ressources](./how-to/get-resource-changes.md) (préversion).

À travers cette documentation, nous allons examiner en détail chacune de ces fonctionnalités.

> [!NOTE]
> Azure Resource Graph gère la barre de recherche du portail Azure, la nouvelle expérience de navigation « Toutes les ressources » d’Azure ainsi que l’option [Historique des modifications de la stratégie](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _Différentiel visuel_ d’Azure Policy. Il est conçu pour aider les clients à gérer des environnements à grande échelle.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Comment Resource Graph vient compléter Azure Resource Manager

Azure Resource Manager prend actuellement en charge les requêtes effectuées sur des champs de ressource de base, à savoir le nom de la ressource, l’ID, le type, le groupe de ressources, l’abonnement et l’emplacement. Resource Manager fournit également des fonctionnalités permettant d’appeler des fournisseurs de ressources individuels afin d’obtenir des propriétés détaillées sur une ressource à la fois.

Avec Azure Resource Graph, vous pouvez accéder aux propriétés retournées par les fournisseurs de ressources sans appeler chaque fournisseur. Pour connaître les types de ressources pris en charge, recherchez la valeur **Oui** dans le tableau de la rubrique [Suppression de ressources Azure pour les déploiements en mode complet](../../azure-resource-manager/complete-mode-deletion.md). Des types de ressources supplémentaires sont disponibles dans les [tables Resource Graph](./concepts/query-language.md#resource-graph-tables) associées. Un autre moyen de voir les types de ressource pris en charge consiste à utiliser le [Navigateur de schémas de l’Explorateur Azure Resource Graph](./first-query-portal.md#schema-browser).

Azure Resource Graph vous permet :

- d’accéder aux propriétés retournées par les fournisseurs de ressources sans appeler chaque fournisseur ;
- d’afficher les 14 derniers jours de l’historique des modifications apportées à la ressource pour voir quelles propriétés ont été modifiées et quand. (préversion)

## <a name="how-resource-graph-is-kept-current"></a>Méthode de mise à jour de Resource Graph

Lorsqu’une ressource Azure est mise à jour, Resource Manager avertit Resource Graph de cette modification.
Resource Graph met alors à jour sa base de données. Resource Graph effectue également une _analyse complète_ régulière. Cette analyse garantit que les données Resource Graph sont à jour en cas de notifications manquantes ou lorsqu'une ressource est mise à jour en dehors de Resource Manager.

> [!NOTE]
> Resource Graph utilise `GET` sur l’API autre que la préversion la plus récente de chaque fournisseur de ressources pour collecter des propriétés et des valeurs. Par conséquent, la propriété attendue peut ne pas être disponible. Dans certains cas, la version d’API utilisée a été remplacée pour fournir des propriétés plus courantes ou largement employées dans les résultats. Pour obtenir la liste complète dans votre environnement, consultez l’exemple [Afficher la version d’API pour chaque type de ressource](./samples/advanced.md#apiversion).

## <a name="the-query-language"></a>Langage de requête

Maintenant que vous avez une meilleure idée de ce qu’est Azure Resource Graph, voyons comment construire des requêtes.

Il est important de comprendre que le langage de requête d’Azure Resource Graph est basé sur le [langage de requête Kusto](../../data-explorer/data-explorer-overview.md) utilisé par Azure Data Explorer.

Tout d’abord, pour obtenir des informations sur les opérations et fonctions qui peuvent être utilisées avec Azure Resource Graph, consultez le [langage de requête Resource Graph](./concepts/query-language.md).
Pour parcourir des ressources, consultez [Explorer les ressources](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Autorisations dans Azure Resource Graph

Pour utiliser Resource Graph, vous devez disposer des droits appropriés pour le [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC), avec au moins un accès en lecture aux ressources à interroger. Sans au moins une `read` autorisation pour l’objet Azure ou un groupe d’objets, les résultats ne seront pas renvoyés.

> [!NOTE]
> Resource Graph utilise les abonnements disponibles pour un principal lors de la connexion. Pour afficher les ressources d’un nouvel abonnement ajouté pendant une session active, le principal doit actualiser le contexte. Cette action se produit automatiquement quand vous vous déconnectez puis vous reconnectez.

Azure CLI et Azure PowerShell utilisent des abonnements auxquels l’utilisateur a accès. Lors de l’utilisation directe de l’API REST, la liste des abonnements est fournie par l’utilisateur. Si l’utilisateur a accès à l’un des abonnements de la liste, les résultats de la requête sont retournés pour les abonnements auxquels l’utilisateur a accès. Ce comportement est le même que lors de l’appel de [Groupes de ressources - Liste](/rest/api/resources/resourcegroups/list) \- vous obtenez les groupes de ressources auxquels vous avez accès sans indication que le résultat peut être partiel.
S’il n’existe aucun abonnement dans la liste des abonnements pour lesquels l’utilisateur dispose des droits appropriés, la réponse est un _403_ (Interdit).

## <a name="throttling"></a>Limitation

Le service étant gratuit, les requêtes vers Resource Graph sont limitées afin d’offrir une excellente expérience et un temps de réponse optimal à tous les clients. Si votre organisation souhaite utiliser l'API Resource Graph pour les requêtes fréquentes et à grande échelle, utilisez l'option « Commentaires » du portail à partir de la [page du portail Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Fournissez votre étude de cas et cochez la case « Microsoft peut vous contacter par e-mail à propos de vos commentaires » afin que l'équipe puisse vous contacter.

Resource Graph limite les requêtes au niveau utilisateur. La réponse du service contient les en-têtes HTTP suivants :

- `x-ms-user-quota-remaining` (int) : quota de ressources restant pour l'utilisateur. Cette valeur correspond au nombre de requêtes.
- `x-ms-user-quota-resets-after` (hh:mm:ss) : délai à attendre avant la réinitialisation de la consommation du quota d'un utilisateur.

Pour plus d’informations, consultez les [Instructions pour les requêtes limitées](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Exécution de votre première requête

L’Explorateur Azure Resource Graph, qui fait partie du portail Azure, permet d’exécuter des requêtes Resource Graph directement dans le portail Azure. Épinglez les résultats sous forme de graphiques dynamiques pour fournir des informations dynamiques en temps réel à votre workflow du portail. Pour plus d’informations, consultez [Première requête avec l’Explorateur Azure Resource Graph](first-query-portal.md).

Resource Graph prend en charge Azure CLI, Azure PowerShell, le kit Azure SDK pour .NET, etc. La requête est structurée de la même manière pour chaque langage. Découvrez comment activer Resource Graph :

- [Portail Azure et Explorateur Resource Graph](first-query-portal.md) 
- [Interface de ligne de commande Azure](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Étapes suivantes

- Exécutez votre première requête en utilisant le [portail Azure](first-query-portal.md).
- Exécuter votre première requête avec [Azure CLI](first-query-azurecli.md).
- Exécuter votre première requête avec [Azure PowerShell](first-query-powershell.md).
- Obtenir plus d’informations sur le [langage de requête](./concepts/query-language.md).
- Commencer des [requêtes de démarrage](./samples/starter.md).
- Aller plus loin avec des [requêtes avancées](./samples/advanced.md).