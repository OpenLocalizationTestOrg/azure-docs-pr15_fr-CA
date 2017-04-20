<properties
   pageTitle="Taille du dossier TEMP par défaut est trop petite pour un rôle | Microsoft Azure"
   description="Un rôle de service cloud possède une quantité limitée d’espace pour le dossier TEMP. Cet article fournit quelques suggestions sur la façon d’éviter de manquer d’espace."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Taille du dossier TEMP par défaut est trop petite sur un rôle web/travailleur de service cloud

Répertoire temporaire par défaut d’un rôle web ou travailleur de service cloud a une taille maximale de 100 Mo, ce qui peut arriver à saturation à un moment donné. Cet article décrit comment faire pour éviter de manquer d’espace pour le répertoire temporaire.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Pourquoi exécuter espace insuffisant ?

Les variables d’environnement Windows standard TEMP et TMP sont disponibles pour le code qui s’exécute dans votre application. TEMP et TMP pointent vers un répertoire unique qui possède une taille maximale de 100 Mo. Toutes les données qui sont stockées dans ce répertoire ne sont pas rendue persistante du cycle de vie du service nuage ; Si les instances de rôle dans un service en nuage sont recyclés, le répertoire est nettoyé.

## <a name="suggestion-to-fix-the-problem"></a>Suggestion pour corriger le problème.

Implémenter l’une des alternatives suivantes :

- Configurer une ressource de stockage local et y accéder directement et non à l’aide de TEMP ou TMP. Pour accéder à une ressource de stockage local à partir du code qui s’exécute au sein de votre application, appelez la méthode [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) . 

- Configurer une ressource de stockage local et pointez les répertoires TEMP et TMP pour pointer vers le chemin d’accès de la ressource de stockage local. Cette modification doit être effectuée dans la méthode [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

L’exemple de code suivant montre comment modifier les répertoires cibles pour TEMP et TMP à partir dans la méthode OnStart :


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Lisez un blog qui décrit [comment augmenter la taille du dossier temporaire Azure Web rôle ASP.NET](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Permet d’afficher plusieurs [articles de dépannage](/?tag=top-support-issue&product=cloud-services) des services en nuage.

Pour savoir comment résoudre les problèmes de rôle de service cloud à l’aide de données de diagnostic d’ordinateur Azure PaaS, afficher la [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
