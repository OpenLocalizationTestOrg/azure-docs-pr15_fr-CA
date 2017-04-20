<properties
   pageTitle="Déploiement d’Azure VM avec Chef | Microsoft Azure"
   description="Apprenez à utiliser le Chef pour effectuer le déploiement de machine virtuelle automatisée et la configuration de Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisation du déploiement d’Azure VM avec Chef

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chef est un excellent outil pour la livraison d’automation et configurations d’état.

Avec notre dernière version de nuage-api, Chef assure l’intégration transparente avec Azure, ce qui vous donne la possibilité de fournir et de déployer les États de configuration via une commande unique.

Dans cet article, je vais vous montrer comment configurer votre environnement de Chef à provisionner des machines virtuelles Azure et vous guide dans la création d’une stratégie ou « Livre de recettes » et déploiement ce livre de recettes pour une machine virtuelle Azure.

Nous allons commencer !

## <a name="chef-basics"></a>Chef de notions de base

Avant de commencer, je vous suggère de que vous passer en revue les concepts de base du Chef. Il est excellent matériel <a href="http://www.chef.io/chef" target="_blank">ici</a> et vous recommandons de vous avez une lecture rapide avant de tenter cette procédure pas à pas. J’ai cependant récapitulent les notions de base avant de commencer.

Le diagramme suivant illustre l’architecture de Chef de haut niveau.

![][2]

Chef a trois composants architecturaux principaux : serveur de Chef, le Chef Client (nœud) et Chef de station de travail.

Le serveur de Chef est notre point de gestion et de deux options pour le serveur Chef : une solution hébergée ou une solution sur site. Nous allons utiliser une solution hébergée.

Le Client de Chef (nœud) est l’agent qui se trouve sur les serveurs que vous gérez.

La station de travail Chef est notre station de travail administrateur où nous créer nos stratégies et exécuter notre gestion des commandes. Nous exécutons la commande **couteau** à partir de la station de travail Chef de gérer notre infrastructure.

Il est également le concept de « Cuisine » et « Recipes ». Elles sont en fait les stratégies que nous définissons et s’appliquent à nos serveurs.

## <a name="preparing-the-workstation"></a>Préparation de la station de travail

Tout d’abord, vous permet de préparer le poste de travail. J’utilise une station de travail Windows standard. Nous devons créer un répertoire pour stocker nos fichiers de configuration et les livres de cuisine.

Tout d’abord créer un répertoire appelé C:\chef.

Créez ensuite un second répertoire appelé c:\chef\cookbooks.

Nous devons maintenant télécharger notre fichier paramètres Azure pour Chef puisse communiquer avec notre abonnement Azure.

