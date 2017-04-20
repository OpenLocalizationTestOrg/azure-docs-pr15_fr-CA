<properties
    pageTitle="Gérer les Services en nuage à l’aide d’Automation d’Azure Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation peut servir à gérer des services cloud Azure à grande échelle."
    services="cloud-services, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/20/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-cloud-services-using-azure-automation"></a>Gestion des Services en nuage Azure à l’aide d’Automation d’Azure

Ce guide présente au service Automation d’Azure, et comment elle peut être utilisée pour simplifier la gestion de vos services de cloud Azure.

## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](https://azure.microsoft.com/services/automation/) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation des processus. Longue, manuels, sujette aux erreurs et régulièrement des tâches peuvent être automatisées à l’aide de Automation d’Azure, pour augmenter la fiabilité, l’efficacité et création de valeur ajoutée pour votre organisation.

Automation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponible qui s’adapte pour répondre à vos besoins, que votre entreprise se développe. Dans Azure Automation, processus peuvent être lancées manuellement, par 3 rd-party systèmes, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Réduire la surcharge opérationnelle et de libérer de l’informatique / personnel DevOps pour vous concentrer sur le travail qui ajoute l’entreprise valeur en déplaçant vos tâches de gestion de cloud pour être exécuté automatiquement par Automation d’Azure.


## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Comment Azure Automation peut aider à gérer les services en nuage Azure ?

Les services en nuage Azure peuvent être gérés dans Azure Automation à l’aide des applets de commande PowerShell disponibles dans les [Outils de PowerShell d’Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automation Azure a ces cloud service applets de commande PowerShell disponibles prêts à l’emploi, afin que vous pouvez effectuer toutes vos tâches de gestion de service nuage au sein du service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser des tâches complexes entre des services Azure et des systèmes tiers de 3e.

Quelques exemples d’utilisation de l’automatisation d’Azure pour gérer les Services en nuage Azure sont les suivants :

- [Déploiement continue d’un Service de nuage à chaque fois que cscfg ou cspkg est mis à jour dans le stockage Azure Blob](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
- [Redémarrage des instances de Service Cloud en parallèle, un domaine de mise à niveau à la fois](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer les services en nuage Azure, suivre ces liens pour en savoir plus sur l’Automation d’Azure.

- [Vue d’ensemble de Automation Azure](../automation/automation-intro.md)
- [Ma procédure opérationnelle premier](../automation/automation-first-runbook-graphical.md)
- [Plan de formation de Automation Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
 
