---
title: Configurer des règles et des actions dans Azure IoT Central
description: Dans le cadre de ce démarrage rapide, vous découvrez, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7e2047ee824c3dc8b6387f7879757a1e7be7a4a9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896657"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Démarrage rapide : Configurer des règles et des actions pour votre appareil dans Azure IoT Central (fonctionnalités d’évaluation)

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Dans le cadre de ce démarrage rapide, vous allez créer une règle qui envoie un e-mail quand la température d’un capteur environnemental dépasse 90&deg; F.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez suivre les deux guides de démarrage rapide précédents : [Créer une application Azure IoT Central](./quick-deploy-iot-central.md) et [Ajouter un appareil simulé à votre application IoT Central](./quick-create-pnp-device.md) pour créer le modèle d'appareil **Capteur environnemental** avec lequel travailler.

## <a name="create-a-telemetry-based-rule"></a>Créer une règle basée sur la télémétrie

1. Pour ajouter une nouvelle règle basée sur la télémétrie à votre application, dans le volet de gauche, sélectionnez **Règles**.

1. Pour créer une nouvelle règle, sélectionnez **+ Nouveau**.

1. Entrez **Température ambiante** comme nom de la règle.

1. Dans la section **Appareils cibles**, sélectionnez le **Capteur environnemental** comme modèle d’appareil. Cette option filtre les appareils auxquels s’applique la règle par type de modèle d’appareil. Vous pouvez ajouter d’autres critères de filtre en choisissant **+ Filtre**.

1. Dans la section **Conditions**, vous définissez ce qui déclenche votre règle. Utilisez les informations suivantes pour définir une condition basée sur la télémétrie de température :

    | Champ                                        | Valeur                             |
    | -------------------------------------------- | ------------------------------    |
    | Mesure                                  | Température                       |
    | Operator                                     | est supérieur à                   |
    | Valeur                                        | 90                                |

    Pour ajouter d’autres conditions, sélectionnez **+ Condition**.

    ![Créer une condition de règle](./media/quick-configure-rules/condition.png)

1. Pour ajouter une action d'e-mail à exécuter quand la règle se déclenche, sélectionnez **+ E-mail**.

1. Utilisez les informations du tableau suivant pour définir votre action :

    | Paramètre   | Valeur                                             |
    | --------- | ------------------------------------------------- |
    | Nom complet | Action d'e-mail de l'opérateur                          |
    | À        | Votre adresse e-mail                                |
    | Notes     | La température ambiante a dépassé le seuil. |

    > [!NOTE]
    > Pour recevoir une notification par courrier électronique, l’adresse e-mail doit être un [ID d’utilisateur dans l’application](howto-administer.md), et l’utilisateur doit s’être connecté au moins une fois à l’application.

    ![Créer une action de règle](./media/quick-configure-rules/action.png)

1. Sélectionnez **Enregistrer**. Votre règle est listée dans la page **Règles**.

## <a name="test-the-rule"></a>Tester la règle

Peu après l’enregistrement de la règle, elle devient active. Lorsque les conditions définies dans la règle sont remplies, votre application envoie un message à l’adresse e-mail que vous avez spécifiée dans l’action.

> [!NOTE]
> Une fois le test terminé, désactivez la règle pour ne plus recevoir d’alertes dans votre boîte de réception.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

* Créer une règle basée sur la télémétrie
* Ajouter une action

Pour en savoir plus sur la surveillance des appareils connectés à votre application, passez au guide de démarrage rapide :

> [!div class="nextstepaction"]
> [Utiliser Azure IoT Central pour surveiller vos appareils](quick-monitor-devices.md).
