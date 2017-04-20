<properties 
    pageTitle="Ports au-delà de 1433 pour SQL de base de données | Microsoft Azure"
    description="Parfois, les connexions client à partir de ADO.NET pour V12 de base de données SQL Azure ignorer le proxy et interagissent directement avec la base de données. Ports de 1433 sont importants."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Ports autres que le 1433 pour ADO.NET 4.5 et V12 de base de données SQL


Cette rubrique décrit les modifications qui V12 de base de données SQL Azure offre le comportement de connexion des clients qui utilisent ADO.NET 4.5 ou une version ultérieure.


## <a name="v11-of-sql-database-port-1433"></a>V11 de base de données SQL : Port 1433


Lorsque votre programme client utilise ADO.NET 4.5 pour se connecter et d’interroger avec V11 de base de données SQL, la séquence interne est la suivante :


1. ADO.NET tente de se connecter à la base de données de SQL.

2. ADO.NET utilise le port 1433 pour appeler un module middleware et le middleware se connecte à la base de données de SQL.

3. Base de données de SQL envoie sa réponse dans le middleware, qui transmet la réponse à ADO.NET au port 1433.


**La terminologie :** Nous décrivons la séquence précédente en disant que ADO.NET interagit avec la base de données de SQL à l’aide de la *gamme de proxy*. Si aucun middleware n’impliqué, indiquer que l' *itinéraire direct* a été utilisé.


## <a name="v12-of-sql-database-outside-vs-inside"></a>V12 de base de données SQL : à l’extérieur et à l’intérieur


Pour les connexions à V12, nous devons demander si votre programme client s’exécute *à l’extérieur* ou *à l’intérieur de* la limite d’Azure cloud. Les sous-sections discuter de deux scénarios courants.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Externe :* Client s’exécute sur votre ordinateur de bureau


Le port 1433 est le seul port qui doit être ouvert sur votre ordinateur de bureau qui héberge votre application de client de base de données SQL.


#### <a name="inside-client-runs-on-azure"></a>*à l’intérieur :* Client s’exécute sur Azure


Lorsque votre client s’exécute à l’intérieur de la limite d’Azure cloud, il utilise ce que nous pouvons appeler un *itinéraire direct* pour interagir avec le serveur de base de données SQL. Lorsqu’une connexion est établie, les interactions entre le client et la base de données n’associe en outre aucun proxy de middleware.


La séquence est la suivante :


1. ADO.NET 4.5 (ou version ultérieure) initie une brève interaction avec le nuage Azure et reçoit un numéro de port identifié de manière dynamique.
 - Le numéro de port dynamique identifié est dans la plage de 11000-11999 ou 14000-14999.

2. ADO.NET se connecte ensuite au serveur de base de données SQL directement, avec aucune middleware entre les deux.

3. Les requêtes sont envoyées directement à la base de données, et les résultats directement au client.


Vérifiez que le port de plages de 11000-11999 et 14000-14999 sur votre ordinateur client Azure restent disponibles pour les interactions client de ADO.NET 4.5 avec V12 de base de données SQL.

- En particulier, les ports de la plage doivent être exempts de toute autres sortants bloqueurs de fenêtres publicitaires.

- Sur votre machine virtuelle Azure, le **Pare-feu Windows avec fonctions avancées de sécurité** contrôle les paramètres du port.
 - Vous pouvez utiliser l' [interface utilisateur du pare-feu](http://msdn.microsoft.com/library/cc646023.aspx) pour ajouter une règle pour laquelle vous spécifiez le protocole **TCP** avec une plage de ports avec la syntaxe comme **11000-11999**.


## <a name="version-clarifications"></a>Précisions de version


Cette section précise les monikers qui se réfèrent aux versions du produit. Il répertorie également des jumelages de versions entre les produits.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 prend en charge le protocole TDS 7.3, mais pas de 7.4.
- ADO.NET 4.5 et ultérieure prend en charge le protocole TDS 7.4.


#### <a name="sql-database-v11-and-v12"></a>V12 et V11 de base de données SQL


Les client connexion V11 de base de données SQL et différences V12 sont mis en surbrillance dans cette rubrique.


*Remarque :* L’instruction Transact-SQL `SELECT @@version;` retourne une valeur qui commencent par un nombre tel que « 11. » ou '12' et ces correspondent à nos noms de version de V11 et V12 pour SQL de base de données.


## <a name="related-links"></a>Liens connexes


- ADO.NET 4.6 a été publiée le 20 juillet 2015. Une annonce du blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 a été publiée le 15 août 2012. Une annonce du blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Un billet de blog sur ADO.NET 4.5.1 est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Liste des versions de protocole TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)


- [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md)


- [Comment : configurer les paramètres du pare-feu sur la base de données de SQL](sql-database-configure-firewall-settings.md)

