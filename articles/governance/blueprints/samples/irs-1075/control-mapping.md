---
title: Exemple de blueprint IRS 1075 - Correspondance des contrôles
description: Correspondance des contrôles de l’exemple de blueprint IRS 1075. Chaque contrôle est mis en correspondance avec une ou plusieurs stratégies Azure qui simplifient l’évaluation.
ms.date: 08/07/2019
ms.topic: sample
ms.openlocfilehash: b56e1a2c8a71e3bb7927dc487e0c2f1e7709d413
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032211"
---
# <a name="control-mapping-of-the-irs-1075-blueprint-sample"></a>Correspondance des contrôles de l’exemple blueprint IRS 1075

L’article suivant explique en détails comment l’exemple Blueprint IRS 1075 d’Azure Blueprints correspond aux contrôles IRS 1075. Pour plus d’informations sur les contrôles, consultez [IRS 1075](https://www.irs.gov/pub/irs-pdf/p1075.pdf).

Les correspondances suivantes concernent les contrôles **IRS 1075**. Utilisez le volet de navigation de droite pour accéder directement à la correspondance d’un contrôle spécifique. De nombreux contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **\[Préversion\] : Auditer les contrôles IRS 1075 et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](../../../policy/overview.md). Ces stratégies peuvent vous aider à [évaluer la conformité](../../../policy/how-to/get-compliance-data.md) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance de 1:1 ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux stratégies elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. En outre, la norme de conformité comprend des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les contrôles et les définitions Azure Policy pour cet exemple de blueprint de conformité peuvent changer au fil du temps. Pour afficher l’historique des changements, consultez l’[historique des validations GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/irs-1075/control-mapping.md).

## <a name="9321-ac-2-account-management"></a>9.3.2.1 AC-2 Gestion des comptes

Ce blueprint vous aide à examiner les comptes qui peuvent ne pas être conformes aux exigences de votre organisation en matière de gestion de comptes. Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui auditent les comptes externes avec des autorisations de lecture, d’écriture et de propriétaire sur un abonnement, ainsi que les comptes dépréciés. En passant en revue les comptes audités par ces stratégies, vous pouvez prendre les mesures appropriées pour veiller au respect des exigences en matière de gestion des comptes.

- Les comptes déconseillés doivent être supprimés de votre abonnement
- Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement

## <a name="9321-ac-2-7-account-management--role-based-schemes"></a>9.3.2.1 AC-2 (7) Gestion des comptes | Schémas basés sur des rôles

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint affecte également des définitions [Azure Policy](../../../policy/overview.md) afin d’auditer l’utilisation de l’authentification Azure Active Directory pour les serveurs SQL et Service Fabric. L’utilisation de l’authentification Azure Active Directory permet une gestion simplifiée des autorisations et une gestion centralisée des identités des utilisateurs de bases de données et d’autres services Microsoft. En outre, ce blueprint affecte une définition Azure Policy pour vérifier l’utilisation des règles RBAC personnalisées. Ces dernières étant une source d’erreurs, le fait de savoir où elles sont implémentées peut vous aider à déterminer les besoins réels et l’implémentation appropriée.

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL
- Auditer l’utilisation de règles personnalisées RBAC
- Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client

## <a name="9321-ac-2-12-account-management--account-monitoring--atypical-usage"></a>9.3.2.1 AC-2 (12) Gestion des comptes | Supervision des comptes/utilisation atypique

L’accès juste-à-temps (JIT) à la machine virtuelle verrouille le trafic entrant vers les machines virtuelles Azure, réduisant l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles en cas de besoin. Toutes les demandes JIT pour accéder aux machines virtuelles sont journalisées dans le journal d’activité, ce qui vous permet de détecter toute utilisation atypique. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pouvant prendre en charge l’accès juste-à-temps mais qui n’ont pas encore été configurées.

- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles

## <a name="9314-ac-4-information-flow-enforcement"></a>9.3.1.4 AC-4 Application du flux d’informations

