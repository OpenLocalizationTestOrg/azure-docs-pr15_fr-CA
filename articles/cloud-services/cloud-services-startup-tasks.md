<properties 
pageTitle="Exécuter des tâches de démarrage des Services de Cloud Azure | Microsoft Azure" 
description="Tâches de démarrage aider à préparer votre environnement de service cloud pour votre application. Il vous apprend comment fonctionnent les tâches de démarrage et comment les rendre" 
services="cloud-services" 
documentationCenter="" 
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



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Comment configurer et exécuter des tâches de démarrage pour un service en nuage

Vous pouvez utiliser des tâches de démarrage pour effectuer des opérations avant le début d’un rôle. Les opérations que vous pouvez effectuer incluent l’installation d’un composant, enregistrement des composants COM, les clés de Registre ou de démarrer un processus de longue durée.

>[AZURE.NOTE] Tâches de démarrage ne sont pas applicables aux Machines virtuelles, qu’aux Cloud Service rôles Web et Worker.

## <a name="how-startup-tasks-work"></a>Comment fonctionnent les tâches de démarrage

Tâches de démarrage sont des actions qui sont effectuées avant de commencent de vos rôles et sont définis dans le fichier [ServiceDefinition.csdef] à l’aide de l’élément de [tâche] dans l’élément de [démarrage] . Fréquence des tâches de démarrage sont des fichiers batch, mais ils peuvent également être des applications console, ou des fichiers batch qui lancent des scripts PowerShell.

Variables d’environnement passent des informations dans une tâche de démarrage et stockage local peut être utilisé pour passer des informations d’une tâche de démarrage. Par exemple, une variable d’environnement peut spécifier le chemin d’accès à un programme que vous souhaitez installer et les fichiers peuvent être écrites sur le stockage local qui peut alors être lu ultérieurement par vos rôles.

Votre tâche de démarrage peut consigner les informations et les erreurs dans le répertoire spécifié par la variable d’environnement **TEMP** . Au cours de la tâche au démarrage, la variable d’environnement **TEMP** résout le *C:\\ressources\\temp\\[guid]. [ rolename]\\RoleTemp* répertoire lors de l’exécution sur le nuage.

Tâches de démarrage peuvent également être exécutés plusieurs fois entre les redémarrages. Par exemple, la tâche de démarrage s’exécute chaque fois que le rôle recycle et recyclages de rôle ne peuvent pas toujours inclure un redémarrage. Tâches de démarrage doivent être écrit d’une façon qui leur permet d’exécuter plusieurs fois sans problèmes.

Tâches de démarrage doivent se terminer par un **errorlevel** ou code de sortie de zéro pour le processus de démarrage pour terminer. Si une tâche de démarrage se termine par un zéro **errorlevel**, le rôle ne démarre pas.


## <a name="role-startup-order"></a>Commande de démarrage de rôle

La liste suivante décrit la procédure de démarrage de rôle dans Azure :

1. L’instance est marquée comme **départ** et ne reçoit pas le trafic.

2. Toutes les tâches de démarrage sont exécutés en fonction de leur attribut **taskType** .
    - Tâches **simples** sont exécutées de manière synchrone, un à la fois.
    - Les tâches de **premier plan** et **d’arrière-plan** sont démarrés en mode asynchrone, parallèlement à la tâche de démarrage.  
       
    > [AZURE.WARNING] IIS n’est pas entièrement configuré pendant l’étape de tâche de démarrage du processus de démarrage, les données spécifiques à un rôle peut ne pas être disponibles. Tâches de démarrage qui exigent des données de rôle spécifiques doivent utiliser [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Le processus hôte du rôle est démarré et que le site est créé dans IIS.

4. La méthode [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) est appelée.

5. L’instance est marquée comme **prêt** et le trafic est routé vers l’instance.

6. La méthode [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) est appelée.


## <a name="example-of-a-startup-task"></a>Exemple d’une tâche de démarrage

Tâches de démarrage sont définies dans le fichier [ServiceDefinition.csdef] , dans l’élément de **tâche** . L’attribut de la **ligne de commande** Spécifie le nom et les paramètres du fichier de commandes de démarrage ou de commande de la console, l’attribut **executionContext** Spécifie le niveau de privilège de la tâche de démarrage et de l’attribut **taskType** spécifie la façon dont la tâche est exécutée.

Dans cet exemple, une variable d’environnement, **MyVersionNumber**, est créée pour la tâche de démarrage et la valeur «**1.0.0.0**».

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

Dans l’exemple suivant, le fichier de commandes **Startup.cmd** écrit la ligne « la version actuelle est 1.0.0.0 » dans le fichier StartupLog.txt dans le répertoire spécifié par la variable d’environnement TEMP. Le `EXIT /B 0` ligne garantit que la tâche de démarrage se termine avec un **niveau d’erreur** égal à zéro.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] Dans Visual Studio, la propriété **Copier dans le répertoire de sortie** de votre fichier de commandes de démarrage doit indiquer **Toujours copier** pour vous assurer que votre fichier de commandes de démarrage est correctement déployé sur votre projet dans Azure (**approot\\emplacement** des rôles Web **approot** de rôles worker).

## <a name="description-of-task-attributes"></a>Description des attributs de tâche

