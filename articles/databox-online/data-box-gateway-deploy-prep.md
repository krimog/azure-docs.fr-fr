---
title: Didacticiel sur la préparation du portail Azure pour déployer Data Box Gateway | Microsoft Docs
description: Le premier didacticiel visant à déployer Azure Data Box Gateway implique la préparation du Portail Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 428b42e62f44d182de109740359544135561e54b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441493"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Didacticiel : Préparer le déploiement d’Azure Data Box Gateway


Ce didacticiel est le premier d’une série de didacticiels permettant de déployer entièrement Azure Data Box Gateway. Il explique comment préparer le Portail Azure pour déployer la ressource Data Box Gateway. 

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Télécharger l'image de l’appareil virtuel
> * Obtenir la clé d'activation

## <a name="get-started"></a>Prise en main

Pour déployer Data Box Gateway, consultez les didacticiels suivants dans l’ordre indiqué.

| **#** | **Dans cette étape** | **Utilisez ces documents** |
| --- | --- | --- | 
| 1. |**[Préparer le Portail Azure pour Data Box Gateway](data-box-gateway-deploy-prep.md)** |Créez et configurez votre ressource Data Box Gateway avant l’approvisionnement d’un appareil virtuel Data Box Gateway. |
| 2. |**[Approvisionner Azure Data Box Gateway dans Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Approvisionner Azure Data Box Gateway dans VMware](data-box-gateway-deploy-provision-vmware.md)**|Pour Hyper-V, approvisionnez un appareil virtuel Data Box Gateway et connectez-vous à celui-ci sur un système hôte exécutant Hyper-V sur Windows Server 2016, Windows Server 2012 ou R2. <br><br><br> Pour VMware, provisionnez un appareil virtuel Data Box Gateway et connectez-vous à celui-ci sur un système hôte exécutant VMware ESXi 6.0, 6.5 ou 6.7.<br></br> |
| 3. |**[Se connecter à Data Box Gateway, le configurer et l’activer](data-box-gateway-deploy-connect-setup-activate.md)** |Connectez-vous à l’interface utilisateur web locale, terminez l’installation de l’appareil et activez-le. Vous pouvez ensuite configurer les partages SMB.  |
| 4. |**[Transférer des données avec Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Ajoutez des partages et connectez-vous à des partages via SMB ou NFS. |

Vous pouvez maintenant commencer à configurer le portail Azure.

## <a name="prerequisites"></a>Prérequis

Dans les informations suivantes, vous trouverez la configuration requise pour votre ressource Data Box Gateway, votre appareil Data Box Gateway et le réseau du centre de données.

### <a name="for-the-data-box-gateway-resource"></a>Pour la ressource Data Box Gateway

Avant de commencer, assurez-vous que :

- Votre abonnement Microsoft Azure doit être pris en charge pour la ressource Data Box Gateway. Les abonnements de type Paiement à l’utilisation sont aussi pris en charge.
- Vous disposez d’un accès Propriétaire ou Contributeur au niveau du groupe de ressources pour les ressources Data Box Edge/Data Box Gateway, IoT Hub et Stockage Azure.

    - Pour créer une ressource Data Box Edge/Data Box Gateway, vous devez disposer d’autorisations Contributeur (ou supérieures) au niveau du groupe de ressources. Vous devez également être sûr que le fournisseur `Microsoft.DataBoxEdge` est inscrit. Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Pour créer une ressource de compte de stockage, là encore, vous avez besoin d’autorisations Contributeur (ou supérieures) au niveau du groupe de ressources. Par défaut, le stockage Azure est un fournisseur de ressources inscrit.
- Vous avez un accès Administrateur ou Utilisateur à l’API Graph Azure Active Directory. Pour plus d’informations, consultez [API Graph Azure Active Directory](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

### <a name="for-the-data-box-gateway-device"></a>Pour l’appareil Data Box Gateway

Avant de déployer un appareil virtuel, assurez-vous que :

- Vous avez accès à un système hôte exécutant Hyper-V sur Windows Server 2012 R2 ou une version ultérieure ou sur VMware (ESXi 6.0, 6.5 ou 6.7) et permettant de provisionner un appareil.
- Le système hôte est en mesure de dédier les ressources suivantes pour approvisionner votre appareil virtuel Data Box :
  
  - Au moins 4 processeurs virtuels.
  - Au moins 8 Go de RAM.
  - Une interface réseau.
  - Un disque de système d’exploitation de 250 Go.
  - Un disque virtuel de 2 To pour les données système.

### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

- Le réseau de votre centre de données est configuré conformément à la configuration réseau requise pour votre appareil Data Box Gateway. Pour plus d’informations, consultez [Configuration requise pour Data Box Gateway](data-box-gateway-system-requirements.md).

- Pour utiliser votre appareil Data Box Gateway dans des conditions normales, vous devez avoir :

    - Une bande passante de chargement de minimum 10 Mbits/s pour s’assurer que l’appareil reste à jour.
    - Une bande passante de chargement et de téléchargement de minimum 20 Mbits/s dédiée pour transférer des fichiers.

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

Si vous disposez d’une ressource Data Box Gateway existante pour gérer vos appareils virtuels, ignorez cette étape et accédez à [Obtenir la clé d’activation](#get-the-activation-key).

Pour créer une ressource Data Box Gateway, effectuez les étapes suivantes dans le portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter :

    - Au portail Azure à cette URL : [https://portal.azure.com](https://portal.azure.com)
    - Ou bien, au portail Azure Government à cette URL : [https://portal.azure.us](https://portal.azure.us) Pour plus d’informations, accédez à [Se connecter à Azure Government à l’aide du portail](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Dans le volet de gauche, sélectionnez **+ Créer une ressource**. Recherchez **Data Box Edge / Data Box Gateway**. Sélectionnez Data Box Edge / Data Box Gateway. Sélectionnez **Créer**.
3. Sélectionnez l’abonnement que vous souhaitez utiliser pour l’appareil Data Box Gateway. Sélectionnez la région dans laquelle vous souhaitez déployer la ressource Data Box Gateway. Pour cette version, les zones USA Est, Asie Sud-Est et Europe Ouest sont disponibles. Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil. Pour l’option **Data Box Gateway**, sélectionnez **Créer**.

    ![Rechercher le service Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Sous l’onglet **Bases**, entrez ou sélectionnez les **détails du projet** suivants.
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement    |Ce champ est automatiquement renseigné en fonction de la sélection antérieure. L’abonnement est lié à votre compte de facturation. |
    |Groupe de ressources  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).     |

5. Entrez ou sélectionnez les **détails de l’instance** suivants.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom doit être compris entre 2 et 50 caractères, et se composer uniquement de lettres, de chiffres et de traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |   
    |Région     |Pour cette version, les zones USA Est, Asie Sud-Est et Europe Ouest sont disponibles pour déployer votre ressource. Pour Azure Government, toutes les régions administratives listées dans les [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/) sont disponibles. <br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|
    
    ![Créer la ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Sélectionnez **Revoir + créer**.
 
7. Sous l’onglet **Vérifier + créer**, passez en revue les **Détails de la tarification**, les **Conditions d’utilisation** et les détails de votre ressource. Sélectionnez **Créer**.

    ![Passer en revue les détails de la ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

La création de la ressource prend quelques minutes. Un message vous informe que la ressource a été créée et déployée. Sélectionnez **Accéder à la ressource**.

![Passer en revue les détails de la ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Télécharger l'image de l’appareil virtuel

Une fois que la ressource Data Box Gateway est créée, téléchargez l'image de l’appareil virtuel appropriée pour approvisionner un appareil virtuel sur le système hôte. Les images d’appareil virtuel sont spécifiques à un système d’exploitation.

> [!IMPORTANT]
> Le logiciel en cours d’exécution sur Data Box Gateway peut seulement être utilisé avec la ressource Data Box Gateway.

Suivez ces étapes dans le [portail Azure](https://portal.azure.com/) pour télécharger une image d’appareil virtuel.

1. Dans la ressource que vous avez créée, sélectionnez **Vue d’ensemble**. Si vous disposez d’une ressource Azure Data Box Gateway existante, sélectionnez sur la ressource, puis accédez à **Vue d’ensemble**. Sélectionnez **Configuration de l’appareil**.

    ![Nouvelle ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Sur la vignette **Télécharger l’image**, sélectionnez l’image d’appareil virtuel correspondant au système d’exploitation sur le serveur hôte utilisé pour provisionner la machine virtuelle. Les fichiers image ont une taille d’environ 5,6 Go.
   
   * [VHD pour Hyper-V sur Windows Server 2012 R2 et versions ultérieures](https://aka.ms/dbe-vhdx-2012)
   * [VMDK pour VMWare ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

    ![Télécharger l’image d’appareil virtuel Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Téléchargez et décompressez le fichier sur un lecteur local et notez l’emplacement du fichier décompressé.


## <a name="get-the-activation-key"></a>Obtenir la clé d'activation

Une fois que la ressource Data Box Gateway est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Data Box Gateway et à le connecter à la ressource. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Sélectionnez la ressource que vous avez créée, puis **Vue d’ensemble**. Dans la **Configuration de l’appareil**, accédez à la vignette **Configurer et activer**.

    ![Vignette Configurer et activer](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Sélectionnez **Générer une clé** pour créer une clé d’activation. Sélectionnez l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.

    ![Obtenir une clé d’activation](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La clé d’activation expire 3 jours après sa création.
> - Si la clé expire, générez une nouvelle clé. L’ancienne clé n'est plus valide.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Gateway et avez notamment appris à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Télécharger l'image de l’appareil virtuel
> * Obtenir la clé d'activation

Passez au didacticiel suivant pour découvrir comment approvisionner une machine virtuelle pour Data Box Gateway. Selon votre système d'exploitation hôte, consultez les instructions détaillées dans :

> [!div class="nextstepaction"]
> [Approvisionner une instance Data Box Gateway dans Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

Ou

> [!div class="nextstepaction"]
> [Approvisionner une instance Data Box Gateway dans VMware](./data-box-gateway-deploy-provision-vmware.md)


