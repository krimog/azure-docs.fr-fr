---
title: Limites de capacité - Azure Synapse Analytics (anciennement SQL DW)
description: Valeurs maximales autorisées pour les différents composants de SQL Analytics dans Azure Synapse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c4ab9d9cc8007281e0e5729fe883e654107be6fe
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645293"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Limites de capacité Azure Synapse Analytics (anciennement SQL DW)

Valeurs maximales autorisées pour les différents composants d’Azure Synapse.

## <a name="workload-management"></a>Gestion des charges de travail

| Category | Description | Maximale |
|:--- |:--- |:--- |
| [Data Warehouse Units (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DWU max pour une seule unité de pool SQL (entrepôt de données) | Gen1 : DW6000<br></br>Gen2 : DW30000c |
| [Data Warehouse Units (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |La valeur par défaut de DTU par serveur |54 000<br></br>Par défaut, le Quota de DTU de chaque serveur SQL (par exemple, myserver.database.windows.net) est de 54 000, ce qui permet jusqu’à 9 DW6000c. Ce quota constitue simplement une limite de sécurité. Vous pouvez augmenter votre quota en [créant un ticket de support](sql-data-warehouse-get-started-create-support-ticket.md) et en sélectionnant *Quota* comme type de requête.  Pour calculer vos besoins en matière de DTU, multipliez le nombre total de DWU nécessaire par 7,5, ou multipliez le nombre total de cDWU nécessaire par 9,0. Par exemple :<br></br>6 000 DW x 7,5 = 45 000 DTU<br></br>DW6000c x 9,0 = 54 000 DTU.<br></br>Vous pouvez consulter votre consommation de DTU actuelle dans l’option SQL Server dans le portail. Les bases de données suspendues et réactivées sont prises en compte dans le quota de DTU. |
| Connexion de base de données |Sessions simultanées ouvertes maximales |1 024<br/><br/>Le nombre de sessions simultanées ouvertes peut varier en fonction de l’unité DWU sélectionnée. DWU600c et unités supérieures prennent en charge un maximum de 1 024 sessions ouvertes. DWU500c et unités inférieures peuvent prendre en charge un maximum de 512 sessions ouvertes simultanées. Notez qu’il existe des limites sur le nombre de requêtes pouvant s’exécuter simultanément. En cas de dépassement d’une limite de concurrence, la demande est placée dans une file d’attente interne où elle attend d’être traitée. |
| Connexion de base de données |Mémoire maximale pour les instructions préparées |20 Mo |
| [Gestion des charges de travail](resource-classes-for-workload-management.md) |Nombre maximal de requêtes concurrentes |128<br/><br/>  Un maximum de 128 requêtes simultanées est exécuté et les requêtes restantes sont mises en file d’attente.<br/><br/>Le nombre de requêtes simultanées peut diminuer quand les utilisateurs sont affectés à des classes de ressources plus élevées ou quand le paramètre [Data Warehouse Unit]memory-concurrency-limits.md est diminué. Certaines requêtes, comme les requêtes DMV, sont toujours autorisées à s’exécuter et n’affectent pas la limite de requêtes simultanées. Pour plus d’informations sur l’exécution de requêtes simultanées, consultez l’article [Limites maximales pour les requêtes simultanées]memory-concurrency-limits.md). |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Go maximum |399 Go par DW100. Par conséquent, pour DWU1000, la taille de tempdb est 3,99 To. |

## <a name="database-objects"></a>Objets de base de données
| Category | Description | Maximale |
|:--- |:--- |:--- |
| Base de données |Taille maximale | Gen1 : 240 To compressés sur disque. Cet espace est indépendant de tempdb ou de l’espace de journalisation. Par conséquent, cet espace est dédié aux tables permanentes.  La compression du cluster columnstore est estimée à 5 X.  Cette compression permet à la base de données d’atteindre un volume d’environ 1 Po lorsque toutes les tables sont en cluster columnstore (le type de table par défaut). <br/><br/> Gen2 : 240 To pour rowstore et stockage illimité pour les tables columnstore |
| Table |Taille maximale |60 To compressés sur disque |
| Table |Tables par base de données | 100 000 |
| Table |Colonnes par table |1 024 colonnes |
| Table |Octets par colonne |Dépend de la colonne [type de données](sql-data-warehouse-tables-data-types.md). La limite est de 8 000 pour les types de données Char, de 4 000 pour nvarchar ou 2 Go pour les types de données MAX. |
| Table |Octets par ligne, taille définie |8060 octets<br/><br/>Le nombre d’octets par ligne est calculé de la même manière que pour SQL Server avec la compression de page. Comme SQL Server, le stockage de dépassement de ligne est pris en charge, permettant d’envoyer les **colonnes de longueur variable** hors ligne. Lorsque des lignes de longueur variable sont envoyées hors ligne, seule une racine de 24 octets est stockée dans l’enregistrement principal. Pour plus d’informations, consultez [Données de dépassement de ligne de plus de 8 Ko](https://msdn.microsoft.com/library/ms186981.aspx). |
| Table |Partitions par table |15 000<br/><br/>Pour des performances élevées, nous vous recommandons de réduire au minimum le nombre de partitions nécessaires tout en prenant quand même en charge les besoins de votre entreprise. À mesure que le nombre de partitions augmente, la charge pour les opérations Langage de définition de données (DDL) et Langage de manipulation de données (DML) augmente et ralentit les performances. |
| Table |Caractères par valeur limite de partition. |4000 |
| Index |Index non-cluster par table. |50<br/><br/>Applicable uniquement aux tables de stockage de lignes. |
| Index |Index cluster par table. |1<br><br/>Applicable à la fois aux tables de stockage de lignes et de stockage de colonnes. |
| Index |Taille de la clé d’index. |900 octets.<br/><br/>Applicable aux index de stockage de lignes uniquement.<br/><br/>Il est possible de créer des index sur des colonnes varchar d’une taille maximale de plus de 900 octets si les données existantes dans les colonnes n’excèdent pas 900 octets quand l’index est créé. Toutefois, les actions INSERT ou UPDATE ultérieures sur les colonnes, qui amènent la taille totale à dépasser 900 octets, échouent. |
| Index |Colonnes clés par index. |16<br/><br/>Applicable aux index de stockage de lignes uniquement. Les index de stockage de colonnes cluster incluent toutes les colonnes. |
| Statistiques |Taille des valeurs de colonnes combinées. |900 octets. |
| Statistiques |Colonnes par objet de statistiques. |32 |
| Statistiques |Statistiques créées sur les colonnes par table. |30 000 |
| Procédures stockées |Nombre maximal de niveaux d’imbrication. |8 |
| Affichage |Colonnes par vue |1 024 |

## <a name="loads"></a>Charges
| Category | Description | Maximale |
|:--- |:--- |:--- |
| Charges Polybase |60 Mo par ligne |1<br/><br/>Polybase charge les lignes qui sont inférieures à 1 Mo. Le chargement de types de données LOB dans les tables avec un index columnstore en cluster n’est pas pris en charge.<br/><br/> |

## <a name="queries"></a>Requêtes
| Category | Description | Maximale |
|:--- |:--- |:--- |
| Requête |Requêtes mises en file d’attente sur les tables utilisateur. |1 000 |
| Requête |Requêtes simultanées sur les vues système. |100 |
| Requête |Requêtes mises en file d’attente sur les vues système |1 000 |
| Requête |Nombre maximal de paramètres |2 098 |
| Batch |Taille maximale |65 536*4 096 |
| Résultats SELECT |Colonnes par ligne |4096<br/><br/>Une ligne ne peut pas contenir plus de 4 096 colonnes dans le résultat SELECT. Le nombre de 4 096 colonnes n’est pas toujours garanti. Si le plan de requête exige une table temporaire, le maximum de 1 024 colonnes par table peut s’appliquer. |
| SELECT |Sous-requêtes imbriquées |32<br/><br/>Un instruction SELECT ne peut pas contenir plus de 32 sous-requêtes imbriquées. Le nombre de 32 sous-requêtes n’est pas toujours garanti. Par exemple, une instruction JOIN peut introduire une sous-requête dans le plan de requête. Le nombre de sous-requêtes peut aussi être limité par la mémoire disponible. |
| SELECT |Colonnes par JOIN |1 024 colonnes<br/><br/>L’instruction JOIN ne peut pas contenir plus de 1 024 colonnes. Le nombre de 1024 colonnes n’est pas toujours garanti. Si le plan JOIN exige une table temporaire avec davantage de colonnes que le résultat JOIN, la limite de 1 024 s’applique à la table temporaire. |
| SELECT |Octets par colonnes GROUP BY. |8 060<br/><br/>Les colonnes incluses dans la clause GROUP BY peuvent comporter un maximum de 8 060 octets. |
| SELECT |Octets par colonnes ORDER BY |8060 octets<br/><br/>Les colonnes incluses dans la clause ORDER BY peuvent comporter un maximum de 8060 octets. |
| Identificateurs par instruction |Nombre d’identificateurs référencés |65 535<br/><br/> Le nombre d’identificateurs pouvant être contenus dans une seule expression d’une requête est limité. Le dépassement de ce nombre génère l’erreur SQL Server 8632. Pour plus d’informations, consultez la rubrique [Internal error: An expression services limit has been reached (Erreur interne : une limite des services d’expression est dépassée)](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Littéraux de chaîne | Nombre de littéraux de chaîne dans une instruction | 20 000 <br/><br/>Le nombre de constantes de chaîne pouvant être contenus dans une seule expression d’une requête est limité. Le dépassement de ce nombre génère l’erreur SQL Server 8632.|

## <a name="metadata"></a>Métadonnées
| Vue système | Nombre maximal de lignes |
|:--- |:--- |
| Sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Nombre total de rôles de travail DMS pour les 1 000 dernières demandes SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Nombre total d’étapes pour les 1 000 dernières demandes SQL stockées dans sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Les 1 000 dernières demandes SQL stockées dans sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des recommandations sur l’utilisation d’Azure Synapse, consultez [l’Aide-mémoire](cheat-sheet.md).
