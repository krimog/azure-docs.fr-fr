---
title: Azure Container Instances - Éléments à prendre en compte en matière de sécurité
description: Recommandations relatives à la sécurisation des images et des secrets pour Azure Container Instances, et éléments à prendre en compte en matière de sécurité sur toutes les plateformes de conteneurs
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 618d3a901698e46760d970f6d4fbc4157c5d2ea3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325925"
---
# <a name="security-considerations-for-azure-container-instances"></a>Éléments à prendre en compte en matière de sécurité pour Azure Container Instances

Cet article présente les recommandations de sécurité relatives à l'utilisation d'Azure Container Instances pour exécuter des applications de conteneurs. Les sujets abordés sont les suivants :

> [!div class="checklist"]
> * **Recommandations de sécurité** sur la gestion des images et des secrets pour Azure Container Instances
> * **Considérations relatives à l'écosystème de conteneurs** tout au long du cycle de vie des conteneurs, pour toutes les plateformes de conteneurs

## <a name="security-recommendations-for-azure-container-instances"></a>Recommandations de sécurité pour Azure Container Instances

### <a name="use-a-private-registry"></a>Utiliser un registre privé

Les conteneurs sont créés à partir d’images stockées dans un ou plusieurs référentiels. Ces référentiels peuvent appartenir à un registre public, comme [Docker Hub](https://hub.docker.com), ou à un registre privé. À l’inverse, [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) est un registre privé pouvant être installé en local ou dans un cloud privé virtuel. Vous pouvez également utiliser des services de registres de conteneurs privés basés sur le cloud, comme [Azure Container Registry](../container-registry/container-registry-intro.md). 

Une image conteneur publique ne garantit pas la sécurité. Les images conteneur se composent de plusieurs couches logicielles, chacune pouvant présenter des vulnérabilités. Pour réduire le risque d'attaques, vous devez stocker et récupérer des images à partir d'un registre privé, comme Azure Container Registry ou Docker Trusted Registry. En plus de fournir un registre privé géré, Azure Container Registry prend en charge l'[authentification basée sur le principal de service](../container-registry/container-registry-authentication.md) via Azure Active Directory pour les flux d'authentification de base. Cette authentification comprend l'accès en fonction du rôle pour les autorisations en lecture seule (pull), les autorisations en écriture (push) et les autorisations de propriétaire.

### <a name="monitor-and-scan-container-images"></a>Surveiller et analyser des images conteneur

Des solutions de surveillance et d'analyse de la sécurité telles que [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) et [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) sont disponibles sur la Place de marché Azure. Vous pouvez les utiliser pour analyser des images conteneur dans un registre privé et pour identifier les vulnérabilités potentielles. Il est important de comprendre l'ampleur de l'analyse proposée par les différentes solutions. 

### <a name="protect-credentials"></a>Protéger les informations d'identification

Les conteneurs peuvent s'étendre sur plusieurs clusters et régions Azure. Vous devez donc sécuriser les informations d'identification requises pour les connexions ou l'accès aux API, comme les mots de passe ou les jetons. Veiller à ce que seuls les utilisateurs disposant de privilèges aient accès à ces conteneurs en transit et au repos. Inventoriez tous les secrets relatifs aux informations d'identification, puis demandez aux développeurs d'utiliser les nouveaux outils de gestion des secrets conçus pour les plateformes de conteneurs.  Assurez-vous que votre solution inclut des bases de données chiffrées, le chiffrement TLS pour les données secrètes en transit, et un [contrôle d'accès basé sur le rôle](../role-based-access-control/overview.md) avec privilèges minimum. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) est un service cloud qui protège les clés et secrets de chiffrement (tels que les certificats, les chaînes de connexion et les mots de passe) de vos applications conteneurisées. Comme il s'agit de données sensibles et stratégiques, sécurisez l'accès à vos coffres de clés afin que seuls les applications et les utilisateurs autorisés y aient accès.

## <a name="considerations-for-the-container-ecosystem"></a>Considérations relatives à l'écosystème de conteneurs

