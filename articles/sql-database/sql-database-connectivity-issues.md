<properties
    pageTitle="Corriger une erreur de connexion SQL, une erreur passagère | Microsoft Azure"
    description="Apprenez à dépanner, diagnostiquer et empêcher une erreur de connexion SQL ou transitoire dans la base de données de SQL Azure. "
    keywords="connexion SQL, chaîne de connexion, problèmes de connectivité, erreur transitoire, erreur de connexion"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Dépanner, diagnostiquer et éviter les erreurs transitoires et les erreurs de connexion SQL pour la base de données de SQL

Cet article décrit comment empêcher, dépanner, diagnostiquer et réduire les erreurs de connexion et que votre application cliente lorsqu’il interagit avec la base de données de SQL Azure des erreurs transitoires. Apprenez à configurer la logique des nouvelles tentatives, générer la chaîne de connexion et ajuster d’autres paramètres de connexion.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>En régime transitoire erreurs (défaillances temporaires)

Une erreur passagère - en outre, une défaillance transitoire - a une cause sous-jacente qui va bientôt se résoudre d’elle-même. Une cause occasionnelle des erreurs transitoires est le système Azure rapidement déplace les ressources matérielles pour mieux équilibrer la charge des charges de travail différentes. Plupart de ces événements de reconfiguration complètes souvent en moins de 60 secondes. Au cours de cette période de reconfiguration, vous pouvez avoir des problèmes de connectivité à la base de données de SQL Azure. Applications se connectant à la base de données de SQL Azure doivent être générées pour attendre ces erreurs transitoires, de les gérer en mettant en œuvre la logique des nouvelles tentatives dans le code plutôt que les surfaces aux utilisateurs sous la forme d’erreurs d’application.

