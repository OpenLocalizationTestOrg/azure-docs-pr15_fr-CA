<properties
    pageTitle="Les rôles web et worker PHP | Microsoft Azure"
    description="Un guide de création de rôles web et worker PHP dans un service cloud Azure et configurez l’exécution de PHP."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Comment faire pour créer des rôles web et worker PHP

## <a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment créer des rôles de web ou travailleur PHP dans un environnement de développement Windows, choisissez une version spécifique de PHP à partir des versions « intégrées » disponibles, modifier la configuration de PHP, activer les extensions et à déployer sur Azure. Il décrit également comment configurer un rôle web ou travailleur pour utiliser un runtime PHP (avec configuration personnalisée et extensions) que vous fournissez.

## <a name="what-are-php-web-and-worker-roles"></a>Quels sont les rôles web et worker PHP ?

Azure propose trois modèles d’exécution d’applications de calcul : Service d’application Azure, ordinateurs virtuels Azure et les Services en nuage Azure. Les trois modèles prennent en charge le PHP. Les Services en nuage, qui inclut les rôles web et worker, fournit la *plate-forme en tant que service (PaaS)*. Au sein d’un service en nuage, un rôle web fournit un serveur dédié de web Internet Information Services (IIS) pour héberger des applications web frontal. Un rôle worker peut exécuter asynchrones, longue ou définitive des tâches indépendantes de l’intervention de l’utilisateur ou l’entrée.

Pour plus d’informations sur ces options, consultez [options d’hébergement fournies par Azure de calcul](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Télécharger le Kit de développement Azure pour PHP

Le [Kit de développement logiciel Azure pour PHP] est constitué de plusieurs composants. Cet article utilise deux d'entre eux : Azure PowerShell et les émulateurs Azure. Ces deux composants peuvent être installés via le de Microsoft Web Platform Installer. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Créez un projet de Services Cloud

La première étape de la création d’un rôle web ou travailleur PHP est de créer un projet de Service d’Azure. un projet de Service d’Azure sert de conteneur logique pour les rôles web et worker, et il contient les fichiers de [définition de service (.csdef)] et de la [configuration du service (.cscfg)] du projet.

Pour créer un nouveau projet de Service d’Azure, exécutez Azure PowerShell en tant qu’administrateur et exécutez la commande suivante :

    PS C:\>New-AzureServiceProject myProject

Cette commande crée un nouveau répertoire (`myProject`) à laquelle vous pouvez ajouter des rôles web et worker.

## <a name="add-php-web-or-worker-roles"></a>Ajouter des rôles web ou travailleur PHP

Pour ajouter un rôle de web PHP à un projet, exécutez la commande suivante dans le répertoire du projet racine :

    PS C:\myProject> Add-AzurePHPWebRole roleName

Pour un rôle worker, utilisez cette commande :

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] Le `roleName` paramètre est facultatif. S’il est omis, le nom de rôle sera généré automatiquement. Le premier rôle de web créé sera `WebRole1`, le second sera `WebRole2`, et ainsi de suite. Le premier rôle de travail créé sera `WorkerRole1`, le second sera `WorkerRole2`, et ainsi de suite.

## <a name="specify-the-built-in-php-version"></a>Spécifier la version PHP intégrée

Lorsque vous ajoutez un rôle web ou travailleur PHP à un projet, les fichiers de configuration du projet sont modifiées afin que PHP sera installé sur chaque instance du web ou travailleur de votre application lorsqu’elle est déployée. Pour afficher la version de PHP qui sera installé par défaut, exécutez la commande suivante :

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

La sortie de la commande ci-dessus est similaire à celui présenté ci-dessous. Dans cet exemple, le `IsDefault` est défini à `true` pour PHP 5.3.17, indiquant qu’il sera installée la version de PHP par défaut.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Vous pouvez définir la version du runtime PHP à une des versions PHP qui sont répertoriées. Par exemple, pour définir la version PHP (pour un rôle portant le nom `roleName`) à 5.4.0, utilisez la commande suivante :

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Les versions PHP disponibles peuvent changer à l’avenir.

## <a name="customize-the-built-in-php-runtime"></a>Personnaliser l’exécution PHP intégrée

Vous avez un contrôle total sur la configuration du runtime PHP est installé lorsque vous suivez les étapes ci-dessus, y compris la modification de `php.ini` les paramètres et l’activation des extensions.

Pour personnaliser l’exécution PHP intégrée, procédez comme suit :

1. Ajouter un nouveau dossier, nommé `php`, à la `bin` répertoire de votre rôle de web. Pour un rôle de collaborateur, vous devez l’ajouter à un répertoire de racine du rôle.
2. Dans le `php` dossier, créez un autre dossier appelé `ext`. Placer les `.dll` les fichiers d’extension (par exemple, `php_mongo.dll`) que vous souhaitez activer dans ce dossier.
3. Ajouter un `php.ini` de fichiers vers le `php` dossier. Activer les extensions personnalisées et définir toutes les directives PHP dans ce fichier. Par exemple, si vous souhaitez activer le `display_errors` sur et activer la `php_mongo.dll` extension, le contenu de votre `php.ini` fichier se présente comme suit :

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Tous les paramètres que vous ne définissez pas explicitement dans la `php.ini` fichier que vous fournissez sera automatiquement fixé à leurs valeurs par défaut. Cependant, gardez à l’esprit que vous pouvez ajouter un texte complet `php.ini` fichier.

