---
title: 'Azure Site Recovery : Forum Aux Questions (FAQ) | Microsoft Docs'
description: Cet article traite des questions fréquemment posées sur Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: a9c7aa2be945e4fbaa65bdd2a145d576422c5539
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491771"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery : Forum Aux Questions (FAQ)
Cet article résume les questions fréquentes sur Azure Site Recovery.</br>
Pour des requêtes spécifiques sur différents scénarios ASR, consultez les questions fréquentes propres au scénario en question.<br>

- [Reprise d’activité après sinistre d’une machine virtuelle Azure vers Azure](azure-to-azure-common-questions.md)
- [Reprise d’activité après sinistre d’une machine virtuelle VMware vers Azure](vmware-azure-common-questions.md)
- [Reprise d’activité après sinistre d’une machine virtuelle Hyper-V vers Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Généralités

### <a name="what-does-site-recovery-do"></a>À quoi sert Site Recovery ?
Site Recovery contribue à mettre en œuvre la stratégie de continuité d’activité et de récupération d’urgence (BCDR) de votre entreprise en coordonnant et en automatisant la réplication de machines virtuelles Azure entre des régions, de machines virtuelles et serveurs physiques locaux sur Azure et de machines locales sur un centre de données secondaire. [Plus d’informations](site-recovery-overview.md)

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Puis-je protéger une machine virtuelle dotée d’un disque Docker ?

Non, ce scénario n’est pas pris en charge.

## <a name="service-providers"></a>Fournisseurs de services

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Je suis un fournisseur de services. Site Recovery fonctionne-t-il pour les modèles d’infrastructure dédiée ou partagée ?
Oui, Site Recovery prend en charge les modèles d’infrastructure dédiée et partagée.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pour un fournisseur de services, l’identité de mon locataire est-elle communiquée au service Site Recovery ?
Non. L’identité du locataire reste anonyme. Vos locataires n’ont pas besoin d’accéder au portail Site Recovery. Seul l’administrateur du fournisseur de services interagit avec le portail.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Les données d’application de mes locataires sont-elles diffusées sur Azure ?
Lors de la réplication entre des sites appartenant à un fournisseur de services, les données d’application n’accèdent jamais à Azure. Les données sont chiffrées en transit et répliquées directement entre les sites du fournisseur de services.

Si vous répliquez vers Azure, les données d’application sont envoyées vers le stockage Azure, mais pas vers le service Site Recovery. Les données sont chiffrées en transit et restent chiffrées dans Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Mes clients recevront-ils une facture pour les services Azure ?
Non. La relation de facturation d’Azure concerne directement le fournisseur de services. Les fournisseurs de services sont responsables de la création de factures spécifiques pour leurs locataires.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Si je réplique vers Azure, faut-il exécuter à tout moment des machines virtuelles dans ce dernier ?
Non, les données sont répliquées vers le stockage Azure de votre abonnement. Lorsque vous effectuez un basculement de test (test de récupération d’urgence) ou un basculement réel, Site Recovery crée automatiquement les machines virtuelles dans votre abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Assurez-vous l’isolation au niveau des clients lors de la réplication vers Azure ?
Oui.

### <a name="what-platforms-do-you-currently-support"></a>Quelles plates-formes prenez-vous en charge, actuellement ?
Nous prenons en charge Azure Pack et le système Cloud Platform, ainsi que les déploiements basés sur System Center (2012 et versions supérieures). [En savoir plus](https://technet.microsoft.com/library/dn850370.aspx) sur l’intégration d’Azure Pack et de Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Prenez-vous en charge les déploiements uniques de serveurs VMM et Azure Pack ?
Oui, vous pouvez répliquer des machines virtuelles Hyper-V vers Azure, ou entre des sites du fournisseur de service.  Notez que si vous répliquez entre des sites du fournisseur de services, l’intégration de runbooks Azure n’est pas disponible.

## <a name="pricing"></a>Tarifs

### <a name="where-can-i-find-pricing-information"></a>Où puis-je obtenir des informations sur la tarification ?
Consultez les [détails de la tarification Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Comment puis-je calculer les frais approximatifs d'utilisation de Site Recovery ?

Vous pouvez utiliser la [calculatrice de prix](https://aka.ms/asr_pricing_calculator) pour estimer les coûts d’utilisation de Site Recovery.

Pour obtenir une estimation détaillée des coûts, exécutez l’outil Planificateur de déploiement pour [VMware](https://aka.ms/siterecovery_deployment_planner) ou [Hyper-V](https://aka.ms/asr-deployment-planner) et utilisez le [rapport d’estimation des coûts](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Les disques managés sont à présent utilisés pour répliquer des machines virtuelles VMware et des serveurs physiques. Des frais supplémentaires sont-ils facturés pour le compte de stockage de cache avec des disques managés ?

Non, le cache n’entraîne pas la facturation de frais. Lorsque vous effectuez une réplication vers le compte de stockage standard, ce stockage de cache fait partie du même compte de stockage cible.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>J’utilise Azure Site Recovery depuis plus d’un mois. Puis-je tout de même bénéficier des 31 premiers jours gratuits pour chaque instance protégée ?

Oui. Chaque instance protégée n’engendre aucun frais Azure Site Recovery pendant les 31 premiers jours. Par exemple, si vous protégez 10 instances depuis 6 mois et que vous connectez une 11e instance à Azure Site Recovery, aucuns frais ne sont facturés pour cette 11e instance pendant les 31 premiers jours. Des frais Azure Site Recovery continuent d’être facturés pour les 10 premières instances, car celles-ci sont protégées depuis plus de 31 jours.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Pendant les 31 premiers jours, d'autres frais Azure sont-ils facturés ?

Oui, bien qu’Azure Site Recovery soit gratuit pendant les 31 premiers jours d’une instance protégée, des frais peuvent s’appliquer pour Stockage Azure, les transactions de stockage et le transfert de données. Des frais de calcul Azure peuvent également être facturés pour une machine virtuelle récupérée.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>L’exécution de simulations de reprise d’activité après sinistre/tests de basculement génère-t-elle des frais ?

Les simulations de reprise d'activité n'entraînent pas de coût distinct. Des frais de calcul interviennent une fois la machine virtuelle créée, après le test de basculement.



## <a name="security"></a>Sécurité

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Les données de réplication sont-elles envoyées vers le service Site Recovery ?
Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les éléments exécutés sur vos machines virtuelles ou serveurs physiques.
Les données de réplication sont échangées entre des hôtes Hyper-V, des hyperviseurs VMware ou des serveurs physiques locaux et le stockage Azure ou votre site secondaire. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.  

Le logiciel Site Recovery est certifié conforme aux normes ISO 27001:2013, 27018, HIPAA et DPA. Il fait actuellement l’objet d’une évaluation de conformité aux exigences SOC2 et JAB FedRAMP.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Pour des raisons de conformité, même nos métadonnées locales doivent rester dans la même région géographique. Site Recovery peut-il nous aider ?
Oui. Quand vous créez un coffre Site Recovery dans une région, nous vérifions que toutes les métadonnées dont nous avons besoin pour activer et coordonner la réplication et le basculement restent au sein de cette région.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery chiffre-t-il la réplication ?
Pour la réplication de machines virtuelles et de serveurs physiques entre des sites locaux, le chiffrement en transit est pris en charge. Pour la réplication de machines virtuelles et de serveurs physiques vers Azure, le chiffrement en transit et le [chiffrement au repos (dans Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) sont tous deux pris en charge.




## <a name="disaster-recovery"></a>Récupération d'urgence

### <a name="what-can-site-recovery-protect"></a>Que peut protéger Site Recovery ?
* **Machines virtuelles Azure** : Site Recovery peut répliquer n’importe quelle charge de travail exécutée sur une machine virtuelle Azure prise en charge.
* **Machines virtuelles Hyper-V** : Site Recovery peut protéger toute charge de travail en cours d’exécution sur une machine virtuelle Hyper-V.
* **Serveurs physiques** : Site Recovery peut protéger les serveurs physiques exécutant Windows ou Linux.
* **Machines virtuelles VMware** : Site Recovery peut protéger toute charge de travail en cours d’exécution dans une machine virtuelle VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quelles charges de travail puis-je protéger avec Site Recovery ?
Vous pouvez utiliser Site Recovery pour protéger la plupart des charges de travail en cours d’exécution sur une machine virtuelle ou un serveur physique pris(e) en charge. Site Recovery assure la prise en charge de la réplication compatible avec les applications afin qu’elles puissent être récupérées dans un état intelligent. Site Recovery s’intègre aux applications Microsoft, notamment à SharePoint, Exchange, Dynamics, SQL Server et Active Directory, et fonctionne en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat. [En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Puis-je gérer la récupération d’urgence pour mes succursales avec Site Recovery ?
Oui. Lorsque vous utilisez Site Recovery pour coordonner la réplication et le basculement dans vos succursales, vous obtenez une orchestration unifiée et l’affichage de toutes les charges de travail de vos succursales dans un emplacement central. Vous pouvez facilement exécuter les basculements et gérer la récupération d’urgence de toutes les succursales à partir de votre siège social, sans vous rendre dans ces succursales.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>La reprise d’activité après sinistre est-elle prise en charge pour les machines virtuelles Azure ?

Oui, Site Recovery prend en charge la reprise d’activité après sinistre pour les machines virtuelles Azure entre des régions Azure. [Lisez les questions fréquentes](azure-to-azure-common-questions.md) sur la reprise d’activité après sinistre des machines virtuelles Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>La reprise d’activité après sinistre est-elle prise en charge pour les machines virtuelles VMware ?

Oui, Site Recovery prend en charge la reprise d’activité après sinistre des machines virtuelles VMware locales. [Lisez les questions fréquentes](vmware-azure-common-questions.md) sur la reprise d’activité après sinistre des machines virtuelles VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>La reprise d’activité après sinistre est-elle prise en charge pour les machines virtuelles Hyper-V ?
Oui, Site Recovery prend en charge la reprise d’activité après sinistre des machines virtuelles Hyper-V locales. [Lisez les questions fréquentes](hyper-v-azure-common-questions.md) sur la reprise d’activité après sinistre des machines virtuelles Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>La reprise d’activité après sinistre est-elle prise en charge pour les serveurs physiques ?
Oui, Site Recovery prend en charge la reprise d’activité après sinistre des serveurs physiques locaux exécutant Windows et Linux dans Azure ou dans un site secondaire. En savoir plus sur la configuration requise pour la reprise d’activité après sinistre vers [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) et [vers un site secondaire](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Notez que les serveurs physiques sont exécutés en tant que machines virtuelles dans Azure après le basculement. La restauration automatique à partir d’Azure sur un serveur physique local n’est actuellement pas prise en charge. Vous pouvez uniquement effectuer la restauration automatique d’une machine virtuelle VMware.





## <a name="replication"></a>Réplication

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Puis-je répliquer un VPN de site à site vers Azure ?
Azure Site Recovery réplique des données vers un compte de stockage Azure ou des disques managés, via un point de terminaison public. La réplication ne s’effectue pas via un réseau VPN de site à site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Pourquoi ne puis-je pas répliquer via VPN ?

Lorsque vous répliquez vers Azure, le trafic de réplication atteint les points de terminaison publics d’un stockage Azure. Par conséquent, vous pouvez uniquement répliquer sur l’internet public avec ExpressRoute (peering Microsoft ou peering public existant) et le VPN ne fonctionne pas.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Puis-je utiliser Riverbed SteelHeads pour la réplication ?

Notre partenaire, Riverbed, fournit des instructions détaillées sur l’utilisation d’Azure Site Recovery. Lisez le [guide de leur solution](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Puis-je utiliser ExpressRoute pour répliquer des machines virtuelles vers Azure ?
Oui, [vous pouvez utiliser ExpressRoute](concepts-expressroute-with-site-recovery.md) pour répliquer des machines virtuelles locales vers Azure.

- Azure Site Recovery réplique des données vers un stockage Azure via un point de terminaison public. Vous devez configurer le [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou utiliser un [peering public existant](../expressroute/expressroute-circuit-peerings.md#publicpeering) (déconseillé pour de nouveaux circuits) afin d’utiliser ExpressRoute pour la réplication Site Recovery.
- Le peering Microsoft est le domaine de routage recommandé pour la réplication.
- La réplication n’est pas pris en charge via un peering privé.
- Si vous protégez des machines VMware ou des machines physiques, vérifiez que les [exigences de mise en réseau](vmware-azure-configuration-server-requirements.md#network-requirements) pour le serveur de configuration sont également remplies. La connectivité à des URL spécifiques est requise par le serveur de configuration pour l’orchestration de la réplication Site Recovery. ExpressRoute ne peut pas être utilisé pour cette connectivité.
- Une fois que les machines virtuelles ont été basculées vers un réseau virtuel Azure, vous pouvez y accéder à l’aide de la configuration du [peering privé](../expressroute/expressroute-circuit-peerings.md#privatepeering) avec le réseau virtuel Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Si j’effectue une réplication vers Azure, de quel type de compte de stockage ou disque managé ai-je besoin ?

Vous devez disposer d’un stockage LRS ou GRS. Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée. Ce compte doit se trouver dans la même région que le coffre Recovery Services. Le Stockage Premium est pris en charge pour les machines virtuelles VMware, les machines virtuelles Hyper-V et la réplication de serveurs physiques lorsque vous déployez Site Recovery dans le portail Azure. Les disques managés prennent uniquement en charge LRS.

### <a name="how-often-can-i-replicate-data"></a>À quelle fréquence puis-je répliquer les données ?
* **Hyper-V :** Les machines virtuelles Hyper-V peuvent être répliquées toutes les 5 minutes, ou 30 secondes (sauf pour le Stockage Premium)
* **Machines virtuelles Azure, machines virtuelles VMware et serveurs physiques :** une fréquence de réplication n’est pas pertinente ici. La réplication est continue.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Puis-je étendre la réplication depuis un site de récupération existant à un site tiers ?
La réplication étendue ou chaînée n’est pas prise en charge. Demandez cette fonctionnalité dans le [forum de commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Puis-je effectuer une réplication hors connexion la première fois que je réplique vers Azure ?
Ceci n’est pas pris en charge. Demandez cette fonctionnalité dans le [forum de commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Puis-je exclure des disques spécifiques de la réplication ?
Cette fonctionnalité est prise en charge dans le cadre d’une réplication de machines virtuelles VMware et de machines virtuelles Hyper-V vers Azure à l’aide du portail Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Puis-je répliquer des machines virtuelles avec des disques dynamiques ?
Les disques dynamiques sont pris en charge lors de la réplication des machines virtuelles Hyper-V et lors de la réplication des machines virtuelles VMware et physiques vers Azure. Le disque du système d’exploitation doit être un disque de base.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Puis-je limiter la bande passante allouée pour le trafic de réplication ?
Oui. Pour plus d’informations sur la limitation de bande passante, consultez les articles suivants :

* [Planification de la capacité pour la réplication de machines virtuelles VMware et de serveurs physiques](site-recovery-plan-capacity-vmware.md)
* [Planification de la capacité pour la réplication de machines virtuelles Hyper-V dans Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Basculement
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Si j’effectue un basculement vers Azure, comment accéder aux machines virtuelles Azure après le basculement ?

Vous pouvez accéder aux machines virtuelles Azure via une connexion Internet sécurisée, via un réseau privé virtuel de site à site ou via Azure ExpressRoute. Vous devez préparer un certain nombre d’éléments afin de vous connecter. [Plus d’informations](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Si j’effectue le basculement vers Azure, comment Azure s’assure-t-il de la résilience de mes données ?
Azure est conçu pour la résilience. Site Recovery est déjà prévu pour assurer le basculement vers un centre de données Azure secondaire, dans le respect du contrat SLA Azure. Dans ce cas, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisie pour votre coffre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Si j’effectue une réplication entre deux centres de données, que se passe-t-il si mon centre de données principal connaît une panne inattendue ?
Vous pouvez déclencher un basculement non planifié à partir du site secondaire. Site Recovery n’a pas besoin d’être connecté au site principal pour effectuer le basculement.

### <a name="is-failover-automatic"></a>Le basculement est-il automatique ?
Le basculement n’est pas automatique. Vous lancez les basculements d’un seul clic dans le portail, ou vous pouvez utiliser [Site Recovery PowerShell](/powershell/module/az.recoveryservices) pour déclencher un basculement. La restauration automatique est une action simple dans le portail Site Recovery.

Pour automatiser les processus, vous pouvez utiliser Orchestrator ou Operations Manager localement pour détecter une défaillance de machine virtuelle, puis déclencher le basculement à l’aide du kit SDK.

* [Découvrez plus d’informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
* [En savoir plus](site-recovery-failover.md) sur le basculement.
* [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration automatique de serveurs physiques et de machines virtuelles VMware

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Si mon hôte local ne répond pas ou est bloqué, puis-je effectuer une restauration automatique vers un hôte différent ?
Oui, vous pouvez utiliser la récupération à un autre emplacement pour la restauration automatique vers un hôte différent depuis Azure.

* [Pour les machines virtuelles VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Pour les machines virtuelles Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automatisation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Puis-je automatiser des scénarios Site Recovery avec un kit SDK ?
Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, de PowerShell ou du kit SDK Azure. Scénarios actuellement pris en charge pour le déploiement de Site Recovery à l’aide de PowerShell :

* [Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de PowerShell et d’Azure Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Réplication vers Azure de machines virtuelles Hyper-V (sans VMM) à l’aide de PowerShell et d’Azure Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Réplication de VMware vers Azure avec le gestionnaire des ressources de PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Mise à niveau du composant/fournisseur

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Où trouver les notes de publication/correctifs cumulatifs des mises à niveau Azure Site Recovery ?

[Apprenez-en davantage](site-recovery-whats-new.md) sur les nouvelles mises à jour et [obtenez des informations sur les correctifs cumulatifs](service-updates-how-to.md).

## <a name="next-steps"></a>Étapes suivantes
* Lisez la [Vue d’ensemble de Microsoft Azure Site Recovery](site-recovery-overview.md)

