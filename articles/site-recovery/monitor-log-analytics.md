---
title: Superviser Azure Site Recovery avec les journaux Azure Monitor (Log Analytics) | Microsoft Docs
description: Découvrez comment superviser Azure Site Recovery avec les journaux Azure Monitor (Log Analytics).
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: raynew
ms.openlocfilehash: 889fa3bee17aa3b0300431b058332c5ec10d9faf
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331934"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Superviser Site Recovery avec les journaux Azure Monitor

Cet article explique comment superviser des machines répliquées par Azure [Site Recovery](site-recovery-overview.md) à l’aide des [journaux Azure Monitor](../azure-monitor/platform/data-platform-logs.md) et de [Log Analytics](../azure-monitor/log-query/log-query-overview.md).

Les journaux Azure Monitor constituent une plateforme permettant de collecter les données des journaux d’activité et des journaux de diagnostic, ainsi que d’autres données de supervision. Dans les journaux Azure Monitor, vous utilisez Log Analytics pour écrire et tester des requêtes de journal, ainsi que pour analyser interactivement les données de journal. Vous pouvez visualiser et interroger les résultats du journal, et configurer des alertes en fonction des données analysées.

Pour Site Recovery, les journaux Azure Monitor vous permettent d’effectuer les opérations suivantes :

- **Superviser l’intégrité et l’état de Site Recovery**. Par exemple, vous pouvez superviser l’intégrité de la réplication, l’état du test de basculement, les événements Site Recovery, les objectifs de point de récupération (RPO) pour les machines protégées et les taux de changement des disques ou des données.
- **Configurer des alertes pour Site Recovery**. Par exemple, vous pouvez configurer des alertes concernant l’intégrité de la machine, l’état du test de basculement ou l’état d’un travail Site Recovery.

L’utilisation des journaux Azure Monitor avec Site Recovery est prise en charge pour la réplication d’**Azure à Azure** et pour la réplication d’une **machine virtuelle VMware ou d’un serveur physique dans Azure**.

> [!NOTE]
> Les journaux de données d’évolution et les journaux de taux de chargement sont uniquement disponibles pour les machines virtuelles Azure répliquées dans une région Azure secondaire.

## <a name="before-you-start"></a>Avant de commencer

Voici ce dont vous avez besoin :

- Au moins une machine protégée dans un coffre Recovery Services
- Un espace de travail Log Analytics pour stocker les journaux Site Recovery. [Découvrez comment configurer un espace de travail](../azure-monitor/learn/quick-create-workspace.md)
- Des connaissances de base concernant l’écriture, l’exécution et l’analyse des requêtes de journal dans Log Analytics. [Plus d’informations](../azure-monitor/log-query/get-started-portal.md)

Avant de commencer, il est recommandé de consulter les [questions courantes concernant la supervision](monitoring-common-questions.md).

## <a name="configure-site-recovery-to-send-logs"></a>Configurer Site Recovery pour envoyer des journaux

1. Dans le coffre, cliquez sur **Paramètres de diagnostic** > **Ajouter un paramètre de diagnostic**.

    ![Sélectionner la journalisation des diagnostics](./media/monitoring-log-analytics/add-diagnostic.png)

2. Dans **Paramètres de diagnostic**, spécifiez un nom, puis cochez la case **Envoyer à Log Analytics**.
3. Sélectionnez l’abonnement Journaux Azure Monitor et l’espace de travail Log Analytics.
4. Sélectionnez **Diagnostics Azure** dans le bouton bascule.
5. Dans la liste des journaux, sélectionnez tous les journaux ayant le préfixe **AzureSiteRecovery**. Cliquez ensuite sur **OK**.

    ![Sélectionner un espace de travail](./media/monitoring-log-analytics/select-workspace.png)

Les journaux Site Recovery commencent à être alimentés dans une table (**AzureDiagnostics**) de l’espace de travail sélectionné.


## <a name="query-the-logs---examples"></a>Interroger les journaux - Exemples

Vous pouvez récupérer les données des journaux à l’aide de requêtes de journal écrites à l’aide du [langage de requête Kusto](../azure-monitor/log-query/get-started-queries.md). Cette section fournit quelques exemples de requêtes courantes que vous pouvez utiliser pour la supervision Site Recovery.

> [!NOTE]
> Certains de ces exemples utilisent **replicationProviderName_s** avec une valeur de **A2A**. Cela permet de récupérer les machines virtuelles Azure qui ont été répliquées dans une région Azure secondaire à l’aide de Site Recovery. Dans ces exemples, vous pouvez remplacer **A2A** par **InMageAzureV2** si vous souhaitez récupérer des machines virtuelles VMware locales ou des serveurs physiques ayant été répliqués dans Azure à l’aide de Site Recovery.


### <a name="query-replication-health"></a>Interroger l’intégrité de la réplication

Cette requête trace un graphique à secteurs représentant l’état d’intégrité de la réplication pour toutes les machines virtuelles Azure protégées. L’intégrité peut être associée à trois états : Normal, Avertissement ou Critique.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Interroger la version du service Mobilité

Cette requête trace un graphique à secteurs représentant les machines virtuelles Azure répliquées avec Site Recovery. Celles-ci sont regroupées en fonction de la version de l’agent Mobilité qu’elles exécutent.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Interroger la durée des RPO

