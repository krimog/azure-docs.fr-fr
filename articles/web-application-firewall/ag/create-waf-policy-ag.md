---
title: Créer des stratégies de pare-feu d’applications web (WAF) pour Application Gateway
description: Découvrez comment créer des stratégies de pare-feu d’applications web pour Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 10/26/2019
ms.author: victorh
ms.openlocfilehash: 31a5ad92942b40e42b66e404df2d09cd8158f7a2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606482"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Créer des stratégies de pare-feu d’applications web pour Application Gateway

L’association d’une stratégie WAF avec des écouteurs permet de protéger plusieurs sites derrière un WAF à l’aide de différentes stratégies. Par exemple, s’il y a cinq sites derrière votre WAF, vous pouvez avoir cinq stratégies WAF distinctes (une par écouteur) de façon à pouvoir personnaliser les exclusions, les règles personnalisées et les ensembles de règles managés d’un site sans que cela affecte les quatre autres. Si vous souhaitez qu’une seule stratégie s’applique à tous les sites, vous pouvez simplement associer la stratégie à Application Gateway, plutôt qu’aux écouteurs individuels, afin de l’appliquer de manière globale. Des stratégies peuvent également être appliquées à une règle d’acheminement basée sur un chemin. 

   > [!NOTE]
   > Les stratégies WAF par site sont disponibles dans les régions USA Centre Sud et Europe Nord. Pour y accéder dans le portail, utilisez [ce lien](https://aka.ms/AppgwwafWithAllFeatureFlags) jusqu’à ce qu’elles soient disponibles pour tout le monde.  

Vous pouvez créer autant de stratégies que vous le souhaitez. Une fois que vous avez créé une stratégie, celle-ci doit être associée à une Application Gateway pour entrer en vigueur, mais elle peut être associée à toute combinaison d’Application Gateways et d’écouteurs. 

Si une stratégie est appliquée à votre Application Gateway, puis que vous appliquez une stratégie différente à un écouteur sur cette Application Gateway, la stratégie de l’écouteur prend effet, mais uniquement pour le ou les écouteurs auxquels elle est attribuée. La stratégie d’Application Gateway s’applique encore à tous les autres écouteurs auxquels aucune stratégie spécifique n’est attribuée. 

   > [!NOTE]
   > Les stratégies WAF par site et par URI sont en préversion publique. Cela signifie que cette fonctionnalité est soumise aux conditions d’utilisation supplémentaires de Microsoft. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tous les nouveaux paramètres WAF du pare-feu d’applications web (règles personnalisées, configurations d’ensemble de règles managé, exclusions, etc.) résident dans une stratégie WAF. Si vous disposez d’un WAF, ces paramètres peuvent toujours exister dans la configuration de votre WAF. Pour savoir comment passer à la nouvelle stratégie WAF, voir [Migrer votre configuration WAF vers une stratégie WAF](#migrate) plus loin dans cet article. 

## <a name="create-a-policy"></a>Création d’une stratégie

Commencez par créer une stratégie WAF de base avec un ensemble de règles par défaut managé via le portail Azure.

1. En haut à gauche du portail, sélectionnez **Créer une ressource**. Recherchez **WAF**, sélectionnez **Pare-feu d’applications web**, puis choisissez **Créer**.
2. Dns la page **Créer une stratégie WAF**, sous l’onglet **De base** entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Vérifier + créer** :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Stratégie pour     |WAF régional (Application Gateway)|
   |Subscription     |Sélectionnez le nom de votre abonnement|
   |Resource group     |Sélectionnez votre groupe de ressources|
   |Nom de la stratégie     |Tapez un nom unique pour votre stratégie WAF.|
3. Sous l’onglet **Association**, entrez l’un des paramètres suivants, puis sélectionnez **Ajouter** :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Associer Application Gateway     |Sélectionnez le nom de profil de votre Application Gateway.|
   |Associer des écouteurs     |Choisissez le nom de votre écouteur Application Gateway, puis sélectionnez **Ajouter**.|

   > [!NOTE]
   > Si vous attribuez une stratégie à votre Application Gateway (ou à votre écouteur) qui a déjà une stratégie en place, la stratégie d’origine est remplacée par la nouvelle.
4. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer**.

   ![Concepts de base de stratégie WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configurer des règles WAF (facultatif)

Quand vous créez une stratégie WAF, celle-ci est en mode de *Détection* par défaut. En mode de détection, WAF ne bloque aucune demande. Au lieu de cela, les règles de WAF correspondantes sont journalisées dans les journaux WAF. Pour voir le fonctionnement de WAF, passez en mode de *Prévention*. En mode de prévention, les règles de correspondance définies dans l’ensemble de règles CRS que vous avez sélectionné sont bloquées ou journalisées dans les journaux WAF.

## <a name="managed-rules"></a>Règles managées

Les règles OWASP managées par Azure sont activées par défaut. Pour désactiver une règle spécifique dans un groupe de règles, développez les règles de celui-ci, activez la case à cocher devant le numéro de la règle, puis sélectionnez **Désactiver** sous l’onglet situé au-dessus.

![Règles managées](../media/create-waf-policy-ag/managed-rules.png)

## <a name="custom-rules"></a>Règles personnalisées

Pour créer une règle personnalisée, sélectionnez **Ajouter une règle personnalisée** sous l’onglet **Règles personnalisées**. Cette action ouvre la page de configuration de règle personnalisée. La capture d’écran suivante montre un exemple de règle personnalisée configurée pour bloquer une demande si la chaîne de requête contient le texte *blockme*.

![Modifier une règle personnalisée](../media/create-waf-policy-ag/edit-custom-rule.png)

## <a name="migrate"></a>Migrer votre configuration WAF vers une stratégie WAF

Si vous disposez d’un WAF, vous avez peut-être remarqué des modifications dans le portail. Tout d’abord, vous devez identifier le type de stratégie que vous avez activée sur votre WAF. Il existe trois états possibles :

- Aucune stratégie WAF
- Stratégie de règles personnalisées uniquement
- Stratégie WAF

Vous pouvez déterminer l’état de votre WAF en examinant celui-ci dans le portail. Si les paramètres WAF sont visibles et peuvent être modifiés dans la vue Application Gateway, votre WAF est dans l’état 1.

![Configuration de WAF](../media/create-waf-policy-ag/waf-configure.png)

Si vous sélectionnez **Pare-feu d’application web** et qu’une stratégie associée apparaît, le WAF est dans l’état 2 ou 3. Après avoir accédé à la stratégie, si **seules** des règles personnalisées s’affichent avec des Application Gateways associées, il s’agit d’une Stratégie de règles personnalisées uniquement.

![Règles personnalisées WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Si des paramètres de stratégie et des règles managées s’affichent également, il s’agit d’une stratégie de pare-feu d’applications web complète. 

![Paramètres de stratégie WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrer vers une stratégie WAF

Si vous avez une Stratégie de règles personnalisées uniquement, vous pouvez éventuellement passer à la nouvelle stratégie WAF. À l’avenir, la stratégie de pare-feu prendra en charge les paramètres de stratégie WAF, les ensembles de règles managés, les exclusions et les groupes de règles désactivés. Fondamentalement, toutes les configurations WAF qui s’effectuaient précédemment dans l’Application Gateway s’effectuent désormais via la stratégie WAF. 

Les modifications apportées à la Stratégie de règles personnalisées uniquement sont désactivées. Pour modifier des paramètres de WAF tels que la désactivation de règles, l’ajout d’exclusions, etc., vous devez migrer vers une nouvelle ressource de stratégie de pare-feu de niveau supérieur.

Pour ce faire, créez une *stratégie de pare-feu d’applications web* et associez-la aux Application Gateways et écouteurs de votre choix. Cette nouvelle stratégie **doit** être identique à la configuration de WAF actuelle, ce qui signifie que chaque règle personnalisée, exclusion, règle désactivée, etc. doit être copiée dans la nouvelle stratégie que vous créez. Une fois que vous avez une stratégie associée à votre Application Gateway, vous pouvez continuer à apporter des modifications à vos règles et paramètres WAF. 

Vous pouvez également faire cela avec Azure PowerShell. Pour plus d’informations, voir [Associer une stratégie WAF à une Application Gateway existante](associate-waf-policy-existing-gateway.md).

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [Règles et groupes de règles CRS de pare-feu d’applications web](application-gateway-crs-rulegroups-rules.md).
