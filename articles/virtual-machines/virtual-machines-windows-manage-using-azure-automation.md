<properties
    pageTitle="Gérer des ordinateurs virtuels à l’aide d’Automation d’Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation utilisable pour gérer des ordinateurs virtuels Azure à grande échelle."
    services="virtual-machines-windows, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/19/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-virtual-machines-using-azure-automation"></a>Gestion des ordinateurs virtuels de Azure à l’aide d’Automation d’Azure

Ce guide vous présente le service Azure Automation et comment il peut être utilisé pour simplifier la gestion de vos machines virtuelles Azure.


## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](https://azure.microsoft.com/services/automation/) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation des processus. À l’aide de Automation d’Azure, longue, manuels, sujette aux erreurs et régulièrement des tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et time-to-value pour votre organisation.

Automation Azure fournit un moteur d’exécution de workflow hautement disponible et très fiable qui s’adapte pour répondre à vos besoins, que votre entreprise se développe. Dans Azure Automation, processus peuvent être lancées manuellement, par des systèmes tiers, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Vous pouvez réduire la surcharge opérationnelle et libérer de l’informatique et le personnel DevOps pour vous concentrer sur le travail qui ajoute la valeur pour l’entreprise en exécutant votre nuage des tâches de gestion automatiquement avec l’automatisation d’Azure.


## <a name="how-can-azure-automation-help-manage-azure-virtual-machines"></a>Comment Azure Automation permettent de gérer des ordinateurs virtuels Azure ?

Ordinateurs virtuels peuvent être gérés dans Azure Automation à l’aide de [PowerShell d’Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automation Azure inclut les applets de commande PowerShell d’Azure, pour vous pouvez d’effectuer toutes vos tâches de gestion de machine virtuelle au sein du service. Vous pouvez également associer les applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser des tâches complexes entre des services Azure et des systèmes tiers.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer des ordinateurs virtuels Azure, plus :

- [Vue d’ensemble de Automation Azure](../automation/automation-intro.md)
- [Ma procédure opérationnelle premier](../automation/automation-first-runbook-graphical.md)
- [Plan de formation de Automation Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
