<properties
   pageTitle="Configurer votre environnement de développement | Microsoft Azure"
   description="Installer les outils, le Kit de développement logiciel et le runtime et créer un cluster de développement local. Après avoir terminé cette installation, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Préparation de votre environnement de développement

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Pour générer et exécuter [les applications Azure Fabric de Service] [ 1] sur votre ordinateur de développement, installez le runtime, SDK et outils. Vous devez également permettre l’exécution de scripts Windows PowerShell inclus dans le Kit de développement.

## <a name="prerequisites"></a>Conditions préalables
### <a name="supported-operating-system-versions"></a>Versions des systèmes d’exploitation pris en charge
Les versions de système d’exploitation suivants sont pris en charge pour le développement :

- Windows 7
- Windows 8 et Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 inclut uniquement Windows PowerShell 2.0 par défaut. Applets de commande PowerShell de tissu de service nécessite PowerShell 3.0 ou version ultérieure. Vous pouvez [télécharger Windows PowerShell 5.0] [ powershell5-download] à partir du Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Installer les outils, le Kit de développement logiciel et le runtime

Le programme d’installation de la plate-forme Web offre deux configurations pour le développement de Service Fabric :

- [Installer le Service Fabric runtime, SDK et outils de Visual Studio 2015 (nécessite une mise à jour de Visual Studio 2015 2 ou version ultérieure)][full-bundle-vs2015]
- [Installer le Service Fabric runtime et le Kit de développement logiciel uniquement (pas d’outils de Visual Studio)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Activer l’exécution du script PowerShell

Service de Fabric utilise les scripts Windows PowerShell pour créer un cluster de développement local et pour déployer des applications à partir de Visual Studio. Par défaut, Windows empêche l’exécution de ces scripts. Pour les activer, vous devez modifier votre stratégie d’exécution PowerShell. Ouvrir PowerShell en tant qu’administrateur et entrez la commande suivante :

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé la configuration de votre environnement de développement, démarrer la génération et l’exécution des applications.

- [Créer votre première application Fabric du Service dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Apprenez à déployer et à gérer les applications sur votre cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [En savoir plus sur les modèles de programmation : des Services fiables et des acteurs fiable](service-fabric-choose-framework.md)
- [Consultez les exemples de code de Service Fabric sur GitHub](https://aka.ms/servicefabricsamples)
- [Visualiser votre cluster à l’aide de l’Explorateur Service Fabric](service-fabric-visualizing-your-cluster.md)
- [Suivez le chemin d’accès de la formation de tissu de Service pour obtenir une large introduction à la plate-forme](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Page de campagne service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "RC DE VS"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Lien de VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI lien"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Lien du SDK WebPI Core"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
