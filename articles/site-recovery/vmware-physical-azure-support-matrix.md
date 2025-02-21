---
title: Matrice de prise en charge de la reprise d’activité des machines virtuelles VMware et serveurs physiques sur Azure avec Azure Site Recovery | Microsoft Docs
description: Résume la prise en charge de la récupération d’urgence des machines virtuelles et des serveurs physiques VMware sur Azure en utilisant Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 2cda2c4e640c48d712ea5ebc8534cf5a4e35da7a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620546"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice de prise en charge de la reprise d’activité des machines virtuelles VMware et serveurs physiques sur Azure

Cet article répertorie les composants et les paramètres pris en charge pour la récupération d’urgence de machines virtuelles et de serveurs physiques VMware sur Azure en utilisant [Azure Site Recovery](site-recovery-overview.md).

- [Apprenez-en davantage](vmware-azure-architecture.md) sur l’architecture de récupération d’urgence de machine virtuelle/serveur physique VMware.
- Pour essayer la récupération d’urgence, suivez nos [didacticiels](tutorial-prepare-azure.md).

## <a name="deployment-scenarios"></a>Scénarios de déploiement

**Scénario** | **Détails**
--- | ---
Récupération d’urgence des machines virtuelles VMware | Réplication de machines virtuelles VMware locales dans Azure. Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Récupération d’urgence des serveurs physiques | Réplication de serveurs physiques Windows/Linux locaux sur Azure. Vous pouvez déployer ce scénario dans le portail Azure.

## <a name="on-premises-virtualization-servers"></a>Serveurs de virtualisation locaux

**Serveur** | **Configuration requise** | **Détails**
--- | --- | ---
Serveur vCenter | Version 6.7, 6.5, 6.0 ou 5.5 | Nous vous recommandons d’utiliser un serveur vCenter dans votre déploiement de récupération d’urgence.
Hôtes vSphere | Version 6.7, 6.5, 6.0 ou 5.5 | Nous vous recommandons d’héberger les hôtes vSphere et les serveurs vCenter dans le même réseau que le serveur de traitement. Par défaut, le serveur de processus s’exécute sur le serveur de configuration. [Plus d’informations](vmware-physical-azure-config-process-server-overview.md)


## <a name="site-recovery-configuration-server"></a>Serveur de configuration Site Recovery

Le serveur de configuration est une machine locale qui exécute les composants de Site Recovery, comprenant le serveur de configuration, le serveur de traitement et le serveur cible maître.

- Pour les machines virtuelles VMware, vous définissez le serveur de configuration en téléchargeant un modèle OVF pour créer une machine virtuelle VMware.
- Pour les serveurs physiques, vous définissez l’ordinateur serveur de configuration manuellement.

