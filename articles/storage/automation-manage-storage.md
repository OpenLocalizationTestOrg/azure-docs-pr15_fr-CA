<properties
    pageTitle="Gérer le stockage Azure à l’aide d’Automation d’Azure"
    description="Découvrez comment le service Azure Automation permet de gérer le stockage Azure à grande échelle."
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-storage-using-azure-automation"></a>Gestion du stockage Azure à l’aide d’Automation d’Azure

Ce guide présente le service Automation d’Azure, et comment il peut être utilisé pour simplifier la gestion de vos objets BLOB Azure Storage, les tables et les files d’attente.


## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](https://azure.microsoft.com/services/automation/) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation des processus. À l’aide de Automation d’Azure, tâches longues, manuels, sujette aux erreurs et fréquemment répétés peuvent être automatisés pour augmenter la fiabilité et l’efficacité et réduisent le temps de la valeur pour votre organisation.

Automation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponible qui s’adapte pour répondre à vos besoins, que votre entreprise se développe. Dans Azure Automation, processus peuvent être lancées manuellement, par 3 rd-party systèmes, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Réduire la surcharge opérationnelle et de libérer de l’informatique / personnel DevOps pour vous concentrer sur le travail qui ajoute l’entreprise valeur en déplaçant vos tâches de gestion de cloud pour être exécuté automatiquement par Automation d’Azure.


## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Comment Azure Automation peut aider à gérer le stockage Azure ?

Stockage Azure peut être géré dans Azure Automation à l’aide des applets de commande PowerShell disponibles dans [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automation Azure a ces applets de commande PowerShell de stockage disponibles prêts à l’emploi, que vous pouvez effectuer tous les blob, table et des tâches de gestion de file d’attente dans le service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser des tâches complexes entre des services Azure et des systèmes tiers de 3e.

La [Galerie de procédure opérationnelle Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contient plusieurs procédures opérationnelles Communauté et de l’équipe produit à démarrer l’automatisation de la gestion du stockage Azure, les autres services Azure et 3 systèmes de tiers. Procédures opérationnelles de la galerie sont les suivantes :

 * [Supprimer les objets BLOB stockage Azure qui sont certains jours ancien à l’aide de Automation Service](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Télécharger un Blob à partir du stockage Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Tous les disques de sauvegarde pour un ordinateur virtuel Azure unique ou pour tous les ordinateurs virtuels dans un Service Cloud](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer les objets BLOB Azure Storage, les tables et les files d’attente, cliquez sur ces liens pour en savoir plus sur l’Automation d’Azure.

Consultez le didacticiel Azure Automation de [Création ou l’importation d’une procédure opérationnelle dans Azure Automation](../automation/automation-creating-importing-runbook.md).
 