À condition d'être correctement et efficacement mises en œuvre et gérées, les mesures de sécurité suivantes peuvent vous aider à sécuriser et à protéger votre écosystème de conteneurs. Ces mesures s'appliquent tout au long du cycle de vie des conteneurs, du développement au déploiement en production, et à toute une gamme d'orchestrateurs de conteneurs, d'hôtes et de plateformes. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Utiliser la gestion des vulnérabilités dans le cadre du cycle de vie de développement de vos conteneurs 

Avec une gestion efficace des vulnérabilités tout au long du cycle de vie de développement des conteneurs, vous aurez plus de chances d'identifier et de résoudre les problèmes de sécurité avant qu'ils ne s'aggravent. 

### <a name="scan-for-vulnerabilities"></a>Analyser les vulnérabilités 

De nouvelles vulnérabilités sont constamment découvertes. Par conséquent, l'analyse et l'identification des vulnérabilités s'inscrivent dans le cadre d'un processus continu. Incorporez l'analyse des vulnérabilités tout au long du cycle de vie du conteneur :

* En guise de vérification finale dans votre pipeline de développement, n'hésitez pas à effectuer une analyse des vulnérabilités sur les conteneurs avant de transmettre les images à un registre public ou privé. 
* Continuez à analyser les images conteneur du registre pour identifier les failles qui n'ont pas été détectées pendant le développement et pour traiter les vulnérabilités nouvellement découvertes et que pourrait renfermer le code utilisé dans les images conteneur.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapper les vulnérabilités des images avec les conteneurs en cours d'exécution 

Vous devez disposer d'un moyen de mapper les vulnérabilités identifiées dans les images conteneur avec les conteneurs en cours d'exécution afin de pouvoir atténuer ou résoudre les problèmes de sécurité.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Veiller à ce que seules les images approuvées soient utilisées dans votre environnement 

Un écosystème de conteneurs est déjà sujet à suffisamment de changement et de volatilité sans que l'on y ajoute l'utilisation de conteneurs inconnus. N'autorisez que les images conteneur approuvées. Mettez en place des outils et des processus permettant de surveiller et de prévenir l'utilisation d'images conteneur non approuvées. 

