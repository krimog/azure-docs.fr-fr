---
title: Exemples Azure PowerShell - Service Fabric | Microsoft Docs
description: Exemples Azure PowerShell - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 73e88e692b68d73c90176a6f5b8fce06bdf8b8c7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035790"
---
# <a name="azure-powershell-samples"></a>Exemples Azure PowerShell

Le tableau suivant contient des liens vers des exemples de scripts PowerShell qui créent et gèrent des services, applications et clusters Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Créer un cluster** ||
| [Créer un cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Crée un cluster Azure Service Fabric. |
| **Gérer le cluster, les nœuds et l’infrastructure** ||
| [Ajouter un certificat d’application](./scripts/service-fabric-powershell-add-application-certificate.md)| Ajoute un certificat X.509 d’application à tous les nœuds d’un cluster. |
| [Mettre à jour la plage de ports RDP sur les machines virtuelles du cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Change la plage de ports RDP sur les machines virtuelles de nœud de cluster dans un cluster déployé.|
| [Mettre à jour le nom d’utilisateur et le mot de passe administrateur pour les machines virtuelles de nœud de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Met à jour le nom d’utilisateur et le mot de passe administrateur pour les machines virtuelles de nœud de cluster. |
| [Ouvrir un port dans l’équilibreur de charge](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Ouvrez un port d’application dans l’équilibreur de charge Azure pour autoriser le trafic entrant sur un port spécifique. |
| [Créer une règle de groupe de sécurité réseau de trafic entrant](./scripts/service-fabric-powershell-add-nsg-rule.md) | Créez une règle de groupe de sécurité réseau de trafic entrant pour autoriser le trafic entrant sur le cluster sur un port spécifique. |
| **Gérer des applications** ||
| [Déployer une application](./scripts/service-fabric-powershell-deploy-application.md)| Déployez une application sur un cluster.|
| [Mettre à niveau une application](./scripts/service-fabric-powershell-upgrade-application.md)| Mettez à niveau une application.|
| [Supprimer une application](./scripts/service-fabric-powershell-remove-application.md)| Supprimez une application d’un cluster.|
