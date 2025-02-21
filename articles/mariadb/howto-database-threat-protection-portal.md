---
title: Advanced Threat Protection - Azure Database for MariaDB | Microsoft Docs
description: La protection contre les menaces détecte les activités de base de données anormales qui indiquent la présence potentielle de menaces de sécurité pour la base de données.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: f77d9f105d5c2ff10753f2b4b1ecc962c84eee6e
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869364"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Advanced Threat Protection pour Azure Database for MariaDB

La protection Advanced Threat Protection pour Azure Database for MariaDB détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

La protection avancée contre les menaces fait partie de l’offre Advanced Data Security qui est un package unifié de fonctionnalités de sécurité avancées. Vous pouvez accéder à Advanced Threat Protection et le gérer via le [portail Azure](https://portal.azure.com).

> [!IMPORTANT]
> Advanced Threat Protection est disponible en préversion publique. Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database for MariaDB est déployé, pour les serveurs à usage général et à mémoire optimisée.

> [!NOTE]
> La fonctionnalité Advanced Threat Protection **n’est pas** disponible dans les régions administratives et souveraines suivantes du cloud Azure : US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginie, US DoD Est, US DoD Central, Allemagne Centre, Allemagne Nord, Chine Est, Chine Est 2. Consultez les [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/) pour connaître la disponibilité générale des produits.

## <a name="set-up-threat-detection"></a>Configurer la détection des menaces
1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration du serveur Azure Database for MariaDB que vous voulez protéger. Dans les paramètres de sécurité, sélectionnez **Protection avancée contre les menaces (préversion)** .
3. Dans la page de configuration **Protection avancée contre les menaces (préversion)** :

   - Activez la protection avancée contre les menaces sur le serveur.
   - Dans les **paramètres de protection avancée contre les menaces**, dans la zone de texte **Envoyer les alertes à**, fournissez la liste d’adresses e-mail devant recevoir des alertes de sécurité lors de la détection d’activités de base de données anormales.
  
   ![Configurer la détection des menaces](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explorer les activités de base de données anormales

Vous recevez une notification par e-mail quand des activités anormales sont détectées sur la base de données. L’e-mail contient des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur, le nom de l’application et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées pour examiner et atténuer la menace potentielle pesant sur la base de données.
 
1. Cliquez sur le lien **Afficher les alertes récentes** figurant dans l’e-mail pour ouvrir le portail Azure et accéder à la page d’alertes d’Azure Security Center, qui fournit une vue d’ensemble des menaces actives détectées sur la base de données SQL.
    
    ![Rapports d’activités anormales](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Affichez les menaces actives :

    ![Menaces actives](./media/howto-database-threat-protection-portal/active-threats.png)

2. Cliquez sur une alerte spécifique pour obtenir des détails supplémentaires et des actions permettant d’examiner cette menace et d’atténuer les menaces futures.
    
    ![Alerte spécifique](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explorer les alertes de détection de menace

SQL Database Threat Detection intègre ses alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/). Des vignettes de détection des menaces SQL dans les panneaux de base de données et SQL ATP au sein du portail Azure effectue le suivi de l’état des menaces actives.

Cliquez sur **Threat detection alert** (Alerte de détection des menaces) pour ouvrir la page d’alertes d’Azure Security Center et obtenir une vue d’ensemble des menaces SQL actives détectées sur la base de données.

   ![Alerte de détection des menaces](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Pour plus d’informations sur les prix, consultez la [page Tarification d’Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/).  
