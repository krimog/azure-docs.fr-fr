---
title: Personnalisation des mappages d’attributs Azure AD | Microsoft Docs
description: Découvrez ce que sont les mappages d’attributs pour les applications SaaS dans Azure Active Directory et comment les modifier pour répondre aux besoins de votre entreprise.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82c1a536bb86f0b3a4fe6a24af00379686ccc292
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641507"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personnalisation des mappages d’attributs d’attribution d’utilisateurs pour les applications SaaS dans Azure Active Directory

Microsoft Azure AD prend en charge l’approvisionnement d’utilisateurs pour des applications SaaS tierces telles que Salesforce, G Suite et autres. Si vous activez l’approvisionnement d’utilisateurs pour une application SaaS tierce, le portail Azure contrôle ses valeurs d’attributs via des mappages d’attributs.

Il existe un ensemble préconfiguré d’attributs et de mappages d’attributs entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets parallèlement aux Utilisateurs, tels que des Groupes.

Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre organisation. Vous pouvez ainsi modifier ou supprimer des mappages d’attributs existants ou en créer de nouveaux.

## <a name="editing-user-attribute-mappings"></a>Modification des mappages d’attributs utilisateur

Pour accéder à la fonctionnalité **Mappages** de l’approvisionnement d’utilisateurs, procédez comme suit :

1. Connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com).
1. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste de toutes les applications configurées s’affiche, dont les applications qui ont été ajoutées à partir de la galerie.
1. Sélectionnez n’importe quelle application pour charger son panneau de gestion d’application, où vous pouvez afficher des rapports et gérer les paramètres de l’application.
1. Sélectionnez **Approvisionnement** pour gérer les paramètres d’approvisionnement du compte utilisateur de l’application sélectionnée.
1. Développez **Mappages** pour afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible. Cette section permet également de configurer l’approvisionnement de groupes et de comptes d’utilisateurs si l’application prend en charge cette fonctionnalité.

   ![Utilisez des mappages pour afficher et modifier les attributs utilisateur](./media/customize-application-attributes/21.png)

1. Sélectionnez une configuration **Mappages** pour ouvrir l’écran **Mappage d’attributs** associé. Une application SaaS nécessitent certains mappages d’attributs pour fonctionner correctement. Pour les attributs requis, la fonctionnalité **Supprimer** n’est pas disponible.

   ![Utilisez le mappage d’attributs pour les configurer pour les applications](./media/customize-application-attributes/22.png)

   Dans cette capture d’écran, vous pouvez voir que l’attribut **Nom d’utilisateur** d’un objet géré dans Salesforce est renseigné avec la valeur **userPrincipalName** de l’objet Azure Active Directory lié.

