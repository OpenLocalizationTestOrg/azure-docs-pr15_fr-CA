<properties 
    pageTitle="Python web app avec Django sur Linux | Microsoft Azure" 
    description="Découvrez comment héberger une application web basée sur les Django sur Azure à l’aide d’une machine virtuelle Linux." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Application web de Hello World Django sur une VM Linux

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

Ce didacticiel explique comment héberger un site Web de base de Django sur Microsoft Azure à l’aide d’une machine virtuelle Linux. Ce didacticiel suppose que vous n’avez aucune expérience préalable à l’aide d’Azure. À la fin de ce guide, vous avez une application basée sur les Django et en cours d’exécution dans le nuage.

Vous allez apprendre comment :

* Installation d’une machine virtuelle Azure à l’hôte Django. Bien que ce didacticiel explique comment effectuer cette opération sous **Linux**, le même peut aussi être effectué avec un ordinateur de serveur de Windows virtuel hébergé dans Azure. 
* Créez une nouvelle application de Django de Linux.

En suivant ce didacticiel, vous allez générer une application web de Hello World simple. L’application sera hébergée dans une machine virtuelle Azure.

Une capture d’écran de l’application terminée est inférieure à :

![Une fenêtre de navigateur affichant la page world hello sur Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Création et configuration d’une machine virtuelle Azure à hôte Django

1. Suivez les instructions donnés [ici](virtual-machines-linux-quick-create-portal.md) pour créer un ordinateur virtuel Azure de la distribution *Ubuntu Server 14.04 LTS* .  Si vous préférez, vous pouvez choisir l’authentification par mot de passe au lieu de la clé publique de SSH.

1. Modifier le groupe de sécurité de réseau pour autoriser le trafic http entrant vers le port 80 à l’aide des instructions [ici](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Par défaut, votre nouvelle machine virtuelle n’a pas un nom de domaine pleinement qualifié (FQDN).  Vous pouvez en créer une en suivant les instructions fournies [ici](virtual-machines-linux-portal-create-fqdn.md).  Cette étape est facultative compléter ce didacticiel.

## <a id="setup"> </a>Configuration de l’environnement de développement

**Remarque :** Si vous devez installer les Python ou que vous souhaitez utiliser les bibliothèques de Client, consultez le [Guide d’Installation de Python](../python-how-to-install.md).

La VM de Linux Ubuntu est déjà fourni avec 2.7 Python préinstallé, mais il ne Apache ou django installé.  Suivez ces étapes pour vous connecter à votre machine virtuelle et installer Apache et django.

1.  Ouvrir une nouvelle fenêtre de **Terminal** .
    
1.  Entrez la commande suivante pour vous connecter à la machine virtuelle d’Azure.  Si vous n’avez pas créé un nom de domaine complet, vous pouvez vous connecter à l’aide de l’adresse IP publique affichée dans la synthèse dans Azure portal classique de machine virtuelle.

        $ ssh yourusername@yourVmUrl

1.  Entrez les commandes suivantes pour installer django :

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Entrez la commande suivante pour installer Apache avec mod-wsgi :

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Création d’une application Django

1.  Ouvrez la fenêtre de **Terminal** que vous utilisé dans la section précédente pour ssh dans votre machine virtuelle.
    
1.  Entrez les commandes suivantes pour créer un nouveau projet Django :

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    Le script **django-Admin.py** génère une structure de base pour les sites Web basés sur Django :
    -   **HelloWorld/Manage.py** vous permet d’héberger et d’arrêter l’hébergement de votre site Web basé sur Django
    -   **HelloWorld/HelloWorld/Settings.py** contient les paramètres de Django pour votre application.
    -   **HelloWorld/HelloWorld/URLs.py** contient le code de mappage entre chaque url et sa vue.

1.  Créer un nouveau fichier nommé **views.py** dans le répertoire **/var/www/helloworld/helloworld** . Il contiendra la vue qui restitue la page « hello world ». Démarrez votre éditeur et entrez les informations suivantes :
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Maintenant, remplacez le contenu du fichier **urls.py** par le texte suivant :

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>Configuration d’Apache

1.  Créer un fichier de configuration Apache hôte virtuel **/etc/apache2/sites-available/helloworld.conf**. Définir le contenu à la suivante et remplacez *yourVmName* par le nom réel de l’ordinateur que vous utilisez (par exemple, *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Activer le site avec la commande suivante :

        $ sudo a2ensite helloworld

1.  Redémarrez Apache avec la commande suivante :

        $ sudo service apache2 reload

1.  Enfin, chargez la page web dans votre navigateur :

    ![Une fenêtre de navigateur affichant la page world hello sur Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Arrêt de votre machine virtuelle Azure

Lorsque vous avez terminé avec ce didacticiel, arrêt et/ou de supprimer votre ordinateur virtuel Azure nouvellement créé pour libérer des ressources pour d’autres didacticiels et éviter de supporter des frais d’utilisation Azure.
