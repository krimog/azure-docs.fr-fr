---
title: Vue d’ensemble d’Azure Blockchain Service
description: Vue d’ensemble d’Azure Blockchain Service
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9fcf75a07d1caf7b411c315d11ce984cbe35df75
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577182"
---
# <a name="what-is-azure-blockchain-service"></a>À propos d’Azure Blockchain Service

Azure Blockchain Service est un service de registre complètement managé qui permet aux utilisateurs de développer et d’exploiter des réseaux blockchain à l’échelle dans Azure. En fournissant un contrôle unifié pour la gestion de l’infrastructure ainsi que la gouvernance du réseau blockchain, Azure Blockchain Service offre :

* Des opérations et un déploiement de réseau simples
* Gestion de consortium intégrée
* Développer des contrats intelligents avec des outils de développement familiers

Azure Blockchain Service est conçu pour prendre en charge plusieurs protocoles de registre. Actuellement, il prend en charge le registre [Quorum](https://www.jpmorgan.com/Quorum) Ethereum à l’aide du mécanisme de consensus [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Ces fonctionnalités ne demandent pratiquement aucune administration, et toutes sont fournies sans coût supplémentaire. Vous pouvez vous concentrer sur la logique métier et le développement d’applications plutôt que sur le temps et les ressources nécessaires à la gestion des machines virtuelles et de l’infrastructure. Vous pouvez également continuer à développer votre application avec les outils open source et la plateforme de votre choix, ce qui vous permet de fournir vos solutions sans avoir besoin d’acquérir de nouvelles compétences.

## <a name="network-deployment-and-operations"></a>Opérations et déploiement de réseau

Vous pouvez déployer Azure Blockchain Service via le portail Azure, Azure CLI ainsi que du code Visual Studio grâce à l’extension Azure Blockchain.  Le déploiement est simplifié et comprend l’approvisionnement des nœuds validateur et de transaction, des réseaux virtuels Azure pour l’isolation de sécurité ainsi qu’un stockage managé par le service.  De plus, lorsque du déploiement d’un nouveau membre pour la blockchain, les utilisateurs peuvent également créer, ou rejoindre, un consortium.  Grâce aux consortiums, plusieurs parties présentes dans des abonnements Azure différents peuvent communiquer entre elles de manière sécurisée sur une blockchain partagée.  Ce déploiement simplifié accélère le déploiement de réseau blockchain, ne prenant plus que quelques minutes au lieu de plusieurs jours.

### <a name="performance-and-service-tiers"></a>Niveaux de performance et de service

Azure Blockchain Service propose deux niveaux de service : *De base* et *Standard*. Chaque niveau offre des performances et des fonctionnalités différentes afin de prendre en charge des charges de travail de développement et test légères ainsi que des déploiements de blockchain de production à très grande échelle. Les deux niveaux comprennent au moins un nœud de transaction, plus un nœud validateur (De base) ou deux nœuds validateur (Standard).

![Niveaux de tarification](./media/overview/pricing-tiers.png)

En plus d’offrir deux nœuds validateur, le niveau *Standard* fournit deux mémoires *vCore* pour chaque nœud validateur et de transaction, tandis que le niveau De base offre une configuration contenant un seul vCore.  En proposant deux mémoires vCore pour les nœuds validateur et de transaction, vous pouvez dédier un vCore au registre Quorum. Le vCore restant peut alors être utilisé pour d’autres services liés à l’infrastructure, offrant des performances optimales pour les charges de travail de blockchain de production. Pour plus d’informations sur la tarification, consultez [Tarification Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Sécurité et maintenance

Après avoir approvisionné votre premier membre de blockchain, vous avez la possibilité d’ajouter des nœuds de transactions supplémentaires à votre membre.  Par défaut, les nœuds de transaction sont sécurisés grâce aux règles de pare-feu et l’accès devra donc être configuré.  En outre, tous les nœuds de transaction chiffrent les données en mouvement via TLS.  Vous disposez de plusieurs options pour sécuriser l’accès au nœud de transaction, notamment les règles de pare-feu, l’authentification de base, les clés d’accès ainsi que l’intégration d’Azure Active Directory. Pour plus d’informations, consultez [Configurer les nœuds de la transaction](configure-transaction-nodes.md) et [Configurer l’accès Azure Active Directory](configure-aad.md).

En tant que service managé, Azure Blockchain Service s’assure que les nœuds du membre de votre blockchain sont corrigés et disposent du système d’exploitation hôte et des mises à jour logicielles de pile de registre les plus récents. Il garantit également que ces nœuds sont configurés pour offrir une haute disponibilité (niveau Standard uniquement), ce qui permet d’éliminer la majorité des DevOps requis pour les nœuds de blockchain IaaS traditionnels.  Pour plus d’informations sur les correctifs et les mises à jour, consultez [Versions du registre Azure Blockchain Service prises en charge](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Surveillance et journalisation

En outre, Azure Blockchain Service propose des mesures riches via le service Azure Monitor, fournissant des insights sur l’utilisation du processeur, de la mémoire et du stockage des nœuds. Il offre aussi des insights utiles sur l’activité du réseau blockchain tels que les transactions et blocs extraits, la profondeur de la file d’attente de transaction ou les connexions actives.  Les mesures peuvent être personnalisées de manière à afficher les insights essentiels à votre application blockchain.  Vous pouvez également définir des seuils par le biais des alertes, ce qui permet aux utilisateurs de déclencher différentes actions, notamment envoyer des e-mails et des SMS, exécuter une application logique ou une fonction Azure ou encore envoyer vers un webhook personnalisé.

![Mesures](./media/overview/metrics.png)

Grâce à Azure Log Analytics, les utilisateurs peuvent afficher les journaux relatifs au registre Quorum ou à d’autres informations importantes comme les tentatives de connexion aux nœuds de transaction.

## <a name="built-in-consortium-management"></a>Gestion de consortium intégrée

Lorsque vous déployez votre premier membre de blockchain, vous rejoignez un consortium ou vous en créez un nouveau.  Un consortium est un groupe logique permettant de gérer la gouvernance et la connectivité entre les membres de la blockchain qui effectuent des transactions dans un processus comprenant plusieurs parties.  Azure Blockchain Service fournit des contrôles de gouvernance intégrés à l’aide de contrats intelligents prédéfinis déterminant les actions que les membres du consortium peuvent effectuer.  L’administrateur du consortium peut personnaliser à sa guise les contrôles de gouvernance. Lorsque vous créez un consortium, le membre de votre blockchain devient son administrateur par défaut. Il obtient alors le droit d’inviter d’autres parties à rejoindre votre consortium.  Vous ne pouvez rejoindre un consortium que si vous y avez été invité au préalable.  Lorsque vous rejoignez un consortium, les contrôles de gouvernance ajoutés par l’administrateur s’appliquent au membre de votre blockchain.

![Gestion de consortium](./media/overview/consortium.png)

Vous pouvez accéder aux actions de gestion du consortium, par exemple l’ajout et la suppression des membres d’un consortium, via PowerShell et une API REST. Vous pouvez gérer un consortium par programmation en utilisant des interfaces au lieu de modifier et d’envoyer des contrats intelligents basés sur Solidity. Pour plus d’informations, consultez [Gestion du consortium](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Développer à l’aide d’outils de développement familiers

En fonction du registre Quorum Ethereum open source utilisé, vous pouvez développer des applications pour Azure Blockchain Service comme des applications Ethereum existantes. Développée en collaboration avec les principaux partenaires du secteur, l’extension Visual Studio Code d’Azure Blockchain Development Kit permet aux développeurs de tirer parti d’outils familiers comme la suite Truffle pour générer des contrats intelligents. À l’aide de l’extension d’Azure Blockchain Development Kit, les développeurs peuvent se connecter à un consortium existant ou en créer un nouveau afin de générer et déployer des contrats intelligents à partir d’un seul environnement de développement intégré. À l’aide de l’extension Visual Studio Code d’Azure Blockchain, vous pouvez vous connecter à un consortium existant ou en créer un nouveau afin de générer et déployer des contrats intelligents à partir d’un seul environnement de développement intégré. Pour plus d’informations, consultez la page [Azure Blockchain Development Kit dans la place de marché Visual Studio Code](https://aka.ms/vscodebcextension) et le [guide d’utilisation d’Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Support et commentaires

Vous avez besoin d’aide ou vous avez des commentaires ?

* Rendez-vous sur le [blog d’Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), la page [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain) et le [forum d’Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, essayez un démarrage rapide ou consultez ces ressources pour en savoir plus.
* [Créer un membre de blockchain à l’aide du portail Azure](create-member.md) ou [Créer un membre de blockchain à l’aide d’Azure CLI](create-member-cli.md)
* Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/blockchain-service) pour des comparaisons de coûts et des calculatrices.
* Générer votre première application à l’aide d’[Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Guide d’utilisation](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)de l’extension VSCode d’Azure Blockchain