1. Sélectionnez un **Mappage d’attributs** pour ouvrir l’écran **Modifier l’attribut**. Ici, vous pouvez modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible.

   ![Utilisez Modifier l’attribut pour modifier les attributs utilisateur](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Présentation des types de mappages d’attributs

Avec les mappages d’attributs, vous contrôlez la façon dont les attributs sont renseignés dans une application SaaS tierce.
Quatre différents types de mappages sont pris en charge :

- **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Azure AD.
- **Constant** : l’attribut cible est renseigné avec une chaîne spécifique que vous avez spécifiée.
- **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression semblable à un script.
  Pour plus d’informations, consultez l’article [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](functions-for-customizing-application-data.md).
- **Aucun** : l’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il est renseigné avec la valeur par défaut que vous spécifiez.

Avec ces quatre types de base, les mappages d’attributs personnalisés prennent en charge le concept d’affectation de valeur **par défaut** facultative. L’affectation de valeur par défaut garantit qu’un attribut cible est renseigné avec une valeur s’il n’existe aucune valeur dans Azure AD ou sur l’objet cible. La configuration la plus courante consiste à laisser ce champ vide.

### <a name="understanding-attribute-mapping-properties"></a>Présentation des propriétés de mappage d’attributs

La section précédente vous a présenté la propriété de type de mappage d’attributs.
Outre cette propriété, les mappages d’attributs prennent en charge les attributs suivants :

- **Attribut source** : attribut utilisateur du système source (par exemple, Azure Active Directory).
- **Attribut cible** : attribut utilisateur dans le système cible (par exemple, ServiceNow).
- **Trouver les objets utilisant cet attribut** : indique si ce mappage doit être utilisé ou pas pour identifier les utilisateurs de manière unique entre les systèmes source et cible. Ce champ est généralement défini sur l’attribut userPrincipalName ou mail dans Azure AD, qui est généralement mappé à un champ de nom d’utilisateur dans une application cible.
- **Priorité des correspondances** : plusieurs attributs de correspondance peuvent être définis. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.
- **Appliquer ce mappage**
  - **Toujours** : appliquez ce mappage à la création de l’utilisateur et aux actions de mise à jour.
  - **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création d’utilisateur.

## <a name="matching-users-in-the-source-and-target--systems"></a>Correspondance des utilisateurs dans les systèmes source et cible
Le service de provisionnement Azure AD peut être déployé dans les deux scénarios Greenfield (les utilisateurs ne quittent pas le système cible) et Brownfield (les utilisateurs existent déjà dans le système cible). Pour prendre en charge les deux scénarios, le service d’approvisionnement utilise le concept de correspondance d’attribut(s). L’attribut ou les attributs correspondants vous permettent de déterminer comment identifier de manière unique un utilisateur dans la source et correspondre à l’utilisateur dans la cible. Dans le cadre de la planification de votre déploiement, identifiez l’attribut qui peut être utilisé pour identifier de manière unique un utilisateur dans les systèmes source et cible. Points à noter :

- **Les attributs correspondants doivent être uniques :** Les clients utilisent souvent des attributs tels que userPrincipalName, le courrier électronique ou l’ID d’objet comme attribut correspondant.
- **Plusieurs attributs peuvent être utilisés comme attributs correspondants :** Vous pouvez définir plusieurs attributs à évaluer lors de la correspondance des utilisateurs et de l’ordre dans lequel ils sont évalués (définis comme priorité correspondante dans l’interface utilisateur). Si, par exemple, vous définissez trois attributs comme attributs correspondants et qu’un utilisateur est mis en correspondance de manière unique après l’évaluation des deux premiers attributs, le service n’évalue pas le troisième attribut. Le service évalue les attributs correspondants dans l’ordre spécifié et arrête l’évaluation lorsqu’une correspondance est trouvée.  
- **La valeur dans la source et la cible n’ont pas besoin de correspondre exactement :** La valeur de la cible peut être une fonction simple de la valeur de la source. Par conséquent, l’une d’elles peut avoir un attribut emailAddress dans la source et userPrincipalName dans la cible, et correspondre à une fonction de l’attribut emailAddress qui remplace certains caractères par une valeur constante.  
- **La correspondance basée sur une combinaison d’attributs n’est pas prise en charge :** La plupart des applications ne prennent pas en charge l’interrogation basée sur deux propriétés et, par conséquent, il n’est pas possible d’effectuer une correspondance basée sur une combinaison d’attributs. Il est possible d’évaluer des propriétés uniques les unes après les autres.
- **Tous les utilisateurs doivent avoir une valeur pour au moins un attribut correspondant :** Si vous définissez un attribut correspondant, tous les utilisateurs doivent avoir une valeur pour cet attribut dans le système source. Si, par exemple, vous définissez userPrincipalName comme attribut de correspondance, tous les utilisateurs doivent avoir un userPrincipalName. Si vous définissez plusieurs attributs correspondants (par exemple, extensionAttribute1 et mail), tous les utilisateurs ne doivent pas avoir le même attribut de correspondance. Un utilisateur peut avoir extensionAttribute1 mais pas mail, alors qu’un autre utilisateur peut avoir mail, mais pas extensionAttribute1. 
- **L’application cible doit prendre en charge le filtrage sur l’attribut correspondant :** Les développeurs d’applications permettent de filtrer un sous-ensemble d’attributs sur leur API d’utilisateur ou de groupe. Pour les applications de la galerie, nous garantissons que le mappage d’attribut par défaut concerne un attribut sur lequel l’API de l’application cible prend en charge le filtrage. Lorsque vous modifiez l’attribut correspondant par défaut pour l’application cible, vérifiez la documentation de l’API tierce pour vous assurer que l’attribut peut être filtré.  

## <a name="editing-group-attribute-mappings"></a>Modification des mappages d’attributs de groupe

Un certain nombre d’applications, telles que ServiceNow, Box et G Suite, prennent en charge la possibilité d’approvisionner des objets Groupe et Utilisateur. Les objets Groupe peuvent contenir des propriétés de groupe telles que des noms d’affichage et des alias d’e-mail en plus des membres du groupe.

![L’exemple montre ServiceNow avec des objets de groupe et d’utilisateur approvisionnés](./media/customize-application-attributes/24.png)

Vous pouvez activer ou désactiver l’approvisionnement de groupe en sélectionnant le mappage de groupe sous **Mappages**, puis en définissant le paramètre **Activé** sur l’option souhaitée dans l’écran **Mappage d’attributs**.

Les attributs approvisionnés en tant que partie d’objets de groupe peuvent être personnalisés de la même manière que les objets utilisateur décrits précédemment. 

> [!TIP]
> L’approvisionnement des objets de groupe (propriétés et membres) est un concept distinct issu de l’[affectation de groupes](assign-user-or-group-access-portal.md) à une application. Il est possible d’affecter un groupe à une application, mais de n’approvisionner que les objets utilisateur contenus dans le groupe. L’approvisionnement des objets de groupe complet n’est pas nécessaire d’utiliser des groupes dans les attributions.

## <a name="editing-the-list-of-supported-attributes"></a>Modification de la liste des attributs pris en charge

Les attributs utilisateur pris en charge pour une application donnée sont préconfigurés. Les API de gestion des utilisateurs de la plupart des applications ne prennent pas en charge la découverte de schéma. Par conséquent, le service d’approvisionnement Azure AD n’est pas en mesure de générer de manière dynamique la liste des attributs pris en charge en appelant l’application.

Cependant, certaines applications prennent en charge les attributs personnalisés, et le service d’approvisionnement Azure AD peut lire et écrire des attributs personnalisés. Pour entrer leurs définitions sur le portail Azure, activez la case **Afficher les options avancées** en bas de l’écran **Mappage d’attributs**, puis sélectionnez **Modifier la liste d’attributs pour**  votre application.

Les applications et les systèmes qui prennent en charge la personnalisation de la liste d’attributs sont les suivantes :

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (les [attributs par défaut de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) et les extensions d’annuaire personnalisées sont pris en charge)
- Les applications qui prennent en charge [SCIM 2.0](https://tools.ietf.org/html/rfc7643), où les attributs définis dans le [schéma principal](https://tools.ietf.org/html/rfc7643) doivent être ajoutés

> [!NOTE]
> La modification de la liste des attributs pris en charge n’est recommandée que pour les administrateurs qui ont personnalisé le schéma de leurs applications et systèmes et ont connaissance de première main de la façon dont leurs attributs personnalisés ont été définis. Ceci nécessite parfois de connaître les API et les outils de développement fournis par une application ou un système.

Lorsque vous modifiez la liste des attributs pris en charge, les propriétés suivantes sont fournies :

- **Name** : le nom du système de l’attribut, tel que défini dans le schéma de l’objet cible.
- **Type** : le type de données stockées par l’attribut, tel que défini dans le schéma de l’objet cible, qui peut être l’un des types suivants :
  - *Binary* : l’attribut contient des données binaires.
  - *Boolean* : l’attribut contient une valeur True ou False.
  - *DateTime* : l’attribut contient une chaîne de date.
  - *Integer* : l’attribut contient un entier.
  - *Référence* : l’attribut contient un ID qui fait référence à une valeur stockée dans une autre table de l’application cible.
  - *String* : l’attribut contient une chaîne de texte.
- **Primary Key?** - L’attribut est défini ou pas comme un champ de clé primaire dans le schéma de l’objet cible.
- **Obligatoire ?** - L’attribut doit ou non être renseigné dans l’application ou le système cible.
- **Multi-value?** - L’attribut prend en charge ou pas plusieurs valeurs.
- **Exact case?** - Les valeurs d’attribut sont ou non évaluées en tenant compte de la casse.
- **API Expression** : ne pas utiliser, sauf si vous êtes invité à le faire dans la documentation relative à un connecteur d’approvisionnement spécifique (tel que Workday).
- **Referenced Object Attribute** : s’il s’agit d’un attribut de type référence, ce menu vous permet de sélectionner la table et l’attribut dans l’application cible qui contient la valeur associée à l’attribut. Par exemple, si vous avez un attribut nommé « Department » dont la valeur stockée fait référence à un objet dans une table « Departments » distincte, sélectionnez « Departments.Name ». Les tables de référence et les champs d’ID primaires pris en charge pour une application donnée sont préconfigurés et ne peuvent actuellement pas être modifiés via le portail Azure, mais qu’ils peuvent être modifiés à l’aide de l’[API Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Pour ajouter un nouvel attribut, faites défiler jusqu'à la fin de la liste des attributs pris en charge, remplissez les champs ci-dessus à l’aide des entrées fournies, puis sélectionnez **ajouter un attribut**. Sélectionnez **Enregistrer** lorsque vous avez fini d’ajouter des attributs. Vous devez ensuite recharger l’onglet **Approvisionnement** pour que les nouveaux attributs soient disponibles dans l’éditeur de mappage d’attributs.
## <a name="provisioning-a-role-to-a-scim-app"></a>Approvisionnement d’un rôle sur une application SCIM
Utilisez les étapes ci-dessous pour approvisionner des rôles pour un utilisateur dans votre application. Notez que la description ci-dessous est spécifique aux applications SCIM personnalisées. Pour les applications de la galerie telles que Salesforce et ServiceNow, utilisez les mappages de rôles prédéfinis. Les puces ci-dessous décrivent comment convertir l’attribut AppRoleAssignments au format attendu par votre application.

- Le mappage de l’attribut appRoleAssignment dans Azure AD à un rôle dans votre application nécessite que vous transformiez l’attribut à l’aide d’une [expression](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data). L’attribut appRoleAssignment **ne doit pas être mappé directement** à un attribut de rôle sans utiliser d’expression pour analyser les détails du rôle. 

- **SingleAppRoleAssignment** 
  - **Quand l’utiliser** : utilisez l’expression SingleAppRoleAssignment pour approvisionner un rôle unique pour un utilisateur et spécifier le rôle principal. 
  - **Comment configurer :** utilisez les étapes décrites ci-dessus pour accéder à la page mappages d’attributs et utilisez l’expression SingleAppRoleAssignment pour mapper à l’attribut de rôles. Vous avez le choix entre trois attributs de rôle : (roles[primary eq "True"].display, roles[primary eq "True].type, et roles[primary eq "True"].value). Vous pouvez choisir d’inclure tout ou partie des attributs de rôle dans vos mappages. Si vous souhaitez en inclure plus d’un, ajoutez simplement un nouveau mappage et incluez-le en tant qu’attribut cible.  
  
  ![Ajouter SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Points importants à prendre en compte**
    - Assurez-vous que plusieurs rôles ne sont pas attribués à un utilisateur. Nous ne pouvons pas garantir le rôle qui sera approvisionné.
    
  - **Exemple de sortie** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **Quand l’utiliser** : Utilisez l’expression AppRoleAssignmentsComplex pour approvisionner plusieurs rôles pour un utilisateur. 
  - **Comment configurer :** Modifiez la liste des attributs pris en charge comme décrit ci-dessus pour inclure un nouvel attribut pour les rôles : 
  
    ![Ajouter des rôles](./media/customize-application-attributes/add-roles.png)<br>

    Utilisez ensuite l’expression AppRoleAssignmentsComplex pour mapper à l’attribut de rôle personnalisé comme indiqué dans l’image ci-dessous :

    ![Ajouter AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Points importants à prendre en compte**
    - Tous les rôles seront approvisionnés en tant que principal = faux.
    - La requête POST contient le type de rôle. La requête PATCH ne contient pas de type. Nous travaillons à l’envoi du type dans les requêtes POST et PATCH.
    
  - **Exemple de sortie** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Approvisionnement d’un attribut à valeurs multiples
Certains attributs, tels que phoneNumbers et emails sont des attributs à valeurs multiples pour lesquels vous devrez peut-être spécifier différents types de numéros de téléphone ou d’adresses e-mail. Utilisez l’expression ci-dessous pour les attributs à valeurs multiples. Elle vous permet de spécifier le type d’attribut et de le mapper à l’attribut d’utilisateur Azure AD correspondant pour la valeur. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restauration des attributs par défaut et des mappages d’attributs

Si vous devez recommencer et réinitialiser vos mappages existants à leur état par défaut, vous pouvez cocher la case **Restaurer les mappages par défaut** et enregistrer la configuration. Cette opération définit tous les mappages comme si l’application venait d’être ajoutée à votre locataire Azure AD à partir de la galerie d’applications.

Cette option entraîne une resynchronisation forcée de tous les utilisateurs pendant l’exécution du service d’approvisionnement.

> [!IMPORTANT]
> Il est fortement recommandé que l’**État de l’approvisionnement** soit défini sur **Désactivé** avant d’appeler cette option.

## <a name="what-you-should-know"></a>Ce que vous devez savoir

- Microsoft Azure AD fournit une implémentation efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités pendant un cycle de synchronisation.
- La mise à jour des mappages d’attributs impacte les performances d’un cycle de synchronisation. Une mise à jour de la configuration des mappages d’attributs nécessite une réévaluation de tous les objets gérés.
- Il est recommandé d’apporter le moins de modifications consécutives possible à vos mappages d’attributs.
- L’ajout d’un attribut de photo à approvisionner pour une application n’est pas actuellement pris en charge, car vous ne pouvez pas spécifier le format de synchronisation de la photo. Vous pouvez demander cette fonctionnalité via [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory).
- L’attribut IsSoftDeleted fait souvent partie des mappages par défaut pour une application. IsSoftdeleted peut avoir la valeur « true » dans l’un des quatre scénarios (l’utilisateur est hors de portée en raison d’une non-affectation de l’application, l’utilisateur est hors de portée en raison de non réponse à un filtre d’étendue, l’utilisateur a été supprimé de manière réversible dans Azure AD ou la propriété AccountEnabled est définie sur « false » pour l’utilisateur). 
- Le service de provisionnement Azure AD ne prend pas en charge le provisionnement de valeurs Null

## <a name="next-steps"></a>Étapes suivantes

- [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](user-provisioning.md)
- [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
- [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](use-scim-to-provision-users-and-groups.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