CORS (Cross origin Resource Sharing) peut autoriser un domaine externe à demander des ressources App Services. Microsoft vous recommande d’autoriser uniquement les domaines requis à interagir avec vos API, fonctions et applications web. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) pour vous aider à superviser les restrictions d’accès aux ressources CORS dans Azure Security Center.
Le fait de comprendre les implémentations CORS peut vous aider à vérifier que des contrôles de flux d’informations sont implémentés.

- CORS ne doit pas autoriser toutes les ressources à accéder à vos applications web

## <a name="9315-ac-5-separation-of-duties"></a>9.3.1.5 AC-5 Séparation des tâches

Le fait d’avoir un seul propriétaire d’abonnement Azure ne permet pas d’assurer la redondance administrative. À l’inverse, un nombre trop élevé de propriétaires d’abonnement Azure peut augmenter le risque d’une violation par le biais d’un compte de propriétaire compromis. Ce blueprint permet de maintenir un nombre adapté de propriétaires d’abonnement Azure en affectant des définitions [Azure Policy](../../../policy/overview.md) qui auditent le nombre de propriétaires pour les abonnements Azure. Ce blueprint affecte également des définitions Azure Policy qui vous permettent de contrôler l’appartenance au groupe Administrateurs sur les machines virtuelles Windows. La gestion des autorisations des propriétaires d’abonnement et des administrateurs de machine virtuelle peut vous aider à implémenter une séparation appropriée des tâches.

- Trois propriétaires au plus doivent être désignés pour votre abonnement
- Auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs contient un membre spécifié
- Auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas tous les membres spécifiés
- Déployer des exigences pour auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs contient un membre spécifié
- Déployer des exigences pour auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas tous les membres spécifiés
- Plusieurs propriétaires doivent être affectés à votre abonnement

## <a name="9316-ac-6-7-least-privilege--review-of-user-privileges"></a>9.3.1.6 AC-6 (7) Privilèges minimum | Révision des privilèges utilisateur

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes à examiner en priorité. L’examen de ces indicateurs de compte peut vous aider à vous assurer que les contrôles de privilège minimum sont implémentés.

- Trois propriétaires au plus doivent être désignés pour votre abonnement
- Auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs contient un membre spécifié
- Auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas tous les membres spécifiés
- Déployer des exigences pour auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs contient un membre spécifié
- Déployer des exigences pour auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas tous les membres spécifiés
- Plusieurs propriétaires doivent être affectés à votre abonnement

## <a name="93112-ac-17-1-remote-access--automated-monitoring--control"></a>9.3.1.12 AC-17 (1) Accès à distance | Supervision/contrôle automatisé

Ce blueprint vous aide à superviser et à contrôler l’accès à distance en affectant des définitions [Azure Policy](../../../policy/overview.md) afin de vérifier que le débogage à distance pour l’application Azure App Service est désactivé. Ce blueprint affecte aussi des définitions de stratégie qui auditent les machines virtuelles Linux autorisant les connexions à distance à partir de comptes sans mot de passe. De plus, ce blueprint affecte une définition Azure Policy qui aide à superviser les accès non restreints aux comptes de stockage. En supervisant ces indicateurs, vous pouvez vérifier que les méthodes d’accès à distance sont conformes à votre stratégie de sécurité.

- \[Préversion\] : Auditer les machines virtuelles Linux qui autorisent les connexions à distance des comptes sans mot de passe
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Linux qui autorisent les connexions à distance des comptes sans mot de passe
- Auditer l'accès réseau non restreint aux comptes de stockage
- Le débogage à distance doit être désactivé pour l’application API
- Le débogage à distance devrait être désactivé pour Function App
- Le débogage à distance doit être désactivé pour l'application web

## <a name="9313-au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>9.3.1.3 AU-3 (2) Contenu des enregistrements d’audit | Gestion centralisée du contenu planifié des enregistrements d’audit

Les données de journal collectées par Azure Monitor sont stockées dans un espace de travail Log Analytics, permettant une configuration et une gestion centralisées. Ce blueprint vous permet de garantir que les événements sont journalisés. Il affecte pour cela des définitions [Azure Policy](../../../policy/overview.md) qui auditent et appliquent le déploiement de l’agent Log Analytics sur les machines virtuelles Azure.

- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités
- \[Préversion\] : Déployer Log Analytics Agent pour Linux VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Linux
- \[Préversion\] : Déployer Log Analytics Agent pour Windows VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Windows

## <a name="9335-au-5-response-to-audit-processing-failures"></a>9.3.3.5 AU-5 Réponse aux échecs du processus d’audit

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui supervisent les configurations d’audit et de journalisation des événements. En supervisant ces configurations, vous pouvez obtenir une indication de la défaillance ou d’une mauvaise configuration du système d’audit et prendre des actions correctives.

- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL
- Advanced Data Security doit être activé sur vos instances managées
- Advanced Data Security doit être activé sur vos serveurs SQL

## <a name="9336-au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>9.3.3.6 AU-6 (4) Révision, analyse et rapports d’audit | Révision et analyse centralisées

Les données de journal collectées par Azure Monitor sont stockées dans un espace de travail Log Analytics, permettant la génération de rapports et l’analyse centralisées. Ce blueprint vous permet de garantir que les événements sont journalisés. Il affecte pour cela des définitions [Azure Policy](../../../policy/overview.md) qui auditent et appliquent le déploiement de l’agent Log Analytics sur les machines virtuelles Azure.

- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités
- \[Préversion\] : Déployer Log Analytics Agent pour Linux VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Linux
- \[Préversion\] : Déployer Log Analytics Agent pour Windows VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Windows

## <a name="93311-au-12-audit-generation"></a>9.3.3.11 AU-12 Génération de l’audit

Ce blueprint vous permet de garantir que les événements système sont journalisés en affectant des définitions [Azure Policy](../../../policy/overview.md) qui vérifient les paramètres de journalisation sur les ressources Azure. Ces définitions de stratégie vérifient et appliquent le déploiement de l’agent Log Analytics sur les machines virtuelles et la configuration des paramètres d’audit pour les autres types de ressources Azure. Ces définitions de stratégie vérifient également la configuration des journaux de diagnostic pour fournir des insights sur les opérations effectuées au sein des ressources Azure. L’audit et Advanced Data Security sont configurés sur les serveurs SQL.

- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités
- \[Préversion\] : Déployer Log Analytics Agent pour Linux VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Linux
- \[Préversion\] : Déployer Log Analytics Agent pour Windows VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Windows
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL
- Advanced Data Security doit être activé sur vos instances managées
- Advanced Data Security doit être activé sur vos serveurs SQL
- Déployer Advanced Data Security sur des serveurs SQL
- Déployer l’audit sur des serveurs SQL
- Déployer les paramètres de diagnostic pour les groupes de sécurité réseau

## <a name="9357-cm-7-2-least-functionality--prevent-program-execution"></a>9.3.5.7 CM-7 (2) Fonctionnalités essentielles | Empêcher l’exécution d’un programme

Le contrôle des applications adaptatif dans Azure Security Center est une solution intelligente et automatisée de mise en liste verte d’applications de bout en bout qui peut bloquer ou empêcher l’exécution de logiciels spécifiques sur vos machines virtuelles. Le contrôle des applications peut s’exécuter dans un mode de mise en conformité qui empêche l’exécution d’applications non approuvées. Ce blueprint affecte une définition Azure Policy qui vous aide à superviser les machines virtuelles pour lesquelles une liste verte d’applications est recommandée mais n’a pas encore été configurée.

- Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="9357-cm-7-5-least-functionality--authorized-software--whitelisting"></a>9.3.5.7 CM-7 (5) Fonctionnalités essentielles | Logiciels autorisés / Mise sur liste verte

Le contrôle des applications adaptatif dans Azure Security Center est une solution intelligente et automatisée de mise en liste verte d’applications de bout en bout qui peut bloquer ou empêcher l’exécution de logiciels spécifiques sur vos machines virtuelles. Le contrôle d’applications vous permet de créer des listes d’applications approuvées pour vos machines virtuelles. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pour lesquelles une liste verte d’applications est recommandée mais n’a pas encore été configurée.

- Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="93511-cm-11-user-installed-software"></a>9.3.5.11 CM-11 Logiciels installés par l’utilisateur

Le contrôle des applications adaptatif dans Azure Security Center est une solution intelligente et automatisée de mise en liste verte d’applications de bout en bout qui peut bloquer ou empêcher l’exécution de logiciels spécifiques sur vos machines virtuelles. Le contrôle d’applications peut vous aider à appliquer et à superviser la conformité à des stratégies de restriction logicielle. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pour lesquelles une liste verte d’applications est recommandée mais n’a pas encore été configurée.

- Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="9366-cp-7-alternate-processing-site"></a>9.3.6.6 CP-7 Site de traitement secondaire

Azure Site Recovery réplique les charges de travail exécutées sur des machines virtuelles à partir d’un emplacement principal vers un emplacement secondaire. Si une panne survient au niveau du site principal, la charge de travail échoue sur l’emplacement secondaire. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui audite les machines virtuelles pour lesquelles la reprise d’activité après sinistre n’est pas configurée. En supervisant cet indicateur, vous pouvez vérifier que les contrôles d’urgence nécessaires sont en place.

- Auditer des machines virtuelles pour lesquelles la reprise d’activité après sinistre n’est pas configurée

## <a name="9372-ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>9.3.7.2 IA-2 (1) Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes privilégiés

Ce blueprint permet de limiter et de contrôler les accès privilégiés en affectant des définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes dotés d’autorisations de propriétaire et/ou d’écriture pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur permet de sécuriser les comptes même si un élément des informations d’authentification est compromis. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement

## <a name="9372-ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>9.3.7.2 IA-2 (2) Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes non privilégiés

Ce blueprint vous aide à limiter et à contrôler les accès en affectant une définition [Azure Policy](../../../policy/overview.md) pour auditer les comptes dotés d’autorisations de lecture pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur permet de sécuriser les comptes même si un élément des informations d’authentification est compromis. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis.

- L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement

## <a name="9375-ia-5-authenticator-management"></a>9.3.7.5 IA-5 Gestion des authentificateurs

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui auditent les machines virtuelles Linux autorisant les connexions à distance à partir de comptes sans mot de passe et/ou associés à des autorisations incorrectes définies dans le fichier passwd. Ce blueprint affecte également des définitions de stratégie qui auditent la configuration du type de chiffrement de mot de passe pour les machines virtuelles Windows. En supervisant ces indicateurs, vous pouvez vérifier que les authentificateurs de système sont conformes à la stratégie d’identification et d’authentification de votre organisation.

- \[Préversion\] : Auditer les machines virtuelles Linux qui n’ont pas les autorisations de fichier passwd définies sur 0644
- \[Préversion\] : Auditer les machines virtuelles Linux qui ont des comptes sans mot de passe
- \[Préversion\] : Auditer les machines virtuelles Windows qui ne stockent pas les mots de passe à l’aide du chiffrement réversible
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Linux qui n’ont pas les autorisations de fichier passwd définies sur 0644
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Linux qui ont des comptes sans mot de passe
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui ne stockent pas les mots de passe à l’aide du chiffrement réversible

## <a name="9375-ia-5-1-authenticator-management--password-based-authentication"></a>9.3.7.5 IA-5 (1) Gestion des authentificateurs | Authentification basée sur mot de passe

Ce blueprint permet d’appliquer des mots de passe forts en affectant des définitions [Azure Policy](../../../policy/overview.md) qui auditent les machines virtuelles Windows n’exigeant pas de niveau de sécurité minimal pour les mots de passe. En identifiant les machines virtuelles qui enfreignent la stratégie de force des mots de passe, vous pouvez prendre des mesures correctives visant à rendre les mots de passe de tous les comptes d’utilisateurs de machine virtuelle conformes à la stratégie de mot de passe de votre organisation.

