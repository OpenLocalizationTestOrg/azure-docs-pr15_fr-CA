<properties 
pageTitle="Gérer des événements de cycle de vie du Service en nuage | Microsoft Azure" 
description="Explique comment utiliser les méthodes de cycle de vie d’un rôle de Service Cloud peuvent être dans .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personnaliser le cycle de vie d’un rôle Web ou travailleur dans .NET

Lorsque vous créez un rôle worker, vous étendez la classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) qui permet de substituer des méthodes qui vous permettent de répondent à des événements de cycle de vie. Pour les rôles web, cette classe est facultative, vous devez l’utiliser pour répondre aux événements de cycle de vie.

## <a name="extend-the-roleentrypoint-class"></a>Étendre la classe RoleEntryPoint

La classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) inclut des méthodes qui sont appelées par Azure lorsqu’il **démarre**, **s’exécute**ou **s’arrête** un rôle web ou travailleur. Vous pouvez éventuellement substituer ces méthodes pour gérer l’initialisation de rôle, des séquences d’arrêt de rôle ou le thread d’exécution du rôle. 

Lors de l’extension **RoleEntryPoint**, vous devez être conscient des problèmes des méthodes suivantes :

-   Les méthodes [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) et [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) renvoient une valeur de type boolean, il est possible de retourner la **valeur false** à partir de ces méthodes.

     Si votre code retourne la **valeur false**, le processus du rôle est brutalement interrompu, sans exécuter n’importe quelle séquence d’arrêt que peut avoir lieu. En général, vous devez éviter le renvoi de **false** à partir de la méthode **OnStart** .
     
-   Toute exception non interceptée dans une surcharge de la méthode **RoleEntryPoint** est considérée comme une exception non gérée.

     Si une exception se produit dans une des méthodes de cycle de vie, Azure déclenche l’événement [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) , et ensuite le processus est arrêté. Une fois votre rôle a été mis hors connexion, il sera redémarré par Azure. Lorsqu’une exception non gérée se produit, l’événement [d’arrêt](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) n’est pas déclenché, et la méthode **OnStop** n’est pas appelée.

Si votre rôle ne démarre pas, ou entre les États d’arrêt et de l’initialisation, occupé, est le recyclage, votre code peut être levée d’une exception non gérée dans un des événements de cycle de vie redémarre de chaque fois que le rôle. Dans ce cas, utilisez l’événement [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) pour déterminer la cause de l’exception et la gérer correctement. Votre rôle peut également renvoyer à partir de la méthode [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , ce qui entraîne le rôle à redémarrer. Pour plus d’informations sur les États de déploiement, reportez-vous à la section [Problèmes qui Cause les rôles communs à recycler](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Si vous utilisez les **Outils Azure pour Visual Studio de Microsoft** pour développer votre application, les modèles de projet de rôle étendent automatiquement la classe **RoleEntryPoint** pour vous, dans les fichiers *WebRole.cs* et *WorkerRole.cs* .

## <a name="onstart-method"></a>Méthode OnStart

La méthode **OnStart** est appelée lorsque l’instance de rôle est mise en ligne par Azure. Le code OnStart est en cours d’exécution, l’instance de rôle est marqué comme **occupé** et aucun trafic externe ne sera dirigé vers elle par l’équilibreur de charge. Vous pouvez substituer cette méthode pour exécuter des tâches d’initialisation, telles que la mise en œuvre des gestionnaires d’événements et le démarrage des [Diagnostics d’Azure](cloud-services-how-to-monitor.md).

Si **OnStart** renvoie la **valeur true**, l’instance est initialisée correctement et Azure appelle la méthode **RoleEntryPoint.Run** . Si **OnStart** retourne la **valeur false**, le rôle se termine immédiatement, sans exécuter les séquences d’arrêt planifié.

L’exemple de code suivant montre comment substituer la méthode **OnStart** . Cette méthode configure et démarre un analyseur de diagnostic lorsque l’instance de rôle démarre et configure un transfert de données de journalisation vers un compte de stockage :

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Méthode OnStop

La méthode **OnStop** est appelée une fois, une instance de rôle a été déconnectée par Azure et avant la fin du processus. Vous pouvez substituer cette méthode pour appeler le code requis pour votre instance de rôle arrêtée.

> [AZURE.IMPORTANT] Le code exécuté dans la méthode **OnStop** a un limite de temps lorsqu’elle est appelée pour des raisons autres qu’un arrêt initié par l’utilisateur. Une fois ce délai écoulé, le processus est terminé, donc vous devez vous assurer que le code dans la méthode **OnStop** a la possibilité s’exécutent rapidement ou tolère ne pas en cours d’exécution jusqu'à la fin. La méthode **OnStop** est appelée après le déclenchement de l’événement **d’arrêt** .


## <a name="run-method"></a>Méthode Run

Vous pouvez substituer la méthode **Run** pour implémenter un thread d’exécution longue pour votre instance de rôle.

Substitution de la méthode à **exécuter** n’est pas obligatoire ; l’implémentation par défaut démarre un thread qui se met en veille pour toujours. Si vous ne substituez pas la méthode **Run** , votre code doit bloquer indéfiniment. Si la méthode **Run** retourne, le rôle est recyclé automatiquement normalement ; en d’autres termes, Azure déclenche l’événement **d’arrêt** et appelle la méthode **OnStop** de sorte que vos séquences d’arrêt peuvent être exécutés avant que le rôle est mis hors connexion.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implémentez les méthodes de cycle de vie ASP.NET pour un rôle web

Vous pouvez utiliser les méthodes de cycle de vie ASP.NET, en plus de celles fournies par la classe **RoleEntryPoint** , pour gérer l’initialisation et les séquences d’arrêt pour un rôle web. Si vous procédez au portage d’une application ASP.NET existante vers Azure, cela peut être utile pour des raisons de compatibilité. Les méthodes de cycle de vie ASP.NET sont appelées à partir dans les méthodes de **RoleEntryPoint** . Le **Application\_démarrer** méthode est appelée après la fin de la méthode **RoleEntryPoint.OnStart** . Le **Application\_fin** méthode est appelée avant la méthode **RoleEntryPoint.OnStop** est appelée.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un package de service cloud](cloud-services-model-and-package.md).