**Composant** | **Configuration requise**
--- |---
Cœurs d’unité centrale | 8
RAM | 16 Go
Nombre de disques | 3 disques<br/><br/> Les disques comprennent le disque du système d’exploitation, le disque de cache du serveur de traitement et le lecteur de rétention pour la restauration automatique.
Espace disque libre | 600 Go d’espace pour le cache du serveur de processus.
Espace disque libre | 600 Go d’espace pour le lecteur de rétention.
Système d’exploitation  | Windows Server 2012 R2 ou Windows Server 2016 avec l’Expérience utilisateur <br/><br> Si vous envisagez d’utiliser le serveur cible maître intégré de cet appareil pour la restauration automatique, vérifiez que la version du système d’exploitation est identique ou supérieure à celle des éléments répliqués.|
Paramètres régionaux du système d’exploitation | Anglais (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Non nécessaire pour le serveur de configuration version [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) ou ultérieure. 
Rôles Windows Server | N’activez pas Active Directory Domain Services, Internet Information Services (IIS) ou Hyper-V 
Stratégies de groupe| - Empêcher l’accès à l’invite de commandes <br/> - Empêcher l’accès aux outils de modification du Registre <br/> - Logique de confiance pour les pièces jointes <br/> - Activer l’exécution des scripts <br/> - [En savoir plus](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assurez-vous d’effectuer les tâches suivantes :<br/><br/> - Vérifier l’absence d’un site web par défaut préexistant <br/> - Activer [l’authentification anonyme](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Activer le paramètre [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Vérifier qu’aucune application/aucun site web préexistants n’écoutent le port 443<br/>
Type de carte réseau | VMXNET3 (en cas de déploiement comme machine virtuelle VMware)
Type d’adresse IP | statique
Ports | 443 utilisé pour l’orchestration du canal de contrôle<br/>9443 utilisé pour le transport de données

## <a name="replicated-machines"></a>Machines répliquées

Site Recovery assure la réplication de toutes les charges de travail exécutées sur une machine prise en charge.

**Composant** | **Détails**
--- | ---
Paramètres de la machine | Les ordinateurs qui répliquent vers Azure doivent répondre aux [conditions requises par Azure](#azure-vm-requirements).
Charge de travail de machine | Site Recovery assure la réplication de toutes les charges de travail exécutées sur une machine prise en charge. [Plus d’informations](https://aka.ms/asr_workload)
Windows Server 2019 | Pris en charge à partir du [Correctif cumulatif 34](https://support.microsoft.com/help/4490016) (version 9.22 de Mobility Service).
Windows Server 2016 64 bits | Pris en charge pour Server Core, Server avec Expérience utilisateur.
Windows Server 2012 R2/Windows Server 2012 | Pris en charge.
Windows Server 2008 R2 avec SP1 et au-delà. | Pris en charge.<br/><br/> À partir de la version 9.30.x.x (version attendue à partir de novembre 2019) de l’agent Mobility Service, vous devez installer la [mise à jour de la pile de maintenance (SSU)](https://support.microsoft.com/help/4490628) et la [mise à jour SHA-2](https://support.microsoft.com/help/4474419) sur les ordinateurs exécutant Windows 2008 R2 avec SP1 ou une version ultérieure. SHA-1 n’est pas pris en charge à partir de septembre 2019, et si la signature de code SHA-2 n’est pas activée, l’extension de l’agent ne sera pas installée/mise à niveau comme prévu. En savoir plus sur la [mise à niveau et la configuration requise pour SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 avec SP2 ou version ultérieure (64 bits/32 bits) |  Pris en charge pour la migration uniquement. [Plus d’informations](migrate-tutorial-windows-server-2008.md)<br/><br/> À partir de la version 9.30.x.x (version attendue à partir de novembre 2019) de l’agent Mobility Service, vous devez installer la [mise à jour de la pile de maintenance (SSU)](https://support.microsoft.com/help/4493730) et la [mise à jour SHA-2](h https://support.microsoft.com/help/4474419) sur les ordinateurs Windows 2008 SP2. SHA-1 n’est pas pris en charge à partir de septembre 2019, et si la signature de code SHA-2 n’est pas activée, l’extension de l’agent ne sera pas installée/mise à niveau comme prévu. En savoir plus sur la [mise à niveau et la configuration requise pour SHA-2](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Pris en charge.
Windows 7 avec SP1 64 bits | Pris en charge à partir du [Correctif cumulatif 36](https://support.microsoft.com/help/4503156) (version 9.22 de Mobility Service). </br></br> À partir de la version 9.30.x.x (version attendue à partir de novembre 2019) de l’agent Mobility Service, vous devez installer la [mise à jour de la pile de maintenance (SSU)](https://support.microsoft.com/help/4490628) et la [mise à jour SHA-2](https://support.microsoft.com/help/4474419) sur les ordinateurs Windows 7 SP1.  SHA-1 n’est pas pris en charge à partir de septembre 2019, et si la signature de code SHA-2 n’est pas activée, l’extension de l’agent ne sera pas installée/mise à niveau comme prévu. En savoir plus sur la [mise à niveau et la configuration requise pour SHA-2](https://aka.ms/SHA-2KB).
Linux | Seul le système 64 bits est pris en charge. Le système 32 bits n’est pas pris en charge.<br/><br/>Les [composants Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) doivent être installés sur chaque serveur Linux. Il sont nécessaires pour démarrer le serveur dans Azure après le test de basculement et le basculement. Si les [composants](https://www.microsoft.com/download/details.aspx?id=55106) LIS sont manquants, veillez à les installer avant d’activer la réplication pour les machines à démarrer dans Azure. <br/><br/> Site Recovery orchestre le basculement pour exécuter des serveurs Linux dans Azure. Toutefois, les fournisseurs Linux peuvent limiter la prise en charge aux versions de distribution qui n’ont pas atteint leur fin de vie.<br/><br/> Dans les distributions Linux, seuls les noyaux de stockage qui font partie de la version/mise à jour mineure de distribution sont pris en charge.<br/><br/> La mise à niveau des machines protégées sur des versions de distribution majeures Linux n’est pas prise en charge. Pour effectuer la mettre à niveau, désactivez la réplication, mettez à niveau le système d’exploitation, puis réactivez la réplication.<br/><br/> [Apprenez-en davantage](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sur la prise en charge des technologies Linux et open source dans Azure.
Red Hat Enterprise Linux | 5.2 à 5.11</b><br/> 6.1 à 6.10</b><br/> 7.0 à 7.7. </br>La version 7.7 est prise en charge à partir de la [version 9.29 de l’agent de mobilité](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)<br/> <br/> Les [composants Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) ne sont pas préinstallés sur les serveurs exécutant Red Hat Enterprise Linux 5.2 à 5.11 et 6.1-6.10. Veillez à installer les [composants](https://www.microsoft.com/download/details.aspx?id=55106) avant d’activer la réplication pour les machines à démarrer dans Azure.
Linux : CentOS | 5.2 à 5.11</b><br/> 6.1 à 6.10</b><br/> 7.0 à 7.6<br/> <br/> Les [composants Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) ne sont pas préinstallés sur les serveurs exécutant CentOS 5.2 à 5.11 et 6.1 à 6.10. Veillez à installer les [composants](https://www.microsoft.com/download/details.aspx?id=55106) avant d’activer la réplication pour les machines à démarrer dans Azure.
Ubuntu | Serveur LTS Ubuntu 14.04[ (voir les versions du noyau prises en charge)](#ubuntu-kernel-versions)<br/><br/>Serveur LTS Ubuntu 16.04[ (voir les versions du noyau prises en charge)](#ubuntu-kernel-versions) </br> Serveur LTS Ubuntu 18.04 [(voir les versions du noyau prises en charge)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8[ (voir les versions du noyau prises en charge)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [ (voir les versions du noyau prises en charge)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> La mise à niveau de machines répliquées de SUSE Linux Enterprise Server 11 SP3 vers SP4 n’est pas pris en charge. Pour effectuer la mise à niveau, désactivez la réplication, puis réactiver-la après la mise à niveau.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7<br/><br/> Exécutant le noyau compatible Red Hat ou le noyau Unbreakable Enterprise Kernel Release 3, 4 et 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Version du noyau Ubuntu

**Version prise en charge** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | [9.28][9.28 UR]| 3.13.0-24-generic à 3.13.0-170-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-148-generic,<br/>4.15.0-1023-azure à 4.15.0-1045-azure |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic à 3.13.0-170-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-148-generic,<br/>4.15.0-1023-azure à 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic à 3.13.0-170-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-148-generic,<br/>4.15.0-1023-azure à 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic à 3.13.0-169-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-146-generic,<br/>4.15.0-1023-azure à 4.15.0-1042-azure |
|||
LTS 16.04 | [9.28][9.28 UR] | 4.4.0-21-generic à 4.4.0-159-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-58-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1055-azure|
LTS 16.04 | [9.27][9.27 UR] | 4.4.0-21-generic à 4.4.0-154-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-54-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1050-azure|
LTS 16.04 | [9.26][9.26 UR] | 4.4.0-21-generic à 4.4.0-148-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-50-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1045-azure|
LTS 16.04 | [9.25][9.25 UR] | 4.4.0-21-generic à 4.4.0-146-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-48-generic,<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1042-azure|
LTS 16.04 | [9.24][9.24 UR] | 4.4.0-21-generic à 4.4.0-143-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-46-generic,<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1040-azure|
|||
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-générique à 4.15.0-62-générique </br> 4.18.0-13-générique à 4.18.0-25-générique </br> 5.0.0-15-générique à 5.0.0-27-générique </br> 4.15.0-1009-azure à 4.15.0-1037-azure </br> 4.18.0-1006-azure à 4.18.0-1025-azure </br> 5.0.0-1012-azure à 5.0.0-1018-azure


### <a name="debian-kernel-versions"></a>Versions du noyau Debian


**Version prise en charge** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
Debian 7 | [9.25][9.25 UR],[9.26][9.26 UR], [9.27][9.27 UR], [9.28][9.28 UR]| 3.2.0-4-amd64 à 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.28][9.28 UR] | 3.16.0-4-amd64 à 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.27][9.27 UR] | 3.16.0-4-amd64 à 3.16.0-9-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.25][9.25 UR], [9.26][9.26 UR] | 3.16.0-4-amd64 à 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Versions du noyau prises en charge de SUSE Linux Enterprise Server 12

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.117-default</br></br>SP3 4.4.73-5-default à 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-azure à 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure à 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default à 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure à 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure à 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default à 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure à 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure à 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default à 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure à 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure à 4.12.14-6.9-azure |

## <a name="linux-file-systemsguest-storage"></a>Stockage invité/système de fichiers Linux

**Composant** | **Pris en charge**
--- | ---
Systèmes de fichiers | ext3, ext4, XFS
Gestionnaire de volume | - LVM est pris en charge.<br/> - /boot sur LVM est pris en charge à partir du [Correctif cumulatif 31](https://support.microsoft.com/help/4478871/) (version 9.20 de Mobility Service). Il n’est pas pris en charge dans les versions antérieures de Mobility Service.<br/> - Les disques de système d’exploitation multiples ne sont pas pris en charge.
Dispositif de stockage paravirtualisé | Les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.
Unités de bloc d’entrée et de sortie en file d’attente | Non pris en charge.
Serveurs physiques avec le contrôleur de stockage HP CCISS | Non pris en charge.
Convention de nommage pour les appareils/points de montage | Le nom de l’appareil ou le nom du point de montage doit être unique.<br/> Vérifiez qu’aucun appareil ou point de montage ne possède un nom sensible à la casse. Par exemple, l’attribution des noms *appareil1* et *Appareil1* à deux appareils de la même machine virtuelle n’est pas prise en charge.
Répertoires | Si vous exécutez une version de Mobility Service antérieure à la version 9.20 (publiée dans le [Correctif cumulatif 31](https://support.microsoft.com/help/4478871/)), les restrictions suivantes s’appliquent :<br/><br/> - Les répertoires suivants (s’ils sont configurés en tant que partitions/systèmes de fichiers séparés) doivent se trouver sur le même disque de système d’exploitation, sur le serveur source : /(root), /boot, /usr, /usr/local, /var, /etc.</br> - Le répertoire /boot doit se trouver sur une partition de disque et ne doit pas être un volume LVM.<br/><br/> À partir de la version 9.20, ces restrictions ne s’appliquent pas. 
Répertoire de démarrage | - Les disques de démarrage ne doivent pas être au format de partition GPT. Il s’agit d’une limitation d’architecture Azure. Les disques GPT sont pris en charge en tant que disques de données.<br/><br/> Plusieurs disques de démarrage ne sont pas pris en charge sur une machine virtuelle<br/><br/> - /boot sur un volume LVM s’étendant sur plusieurs disques n’est pas pris en charge.<br/> - Une machine sans un disque de démarrage ne peuvent pas être répliquée.
Espace libre requis| 2 Go sur la partition/root <br/><br/> 250 Mo dans le dossier d’installation
XFSv5 | Les fonctionnalités XFSv5 sur des systèmes de fichiers XFS, tels que les sommes de contrôle de métadonnées, sont prises en charge (à partir de la version 9.10 de Mobility Service).<br/> Utilisez l’utilitaire xfs_info pour vérifier le superbloc XFS pour la partition. Si `ftype` est défini sur 1, les fonctionnalités XFSv5 sont utilisées.
BTRFS | BTRFS est pris en charge à partir du [Correctif cumulatif 34](https://support.microsoft.com/help/4490016) (version 9.22 de Mobility Service). BTRFS n’est pas pris en charge si :<br/><br/> - Le sous-volume du système de fichiers BTRFS est modifié après l’activation de la protection.</br> - Le système de fichiers BTRFS s’étend sur plusieurs disques.</br> - Le système de fichiers BTRFS prend en charge RAID.

## <a name="vmdisk-management"></a>Gestion des machines virtuelles/disques

**Action** | **Détails**
--- | ---
Redimensionner le disque sur la machine virtuelle répliquée | Pris en charge.
Ajouter un disque à la machine virtuelle répliquée | Non pris en charge.<br/> Désactivez la réplication pour la machine virtuelle, ajoutez le disque, puis réactivez la réplication.

## <a name="network"></a>Réseau

**Composant** | **Pris en charge**
--- | ---
Association de cartes réseau de réseau hôte | Pris en charge pour les machines virtuelles VMware <br/><br/>Non pris en charge pour la réplication des machines physiques
Réseau hôte VLAN | Oui.
Réseau hôte IPv4 | Oui.
Réseau hôte IPv6 | Non.
Association de cartes de réseau invité/serveur | Non.
Réseau invité/serveur IPv4 | Oui.
Réseau invité/serveur IPv6 | Non.
Adresse IP statique du réseau invité/serveur (Windows) | Oui.
Adresse IP statique du réseau invité/serveur (Linux) | Oui. <br/><br/>Les machines virtuelles sont configurées pour utiliser le protocole DHCP lors de la restauration automatique.
Plusieurs cartes réseau invité/serveur | Oui.


## <a name="azure-vm-network-after-failover"></a>Réseau de machines virtuelles Azure (après le basculement)

**Composant** | **Pris en charge**
--- | ---
Azure ExpressRoute | OUI
ILB | OUI
ELB | OUI
Azure Traffic Manager | OUI
Plusieurs cartes réseau | OUI
Adresses IP réservées | OUI
IPv4 | OUI
Conserver l’adresse IP source | OUI
Points de terminaison de service de réseau virtuel Azure<br/> | OUI
Mise en réseau accélérée | Non

## <a name="storage"></a>Stockage
**Composant** | **Pris en charge**
--- | ---
Disque dynamique | Le disque du système d’exploitation doit être un disque de base. <br/><br/>Les disques de données peuvent être des disques dynamiques
Configuration des disques Docker | Non
Hôte NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques
Hôte SAN (iSCSI/FC) | OUI
vSAN hôte | Oui pour VMware<br/><br/> N/A pour les serveurs physiques
Multipath hôte (MPIO) | Oui, testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pour CLARiiON
Volumes virtuels hôtes (VVols) | Oui pour VMware<br/><br/> N/A pour les serveurs physiques
VMDK invité/serveur | OUI
Disque de cluster partagé invité/serveur | Non
Disque chiffré invité/serveur | Non
NFS invité/serveur | Non
iSCSI invité/serveur | Pour la migration : Oui<br/>Pour la récupération d’urgence : Non. iSCSI est automatiquement restauré en tant que disque attaché à la machine virtuelle
SMB 3.0 invité/serveur | Non
RDM invité/serveur | OUI<br/><br/> N/A pour les serveurs physiques
Disque invité/serveur > 1 To | Oui, le disque doit être d’une taille supérieure à 1024 Mo<br/><br/>Jusqu'à 8 192 Go lors de la réplication sur des disques managés (à partir de la version 9.26)<br></br> Jusqu'à 4 095 Go lors de la réplication vers des comptes de stockage
Disque invité/serveur avec une taille de secteur logique de 4 Ko et une taille de secteur physique de 4 K | Non
Disque invité/serveur avec une taille de secteur logique de 4 K et une taille de secteur physique de 512 octets | Non
Volume invité/serveur avec disque à bandes > 4 To <br/><br/>Gestion des volumes logiques (LVM)| OUI
Invité/serveur - Espaces de stockage | Non
Ajout/retrait à chaud de disque d’Invité/de serveur | Non
Invité/serveur - Exclure le disque | OUI
Multipath invité/serveur (MPIO) | Non
Partitions GPT invité/serveur | Cinq partitions sont prises en charge à partir du [Correctif cumulatif 37](https://support.microsoft.com/help/4508614/) (version 9.25 de Mobility Service). Auparavant, elles étaient au nombre de quatre.
ReFS | Resilient File System est pris en charge avec le service Mobility version 9.23 ou ultérieure
Démarrage EFI/UEFI invité/serveur | - Pris en charge lorsque vous exécutez Mobility Service version 9.13 ou ultérieure.<br/> - Pris en charge lors de la migration de machines virtuelles ou serveurs physiques VMware exécutant Windows Server 2012 ou version ultérieure vers Azure.<br/> - Vous ne pouvez répliquer des machines virtuelles que pour la migration. La restauration automatique en local n’est pas pris en charge.<br/> - Seul le système NTFS est pris en charge <br/> - Le type de démarrage UEFI sécurisé n’est pas pris en charge. <br/> - La taille de secteur de disque doit être de 512 octets par secteur physique.

## <a name="replication-channels"></a>Canaux de réplication

|**Type de réplication**   |**Pris en charge**  |
|---------|---------|
|Transferts de données déchargées (ODX)    |       Non  |
|Amorçage hors connexion        |   Non      |
| Azure Data Box | Non

## <a name="azure-storage"></a>Stockage Azure

**Composant** | **Pris en charge**
--- | ---
Stockage localement redondant | OUI
Stockage géo-redondant | OUI
Stockage géo-redondant avec accès en lecture | OUI
Stockage froid | Non
Stockage chaud| Non
Objets blob de blocs | Non
Chiffrement au repos (SSE)| OUI
Stockage Premium | OUI
Service Import/Export | Non
Pare-feu de Stockage Azure pour les réseaux virtuels | Oui.<br/> Configurés sur le compte de stockage de cache/stockage cible (utilisé pour stocker les données de réplication).
Comptes de stockage v2 à usage général (niveaux chaud et froid) | Oui (les coûts de transaction sont sensiblement plus élevées pour la version V2 que pour la version V1)

## <a name="azure-compute"></a>Calcul Azure

**Fonctionnalité** | **Pris en charge**
--- | ---
Groupes à haute disponibilité | OUI
Zones de disponibilité | Non
CONCENTRATEUR | OUI
Disques managés | OUI

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les machines virtuelles locales répliquées sur Azure doivent respecter les exigences des machines virtuelles Azure décrites dans ce tableau. Lorsque Site Recovery vérifie la configuration requise pour la réplication, la vérification échoue si certaines conditions requises ne sont pas remplies.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Système d’exploitation invité | Vérifiez les [systèmes d’exploitation pris en charge](#replicated-machines) pour les machines répliquées. | La vérification est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 64 bits. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go. | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 64 ou moins. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille de disque de données | Jusqu'à 8 192 Go lors de la réplication sur un disque managé (à partir de la version 9.26)<br></br>Jusqu'à 4 095 Go lors de la réplication vers un compte de stockage| La vérification est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
BitLocker | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine. |
nom de la machine virtuelle | De 1 et 63 caractères.<br/><br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. |  Mettez à jour la valeur dans les propriétés de machine de Site Recovery.

## <a name="resource-group-limits"></a>Limites de groupe de ressources

Pour plus d’informations sur le nombre de machines virtuelles pouvant être protégées sous un groupe de ressources unique, reportez-vous à l’article sur les [limites et quotas d’abonnement](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits).

## <a name="churn-limits"></a>Limites d’activité

Le tableau suivant présente les limites d’Azure Site Recovery. 
- Ces limites sont basées sur nos tests, mais ne couvrent pas toutes les combinaisons d’E/S d’application possibles.
- Les résultats réels varient en fonction de la combinaison d’E/S de votre application.
- Pour des résultats optimaux, nous recommandons d’exécuter l’[outil Planificateur de déploiement](site-recovery-deployment-planner.md) et d’effectuer des tests d’application étendus, notamment des tests de basculement pour obtenir une image réelle des performances de votre application.

**Cible de réplication** | **Taille d’E/S moyenne de disque source** |**Activité des données moyenne de disque source** | **Total de l’activité des données de disque source par jour**
---|---|---|---
Stockage Standard | 8 Ko | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 8 Ko  | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 16 Ko | 4 Mo/s |  336 Go par disque
Disque Premium P10 ou P15 | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 16 Ko ou plus |20 Mo/s | 1 684 Go par disque


**Activité de données sources** | **Limite maximale**
---|---
Activité des données de pointe sur tous les disques d’une machine virtuelle | 54 Mo/s
Activité des données maximale par jour prise en charge par un serveur de processus | 2 To

- Il s’agit de moyennes en partant sur un chevauchement d’E/S de 30 pour cent.
- Site Recovery est capable de gérer un débit plus élevé en fonction du ratio de chevauchement, de tailles d’écriture plus grandes et du comportement d’E/S des charges de travail réelles.
- Ces valeurs supposent un backlog typique d’environ cinq minutes. Autrement dit, une fois que les données sont chargées, elles sont traitées, et un point de récupération est créé dans un délai de cinq minutes.

## <a name="vault-tasks"></a>Tâches de coffre

**Action** | **Pris en charge**
--- | ---
Déplacer le coffre entre plusieurs groupes de ressources | Non
Déplacer le coffre au sein des abonnements et entre ceux-ci | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources | Non
Déplacer le stockage, le réseau et les machines virtuelles Azure au sein des abonnements et entre ceux-ci | Non


## <a name="obtain-latest-components"></a>Obtenir les composants les plus récents

**Nom** | **Description** | **Détails**
--- | --- | ---
Serveur de configuration | Installé en local.<br/> Coordonne les communications entre les serveurs ou machines physiques VMware locaux et Azure. | - [Apprenez-en davantage sur](vmware-physical-azure-config-process-server-overview.md) le serveur de configuration.<br/> - [Apprenez-en davantage sur](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) la mise à niveau vers la dernière version.<br/> - [Apprenez-en davantage sur](vmware-azure-deploy-configuration-server.md) l’installation du serveur de configuration. 
Serveur de traitement | Installé par défaut sur le serveur de configuration.<br/> Reçoit les données de réplication, les optimise avec une mise en cache, une compression et un chiffrement, puis les envoie à Azure.<br/> À mesure que croît votre déploiement, vous pouvez ajouter des serveurs de traitement afin de gérer de plus grands volumes de trafic de réplication. | - [Apprenez-en davantage sur](vmware-physical-azure-config-process-server-overview.md) le serveur de processus.<br/> - [Apprenez-en davantage sur](vmware-azure-manage-process-server.md#upgrade-a-process-server) la mise à niveau vers la dernière version.<br/> - [Apprenez-en davantage sur](vmware-physical-large-deployment.md#set-up-a-process-server) la configuration de serveurs de processus scale-out.
Service Mobilité | Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer.<br/> Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure.| - [Apprenez-en davantage sur](vmware-physical-mobility-service-overview.md) Mobility Service.<br/> - [Apprenez-en davantage sur](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) la mise à niveau vers la dernière version.<br/> 



## <a name="next-steps"></a>Étapes suivantes
[Découvrez comment](tutorial-prepare-azure.md) préparer Azure à la récupération d’urgence de machines virtuelles VMware.

[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