La section suivante décrit les attributs de l’élément de **tâche** dans le fichier [ServiceDefinition.csdef] :

**ligne de commande** : Spécifie la ligne de commande pour la tâche de démarrage :

- La commande avec des paramètres de ligne de commande facultatifs, qui commence la tâche au démarrage.
- C’est aussi souvent le nom de fichier d’un fichier de commandes .cmd ou .bat.
- La tâche est par rapport à la AppRoot\\dossier Bin pour le déploiement. Variables d’environnement ne sont pas développées pour déterminer le chemin d’accès et le fichier de la tâche. Si l’extension de l’environnement est requise, vous pouvez créer un script .cmd small qui appelle votre tâche de démarrage.
- Peut être une application console ou un fichier batch qui démarre un [script PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - Spécifie le niveau de privilège de la tâche de démarrage. Le niveau de privilège peut être limité ou élevé :

- **limitée**  
La tâche de démarrage s’exécute avec les mêmes privilèges que le rôle. Lorsque l’attribut **executionContext** pour l’élément [Runtime] est également **limité**, les privilèges de l’utilisateur sont utilisés.

- **avec des privilèges élevés**  
La tâche de démarrage s’exécute avec des privilèges d’administrateur. Ainsi, les tâches de démarrage pour installer des programmes, apporter des modifications de configuration de IIS, effectuez les modifications du Registre et les autres tâches de niveau administrateur, sans augmenter le niveau de privilège du rôle lui-même.  

> [AZURE.NOTE] Le niveau de privilège d’une tâche de démarrage n’a pas besoin d’être le même que le rôle lui-même.

**taskType** - Spécifie le mode d’exécution d’une tâche de démarrage.

- **simple**  
Les tâches sont exécutées de façon synchrone, un à la fois, dans l’ordre spécifié dans le fichier [ServiceDefinition.csdef] . Lorsqu’une tâche de démarrage **simple** se termine par un **niveau d’erreur** égal à zéro, la tâche de démarrage **simple** suivante est exécutée. S’il n’y a aucune tâche de démarrage **simple** plus à exécuter, puis le rôle lui-même sera démarré.   

    > [AZURE.NOTE] Si la tâche **simple** se termine par un zéro **errorlevel**, l’instance sera bloquée. Tâches de démarrage ultérieures **simple** et le rôle lui-même, ne démarrent pas.

    Pour vous assurer que votre fichier de commandes se termine avec un **niveau d’erreur** égal à zéro, exécutez la commande `EXIT /B 0` à la fin de votre processus de fichier de traitement par lots.

- **arrière-plan**  
Les tâches sont exécutées de façon asynchrone, en parallèle avec le démarrage du rôle.

- **premier plan**  
Les tâches sont exécutées de façon asynchrone, en parallèle avec le démarrage du rôle. La principale différence entre un **premier plan** et une tâche **d’arrière-plan** est qu’une tâche de **premier plan** empêche le rôle de recyclage ou d’arrêt jusqu'à ce que la tâche est terminée. Les tâches **d’arrière-plan** n’ont pas cette restriction.

## <a name="environment-variables"></a>Variables d’environnement

Variables d’environnement sont un moyen de passer des informations à une tâche de démarrage. Par exemple, vous pouvez placer le chemin d’accès à un blob qui contient un programme d’installation, les numéros de port qui utilise votre rôle ou des paramètres aux fonctionnalités de contrôle de votre tâche de démarrage.

Il existe deux types de variables d’environnement pour les tâches de démarrage ; variables d’environnement statiques et les variables d’environnement basées sur des membres de la classe [RoleEnvironment] . Les deux se trouvent dans la section [environnement] du fichier [ServiceDefinition.csdef] , et utilisent tous les deux l’attribut d’élément et le **nom de** [Variable] .

Variables d’environnement statique utilise l’attribut **value** de l’élément [Variable] . L’exemple ci-dessus crée la variable d’environnement **MyVersionNumber** , qui a une valeur statique «**1.0.0.0**». Un autre exemple consisterait à créer une variable d’environnement **StagingOrProduction** qui vous pouvez définir manuellement les valeurs de «**test**» ou la «**production**» pour exécuter des actions de démarrage différentes en fonction de la valeur de la variable d’environnement **StagingOrProduction** .

Les variables d’environnement basées sur des membres de la classe RoleEnvironment n’utilisent pas l’attribut **value** de l’élément [Variable] . Au lieu de cela, l’élément enfant de [RoleInstanceValue] , avec la valeur de l’attribut **XPath** appropriée, sont utilisés pour créer une variable d’environnement basée sur un membre spécifique de la classe [RoleEnvironment] . Valeurs pour l’attribut **XPath** accéder aux différentes valeurs de [RoleEnvironment] sont disponibles [ici](cloud-services-role-config-xpath.md).



Par exemple, pour créer une variable d’environnement qui est «**true**» lorsque l’instance est en cours d’exécution dans l’émulateur de calcul et «**false**» lors de l’exécution dans le nuage, vous devez utiliser les éléments [Variable] et [RoleInstanceValue] suivants :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment effectuer des [tâches courantes de démarrage](cloud-services-startup-tasks-common.md) avec votre Service Cloud.

[Package de](cloud-services-model-and-package.md) votre Service Cloud.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tâche]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Démarrage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Moment de l’exécution]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environnement]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx