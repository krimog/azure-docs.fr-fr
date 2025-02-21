---
title: Créer un runtime d’intégration auto-hébergé dans Azure Data Factory
description: Découvrez comment créer un runtime d’intégration auto-hébergé dans Azure Data Factory, permettant aux fabriques de données d’accéder aux magasins de données dans un réseau privé.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 594d4b941f3ed67daa4e1cfd57c2f5539e0cb9ee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677182"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Créer et configurer un runtime d’intégration auto-hébergé
Le runtime d’intégration (IR) représente l’infrastructure de calcul utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. Pour plus d’informations sur le runtime d’intégration (IR), consultez [Runtime d’intégration dans Azure Data Factory](concepts-integration-runtime.md).

Un runtime d’intégration auto-hébergé peut exécuter des activités de copie entre un magasin de données cloud et un magasin de données dans un réseau privé, et répartir des activités de transformation par rapport à des ressources de calcul dans un réseau local ou un réseau virtuel Azure. L’installation d’un runtime d’intégration auto-hébergé nécessite une machine locale ou une machine virtuelle à l’intérieur d’un réseau privé.  

Ce document décrit la façon dont vous pouvez créer et configurer un runtime d’intégration auto-hébergé.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Installation d’un runtime d’intégration auto-hébergé

Pour créer et installer un runtime d’intégration auto-hébergé, vous pouvez utiliser les méthodes suivantes :

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Créer un runtime d’intégration auto-hébergé via Azure PowerShell 

1. Vous pouvez utiliser Azure PowerShell pour cette tâche. Voici un exemple :

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39717) et installez le runtime d’intégration auto-hébergé sur une machine locale.

3. Récupérez la clé d’authentification et inscrivez le runtime d’intégration auto-hébergé à l’aide de la clé. Voici un exemple PowerShell :

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Créer un runtime d’intégration auto-hébergé via l’interface utilisateur Azure Data Factory

Vous pouvez suivre les étapes ci-dessous pour créer un runtime d’intégration auto-hébergé à l’aide de l’interface utilisateur Azure Data Factory. 

1. Dans la page **Prise en main** de l’interface utilisateur Azure Data Factory, sélectionnez l’onglet **Auteur** dans le volet gauche.

   ![Bouton Auteur de la page d’accueil](media/doc-common-process/get-started-page-author-button.png)
2. En bas du volet gauche, cliquez sur **Connexions** et basculez vers **Runtimes d’intégration** dans la fenêtre Connexions. Sélectionnez **+Nouveau**.

   ![Runtime d’intégration](media/create-self-hosted-integration-runtime/new-integration-runtime.png)
