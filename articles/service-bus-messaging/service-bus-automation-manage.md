<properties
    pageTitle="Gérer le Bus des services Azure à l’aide d’Automation d’Azure | Microsoft Azure"
    description="Apprenez à utiliser le service Azure Automation pour gérer le Bus des services Azure."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Gestion de Bus des services Azure à l’aide d’Automation d’Azure

Ce guide présente le service Automation d’Azure, et comment il peut être utilisé pour simplifier la gestion de Bus des services Azure.

## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](../automation/automation-intro.md) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation de processus et de configuration de l’état de votre choix. À l’aide d’Automation d’Azure, manuel, répétée, longue et source d’erreurs tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et création de valeur ajoutée pour votre organisation.

Automation Azure fournit un moteur d’exécution de workflow hautement fiable et hautement disponible qui s’adapte pour répondre à vos besoins. Dans Azure Automation, processus peuvent être lancées manuellement, par 3 rd-party systèmes, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Réduire la surcharge opérationnelle et de libérer de l’informatique et le personnel DevOps pour vous concentrer sur le travail qui ajoute la valeur commerciale en déplaçant vos tâches de gestion de cloud pour être exécuté automatiquement par Automation d’Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Comment Azure Automation peut aider à gérer le Bus des services Azure ?

Vous pouvez gérer le Bus des services Azure Automation à l’aide de l' [API REST de Bus de Service](https://msdn.microsoft.com/library/azure/mt639375.aspx). Dans Azure Automation, vous pouvez exécuter les scripts PowerShell pour effectuer la plupart de vos tâches de Bus des services à l’aide de l’API REST. Vous pouvez également associer ces appels API REST dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser des tâches complexes entre des services Azure et des systèmes tiers de 3e.

Voici quelques exemples de l’utilisation de PowerShell pour gérer le Bus des services Azure :

* [Applets de commande PowerShell personnalisées pour gérer les files d’attente du Bus des services Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Comment faire pour créer des files d’attente, des rubriques et des abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Créer des espaces de noms Bus de Service Azure à l’aide de PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

Le module PowerShell pour travailler avec le bus des services Azure dans les procédures opérationnelles d’Automation peut être téléchargé à partir de la [Galerie de PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer le Bus des services Azure, suivre ces liens pour en savoir plus sur l’Automation d’Azure.

* Consultez l’Automation Azure [didacticiel de mise en route](../automation/automation-first-runbook-graphical.md)
* Voir comment [gérer le Bus de Service avec PowerShell](service-bus-powershell-how-to-provision.md)
