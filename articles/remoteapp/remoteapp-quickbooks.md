<properties 
    pageTitle="Déploiement de QuickBooks dans Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment partager QuickBooks avec Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Comment pour déployer QuickBooks dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Utilisez les informations suivantes pour partager QuickBooks comme une application dans Azure RemoteApp.


Vous pouvez partager des QuickBooks 2015 Enterprise avec Azure RemoteApp dans la collection un hybride ou un nuage. Le fichier de la société doit résider sur un ordinateur virtuel qui exécute le serveur de base de données QuickBooks distinct à partir des serveurs Azure RemoteApp. Ne stockez jamais le fichier société sur votre image Azure RemoteApp - perte de données est prévu si vous le faites. Entreprise de QuickBooks seulement prend en charge l’hébergeant le fichier QuickBooks sur un partage externe avec le serveur de base de données QuickBooks accessible via le réseau standard de Windows.   

> [AZURE.IMPORTANT] Le serveur de base de données QuickBooks qui héberge le fichier de la société doit résider sur un ordinateur distinct virtuel au sein de la même VNET que la collection Azure RemoteApp.  

## <a name="steps-to-deploy-quickbooks"></a>Étapes pour déployer QuickBooks

1. Créer une VM Azure et installer QuickBooks, serveur de base de données QuickBooks et placez le fichier de la société sur une machine virtuelle d’Azure.  Assurez-vous de configurer correctement les règles de pare-feu.
2. Installation de QuickBooks sur une [image personnalisée](remoteapp-imageoptions.md) et créer une [collection de RemoteApp d’Azure](remoteapp-collections.md), nuage ou hybride, dans le VNET même exactement l’emplacement de la machine virtuelle qui héberge le serveur de base de données de QuickBooks avec des fichiers de la société. 
3.  [Publier](remoteapp-publish.md) Application de QuickBooks aux utilisateurs
4.  Lancer le client hébergé Azure RemoteApp de QuickBooks, naviguer à l’aide de la mise en réseau standard de Windows à la machine virtuelle qui héberge le serveur de base de données QuickBooks et ouvrez le fichier de la société. 

## <a name="documentation-references"></a>Références de la documentation

- QuickBooks [configurations prises en charge](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- QuickBooks, [options de déploiement](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Vous pouvez également extraire ma Ignite présentation, [principes de base de Microsoft Azure RemoteApp la gestion et l’Administration](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - avance rapide à 1:02:45 pour accéder à la partie de QuickBooks.

## <a name="deployment-architecture"></a>Architecture de déploiement

![QuickBooks + déploiement de RemoteApp Azure](./media/remoteapp-quickbooks/ra-quickbooks.png)