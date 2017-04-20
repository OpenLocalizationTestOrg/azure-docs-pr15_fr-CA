
<properties 
    pageTitle="L’utilisation de votre abonnement à Office 365 avec Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment vous pouvez utiliser votre abonnement à Office 365 dans Azure RemoteApp pour partager des applications Office."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>L’utilisation de votre abonnement à Office 365 avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Saviez-vous que vous pouvez utiliser votre abonnement Office 365 dans Azure RemoteApp pour partager des applications Office à partir du cloud Lisez la suite pour plus d’informations sur votre Office 365 + les options de RemoteApp Azure, notamment liens vers des articles sur Office 365 vous aider à faire le meilleur parti de votre abonnement.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Comment utiliser les comptes d’Office 365 pour Azure RemoteApp ?
Découvrez le nouvel article de Peter de toutes les informations : [comment utiliser RemoteApp Azure avec des comptes d’utilisateur Office 365](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Puis-je utiliser mon abonnement à Office 365 pour exécuter des applications Office dans Azure RemoteApp ?

Oui ! En fait, à l’aide de votre abonnement à Office 365 est la seule façon de porter vos applications Office pour Azure RemoteApp.

(Remarque : Si votre déploiement d’Azure RemoteApp est fourni par un partenaire d’hébergement, qu’ils peuvent être en mesure de vous fournir des licences Office basés sur un [Contrat de licence de fournisseur de Service](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx))


L’avantage de l’abonnement à Office 365 est qu’il vous permet d’utiliser la même licence utilisateur sur de nombreuses plates-formes différentes et les environnements, y compris le nuage Azure. Lorsque vous utilisez des applications Office dans Azure RemoteApp vous n’avez pas besoin d’acheter des licences supplémentaires ou configurer vos licences existantes d’une manière particulière. Il vous suffit d’un abonnement à Office 365 qui inclut [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus permet [l’activation de l’ordinateur partagé](https://technet.microsoft.com/library/Dn782860.aspx) : cette fonction permet l’activation basée sur un utilisateur temporaire pour Office dans virtual et les environnements de cloud comme Azure RemoteApp (et aux Services Bureau à distance).

Les plans Office 365 incluent Office 365 ProPlus ? Reportez-vous à la table de la [disponibilité des services au sein de chaque plan](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Notez que pas tous les plans incluent Office 365 ProPlus (par exemple, le plan Office 365 Business). Si votre projet n’a pas, envisagez de mise à niveau vers un plan qui fait (par exemple, Office 365 éducation E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>OK, comment sont mon Office 365 licences ProPlus utilisés avec Azure RemoteApp ?

Chaque licence utilisateur pour Office 365 ProPlus permet à un seul utilisateur activer des applications Office sur jusqu'à 5 ordinateurs plus les tablettes et les téléphones portables. Chaque activation est enregistrée avec l’utilisateur jusqu'à ce qu’ils désactiver Office sur le périphérique. (Les utilisateurs peuvent gérer les périphériques dans le [portail Office 365](https://portal.office365.com/).)

Avec Azure RemoteApp un seul utilisateur peut ouvrir une session dans plusieurs ordinateurs sur le même jour sans vous en rendre compte. C’est parce que le service est automatiquement gère et met à l’échelle les ressources dans le nuage, tandis que l’utilisateur voit uniquement les applications et les programmes que vous avez partagés. Pour ce scénario, Office 365 ProPlus offre un mode de l’activation d’un ordinateur partagé, cela signifie que cet utilisateur n’a pas besoin effectuer la gestion des licences pour accéder à ces ressources et que les ordinateurs individuels ne comptent pas par rapport à la limite de l’activation de 5 ordinateur.

Tant que vous (l’administrateur) attribuez des licences Office 365 ProPlus à vos utilisateurs, ils peuvent utiliser Office sur leurs périphériques personnels, ainsi que par le biais de votre collection Azure RemoteApp.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Les applications Office puis-je utiliser avec Office 365 et Azure RemoteApp ?

Votre abonnement à Office 365 vous permet d’activer et partage Office 2013 dans les déploiements d’Azure RemoteApp. Actuellement, nous ne gèrent pas l’utilisation d’autres versions d’Office avec Azure RemoteApp. Cela inclut d’Office 2003, Office 2007, Office 2010 et Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>Qu’en est-il des Pro de Visio ou Project Pro ?

L’image d’Office 365 ProPlus inclus dans votre abonnement RemoteApp inclut Pro de Visio et Project Pro. Mais vous ne pouvez pas utiliser votre abonnement Office 365 ProPlus pour activer ces programmes, ils ont chacun leur propre licence. Vous pouvez les activer dans le [portail Office 365](https://portal.office365.com/). 

Vous n’êtes pas obligé de licence de ces programmes si vous ne souhaitez pas les utiliser. Il vous suffit d’activer les programmes que vous souhaitez utiliser - et ignorer les autres. Vous pouvez toujours les voir dans l’image, mais vous ne pouvez pas les utiliser. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Comment commencer avec Office 365 et Azure RemoteApp ?

Maintenant que vous connaissez les détails de la licence d’Office 365, nous allons vous aider à démarrer à l’aide de la dans Azure RemoteApp, c’est très simple :

Lorsque vous créez votre collection RemoteApp d’Azure, utilisez l’image **Office 365 ProPlus (inscription requise)** .

![Image de RemoteApp Azure avec Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Cette image contient la dernière version de Windows Server et Office 365 ProPlus. Après avoir configuré votre collection (y compris les applications publication), vos utilisateurs simplement ouvrir une session dans Azure RemoteApp (à l’aide de leur client de RemoteApp) et fournir leurs informations d’identification d’Office 365 pour les applications Office. Licences sont automatiquement remis sans n’importe quel jeu ou de gestion requis.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Puis-je créer une image personnalisée avec Office 365 ProPlus ?

Vous pouvez créer une image personnalisée pour votre collection qui contient Office 365 ProPlus. Il y a 2 choix - utiliser l’image de la galerie Azure que nous fournir ou vous pouvez créer votre propre image personnalisée et installez Office 365 ProPlus il.

### <a name="use-the-azure-gallery-image"></a>Utiliser l’image de la galerie Azure

La façon la plus simple pour déployer Office 365 ProPlus à une collection doit [commencer par une des images de la galerie Azure](remoteapp-image-on-azurevm.md) inclus dans votre abonnement Azure RemoteApp. Assurez-vous de que sélectionner l’image **Windows Server à distance hôte de Session bureau avec Office 365 ProPlus est pré-installé** . Ensuite, installez les autres applications que vous souhaitez sur cette image, et vous êtes fin prêt.

### <a name="use-a-custom-image"></a>Utiliser une image personnalisée

Vous pouvez toujours créer une image personnalisée, vous pouvez créer un [Azure VM](remoteapp-image-on-azurevm.md) , ou [créer l’image localement](remoteapp-create-custom-image.md) et téléchargez-le dans Azure. Dans les deux cas, assurez-vous que vous installez Office 365 ProPlus en utilisant le nœud de l’activation d’un ordinateur partagé. Utilisez l' [Outil de déploiement d’Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) et suivez les [instructions](https://technet.microsoft.com/library/Dn782858.aspx) pour l’installation.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Désactiver les mises à jour automatiques pour Office 365 ProPlus dans votre image personnalisée - IMPORTANT

Votre image personnalisée est utilisée par Azure RemoteApp comme un modèle pour l’ajout de ressources supplémentaires en tant que la demande d’augmentation de vos utilisateurs. Pour éviter les retards et problèmes de connexion, désactivez les mises à jour automatiques pour Office dans l’image. Si vous ne le faites pas, puis chaque ressource créée avec ce modèle met automatiquement à jour lorsqu’il est démarré. Au lieu de cela, utilisez le processus d’Azure RemoteApp standard de mise à jour de votre image personnalisée. De cette manière vous mettre à jour les applications Office une fois sur l’image du modèle et que vous laissez Azure RemoteApp, prenez soin d’obtenir les mises à jour pour vos utilisateurs.

Pour désactiver les mises à jour automatiques, ajoutez le code suivant du fichier de configuration d’outil de déploiement de Office :

        <Updates Enabled="FALSE" />

Maintenant, votre fichier de configuration doit contenir les lignes :
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Comment puis-je mettre à jour une image avec Office 365 ProPlus ?

Il existe de nombreuses raisons pour mettre à jour l’image dans votre collection. Voici quelques :

- Obtenir les dernières mises à jour de Windows 
- Obtenir les dernières mises à jour application Office 365 ProPlus
- Mise à jour de votre application personnalisée
- Modifier les autres paramètres de configuration pour l’image elle-même

Pour les étapes de bout en bout pour la mise à jour de votre collection pour utiliser l’image mise à jour, consultez la rubrique [ici](remoteapp-update.md). Mais, pour plus d’informations sur la façon de mettre à jour l’image et Office 365 ProPlus, vérifiez les informations suivantes.

Vous disposez de deux options pour mettre à jour votre image - remplacer votre image avec un complètement nouveau ou manuellement mettre à jour votre image existant.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Remplacez votre image avec la dernière image de la galerie Azure + ajouter des personnalisations
Avec cette option, vous laisser Microsoft prennent en charge les mises à jour de Windows Server et Office 365 ProPlus. Au lieu de la mise à jour de votre image existante, vous allez créer une image entièrement nouvelle, basée sur la dernière image de la galerie. Ensuite, répétez les étapes que vous le faisiez auparavant pour personnaliser l’image - installation des applications personnalisées, modifiez la configuration de l’image, etc..

Les images de la galerie sont régulièrement mis à jour, vous pouvez l’esprit tranquille, sachant que vos applications Windows Server et Office 365 ProPlus sont à jour. Bien entendu, le compromis est que vous devrez appliquer vos personnalisations chaque fois que vous recevez une nouvelle image. Vous pouvez créer des scripts pour automatiser la configuration de vos personnalisations.

### <a name="manually-update-your-existing-image"></a>Mettre à jour manuellement votre image existante

Avec cette option, les outils Windows standard vous permet d’appliquer des mises à jour à l’image. Pour Office 365 ProPlus, utilisez l’outil de déploiement de Office pour télécharger et installer les dernières mises à jour ou les versions d’Office 365 ProPlus.

> [AZURE.IMPORTANT] N’oubliez pas : désactiver les mises à jour automatiques Office 365 ProPlus.

Vous avez besoin de plus d’informations sur les mises à jour à l’aide de l’outil de déploiement de Microsoft Office ?

- [Déploiement en un clic pour les produits Office 365 à l’aide de l’outil de déploiement de Microsoft Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Le déploiement et la mise à jour d’Office 365 ProPlus à l’aide de l’outil de déploiement de Microsoft Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vidéo)
- [Configurer les paramètres de mise à jour pour Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)
