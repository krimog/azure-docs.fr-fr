---
title: Créer une application d’analytique de compteur intelligente avec IoT Central | Microsoft Docs
description: Découvrez comment créer une application de surveillance de compteur intelligente à l’aide de modèles d’application Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: c7bb35e7de6400a0fad13845a3faa38ad21b6de7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581255"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Didacticiel : Créer et découvrir pas à pas le modèle d’application de surveillance de compteur intelligente 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce didacticiel vous guide tout au long du processus de création de l’application de surveillance de compteur intelligente, qui comprend un exemple de modèle d’appareil avec des données simulées. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer l’application de compteur intelligent gratuitement
> * Découvrir pas à pas l’application
> * Supprimer des ressources


Si vous n’avez pas d’abonnement, [créez un compte d’essai gratuit](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Prérequis
- Aucun
- Pour l’essai, un abonnement Azure est recommandé, mais pas obligatoire

## <a name="create-a-smart-meter-monitoring-app"></a>Créer une application de surveillance de compteur intelligente 

Vous pouvez créer cette application en trois étapes simples :

1. Ouvrez la [page d’accueil d’Azure IoT Central](https://apps.azureiotcentral.com), puis cliquez sur **Générer** pour créer une application. 

2. Sélectionnez l’onglet **Énergie**, puis, sous la vignette d’application **Surveillance de compteur intelligente**, cliquez sur **Créer une application**.

    > [!div class="mx-imgBorder"]
    > ![Créer une application](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. La commande **Créer une application** ouvre le formulaire **Nouvelle application**. Entrez les détails demandés, comme illustré dans la figure ci-dessous :
    * **Nom d’application** : Choisissez un nom pour votre application IoT Central. 
    * **URL** : Choisissez une URL IoT Central. La plateforme vérifie son unicité.
    * **Essai gratuit de 7 jours** : Si vous avez déjà un abonnement Azure, le paramètre par défaut est recommandé. Si vous n’avez pas d’abonnement Azure, commencez avec l’essai gratuit.
    * **Informations de facturation** : L’application proprement dite est gratuite. Les champs Annuaire, Abonnement Azure et Région doivent être obligatoirement renseignés l’approvisionnement des ressources de votre application.
    * Cliquez sur bouton **Créer** au bas de la page pour créer votre application. L’opération prend environ une minute.     
        > [!div class="mx-imgBorder"]
        > ![Formulaire Nouvelle application](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Vérifier l’application et les données simulées

L’application de compteur intelligente nouvellement créée vous appartient. Vous pouvez la modifier à tout moment. Avant de modifier l’application, vérifions qu’elle est déployée et qu’elle fonctionne comme prévu.

Pour vérifier la création de l’application et la simulation des données, accédez au **Tableau de bord**. Si vous pouvez voir les vignettes avec des données dans celles-ci, cela signifie que le déploiement de votre application a réussi. La simulation des données pouvant prendre quelques minutes, patientez 1 ou 2 minutes. 

## <a name="application-walk-through"></a>Découvrir pas à pas l’application
Une fois le modèle d’application correctement déployé, il comprend un exemple d’appareil de compteur intelligent, un modèle d’appareil et un tableau de bord. 

Adatum est un fournisseur d’énergie fictif qui surveille et gère des compteurs intelligents. Le tableau de bord de surveillance de compteur intelligente, affiche des propriétés, des données et des exemples de commandes. Il permet aux opérateurs et aux équipes du support technique d’effectuer de manière proactive les activités suivantes avant l’apparition d’incidents de support : 
* Examiner les dernières informations du compteur et l’emplacement où celui-ci est installé sur la carte
* Vérifier de manière proactive le réseau du compteur et son état de la connexion 
* Surveiller les mesures de tensions minimale et maximale pour vérifier l’intégrité du réseau 
* Examiner les tendances en termes d’énergie, de puissance et de tension pour détecter d’éventuelles anomalies 
* Suivre la consommation énergétique totale à des fins de planification et de facturation
* Opérations de commande et de contrôle telles que la reconnexion du compteur et la mise à jour de la version du microprogramme. Dans le modèle, les boutons de commande affichent les fonctionnalités possibles et n’envoient pas de commandes réelles. 

> [!div class="mx-imgBorder"]
> ![Tableau de bord de surveillance de compteur intelligente](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Appareils
L’application est fournie avec un exemple d’appareil de compteur intelligent. Vous pouvez consulter les détails de l’appareil en cliquant sur l’onglet **Appareils**.

> [!div class="mx-imgBorder"]
> ![Appareils de compteur intelligents](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Cliquez sur le lien de l’exemple d’appareil **SM0123456789** pour afficher les détails de celui-ci. Vous pouvez mettre à jour les propriétés accessibles en écriture de l’appareil dans la page **Mettre à jour les propriétés**, et visualiser les valeurs mises à jour sur le tableau de bord.

> [!div class="mx-imgBorder"]
> ![Propriétés de compteur intelligent](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Modèle d’appareil
Cliquez sur l’onglet **Modèles d’appareils** pour afficher le modèle d’appareil de compteur intelligent. Le modèle a une interface pré-définie pour les données, les propriétés, les commandes et les affichages.

> [!div class="mx-imgBorder"]
> ![Modèles d’appareil de compteur intelligent](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous décidez de ne pas continuer à utiliser cette application, supprimez-la en procédant comme suit :

1. Dans le volet gauche, ouvrez l’onglet Administration.
2. Sélectionnez Paramètres de l’application, puis cliquez sur le bouton Supprimer au bas de la page. 

    > [!div class="mx-imgBorder"]
    > ![Supprimer une application](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’architecture d’application de compteur intelligente en consultant l’[article relatif au concept](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Créer gratuitement des modèles d’application de compteur intelligente : [application de compteur intelligente](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* En apprendre davantage sur IoT Central en consultant la [vue d’ensemble d’IoT Central](https://docs.microsoft.com/azure/iot-central/)
