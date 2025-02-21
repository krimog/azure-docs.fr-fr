---
title: Configuration basée sur STIG à utiliser dans la configuration d’état – Azure Automation
description: Découvrez les configurations basées sur STIG pour la configuration d’état dans Azure Automation.
keywords: dsc,powershell,configuration,installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243528"
---
# <a name="configuration-based-on-stig"></a>Configuration basée sur STIG

> S’applique à : Windows PowerShell 5.1

Créer du contenu de configuration pour la première fois peut s’avérer délicat.
Dans bien des cas, l’objectif est d’automatiser la configuration des serveurs selon une « ligne de base » qui, de préférence, est conforme aux recommandations du secteur.

> [!NOTE]
> Cet article fait référence à une solution tenue à jour par la communauté Open Source.
> La prise en charge est disponible uniquement sous forme de collaboration GitHub, et non auprès de Microsoft.

## <a name="community-project-powerstig"></a>Projet communautaire : PowerSTIG

Un projet communautaire nommé [PowerSTIG](https://github.com/microsoft/powerstig) a pour but de résoudre ce problème en générant du contenu DSC basé sur des [informations publiques](https://public.cyber.mil/stigs/) sur STIG (Security Technical Implementation Guide).

Utiliser des lignes de base est plus compliqué qu’il n’y paraît.
Nombreuses sont les organisations à devoir [documenter les exceptions](https://github.com/microsoft/powerstig#powerstigdata) aux règles et à gérer ces données à la bonne échelle.
PowerSTIG résout le problème en fournissant des [ressources composites](https://github.com/microsoft/powerstig#powerstigdsc) destinées à gérer chaque partie de la configuration au lieu de tenter de gérer l’ensemble des paramètres dans un même fichier volumineux.

Une fois que les configurations ont été générées, vous pouvez utiliser les [scripts de configuration DSC](/powershell/scripting/dsc/configurations/configurations) pour générer des fichiers MOF et [charger ces mêmes fichiers MOF dans Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Inscrivez ensuite vos serveurs [localement](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) ou [dans Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) pour extraire les configurations.

Pour tester PowerSTIG, accédez à [PowerShell Gallery](http://www.powershellgallery.com) et téléchargez la solution ou cliquez sur « Project Site » pour afficher la [documentation](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Automation DSC](/powershell/dsc/overview/overview)
- [Ressources DSC](/powershell/dsc/resources/resources)
- [Configuration de Local Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
