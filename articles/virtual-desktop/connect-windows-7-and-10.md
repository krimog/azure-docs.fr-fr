---
title: Se connecter à Windows Virtual Desktop - Windows 10 ou 7 - Azure
description: Guide pratique pour se connecter à Windows Virtual Desktop à l’aide du client Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: helohr
ms.openlocfilehash: 2552fcbd860a0cc98aa7e2a6c7f92796a8d588ca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605785"
---
# <a name="connect-with-the-windows-desktop-client"></a>Se connecter avec le client Windows Desktop

> S’applique à : Windows 7 et Windows 10

Vous pouvez accéder aux ressources Windows Virtual Desktop sur des appareils Windows 7 ou Windows 10 à l’aide du client Windows Desktop.

> [!IMPORTANT]
> Windows Virtual Desktop ne prend pas en charge le client RADC (Connexions RemoteApp et Bureau à distance) ni le client MSTSC (Connexion Bureau à distance).

## <a name="install-the-windows-desktop-client"></a>Installer le client Windows Desktop

Choisissez le client qui correspond à votre version de Windows :

- [Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bit, version préliminaire](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64, version préliminaire](https://go.microsoft.com/fwlink/?linkid=2098961)

Vous pouvez installer le client pour l’utilisateur actuel, ce qui ne nécessite pas de droits d’administrateur, ou votre administrateur peut installer et configurer le client afin que tous les utilisateurs de l’appareil puissent y accéder.

Une fois installé, le client peut être lancé à partir du menu Démarrer quand vous recherchez **Bureau à distance**.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Obtenez la liste des ressources managées disponibles pour vous en vous abonnant au flux fourni par votre administrateur. Grâce à un abonnement, les ressources sont disponibles sur votre ordinateur local.

Pour s’abonner à un flux :

1. Ouvrez le client Windows Desktop.
2. Sélectionnez **S’abonner** dans la page principale pour vous connecter au service et récupérer vos ressources.
3. Connectez-vous avec votre compte d’utilisateur quand vous y êtes invité.

Une fois la connexion réussie, vous devez voir la liste des ressources auxquelles vous pouvez accéder.

Vous pouvez lancer des ressources selon l’une de deux méthodes suivantes.

- Dans la page principale du client, double-cliquez sur une ressource pour la lancer.
- Lancez une ressource comme vous le feriez normalement pour d’autres applications à partir du menu Démarrer.
  - Vous pouvez également rechercher les applications dans la barre de recherche.

Une fois abonné à un flux, le contenu du flux de données est automatiquement mis à jour régulièrement. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client Windows Desktop, consultez [Bien démarrer avec le client Windows Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
