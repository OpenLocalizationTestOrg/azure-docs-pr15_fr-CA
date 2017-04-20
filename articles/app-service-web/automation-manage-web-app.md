<properties
    pageTitle="Gérer Azure Web App à l’aide d’Automation d’Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation peut servir à gérer des Azure Web App."
    services="app-service\web, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-web-app-using-azure-automation"></a>Gestion d’Azure Web App à l’aide d’Automation d’Azure

Ce guide présente le service Automation d’Azure, et comment il peut être utilisé pour simplifier la gestion d’Azure Web App.

## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](../automation/automation-intro.md) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation des processus. À l’aide d’Automation d’Azure, manuel, répétée, longue et source d’erreurs tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et création de valeur ajoutée pour votre organisation.

Automation Azure fournit un moteur d’exécution de workflow hautement fiable et hautement disponible qui s’adapte pour répondre à vos besoins. Dans Azure Automation, processus peuvent être lancées manuellement, par 3 rd-party systèmes, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Réduire la surcharge opérationnelle et de libérer de l’informatique et le personnel DevOps pour vous concentrer sur le travail qui ajoute la valeur commerciale en déplaçant vos tâches de gestion de cloud pour être exécuté automatiquement par Automation d’Azure.


## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Comment Azure Automation peut aider à gérer les Azure Web App ?

Application Web peut être gérée dans Azure Automation à l’aide des applets de commande PowerShell disponibles dans les [modules PowerShell d’Azure](../powershell-install-configure.md). Vous pouvez [installer ces applets de commande PowerShell d’application Web dans Azure Automation](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/), de sorte que vous pouvez effectuer toutes les tâches du service de gestion Web App. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure automatiser des tâches complexes entre des services Azure et des systèmes tiers de 3e.

Voici quelques exemples de gestion des Services d’application avec Automation :

* [Scripts pour la gestion des applications Web](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer les Azure Web App, cliquez sur ces liens pour en savoir plus sur l’Automation d’Azure.

* Consultez l’Automation Azure [didacticiel de mise en route](../automation/automation-first-runbook-graphical.md)
