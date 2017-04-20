<properties
   pageTitle="SQL Azure avec RemoteApp Azure | Microsoft Azure"
   description="Apprenez à utiliser SQL Azure avec Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Souvent lorsque les clients choisissent d’héberger leurs applications Windows dans le nuage avec Azure RemoteApp ils veulent également migrer leurs données tels que les serveurs SQL dans le nuage pour un déploiement de cloud entière. Ainsi, pour la solution entière nuage hébergé qui est accessible à tout moment par n’importe quel périphérique sur n’importe quel endroit à l’aide de RemoteApp d’Azure. Vous trouverez ci-dessous des liens et des références ainsi que des conseils pour vous aider tout au long de ce processus.  

## <a name="migrate-your-sql-data"></a>Migrer vos données SQL

Démarrer à la [migration d’une base de données SQL Server pour la base de données de SQL Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurer RemoteApp Azure
Héberger votre application Windows Azure RemoteApp. Vous trouverez ci-dessous un très haut niveau étape par étape :

1.     Créez le [modèle de RemoteApp d’Azure VM](remoteapp-imageoptions.md). 
2.     Installez l’application nécessaire sur l’ordinateur virtuel.
3.     Configurer l’application afin qu’il se connecte à la base de données SQL et vérifiez qu’il fonctionne.
4.     Sysprep et l’arrêt de la machine virtuelle. Capturer ce sous forme d’image pour une utilisation avec Azure. **Remarque :** Vous devez vous assurer que l’application est en mesure de conserver les informations de connectivité de base de données à travers le processus de sysprep. Si l’application ne peut pas conserver les informations de connexion de base de données, vous pouvez souhaiter s’engager le fournisseur de l’application pour vérifier comment nous pouvons spécifier la chaîne de connexion.
5.     Importer l’image personnalisée dans votre bibliothèque d’Azure RemoteApp sélection de l’emplacement géographique appropriée résidant dans votre déploiement de SQL Azure. 
6.     Déployer une collection RemoteApp dans le même centre de données que votre déploiement de SQL Azure en utilisant le modèle ci-dessus et publier l’application. Déploiement déploiement de RemoteApp Azure dans le même centre de données en tant que votre SQL Azure permet de s’assurer de la vitesse de connexion plus rapide et de réduire la latence. 

## <a name="app-and-sql-configuration-considerations"></a>Considérations de configuration application et SQL :
Il existe quelques points à prendre en compte lors de l’utilisation d’Azure SQL avec RemoteApp :

Apprenez [à configurer un pare-feu de base de données SQL d’Azure](../sql-database/sql-database-firewall-configure.md). Un extrait des États de l’article, « au départ, tous les accès à votre serveur de base de données SQL Azure sont bloqué par le pare-feu. Pour commencer à utiliser votre serveur de base de données de SQL Azure, vous devez atteindre le portail classique et spécifier une ou plusieurs règles au niveau du serveur pare-feu qui permettent l’accès à votre serveur de base de données de SQL Azure. Utiliser les règles de pare-feu pour spécifier les plages d’adresses IP à partir d’Internet sont autorisés, et ou non des applications Azure peuvent tenter de se connecter à votre serveur de base de données de SQL Azure ».

Également, lorsqu’un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie l’adresse IP d’origine de la demande par rapport à l’ensemble au niveau du serveur (si nécessaire) et de règles de pare-feu au niveau de base de données. « Si l’adresse IP de la demande est dans une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur de base de données de SQL Azure ». Par conséquent, nous pouvons faire utiliser des plages IP et non pas uniquement les adresses IP source individuelle.

Suivez les instructions étape par étape de [Comment : configurer les paramètres du pare-feu sur la base de données SQL via le portail Azure](../sql-database/sql-database-configure-firewall-settings.md) pour spécifier la plage d’adresses IP. Lorsque vous configurez les règles de pare-feu de SQL, indiquez la plage IP du sous-réseau qui est spécifié pour la collection Azure RemoteApp. Cela doit permettre aux serveurs de ARA pour se connecter à la base de données SQL, même si elles seront-affectés de manière dynamique des adresses IP.

## <a name="troubleshooting"></a>Résolution des problèmes
Si l’expérience de l’utilisation d’une application cliente hébergé dans Azure RemoteApp qui se connecte à un SQL de base de données où hébergé sur Azure ou sur site est lent il peut y avoir plusieurs raisons pourquoi.  

- Latence du réseau à partir de votre périphérique vers Azure est élevée. À la connexion réseau meilleur et plus rapide que vous pouvez la déplacer pour de meilleures performances. Utilisez [azurespeed.com](http://azurespeed.com/) comme un outil générique pour tester la latence de vos équipements de centre de données Azure.  
- Application client hébergée dans Azure RemoteApp est sous tension. Sélection d’un plan de facturation différent de facturation de prime améliorera les performances. Une autre astuce consiste à surveiller les ressources de votre application consomme : au cours d’une session active, effectuer une séquence de touches ctrl-alt-fin qui lancera l’écran SAS, sélectionnez Gestionnaire des tâches et observer l’utilisation des ressources pour votre application.
- Serveur SQL est sous contrainte ou non optimisé. Suivez les instructions SQL pour le dépannage. 

