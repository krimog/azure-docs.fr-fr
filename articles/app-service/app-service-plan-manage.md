---
title: Gérer un plan App Service - Azure | Microsoft Docs
description: Découvrez comment effectuer différentes tâches pour gérer un plan App Service.
keywords: app service, azure app service, mise à l’échelle, plan app service, changer, créer, gérer, gestion
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a5e69209c30eae816837ce8f00a065231a5fd821
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067207"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gérer un plan App Service dans Azure

Un [plan Azure App Service](overview-hosting-plans.md) fournit les ressources nécessaires à l’exécution d’une application App Service. Ce guide montre comment gérer un plan App Service.

## <a name="create-an-app-service-plan"></a>Créer un plan App Service

> [!TIP]
> Si vous avez un environnement App Service, consultez [Créer un plan App Service dans un environnement App Service](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Vous pouvez créer un plan App Service vide ou en créer un dans le cadre de la création d’une application.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Nouveau** > **Web + Mobile**, puis sélectionnez **Application web** ou un autre genre d’application App Service.

2. Sélectionnez un plan App Service existant ou créez-en un pour la nouvelle application.

   ![Créer une application dans le portail Azure.][createWebApp]

   Pour créer un plan :

   a. Sélectionnez **[+] Créer nouveau**.

      ![Créer un plan App Service.][createASP] 

   b. Sous **Plan App Service**, entrez le nom du plan.

   c. Sous **Localisation**, sélectionnez une localisation appropriée.

   d. Sous **Niveau tarifaire**, sélectionnez un niveau tarifaire approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d’options de tarification, telles que **Gratuit** et **Partagé**. Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Déplacer une application vers un autre plan App Service

Vous pouvez déplacer une application vers un autre plan App Service tant que les plans source et cible se trouvent dans _le même groupe de ressources et la même région géographique_.

> [!NOTE]
> Azure déploie chaque nouveau plan App Service dans une unité de déploiement, désignée en interne sous le terme d’espace web. Chaque région peut avoir de nombreux espaces web, mais votre application peut uniquement être déplacée entre des plans créés dans le même espace web. Un environnement App Service est un espace web isolé. Il en découle que les applications peuvent être déplacées entre les différents plans du même environnement App Service, mais pas entre des plans créés dans d’autres environnements App Service.
>
> Quand vous créez un plan, vous ne pouvez pas choisir l’espace web à utiliser, mais vous pouvez spécifier que le plan soit créé dans le même espace web qu’un autre plan existant. En résumé, tous les plans créés avec la même combinaison groupe de ressources/région sont déployés au sein du même espace web. Par exemple, si vous avez créé un plan dans le groupe de ressources A et la région B, tous les plans que vous créerez par la suite dans ce groupe de ressources A et cette région B seront déployés dans le même espace web. Notez que vous ne pouvez pas déplacer les plans vers d’autres espaces web après leur création et que, pour cette raison, vous ne pouvez pas déplacer un plan dans « le même espace web » qu’un autre plan en le déplaçant vers un autre groupe de ressources.
> 

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’application à déplacer.

1. Dans le menu, recherchez la section **Plan App Service**.

1. Sélectionnez **Changer le plan App Service** pour ouvrir le sélecteur de **plan App Service**.

   ![Sélecteur de plan App Service.][change] 

1. Dans le sélecteur de **plan App Service**, sélectionnez un plan existant vers lequel déplacer cette application.   

La page **Sélectionner un plan App Service** affiche uniquement les plans qui se trouvent dans le même groupe de ressources et la même région géographique que le plan App Service de l’application actuelle.

Chaque plan a son propre niveau de tarification. Par exemple, quand vous passez du niveau **Gratuit** au niveau **Standard** d’un site, toutes les applications affectées peuvent utiliser les fonctionnalités et ressources du niveau **Standard**. Par contre, le déplacement d’une application d’un plan de niveau supérieur vers un plan de niveau inférieur vous prive de certaines fonctionnalités. Si votre application utilise une fonctionnalité qui n’est pas disponible dans le plan cible, vous obtenez une erreur qui indique quelle fonctionnalité en cours d’utilisation n’est pas disponible. 

Par exemple, si l’une de vos applications utilise des certificats SSL, ce message d’erreur peut s’afficher :

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Dans ce cas, avant de pouvoir déplacer l’application vers le plan cible, vous devez :
- Faire passer le plan cible à un niveau tarifaire supérieur (**De base** au minimum).
- Ou supprimer toutes les connexions SSL à votre application.

## <a name="move-an-app-to-a-different-region"></a>Déplacer une application vers une autre région

La région dans laquelle votre application s’exécute est la région où se trouve le plan App Service. Toutefois, vous ne pouvez pas changer la région d’un plan App Service. Si vous souhaitez exécuter votre application dans une autre région, vous pouvez également utiliser le clonage d’application. Le clonage crée une copie de votre application dans un plan App Service, nouveau ou existant, dans n’importe quelle région.

La commande **Cloner l’application** figure dans la section **Outils de développement** du menu.

> [!IMPORTANT]
> Le clonage présente des limitations. Pour en savoir plus sur celles-ci, consultez [Clonage de l’application Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Mettre à l’échelle un plan App Service

Pour faire évoluer le niveau tarifaire d’un plan App Service, consultez [Scalabilité verticale d’une application dans Azure](manage-scale-up.md).

Pour augmenter le nombre d’instances d’une application, consultez [Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Supprimer un plan App Service

Par défaut, pour éviter des frais inattendus, quand vous supprimez la dernière application d’un plan App Service, App Service supprime également le plan. Si vous choisissez de conserver le plan, faites-le passer au niveau **Gratuit** pour ne pas être facturé.

> [!IMPORTANT]
> Les plans App Service auxquels aucune application n’est associée engendrent encore des frais, car ils continuent à réserver les instances de machine virtuelle configurées.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Montée en puissance d’une application dans Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
