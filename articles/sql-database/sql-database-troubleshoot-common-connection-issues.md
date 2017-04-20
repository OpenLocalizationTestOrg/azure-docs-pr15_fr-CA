<properties
    pageTitle="Résoudre les problèmes courants de connexion à la base de données de SQL Azure"
    description="Étapes pour identifier et résoudre des erreurs courantes de connexion pour la base de données de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Résoudre les problèmes de connexion à la base de données de SQL Azure

En cas d’échec de la connexion à la base de données de SQL Azure, vous recevez des [messages d’erreur](sql-database-develop-error-messages.md). Cet article est une rubrique centralisée qui vous permet de résoudre les problèmes de connectivité de base de données de SQL Azure. Présente [le courant provoque](#cause) des problèmes de connexion, il recommande [un outil de dépannage](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) qui permet l’identité sur le problème et fournit les étapes de dépannage pour résoudre les [erreurs transitoires](#troubleshoot-transient-errors) et [persistant ou non transitoire](#troubleshoot-the-persistent-errors). Enfin, il répertorie [tous les articles pertinents pour les problèmes de connectivité de base de données de SQL Azure](#all-topics-for-azure-sql-database-connection-problems).

Si vous rencontrez les problèmes de connexion, essayez les étapes de résolution des problèmes qui sont décrits dans cet article.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Cause

Des problèmes de connexion peuvent être provoqués par une des opérations suivantes :

- Échec à appliquer les meilleures pratiques et des instructions de conception lors du processus de conception d’application.  Consultez [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) pour commencer.
- Reconfiguration de la base de données SQL Azure
- Paramètres de pare-feu
- Délai de connexion
- Informations de connexion incorrectes
- Limite maximale atteinte sur certaines ressources de base de données de SQL Azure

En général, les problèmes de connexion à la base de données de SQL Azure peuvent être classés comme suit :

- [Erreurs transitoires (courtes ou intermittentes)](#troubleshoot-transient-errors)
- [Permanents ou non transitoires erreurs (qui se reproduisent régulièrement)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Essayez l’utilitaire de dépannage pour les problèmes de connectivité de base de données de SQL Azure

Si vous rencontrez une erreur de connexion spécifique, essayez [cet outil](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), qui vous permettra de rapidement identité et résoudre votre problème.

## <a name="troubleshoot-transient-errors"></a>Résoudre les erreurs transitoires
Si votre application connaît des erreurs temporaires, consultez les rubriques suivantes pour obtenir des conseils sur la façon de résoudre les problèmes et de réduire la fréquence de ces erreurs :

- [Résolution des problèmes de base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible (erreur : 40613)](sql-database-troubleshoot-connection.md)
- [Dépanner, diagnostiquer et éviter les erreurs transitoires et les erreurs de connexion SQL pour la base de données de SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Résoudre les erreurs persistantes (erreurs non transitoires)

Si l’application échoue de façon permanente pour se connecter à la base de données de SQL Azure, il indique généralement un problème avec l’une des opérations suivantes :

- Configuration du pare-feu. Le pare-feu de base de données ou côté client Azure SQL bloque les connexions à la base de données de SQL Azure.
- Reconfiguration du côté client du réseau : par exemple, une nouvelle adresse IP ou un serveur proxy.
- Erreur de l’utilisateur : par exemple, tapé de manière incorrecte des paramètres de connexion, comme le nom du serveur dans la chaîne de connexion.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Étapes à suivre pour résoudre les problèmes de connectivité permanente

1.  Définir des [règles de pare-feu](sql-database-configure-firewall-settings.md) pour autoriser l’adresse IP du client.
2.  Sur tous les pare-feu entre le client et d’Internet, assurez-vous que le port 1433 est ouvert pour les connexions sortantes. Consultez [configurer le pare-feu Windows pour autoriser l’accès SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) pour les pointeurs supplémentaires.
3.  Vérifiez votre chaîne de connexion et d’autres paramètres de connexion. Reportez-vous à la section de la chaîne de connexion dans la [rubrique des problèmes de connectivité](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.  Vérifier l’intégrité du service dans le tableau de bord. Si vous pensez qu’il y a une panne régionale, consultez le [récupérer en cas de panne](sql-database-disaster-recovery.md) pour les étapes pour récupérer vers une nouvelle zone.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Toutes les rubriques pour des problèmes de connexion de base de données de SQL Azure

Le tableau suivant répertorie chaque rubrique de problème de connexion qui s’applique directement au service de base de données de SQL Azure.


| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Résoudre les problèmes de connexion à la base de données de SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Il s’agit de la page de lancement pour la résolution des problèmes de connectivité de base de données de SQL Azure. Il explique comment identifier et résoudre les erreurs transitoires et permanentes ou non transitoire des erreurs dans la base de données de SQL Azure. |
| 2 | [Dépanner, diagnostiquer et éviter les erreurs transitoires et les erreurs de connexion SQL pour la base de données de SQL](sql-database-connectivity-issues.md) | Apprenez à dépanner, diagnostiquer et empêcher une erreur de connexion SQL ou transitoire dans la base de données de SQL Azure. |
| 3 | [Guide de gestion des erreurs transitoire général](best-practices-retry-general.md) | Fournit des recommandations pour la gestion des erreurs transitoires lors de la connexion à la base de données de SQL Azure. |
| 4 | [Résolution des problèmes de connectivité de base de données SQL de Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Cet outil permet d’identité votre problème résoudre des erreurs de connexion. |
| 5 | [Résoudre les problèmes de « base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible actuellement. Recommencez la connexion ultérieurement » une erreur](sql-database-troubleshoot-connection.md) | Explique comment identifier et résoudre une erreur 40613 : « base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible actuellement. Recommencez la connexion ultérieurement. » |
| 6 | [Codes d’erreur SQL pour les applications de client de base de données SQL : base de données d’erreur de connexion et d’autres problèmes](sql-database-develop-error-messages.md) | Fournit des informations sur les codes d’erreur SQL pour les applications clientes de la base de données SQL, comme les erreurs courantes de connexion de base de données, les problèmes de copie de base de données et erreurs générales. |
| 7 | [Conseils pour les performances de la base de données SQL Azure des bases de données unique](sql-database-performance-guidance.md) | Fournit des conseils pour vous aider à déterminer le niveau de service adapté à votre application. Fournit également des recommandations pour le réglage de votre application pour tirer le meilleur parti de votre base de données de SQL Azure. |
| 8 | [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) | Fournit des liens vers des exemples de code pour les diverses technologies que vous pouvez utiliser pour vous connecter à et d’interagir avec la base de données de SQL Azure. |
| 9 | Mise à niveau vers la page de v12 de base de données de SQL Azure ([Azure portal](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Fournit des instructions relatives à la mise à niveau de V11 de base de données SQL Azure serveurs et des bases de données existants vers V12 de base de données SQL Azure, à l’aide d’Azure portal ou PowerShell. |


## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de performances de base de données de SQL Azure](sql-database-troubleshoot-performance.md)
- [Résoudre les problèmes d’autorisations de base de données de SQL Azure](sql-database-troubleshoot-permissions.md)
- [Voir toutes les rubriques pour le service de base de données de SQL Azure](sql-database-index-all-articles.md)
- [Recherchez dans la documentation sur Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Permet d’afficher les dernières mises à jour pour le service de base de données de SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
- [Guide de gestion des erreurs transitoire général](../best-practices-retry-general.md)
- [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)
- [Le chemin d’accès de formation pour l’utilisation de base de données de SQL Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [Le chemin d’accès de formation pour l’utilisation des outils et fonctionnalités de base de données élastique](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 
