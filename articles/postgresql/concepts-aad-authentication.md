---
title: Authentification Azure Active Directory avec Azure Database pour PostgreSQL - Serveur unique
description: En savoir plus sur les concepts d’Azure Active Directory pour l’authentification avec Azure Database pour PostgreSQL - Serveur unique
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 47637396581beeafb0748066cd6a66f011e8eaa1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512098"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Utiliser Azure Active Directory pour l’authentification avec PostgreSQL

L’authentification Microsoft Azure Active Directory (Azure AD) est un mécanisme de connexion à Azure Database pour PostgreSQL à l’aide d’identités définies dans Azure AD.
Avec l’authentification Azure AD, vous pouvez gérer les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé, ce qui simplifie la gestion des autorisations.

> [!IMPORTANT]
> L’authentification Azure AD pour Azure Database pour PostgreSQL est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les avantages liés à l’utilisation d’Azure AD incluent ce qui suit :

- Authentification uniforme des utilisateurs dans les services Azure
- Gestion des stratégies de mot de passe et de la rotation de mot de passe dans un emplacement unique
- Formes d’authentification multiples prises en charge par Azure Active Directory, ce qui peut éviter d’avoir à stocker les mots de passe
- Les clients peuvent gérer les autorisations de base de données à l’aide des groupes (Azure AD) externes.
- L’authentification Azure AD utilise des rôles de base de données PostgreSQL pour authentifier les identités au niveau de la base de données
- Prise en charge de l’authentification basée sur des jetons pour les applications se connectant à Azure Database pour PostgreSQL

Pour configurer et utiliser l’authentification Azure Active Directory, procédez comme suit :

1. Créez et remplissez Azure Active Directory avec les identités des utilisateurs, si nécessaire.
2. Le cas échéant, associez ou modifiez l’annuaire Active Directory actuellement associé à votre abonnement Azure.
3. Créez un administrateur Azure AD pour le serveur Azure Database pour PostgreSQL.
4. Créer des utilisateurs de base de données dans votre base de données mappés sur les identités Azure AD.
5. Connectez-vous à votre base de données en extrayant un jeton pour une identité Azure AD et en ouvrant une session.

