---
title: Ajouter ou supprimer des utilisateurs relevant d’une appartenance de groupe dynamique - tutoriel - Azure Active Directory
description: Dans le cadre de ce didacticiel, vous utilisez des groupes avec des règles d’appartenance des utilisateurs pour ajouter ou supprimer automatiquement des utilisateurs.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1429841ca1376d67c7372f36bd35694afd4cd7ce
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102625"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Didacticiel : Ajouter ou supprimer automatiquement des membres dans un groupe

Dans Azure Active Directory (Azure AD), vous pouvez ajouter ou supprimer automatiquement des utilisateurs dans des groupes de sécurité ou des groupes Office 365, ce qui vous évite d’avoir à le faire manuellement. Chaque fois que l’une des propriétés d’un utilisateur ou d’un appareil change, Azure AD évalue toutes les règles de groupe dynamique dans votre locataire pour déterminer si cette modification doit ajouter ou supprimer des membres.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un groupe automatiquement rempli d’utilisateurs invités d’une entreprise partenaire
> * Attribuer des licences au groupe pour les fonctionnalités propres au partenaire afin de permettre aux utilisateurs invités d’y accéder
> * Bonus : sécuriser le groupe **Tous les utilisateurs** en supprimant les utilisateurs invités, par exemple en vue d’autoriser les utilisateurs membres à accéder aux sites à usage interne

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous devez disposer d’une licence Azure AD Premium en tant qu’administrateur général du locataire. Si vous ne possédez pas cette licence, accédez à Azure AD et sélectionnez **Licences** > **Produits** > **Essayer/Acheter**.

Vous n’êtes pas tenu d’attribuer des licences aux utilisateurs pour que ces derniers puissent appartenir à des groupes dynamiques. Vous devez uniquement disposer du nombre minimal de licences Azure AD Premium P1 disponibles dans le locataire pour couvrir l’ensemble de ces utilisateurs. 

## <a name="create-a-group-of-guest-users"></a>Créer un groupe d’utilisateurs invités

Vous allez commencer par créer un groupe de vos utilisateurs invités qui proviennent tous d’une même entreprise partenaire. Ces utilisateurs nécessitent une licence spéciale ; il est donc généralement plus efficace de créer un groupe à cet effet.

1. Connectez-vous au portail Azure (https://portal.azure.com) en utilisant le compte d’administrateur général de votre locataire.
2. Sélectionnez **Azure Active Directory** > **Groupes** > **Nouveau groupe**.
   ![Sélectionner la commande permettant de démarrer un nouveau groupe](./media/groups-dynamic-tutorial/new-group.png)
3. Dans le panneau **Groupe** :
  
   * Sélectionnez le type de groupe **Sécurité**.
   * Entrez `Guest users Contoso` en guise de nom et de description du groupe.
   * Redéfinissez **Type d’appartenance** sur **Utilisateur dynamique**.
   
4. Sélectionnez **Propriétaires** puis, dans le panneau **Ajouter des propriétaires**, recherchez les propriétaires souhaités. Cliquez sur les propriétaires que vous voulez ajouter à la sélection.
5. Cliquez sur **Sélectionner** pour fermer le panneau **Ajouter des propriétaires**.  
6. Sélectionnez **Modifier la requête dynamique** dans la zone **Membres utilisateurs dynamiques**.
7. Dans le panneau **Règles d’appartenance dynamique** :

   * Dans le champ **Propriété**, cliquez sur la valeur existante et sélectionnez **userType**. 
   * Dans le champ **Opérateur**, vérifiez que **Est égal à** est sélectionné.  
   * Sélectionnez le champ **Valeur** et entrez **Invité**. 
   * Cliquez sur le lien hypertexte **Ajouter une expression** pour ajouter une autre ligne.
   * Dans le champ **Et/Ou**, sélectionnez **Et**.
   * Dans le champ **Propriété**, sélectionnez **companyName**.
   * Dans le champ **Opérateur**, vérifiez que **Est égal à** est sélectionné.
   * Dans le champ **Valeur**, entrez **Contoso**.
   * Cliquez sur **Enregistrer** pour fermer le panneau **Règles d’appartenance dynamique**.
   
8. Dans le panneau **Groupe**, sélectionnez **Créer** pour créer le groupe.

## <a name="assign-licenses"></a>Attribuer des licences

Maintenant que vous disposez de votre nouveau groupe, vous pouvez appliquer les licences requises par les utilisateurs partenaires.

1. Dans Azure AD, sélectionnez **Licences**, sélectionnez une ou plusieurs licences, puis sélectionnez **Attribuer**.
2. Sélectionnez **Utilisateurs et groupes**, sélectionnez le groupe **Guest users Contoso**, puis enregistrez vos modifications.
3. Les **Options d’affectation** vous permettent d’activer ou de désactiver les plans de service inclus dans les licences que vous avez sélectionnées. Lorsque vous modifiez une valeur, veillez à cliquer sur **OK** pour enregistrer cette modification.
4. Pour terminer l’affectation, dans le volet **Affecter une licence**, cliquez sur **Affecter** au bas du volet.

## <a name="remove-guests-from-all-users-group"></a>Supprimer les invités du groupe Tous les utilisateurs

Votre plan administratif final consiste peut-être à répartir l’ensemble de vos utilisateurs invités dans des groupes correspondant à l’entreprise à laquelle ils appartiennent. Vous pouvez également modifier le groupe **Tous les utilisateurs** afin de le réserver exclusivement aux utilisateurs membres de votre locataire. Vous pouvez ensuite utiliser ce groupe pour attribuer des applications et des licences propres à votre organisation d’origine.

   ![Modifier le groupe Tous les utilisateurs pour le réserver aux membres](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

**Pour supprimer le groupe d’utilisateurs invités**

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant le compte d’administrateur général de votre locataire.
2. Sélectionnez **Azure Active Directory** > **Groupes**. Sélectionnez le groupe **Guest users Contoso**, sélectionnez les points de suspension (...), puis sélectionnez **Supprimer**. Lorsque vous supprimez le groupe, toutes les licences attribuées sont également supprimées.

**Pour restaurer le groupe Tous les utilisateurs**
1. Sélectionnez **Azure Active Directory** > **Groupes**. Sélectionnez le nom du groupe **Tous les utilisateurs** afin d’ouvrir le groupe.
1. Sélectionnez **Règles d’appartenance dynamique**, effacez la totalité du texte de la règle, puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :
> [!div class="checklist"]
> * Créer un groupe d’utilisateurs invités
> * Attribuer des licences à votre nouveau groupe
> * Modifier le groupe Tous les utilisateurs pour le réserver aux membres

Passez à l’article suivant pour en savoir plus sur les principes de base des licences basées sur les groupes.
> [!div class="nextstepaction"]
> [Principes de base des licences basées sur les groupes](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



