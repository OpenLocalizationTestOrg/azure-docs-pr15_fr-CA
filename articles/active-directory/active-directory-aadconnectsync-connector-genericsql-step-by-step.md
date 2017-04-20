<properties
   pageTitle="Générique SQL connecteur étape par étape | Microsoft Azure"
   description="Cet article est étudiant en détail un système de ressources humaines simple étape par étape l’utilisation du connecteur SQL générique."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-step-by-step"></a>Pas à pas de connecteur de SQL générique
Cette rubrique est un guide pas à pas. Il crée une base de données des ressources humaines exemple simple et l’utiliser pour l’importation des utilisateurs et leur appartenance au groupe.

## <a name="prepare-the-sample-database"></a>Préparer la base de données exemple
Sur un serveur exécutant SQL Server, exécutez le script SQL de [l’annexe A](#appendix-a). Ce script crée une base de données exemple avec le nom GSQLDEMO. Le modèle objet pour la base de données ressemble à cette image :  
![Modèle d’objet](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Également créer un utilisateur que vous souhaitez utiliser pour vous connecter à la base de données. Dans cette procédure pas à pas, l’utilisateur est appelé FABRIKAM\SQLUser et se trouve dans le domaine.

## <a name="create-the-odbc-connection-file"></a>Créer le fichier de connexion ODBC
Le connecteur SQL générique utilise ODBC pour se connecter au serveur distant. Nous devons tout d’abord créer un fichier avec les informations de connexion ODBC.

1. Démarrez l’utilitaire de gestion ODBC sur votre serveur :  
![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Sélectionnez l’onglet **DSN fichier**. Cliquez sur **Ajouter**.
![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Le pilote d’out-of-box est compatible fines : sélectionner et cliquez sur **Suivant >**.  
![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Donnez au fichier un nom, comme **GenericSQL**.  
![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Cliquez sur **Terminer**.  
![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Temps de configurer la connexion. Donner une bonne description à la source de données et de fournir le nom du serveur exécutant SQL Server.  
![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Sélectionnez la méthode d’authentification avec SQL. Dans ce cas, nous utilisons l’authentification Windows.  
![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Indiquez le nom de la base de données exemple, **GSQLDEMO**.  
![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Conserver tous les éléments par défaut sur cet écran. Cliquez sur **Terminer**.  
![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Pour vérifier que tout fonctionne comme prévu, cliquez sur **Tester la Source de données**.  
![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Vérifiez que le test est réussi.  
![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. Le fichier de configuration ODBC doit maintenant être visible dans le DSN fichier.  
![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Nous avons maintenant le fichier que nous avons besoin et que vous pouvez commencer à créer le connecteur.

## <a name="create-the-generic-sql-connector"></a>Créer le connecteur SQL générique

1. Dans le Gestionnaire de Service de synchronisation UI, sélectionnez **connecteurs** et **créer**. Sélectionnez **SQL générique (Microsoft)** et lui donner un nom descriptif.  
![Connecteur1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Rechercher le fichier DSN que vous avez créé dans la section précédente et de le télécharger sur le serveur. Fournir les informations d’identification pour se connecter à la base de données.  
![C'est-à-dire le connecteur2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. Dans cette procédure pas à pas, nous sont rendant facile pour nous et disons qu’il existe deux types d’objets **utilisateur** et **groupe**.
![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Pour rechercher les attributs, nous souhaitons que le connecteur pour détecter ces attributs en consultant la table elle-même. **Les utilisateurs** étant un mot réservé dans SQL, nous devons fournir de crochets [].  
![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Heure pour définir l’attribut d’ancrage et l’attribut de nom unique. Pour **les utilisateurs**, nous utilisons la combinaison de deux attributs nom de l’employé. Pour un **groupe**, nous utilisons GroupName (pas réaliste dans la réalité, mais pour cette procédure pas à pas, il fonctionne).
![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Pas tous les types d’attributs peuvent être détectés dans une base de données SQL. Le type d’attribut de référence ne peut en particulier. Pour le type d’objet de groupe, nous devons modifier la OwnerID et un ID de membre pour faire référence à.  
![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Les attributs que nous avons choisi comme attributs de référence à l’étape précédente nécessitent le type d’objet ces valeurs sont une référence à. Dans notre cas, le type d’objet utilisateur.  
![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Dans la page Paramètres généraux, sélectionnez **filigrane** en tant que la stratégie de delta. Entrez également dans la date/heure format **AAAA-MM-JJ HH : mm :**.
![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Dans la page **Configuration des Partitions et des hiérarchies** , sélectionnez les deux types d’objet.
![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. **Sélection des Types objet** et **Sélectionner les attributs**, sélectionnez les types d’objet et tous les attributs. Dans la page **Configurer les points d’ancrage** , cliquez sur **Terminer**.

## <a name="create-run-profiles"></a>Créer des profils d’exécution

1. Dans le Gestionnaire de Service de synchronisation UI, sélectionnez **connecteurs**et **Configuration des profils de s’exécuter**. Cliquez sur **nouveau profil**. Nous allons commencer avec une **Importation complète**.  
![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Sélectionnez le type **d’Importation complète (étape uniquement)**.  
![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Sélectionnez la partition **objet = utilisateur**.  
![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Sélectionnez la **Table** et tapez **[utilisateurs]**. Faites défiler jusqu'à la section de type d’objet à valeurs multiples et entrez les données que dans l’image suivante. Sélectionnez **Terminer** pour enregistrer l’étape.
![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png)  
![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Sélectionnez la **nouvelle étape**. Cette fois-ci, sélectionnez **objet = groupe de**. Sur la dernière page, utilisez la configuration comme dans l’illustration suivante. Cliquez sur **Terminer**.  
![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)  
![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Facultatif : Si vous le souhaitez, vous pouvez configurer des profils d’exécution supplémentaires. Pour cette procédure pas à pas, seulement l’importation complète est utilisée.
7. Cliquez sur **OK** pour terminer la modification de profils d’exécution.

## <a name="add-some-test-data-and-test-the-import"></a>Ajouter quelques données test et tester l’importation
Remplir des données de test dans votre base de données exemple. Lorsque vous êtes prêt, sélectionnez **exécuter** et **importation complète**.

Voici un utilisateur avec deux numéros de téléphone et un groupe avec certains membres.  
![CS1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png)  
![CS2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)  

## <a name="appendix-a"></a>Annexe A
**Script SQL pour créer la base de données exemple**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
