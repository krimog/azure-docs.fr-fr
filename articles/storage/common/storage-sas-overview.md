---
title: Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)
description: Apprenez à utiliser des signatures d’accès partagé (SAP) pour déléguer l’accès aux ressources de stockage Azure, notamment les objets blob, les files d’attente, les tables et les fichiers.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9623152bdea5cc56e6b9bcb7d9911a730fd7a4a4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72382009"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)

Une signature d’accès partagé (SAP) fournit un accès délégué sécurisé aux ressources de votre compte de stockage sans compromettre la sécurité de vos données. Avec une SAP, vous avez un contrôle granulaire sur la manière dont un client peut accéder à vos données. Vous pouvez contrôler, entre autres paramètres, les ressources auxquelles le client peut accéder, les autorisations dont il dispose pour ces ressources ainsi que la durée de validité de la SAP, parmi d’autres paramètres.

## <a name="types-of-shared-access-signatures"></a>Types de signatures d’accès partagé

Le service Stockage Azure prend en charge trois types de signatures d’accès partagé :

- **SAP de délégation d’utilisateur (préversion).** Une SAP de délégation d’utilisateur est sécurisée avec les informations d’identification Azure Active Directory (Azure AD) ainsi que par les autorisations spécifiées pour la SAP. Une SAP de délégation d’utilisateur s’applique uniquement au stockage d’objets blob.

    Pour plus d’informations sur la SAP de délégation d’utilisateur, consultez [Créer une SAP de délégation d’utilisateur (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- **SAP de service.** Une SAP de service est sécurisée à l’aide de la clé de compte de stockage. Une SAP de service délègue l’accès à une ressource d’un seul des services de stockage Azure : Stockage Blob, Stockage File d'attente, Stockage Table ou Azure Files. 

    Pour plus d’informations sur la SAP de service, consultez [Créer une SAP de service (API REST)](/rest/api/storageservices/create-service-sas).

- **SAP de compte.** Une SAP de compte est sécurisée à l’aide de la clé de compte de stockage. Une SAP de compte délègue l’accès aux ressources d’un ou plusieurs des services de stockage. Toutes les opérations disponibles via une SAP de service ou de délégation d’utilisateur sont également disponibles via une SAP de compte. En outre, avec la SAP de compte, vous pouvez déléguer l’accès à des opérations qui s’appliquent au niveau du service, telles que **Get/Set Service Properties** et **Get Service Stats**. Vous pouvez également déléguer l'accès aux opérations de lecture, d’écriture et de suppression sur les conteneurs d'objets blob, les tables, les files d'attente et les partages de fichiers qui ne sont pas autorisées avec une SAP de service. 

    Pour plus d’informations sur la SAP de compte, [Créez une SAP de compte (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Comme meilleure pratique de sécurité, Microsoft vous recommande d’utiliser si possible les informations d’identification Azure AD plutôt que d’utiliser la clé de compte qui peut être plus facilement compromise. Lorsque la conception de votre application nécessite des signatures d’accès partagé pour être en mesure d’accéder au Stockage Blob, utilisez les informations d’identification Azure AD pour créer, si possible, une SAP de délégation d’utilisateur pour profiter d’une sécurité supérieure.

Une signature d’accès partagé peut prendre deux formes :

- **SAP ad hoc :** quand vous créez une signature d’accès partagé ad hoc, l’heure de début, l’heure d’expiration et les autorisations associées à cette signature sont spécifiées dans l’URI SAP (ou sont implicites si l’heure de début est omise). Tout type de SAP peut être une SAP ad hoc.
- **SAP de service avec stratégie d'accès stockée :** une stratégie d’accès stockée est définie sur un conteneur de ressources, qui peut être un conteneur d’objets blob, une table, une file d’attente ou un partage de fichiers. La stratégie d'accès stockée et permet de gérer les contraintes d’une ou de plusieurs signatures d’accès partagé de service. Lorsque vous associez une signature d'accès partagé de service à une stratégie d'accès stockée, la signature hérite des contraintes&mdash;heure de début, heure d'expiration et autorisations&mdash;définies pour la stratégie.

> [!NOTE]
> Une SAP de délégation d’utilisateur ou de compte doit être une SAP ad hoc. Les stratégies d’accès stockées ne sont pas prises en charge pour les SAP de délégation d’utilisateur ou les SAP de compte.

## <a name="how-a-shared-access-signature-works"></a>Fonctionnement d’une signature d’accès partagé

Une signature d’accès partagé est un URI signé qui désigne une ou plusieurs ressources de stockage et inclut un jeton qui contient un ensemble spécial de paramètres de requête. Le jeton indique comment le client peut accéder aux ressources. L’un des paramètres de requête, la signature, est construit à partir des paramètres de signature d’accès partagé et signé avec la clé utilisée pour créer la SAP. Cette signature est utilisée par le stockage Azure pour autoriser l’accès à la ressource de stockage.

### <a name="sas-signature"></a>SAP

Vous pouvez signer une SAP de l’une des deux méthodes suivantes :

- Avec une *clé de délégation d’utilisateur* créée à l’aide des informations d’identification d’Azure Active Directory (Azure AD). Une SAP de délégation d’utilisateur est signée avec la clé de délégation d’utilisateur.

    Pour obtenir la clé de délégation d’utilisateur et créer la SAP, un rôle de contrôle d’accès en fonction du rôle (RBAC) incluant l’action **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** doit être attribué à un principal de sécurité Azure AD. Pour plus d’informations sur les rôles RBAC disposant des autorisations nécessaires pour obtenir la clé de délégation d’utilisateur, consultez [Créer une SAP (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- Avec la clé du compte de stockage. Une SAP de service et une SAP de compte sont signées avec la clé du compte de stockage. Pour créer une SAP signée avec la clé de compte, une application doit avoir accès à la clé de compte.

### <a name="sas-token"></a>Jeton SAS

Le jeton SAS est une chaîne que vous générez côté client, par exemple à l’aide de l’une des bibliothèques clientes de stockage Azure. Le jeton SAS n’est suivi par le stockage Azure de quelque manière que ce soit. Vous pouvez créer un nombre illimité de jetons de SAP côté client. Après avoir créé une SAP, vous pouvez la distribuer aux applications clientes qui requièrent l’accès aux ressources de votre compte de stockage.

Lorsqu’une application cliente fournit un URI de SAP au stockage Azure dans le cadre d’une demande, le service vérifie les paramètres et la signature de la SAP pour s’assurer que celle-ci est valide pour l’autorisation de la demande. Si le service vérifie que la signature est valide, la demande est autorisée. Dans le cas contraire, la demande est refusée avec le code d’erreur 403 (Interdit).

Voici un exemple d’URI de SAP de service, montrant l’URI de la ressource et le jeton de la SAP :

![Composants d’une URI de SAP](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quand utiliser une signature d’accès partagé

Utilisez une SAP lorsque vous souhaitez fournir un accès sécurisé aux ressources de votre compte de stockage à tout client qui n’a normalement pas d’autorisations pour ces ressources.

Un service où les utilisateurs lisent et écrivent leurs propres données dans votre compte de stockage correspond à un scénario courant dans lequel une signature d'accès partagé peut s'avérer utile. Dans un scénario où un compte de stockage stocke les données utilisateur, il existe deux modèles de conception types :

1. Les clients chargent et téléchargent les données par le biais d’un service proxy frontal, qui se charge de l’authentification. Ce service présente l'avantage de permettre la validation des règles métier, mais pour de grosses quantités de données ou des transactions à haut volume, la création d'un service qui peut être mis à l'échelle en fonction de la demande peut se révéler coûteuse ou difficile.

   ![Schéma du scénario : service proxy frontal](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Un service léger authentifie le client en fonction des besoins, puis génère une signature d’accès partagé. Une fois que l’application cliente reçoit la signature, il peut accéder aux ressources du compte de stockage directement avec les autorisations définies par la signature d'accès partagé et pendant l'intervalle autorisé par cette dernière. La signature d'accès partagé atténue la nécessité du routage de toutes les données via le service proxy frontal.

   ![Schéma du scénario : service de fournisseur SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

De nombreux services réels peuvent utiliser un mélange de ces deux approches. Par exemple, certaines données peuvent être traitées et validées via le proxy frontal, tandis que les autres données sont enregistrées et/ou lues directement à l’aide de SAP.

En outre, une SAP est requise pour autoriser l’accès à l’objet source d’une opération de copie dans certains cas de figure :

- Quand vous copiez un objet blob dans un autre objet blob qui réside dans un autre compte de stockage, vous devez utiliser une SAP pour autoriser l’accès à l’objet blob source. Vous pouvez éventuellement utiliser une SAP pour autoriser également l’accès à l’objet blob de destination.
- Quand vous copiez un fichier dans un autre fichier qui réside dans un autre compte de stockage, vous devez utiliser une SAP pour autoriser l’accès au fichier source. Vous pouvez éventuellement utiliser une SAP pour autoriser également l’accès au fichier de destination.
- Quand vous copiez un objet blob dans un fichier ou un fichier dans un objet blob, vous devez utiliser une SAP pour autoriser l’accès à l’objet source, même si les objets source et de destination résident dans le même compte de stockage.

## <a name="best-practices-when-using-sas"></a>Bonnes pratiques lors de l’utilisation de SAP

Lorsque vous utilisez des signatures d'accès partagé dans vos applications, vous devez être conscient de deux risques potentiels :

- Si une signature d'accès partagé est divulguée, toute personne qui se la procure peut s'en servir et votre compte de stockage court donc le risque d'être compromis.
- Si une signature d'accès partagé fournie à une application cliente expire et que l'application est incapable d'en récupérer une nouvelle à partir de votre service, la fonctionnalité de votre application risque d'être entravée.

Les recommandations suivantes relatives à l’utilisation des signatures d’accès partagé peuvent aider à limiter ces risques :

- **Utilisez toujours HTTPS** pour créer ou distribuer une signature d’accès partagé. Si une signature d’accès partagé est transmise sur HTTP et interceptée, un pirate qui lance une attaque de type « attaque de l’intercepteur » (man-in-the-middle) peut lire la signature et s’en servir exactement comme l’utilisateur concerné aurait pu le faire, d’où le risque que les données sensibles soient compromises ou que les données soient altérées par l’utilisateur malveillant.
- **Utilisez une SAS de délégation d’utilisateur lorsque c’est possible.** Une SAP de délégation d’utilisateur offre une sécurité supérieure à une SAP de service ou à une SAP de compte. Une SAP de délégation d’utilisateur est sécurisée avec des informations d’identification Azure AD, ce qui vous permet de ne pas avoir à stocker votre clé de compte avec votre code.
- **Ayez un plan de révocation pour une SAP.** Assurez-vous que vous êtes prêt à répondre si une SAP est compromise.
- **Définissez une stratégie d’accès stockée pour une SAP de service.** Celles-ci vous donnent la possibilité de révoquer les autorisations pour une SAP de service sans avoir à régénérer les clés de compte de stockage. Définissez une échéance très éloignée dans le temps (voire infinie) pour l’expiration de ces dernières et veillez à ce qu’elle soit régulièrement mise à jour et repoussée dans le futur.
- **Utilisez des heures d'expiration avec une échéance à court terme sur une SAP de service ou une SAP de compte ad hoc.** De cette manière, même si une signature d’accès partagé est compromise, elle n’est valide que pendant une courte durée. Cette pratique est particulièrement importante si vous ne pouvez pas référencer une stratégie d'accès stockée. Des heures d’expiration avec une échéance à court terme permettent également de limiter la quantité de données pouvant être écrites dans un objet blob en limitant le temps disponible pour le chargement vers ce dernier.
- **Faites en sorte que les clients renouvellent automatiquement la signature d'accès partagé si nécessaire.** Les clients doivent renouveler la signature d’accès partagé bien avant l’heure d’expiration afin de laisser suffisamment de temps pour de nouvelles tentatives, si le service qui fournit la signature est indisponible. Si votre signature d’accès partagé doit être utilisée pour un petit nombre d’opérations immédiates de courte durée, censées être terminées avant l’heure d’expiration, cela ne sera peut-être pas nécessaire, car il n’est pas prévu que la signature d’accès partagé soit renouvelée. Toutefois, si vous avez un client qui effectue régulièrement des demandes par le biais de signatures d'accès partagé, le risque d'expiration est à prendre en compte. La principale considération consiste à trouver un équilibre entre la nécessité que la signature d’accès partagé ait une durée de vie limitée (comme indiqué plus haut) et la nécessité de veiller à ce que le client demande le renouvellement suffisamment tôt pour éviter une interruption due à une expiration de la signature avant le renouvellement effectif.
- **Faites attention à la date de début de la signature d’accès partagé.** Si vous définissez la date de début d’une signature d’accès partagé sur **maintenant**, en raison du décalage d’horloge (différences constatées dans l’heure actuelle sur des machines différentes), des défaillances peuvent être observées par intermittence pendant les premières minutes. En règle générale, définissez une heure de début située au moins 15 minutes avant l’heure courante ou ne la définissez pas du tout, et elle sera alors valide immédiatement dans tous les cas. Cela vaut également d’une manière générale pour l’heure d’expiration. Souvenez-vous que vous pouvez observer jusqu’à 15 minutes de décalage d’horloge (dans l’une ou l’autre direction) sur une demande. Pour les clients qui utilisent une version de REST antérieure à la version 2012-02-12, la durée maximale pour une signature d’accès partagé qui ne renvoie pas à une stratégie d’accès stockée est de 1 heure et toutes les stratégies spécifiant une période plus longue échouent.
- **Soyez précis quant à la ressource pour laquelle vous voulez configurer l'accès.** Une bonne pratique en matière de sécurité consiste à fournir à l’utilisateur les privilèges minimaux requis. Si un utilisateur a besoin d'un accès en lecture à une seule entité, accordez-lui un accès en lecture à cette seule entité, plutôt qu'un accès en lecture/écriture/suppression à toutes les entités. Cela permet également d’atténuer les dégâts si une signature d’accès partagé est compromise, car son pouvoir est moindre entre les mains d’une personne malveillante.
- **Sachez que toute utilisation de votre compte sera facturée, notamment via une SAP.** Si vous fournissez un accès en écriture à un objet blob, un utilisateur peut choisir de charger un objet blob de 200 Go. Si vous lui avez également accordé un accès en lecture, il peut choisir de le télécharger 10 fois, et vous devrez alors acquitter des frais de sortie pour l’équivalent de 2 To. Accordez des autorisations limitées pour atténuer les risques liés aux actions éventuelles d’utilisateurs malveillants. Utilisez des signatures d'accès partagé à durée de vie limitée pour atténuer cette menace (mais pensez au décalage d'horloge pour l'heure de fin).
- **Validez les données écrites avec une signature d'accès partagé.** Lorsqu'une application cliente écrit des données dans votre compte de stockage, n'oubliez pas que ces données peuvent être une source de problèmes. Si votre application exige que ces données soient validées ou autorisées avant de pouvoir être utilisées, vous devez effectuer cette validation après l’écriture des données et avant qu’elles ne soient utilisées par votre application. Cette pratique assure également une protection contre l'écriture de données endommagées ou malveillantes dans votre compte, soit par un utilisateur qui a acquis correctement la signature d'accès partagé, soit par un utilisateur qui exploite sa divulgation.
- **Savoir quand ne pas utiliser une SAP.** Parfois, les risques associés à une opération particulière sur votre compte de stockage l'emportent sur les avantages de l’utilisation d’une signature d'accès partagé. Pour ces opérations, créez un service de niveau intermédiaire qui écrit dans votre compte de stockage après avoir effectué la validation des règles métier, l'authentification et un audit. Parfois aussi, il est plus simple de gérer l'accès par d'autres moyens. Par exemple, si vous voulez que tous les objets blob dans un conteneur soient publiquement lisibles, vous pouvez rendre le conteneur public, au lieu de fournir une signature d'accès partagé à chaque client.
- **Utilisez Azure Monitor et les journaux de stockage Azure pour surveiller votre application.** Vous pouvez utiliser la journalisation Azure Monitor et la journalisation d’analyse de stockage pour observer tout pic dans les échecs d’autorisation dus à une interruption du service de votre fournisseur de signatures d’accès partagé ou à la suppression par inadvertance d’une stratégie d’accès stockée. Pour plus d’informations, consultez [Métriques de stockage Azure dans Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) et [Journalisation Azure Storage Analytics](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Prise en main de la SAP

Pour prendre en main les signatures d’accès partagé, consultez les articles suivants pour chaque type de SAP.

### <a name="user-delegation-sas"></a>SAP de délégation d’utilisateur

- [Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec PowerShell (préversion)](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet BLOB avec l’interface de ligne de commande Azure (préversion)](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec .NET (préversion)](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAP de service

- [Créer une SAP de service pour un conteneur ou un objet blob avec .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>SAP de compte

- [Créer une SAP de compte avec .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes

- [Déléguer l’accès avec une signature d’accès partagé (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Créer une SAP de délégation d’utilisateur (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Créer une SAP de service (API REST)](/rest/api/storageservices/create-service-sas)
- [Créer une SAP de compte (API REST)](/rest/api/storageservices/create-account-sas)