Télécharger vos paramètres à partir [d’ici](https://manage.windowsazure.com/publishsettings/)de publication.

Enregistrez le fichier de paramètres de publication dans C:\chef.

##<a name="creating-a-managed-chef-account"></a>Création d’un compte de Chef géré

Inscrivez-vous à un compte de Chef hébergé [ici](https://manage.chef.io/signup).

Au cours du processus d’inscription, vous devrez créer une nouvelle organisation.

![][3]

Une fois que votre organisation est créé, téléchargez le starter kit.

![][4]

> [AZURE.NOTE] Si vous recevez une invite d’avertissement vous indique que vos clés sont réinitialisées, il est OK pour continuer que nous n’avons configuré encore aucune infrastructure n’existe.

Ce fichier zip de starter kit contient votre entreprise de fichiers config et des clés.

##<a name="configuring-the-chef-workstation"></a>Configuration de la station de travail Chef

Extrayez le contenu de la starter.zip-chef de C:\chef.

Copiez tous les fichiers sous chef-starter\chef-emprunteuses\.chef dans le répertoire c:\chef.

Votre répertoire doit maintenant ressembler à l’exemple suivant.

![][5]

Vous devez maintenant avoir quatre fichiers, y compris le fichier de publication Azure dans la racine de c:\chef.

Les fichiers PEM contiennent votre organisation et l’administration des clés privées pour la communication alors que le fichier knife.rb contient la configuration de votre couteau. Nous devrons modifier le fichier knife.rb.

Ouvrez le fichier dans votre éditeur de choix et de modifier le « cookbook_path » en supprimant la /... / du chemin d’accès afin qu’il apparaisse comme indiqué ci-après.

    cookbook_path  ["#{current_dir}/cookbooks"]

Ajoutez également le ligne reflète le nom de votre Azure publie le fichier de paramètres.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Votre fichier knife.rb doit maintenant ressembler à l’exemple suivant.

![][6]

Ces lignes garantit que couteau référence le répertoire de livres de cuisine sous c:\chef\cookbooks et utilise également notre fichier de paramètres de publication Azure pendant les opérations d’Azure.

## <a name="installing-the-chef-development-kit"></a>Installez le Kit de développement de Chef

Suivant [télécharger et installer](http://downloads.getchef.com/chef-dk/windows) le ChefDK (Chef Development Kit) pour configurer votre station de travail du Chef.

![][7]

Installez dans l’emplacement par défaut de c:\opscode. Cette opération prendra environ 10 minutes.

Confirmer que votre variable PATH contient des entrées pour les C:\opscode\chefdk\bin ; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Si elles n’y ne sont pas, assurez-vous que vous ajoutez ces chemins d’accès !

*REMARQUE L’ORDRE DU CHEMIN EST IMPORTANT !* Si vos chemins d’accès d’opscode ne sont pas dans l’ordre correct, que vous aurez des problèmes.

Redémarrez votre poste de travail avant de continuer.

Ensuite, nous allons installer l’extension couteau Azure. Ainsi, couteau avec le Plugin « Azure ».

Exécutez la commande suivante.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] L’argument pre – garantit que vous recevez la dernière version de RC du plug-in d’Azure couteau qui permet d’accéder à la dernière série d’API.

Il est probable qu’un certain nombre de dépendances sera également installé en même temps.

![][8]


Pour vous assurer que tout est configuré correctement, exécutez la commande suivante.

    knife azure image list

Si tout est correctement configuré, vous verrez une liste de défilement images Azure disponibles par le biais de.

Félicitations. La station de travail est configurée.

##<a name="creating-a-cookbook"></a>Création d’un livre de recettes

Un livre de recettes permet de définir un ensemble de commandes que vous souhaitez exécuter sur votre client managé en Chef. Création d’un livre de recettes est simple, et nous utilisons la commande **chef générer des recettes** générer notre modèle de livres de cuisine. J’en appelant mon serveur web de conseils et astuces que j’aimerais une stratégie qui déploie automatiquement IIS.

Sous le répertoire C:\Chef, exécutez la commande suivante.

    chef generate cookbook webserver

Cela génère un jeu de fichiers sous le répertoire C:\Chef\cookbooks\webserver. Nous devons maintenant définir l’ensemble des commandes, nous souhaiterions notre client Chef d’exécuter sur notre ordinateur virtuel géré.

Les commandes sont stockées dans le fichier default.rb. Dans ce fichier, je vais définir un ensemble de commandes qui installe les services IIS, démarre IIS et copie d’un fichier de modèle dans le dossier wwwroot.

Modifiez le fichier C:\chef\cookbooks\webserver\recipes\default.rb et ajoutez les lignes suivantes.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Enregistrez le fichier lorsque vous avez terminé.

## <a name="creating-a-template"></a>Création d’un modèle

Comme nous l’avons mentionné précédemment, nous devons générer un fichier de modèle qui sera utilisé en tant que notre page default.html.

Exécutez la commande suivante pour générer le modèle.

    chef generate template webserver Default.htm

Maintenant, accédez au fichier C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Modifiez le fichier en ajoutant un code « Hello World » HTML simple et puis enregistrez le fichier.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Télécharger le Guide au serveur Chef

Dans cette étape, nous sommes en prenant une copie de la livre de recettes que nous avons créés sur la machine locale et le télécharger vers le serveur hébergé de Chef. Une fois téléchargé, le livre de recettes s’affiche sous l’onglet **stratégie** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Déployer un ordinateur virtuel avec un couteau Azure

Maintenant nous déployer un ordinateur virtuel Azure et appliquer les conseils et astuces de « Webserver » qui installera notre page de web IIS web service et par défaut.

Pour ce faire, utilisez la commande **créer de serveur de couteau azure** .

Suis d’exemple de la commande s’affiche ensuite.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Les paramètres sont explicites. Remplacez vos variables particuliers et exécuter.

> [AZURE.NOTE] Par le biais de la ligne de commande, je suis également automatisation mes règles de filtre de point de terminaison réseau en utilisant le paramètre – tcp-points de terminaison. J’ai ouvert les ports 80 et 3389 pour fournir l’accès à ma page web et d’une session RDP.

Une fois que vous exécutez la commande, accédez au portail Azure et vous verrez votre ordinateur commence à disposition.

![][13]

L’invite de commande s’affiche ensuite.

![][10]

Une fois le déploiement terminé, nous devons être capable de se connecter au service web sur le port 80 comme nous avions ouvert le port lorsque nous avons configuré l’ordinateur virtuel avec la commande couteau Azure. Que cet ordinateur virtuel est l’ordinateur virtuel uniquement dans mon service en nuage, je vous le connectez à l’url de service cloud.

![][11]

Comme vous pouvez le voir, j’ai creative avec mon code HTML.

N’oubliez pas que nous pouvons également connecter via une session RDP à partir du portail classique Azure via le port 3389.

J’espère que cela vous ont été utile ! Accédez et commencez votre infrastructure comme parcours de code avec Azure !


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->
