---
title: 'Didacticiel : Créer une stratégie de pare-feu d’applications web pour Azure Front Door à l’aide du portail Azure'
description: Dans ce tutoriel, vous allez apprendre à créer une stratégie de pare-feu d’applications web (WAF) à l’aide du portail Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/07/2019
ms.author: victorh
ms.openlocfilehash: 602a8620ef35ea247c5865d97495ea43a7d7d93c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508903"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Didacticiel : Créer une stratégie de pare-feu d’applications web dans Azure Front Door à l’aide du portail Azure

Ce tutoriel explique comment créer une stratégie de pare-feu d’applications web (WAF) Azure de base et comment l’appliquer sur un hôte front-end dans Azure Front Door.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une stratégie de pare-feu d’applications web (WAF)
> * Associer la stratégie à un hôte front-end
> * Configurer des règles WAF

## <a name="prerequisites"></a>Prérequis

Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : créer un profil Front Door](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Créer une stratégie de pare-feu d’applications web

Commencez par créer une stratégie WAF de base avec un ensemble de règles managées par défaut à partir du portail. 

1. Dans l’angle supérieur gauche de l’écran, sélectionnez **Créer une ressource** > recherchez **WAF** > sélectionnez **Pare-feu d’applications web (préversion)** > sélectionnez **Créer**.
2. Sous l’onglet **De base** dans la page **Créer une stratégie WAF**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Vérifier + créer** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Subscription            |Sélectionnez le nom de votre abonnement Front Door.|
    | Resource group          |Sélectionnez le nom de votre groupe de ressources Front Door.|
    | Nom de la stratégie             |Entrez un nom unique pour votre stratégie WAF.|

   ![Créer une stratégie de pare-feu d’applications web (WAF)](../media/waf-front-door-create-portal/basic.png)

3. Sous l’onglet **Association** de la page **Créer une stratégie WAF**, sélectionnez **Ajouter un hôte frontend**, entrez les paramètres suivants, puis sélectionnez **Ajouter** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Front Door              | Sélectionnez le nom de votre profil Front Door.|
    | Hôte frontend           | Sélectionnez le nom de votre hôte Front Door, puis sélectionnez **Ajouter**.|
    
    > [!NOTE]
    > Si l’hôte front-end est associé à une stratégie WAF, il apparaît en grisé. Vous devez d’abord supprimer l’hôte front-end de la stratégie associée, puis réassocier l’hôte front-end à une nouvelle stratégie WAF.
1. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurer des règles de pare-feu d’applications web (facultatif)

### <a name="change-mode"></a>Changer de mode

Quand vous créez une stratégie WAF, celle-ci est par défaut en mode **Détection**. En mode **Détection**, la stratégie WAF ne bloque aucune requête, mais elle enregistre les requêtes correspondant aux règles WAF dans les journaux WAF.
Pour voir le fonctionnement de WAF, passez du mode **Détection** au mode **Prévention**. En mode **Prévention**, les requêtes qui correspondent à des règles définies dans un ensemble de règles par défaut sont bloquées et enregistrées dans les journaux WAF.

 ![Changer le mode de stratégie WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Règles personnalisées

Vous pouvez créer une règle personnalisée en sélectionnant **Ajouter une règle personnalisée** sous la section **Règles personnalisées**. Cette action ouvre la page de configuration d’une règle personnalisée. Voici un exemple de configuration d’une règle personnalisée qui bloque une requête si la chaîne de requête contient **blockme**.

![Changer le mode de stratégie WAF](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Ensemble de règles par défaut

L’ensemble de règles par défaut managées par Azure est activé par défaut. Pour désactiver une règle spécifique dans un groupe de règles, développez les règles de ce groupe de règles, sélectionnez la **case à cocher** devant le numéro de la règle, puis sélectionnez **Désactiver** sous l’onglet ci-dessus. Pour changer le type d’action associée à une règle dans l’ensemble de règles, sélectionnez la case à cocher devant le numéro de la règle, puis sélectionnez l’onglet **Changer l’action** ci-dessus.

 ![Changer l’ensemble de règles WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le pare-feu d’applications web Azure](../overview.md)
> [En savoir plus sur Azure Front Door](../../frontdoor/front-door-overview.md)