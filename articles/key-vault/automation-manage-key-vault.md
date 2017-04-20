<properties
    pageTitle="Gérer le coffre-fort de clé Azure à l’aide d’Automation d’Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation peut servir à gérer des Azure clé coffre-fort."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Gestion de coffre-fort de clé Azure à l’aide d’Automation d’Azure

Ce guide présente au service Azure Automation et comment elle peut être utilisée pour simplifier la gestion de vos clés et les secrets dans le coffre-fort de clé Azure.

## <a name="what-is-azure-automation"></a>Nouveautés, Automation d’Azure ?

[Automation d’Azure](../automation/automation-intro.md) est un service Azure pour simplifier la gestion du nuage grâce à l’automatisation de processus et de configuration de l’état de votre choix. À l’aide d’Automation d’Azure, manuel, répétée, longue et source d’erreurs tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et création de valeur ajoutée pour votre organisation.

Automation Azure fournit un moteur d’exécution de workflow hautement fiable et hautement disponible qui s’adapte pour répondre à vos besoins. Dans Azure Automation, processus peuvent être lancées manuellement, par 3 rd-party systèmes, ou à intervalles réguliers afin que les tâches se passe exactement lorsque cela est nécessaire.

Réduire la surcharge opérationnelle et de libérer de l’informatique et le personnel DevOps pour vous concentrer sur le travail qui ajoute la valeur commerciale en déplaçant vos tâches de gestion de cloud pour être exécuté automatiquement par Automation d’Azure.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Comment Azure Automation peut aider à gérer le coffre-fort de clé Azure ?

Clé de stockage en chambre forte peuvent être géré dans Azure Automation à l’aide de [applets de commande en chambre forte de la clé AzureRM] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) et [les applets de commande Azure classique clé coffre-fort](https://msdn.microsoft.com/library/azure/dn868052.aspx). Le module Azure pour la gestion de coffre-fort de clé classique est disponible automatiquement dans Azure Automation, et vous pouvez importer le [module de AzureRM-KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) dans Azure Automation, afin que vous pouvez accomplir la plupart de vos tâches de gestion de stockage en chambre forte de clé au sein du service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser des tâches complexes entre des services Azure et des systèmes tiers de 3e.

Avec les applets de commande Azure clé coffre-fort, vous pouvez effectuer ces tâches, entre autres : 

- Créer et configurer un clé coffre-fort
- Créer ou importer une clé
- Créer ou mettre à jour d’un secret
- Mettre à jour les attributs d’une clé
- Obtenir une clé ou un secret
- Supprimer une clé ou un secret

Voici quelques exemples de l’utilisation de PowerShell pour gérer la clé de stockage en chambre forte :  

* [Chambre forte Azure clé - étape par étape](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Paramétrage et configuration d’un coffre-fort de clé Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer le coffre-fort de clé Azure, suivre ces liens pour en savoir plus sur l’Automation d’Azure.

* Consultez l’Automation Azure [didacticiel de mise en route](../automation/automation-first-runbook-graphical.md).
* Consultez les [scripts PowerShell de coffre-fort clé Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