Pour réduire la surface d'attaque et empêcher les développeurs de commettre des erreurs de sécurité critiques, vous pouvez contrôler le flux d'images conteneur dans votre environnement de développement. Par exemple, vous pouvez autoriser une distribution Linux donnée comme image de base, de préférence épurée (Alpine ou CoreOS plutôt qu'Ubuntu), afin de minimiser la surface des attaques potentielles. 

La signature d'images ou la prise d'empreintes digitales peuvent constituer une chaîne de contrôle permettant de vérifier l'intégrité des conteneurs. Par exemple, Azure Container Registry prend en charge le modèle [Approbation de contenu](https://docs.docker.com/engine/security/trust/content_trust) de Docker, qui permet aux éditeurs d'images de signer les images placées dans un registre et aux utilisateurs d'images de n'extraire que des images signées.

### <a name="permit-only-approved-registries"></a>Autoriser uniquement les registres approuvés 

En veillant à ce que votre environnement n'utilise que des images approuvées, vous avez également l'assurance que seuls des registres de conteneurs approuvés seront utilisés. L'utilisation de registres de conteneurs approuvés réduit votre exposition aux risques en limitant la possibilité d'introduire des vulnérabilités ou des problèmes de sécurité inconnus. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir l'intégrité des images tout au long du cycle de vie 

Une partie de la gestion de la sécurité tout au long du cycle de vie des conteneurs consiste à garantir l'intégrité des images conteneur dans le registre et lorsqu'elles sont modifiées ou déployées en production. 

* Les images présentant des vulnérabilités, même mineures, ne doivent pas être autorisées à s'exécuter dans un environnement de production. Idéalement, toutes les images déployées en production devraient être enregistrées dans un registre privé uniquement accessible à quelques personnes. Limitez le nombre d'images de production pour pouvoir les gérer efficacement.

* Dans la mesure où il est difficile de déterminer l'origine du logiciel à partir d'une image conteneur publique, créez des images à partir de la source pour être certain de connaître l'origine de la couche. En cas de vulnérabilités dans une image conteneur auto-créée, les clients peuvent plus rapidement trouver une solution. Avec une image publique, les clients devraient trouver la racine de cette image pour résoudre le problème ou obtenir une autre image sécurisée de l'éditeur. 

* Rien ne garantit que les images soigneusement numérisées déployées en production soient à jour pendant toute la durée de vie de l'application. Des vulnérabilités de sécurité peuvent être signalées pour des couches de l’image qui n’étaient pas connues précédemment ou qui ont été introduites après le déploiement en production. 

  Vérifiez périodiquement les images déployées en production pour identifier celles qui sont obsolètes ou qui n'ont pas été mises à jour depuis un certain temps. Vous pouvez utiliser des méthodologies de déploiement bleu-vert et des mécanismes de mise à niveau propagée pour mettre à jour les images conteneur sans interruption de service. Vous pouvez analyser les images à l'aide des outils décrits dans la section précédente. 

* Utilisez un pipeline d'intégration continue (CI) avec une analyse intégrée de la sécurité pour créer des images sécurisées et les transmettre à votre registre privé. L’analyse des vulnérabilités intégrée à la solution CI garantit que les images réussissant tous les tests sont transmises au registre privé à partir duquel les charges de travail de production sont déployées. 

  En cas d'échec aux tests, la solution de pipeline CI garantit que les images vulnérables ne sont pas transmises au registre privé utilisé pour les déploiements de charges de travail de production. Elle automatise aussi l'analyse de la sécurité des images si celles-ci sont nombreuses. Il s’agit d’une bonne alternative aux audits manuels des images visant à contrôler la présence de vulnérabilités de sécurité, qui sont des processus longs, fastidieux et sujets aux erreurs. 

### <a name="enforce-least-privileges-in-runtime"></a>Appliquer les privilèges minimum au moment de l'exécution 

Le concept de privilèges minimum est une recommandation de sécurité de base qui s'applique également aux conteneurs. Lorsqu'une vulnérabilité est exploitée, elle donne généralement à l'attaquant un accès et des privilèges équivalant à ceux de l'application ou du processus compromis. En vous assurant que les conteneurs fonctionnent avec les privilèges et l'accès minimum requis pour accomplir le travail, votre exposition aux risques est réduite. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Réduire la surface d'attaque des conteneurs en supprimant les privilèges inutiles 

Vous pouvez également réduire la surface d'attaque potentielle en supprimant tous les processus ou privilèges inutilisés ou inutiles de l'exécution du conteneur. Les conteneurs dotés de privilèges sont exécutés en tant que racine. Si un utilisateur ou une charge de travail malveillant(e) s'échappe d'un conteneur doté de privilèges, celui-ci s'exécutera en tant que racine sur ce système.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Ajouter à la liste verte les fichiers et exécutables auxquels le conteneur est autorisé à accéder ou qu'il est autorisé à s'exécuter 

La réduction du nombre de variables ou d'inconnues vous permet de maintenir un environnement stable et fiable. Limiter les conteneurs de manière à ce qu'ils puissent uniquement accéder aux fichiers et exécutables pré-approuvés ou ajoutés à la liste verte, ou les exécuter, est une méthode éprouvée pour limiter l'exposition aux risques.  

Il est beaucoup plus facile de gérer une liste verte lorsqu'elle est mise en place dès le début. Une liste verte permet de mesurer le contrôle et la facilité de gestion en désignant les fichiers et exécutables nécessaires au bon fonctionnement de l'application. 

Une liste verte ne réduit pas seulement la surface d'attaque ; elle peut également fournir une base de référence pour les anomalies et prévenir les cas d'usage des scénarios de types « voisin bruyant » et rupture de conteneur. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Appliquer la segmentation du réseau sur les conteneurs en cours d'exécution  

Pour protéger les conteneurs d'un sous-réseau contre les risques liés à la sécurité dans un autre sous-réseau, maintenez la segmentation du réseau (ou nano-segmentation) ou la séparation entre les conteneurs en cours d'exécution. Le maintien de la segmentation du réseau peut également s'avérer nécessaire à l'utilisation de conteneurs dans les secteurs d'activité soumis à des mandats de conformité.  

Par exemple, l'outil partenaire [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fournit une approche automatisée de la nano-segmentation. Aqua surveille les activités du réseau de conteneurs au moment de l'exécution. Il identifie toutes les connexions réseau entrantes et sortantes vers/depuis d'autres conteneurs, services, adresses IP et le réseau Internet public. La nano-segmentation est automatiquement créée en fonction du trafic surveillé. 

### <a name="monitor-container-activity-and-user-access"></a>Surveiller l'activité des conteneurs et l'accès des utilisateurs 

Comme pour tout environnement informatique, vous devez constamment surveiller l'activité et l'accès des utilisateurs à votre écosystème de conteneurs afin d'identifier rapidement toute activité suspecte ou malveillante. Azure fournit des solutions de supervision des conteneurs, telles que :

* [Azure Monitor pour conteneurs](../azure-monitor/insights/container-insights-overview.md) pour surveiller les performances des charges de travail déployées dans les environnements Kubernetes hébergés sur Azure Kubernetes Service (AKS). Azure Monitor pour les conteneurs vous permet de surveiller les performances en collectant des métriques sur le processeur et la mémoire à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. 

* La [solution Azure Container Monitoring](../azure-monitor/insights/containers.md) vous permet de visualiser et de gérer d'autres hôtes de conteneurs Docker et Windows à un emplacement unique. Par exemple :

  * Consultez des informations d'audit détaillées montrant les commandes utilisées avec les conteneurs. 
  * Résolvez les problèmes de conteneurs en consultant des journaux d'activité centralisés et en y effectuant des recherches sans devoir afficher à distance les hôtes Docker ou Windows.  
  * Recherchez des conteneurs bruyants et consommant des ressources excessives sur un ordinateur hôte.
  * Consultez des informations centralisées sur le processeur, la mémoire, le stockage ainsi que l'utilisation et les performances du réseau.  

  La solution prend en charge les orchestrateurs de conteneurs, notamment Docker Swarm, DC/OS, le service Kubernetes non géré, Service Fabric et Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Surveiller l'activité des ressources conteneur 

Surveillez l'activité de vos ressources, comme les fichiers, le réseau et les autres ressources auxquelles vos conteneurs ont accès. La surveillance de l'activité et de la consommation des ressources est à la fois utile pour analyser les performances et comme mesure de sécurité. 

[Azure Monitor](../azure-monitor/overview.md) active la supervision globale des services Azure en permettant la collecte de métriques, de journaux d’activité et de journaux de diagnostic. Par exemple, le journal d’activité indique quand des ressources sont créées ou modifiées. 

Les métriques fournissent des statistiques sur les performances pour différentes ressources, voire pour le système d’exploitation à l’intérieur d’une machine virtuelle. Vous pouvez afficher ces données avec un des explorateurs dans le portail Azure et créer des alertes en fonction de leurs mesures. Azure Monitor fournit le pipeline de mesures le plus rapide (5 minutes à 1 minute). Vous devez donc l'utiliser pour les alertes et les notifications urgentes. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Consigner tous les accès des utilisateurs administratifs du conteneur à des fins d'audit 

Maintenez un journal d'audit précis des accès administratifs à votre écosystème de conteneurs, à votre registre de conteneurs et à vos images conteneur. Ces enregistrements peuvent être nécessaires à des fins d'audit et ils peuvent être utilisés comme preuves médico-légales après un incident de sécurité. Vous pouvez utiliser la [solution Azure Container Monitoring](../azure-monitor/insights/containers.md) pour atteindre cet objectif. 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la gestion des vulnérabilités des conteneurs avec les solutions de [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) et [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* En savoir plus sur la [sécurité des conteneurs dans Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).