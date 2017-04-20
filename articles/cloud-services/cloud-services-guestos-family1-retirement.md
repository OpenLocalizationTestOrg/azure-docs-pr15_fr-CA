<properties
   pageTitle="Notez de famille du système d’exploitation invité 1 retraite | Microsoft Azure"
   description="Fournit des informations sur quand la retraite 1 de famille Azure invité du système d’exploitation s’est produite et comment déterminer si vous êtes concerné"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Avis de retraite 1 de famille du système d’exploitation invité

La mise hors service du système d’exploitation famille 1 première annonce le 1er juin 2013.

**2 septembre 2014.** Système d’exploitation invité d’Azure (système d’exploitation invité) famille 1.x, qui repose sur le système d’exploitation Windows Server 2008, a été officiellement déclassé. Toutes les tentatives de déployer de nouveaux services ou mettre à niveau des services existants à l’aide de la famille 1 échoue avec un message d’erreur vous informant que le 1 de famille du système d’exploitation invité a été supprimé.

**3 novembre 2014.** Prise en charge étendue pour 1 de famille du système d’exploitation invité est terminée, et il est entièrement déclassé. Tous les services toujours sur la famille 1 seront affectées. Nous pouvons arrêter ces services à tout moment. Il n’y a aucune garantie que vos services continue à s’exécuter, sauf si vous mettre à niveau manuellement les vous-même.

Si vous avez des questions supplémentaires, visitez les [Forums de Services Cloud](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contacter le support technique Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>Sont concernés ?

Vos Services de Cloud sont affectés si l’une des actions suivantes s’applique :

1. Vous avez une valeur de « osFamily = « 1 » qui a été spécifié explicitement dans le fichier ServiceConfiguration.cscfg pour votre Service Cloud.
2. Vous n’avez pas d’une valeur d’osFamily spécifiée explicitement dans le fichier ServiceConfiguration.cscfg pour votre Service Cloud. Actuellement, le système utilise la valeur par défaut de « 1 » dans ce cas.
3. Le portail classique Azure répertorie la valeur de famille de votre système d’exploitation invité en tant que « Windows Server 2008 ».

Pour déterminer lequel de vos services de nuage exécutent la famille du système d’exploitation, vous pouvez exécuter le script ci-dessous dans Azure PowerShell, bien que vous devez [configurer Azure PowerShell](../powershell-install-configure.md) . Pour plus de détails sur le script, consultez [Azure invité du système d’exploitation 1 famille de fin de vie : juin 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Vos services de nuage seront affectées par la retraite de système d’exploitation famille 1 si la colonne osFamily dans la sortie du script est vide ou contient un « 1 ».

## <a name="recommendations-if-you-are-affected"></a>Si vous êtes concerné de recommandations

Nous vous recommandons de que vous faire migrer vos rôles de Service Cloud à l’une des familles de système d’exploitation invités pris en charge :

**Système d’exploitation invité famille 4.x** -Windows Server 2012 R2 *(recommandé)*

1. Assurez-vous que votre application est 2.1 ou une version ultérieure du Kit de développement .NET Framework 4.0, 4.5 ou 4.5.1.
2. La valeur de l’attribut osFamily à « 4 » dans le fichier ServiceConfiguration.cscfg et redéployer votre service cloud.


**Système d’exploitation invité 3.x famille** -Windows Server 2012

1. Assurez-vous que votre application utilise 1,8 ou une version ultérieure du Kit de développement .NET Framework 4.0 ou 4.5.
2. La valeur de l’attribut osFamily sur « 3 » dans le fichier ServiceConfiguration.cscfg et redéployer votre service cloud.


**Système d’exploitation invité 2.x famille** -Windows Server 2008 R2

1. Assurez-vous que votre application utilise les SDK 1.3 et versions ultérieures avec .NET framework 3.5 ou 4.0.
2. La valeur de l’attribut osFamily sur « 2 » dans le fichier ServiceConfiguration.cscfg et redéployer votre service cloud.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Prise en charge étendue pour 1 de famille du système d’exploitation invité a pris fin le 3 novembre 2014.
Les services en nuage sur la famille de système d’exploitation invité 1 ne sont plus pris en charge. Veuillez migration à partir de la famille 1 dès que possible afin d’éviter des interruptions de service.  

## <a name="next-steps"></a>Étapes suivantes
Passez en revue les dernières [mises à jour de système d’exploitation invité](cloud-services-guestos-update-matrix.md).
