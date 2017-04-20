<properties
    pageTitle="Python web application Django | Microsoft Azure"
    description="Ce didacticiel vous enseigne comment héberger un site Web de base de Django sur Azure à l’aide d’un ordinateur virtuel de Windows Server 2012 R2 Datacenter en utilisant le modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Application web de Hello World Django sur une machine virtuelle de Windows Server

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Ce didacticiel explique comment héberger un site Web de base de Django sur Microsoft Azure à l’aide d’un ordinateur virtuel de Windows Server. Ce didacticiel suppose que vous n’avez aucune expérience préalable à l’aide d’Azure. À la fin de ce didacticiel, vous avez une application basée sur les Django et en cours d’exécution dans le nuage.

Vous allez apprendre comment :

* Configurer un ordinateur virtuel Azure à l’hôte Django. Bien que ce didacticiel explique comment effectuer cette opération sous Windows Server, le même peut aussi être effectué avec une machine virtuelle hébergée dans Azure de Linux.
* Créez une nouvelle application de Django à partir de Windows.

En suivant ce didacticiel, vous allez générer une application web de Hello World simple. L’application sera hébergée dans une machine virtuelle Azure.

Une capture d’écran de l’application terminée s’affiche ensuite.

![Une fenêtre de navigateur affichant la page world hello sur Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Création et configuration d’une machine virtuelle Azure à hôte Django

1. Suivez les instructions donnés [ici](virtual-machines-windows-classic-tutorial.md) pour créer un ordinateur virtuel Azure de la distribution de Windows Server 2012 R2 Datacenter.

1. Demander à Azure pour diriger le trafic du port 80 à partir du web au port 80 sur l’ordinateur virtuel :
 - Accédez à votre ordinateur virtuel nouvellement créé dans Azure portal classique et cliquez sur l’onglet **points de terminaison** .
 - Cliquez sur le bouton **Ajouter** au bas de l’écran.
    ![ajouter le point de terminaison](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Ouvrez le protocole **TCP** **80 de PORT PUBLIC** dans le cadre de **privé PORT 80**.
![][port80]
1. Sous l’onglet **tableau de bord** , cliquez sur **se connecter** pour utiliser **Bureau à distance** pour vous connecter à distance à l’ordinateur virtuel Azure nouvellement créé.  

**Remarque importante :** Toutes les instructions ci-dessous supposent que vous ouvert une session sur l’ordinateur virtuel correctement et émettant des commandes il plutôt que sur votre ordinateur local.

## <a id="setup"> </a>L’installation les Python, Django, WFastCGI

**Remarque :** Pour télécharger à l’aide d’Internet Explorer, il se peut que vous deviez configurer les paramètres d’Internet Explorer ÉCHAP (Démarrer/administration outils / / Local du Gestionnaire de serveur, puis cliquez sur **La Configuration de sécurité renforcée d’Internet Explorer**, la valeur Off).

1. Installez la dernière version 2.7 Python ou 3.4 à partir de [python.org][].
1. Installez les packages wfastcgi et django à l’aide du pip.

    Pour les Python 2.7, utilisez la commande suivante.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Pour les Python 3.4, utilisez la commande suivante.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>L’installation d’IIS avec FastCGI

1. Installez IIS avec prise en charge de FastCGI.  Cela peut prendre plusieurs minutes pour s’exécuter.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Création d’une application Django

1.  À partir de *C:\inetpub\wwwroot*, entrez la commande suivante pour créer un nouveau projet Django :

    Pour les Python 2.7, utilisez la commande suivante.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Pour les Python 3.4, utilisez la commande suivante.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Le résultat de la commande New-AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  La commande **django-admin** génère une structure de base pour les sites Web basés sur Django :

  -   **helloworld\manage.py** vous permet d’héberger et d’arrêter l’hébergement de votre site Web basé sur Django
  -   **helloworld\helloworld\settings.py** contient les paramètres de Django pour votre application.
  -   **helloworld\helloworld\urls.py** contient le code de mappage entre chaque url et sa vue.

1.  Créer un nouveau fichier nommé **views.py** dans le répertoire *C:\inetpub\wwwroot\helloworld\helloworld* . Il contiendra la vue qui restitue la page « hello world ». Démarrez votre éditeur et entrez les informations suivantes :

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Remplacez le contenu du fichier urls.py par le texte suivant.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Configuration d’IIS

1. Déverrouiller la section des gestionnaires dans le applicationhost.config global.  Cela permettra l’utilisation du Gestionnaire de python dans votre fichier web.config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Activer la WFastCGI.  Cela ajoutera le applicationhost.config global qui fait référence à votre interpréteur Python exécutable et le script de wfastcgi.py d’une application.

    Python 2.7 :

        c:\python27\scripts\wfastcgi-enable

    Python 3.4 :

        c:\python34\scripts\wfastcgi-enable

1. Créer un fichier web.config dans *C:\inetpub\wwwroot\helloworld*.  La valeur de la `scriptProcessor` l’attribut doit correspondre à la sortie de l’étape précédente.  Voir la page [wfastcgi][] sur pypi pour d’autres paramètres de wfastcgi sur.

    Python 2.7 :

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4 :

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Mettre à jour l’emplacement de l’IIS Site Web par défaut pour pointer vers le dossier du projet django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Enfin, chargez la page web dans votre navigateur.

![Une fenêtre de navigateur affichant la page world hello sur Azure][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Arrêt de votre machine virtuelle Azure

Lorsque vous avez terminé avec ce didacticiel, arrêter ou supprimer de votre ordinateur virtuel Azure nouvellement créé pour libérer des ressources pour d’autres didacticiels et éviter de supporter des frais d’utilisation Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