- \[Préversion\] : Auditer les machines virtuelles Windows qui autorisent la réutilisation des 24 mots de passe précédents
- \[Préversion\] : Auditer les machines virtuelles Windows qui n’ont pas l’antériorité maximale du mot de passe définie sur 70 jours
- \[Préversion\] : Auditer les machines virtuelles Windows qui n’ont pas l’antériorité minimale du mot de passe définie sur 1 jour
- \[Préversion\] : Auditer les machines virtuelles Windows qui n’ont pas le paramètre de complexité de mot de passe activé
- \[Préversion\] : Auditer les machines virtuelles Windows qui ne limitent pas la longueur minimale du mot de passe à 14 caractères
- \[Préversion\] : Auditer les machines virtuelles Windows qui ne stockent pas les mots de passe à l’aide du chiffrement réversible
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui autorisent la réutilisation des 24 mots de passe précédents
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui n’ont pas l’antériorité maximale du mot de passe définie sur 70 jours
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui n’ont pas l’antériorité minimale du mot de passe définie sur 1 jour
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui n’ont pas le paramètre de complexité de mot de passe activé
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui ne limitent pas la longueur minimale du mot de passe à 14 caractères
- \[Préversion\] : Déployer des exigences pour auditer les machines virtuelles Windows qui ne stockent pas les mots de passe à l’aide du chiffrement réversible

## <a name="93143-ra-5-vulnerability-scanning"></a>9.3.14.3 RA-5 Analyse des vulnérabilités

Ce blueprint permet de gérer les vulnérabilités du système d’informations en affectant des définitions [Azure Policy](../../../policy/overview.md) qui supervisent les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées. Ce blueprint affecte également des définitions de stratégie qui auditent et appliquent Advanced Data Security sur les serveurs SQL. Advanced Data Security comprend l’évaluation des vulnérabilités et des fonctionnalités de protection avancée contre les menaces pour vous aider à comprendre les vulnérabilités dans vos ressources déployées.

- Advanced Data Security doit être activé sur vos instances managées
- Advanced Data Security doit être activé sur vos serveurs SQL
- Déployer Advanced Data Security sur des serveurs SQL
- Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées
- Les vulnérabilités détectées dans la configuration de la sécurité de vos machines virtuelles doivent être corrigées
- Les vulnérabilités de vos bases de données SQL doivent être éliminées
- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités

## <a name="93164-sc-5-denial-of-service-protection"></a>9.3.16.4 SC-5 Protection contre le déni de service

Le niveau Standard de déni de service distribué (DDoS) d’Azure offre des fonctionnalités et des capacités d’atténuation supplémentaires par rapport au niveau de service De base. Ces fonctionnalités supplémentaires incluent l’intégration d’Azure Monitor et la possibilité de passer en revue les rapports d’atténuation post-attaque. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vérifie si le niveau Standard DDoS est activé. En comprenant la différence de capacité entre les niveaux de service, vous pouvez choisir la meilleure solution pour traiter les protections contre le déni de service dans votre environnement Azure.

- DDoS Protection Standard doit être activé

## <a name="93165-sc-7-boundary-protection"></a>9.3.16.5 SC-7 Protection de la limite

Ce blueprint permet de gérer et de contrôler la limite système en affectant une définition [Azure Policy](../../../policy/overview.md) qui supervise les recommandations de sécurisation renforcée des groupes de sécurité réseau dans Azure Security Center. Azure Security Center analyse les modèles de trafic des machines virtuelles accessibles sur Internet et fournit des recommandations en matière de règle de groupe de sécurité réseau afin de réduire la surface d’attaque potentielle.
Ce blueprint affecte également des définitions de stratégie qui supervisent les points de terminaison, les applications et les comptes de stockage non protégés. Les points de terminaison et les applications qui ne sont pas protégés par un pare-feu, de même que les comptes de stockage avec un accès illimité, peuvent permettre un accès involontaire aux informations contenues dans le système d’information.

- Les règles de groupe de sécurité réseau pour les machines virtuelles accessibles sur Internet doivent être renforcées
- L'accès via un point de terminaison accessible sur Internet doit être limité
- Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées
- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="93165-sc-7-3-boundary-protection--access-points"></a>9.3.16.5 SC-7 (3) Protection de la limite | Points d’accès

L’accès juste-à-temps (JIT) à la machine virtuelle verrouille le trafic entrant vers les machines virtuelles Azure, réduisant l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles en cas de besoin. L’accès JIT à la machine virtuelle vous aide à limiter le nombre de connexions externes à vos ressources dans Azure. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pouvant prendre en charge l’accès juste-à-temps mais qui n’ont pas encore été configurées.

- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles

## <a name="93165-sc-7-4-boundary-protection--external-telecommunications-services"></a>9.3.16.5 SC-7 (4) Protection de la limite | Services de télécommunications externes

L’accès juste-à-temps (JIT) à la machine virtuelle verrouille le trafic entrant vers les machines virtuelles Azure, réduisant l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles en cas de besoin. L’accès JIT à la machine virtuelle vous aide à gérer les exceptions de votre stratégie de flux de trafic en facilitant les processus de demande d’accès et d’approbation. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pouvant prendre en charge l’accès juste-à-temps mais qui n’ont pas encore été configurées.

- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles

## <a name="96163-sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>9.6.16.3 SC-8 (1) Confidentialité et intégrité des transmissions | Protection par chiffrement ou autre protection physique

Ce blueprint permet de protéger la confidentialité et l’intégrité des informations transmises en affectant des définitions [Azure Policy](../../../policy/overview.md) permettant de superviser le mécanisme de chiffrement implémenté pour les protocoles de communication. Vérifiez que les communications sont correctement chiffrées pour mieux répondre aux besoins de votre organisation et pour protéger vos informations contre toute divulgation ou modification non autorisées.

- L'application API doit uniquement être accessible via HTTPS
- Auditer les serveurs web Windows qui n’utilisent pas de protocole de communication sécurisé
- Déployer des exigences pour auditer les serveurs web Windows qui n’utilisent pas de protocole de communication sécurisé
- Function App ne doit pas être accessible via HTTPS
- Seules les connexions sécurisées à votre cache Redis doivent être activées
- La sécurisation du transfert vers des comptes de stockage doit être activée
- L'application web ne doit pas être accessible via HTTPS

## <a name="93166-sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>9.3.16.6 SC-28 (1) Protection des informations au repos | Protection par chiffrement

Ce blueprint permet d’appliquer votre stratégie sur l’utilisation des contrôles de chiffrement pour protéger les informations au repos en affectant des définitions [Azure Policy](../../../policy/overview.md) qui mettent en œuvre des contrôles de chiffrement spécifiques et détectent l’utilisation de paramètres de chiffrement faibles. Le fait de savoir où vos ressources Azure peuvent avoir des configurations de chiffrement non optimales peut vous aider à prendre des mesures correctives visant à vérifier que les ressources sont configurées conformément à votre stratégie de sécurité des informations. Plus précisément, les définitions de stratégie affectées par ce blueprint exigent le chiffrement des comptes Data Lake Storage, exigent le chiffrement transparent des données sur les bases de données SQL et vérifient l’absence de chiffrement sur les bases de données SQL, les disques de machine virtuelle et les variables de compte Automation.

- Advanced Data Security doit être activé sur vos instances managées
- Advanced Data Security doit être activé sur vos serveurs SQL
- Déployer Advanced Data Security sur des serveurs SQL
- Déployer le chiffrement transparent des données sur les bases de données SQL
- Le chiffrement de disque doit être appliqué sur les machines virtuelles
- Exiger un chiffrement sur les comptes Data Lake Store
- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL

## <a name="93172-si-2-flaw-remediation"></a>9.3.17.2 SI-2 Correction des défauts

Ce blueprint permet de gérer les défauts du système d’informations en affectant des définitions [Azure Policy](../../../policy/overview.md) qui supervisent les mises à jour système manquantes, ainsi que les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées. Ce blueprint affecte également une définition de stratégie qui garantit la mise à jour corrective du système d’exploitation des groupes de machines virtuelles identiques.

- Exiger la mise à jour corrective automatique de l’image du système d’exploitation sur les groupes de machines virtuelles identiques
- Les mises à jour système doivent être installées sur les groupes de machines virtuelles identiques
- Les mises à jour système doivent être installées sur vos machines virtuelles
- Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées
- Les vulnérabilités détectées dans la configuration de la sécurité de vos machines virtuelles doivent être corrigées
- Les vulnérabilités de vos bases de données SQL doivent être éliminées
- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités

## <a name="93173-si-3-malicious-code-protection"></a>9.3.17.3 SI-3 Protection contre les codes malveillants

Ce blueprint permet de gérer la protection des points de terminaison, notamment la protection contre le code malveillant, en affectant des définitions [Azure Policy](../../../policy/overview.md) qui supervisent l’absence de protection des points de terminaison sur les machines virtuelles dans Azure Security Center, et appliquent la solution anti-programme malveillant de Microsoft sur les machines virtuelles Windows.

- Déployer l’extension Microsoft IaaSAntimalware par défaut pour Windows Server
- La solution de protection des points de terminaison doit être installée sur les groupes de machines virtuelles identiques
- Superviser les agents Endpoint Protection manquants dans Azure Security Center

## <a name="93173-si-3-1-malicious-code-protection--central-management"></a>9.3.17.3 SI-3 (1) Protection contre les codes malveillants | Gestion centralisée

Ce blueprint permet de gérer la protection des points de terminaison, notamment la protection contre le code malveillant, en affectant des définitions [Azure Policy](../../../policy/overview.md) qui vérifient l’absence de protection des points de terminaison sur les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de gestion centralisée et de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées.

- La solution de protection des points de terminaison doit être installée sur les groupes de machines virtuelles identiques
- Superviser les agents Endpoint Protection manquants dans Azure Security Center

## <a name="93174-si-4-information-system-monitoring"></a>9.3.17.4 SI-4 Supervision du système d’information

Ce blueprint vous aide à superviser votre système en auditant et en appliquant la journalisation et la sécurité des données aux ressources Azure. Plus précisément, les stratégies affectées auditent et appliquent le déploiement de l’agent Log Analytics et de paramètres de sécurité renforcée pour les bases de données SQL, les comptes de stockage et les ressources réseau. Ces fonctionnalités peuvent vous aider à détecter des comportements anormaux et des indicateurs d’attaques afin de prendre des mesures appropriées.

- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier les machines virtuelles de l’espace de travail Log Analytics - Signaler les incompatibilités
- \[Préversion\] : Déployer Log Analytics Agent pour Linux VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Linux
- \[Préversion\] : Déployer Log Analytics Agent pour Windows VM Scale Sets (VMSS)
- \[Préversion\] : Déployer Log Analytics Agent pour les machines virtuelles Windows
- Advanced Data Security doit être activé sur vos instances managées
- Advanced Data Security doit être activé sur vos serveurs SQL
- Déployer Advanced Data Security sur des serveurs SQL
- Déployer Advanced Threat Protection sur les comptes de stockage
- Déployer l’audit sur des serveurs SQL
- Déployer Network Watcher lors de la création de réseaux virtuels
- Déployer la détection de menaces sur les serveurs SQL

## <a name="93174-si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>9.3.17.4 SI-4 (18) Supervision du système d’information | Analyser le trafic/l’exfiltration secrète

Advanced Threat Protection pour Stockage Azure détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Les alertes de protection couvrent les modèles d’accès inhabituels, les extractions ou chargements anormaux ainsi que les activités de stockage suspectes. Ces indicateurs peuvent vous aider à détecter l’exfiltration secrète d’informations.

- Déployer Advanced Threat Protection sur les comptes de stockage

> [!NOTE]
> La disponibilité des définitions Azure Policy peut varier dans Azure Government et dans d’autres clouds nationaux. 

## <a name="next-steps"></a>Étapes suivantes

Vous venez de passer en revue la mise en correspondance des contrôles du blueprint IRS 1075. Consultez à présent les articles suivants pour en savoir plus sur le blueprint et découvrir comment déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprint IRS 1075 - Vue d’ensemble](./index.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).