---
title: Utiliser Azure Virtual WAN pour établir des connexions ExpressRoute à des environnements Azure et locaux | Microsoft Docs
description: Dans ce tutoriel, découvrez comment utiliser Azure Virtual WAN pour établir des connexions ExpressRoute à des environnements Azure et locaux.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 8ad86280eab3041667bf9d1713ae2b4bc82a4c9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491605"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Didacticiel : Créer une association ExpressRoute avec Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Azure Virtual WAN pour vous connecter à vos ressources dans Azure via un circuit ExpressRoute. Pour plus d’informations sur Azure Virtual WAN et les ressources Azure Virtual WAN, consultez [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un WAN virtuel
> * Créer un hub et une passerelle
> * Connecter un réseau virtuel à un hub
> * Connecter un circuit à une passerelle de hub
> * Tester la connectivité
> * Modifier la taille d’une passerelle
> * Publier un itinéraire par défaut

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. Pour créer un réseau virtuel dans le Portail Azure, consultez le [Démarrage rapide](../virtual-network/quick-create-portal.md).

* Votre réseau virtuel n’a pas de passerelle de réseau virtuel. Si votre réseau virtuel dispose d’une passerelle (VPN ou ExpressRoute), vous devez supprimer toutes les passerelles. Cette configuration nécessite que les réseaux virtuels soient connectés à la passerelle hub Virtual WAN.

* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel qui est créé et utilisé par Virtual WAN. La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. Il ne peut pas chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Créer un WAN virtuel

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

