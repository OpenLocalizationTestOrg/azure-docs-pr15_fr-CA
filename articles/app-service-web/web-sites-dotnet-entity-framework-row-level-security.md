<properties
    pageTitle="Didacticiel : Web app avec une base de données partagée à l’aide d’Entity Framework et la sécurité de niveau ligne"
    description="Découvrez comment développer une application web ASP.NET MVC 5 une mutualisée SQL de base de données backent, à l’aide d’Entity Framework et la sécurité de niveau ligne."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="04/25/2016"
    ms.author="thmullan"/>

# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Didacticiel : Web app avec une base de données partagée à l’aide d’Entity Framework et la sécurité de niveau ligne

Ce didacticiel explique comment créer une application web de mutualisée avec un modèle de location «[base de données partagée, schéma partagé](https://msdn.microsoft.com/library/aa479086.aspx)», à l’aide d’Entity Framework et la [Sécurité de niveau ligne](https://msdn.microsoft.com/library/dn765131.aspx). Dans ce modèle, une seule base de données contient les données de nombreux locataires, et chaque ligne de chaque table est associé à une « locataire Réf. ». Sécurité de niveau ligne (RLS), une nouvelle fonctionnalité de base de données SQL Azure est utilisée pour empêcher les locataires d’accéder aux données de l’autre. Cela nécessite seulement une seule, petite modification à l’application. Centralisation de la logique d’accès aux clients au sein de la base de données elle-même, RLS simplifie le code de l’application et réduit les risques de fuite accidentelle des données entre les utilisateurs.

Commençons par l’application du Gestionnaire de contacts simple de [créer une application ASP.NET MVP avec l’authentification et la base de données SQL et le déployer vers Azure Application Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Droit maintenant, l’application permet à tous les utilisateurs (locataires) pour afficher tous les contacts :

![Application du Gestionnaire de contacts avant d’activer le RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Avec seulement quelques petites modifications, nous allons ajouter la prise en charge de l’architecture mutualisée, afin que les utilisateurs sont en mesure de voir uniquement les contacts qui leur appartiennent.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Étape 1 : Ajouter une classe intercepteur dans l’application de définir la SESSION_CONTEXT

Il existe un changement d’application que nous devons faire. Étant donné que tous les utilisateurs de l’application de se connectent à la base de données à l’aide de la même chaîne de connexion (c'est-à-dire même connexion SQL), il n’est actuellement aucun moyen d’une stratégie RLS à savoir à qui il doit filtrer. Cette approche est très courante dans les applications web, car il permet le regroupement de connexions efficace, mais cela signifie que nous avons besoin d’une autre façon d’identifier l’utilisateur actuel de l’application dans la base de données. La solution consiste pour que l’application de la valeur d’une paire clé-valeur pour l’ID d’utilisateur en cours dans le [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) immédiatement après l’ouverture d’une connexion, avant d’exécuter des requêtes. SESSION_CONTEXT est un magasin de clé-valeur de portée de session, et notre politique RLS utilise l’ID utilisateur stockée pour identifier l’utilisateur en cours.

Nous allons ajouter un [intercepteur](https://msdn.microsoft.com/data/dn469464.aspx) (en particulier, un [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), une nouvelle fonctionnalité dans Entity Framework (EF) 6, pour définir automatiquement le nom d’utilisateur en cours dans la SESSION_CONTEXT, en exécutant une instruction T-SQL chaque fois qu’EF ouvre une connexion.

1.  Dans Visual Studio, ouvrez le projet ContactManager.
2.  Avec le bouton droit sur le dossier de modèles dans l’Explorateur de solutions, puis sélectionnez Ajouter > classe.
3.  Nommez la nouvelle classe « SessionContextInterceptor.cs » et cliquez sur Ajouter.
4.  Remplacez le contenu de SessionContextInterceptor.cs par le code suivant.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

C’est la modification de la seule application requise. Continuez et générer et publier l’application.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Étape 2 : Ajouter une colonne de l’ID utilisateur du schéma de base de données

Ensuite, nous devons ajouter une colonne de nom d’utilisateur à la table de Contacts pour associer chaque ligne d’un utilisateur (clients). Nous modifiera le schéma directement dans la base de données, afin que nous n’avons pas à inclure ce champ dans notre modèle de données EF.

Connexion à la base de données directement, en utilisant SQL Server Management Studio ou Visual Studio et puis exécuter le T-SQL suivant :

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Cette opération ajoute une colonne d’identificateur d’utilisateur à la table Contacts. Nous utilisons le type de données nvarchar (128) pour correspondre à l’ID utilisateur stockées dans la table AspNetUsers, et nous créer une contrainte par défaut qui définira automatiquement l’ID utilisateur pour les lignes nouvellement insérées à l’ID utilisateur actuellement stocké dans SESSION_CONTEXT.

La table ressemble maintenant à ceci :

![Table des Contacts de SSMS](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Lors de la création de nouveaux contacts, ils automatiquement est affectés le nom d’utilisateur correct. À des fins de démonstration, toutefois, nous allons attribuer des quelques-unes de ces contacts existants à un utilisateur existant.

Si vous avez créé un petit nombre d’utilisateurs dans l’application déjà (par exemple, l’aide locale, Google ou Facebook comptes), vous allez voir dans la table AspNetUsers. Dans la capture d’écran ci-dessous, il est jusqu’ici qu’un seul utilisateur.

![Table de SSMS AspNetUsers](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Copiez le code de user1@contoso.com, et collez-le dans l’instruction T-SQL ci-dessous. Exécuter cette instruction pour associer trois contacts avec cet ID utilisateur.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Étape 3 : Créer une stratégie de sécurité de niveau ligne dans la base de données

L’étape finale est de créer une stratégie de sécurité qui utilise l’ID utilisateur SESSION_CONTEXT pour filtrer automatiquement les résultats renvoyés par les requêtes.

Bien que toujours connecté à la base de données, exécutez le T-SQL suivant :

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Ce code effectue trois opérations. Tout d’abord, il crée un nouveau schéma en tant que meilleure pratique pour la centralisation et la limitation de l’accès aux objets RLS. Ensuite, il crée une fonction de prédicat qui renverra '1' lorsque l’ID utilisateur d’une ligne correspond à l’ID utilisateur dans SESSION_CONTEXT. Enfin, il crée une stratégie de sécurité qui ajoute cette fonction en tant que prédicat à la fois un filtre et un bloc sur la table Contacts. Le prédicat de filtre entraîne des requêtes retourner uniquement les lignes qui appartiennent à l’utilisateur actuel et le prédicat de bloc agit comme un dispositif de protection pour empêcher l’application de jamais accidentellement insérer une ligne de l’utilisateur incorrect.

Maintenant exécuter l’application et vous connecter en tant que user1@contoso.com. Cet utilisateur voit maintenant uniquement les Contacts que nous avons attribué à cet ID utilisateur :

![Application du Gestionnaire de contacts avant d’activer le RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Pour valider cette supplémentaires, essayez d’enregistrer un nouvel utilisateur. Ils ne verront aucun contact, car aucun ont été affectés. S’ils créent un nouveau contact, il sera affecté à leur, et seulement ils seront en mesure de voir.

## <a name="next-steps"></a>Étapes suivantes

Voilà ! La simple application de web du Gestionnaire de contacts a été convertie en une mutualisée une où chaque utilisateur possède sa propre liste de contacts. En utilisant la sécurité de niveau ligne, nous avons évité la complexité de la mise en œuvre de la logique d’accès aux clients dans le code de notre application. Cette transparence permet à l’application de se concentrer sur le problème d’entreprise réelles à portée de main, et il permet également de réduire les risques de divulguer par inadvertance des données comme base de l’application du code augmente.

Ce didacticiel a seulement gratté la surface de ce qui est possible avec RLS. Par exemple, il est possible d’avoir plus sophistiquées ou logique d’accès granulaire et du possible de stocker de plus que le nom d’utilisateur en cours dans le SESSION_CONTEXT. Il est également possible [d’intégrer RLS avec les bibliothèques d’outils élastique de la base de données client pour prendre en charge plusieurs utilisateurs milieu des fragments dans une couche de données évolutif.](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md)

Au-delà de ces possibilités, nous nous efforçons également pour améliorer encore RLS. Si vous avez des questions, idées ou autres éléments que vous souhaitez voir, faites-le nous savent dans les commentaires. Nous vous remercions pour vos commentaires !