3. Dans la fenêtre **Integration runtime setup** (Installation des runtimes d’intégration), sélectionnez *Perform data movement and dispatch activities to external computes* (Effectuer des activités de déplacement et de distribution des données vers des ressources de calcul externes), puis cliquez sur **Continuer**.
4. Entrez un nom pour votre runtime d’intégration, puis sélectionnez **Créer**.
5. Vous pouvez utiliser **Option 1** pour lancer l’installation rapide sur votre ordinateur. Vous pouvez également utiliser **Option 2** pour réaliser une installation manuelle. Les instructions suivantes sont basées sur l’option **Installation manuelle** :

   ![Installation du runtime d’intégration](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copiez et collez la clé d’authentification. Sélectionnez **Download and install integration runtime** (Télécharger et installer le runtime d’intégration).
        
    1. Téléchargez le runtime d’intégration auto-hébergé sur un ordinateur Windows local. Exécutez l’installation.
        
    1. Sur la page **Inscrire le Integration Runtime (auto-hébergé)** , collez la clé que vous avez enregistrée dans la section précédente, puis cliquez sur **Inscrire**.
    
       ![Inscrire le runtime d’intégration](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Dans la page **Nouveau runtime d’intégration (auto-hébergé)** , sélectionnez **Terminer**. 

6. Le message suivant s’affiche une fois que le runtime d’intégration auto-hébergé est bien inscrit :

    ![Inscription réussie](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-azure-vm-via-azure-resource-manager-template"></a>Installer un runtime d’intégration auto-hébergé sur une machine virtuelle Azure via un modèle Azure Resource Manager 
Vous pouvez automatiser la configuration du runtime d’intégration auto-hébergé sur une machine virtuelle Azure à l’aide de [ce modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Ce modèle offre un moyen simple d’avoir un runtime d’intégration auto-hébergé entièrement fonctionnel à l’intérieur d’un réseau virtuel Azure disposant de fonctionnalités de haute disponibilité et d’évolutivité (tant que vous définissez 2 nœuds ou plus).

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Installer un runtime d’intégration auto-hébergé existant via la version locale de PowerShell

Vous pouvez utiliser une ligne de commande pour configurer ou gérer un runtime d’intégration auto-hébergé existant. Cela peut notamment permettre l’automatisation de l’installation et de l’inscription de nœuds du runtime d’intégration auto-hébergé. 

**Dmgcmd.exe** est inclus dans l’installation auto-hébergée, qui généralement située dans le dossier C:\Program Files\Microsoft Integration Runtime\3.0\Shared\. Il prend en charge différents paramètres et peut être appelé à l’aide de l’invite de commandes en utilisant des scripts de commandes par lot pour l’automatisation. 

*Utilisation :* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Détails (paramètres/propriété) :* 

| Propriété                                                    | Description                                                  | Obligatoire |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Inscrire le nœud Integration Runtime (auto-hébergé) avec la clé d’authentification spécifiée | Non       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Activer l’accès à distance sur le nœud actif pour la configuration d’un cluster à haute disponibilité et/ou l’activation du paramètre d’informations d’identification directement sur le runtime d’intégration auto-hébergé (sans passer par le service ADF) à l’aide de l’applet de commande **New-AzDataFactoryV2LinkedServiceEncryptedCredential** à partir d’un ordinateur distant du même réseau. | Non       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Activer l’accès à distance au nœud actif quand le nœud s’exécute dans le conteneur | Non       |
| DisableRemoteAccess                                         | Désactiver l’accès à distance au nœud actif. L’accès à distance est nécessaire pour une configuration à plusieurs nœuds. L’applet de commande PowerShell **New-AzDataFactoryV2LinkedServiceEncryptedCredential** fonctionne toujours même quand l’accès à distance est désactivé, tant qu’elle est exécutée sur le même ordinateur que le nœud du runtime d’intégration auto-hébergé. | Non       |
| Key "`<AuthenticationKey>`"                                 | Remplacer/mettre à jour la clé d’authentification précédente. Soyez prudent, car cela peut entraîner la mise hors connexion de votre nœud du runtime d’intégration auto-hébergé précédent si la clé appartient à un nouveau runtime d’intégration. | Non       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Générer un fichier de sauvegarde pour le nœud actif. Ce fichier inclut la clé du nœud et les informations d’identification du magasin de données | Non       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | Restaurer le nœud à partir d’un fichier de sauvegarde                          | Non       |
| Redémarrer                                                     | Redémarrer le service hôte Integration Runtime (auto-hébergé)   | Non       |
| Start                                                       | Démarrer le service hôte Integration Runtime (auto-hébergé)     | Non       |
| Arrêter                                                        | Arrêter le service de mise à jour d’Integration Runtime (auto-hébergé)        | Non       |
| StartUpgradeService                                         | Démarrer le service de mise à jour d’Integration Runtime (auto-hébergé)       | Non       |
| StopUpgradeService                                          | Arrêter le service de mise à jour d’Integration Runtime (auto-hébergé)        | Non       |
| TurnOnAutoUpdate                                            | Activer la mise à jour automatique d’Integration Runtime (auto-hébergé)        | Non       |
| TurnOffAutoUpdate                                           | Désactiver la mise à jour automatique d’Integration Runtime (auto-hébergé)       | Non       |
| SwitchServiceAccount "<domaine\utilisateur>" ["mot_de_passe"]           | Définir DIAHostService de façon à ce qu’il s’exécute en tant que nouveau compte. Utilisez un mot de passe vide (« ») pour un compte système ou un compte virtuel. | Non       |
| Loglevel `<logLevel>`                                       | Définir le niveau de journalisation ETW (Désactivé, Erreur, Détaillé ou Tous). Généralement utilisé par le support Microsoft pendant le débogage. | Non       |


## <a name="command-flow-and-data-flow"></a>Flux de commandes et flux de données
Lorsque vous déplacez les données entre des machines locales et cloud, l’activité utilise un runtime d’intégration auto-hébergé pour transférer les données d’une source de données locale au cloud et vice versa.

Voici un flux de données global résumant les étapes de copie avec un runtime d’intégration auto-hébergé :

![Vue d’ensemble globale](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Le développeur des données crée un runtime d’intégration auto-hébergé dans une fabrique de données Azure à l’aide d’une cmdlet PowerShell. Actuellement, le portail Azure ne prend pas en charge cette fonctionnalité.
2. Le développeur des données crée un service lié pour un magasin de données local en spécifiant l’instance de runtime d’intégration auto-hébergé qu’il doit utiliser pour se connecter à des magasins de données.
3. Le nœud du runtime d’intégration auto-hébergé chiffre les informations d’identification à l’aide de l’API de protection des données (DPAPI) Windows et les enregistre localement. Si plusieurs nœuds sont définis pour une haute disponibilité, les informations d’identification sont synchronisées sur les autres nœuds. Chaque nœud chiffre les informations d’identification à l’aide de DPAPI et les stocke localement. La synchronisation des informations d’identification est une opération transparente pour le développeur des données, et elle est gérée par le runtime d’intégration auto-hébergé.    
4. Le service Data Factory communique avec le runtime d’intégration auto-hébergé pour la planification et la gestion des travaux par le biais d’un *canal de contrôle* qui utilise un [relai Azure Service Bus Relay](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) partagé. Lorsqu’une tâche de l’activité doit être lancée, Data Factory place en file d’attente la requête ainsi que les informations d’identification (au cas où les informations d’identification ne sont pas déjà stockées sur le runtime d’intégration autohébergé). Le runtime d’intégration auto-hébergé lance le travail après interrogation de la file d’attente.
5. Le runtime d’intégration auto-hébergé copie les données d’un magasin local vers un stockage cloud, ou vice versa selon la configuration de l’activité de copie dans le pipeline de données. Pour cette étape, le runtime d’intégration auto-hébergé communique directement avec les services de stockage cloud comme le stockage Blob Azure via un canal sécurisé (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considérations relatives à l’utilisation du runtime d’intégration auto-hébergé

- Un même runtime d’intégration autohébergé peut servir pour plusieurs sources de données sur site. Un runtime d’intégration auto-hébergé unique peut être partagé avec une autre fabrique de données au sein du même locataire Azure Active Directory. Pour plus d’informations, consultez [Partage du runtime d’intégration auto-hébergé avec plusieurs fabriques de données](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Vous ne pouvez installer qu’une seule instance d’un runtime d’intégration auto-hébergé sur une même machine. Si vous avez deux fabriques de données qui doivent accéder aux sources de données locales, utilisez la [fonctionnalité de partage du runtime d’intégration auto-hébergé](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) pour partager le runtime d’intégration auto-hébergé ou installez le runtime d’intégration auto-hébergé sur deux ordinateurs locaux, un pour chaque fabrique de données.  
- Le runtime d’intégration auto-hébergé n’a pas besoin d’être sur la même machine que la source de données. Toutefois, le fait d’avoir un runtime d’intégration auto-hébergé plus proche de la source de données réduit le temps de connexion du runtime d’intégration auto-hébergé à la source de données. Nous vous recommandons d’installer le runtime d’intégration autohébergé sur un ordinateur différent de celui qui héberge la source de données locale. Lorsque le runtime d’intégration auto-hébergé et la source de données se trouvent sur des machines différentes, le runtime d’intégration auto-hébergé ne demande pas de ressources de la source de données.
- Vous pouvez avoir plusieurs runtimes d’intégration auto-hébergés sur différentes machines connectées à la même source de données locale. Par exemple, vous pouvez avoir deux runtimes d’intégration auto-hébergés utilisés pour deux fabriques de données, alors que la même source de données locale est inscrite auprès des deux fabriques de données.
- Si une passerelle est déjà installée sur votre ordinateur pour un scénario Power BI, installez un runtime d’intégration auto-hébergé distinct pour Azure Data Factory sur une autre machine.
- Le runtime d’intégration auto-hébergé doit être utilisé pour prendre en charge l’intégration des données au sein d’un réseau virtuel Azure.
- Considérez votre source de données comme une source de données locale qui se trouve derrière un pare-feu, même lorsque vous utilisez Azure ExpressRoute. Utilisez le runtime d’intégration autohébergé pour établir la connectivité entre le service et la source de données.
- Vous devez utiliser le runtime d’intégration auto-hébergé même si le magasin de données se trouve dans le cloud sur une machine virtuelle Azure IaaS.
- Les tâches peuvent échouer dans un runtime d’intégration auto-hébergé installé sur un serveur Windows sur lequel le chiffrement compatible FIPS est activé. Pour contourner ce problème, désactivez le chiffrement compatible FIPS sur le serveur. Pour désactiver le chiffrement compatible FIPS, modifiez la valeur de registre suivante de 1 (activé) à 0 (désactivé) : `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Prérequis

- Les versions de système d’exploitation prises en charge sont Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 et Windows Server 2019. L’installation du runtime d’intégration auto-hébergé sur un contrôleur de domaine n’est pas prise en charge.
- .NET Framework 4.6.1 ou version ultérieure est requis. Si vous installez le runtime d’intégration auto-hébergé sur une machine Windows 7, installez .NET Framework 4.6.1 ou une version ultérieure. Consultez [Configuration système requise pour .NET Framework](/dotnet/framework/get-started/system-requirements) pour plus d’informations.
- La configuration recommandée pour la machine du runtime d’intégration auto-hébergé est la suivante : au moins 2 GHz, quatre cœurs, 8 Go de RAM et un disque de 80 Go.
- Si l’ordinateur hôte est en veille prolongée, le runtime d’intégration autohébergé ne répond pas à la demande de données. Configurez un plan d’alimentation approprié sur l’ordinateur avant d’installer le runtime d’intégration auto-hébergé. L’installation du runtime d’intégration autohébergé ouvre un message si l’ordinateur est configuré pour la mise en veille prolongée.
- Vous devez être administrateur sur la machine pour installer et configurer le runtime d’intégration autohébergé avec succès.
- L’activité de copie s’exécute selon une fréquence spécifique. L’utilisation des ressources (UC, mémoire) sur la machine suit le même modèle avec des périodes de pointe et d’inactivité. L'utilisation des ressources dépend également en grande partie de la quantité de données déplacées. Lorsque plusieurs travaux sont en cours, vous constaterez une augmentation des ressources utilisées pendant les heures de pointe.
- Les tâches peuvent échouer lors de l’extraction de données au format Parquet, ORC ou Avro. La création de fichiers s’effectue sur la machine d’intégration auto-hébergée et nécessite que les éléments prérequis ci-après fonctionnent correctement (consultez [Format Parquet dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Package redistribuable Visual C++ 2010 (x64)](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe)
    - Java Runtime Environment (JRE) version 8 provenant d’un fournisseur JRE comme [Adopt OpenJDK](https://adoptopenjdk.net/), assurant la définition de la variable d’environnement `JAVA_HOME`

## <a name="installation-best-practices"></a>Bonnes pratiques d’installation
Vous pouvez installer le runtime d’intégration auto-hébergé en téléchargeant un package de configuration MSI à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Pour des instructions pas à pas, consultez l’article [Déplacement de données entre des sources locales et le cloud](tutorial-hybrid-copy-powershell.md).

- Configurez un plan d’alimentation sur la machine hôte du runtime d’intégration auto-hébergé afin d’empêcher la mise en veille prolongée de la machine. Si cette dernière se met en veille prolongée, le runtime d’intégration auto-hébergé passe à l’état hors connexion.
- Sauvegardez régulièrement les informations d’identification associées au runtime d’intégration autohébergé.
- Pour automatiser les opérations d’installation du runtime d’intégration auto-hébergé, consultez **Installer un runtime d’intégration auto-hébergé existant via PowerShell** dans [cette section](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Installer et inscrire le runtime d’intégration auto-hébergé à partir du Centre de téléchargement

1. Rendez-vous sur la [page de téléchargement du runtime d’intégration Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Sélectionnez **Télécharger**, la version 64 bits (la version 32 bits n’est pas prise en charge), puis **Suivant**.
3. Exécutez le fichier MSI directement ou enregistrez-le sur votre disque dur avant de l’exécuter.
4. Sur la page **Bienvenue**, sélectionnez une langue et cliquez sur **Suivant**.
5. Acceptez les termes du contrat de licence du logiciel Microsoft et sélectionnez **Suivant**.
6. Sélectionnez le **dossier** pour l’installation du runtime d’intégration auto-hébergé et sélectionnez **Suivant**.
7. Sur la page **Prêt pour l’installation**, sélectionnez **Installer**.
8. Cliquez sur **Terminer** pour terminer l’installation.
9. Obtenez la clé d’authentification à l’aide d’Azure PowerShell. Voici un exemple PowerShell pour récupérer la clé d’authentification :

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Sur la page **Inscrire Integration Runtime (auto-hébergé)** du Gestionnaire de configuration de Microsoft Integration Runtime en cours d’exécution sur votre machine, procédez comme suit :

    a. Collez la clé d’authentification dans la zone de texte.

    b. Éventuellement, sélectionnez **Afficher la clé d’authentification** pour afficher le texte de la clé.

    c. Sélectionnez **Inscription**.


## <a name="high-availability-and-scalability"></a>Haute disponibilité et extensibilité
Un runtime d’intégration auto-hébergé peut être associé à plusieurs ordinateurs locaux ou machines virtuelles dans Azure. Ces ordinateurs sont appelés nœuds. Vous pouvez associer jusqu’à quatre nœuds à un runtime d’intégration auto-hébergé. Avoir plusieurs nœuds (machines locales avec une passerelle installée) procure les avantages suivants à une passerelle logique :
* La haute disponibilité du runtime d’intégration auto-hébergé supprime le point de défaillance unique dans votre solution Big Data ou dans l’intégration de vos données cloud avec Azure Data Factory, ce qui garantit la continuité des activités sur un maximum de quatre nœuds.
* Les performances et le débit lors du déplacement des données entre les magasins de données locaux et dans le cloud ont été améliorés. Plus d’informations sur les [comparaisons des performances](copy-activity-performance.md).

Vous pouvez associer plusieurs nœuds en installant le logiciel du runtime d’intégration auto-hébergé à partir du [Centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=39717). Ensuite, inscrivez-le à l’aide des clés d’authentification obtenues par le biais de l’applet de commande **New-AzDataFactoryV2IntegrationRuntimeKey**, comme décrit dans le [tutoriel](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Vous n’avez pas besoin de créer de runtime d’intégration auto-hébergé pour associer chaque nœud. Vous pouvez installer le runtime d’intégration auto-hébergé sur une autre machine et l’inscrire à l’aide de la même clé d’authentification. 

> [!NOTE]
> Avant d’ajouter un autre nœud de haute disponibilité et d’extensibilité, vérifiez que l’option **Accès à distance à partir de l’intranet** est activée sur le premier nœud (**Gestionnaire de configuration Microsoft Integration Runtime** > **Paramètres** > **Accès à distance à partir de l’intranet**). 

### <a name="scale-considerations"></a>Considérations d’échelle

#### <a name="scale-out"></a>Montée en charge

Quand la mémoire disponible sur le runtime d’intégration auto-hébergé est faible et l’utilisation de l’UC est élevée, l’ajout d’un nouveau nœud permet d’effectuer une opération de scale out sur la charge des machines. Si des activités échouent parce qu’elles expirent ou que le nœud du runtime d’intégration auto-hébergé passe à l’état hors connexion, il est judicieux d’ajouter un nœud à la passerelle.

#### <a name="scale-up"></a>Monter en puissance

Quand la mémoire disponible et l’UC ne sont pas correctement utilisées, mais que l’exécution de travaux simultanés atteint la limite, vous devez procéder à une opération de scale up en augmentant le nombre de travaux simultanés pouvant s’exécuter sur un nœud. Vous pouvez également effectuer une opération de scale up quand les activités expirent parce que le runtime d’intégration auto-hébergé est surchargé. Comme le montre l’image suivante, vous pouvez augmenter la capacité maximale d’un nœud :  

![Augmentation des travaux simultanés pouvant s’exécuter sur un nœud](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Configuration requise des certificats TLS/SSL

Voici la configuration requise pour le certificat TLS/SSL utilisé pour sécuriser les communications entre les nœuds de runtime d’intégration :

- Le certificat doit être un certificat X509 v3 approuvé publiquement. Nous vous recommandons d’utiliser des certificats émis par une autorité de certification (partenaire) publique.
- Chaque nœud de runtime d’intégration doit approuver ce certificat.
- Nous vous déconseillons d’utiliser des certificats SAN, car seul le dernier élément SAN est utilisé et tous les autres sont ignorés en raison des limitations actuelles. Par exemple, si vous avez un certificat SAN dont les noms SAN sont **node1.domain.contoso.com** et **node2.domain.contoso.com**, vous pouvez utiliser ce certificat uniquement sur une machine dont le nom de domaine complet est **node2.domain.contoso.com**.
- Le certificat prend en charge toutes les tailles de clé prises en charge par Windows Server 2012 R2 pour les certificats SSL.
- Les certificats qui utilisent des clés CNG ne sont pas pris en charge.  

> [!NOTE]
> Ce certificat est utilisé pour chiffrer les ports sur le nœud du runtime d’intégration auto-hébergé, pour la **communication nœud à nœud** (à des fins de synchronisation d’état, ce qui inclut la synchronisation des informations d’identification des services liés entre des nœuds) et lors de l’**utilisation de l’applet de commande PowerShell pour le paramètre d’informations d’identification du service lié** à partir du réseau local. Nous vous conseillons d'utiliser ce certificat si votre environnement de réseau privé n'est pas sécurisé ou si vous souhaitez également sécuriser la communication entre les nœuds de votre réseau privé. Le déplacement des données en transit d'un runtime d'intégration auto-hébergé vers d'autres magasins de données se fait toujours via un canal chiffré, que ce certificat soit défini ou non. 

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Créer un runtime d’intégration auto-hébergé partagé dans Azure Data Factory

Vous pouvez réutiliser une infrastructure existante de runtime d’intégration auto-hébergé que vous avez déjà configurée dans une fabrique de données. Cela vous permet de créer un *runtime d’intégration auto-hébergé lié* dans une fabrique de données différente en référençant un runtime d’intégration auto-hébergé (partagé) existant.

La vidéo suivante est une présentation et une démonstration de 12 minutes de cette fonctionnalité :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Runtime d’intégration partagé** : le runtime d’intégration autohébergé d’origine qui est exécuté sur une infrastructure physique.  
- **Runtime d’intégration lié** : le runtime d’intégration qui fait référence à un autre runtime d’intégration partagé. Il s’agit d’un runtime d’intégration logique qui utilise l’infrastructure d’un autre runtime d’intégration autohébergé (partagé).

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Méthodes pour partager un runtime d’intégration auto-hébergé

Pour partager un runtime d’intégration auto-hébergé avec plusieurs fabriques de données, consultez [cette instruction](create-shared-self-hosted-integration-runtime-powershell.md) pour plus d’informations.

### <a name="monitoring"></a>Surveillance

- **IR partagé**

  ![Sélections pour la recherche d’un runtime d’intégration partagé](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Onglet pour la supervision](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **IR lié**

  ![Sélections pour la recherche d’un runtime d’intégration lié](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Onglet pour la supervision](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitations connues du partage de runtime d’intégration autohébergé

* La fabrique de données dans laquelle un runtime d’intégration lié va être créé doit avoir une identité [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Par défaut, les fabriques de données créées dans le Portail Azure ou les cmdlets PowerShell disposent d’une identité MSI créée implicitement. Toutefois, lorsque la fabrique de données est créée à l’aide d’un modèle Azure Resource Manager ou d’un Kit de développement logiciel (SDK), la propriété **Identité** doit être définie explicitement pour garantir qu’Azure Resource Manager crée une fabrique de données contenant une identité MSI. 

* Le Kit de développement logiciel (SDK) Azure Data Factory .NET version 1.1.0 ou ultérieure prend en charge cette fonctionnalité.

* Pour accorder l’autorisation, l’utilisateur a besoin du rôle Propriétaire ou du rôle Propriétaire hérité dans la fabrique de données dans laquelle se trouve le runtime d’intégration partagé.

* La fonctionnalité de partage fonctionne uniquement pour les fabriques de données relevant du même locataire Azure Active Directory.

* Pour les [utilisateurs invités](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) Active Directory, la fonctionnalité de recherche (répertoriant toutes les fabriques de données à l’aide d’un mot-clé de recherche) de l’interface utilisateur [ne fonctionne pas](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Mais, tant que l’utilisateur invité est le propriétaire de la fabrique de données, il peut partager le runtime d’intégration sans la fonctionnalité de recherche. Pour ce faire, il lui suffit de taper directement l’identité MSI de la fabrique de données avec laquelle le runtime d’intégration doit être partagé dans la zone de texte **Assign Permission** (Assigner une autorisation) et de sélectionner **Ajouter** dans l’interface utilisateur d’Azure Data Factory. 

  > [!NOTE]
  > Cette fonctionnalité est uniquement disponible dans Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Notifications et icônes de la zone de notification

Si vous déplacez votre curseur sur les icônes ou les messages de la zone de notification, vous obtenez des informations supplémentaires sur l’état du runtime d’intégration auto-hébergé.

![Notifications dans la zone de notification](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Ports et pare-feu

Deux pare-feu sont importants : le *pare-feu d’entreprise*, exécuté sur le routeur central de l’organisation et le *pare-feu Windows*, configuré en tant que démon sur la machine locale sur laquelle le runtime d’intégration auto-hébergé est installé.

![Pare-feu](media/create-self-hosted-integration-runtime/firewall.png)

Au niveau du *pare-feu d’entreprise*, vous devez configurer les domaines et ports de sortie suivants :

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


Au niveau du *pare-feu Windows* (niveau de la machine), ces ports de sortie sont normalement activés. Dans le cas contraire, vous pouvez configurer les domaines et les ports en conséquence sur une machine du runtime d’intégration auto-hébergé.

> [!NOTE]
> Selon votre source et vos récepteurs, vous devez peut-être autoriser des domaines et des ports de sortie supplémentaires dans votre pare-feu d’entreprise ou Windows.
>
> Pour certaines bases de données cloud (par exemple, Azure SQL Database et Azure Data Lake), vous devez peut-être autoriser les adresses IP des machines du runtime d’intégration auto-hébergé dans la configuration du pare-feu.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copier des données d’une source vers un récepteur

Assurez-vous que les règles de pare-feu sont correctement activées sur le pare-feu d’entreprise, sur le pare-feu Windows de la machine du runtime d’intégration auto-hébergé, ainsi que sur le magasin de données lui-même. Activer ces règles permet au runtime d’intégration autohébergé de se connecter correctement à la source et au récepteur. Activez les règles pour chaque magasin de données impliqué dans l’opération de copie.

Par exemple, pour effectuer une copie à partir d’un magasin de données local vers un récepteur Azure SQL Database ou un récepteur Azure SQL Data Warehouse, procédez comme suit :

1. Autorisez le trafic TCP sortant sur le port 1433 pour le pare-feu Windows et le pare-feu d’entreprise.
2. Configurez les paramètres de pare-feu de la base de données Azure SQL pour ajouter l’adresse IP de la machine du runtime d’intégration auto-hébergé à la liste des adresses IP autorisées.

> [!NOTE]
> Si votre pare-feu n’autorise pas le port de sortie 1433, le runtime d’intégration auto-hébergé ne peut pas accéder directement à la base de données Azure SQL. Dans ce cas, vous pouvez effectuer une [copie intermédiaire](copy-activity-performance.md) vers Azure SQL Database et Azure SQL Data Warehouse. Dans ce scénario, vous auriez uniquement besoin du protocole HTTPS (port 443) pour le déplacement des données.

## <a name="proxy-server-considerations"></a>Considérations relatives aux serveurs proxy

Si votre environnement de réseau d’entreprise utilise un serveur proxy pour accéder à Internet, configurez le runtime d’intégration auto-hébergé pour utiliser les bons paramètres de proxy. Vous pouvez définir le proxy lors de la phase initiale de l’enregistrement.

![Spécifier le proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Une fois configuré, le runtime d’intégration auto-hébergé utilise le serveur proxy pour se connecter au service cloud, à la source et la destination (ceux utilisant le protocole HTTP/HTTPS). Sélectionnez **Changer le lien**  pendant la configuration initiale. La boîte de dialogue des paramètres de proxy s’affiche.

![Définir le proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Il existe trois options de configuration :

- **Ne pas utiliser de proxy** : le runtime d’intégration autohébergé n’utilise pas explicitement de proxy pour se connecter aux services cloud.
- **Utiliser le proxy système** : le runtime d’intégration autohébergé utilise le paramètre de proxy configuré dans diahost.exe.config et diawp.exe.config. Si aucun proxy n’est configuré dans diahost.exe.config et diawp.exe.config, le runtime d’intégration auto-hébergé se connecte au service cloud directement sans passer par un proxy.
- **Utiliser un proxy personnalisé** : configurez les paramètres du proxy HTTP à utiliser pour le runtime d’intégration auto-hébergé au lieu d’utiliser les configurations dans diahost.exe.config et diawp.exe.config. Les champs **Adresse** et **Port** doivent être renseignés. Les champs **Nom d’utilisateur** et **Mot de passe** sont facultatifs, en fonction des paramètres d’authentification de votre proxy. Tous les paramètres sont chiffrés avec Windows DPAPI sur le runtime d’intégration autohébergé et stockés localement sur l’ordinateur.

Le service hôte du runtime d’intégration autohébergé redémarre automatiquement après avoir enregistré les paramètres de proxy mis à jour.

Une fois le runtime d’intégration auto-hébergé inscrit, si vous souhaitez afficher ou mettre à jour les paramètres de proxy, utilisez le Gestionnaire de configuration Integration Runtime.

1. Ouvrez le **Gestionnaire de configuration de Microsoft Integration Runtime**.
2. Basculez vers l’onglet **Paramètres** .
3. Sélectionnez le lien **Modifier** dans la section **Serveur proxy HTTP** pour ouvrir la boîte de dialogue **Définir le proxy HTTP**.
4. Sélectionnez **Suivant**. Vous pouvez voir un avertissement demandant l’autorisation d’enregistrer les paramètres de proxy et de redémarrer le service hôte du runtime d’intégration.

Vous pouvez afficher et mettre à jour le proxy HTTP à l’aide de l’outil Gestionnaire de configuration.

![Afficher le proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Si vous configurez un serveur proxy avec l’authentification NTLM, le service hôte du runtime d’intégration s’exécute sous le compte du domaine. Si vous modifiez le mot de passe du compte du domaine ultérieurement, veillez à mettre à jour les paramètres de configuration pour le service et à redémarrer ce dernier en conséquence. En raison de cette exigence, nous vous conseillons d’utiliser un compte de domaine dédié qui ne nécessite pas de mettre à jour le mot de passe fréquemment pour accéder au serveur proxy.

### <a name="configure-proxy-server-settings"></a>Configurer les paramètres du serveur proxy

Si vous sélectionnez le paramètre **Utiliser le proxy système** pour le proxy HTTP, le runtime d’intégration auto-hébergé utilise le paramètre du proxy dans diahost.exe.config et diawp.exe.config. Si aucun proxy n’est spécifié dans diahost.exe.config et diawp.exe.config, le runtime d’intégration auto-hébergé se connecte au service cloud directement sans passer par le proxy. La procédure suivante fournit des instructions pour mettre à jour le fichier diahost.exe.config :

1. Dans l’Explorateur de fichiers, effectuez une copie de sauvegarde de C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config pour sauvegarder le fichier d’origine.
2. Ouvrez Notepad.exe en tant qu’administrateur, puis ouvrez le fichier texte C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Localisez la balise par défaut pour system.net comme indiqué dans le code suivant :

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Vous pouvez ensuite ajouter les détails du serveur proxy comme illustré dans l’exemple suivant :

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Vous pouvez ajouter des propriétés supplémentaires à l’intérieur de la balise de proxy pour spécifier les paramètres requis comme `scriptLocation`. Reportez-vous à la page [proxy, élément (paramètres réseau)](https://msdn.microsoft.com/library/sa91de1e.aspx) pour connaître la syntaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Enregistrez le fichier config dans l’emplacement d’origine. Redémarrez ensuite le service hôte du runtime d’intégration auto-hébergé, qui relève les modifications. 

   Pour redémarrer le service, utilisez l’applet des services dans le Panneau de configuration. Ou, dans le Gestionnaire de configuration Integration Runtime, sélectionnez le bouton **Arrêter le service**, puis **Démarrer le service**. 
   
   Si le service ne démarre pas, il est probable qu’une syntaxe de balise XML incorrecte ait été ajoutée dans le fichier de configuration de l’application que vous avez modifié.

> [!IMPORTANT]
> N’oubliez pas de mettre à jour diahost.exe.config et diawp.exe.config.

Vous devez également vérifier que Microsoft Azure figure dans la liste autorisée de votre entreprise. Vous pouvez télécharger la liste des adresses IP Microsoft Azure valides à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Symptômes possibles des erreurs liées au pare-feu et au serveur proxy

Si vous rencontrez l’une des erreurs suivantes, cela signifie que vous avez probablement mal configuré le serveur proxy ou le pare-feu, ce qui empêche le runtime d’intégration auto-hébergé de se connecter à Data Factory pour s’authentifier. Pour vous assurer que votre pare-feu et votre serveur proxy sont correctement configurés, reportez-vous à la section précédente.

* Lorsque vous tentez d’inscrire le runtime d’intégration autohébergé, vous recevez le message d’erreur suivant : Échec d’inscription de ce nœud Runtime d’intégration ! Vérifiez que la clé d’authentification est valide et que le service hôte d’intégration est en cours d’exécution sur cet ordinateur.
* Lorsque vous ouvrez le Gestionnaire de configuration Integration Runtime, l’état indiqué est **Déconnecté** ou **En cours de connexion**. Lorsque vous affichez les journaux des événements Windows, sous **Observateur d’événements** > **Journaux des applications et services** > **Microsoft Integration Runtime**, des messages d’erreur tels que le suivant s’affichent :

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Activation de l’accès à distance à partir d’un intranet 

Si vous utilisez PowerShell pour chiffrer les informations d’identification à partir d’une machine (dans le réseau) autre que celle où est installé le runtime d’intégration auto-hébergé, vous pouvez activer l’option **Accès à distance à partir de l’intranet**. Si vous exécutez PowerShell pour chiffrer les informations d’identification sur la même machine que celle où est installé le runtime d’intégration auto-hébergé, vous ne pouvez pas activer l’option **Accès à distance à partir de l’intranet**.

Vous devez activer l’option **Accès à distance à partir de l’Intranet** avant d’ajouter un autre nœud de haute disponibilité et d’extensibilité.  

Pendant la configuration du runtime d’intégration auto-hébergé (version 3.3.xxxx.x et versions ultérieures), par défaut, l’installation du runtime d’intégration auto-hébergé désactive l’option  **Accès à distance à partir de l’Intranet** sur la machine du runtime d’intégration auto-hébergé.

Si vous utilisez un pare-feu tiers, vous pouvez ouvrir manuellement le port 8060 (ou le port configuré par l’utilisateur). Si vous rencontrez des problèmes de pare-feu lors de la configuration du runtime intégration auto-hébergé, essayez d’utiliser la commande suivante pour installer le runtime intégration auto-hébergé sans configurer le pare-feu :

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Si vous préférez ne pas ouvrir le port 8060 sur la machine du runtime intégration auto-hébergé, utilisez d’autres mécanismes que l’application Définition des informations d’identification pour configurer les informations d’identification du magasin de données. Vous pouvez, par exemple, utiliser l’applet de commande PowerShell **New-AzDataFactoryV2LinkedServiceEncryptCredential**.


## <a name="next-steps"></a>Étapes suivantes
Pour des instructions pas à pas, consultez le didacticiel suivant : [Didacticiel : Copier des données locales dans le cloud](tutorial-hybrid-copy-powershell.md).
