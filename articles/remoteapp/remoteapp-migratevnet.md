<properties
    pageTitle="Comment migrer à partir d’un VNET RemoteApp vers un VNET Azure | Microsoft Azure"
    description="Apprenez à migrer à partir d’un VNET RemoteApp à une VNET d’Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Comment migrer une collection hybride à partir d’un VNET RemoteApp à une VNET d’Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Bonne nouvelle ! Nous avons activé vous permet de déployer des collections de RemoteApp hybride directement dans votre existant Azure réseaux virtuels (VNETs) au lieu de créer des VNETs de RemoteApp spécifiques. Cela vous permet de tirer parti des dernières fonctionnalités VNET (comme ExpressRoute) et donner vos collections hybride d’un accès réseau direct à d’autres services Azure et les ordinateurs virtuels déployés à ce VNET.  (Cette dernière obtient vous améliorer les performances et le programme d’installation plus facile aux configurations VNET à VNET).


Supposons que vous avez déjà créé un hybride collection RemoteApp appelé *OriginalCollection* avec un VNET appelé *RemoteAppVNET*de RemoteApp. Voici les étapes à effectuer une migration vers un nouveau VNET d’Azure appelé *AzureVNET*.

1.  Sous l’onglet **réseaux** dans le [portail de gestion](http://manage.windowsazure.com/), créer un VNET appelé *AzureVNET*, en utilisant le même emplacement, la configuration DNS et espace d’adressage (pour au moins un des sous-réseaux *AzureVNET* ) que vous avez utilisé pour *RemoteAppVNET*.
2.  Configurer *AzureVNET* sur l’hôte ou disposer d’une connexion réseau pour le déploiement de Active Directory *OriginalCollection* est domaine joint à.
3.  Sous l’onglet **RemoteApps** , créer une nouvelle collection de RemoteApp appelée *Nouvelle Collection*. (Utilisez l’option de **créer avec VNET** , pas **Création rapide**.)
3.  Configurer *NewCollection* pour être déployés sur un sous-réseau dans *AzureVNET*.
4.  Configurer *NewCollection* pour utiliser la même image et les informations de jointure de domaine que vous avez utilisé pour *OriginalCollection*.
5.  Après quelques heures, *NewCollection* s’afficheront dans votre liste de collection avec un état actif.

Maintenant, si vous n’avez pas besoin de migrer des informations utilisateur à partir de la collection d’origine à la nouvelle collection, procédez ensuite comme suit :

6.  Supprimez *OriginalCollection*.
7.  Supprimez *RemoteAppVNET*.

Et bien, vous avez terminé !

Alternativement, si vous n’avez pas besoin de migrer des informations utilisateur à partir de la collection d’origine dans la nouvelle collection, procédez ensuite comme suit :

6.  Envoyez un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) avec votre ID d’abonnement Azure, le nom de votre collection d’origine et le nom de votre nouvelle collection et demandez-lui de faire migrer vos informations utilisateur.
7.  Sous 2 jours ouvrés l’équipe RemoteApp déplace la liste d’accès utilisateur et tous les documents de l’utilisateur et les paramètres utilisateur à partir de la collection d’origine vers la nouvelle collection.
8.  Supprimez *OriginalCollection*.
9.  Supprimez *RemoteAppVNET*.

Et maintenant, vous avez terminé !

Si vous avez des questions ou besoin d’une assistance spéciale, veuillez envoyer un e-mail [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