## <a name="use-your-own-php-runtime"></a>Utilisez votre propre runtime PHP
Dans certains cas, au lieu de la sélection d’un runtime PHP intégré et en le configurant comme décrit ci-dessus, vous souhaiterez peut-être fournir votre propre runtime PHP. Par exemple, vous pouvez utiliser le même runtime PHP dans un rôle web ou de travail que vous utilisez dans votre environnement de développement. Cela rend plus facile de garantir que l’application ne change pas de comportement dans votre environnement de production.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurer un rôle web pour utiliser votre propre runtime PHP

Pour configurer un rôle web pour utiliser un runtime PHP que vous fournissez, procédez comme suit :

1. Créez un projet de Service d’Azure et ajoutez un rôle web PHP, comme décrit précédemment dans cette rubrique.
2. Créer un `php` dossier dans le `bin` dossier qui se trouve dans le répertoire racine de votre rôle de serveur web, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) à la `php` dossier.
3. (FACULTATIF) Si votre runtime PHP utilise les [Pilotes Microsoft pour PHP pour SQL Server][sqlsrv drivers], vous devez d’abord configurer votre rôle web pour installer [SQL Server Native Client 2012] [ sql native client] lorsqu’il est mis en service. Pour ce faire, ajoutez l' [installateur de sqlncli.msi x64] à la `bin` dossier dans le répertoire racine de votre rôle de serveur web. Le script de démarrage décrit à l’étape suivante s’exécute en mode silencieux du programme d’installation lorsque le rôle est mis en service. Si votre runtime PHP n’utilise pas le Microsoft Drivers pour PHP pour SQL Server, vous pouvez supprimer la ligne suivante à partir du script illustré à l’étape suivante :

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Définir une tâche de démarrage qui configure [Internet Information Services (IIS)] [ iis.net] à votre runtime PHP permet de gérer les demandes de `.php` pages. Pour ce faire, ouvrez le `setup_web.cmd` fichier (dans le `bin` le fichier du répertoire racine de votre rôle de serveur web) dans un éditeur de texte et remplacez son contenu par le script suivant :

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Ajoutez vos fichiers d’application pour le répertoire racine de votre rôle de serveur web. Ce sera le répertoire de racine du serveur web.

6. Publier votre application comme décrit dans la section [publier votre application](#how-to-publish-your-application) ci-dessous.

> [AZURE.NOTE] Le `download.ps1` script (dans le `bin` dossier du répertoire racine du rôle de serveur web) peut être supprimé après avoir suivi les étapes décrites ci-dessus pour l’utilisation de votre propre runtime PHP.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurer un rôle de collaborateur pour utiliser votre propre runtime PHP

Pour configurer un rôle de collaborateur pour utiliser une exécution PHP que vous fournissez, procédez comme suit :

1. Créer un projet de Service d’Azure et d’ajouter un rôle de collaborateur PHP, comme décrit précédemment dans cette rubrique.
2. Créer un `php` dossier dans le répertoire racine du rôle travailleur, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) à la `php` dossier.
3. (FACULTATIF) Si votre runtime PHP utilise des [Pilotes Microsoft pour PHP pour SQL Server][sqlsrv drivers], vous devez d’abord configurer votre rôle de collaborateur pour installer [SQL Server Native Client 2012] [ sql native client] lorsqu’il est mis en service. Pour ce faire, ajoutez [programme d’installation de sqlncli.msi x64] au répertoire racine du rôle travailleur. Le script de démarrage décrit à l’étape suivante s’exécute en mode silencieux du programme d’installation lorsque le rôle est mis en service. Si votre runtime PHP n’utilise pas le Microsoft Drivers pour PHP pour SQL Server, vous pouvez supprimer la ligne suivante à partir du script illustré à l’étape suivante :

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Définir une tâche de démarrage qui ajoute votre `php.exe` exécutable à la variable d’environnement PATH du travailleur rôle lorsque le rôle est mis en service. Pour ce faire, ouvrez le `setup_worker.cmd` de fichiers dans un éditeur de texte (dans le répertoire racine du rôle Collaborateur) et remplacez son contenu par le script suivant :

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Ajoutez vos fichiers d’application pour le répertoire racine de votre rôle Collaborateur.

6. Publier votre application comme décrit dans la section [publier votre application](#how-to-publish-your-application) ci-dessous.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Exécutez votre application dans les émulateurs de calcul et de stockage

Les émulateurs Azure fournissent un environnement local, dans lequel vous pouvez tester votre application Azure avant de le déployer dans le nuage. Il existe certaines différences entre les émulateurs et l’environnement Azure. Pour mieux comprendre ce point, voir [utiliser l’émulateur de stockage Azure pour le développement et le test](./storage/storage-use-emulator.md).

Notez que vous devez avoir installé localement pour utiliser l’émulateur de calcul PHP. L’émulateur de calcul utilisera votre installation PHP locale pour exécuter votre application.

Pour exécuter votre projet dans les émulateurs, exécutez la commande suivante à partir du répertoire racine de votre projet :

    PS C:\MyProject> Start-AzureEmulator

Vous verrez un résultat similaire à celui-ci :

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Vous pouvez visualiser votre application en cours d’exécution dans l’émulateur en ouvrant un navigateur web et en accédant à l’adresse locale indiquée dans la sortie (`http://127.0.0.1:81` dans l’exemple ci-dessus).

Pour arrêter les émulateurs, exécutez la commande suivante :

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publication de votre application.

Pour publier votre application, vous devez tout d’abord importer vos paramètres de publication à l’aide de la cmdlet [Import-AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Vous pouvez ensuite publier votre application à l’aide de l’applet de commande [Publier-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Pour plus d’informations sur l’inscription, consultez [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

[Azure SDK pour PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[définition de service (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuration du service (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[le programme d’installation de SQLNCLI.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
