---
title: Utiliser Azure Data Factory pour migrer des données d’Amazon S3 vers le stockage Azure
description: Utiliser Azure Data Factory pour migrer des données d’Amazon S3 vers le stockage Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/04/2019
ms.openlocfilehash: 4d4e0453105dacfbf35624a2a9acb9d5994f4dea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675742"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Utiliser Azure Data Factory pour migrer des données d’Amazon S3 vers le stockage Azure 

Azure Data Factory fournit un mécanisme performant, robuste et rentable pour migrer des données à l’échelle d’Amazon S3 vers le stockage Blob Azure ou Azure Data Lake Storage Gen2.  Cet article fournit les informations suivantes à l’attention des ingénieurs et des développeurs de données : 

> [!div class="checklist"]
> * Performances 
> * Résilience de copie
> * Sécurité du réseau
> * Architecture de solution de haut niveau 
> * Meilleures pratiques en matière d’implémentation  

## <a name="performance"></a>Performances

ADF offre une architecture serverless qui autorise le parallélisme à différents niveaux, ce qui permet aux développeurs de créer des pipelines pour utiliser pleinement la bande passante de votre réseau, ainsi que les IOPS et la bande passante de stockage pour maximiser le débit de déplacement de données pour votre environnement. 

Les clients ont réussi à migrer des pétaoctets de données constitués de centaines de millions de fichiers d’Amazon S3 vers le stockage d’objets Blob Azure, avec un débit soutenu de 2 Gbits/s et plus. 

![performances](media/data-migration-guidance-s3-to-azure-storage/performance.png)

L’image ci-dessus montre comment vous pouvez obtenir des vitesses de déplacement de données exceptionnelles à travers différents niveaux de parallélisme:
 
