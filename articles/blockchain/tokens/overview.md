---
title: Qu’est-ce qu’Azure Blockchain Tokens
description: Azure Blockchain Tokens est une plateforme en tant que service (PaaS) pour l’émission et la gestion des jetons.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: cd41d52e06a5c1833dca9669881cbe48f362d81d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579750"
---
# <a name="what-is-azure-blockchain-tokens"></a>Qu’est-ce qu’Azure Blockchain Tokens ?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens sont une plateforme en tant que service (PaaS) pour la gestion et l’émission de jetons normalisés sur des registres de blockchain dans Azure.

Avec Azure Blockchain Tokens, vous pouvez créer des jetons standardisés pour votre solution blockchain à l’aide d’un modèle de jeton prédéfini. Vous pouvez également composer votre propre modèle de jeton à l’aide du service. Après création, utilisez Azure Blockchain Tokens pour vous connecter et émettre des jetons sur un blockchain. Une fois la publication effectuée, vous pouvez gérer les jetons sur plusieurs réseaux blockchain.

## <a name="templates"></a>Modèles

Utilisez Azure Blockchain Tokens pour sélectionner un modèle de jeton prédéfini ou créer votre propre modèle de jeton. Azure Blockchain Tokens prend en charge la composabilité des modèles de jeton, ce qui vous permet de créer votre propre modèle de jeton en fonction des comportements pris en charge. Les modèles de jeton peuvent être utilisés pour la plupart des solutions blockchain, car ils sont mappés aux jetons les plus couramment utilisés. Vous pouvez commencer avec un modèle, le personnaliser, puis déployer les jetons pour votre solution.

Pour plus d’informations sur les modèles Azure Blockchain Tokens, consultez [Modèles Azure Blockchain Tokens](templates.md).

## <a name="management"></a>gestion

Azure Blockchain Tokens fournit la gestion du portail Azure et des API pour vous connecter à un réseau blockchain existant. Actuellement, vous pouvez vous connecter à [Azure Blockchain Service](../service/overview.md) ou à un autre blockchain de la famille Ethereum.

Une fois connecté à un ou plusieurs réseaux blockchain, vous pouvez utiliser les API Azure Blockchain Tokens pour émettre et gérer les jetons à utiliser dans votre solution blockchain. À l’aide des API, vous pouvez intégrer la gestion des jetons à vos applications et à votre logique métier. Par exemple, vous pouvez utiliser l’API REST pour gérer les jetons au lieu de gérer les jetons directement sur le blockchain.

## <a name="blockchains-and-accounts"></a>Blockchains et comptes

Azure Blockchain Tokens fournit la gestion du portail Azure et des API permettant de créer de nouveaux groupes et de nouveaux comptes blockchain sur des réseaux blockchain connectés. Vous pouvez créer des comptes directement sur vos réseaux connectés, et Azure Blockchain Tokens gère les clés privées de votre compte à votre place. À l’aide des groupes, vous pouvez regrouper différents comptes blockchain à partir de plusieurs réseaux et gérer le contrôle d’accès via les groupes.

Pour plus d’informations sur la gestion des comptes Azure Blockchain Tokens, consultez [Gestion des comptes Azure Blockchain Tokens](account-management.md).

## <a name="token-taxonomy-framework"></a>Infrastructure de taxonomie des jetons

Azure Blockchain Tokens est basé sur un ensemble de normes appelé Infrastructure de taxonomie des jetons (TTF). TTF est un ensemble de livrables créés par le groupe de travail [Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI). Le groupe de travail TTI définit une taxonomie métier pour les jetons et leurs comportements qui peut être appliquée à tous les grands registres, y compris Ethereum, Quorum, Corda et Hyperledger Fabric. L’objectif du groupe de travail est de créer une infrastructure qui normalise l’utilisation des jetons du point de vue de l’entreprise pour simplifier et démocratiser le développement basé sur les jetons. En permettant au secteur de définir ces jetons et leur comportement au niveau de l’entreprise, l’implémentation détaillée des jetons est abstraite de la logique métier qui manipule les jetons.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [modèles Azure Blockchain Tokens](templates.md) disponibles.
