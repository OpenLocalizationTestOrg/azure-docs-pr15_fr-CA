<properties
    pageTitle="Gérer les bases de données SQL Azure à l’aide d’Automation d’Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation permet de gérer des bases de données SQL d’Azure à grande échelle."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Gestion des bases de données SQL Azure à l’aide d’Automation d’Azure

Ce guide présente le service Automation d’Azure, et comment il peut être utilisé pour simplifier la gestion de vos bases de données SQL d’Azure.


## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](https://azure.microsoft.com/services/automation/) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation des processus. Longue, manuels, sujette aux erreurs et régulièrement des tâches peuvent être automatisées à l’aide de Automation d’Azure, pour augmenter la fiabilité, l’efficacité et création de valeur ajoutée pour votre organisation.

Automation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponible qui s’adapte pour répondre à vos besoins, que votre entreprise se développe. Dans Azure Automation, processus peuvent être lancées manuellement, par 3 rd-party systèmes, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Réduire la surcharge opérationnelle et de libérer de l’informatique / personnel DevOps pour vous concentrer sur le travail qui ajoute l’entreprise valeur en déplaçant vos tâches de gestion de cloud pour être exécuté automatiquement par Automation d’Azure.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Comment Azure Automation peut aider à gérer des bases de données SQL d’Azure ?

Une base de données SQL Azure peuvent être géré dans Azure Automation en utilisant les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/dn546723.aspx) qui sont disponibles dans les [Outils de PowerShell d’Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automation Azure a ces applets de commande PowerShell de base de données SQL Azure disponibles prêts à l’emploi, que vous pouvez effectuer toutes vos tâches de gestion de base de données SQL dans le service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser des tâches complexes entre des services Azure et des systèmes tiers de 3e.

Automation Azure a également la capacité de communiquer avec les serveurs SQL directement, en envoyant des commandes SQL à l’aide de PowerShell.

La [Galerie de procédure opérationnelle Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contient un ensemble de procédures opérationnelles Communauté et de l’équipe produit à démarrer l’automatisation de la gestion de bases de données SQL Azure, les autres services Azure et systèmes tiers de 3e. Procédures opérationnelles de la galerie sont les suivantes :

 * [Exécuter les requêtes SQL contre une base de données SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Évolution verticale (vers le haut ou vers le bas) une base de données de SQL Azure selon un calendrier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Tronquer une table SQL si sa base de données approche de sa taille maximale](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Index des tables dans une base de données de SQL Azure si elles sont très fragmentés](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer des bases de données SQL d’Azure, suivre ces liens pour en savoir plus sur l’Automation d’Azure.

- [Vue d’ensemble de Automation Azure](../automation/automation-intro.md)
- [Ma procédure opérationnelle premier](../automation/automation-first-runbook-graphical.md)
- [Plan de formation de Automation Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Automation Azure : Votre Agent SQL dans le nuage](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