- Une seule activité de copie peut tirer parti des ressources de calcul évolutives : lorsque vous utilisez Azure Integration Runtime, vous pouvez spécifier [jusqu'à 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) pour chaque activité de copie serverless ; lorsque vous utilisez des Integration Runtime auto-hébergés, vous pouvez mettre à l’échelle manuellement la machine ou la monter en charge sur plusieurs machines ([jusqu’à 4 nœuds](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), et une seule activité de copie partitionnera son jeu de fichiers sur tous les nœuds. 
- Une activité de copie unique lit et écrit dans le magasin de données à l’aide de plusieurs conversations. 
- Le flux de contrôle ADF peut démarrer plusieurs activités de copie en parallèle, par exemple [Pour chaque boucle](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Résilience

Dans une exécution d’activité de copie unique, ADF intègre un mécanisme de nouvelle tentative qui permet de gérer un certain niveau d’échecs temporaires dans les magasins de données ou dans le réseau sous-jacent. 

Lors de la copie binaire de S3 vers un objet Blob et de S3 vers ADLS Gen2, ADF effectue automatiquement des points de contrôle.  Si l’exécution d’une activité de copie a échoué ou a expiré, lors d’une nouvelle tentative (Assurez-vous que le nombre de nouvelles tentatives est > 1), la copie reprend à partir du dernier point d’échec au lieu de commencer depuis le début. 

## <a name="network-security"></a>Sécurité du réseau 

Par défaut, ADF transfère les données d’Amazon S3 vers le stockage Blob Azure ou Azure Data Lake Storage Gen2 à l’aide d’une connexion chiffrée sur le protocole HTTPS.  Le protocole HTTPS assure le chiffrement des données en transit et empêche les écoutes clandestines et les attaques de l’intercepteur. 

Sinon, si vous ne souhaitez pas que les données soient transférées via l’internet public, vous pouvez obtenir une sécurité accrue en transférant des données via un lien de peering privé entre le service de connexion directe AWS et Azure Express Route.  Reportez-vous à l’architecture de la solution ci-dessous pour savoir comment procéder. 

## <a name="solution-architecture"></a>Architecture de solution

Migrer des données sur l’Internet public :

![solution-architecture-public-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Dans cette architecture, les données sont transférées en toute sécurité à l’aide du protocole HTTPS sur l’Internet public. 
- La source Amazon S3 ainsi que les destinations Azure Blob Storage ou Azure Data Lake Storage Gen2 sont configurées pour autoriser le trafic à partir de toutes les adresses IP du réseau.  Reportez-vous à la deuxième architecture ci-dessous pour savoir comment limiter l’accès réseau à une plage d’adresses IP spécifique. 
- Vous pouvez facilement augmenter la quantité de puissance serverless pour utiliser pleinement votre réseau et votre bande passante de stockage, de manière à obtenir le meilleur débit pour votre environnement. 
- La migration des instantanés initiaux et la migration des données Delta peuvent être réalisées à l’aide de cette architecture. 

Migrer des données via un lien privé : 

![solution-architecture-private-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Dans cette architecture, la migration des données s’effectue via un lien de peering privé entre AWS Direct Connect et Azure Express Route, de telle sorte que les données ne transitent jamais sur l’Internet public.  Cela nécessite l'utilisation de AWS VPC et du réseau virtuel Azure. 
- Vous devez installer le runtime d'intégration ADF auto-hébergé sur une machine virtuelle Windows au sein de votre réseau virtuel Azure pour réaliser cette architecture.  Vous pouvez mettre à l’échelle manuellement vos machines virtuelles IR auto-hébergées ou les mettre à l’échelle sur plusieurs machines virtuelles (jusqu’à 4 nœuds) pour utiliser pleinement votre réseau et vos IOPS/bande passante de stockage. 
- S’il est acceptable de transférer des données sur HTTPS mais que vous souhaitez verrouiller l’accès réseau à la source S3 à une plage d’adresses IP spécifique, vous pouvez adopter une variante de cette architecture en supprimant l’AWS VPC et en remplaçant le lien privé par HTTPS.  Vous devez conserver le runtime d’intégration virtuel Azure et auto-hébergé sur une machine virtuelle Azure, afin de pouvoir utiliser une adresse IP publiquement routable à des fins de liste verte. 
- Cette architecture permet à la fois la migration initiale des données instantanées et la migration des données delta. 

## <a name="implementation-best-practices"></a>Meilleures pratiques en matière d’implémentation 

### <a name="authentication-and-credential-management"></a>Authentification et gestion des informations d’identification 

- Pour vous authentifier sur un compte Amazon S3, vous devez utiliser la [clé d’accès pour le compte IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Plusieurs types d’authentification sont pris en charge pour la connexion à Azure Blob Storage.  L’utilisation d’[identités gérées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) est fortement recommandée : basée sur une identification ADF automatiquement gérée dans Azure AD, elle vous permet de configurer des pipelines sans fournir d’informations d’identification dans la définition de service lié .  Vous pouvez également vous authentifier auprès de Azure Blob Storage à l’aide du [Principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), de la [signature d’accès partagé](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication) ou de la [clé de compte de stockage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Plusieurs types d’authentification sont également pris en charge pour la connexion à Azure Data Lake Storage Gen2.  L’utilisation d’[identités gérées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) est fortement recommandée, bien que le [principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou la [clé de compte de stockage](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) puissent également être utilisés. 
- Lorsque vous n’utilisez pas d’identités gérées pour les ressources Azure, il est vivement recommandé de [stocker les informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) pour faciliter la gestion et la rotation des clés sans modifier les services liés ADF.  Il s’agit également de l'une des [meilleures pratiques pour CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migration initiale des données d’instantané 

La partition de données est recommandée en particulier lors de la migration de plus de 10 To de données.  Pour partitionner les données, tirez parti du paramètre « Prefix » pour filtrer les dossiers et les fichiers d’Amazon S3 par nom, puis chaque travail de copie ADF peut copier une partition à la fois.  Vous pouvez exécuter plusieurs tâches de copie ADF simultanément pour un meilleur débit. 

Si l’un des travaux de copie échoue en raison d’un problème temporaire de réseau ou de magasin de données, vous pouvez réexécuter la tâche de copie ayant échoué pour recharger à nouveau cette partition spécifique à partir de AWS S3.  Tous les autres travaux de copie qui chargent d’autres partitions ne seront pas affectés. 

### <a name="delta-data-migration"></a>Migration des données Delta 

La méthode la plus efficace pour identifier les fichiers nouveaux ou modifiés à partir de AWS S3 est d’utiliser une convention d’affectation de noms partitionnée : lorsque les données de l’AWS S3 ont été partitionnées avec les informations de tranche de temps dans le nom de fichier ou de dossier (par exemple,/yyyy/mm/dd/file.csv), votre pipeline peut facilement identifier les fichiers/dossiers à copier de façon incrémentielle. 

En guise d’alternative, si vos données de AWS S3 ne sont pas partitionnées, ADF peut identifier les fichiers nouveaux ou modifiés par leur LastModifiedDate.   La façon de procéder est la suivante : ADF analyse tous les fichiers de AWS S3 et copie uniquement le nouveau fichier mis à jour dont la date de dernière modification est supérieur à une certaine valeur.  Sachez que si vous avez un grand nombre de fichiers en S3, l’analyse de fichiers initiale peut prendre beaucoup de temps, quel que soit le nombre de fichiers correspondant à la condition de filtre.  Dans ce cas, vous avez la possibilité de partitionner les données en premier, en utilisant le même paramètre de préfixe pour la migration initiale des instantanés, afin que l’analyse des fichiers puisse se produire en parallèle.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Pour les scénarios qui requièrent le runtime d’intégration auto-hébergé sur une machine virtuelle Azure 

Si vous migrez des données via un lien privé ou si vous souhaitez autoriser une plage d’adresses IP spécifiques sur un pare-feu Amazon S3, vous devez installer le runtime d’intégration auto-hébergé sur une machine virtuelle Windows Azure. 

- La configuration de démarrage recommandée pour chaque machine virtuelle Azure est Standard_D32s_v3 avec des processeurs virtuels 32 et 128 Go de mémoire.  Vous pouvez conserver à surveiller l’utilisation du processeur et de la mémoire de la machine virtuelle IR pendant la migration des données afin de déterminer si vous devez augmenter la taille de la machine virtuelle pour améliorer les performances ou réduire la taille de la machine virtuelle pour réduire les coûts. 
- Vous pouvez également effectuer une montée en charge en associant jusqu’à 4 nœuds de machine virtuelle à un seul runtime d’intégration auto-hébergé.  Une seule tâche de copie exécutée sur un runtime d’intégration auto-hébergé partitionne automatiquement le jeu de fichiers et tire parti de tous les nœuds de machine virtuelle pour copier les fichiers en parallèle.  Pour une haute disponibilité, il est recommandé de commencer par deux nœuds de machine virtuelle pour éviter un point de défaillance unique pendant la migration des données. 

### <a name="rate-limiting"></a>Limitation du débit 

Il est recommandé de procéder à un examen des performances avec un exemple de jeu de données représentatif, afin de pouvoir déterminer la taille de partition appropriée. 

Commencez avec une seule partition et une seule activité de copie avec le paramètre DIU par défaut.  Augmentez progressivement le paramètre DIU jusqu'à atteindre la limite de bande passante de votre réseau ou la limite IOPS/bande passante des magasins de données, ou vous avez atteint le nombre maximal de 256 DIU autorisé pour une activité de copie unique. 

Ensuite, augmentez progressivement le nombre d’activités de copie simultanées jusqu’à ce que vous atteigniez les limites de votre environnement. 

Lorsque vous rencontrez des erreurs de limitation signalées par l'activité de copie de l'ADF, réduisez le paramètre d'accès simultané ou DIU dans l'ADF ou envisagez d'augmenter les limites de bande passante/IOPS des magasins de données et du réseau.  

### <a name="estimating-price"></a>Estimation du coût 

> [!NOTE]
> Il s’agit d’un exemple de tarification hypothétique.  Vos tarifs réels dépendent du débit réel dans votre environnement.

Considérez le pipeline suivant construit pour la migration de données de S3 vers le Stockage Blob Azure : 

![pricing-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Prenons l’exemple suivant : 

- Le volume total de données est de 2 Po 
- Migration de données via HTTPS à l’aide de la première architecture de solution 
- 2 Po est divisé en partitions de 1K et chaque copie déplace une partition 
- Chaque copie est configurée avec DIU = 256 et atteint un débit de 1 Gbit/s. 
- L’accès concurrentiel ForEach est défini sur 2 et le débit agrégé est de 2 Gbits/s 
- Au total, il faut 292 heures pour terminer la migration 

Voici le prix estimé selon les hypothèses ci-dessus : 

![pricing-table](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Références supplémentaires 
- [Connecteur Amazon Simple Storage Service](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Connecteur Stockage Blob Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connecteur Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guide sur les performances et le réglage de l’activité de copie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Créer et configurer un runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Extensibilité et haute disponibilité du runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considérations relatives à la sécurité des déplacements de données](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Stocker les informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copier le fichier de façon incrémentielle en fonction du nom de fichier partitionné](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copier les fichiers nouveaux et modifiés basés sur LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Page de tarification ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modèle

Voici le [modèle](solution-template-migration-s3-azure.md) avec lequel commencer pour migrer des pétaoctets de données composées de centaines de millions de fichiers d’Amazon S3 vers Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Étapes suivantes

- [Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory](solution-template-copy-files-multiple-containers.md)