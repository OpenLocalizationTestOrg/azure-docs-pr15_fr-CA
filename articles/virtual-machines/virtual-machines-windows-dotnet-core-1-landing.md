<properties
   pageTitle="Azure Virtual Machine DotNet Core didacticiel 1 | Microsoft Azure"
   description="Didacticiel de DotNet Core Azure VM"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automatisation des déploiements d’applications pour Azure Virtual Machines

Cette série de quatre détaille le déploiement et la configuration des ressources Azure et les applications à l’aide de modèles de gestion des ressources d’Azure. Dans cette série, un exemple de modèle est déployé et le modèle de déploiement est examiné. L’objectif de cette série est de former sur la relation entre ressources Azure et de fournir des mains sur l’expérience de déploiement de modèles de gestionnaire de ressources Azure entièrement intégrée. Ce document suppose un niveau de base de connaissances avec le Gestionnaire de ressources Azure, avant de commencer ce didacticiel vous familiariser avec les concepts de base Azure le Gestionnaire de ressources.

## <a name="music-store-application"></a>Application de magasin de musique

L’exemple utilisé dans cette série est un .net application cœur simulant un magasin de musique expérience d’achat. Cette application peut être déployée sur un Linux ou Windows de système virtuel, déploiements exemple ont été créés pour les deux. L’application inclut une application web et une base de données SQL. Avant de lire les articles de cette série, déployer l’application à l’aide du bouton de déploiement trouvé sur cette page. Lorsque complètement déployé, l’architecture de l’application / Azure ressemble le diagramme suivant. 

Le modèle de gestionnaire de ressources de stockage de musique se trouve ici, [Modèle de Windows de magasin de musique](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Application magasin de musique](./media/virtual-machines-windows-dotnet-core/music-store.png)

Chacun de ces composants, y compris le modèle associer JSON est examinée dans les articles suivants de quatre.

- [**Architecture de l’application**](./virtual-machines-windows-dotnet-core-2-architecture.md) – composants d’Application tels que les bases de données et les sites web doivent être hébergés sur des ressources telles que des machines virtuelles et les bases de données Azure SQL Azure. Ce document Guide besoin de calcul de mappage, ressources Azure, et du déploiement de ces ressources avec un modèle de gestionnaire de ressources Azure. 

- [**Accès et sécurité**](./virtual-machines-windows-dotnet-core-3-access-security.md) – lors de l’hébergement des applications dans Azure, il est nécessaire d’envisager comment accéder à l’application et comment les différents accès de composants d’application mutuellement. Ce document détaille la fourniture et la sécurisation des accès internet à une application et entre les composants de l’application.

- [**Disponibilité et meilleure évolutivité**](./virtual-machines-windows-dotnet-core-4-availability-scale.md) , disponibilité et meilleure évolutivité font référence à la capacité des applications à rester en cours d’exécution lors de l’arrêt de l’infrastructure et la capacité d’évoluer pour répondre à la demande de l’application des ressources de calcul. Ce document détails les composants nécessaires pour déployer une équilibrage de la charge et une application hautement disponible.

- [**Déploiement d’applications**](./virtual-machines-windows-dotnet-core-5-app-deployment.md) - lors du déploiement d’applications sur Azure Virtual Machines, la méthode par laquelle les fichiers binaires sont installés sur l’ordinateur virtuel peut être envisagée. Ce document détaille l’automatisation installation d’une application à l’aide des Extensions de Script Azure ordinateur virtuel personnalisé.

Lorsque vous développez des modèles du Gestionnaire de ressources Azure vise à automatiser le déploiement de l’Infrastructure d’Azure et l’installation et la configuration de toutes les applications hébergées sur cette infrastructure Azure. Travail par le biais de ces articles fournit un exemple de cette expérience.

## <a name="deploy-the-music-store-application"></a>Déployez l’application magasin de musique

L’application magasin de musique peut être déployée à l’aide de ce bouton.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Le modèle de gestionnaire de ressources Azure requiert que les valeurs de paramètre suivantes.

|Nom du paramètre |Description   |
|---|---|
|ADMINUSERNAME   | Nom d’utilisateur Admin est utilisé sur l’ordinateur virtuel et la base de données de SQL Azure.  |
|ADMINPASSWORD | Mot de passe qui est utilisé sur les de la base de données SQL Azure Virtual Machine.  |
|NUMBEROFINSTANCES | Le nombre de machines virtuelles à créer. Chacun de ces ordinateurs virtuels héberger l’application web de magasin de musique, et tout le trafic est équilibré entre eux. |
|PUBLICIPADDRESSDNSNAME | Nom DNS associé à l’adresse IP publique globalement unique. |

Une fois le déploiement du modèle terminée, accédez à l’adresse IP publique à l’aide de n’importe quel navigateur internet. .Net site musical de base s’affiche.

## <a name="next-steps"></a>Étapes suivantes

<hr>

[Étape 1 - Architecture des applications avec des modèles de gestionnaire de ressources Azure](./virtual-machines-windows-dotnet-core-2-architecture.md)

[Étape 2 : accès et sécurité dans Azure modèles du Gestionnaire de ressources](./virtual-machines-windows-dotnet-core-3-access-security.md)

[Étape 3 : disponibilité et évolutivité dans Azure modèles du Gestionnaire de ressources](./virtual-machines-windows-dotnet-core-4-availability-scale.md)

[Étape 4 - déploiement d’applications avec modèles de gestionnaire de ressources Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)


