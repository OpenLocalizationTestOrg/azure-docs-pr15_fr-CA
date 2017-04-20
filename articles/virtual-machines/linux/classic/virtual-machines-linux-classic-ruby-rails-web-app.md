<properties
    pageTitle="Héberger un Ruby sur le site Web de Rails sur une VM Linux | Microsoft Azure"
    description="Configurer et héberger un Ruby sur site de Web basée sur des Rails sur Azure à l’aide d’une machine virtuelle Linux."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Ruby sur l’application Web de Rails sur une machine virtuelle d’Azure

Ce didacticiel explique comment héberger un Ruby sur le site Web de Rails sur Azure à l’aide d’une machine virtuelle Linux.  

Ce didacticiel a été validé à l’aide d’Ubuntu Server 14.04 LTS. Si vous utilisez une autre distribution de Linux, vous devrez peut-être modifier les étapes pour installer les Rails.

> [AZURE.IMPORTANT] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources et classique](../../../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement classique. Microsoft recommande que la plupart des nouveaux déploiements utilisent le modèle de gestionnaire de ressources.

## <a name="create-an-azure-vm"></a>Créer une VM Azure

Commencez par créer un ordinateur virtuel d’Azure avec une image de Linux.

Pour créer la machine virtuelle, vous pouvez utiliser le portail classique Azure ou l’Azure Interface de ligne de commande (CLI).

### <a name="azure-management-portal"></a>Portail de gestion Azure

1. Connectez-vous au [Azure portal classique](http://manage.windowsazure.com)
2. Cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **Création rapide**. Sélectionnez une image de Linux.
3. Entrez un mot de passe.

Une fois que la machine virtuelle est configurée, cliquez sur le nom de la machine virtuelle et cliquez sur le **tableau de bord**. Rechercher le point de terminaison SSH, répertorié sous **Détails SSH**.

### <a name="azure-cli"></a>CLI Azure

Suivez les étapes dans [créer un Linux exécutant d’ordinateur virtuel][vm-instructions].

Une fois que la machine virtuelle est configurée, vous pouvez obtenir le point de terminaison SSH en exécutant la commande suivante :

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installer Ruby sur Rails

1. Utiliser le protocole SSH pour se connecter à la machine virtuelle.

2. À partir de la session SSH, utilisez les commandes suivantes pour installer Ruby sur l’ordinateur virtuel :

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    L’installation peut prendre quelques minutes. Lorsqu’elle est terminée, utilisez la commande suivante pour vérifier que Ruby est installé :

        ruby -v

    Cette propriété renvoie la version de Ruby qui a été installé.

3. Pour installer les Rails, utilisez la commande suivante :

        sudo gem install rails --no-rdoc --no-ri -V

    Utilisation : no-rdoc et--non-ri indicateurs d’ignorer l’installation de la documentation, ce qui est plus rapide.
    Cette commande prendra probablement beaucoup de temps à s’exécuter, afin que l’ajout de la V - Affiche des informations sur la progression de l’installation.

## <a name="create-and-run-an-app"></a>Créer et exécuter une application

Bien que toujours connecté via le protocole SSH, exécutez les commandes suivantes :

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

La [nouvelle](http://guides.rubyonrails.org/command_line.html#rails-new) commande crée une nouvelle application de Rails. La commande [server](http://guides.rubyonrails.org/command_line.html#rails-server) démarre le serveur web WEBrick qui est fourni avec les Rails. (Pour la production, serait probablement voulez-vous utiliser un autre serveur, comme la licorne ou le passager.)

Vous devez voir une sortie similaire à ce qui suit.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Ajouter un point de terminaison

1. Atteindre le [Azure portal classique] [ management-portal] et sélectionnez votre machine virtuelle.

    ![liste des machines virtuelles][vmlist]

2. Sélectionnez les **points de terminaison** en haut de la page, puis cliquez sur **+ Ajouter un point de terminaison** au bas de la page.

    ![page points de terminaison][endpoints]

3. Dans la boîte de dialogue **Ajouter un point de terminaison** , sélectionnez « Ajouter un point de terminaison autonome » et cliquez sur la flèche **suivant** .

    ![boîte de dialogue Nouveau point de terminaison][new-endpoint1]

3. Dans la page suivante de la boîte de dialogue, entrez les informations suivantes :

    * **Nom**: HTTP

    * **Protocole**: TCP

    * **PORT PUBLIC**: 80

    * **PORT de privée**: 3000

    Cette opération crée un port public de 80 qui achemine le trafic vers le port privé de 3000, où le serveur de Rails est à l’écoute.

    ![boîte de dialogue Nouveau point de terminaison][new-endpoint]

4. Cliquez sur la case à cocher pour enregistrer le point de terminaison.

5. Un message doit s’afficher indiquant la **Mise à jour en cours**. Une fois ce message disparaît, le point de terminaison est actif. Vous pouvez maintenant tester votre application en navigant vers le nom DNS de votre machine virtuelle. Le site Web doit ressembler à ce qui suit :

    ![page de rails par défaut][default-rails-cloud]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué la plupart des étapes manuellement. Dans un environnement de production, vous écrivez votre application sur un ordinateur de développement et le déployer sur la machine virtuelle d’Azure. En outre, la plupart des environnements de production hébergent l’application de Rails en conjonction avec un autre processus serveur Apache ou NginX, qui gère la demande routage à plusieurs instances de l’application de Rails et servant de ressources statiques. Pour plus d’informations, consultez http://rubyonrails.org/deploy/.

Pour en savoir plus sur Ruby on Rails, visitez le [Ruby on Rails Guides][rails-guides].

Pour utiliser des services Azure à partir de votre application Ruby, voir :

* [Stocker des données non structurées à l’aide d’objets BLOB][blobs]

* [Paires clé/valeur de banque d’informations à l’aide de tables][tables]

* [Fournir du contenu d’une bande passante élevée avec le réseau de livraison de contenu][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
