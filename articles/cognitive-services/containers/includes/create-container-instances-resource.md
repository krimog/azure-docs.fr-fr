---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource d’instance de conteneur Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051177"
---
## <a name="create-an-azure-container-instance-resource"></a>Créer une ressource d’instance de conteneur Azure

1. Accédez à la page [Créer](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) de Container Instances.

2. Sous l’onglet **De base**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Subscription|Sélectionnez votre abonnement.|
    |Resource group|Sélectionnez le groupe de ressources disponible ou créez-en un tel que `cognitive-services`.|
    |Nom du conteneur|Entrez un nom tel que `cognitive-container-instance`. Ce nom doit être en minuscules.|
    |Location|Sélectionnez une région pour le déploiement.|
    |Type d’image|Si votre image de conteneur est stockée dans un registre de conteneurs qui ne nécessite pas d’informations d’identification, sélectionnez `Public`. Si l’accès à votre image conteneur nécessite des informations d’identification, choisissez `Private`. Reportez-vous [aux référentiels de conteneurs et aux images conteneur](../../cognitive-services-container-support.md#container-repositories-and-images) pour savoir si l’image conteneur est `Public` ou `Private` (« Préversion publique »). |
    |Nom de l’image|Entrez l’emplacement du conteneur Cognitive Services. L’emplacement est utilisé comme argument de la commande `docker pull`. Reportez-vous à [Référentiels et images de conteneur](../../cognitive-services-container-support.md#container-repositories-and-images) pour les noms d’image disponibles et leur référentiel correspondant.<br><br>Le nom d’image doit être complet, c’est-à-dire se composer de trois parties. Tout d’abord, le registre de conteneurs, puis le référentiel et enfin le nom de l’image : `<container-registry>/<repository>/<image-name>`.<br><br>Voici un exemple, `mcr.microsoft.com/azure-cognitive-services/keyphrase` représenterait l’image Extraction de phrases clés dans le registre de conteneurs Microsoft sous le référentiel Azure Cognitive Services. Un autre exemple est `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` qui représenterait l’image Reconnaissance vocale dans le référentiel Microsoft du registre des conteneurs en préversion. |
    |Type de système d’exploitation|`Linux`|
    |Size|Appliquez les suggestions de taille pour votre conteneur Cognitive Services :<br>2 cœurs de processeur<br>4 Go

3. Sur l’onglet **Mise en réseau**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Ports|Définissez le port TCP sur `5000`. Expose le conteneur sur le port 5000.|

4. Sous l’onglet **Avancé**, entrez les **Variables d’environnement** exigées pour les paramètres de facturation du conteneur de la ressource Azure Container Instance :

    | Clé | Valeur |
    |--|--|
    |`apikey`|Copiée de la page **Clés** de la ressource. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiée de la page **Vue d’ensemble** de la ressource.|
    |`eula`|`accept`|

1. Cliquez sur **Examiner et créer**
1. Une fois la validation réussie, cliquez sur **Créer** pour terminer le processus de création
1. Lorsque la ressource est déployée avec succès, elle est prête