Si votre programme client utilise ADO.NET, votre programme est informé l’erreur transitoire par la levée d’une **exception SqlException**. La propriété **Number** peut être comparée à la liste des erreurs transitoires près du haut de la rubrique : [codes d’erreur SQL pour les applications de client de base de données SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Connexion par rapport à la commande

Vous allez tenter une nouvelle connexion SQL ou établir de nouveau, selon les éléments suivants :

* **Une erreur temporaire qui se produit au cours d’une tentative de connexion**: la connexion devrait être recommencée après retarder de plusieurs secondes.

* **Une erreur temporaire qui se produit au cours d’une commande de requête SQL**: la commande ne doit pas être retentée immédiatement. Au lieu de cela, après un certain délai, la connexion doit être fraîchement établie. Puis, la commande peut être tentée.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Réessayer la logique des erreurs transitoires


Les programmes clients qui parfois rencontrent une erreur transitoire sont plus robustes lorsqu’ils contiennent la logique des nouvelles tentatives.


Lorsque votre programme communique avec la base de données de SQL Azure via un middleware de partie 3, vous renseigner auprès du fournisseur si le middleware contient la logique de nouvelle tentative pour les erreurs transitoires.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Principes de la nouvelle tentative


- Une tentative d’ouverture d’une connexion doit être relancée si l’erreur est transitoire.


- Une instruction SELECT de SQL qui échoue avec une erreur passagère ne doit pas être relancée directement.
 - Au lieu de cela, établir une nouvelle connexion et recommencez la sélectionner.


- Lorsqu’une instruction SQL UPDATE échoue avec une erreur passagère, doit établir une connexion fraîche avant nouvelle tentative de la mise à jour.
 - La logique de nouvelle tentative doit s’assurer que la transaction de base de données complète terminée, ou que la transaction entière est restaurée.


#### <a name="other-considerations-for-retry"></a>Autres considérations pour réessayer


- Un programme de commandes qui est automatiquement démarré après les heures de travail, et qui se termine avant le matin, peut vous permettre de très patient avec le long des intervalles de temps entre les tentatives.


- Un programme de l’interface utilisateur doit comptabiliser la tendance d’abandonner après une attente de trop longue.
 - Toutefois, la solution ne doit pas être pour réessayer toutes les quelques secondes, car cette stratégie peut inonder le système avec les demandes.


#### <a name="interval-increase-between-retries"></a>Augmentation de l’intervalle entre les tentatives



Nous vous recommandons de vous reporter pendant 5 secondes avant votre première tentative. Nouvelle tentative après un délai plus court que les risques de 5 secondes submerger le service en nuage. Pour chaque tentative ultérieure le délai doit croître de manière exponentielle, avec un maximum de 60 secondes.

Une description de la *période de blocage* pour les clients qui utilisent ADO.NET est disponible dans [Le pool de connexion serveur SQL (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Vous pourriez également définir le nombre maximal de nouvelles tentatives avant que le programme s’arrête automatiquement.


#### <a name="code-samples-with-retry-logic"></a>Exemples de code avec la logique de nouvelle tentative


Avec la logique des nouvelles tentatives, dans différents langages de programmation, des exemples de code sont disponibles sur :

- [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Tester votre logique de nouvelle tentative


Pour tester votre logique de nouvelle tentative, vous devez simuler ou provoquer une erreur que vous pouvez être corrigées pendant que votre programme est en cours d’exécution.


##### <a name="test-by-disconnecting-from-the-network"></a>Test par la déconnexion du réseau


Un moyen de tester votre logique des nouvelles tentatives est de déconnecter votre ordinateur client à partir du réseau alors que le programme est en cours d’exécution. L’erreur est :

- **SqlException.Number** = 11001
- Message : « aucun hôte de ce type n’est connu »


Dans le cadre de la première tentative, votre programme peut corriger la faute d’orthographe et puis essayez de vous connecter.


Pour rendre cette pratique, vous déconnectez votre ordinateur à partir du réseau avant de démarrer votre programme. Puis, votre programme reconnaît un paramètre d’exécution que le programme :

1. Ajoutez provisoirement 11001 à sa liste d’erreurs à considérer comme transitoires.
2. Comme d’habitude, essayez de sa première connexion.
3. Une fois que l’erreur est interceptée, supprimez 11001 à partir de la liste.
4. Affiche un message indiquant à l’utilisateur à brancher l’ordinateur sur le réseau.
 - Pause plus l’exécution à l’aide de la méthode **Console.ReadLine** ou une boîte de dialogue avec un bouton OK. L’utilisateur appuie sur la touche ENTRÉE après que l’ordinateur connecté au réseau.
5. Tenter à nouveau de vous connecter, attendu de réussite.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Test par la faute d’orthographe dans le nom de la base de données lors de la connexion


Votre programme ne peut volontairement mal orthographié le nom d’utilisateur avant la première tentative de connexion. L’erreur est :

- **SqlException.Number** = 18456
- Message : « échouée de la connexion pour l’utilisateur 'WRONG_MyUserName'. »


Dans le cadre de la première tentative, votre programme peut corriger la faute d’orthographe et puis essayez de vous connecter.


Pour que cela soit possible, votre programme peut reconnaître un paramètre d’exécution que le programme :

1. Ajoutez provisoirement 18456 à sa liste d’erreurs à considérer comme transitoires.
2. Volontairement, ajoutez 'WRONG_' pour le nom d’utilisateur.
3. Une fois que l’erreur est interceptée, supprimez 18456 à partir de la liste.
4. Supprimer « WRONG_ » à partir du nom de l’utilisateur.
5. Tenter à nouveau de vous connecter, attendu de réussite.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Paramètres de .NET SqlConnection pour nouvelle tentative de connexion


Si votre programme client se connecte à la base de données de SQL Azure en utilisant le.NET Framework classe **System.Data.SqlClient.SqlConnection**, vous devez utiliser .NET 4.6.1 ou version ultérieure pour pouvoir valoriser sa fonctionnalité de nouvelle tentative de connexion. Détails de la fonction sont [ici](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Lorsque vous générez la [chaîne de connexion](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pour votre objet **SqlConnection** , vous devez coordonner les valeurs entre les paramètres suivants :

- ConnectRetryCount &nbsp; &nbsp; *(valeur par défaut est 1. Est comprise entre 0 et 255.)*
- ConnectRetryInterval &nbsp; &nbsp; *(valeur par défaut est 1 seconde. Plage est 1 à 60)*
- Délai de connexion &nbsp; &nbsp; *(valeur par défaut est 15 secondes. Plage va de 0 à 2147483647)*


En particulier, les valeurs choisies doivent rendre le vrai l’égalité suivante :

- Délai de connexion = ConnectRetryCount * ConnectionRetryInterval

Par exemple, si le nombre = 3 et intervalle = 10 secondes, un délai de seulement 29 secondes pas assez donnerait le système suffisamment de temps pour ses tentatives de 3e et dernière au niveau de la connexion : 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Connexion par rapport à la commande


Les paramètres **ConnectRetryCount** et **ConnectRetryInterval** permettent à votre objet **SqlConnection** réessayer l’opération de connexion sans indiquant ou déranger votre programme, par exemple rendre le contrôle à votre programme. Les tentatives peuvent se produire dans les situations suivantes :

- appel de la méthode mySqlConnection.Open
- appel de la méthode mySqlConnection.Execute

Il existe plusieurs méthodes. Si une erreur passagère se produit pendant l’exécution de votre *requête* , votre objet **SqlConnection** ne pas recommencer l’opération de connexion, et certainement qu’il ne tente pas de votre requête. Toutefois, **SqlConnection** vérifie rapidement la connexion avant d’envoyer votre requête pour l’exécution. Si la vérification rapide détecte un problème de connexion, **SqlConnection** réessaie l’opération de connexion. Si la tentative réussit, votre requête est envoyée pour exécution.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount doit être combiné avec la logique de nouvelle tentative d’application ?

Supposons que votre application possède la logique des nouvelles tentatives de personnalisé robuste. Il peut reprendre l’opération de connexion de 4 heures. Si vous ajoutez **ConnectRetryInterval** et **ConnectRetryCount** = 3 à la chaîne de connexion, vous augmentez le nombre de nouvelles tentatives 4 * 3 = 12 nouvelles tentatives. Vous pourriez l’utiliser pas un grand nombre de nouvelles tentatives.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Connexions de base de données SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>La connexion : Chaîne de connexion


La chaîne de connexion nécessaire pour se connecter à la base de données de SQL Azure est légèrement différente de la chaîne de connexion à Microsoft SQL Server. Vous pouvez copier la chaîne de connexion pour votre base de données à partir du [Portail Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Connexion : Adresse IP


Vous devez configurer le serveur de base de données SQL pour accepter les communications provenant de l’adresse IP de l’ordinateur qui héberge votre programme client. Pour cela, vous devez en modifiant les paramètres du pare-feu via le [Portail Azure](https://portal.azure.com/).


Si vous oubliez de configurer l’adresse IP, votre programme échouera avec un message d’erreur de pratique qui indique l’adresse IP nécessaire.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Pour plus d’informations, consultez : [Comment : configurer les paramètres du pare-feu sur la base de données de SQL](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Connexion : Ports


En général vous devez seulement pour vous assurer que le port 1433 est ouvert pour les communications sortantes, sur l’ordinateur hébergeant programme client.


Par exemple, lorsque votre programme client est hébergé sur un ordinateur Windows, le pare-feu Windows sur l’hôte vous permet d’ouvrir le port 1433 :


1. Ouvrez le panneau de contrôle
2. &gt;Tous les éléments du Panneau de contrôle
3. &gt;Pare-feu Windows
4. &gt;Paramètres avancés
5. &gt;Règles de trafic sortant
6. &gt;Actions
7. &gt;Nouvelle règle


Si votre programme client est hébergé sur un ordinateur virtuel Azure (VM), vous devez lire :<br/>Les [ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


Pour plus d’informations sur cofiguration de ports et d’adresse IP, consultez : [pare-feu de base de données de SQL Azure](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Connexion : ADO.NET 4.6.1


Si votre programme utilise des classes ADO.NET comme **System.Data.SqlClient.SqlConnection** pour se connecter à la base de données de SQL Azure, nous vous conseillons d’utiliser le.NET Framework version 4.6.1 ou supérieure.


ADO.NET 4.6.1 :

- Pour la base de données SQL Azure est une fiabilité améliorée lors de l’ouverture d’une connexion à l’aide de la méthode **SqlConnection.Open** . La méthode **Open** incorpore désormais des mécanismes de nouvelle tentative meilleurs effort en réponse à des défaillances temporaires, pour certaines erreurs dans le délai de connexion.
- Prend en charge le regroupement de connexion. Il s’agit d’un contrôle efficace du fonctionnement de l’objet de connexion qu’il donne à votre programme.



Lorsque vous utilisez un objet de connexion à partir d’un pool de connexions, nous vous recommandons que votre programme fermer temporairement la connexion lorsque vous l’utilisez pas immédiatement. Rouvrir une connexion n’est pas coûteuse est de la façon de créer une nouvelle connexion.


Si vous utilisez ADO.NET 4.0 ou version antérieure, nous vous recommandons de vous mettre à niveau vers la dernière ADO.NET.

- À partir de novembre 2015, vous pouvez [Télécharger ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Tests de diagnostic

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostic : Les tests si les utilitaires peuvent se connecter.


Si votre programme ne peut pas se connecter à la base de données de SQL Azure, une option de diagnostic consiste à essayer de vous connecter à un programme utilitaire. Idéalement, l’utilitaire doit se connecter à l’aide de la même bibliothèque que votre programme utilise.


Sur un ordinateur Windows, vous pouvez essayer ces utilitaires :

- SQL Server Management Studio (ssms.exe), qui se connecte à l’aide de ADO.NET.
- SQLCMD.exe, qui se connecte à l’aide de [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Une fois connecté, testez une requête SQL sélectionnez short fonctionne.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostics : Vérifier les ports ouverts


Supposons que vous pensez que les tentatives de connexion échouent en raison de problèmes de port. Sur votre ordinateur, vous pouvez exécuter un utilitaire qui génère des rapports sur les configurations de ports.


Sous Linux, les utilitaires suivants peuvent être utiles :

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Modifier la valeur exemple est votre adresse IP.)


Sous Windows la [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilitaire peut être utile. Voici l’exécution de l’exemple qui interrogé la situation port sur un serveur de base de données de SQL Azure, et qui a été exécuté sur un ordinateur portable :


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostic : Journaux vos erreurs


Un problème intermittent est parfois mieux diagnostiqué par détection d’un modèle général sur des jours ou des semaines.


Votre client peut aider à un diagnostic par consigner toutes les erreurs qu’il rencontre. Vous ne pourrez pas établir une corrélation entre les entrées du journal avec les données d’erreur base de données de SQL Azure se connecte lui-même en interne.


Enterprise Library 6 (EntLib60) offre des classes .NET gérés pour vous aider à l’enregistrement :

- [5 - aussi simple que de tomber à un journal : en utilisant le bloc d’Application de journalisation](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostics : Examinez les erreurs dans les journaux système


Voici certaines instructions Transact-SQL Sélectionnez cette requête les journaux d’erreur et d’autres informations.


| Requête du journal | Description |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La vue [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) contient des informations sur les événements individuels, y compris celles qui peuvent provoquer des erreurs transitoires ou des échecs de connectivité.<br/><br/>Dans l’idéal, vous pouvez corréler les valeurs **start_time** ou **end_time** avec des informations lorsque votre programme client a rencontré des problèmes.<br/><br/>**Conseil :** Vous devez vous connecter à la base de données **master** pour effectuer cette opération. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | La vue [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) offre nombre agrégé de types d’événements, de diagnostic supplémentaires.<br/><br/>**Conseil :** Vous devez vous connecter à la base de données **master** pour effectuer cette opération. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostics : Recherchez des événements de problème dans le journal de la base de données SQL


Vous pouvez rechercher des entrées relatives aux événements de problème dans le journal de la base de données de SQL Azure. Essayez l’instruction Transact-SQL SELECT suivante, en attente dans la base de données **master** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Quelques lignes renvoyées à partir de sys.fn_xe_telemetry_blob_target_read_file


Next est à quoi peut ressembler une ligne retournée. Les valeurs null ne sont souvent pas null dans d’autres lignes.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Bibliothèque d’entreprise 6


Enterprise Library 6 (EntLib60) est une structure de classes .NET qui vous permet de mettre en œuvre des clients robustes des services en nuage, y compris le service de base de données de SQL Azure. Vous pouvez rechercher des rubriques dédiés pour chaque domaine dans lequel les EntLib60 peut vous aider en visitant le site premier :

- [Enterprise Library 6 – avril 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Logique des nouvelles tentatives pour la gestion des erreurs transitoires est une zone dans laquelle les EntLib60 peut vous aider :

- [4 - perseverance, le Secret de toutes les luttes : utilisant le bloc d’Application de gestion des erreurs transitoires](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] Le code source de EntLib60 est disponible pour le public le [Télécharger](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft n’a aucuns prévu d’effectuer d’autres mises à jour de la maintenance ou de mises à jour de la fonctionnalité à EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes de EntLib60 pour les erreurs transitoires et réessayer


Les classes EntLib60 suivantes sont particulièrement utiles pour la logique de nouvelle tentative. Tous ces sont en cours, ou plus sous l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*Dans l’espace de noms* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- Classe de **RetryPolicy**
 - Méthode **ExecuteAction**


- Classe de **ExponentialBackoff**


- Classe de **SqlDatabaseTransientErrorDetectionStrategy**


- Classe de **ReliableSqlConnection**
 - **ExecuteCommand** , méthode


Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- Classe de **AlwaysTransientErrorDetectionStrategy**

- Classe de **NeverTransientErrorDetectionStrategy**


Voici des liens vers des informations sur EntLib60 :

- Libre [téléchargement du carnet de : Guide du développeur de Microsoft Enterprise Library, 2e édition](http://www.microsoft.com/download/details.aspx?id=41145)

- Meilleures pratiques : [Réessayer conseils généraux](../best-practices-retry-general.md) a une excellente discussion approfondie de la logique des nouvelles tentatives.

- Téléchargement de NuGet [d’Enterprise Library - bloc d’application de gestion des pannes transitoires 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60 : Le bloc de journalisation


- Le bloc de journalisation est une solution extrêmement flexible et configurable qui vous permet de :
 - Créer et stocker des messages du journal dans une grande variété de destinations.
 - Classer et filtrer les messages.
 - Collecter les informations contextuelles qui sont utile pour le débogage et le traçage, ainsi que pour l’audit et générales des informations requises.


- Le bloc de journalisation fait abstraction de la fonctionnalité de journalisation de la destination du journal afin que le code de l’application est cohérent, quel que soit l’emplacement et le type de la banque de la journalisation cible.


Pour plus d’informations, consultez : [5 - en tant que simple comme relevant du désactiver un journal : le bloc d’Application de journalisation à l’aide de](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Code source de méthode EntLib60 IsTransient


Ensuite, à partir de la classe **SqlDatabaseTransientErrorDetectionStrategy** , est le code source C# pour la méthode **IsTransient** . Le code source clarifie les erreurs ont été considérées comme étant transitoires et digne de nouvelle tentative, à compter d’avril 2013.

Plusieurs lignes de **//comment** ont été supprimés à partir de cette copie pour mettre en évidence des raisons de lisibilité.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Étapes suivantes

- Pour la résolution des autres problèmes de connexion de base de données de SQL Azure courants, consultez [résoudre les problèmes de connexion à la base de données de SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [Connexion à SQL Server Pooling (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Nouvel essai* est qu'une Apache 2.0 sous licence de bibliothèque en réessayant polyvalente, écrit dans **Python**, pour simplifier la tâche d’ajout de comportement des nouvelles tentatives à pratiquement n’importe quoi.](https://pypi.python.org/pypi/retrying)