1. Accédez à la page WAN Virtuel. Dans le portail, cliquez sur **+Créer une ressource**. Tapez **WAN virtuel** dans la zone de recherche et sélectionnez Entrée.
2. Sélectionnez **WAN virtuel** dans les résultats. Sur la page Virtual WAN, cliquez sur **Créer** pour ouvrir la page Créer un WAN.
3. Sur la page **Créer un WAN**, dans l’onglet **Fonctions de base**, renseignez les champs suivants :

   ![Créer un WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
   * **Groupe de ressources** : Créer en un nouveau ou utiliser un qui existe déjà.
   * **Emplacement du groupe de ressources** : choisissez un emplacement de la ressource dans la liste déroulante. Un réseau étendu est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région afin de gérer et de localiser plus facilement la ressource de réseau étendu que vous créez.
   * **Nom** : tapez le nom que vous souhaitez donner à votre réseau étendu.
   * **Type** : sélectionnez **Standard**. Vous ne pouvez pas créer une passerelle ExpressRoute à l’aide de la référence SKU de base.
4. Lorsque vous avez fini de renseigner les champs, cliquez sur **Vérifier + Créer**.
5. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.

## <a name="hub"></a>Créer un hub virtuel et une passerelle

Un hub virtuel est un réseau virtuel qui est créé et utilisé par Virtual WAN. Il peut contenir différentes passerelles, telles que VPN et ExpressRoute. Dans cette section, vous allez créer une passerelle ExpressRoute pour votre hub virtuel. Vous pouvez créer la passerelle lorsque vous [créez un hub virtuel](#newhub), ou vous pouvez créer la passerelle dans un [hub existant](#existinghub) en la modifiant. 

Les passerelles ExpressRoute sont approvisionnées en unités de 2 Gbits/s. 1 unité d’échelle = 2 Gbits/s avec prise en charge jusqu’à 10 unités d’échelle = 20 Gbits/s. La création complète d’un hub virtuel et d’une passerelle prend environ 30 minutes.

### <a name="newhub"></a>Pour créer un hub virtuel et une passerelle

Créez un hub virtuel. Une fois le hub créé, vous serez facturé, même si vous n’y joignez aucun site.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>Pour créer une passerelle dans un hub existant

Vous pouvez également créer une passerelle dans un hub existant en la modifiant.

1. Accédez au hub virtuel que vous souhaitez modifier et sélectionnez-le.
2. Sur la page **Modifier un hub virtuel**, cochez la case **Inclure la passerelle ExpressRoute**.
3. Cliquez sur **Confirmer** pour confirmer vos modifications. La création complète d’un hub virtuel et des ressources d’un hub prend environ 30 minutes.

   ![hub existant](./media/virtual-wan-expressroute-portal/edithub.png "modifier un hub")

### <a name="to-view-a-gateway"></a>Pour afficher une passerelle

Une fois que vous avez créé une passerelle ExpressRoute, vous pouvez afficher les détails de la passerelle. Accédez au Hub, sélectionnez **ExpressRoute**et affichez la passerelle.

![Afficher la passerelle](./media/virtual-wan-expressroute-portal/viewgw.png "afficher la passerelle")

## <a name="connectvnet"></a>Connecter votre réseau virtuel au hub

Dans cette section, vous créez la connexion de peering entre votre hub et un réseau virtuel. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Dans la page de votre réseau étendu, cliquez sur **Connexion réseau de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante (ni VPN, ni ExpressRoute).

## <a name="connectcircuit"></a>Connecter votre circuit à la passerelle de hub

Une fois la passerelle créée, vous pouvez y connecter un circuit [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Notez que les circuits ExpressRoute Premium qui se trouvent dans les emplacements pris en charge par ExpressRoute Global Reach peuvent se connecter à une passerelle ExpressRoute Virtual WAN.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Pour connecter votre circuit à la passerelle de hub

Dans le portail, accédez à la page **Hub virtuel -> Connectivité -> ExpressRoute**. Si vous avez accès à un circuit ExpressRoute dans votre abonnement, vous verrez le circuit que vous souhaitez utiliser dans la liste des circuits. Si vous ne voyez aucun circuit, mais que vous l’avez fourni avec une clé d’autorisation et un URI de circuit homologue, vous pouvez échanger et connecter un circuit. Consultez [Rechercher un circuit en utilisant une clé d'autorisation](#authkey).

1. Sélectionnez le circuit.
2. Sélectionnez **Connecter un ou plusieurs circuits**.

   ![connecter des circuits](./media/virtual-wan-expressroute-portal/cktconnect.png "connecter des circuits")

### <a name="authkey"></a>Se connecter en utilisant une clé d'autorisation

Utilisez la clé d’autorisation et l’URI de circuit fournis pour vous connecter.

1. Sur la page ExpressRoute, cliquez sur **+Échanger la clé d’autorisation**

   ![échanger](./media/virtual-wan-expressroute-portal/redeem.png "échanger")
2. Dans la page Échanger la clé d’autorisation, renseignez les valeurs.

   ![échanger des valeurs de clés](./media/virtual-wan-expressroute-portal/redeemkey2.png "échanger des valeurs de clés")
3. Sélectionnez **Ajouter** pour ajouter la clé.
4. Affichez le circuit. Un circuit échangé affiche uniquement le nom (sans le type, le fournisseur et d’autres informations), car il se trouve dans un abonnement différent de celui de l’utilisateur.

## <a name="to-test-connectivity"></a>Pour tester la connectivité

Une fois la connexion au circuit établie, l’état de la connexion du hub indique « Ce hub », signifiant que la connexion est établie à la passerelle ExpressRoute Hub. Attendez environ 5 minutes avant de tester la connectivité à partir d’un client situé derrière votre circuit ExpressRoute, par exemple, une machine virtuelle dans le réseau virtuel que vous avez créée précédemment.

Si vous avez des sites connectés à une passerelle VPN de Virtual WAN dans le même hub que la passerelle ExpressRoute, vous pouvez disposer d’une connectivité bidirectionnelle entre les points de terminaison VPN et ExpressRoute. Le routage dynamique (BGP) est pris en charge. L’ASN des passerelles dans le hub est fixe et ne peut pas être modifié pour l’instant.

## <a name="to-change-the-size-of-a-gateway"></a>Pour modifier la taille d’une passerelle

Si vous souhaitez modifier la taille de votre passerelle ExpressRoute, localisez la passerelle ExpressRoute dans le hub, puis sélectionnez les unités d’échelle dans la liste déroulante. Enregistrez vos modifications. La mise à jour de la passerelle du hub prendra environ 30 minutes.

![modifier la taille d’une passerelle](./media/virtual-wan-expressroute-portal/changescale.png "modifier la taille d’une passerelle")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Pour publier l’itinéraire par défaut 0.0.0.0/0 vers les points de terminaison

Si vous souhaitez que le hub virtuel Azure publie l’itinéraire par défaut 0.0.0.0/0 sur vos points de terminaison ExpressRoute, vous devez activer « Propager l’itinéraire par défaut ».

1. Sélectionnez votre **Circuit ->...-> Modifie la connexion**.

   ![Modifier la connexion](./media/virtual-wan-expressroute-portal/defaultroute1.png "Modifier la connexion")

2. Sélectionnez **Activer** pour propager l’itinéraire par défaut.

   ![Propager l’itinéraire par défaut](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propager l’itinéraire par défaut")

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).