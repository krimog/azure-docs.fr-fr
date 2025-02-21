---
title: Migrer des utilisateurs et des groupes SQL ServerWindows vers une instance gérée à l’aide de T-SQL
description: Découvrir plus d’informations sur la migration des utilisateurs et des groupes Windows locaux de SQL Server vers Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 3ed4e4b1d37a9705378281ca74b53a6b60713d97
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807173"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Didacticiel : Migrer les utilisateurs et les groupes Windows locaux de SQL Server vers Azure SQL Database Managed Instance en utilisant la syntaxe DDL T-SQL

> [!NOTE]
> La syntaxe utilisée dans cet article pour migrer des utilisateurs et des groupes vers Managed instance est en **préversion publique**.

Cet article vous guide dans le processus de migration de vos groupes et utilisateurs Windows locaux de SQL Server vers une instance existante d’Azure SQL Database Managed Instance avec la syntaxe T-SQL.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> - Créer des connexions pour SQL Server
> - Créer une base de données de test pour la migration
> - Créer des connexions, des utilisateurs et des rôles
> - Sauvegarder et restaurer votre base de données sur Managed instance
> - Migrer manuellement des utilisateurs vers Managed Instance avec la syntaxe ALTER USER
> - Tester l’authentification avec les nouveaux utilisateurs mappés

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des prérequis suivants :

