<properties
    pageTitle="Base de données serveur n’est pas disponible actuellement, de se connecter à la base de données de SQL | Microsoft Azure"
    description="Résoudre les problèmes de la base de données sur serveur n’est pas actuellement disponible erreur lorsqu’une application se connecte à la base de données de SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="base de données serveur n’est pas disponible actuellement, de se connecter à la base de données sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Erreur « Base de données serveur n’est pas disponible actuellement » lors de la connexion à la base de données sql
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Lorsqu’une application se connecte à une base de données SQL d’Azure, le message d’erreur suivant s’affiche :

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Ce message d’erreur est généralement transitoire (courte).

Cette erreur se produit lorsque la base de données Azure est déplacé (ou reconfiguré) et votre application perd sa connexion à la base de données SQL. Événements de reconfiguration de base de données SQL se produit à cause d’un événement planifié (par exemple, une mise à niveau logicielle) ou un événement non planifié (par exemple, un arrêt de processus, ou l’équilibrage de charge). La plupart des événements de reconfiguration sont généralement éphémères et doit être terminée en moins de 60 secondes au plus. Toutefois, ces événements peuvent parfois prendre plus de temps, par exemple lorsqu’une longue transaction provoque une récupération de la durée d’exécution longue.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Étapes à suivre pour résoudre les problèmes de connectivité transitoire
1.  Consultez le [Tableau de bord de Service Microsoft Azure](https://azure.microsoft.com/status) pour les arrêts connus qui s’est produite lors de la période pendant laquelle les erreurs ont été signalées par l’application.
2. Les applications qui se connectent à un service en nuage, telles que la base de données de SQL Azure doit attendre les événements périodiques de reconfiguration et de mettre en œuvre une nouvelle tentative logique pour gérer ces erreurs au lieu de surfaces en tant que les erreurs d’application pour les utilisateurs. Consultez la section [erreurs transitoires](sql-database-connectivity-issues.md) et les meilleures pratiques et lignes directrices en [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) pour plus d’informations générales et réessayer les stratégies de conception. Ensuite, consultez Exemples de code, à [Des bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md) pour connaître les spécificités.
3.  Une base de données approche de ses limites de ressources, il peut sembler être un problème de connectivité transitoire. Voir [résolution des problèmes de performances](sql-database-troubleshoot-performance.md).
4.  Si des problèmes de connectivité continuent, ou si la durée pour laquelle votre application rencontre l’erreur dépasse les 60 secondes ou si vous consultez plusieurs occurrences de l’erreur dans un jour donné, une demande de prise en charge Azure du fichier en sélectionnant **Obtenir la prise en charge** sur le site de [Support d’Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Étapes suivantes
- Si vous recevez une autre erreur, évaluer le [message d’erreur](sql-database-develop-error-messages.md) pour obtenir des indices sur la cause.
- Si le problème est persistant, consultez les conseils de [Dépannage des problèmes de connexion communs à la base de données de SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