> [!NOTE]
> Pour apprendre à créer et à remplir Azure AD, puis à configurer Azure AD avec Azure SQL Database pour PostgreSQL, consultez [Configurer et se connecter avec Azure AD pour Azure Database pour PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Architecture

Le diagramme général suivant résume le fonctionnement de l’authentification Azure AD avec Azure Database pour PostgreSQL. Les flèches indiquent les voies de communication.

![flux d’authentification][1]

## <a name="administrator-structure"></a>Structure de l’administrateur

En cas d’utilisation de l’authentification Azure AD, il existe deux comptes administrateur pour le serveur PostgreSQL ; l’administrateur de PostgreSQL d’origine et l’administrateur Azure AD. Seul l’administrateur basé sur un compte Azure AD peut créer le premier utilisateur de la base de données autonome Azure AD dans une base de données utilisateur. La connexion d’administrateur Azure AD peut être un utilisateur Azure AD ou un groupe Azure AD. Lorsque l’administrateur est un compte de groupe, il peut être utilisé par n’importe quel membre du groupe, activant plusieurs administrateurs Azure AD pour le serveur PostgreSQL. L’utilisation d’un compte de groupe en tant qu’administrateur facilite la gestion en vous permettant d’ajouter et de supprimer des membres du groupe dans Azure AD sans modifier les utilisateurs ou les autorisations du serveur PostgreSQL. Seul un administrateur Azure AD (utilisateur ou groupe) peut être configuré à tout moment.

![structure admin][2]

## <a name="permissions"></a>Autorisations

Pour créer des utilisateurs qui peuvent s’authentifier avec Azure AD, vous devez disposer du rôle `azure_ad_admin` dans la base de données. Ce rôle est attribué en configurant le compte d’administrateur Azure AD pour un serveur Azure Database pour PostgreSQL spécifique.

Pour créer un utilisateur de base de données Azure AD, vous devez vous connecter en tant qu’administrateur Azure AD. Cela est illustré dans [Configurer et se connecter avec Azure AD pour Azure Database pour PostgreSQL](howto-configure-sign-in-aad-authentication.md).

L’authentification Azure AD n’est possible que si l’administrateur Azure AD a été créé pour Azure Database pour PostgreSQL. Si l’administrateur Azure Active Directory a été supprimé du serveur, les utilisateurs Azure Active Directory existants créés précédemment ne peuvent plus se connecter à la base de données à l’aide de leurs informations d’identification Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Connexion à l’aide des identités Azure AD

L’authentification Azure Active Directory prend en charge les méthodes suivantes de connexion à une base de données à l’aide d’identités Azure AD :

- Mot de passe Azure Active Directory
- Intégration d’Azure Active Directory
- Authentification universelle Azure Active Directory avec MFA
- Utilisation des certificats ou de clés secrètes client d’application Active Directory

Une fois authentifié auprès d’Active Directory, vous récupérez un jeton. Ce jeton est votre mot de passe de connexion.

> [!NOTE]
> Pour plus d’informations sur la façon de se connecter avec un jeton Active Directory, consultez [Configurer et se connecter avec Azure AD pour Azure Database pour PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Considérations supplémentaires

- Pour améliorer la facilité de gestion, nous vous conseillons de mettre en service un groupe Azure AD dédié en tant qu’administrateur.
- Un seul utilisateur administrateur Azure AD (utilisateur ou groupe) peut être configuré pour un serveur Azure Database pour PostgreSQL à tout moment.
- Seul un administrateur d’Azure AD pour PostgreSQL peut se connecter initialement au serveur Azure Database pour PostgreSQL à l’aide d’un compte Azure Active Directory. L’administrateur Active Directory peut configurer les utilisateurs de base de données Azure AD suivants.
- Si un utilisateur est supprimé d’Azure AD, il ne pourra plus s’authentifier auprès d’Azure AD, et il ne sera donc plus possible d’acquérir un code d'accès pour cet utilisateur. Dans ce cas, bien que le rôle correspondant se trouve toujours dans la base de données, il ne sera pas possible de se connecter au serveur avec lui.
> [!NOTE]
> La connexion à l’aide de l’utilisateur Azure AD supprimé peut toujours être effectuée jusqu’à l’expiration du jeton (60 minutes maximum après l’émission du jeton).  Si vous supprimez également l’utilisateur de la base de données Azure pour PostgreSQL, cet accès sera immédiatement révoqué.
- Si l’administrateur Azure AD est supprimé du serveur, ce dernier n’est plus associé à un locataire Azure AD et, par conséquent, toutes les connexions Azure AD sont désactivées pour le serveur. L’ajout d’un nouvel administrateur Azure AD à partir du même locataire réactivera les connexions Azure AD.
- Azure Database pour PostgreSQL met en correspondance les jetons d’accès au rôle Azure Database pour PostgreSQL à l’aide de l’ID d’utilisateur Azure AD unique de l’utilisateur au lieu d’utiliser le nom d’utilisateur. Cela signifie que si un utilisateur Azure AD est supprimé d’Azure AD et qu’un nouvel utilisateur est créé avec le même nom, Azure Database pour PostgreSQL considère que le nouvel utilisateur est différent de l’utilisateur précédent. Par conséquent, si un utilisateur est supprimé d’Azure AD et qu’un nouvel utilisateur portant le même nom a été ajouté, le nouvel utilisateur ne sera pas en mesure de se connecter au rôle existant. Pour y remédier, l’administrateur Azure AD d’Azure Database pour PostgreSQL doit révoquer puis accorder le rôle « azure_ad_user » à l’utilisateur afin d’actualiser l’ID d’utilisateur Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- Pour apprendre à créer et à remplir Azure AD, puis à configurer Azure AD avec Azure SQL Database pour PostgreSQL, consultez [Configurer et se connecter avec Azure AD pour Azure Database pour PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Pour obtenir une vue d’ensemble des connexions, des utilisateurs et des rôles de base de données Azure Database pour PostgreSQL, consultez [Créer des utilisateurs dans Azure Database pour PostgreSQL - Serveur unique](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