- Le domaine Windows est fédéré avec Azure Active Directory (Azure AD).
- L’accès à Active Directory pour créer des utilisateurs/groupes.
- Un serveur SQL Server existant dans votre environnement local.
- Une instance managée existante. Consultez [Démarrage rapide : Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
  - Il est nécessaire d’utiliser un `sysadmin` dans l’instance gérée pour créer des connexions Azure AD.
- [Créez un administrateur Azure AD pour l’instance gérée](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Vous pouvez vous connecter à votre instance managée au sein de votre réseau. Pour plus d’informations, consultez les articles suivants : 
    - [Connecter votre application à Azure SQL Database Managed Instance](sql-database-managed-instance-connect-app.md)
    - [Démarrage rapide : Configurer une connexion point à site à une instance managée Azure SQL Database à partir d’un emplacement local](sql-database-managed-instance-configure-p2s.md)
    - [Configurer un point de terminaison public dans Azure SQL Database Managed Instance](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Syntaxe DDL T-SQL

Voici la syntaxe DDL T-SQL utilisée pour prendre en charge la migration des utilisateurs et groupes Windows locaux de SQL Server vers Managed instance avec l’authentification Azure AD.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Arguments

_domainName_</br>
Spécifie le nom de domaine de l’utilisateur.

_userName_</br>
Spécifie le nom de l’utilisateur identifié dans la base de données.

_= loginName\@domainName.com_</br>
Remappe un utilisateur à la connexion Azure AD

_groupName_</br>
Spécifie le nom du groupe identifié dans la base de données.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Partie 1 : Créer des connexions pour les utilisateurs et les groupes locaux SQL Server

> [!IMPORTANT]
> La syntaxe suivante crée une connexion d’utilisateur et de groupe dans votre serveur SQL Server. Vous devez vérifier que l’utilisateur et le groupe existent au sein de votre annuaire Active Directory (AD) avant d’exécuter la syntaxe ci-dessous. </br> </br>
> Utilisateurs : testUser1, testGroupUser </br>
> Groupe : migration - testGroupUser doit appartenir au groupe de migration dans Active Directory

L’exemple ci-dessous crée une connexion dans SQL Server pour un compte nommé _testUser1_ sous le domaine _aadsqlmi_. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Créez une base de données pour ce test.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Partie 2 : Créer des utilisateurs et des groupes Windows, puis ajouter des rôles et des autorisations

Utilisez la syntaxe suivante pour créer l’utilisateur de test.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Vérifiez les autorisations de l’utilisateur :

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Créez un rôle et affectez votre utilisateur de test à ce rôle :

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Utilisez la requête suivante pour afficher les noms d’utilisateur affectés à un rôle spécifique :

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Utilisez la syntaxe suivante pour créer un groupe. Ajoutez ensuite le groupe au rôle `db_owner`.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Créez une table de test et ajoutez des données en utilisant la syntaxe suivante :

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Partie 3 : Sauvegarder et restaurer la base de données utilisateur individuelle sur l’instance managée

Créez une sauvegarde de la base de données de migration en utilisant l’article [Copier des bases de données avec la sauvegarde et la restauration](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), ou bien utilisez la syntaxe suivante :

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Suivez notre [Démarrage rapide : Restaurer une base de données sur une instance managée](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Partie 4 : Migrer les utilisateurs vers l’instance managée

> [!NOTE]
> L’administrateur Azure AD de la fonctionnalité d’instance qui a été gérée après la création a changé. Pour plus d’informations, consultez [Nouvelle fonctionnalité d’administration Azure AD pour MI](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Exécutez la commande ALTER USER pour effectuer le processus de migration sur l’instance managée.

1. Connectez-vous à votre instance managée en utilisant le compte d’administrateur Azure AD pour l’instance gérée. Créez ensuite votre connexion Azure AD dans l’instance managée en utilisant la syntaxe suivante. Pour plus d’informations, consultez [Tutoriel : Sécurité des instances managées dans Azure SQL Database à l’aide de principaux de serveur (connexions) Azure AD](sql-database-managed-instance-aad-security-tutorial.md).

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Vérifiez que votre migration utilise la base de données, la table et les principaux de service corrects.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Utilisez la syntaxe ALTER USER pour mapper l’utilisateur local à la connexion Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Utilisez la syntaxe ALTER USER pour mapper le groupe local à la connexion Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Partie 5 : Test de l’authentification de l’utilisateur ou du groupe Azure AD

Testez l’authentification sur l’instance managée avec l’utilisateur précédemment mappé à la connexion Azure AD en utilisant la syntaxe ALTER USER.
 
1. Connectez-vous à la machine virtuelle fédérée en utilisant votre abonnement Managed Instance avec `aadsqlmi\testUser1`
1. En utilisant SQL Server Management Studio (SSMS), connectez-vous à votre instance managée avec l’authentification **intégrée Active Directory**, en vous connectant à la base de données `migration`.
    1. Vous pouvez également vous connecter en utilisant les informations d’identification testUser1@aadsqlmi.net avec l’option SSMS **Active Directory - Authentification universelle avec prise en charge de MFA**. Cependant, dans ce cas, vous ne pouvez pas utiliser le mécanisme d’authentification unique, et vous devez taper un mot de passe. Vous n’avez pas besoin d’utiliser une machine virtuelle fédérée pour vous connecter à votre instance managée.
1. Dans le cadre du membre de rôle **SELECT**, vous pouvez sélectionner dans la table `test`

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Testez l’authentification auprès d’une instance managée en utilisant un membre d’un groupe Windows `migration`. L’utilisateur `aadsqlmi\testGroupUser` doit avoir été ajouté au groupe `migration` avant la migration.

1. Connectez-vous à la machine virtuelle fédérée en utilisant votre abonnement Managed Instance avec `aadsqlmi\testGroupUser` 
1. En utilisant SSMS avec l’authentification **intégrée Active Directory**, connectez-vous au serveur Managed Instance et à la base de données `migration`
    1. Vous pouvez également vous connecter en utilisant les informations d’identification testGroupUser@aadsqlmi.net avec l’option SSMS **Active Directory - Authentification universelle avec prise en charge de MFA**. Cependant, dans ce cas, vous ne pouvez pas utiliser le mécanisme d’authentification unique, et vous devez taper un mot de passe. Vous n’avez pas besoin d’utiliser une machine virtuelle fédérée pour vous connecter à votre instance managée. 
1. Dans le cadre du rôle `db_owner`, vous pouvez créer une table.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> En raison d’un problème de conception connu pour Azure SQL Database, une instruction de création de table exécutée en tant que membre d’un groupe échoue avec l’erreur suivante : </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> La solution de contournement actuelle consiste à créer une table avec un schéma existant, dans le cas ci-dessus : <dbo.new>

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Effectuer la migration hors connexion d’une instance SQL Server vers une instance managée d’Azure SQL Database à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)