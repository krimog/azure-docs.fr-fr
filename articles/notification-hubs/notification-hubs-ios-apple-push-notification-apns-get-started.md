---
title: Notifications Push vers des applications iOS à l’aide d’Azure Notification Hubs | Microsoft Docs
description: Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application iOS.
services: notification-hubs
documentationcenter: ios
keywords: notification push,notifications push,notifications push ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 452ccfc796fcd2a390c7380f4c6b2ced2057dc3b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822351"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Didacticiel : Notifications Push vers des applications iOS avec Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Dans ce didacticiel, vous utilisez Azure Notification Hubs pour envoyer des notifications Push à une application iOS. Vous créez une application iOS vide qui reçoit des notifications Push à l’aide du [service de notification Push Apple (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Dans ce didacticiel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Génération du fichier de demande de signature de certificat
> * Inscription de votre application pour les notifications Push
> * Création d’un profil d’approvisionnement pour l’application
> * Configuration de votre hub de notification pour les notifications Push iOS
> * Connexion de votre application iOS aux hubs de notification
> * Envoi de notifications Push de test
> * Vérification de la réception des notifications par votre application

Le code complet de ce didacticiel est disponible sur [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free).
* [Infrastructure de messagerie Azure Windows]
* Version la plus récente de [Xcode]
* Appareil iOS version 10 (ou ultérieure)
* [programme pour développeurs Apple](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > En raison des exigences de configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil iOS physique (iPhone ou iPad) au lieu du simulateur iOS.
  
Vous devez terminer ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connexion de votre application iOS à Notification Hubs

1. Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View Application** .

    ![Xcode - Application à vue unique][8]

2. Au moment de définir les options de votre nouveau projet, veillez à utiliser le même **nom de produit** et le même **identificateur d’organisation** que ceux utilisés lorsque vous avez défini l’identifiant d’offre groupée dans le portail des développeurs Apple.

    ![Xcode - options de projet][11]

3. Dans le navigateur de projet, cliquez sur le nom de votre projet et sur l’onglet **Général**, puis recherchez **Signature**. Vérifiez que vous sélectionnez l’équipe correspondant à votre compte de développeur Apple. XCode doit extraire automatiquement le profil d’approvisionnement que vous avez créé précédemment en fonction de l’identificateur d’offre groupée.

    Si vous ne voyez pas le nouveau profil d’approvisionnement que vous avez créé dans Xcode, essayez d’actualiser les profils pour votre identité de signature. Cliquez sur **Xcode** dans la barre de menus, sur **Preferences**, sur l’onglet **Account**, sur le bouton **View Details**, sur votre identité de signature, puis cliquez sur le bouton d’actualisation dans le coin inférieur droit.

    ![Xcode - profil d’approvisionnement][9]

4. Sélectionnez l’onglet **Fonctionnalités** sans oublier d’activer les notifications Push.

    ![Xcode : Fonctionnalités push][12]

5. Ajoutez les modules du Kit de développement logiciel (SDK) Azure Notification Hubs.

   Vous pouvez intégrer le Kit de développement logiciel (SDK) Azure Notification Hubs dans votre application à l’aide de [Cocoapods](https://cocoapods.org) ou en ajoutant manuellement les fichiers binaires à votre projet.

   - Intégration via Cocoapods

     Ajoutez les dépendances suivantes à votre `podfile` pour inclure le Kit de développement logiciel (SDK) Azure Notification Hubs dans votre application.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Exécutez `pod install` pour installer votre pod récemment défini et ouvrir votre `.xcworkspace`.

     > [!NOTE]
     > Si le message d’erreur **[!] Unable to find a specification for AzureNotificationHubs-iOS** s’affiche lors de l’exécution de `pod install`, exécutez `pod repo update` pour obtenir les derniers pods à partir du référentiel Cocoapods, puis exécutez `pod install`.

   - Intégration via Carthage

     Ajoutez les dépendances suivantes à votre `Cartfile` pour inclure le Kit de développement logiciel (SDK) Azure Notification Hubs dans votre application.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Ensuite, mettez à jour et créez des dépendances :

     ```shell
     $ carthage update
     ```

     Pour plus d’informations sur l’utilisation de Carthage, voir le [référentiel GitHub Carthage](https://github.com/Carthage/Carthage).

   - Intégration en copiant les fichiers binaires dans votre projet

     1. Téléchargez l’infrastructure [Kit de développement logiciel (SDK) Azure Notification Hubs](https://github.com/Azure/azure-notificationhubs-ios/releases) fournie dans un fichier zip et décompressez-le.

     2. Dans Xcode, cliquez avec le bouton droit sur votre projet et sélectionnez l’option **Add Files to** pour ajouter le dossier **WindowsAzureMessaging.framework** à votre projet Xcode. Sélectionnez **Options**, assurez-vous que l’option **Copy items if needed** (Copier les éléments si nécessaire) est cochée, puis cliquez sur **Add** (Ajouter).

        ![Décompresser le SDK Azure][10]

6. Ajoutez un nouveau fichier d’en-tête à votre projet nommé `HubInfo.h`. Ce fichier contient les constantes de votre hub de notification. Ajoutez les définitions suivantes et remplacez les espaces réservés des littéraux de chaîne par le *nom de votre hub* et la chaîne *DefaultListenSharedAccessSignature* notée précédemment.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

    #define HUBNAME @"<Enter the name of your hub>"
    #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Ouvrez le fichier `AppDelegate.h` et ajoutez l’instruction d’importation suivante :

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```

8. Dans le fichier `AppDelegate.m`, ajoutez le code suivant dans la méthode `didFinishLaunchingWithOptions` basée sur votre version d’iOS. Ce code enregistre le handle de votre appareil avec APNs :

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. Dans ce même fichier, ajoutez les méthodes suivantes :

    ```objc
    (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
     SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                 notificationHubPath:HUBNAME];

     [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
         if (error != nil) {
             NSLog(@"Error registering for notifications: %@", error);
         }
         else {
             [self MessageBox:@"Registration Status" message:@"Registered"];
         }
     }];
     }

    (void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Ce code se connecte au hub de notification utilisant les informations de connexion que vous avez spécifiées dans HubInfo.h. Il transmet ensuite le jeton de l’appareil au hub de notification de sorte que ce dernier puisse envoyer des notifications.

10. Dans le même fichier, ajoutez la méthode suivante pour afficher une **UIAlert** si la notification est reçue alors que l’application est active :

    ```objc
    (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
      NSLog(@"%@", userInfo);
      [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Pour vérifier l’absence d’échecs, générez et exécutez l’application sur votre appareil.

## <a name="send-test-push-notifications"></a>Envoi de notifications Push de test

Vous pouvez tester la réception de notifications dans votre application avec l’option *Test Send* du [portail Azure]. Cette option envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Vérification de la réception des notifications Push par votre application

Pour tester les notifications Push sur iOS, vous devez déployer l’application sur un appareil iOS physique. Vous ne pouvez pas envoyer de notifications push Apple en utilisant le simulateur iOS.

1. Exécutez l’application, vérifiez que l’inscription est effectuée, puis appuyez sur **OK**.

    ![Test d’inscription de notifications Push pour applications iOS][33]

2. Vous envoyez ensuite une notification Push de test depuis le [portail Azure], comme indiqué dans la section précédente.

3. La notification Push est envoyée à tous les appareils qui sont inscrits pour recevoir les notifications depuis le hub de notification concerné.

    ![Test de réception de notifications Push pour applications iOS][35]

## <a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications Push à tous vos appareils iOS inscrits. Pour savoir comment envoyer des notifications à des appareils iOS spécifiques, passez au didacticiel suivant :

> [!div class="nextstepaction"]
>[Notifications Push vers des appareils spécifiques](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Infrastructure de messagerie Azure Windows]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portail Azure]: https://portal.azure.com