Cette requête trace un graphique à barres représentant les machines virtuelles Azure répliquées avec Site Recovery. Celles-ci sont regroupées selon la durée de leur objectif de point de récupération (RPO) : Inférieure à 15 minutes, entre 15 et 30 minutes, supérieure à 30 minutes.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Interroger les RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Interroger les travaux Azure Site Recovery

Cette requête récupère l’ensemble des travaux Site Recovery (pour tous les scénarios de reprise d’activité) déclenchés au cours des 72 dernières heures, ainsi que leur état d’achèvement.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Interroger les événements Site Recovery

Cette requête récupère l’ensemble des événements Site Recovery (pour tous les scénarios de reprise d’activité) signalés au cours des 72 dernières heures, ainsi que leur niveau de gravité. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Interroger l’état du test de basculement (graphique à secteurs)

Cette requête trace un graphique à secteurs représentant l’état du test de basculement des machines virtuelles Azure répliquées avec Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Interroger l’état du test de basculement (table)

Cette requête trace un tableau représentant l’état du test de basculement des machines virtuelles Azure répliquées avec Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Interroger les RPO des machines

Cette requête trace un graphique de tendance qui suit l’évolution du RPO d’une machine virtuelle Azure (ContosoVM123) au cours des 72 dernières heures.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Interroger les RPO des machines](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>Interroger le taux de changement des données (évolution) pour une machine virtuelle

> [!NOTE] 
> Ces informations d’évolution sont uniquement disponibles pour les machines virtuelles Azure qui sont répliquées dans une région Azure secondaire.

Cette requête trace un graphique de tendance qui suit l’évolution du taux de changement des données (octets écrits par seconde) et du taux de chargement des données pour une machine virtuelle Azure (ContosoVM123). 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Interroger le changement des données](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Interroger le récapitulatif d’une reprise d’activité (au sein d’Azure)

Cette requête trace un tableau récapitulatif des machines virtuelles Azure qui ont été répliquées dans une région Azure secondaire.  Il indique le nom de la machine virtuelle, l’état de la réplication et de la protection, le RPO, l’état du test de basculement, la version de l’agent Mobilité, les erreurs de réplication actives et l’emplacement source.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Interroger le récapitulatif d’une reprise d’activité (serveurs VMware ou physiques)

Cette requête trace un tableau récapitulatif des machines virtuelles VMware et des serveurs physiques répliqués dans Azure.  Il indique le nom de la machine, l’état de la réplication et de la protection, le RPO, l’état du test de basculement, la version de l’agent Mobilité, les erreurs de réplication actives et le serveur de processus impliqué.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Configurer des alertes - Exemples

Vous pouvez configurer des alertes Site Recovery en fonction des données Azure Monitor. [En savoir plus sur la configuration des alertes de journaux](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) 

> [!NOTE]
> Certains de ces exemples utilisent **replicationProviderName_s** avec une valeur de **A2A**. Cela permet de configurer des alertes pour les machines virtuelles Azure qui ont été répliquées dans une région Azure secondaire. Dans ces exemples, vous pouvez remplacer **A2A** par **InMageAzureV2** si vous souhaitez configurer des alertes pour des machines virtuelles VMware locales ou des serveurs physiques ayant été répliqués dans Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Plusieurs machines avec un état Critique

Configurez une alerte si plus de 20 machines virtuelles Azure répliquées passent à l’état Critique.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Pour l’alerte, définissez la **Valeur de seuil** sur 20.

### <a name="single-machine-in-a-critical-state"></a>Une seule machine avec un état Critique

Configurez une alerte si une machine virtuelle Azure répliquée passe à l’état Critique.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Pour l’alerte, définissez la **Valeur de seuil** sur 1.

### <a name="multiple-machines-exceed-rpo"></a>Plusieurs machines dépassent le RPO

Configurez une alerte si le RPO de plus de 20 machines virtuelles Azure dépasse 30 minutes.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Pour l’alerte, définissez la **Valeur de seuil** sur 20.

### <a name="single-machine-exceeds-rpo"></a>Une seule machine dépasse le RPO

Configurez une alerte si le RPO d’une machine virtuelle Azure dépasse 30 minutes.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Pour l’alerte, définissez la **Valeur de seuil** sur 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Le test de basculement de plusieurs machines remonte à plus de 90 jours

Configurez une alerte si le dernier test de basculement réussi remonte à plus de 90 jours pour plus de 20 machines virtuelles. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Pour l’alerte, définissez la **Valeur de seuil** sur 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Le test de basculement d’une seule machine remonte à plus de 90 jours

Configurez une alerte si le dernier test de basculement réussi remonte à plus de 90 jours pour une machine virtuelle.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Pour l’alerte, définissez la **Valeur de seuil** sur 1.

### <a name="site-recovery-job-fails"></a>Échec d’un travail Site Recovery

Configurez une alerte si un travail Site Recovery (dans le cas présent, un travail de reprotection) échoue lors du dernier jour, dans n’importe quel scénario Site Recovery. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Pour l’alerte, définissez la **Valeur de seuil** sur 1 et la **Période** sur 1 440 minutes, afin de vérifier les échecs qui ont eu lieu le dernier jour.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la supervision Site Recovery intégrée](site-recovery-monitor-and-troubleshoot.md)
