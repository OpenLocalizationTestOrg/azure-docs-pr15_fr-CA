<properties
    pageTitle="Rôles web et worker de Python avec Visual Studio | Microsoft Azure"
    description="Vue d’ensemble de l’utilisation des outils de Python pour Visual Studio pour créer des services cloud Azure, y compris les rôles web et worker."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/03/2016"
    ms.author="adegeo"/>


# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Rôles web et worker de Python avec les outils de Python pour Visual Studio

Cet article fournit une vue d’ensemble de l’utilisation de rôles web et worker Python, à l’aide des [Outils de Python pour Visual Studio][]. Vous allez apprendre à utiliser Visual Studio pour créer et déployer un Service Cloud de base qui utilise les Python.

## <a name="prerequisites"></a>Conditions préalables

 - Visual Studio 2013 ou 2015
 - [Outils de Python pour Visual Studio][] (PTVS)
 - [Outils du SDK Azure pour VS 2013][] ou [Outils SDK Azure pour VS 2015][]
 - [Python 2.7 32 bits][] ou les [Python 3.5 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Quels sont les rôles web et worker Python ?

Azure propose trois modèles d’exécution d’applications de calcul : [fonctionnalité d’applications Web dans le Service d’application Azure][execution model-web sites], [ordinateurs virtuels Azure][execution model-vms]et [Les Services en nuage Azure][execution model-cloud services]. Les trois modèles prennent en charge les Python. Les Services en nuage, qui incluent les rôles web et worker, fournissent une *plate-forme en tant que Service (PaaS)*. Au sein d’un service en nuage, un rôle web fournit un serveur dédié de web Internet Information Services (IIS) pour héberger des applications web frontal, pendant un rôle worker peut exécuter des tâches d’asynchrones, longue ou définitive indépendantes de l’intervention de l’utilisateur ou l’entrée.

Pour plus d’informations, consultez [ce qu’est un Service en nuage ?].

> [AZURE.NOTE]*Vous souhaitez créer un site Web simple ?*
Si votre scénario implique qu’un simple site Web frontal, envisagez d’utiliser la fonctionnalité d’applications Web léger dans le Service d’application Azure. Vous pouvez facilement mettre à niveau à un Service en nuage que votre site se développe et l’évolution de vos besoins. Consultez le <a href="/develop/python/">Centre de développement de Python</a> pour articles couvrant le développement de la fonctionnalité d’applications Web dans le Service d’application Azure.
<br />


## <a name="project-creation"></a>Création d’un projet

Dans Visual Studio, vous pouvez sélectionner **Azure Cloud Service** dans la boîte de dialogue **Nouveau projet** , sous les **Python**.

![Boîte de dialogue Nouveau projet](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Dans l’Assistant Azure Cloud Service, vous pouvez créer de nouveaux rôles web et worker.

![Boîte de dialogue Azure Cloud Service](./media/cloud-services-python-ptvs/new-service-wizard.png)

Le modèle de rôle de travail est fourni avec le code réutilisable pour se connecter à un compte de stockage Azure ou le Bus des services Azure.

![Solution de Service cloud](./media/cloud-services-python-ptvs/worker.png)

Vous pouvez ajouter des rôles web ou travailleur à un service cloud existant à tout moment.  Vous pouvez choisir d’ajouter des projets existants dans votre solution, ou créer de nouveaux.

![Ajouter des commandes de rôle](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Votre service cloud peut contenir des rôles implémentés dans différentes langues.  Par exemple, vous avez un rôle web de Python implémenté à l’aide de Django, avec les Python ou C# de rôles worker.  Vous pouvez communiquer facilement entre vos rôles à l’aide de files d’attente du Bus des services ou des files d’attente de stockage.

## <a name="install-python-on-the-cloud-service"></a>Installer les Python sur le service en nuage

>[AZURE.WARNING] Les scripts de configuration qui sont installés avec Visual Studio (au moment de la que dernière mise à jour de cet article) ne fonctionnent pas. Cette section décrit une solution de contournement.

Le principal problème avec les scripts d’installation sont qu’ils n’installent pas les python. Commencez par définir deux [tâches de démarrage](cloud-services-startup-tasks.md) dans le fichier [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) . La première tâche (**PrepPython.ps1**) télécharge et installe le runtime Python. La seconde tâche (**PipInstaller.ps1**) s’exécute pip pour installer des dépendances que peut avoir.

Les scripts ci-dessous ont été écrits ciblant les Python 3.5. Si vous souhaitez utiliser la version 2.x de python, la valeur du fichier de variables **PYTHON2** **sur** les deux tâches de démarrage et la tâche d’exécution : `<Variable name="PYTHON2" value="<mark>on</mark>" />`.


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
    
  </Task>

</Startup>
```

Les variables **PYTHON2** et **PYPATH** doivent être ajoutées à la tâche de démarrage du travail. La variable **PYPATH** est uniquement utilisée si la variable **PYTHON2** est définie sur **on**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Exemple ServiceDefinition.csdef

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Ensuite, créez les fichiers **PrepPython.ps1** et **PipInstaller.ps1** dans la **. / bin** le dossier de votre rôle.

#### <a name="preppythonps1"></a>PrepPython.ps1

Ce script installe les python. Si la variable d’environnement **PYTHON2** est définie sur, **sur** puis les Python 2.7 seront installé, sinon les Python 3.5 sera installé.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }
        
        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1

Ce script appelle pip et installe toutes les dépendances dans le fichier **requirements.txt** . Si la variable d’environnement **PYTHON2** est définie sur, **sur** puis les Python 2.7 seront utilisée, sinon les Python 3.5 sera utilisé.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Modifier LaunchWorker.ps1

>[AZURE.NOTE] Dans le cas d’un projet de **rôle worker** , **LauncherWorker.ps1** fichier est requis pour exécuter le fichier de démarrage. Dans un projet de **rôle web** , le fichier de démarrage est plutôt défini dans les propriétés du projet.

Le **bin\LaunchWorker.ps1** a été créé pour faire beaucoup de travail de préparation, mais elle ne fonctionne pas vraiment. Avec le script suivant, remplacez le contenu de ce fichier.

Ce script appelle le fichier **worker.py** à partir de votre projet de python. Si la variable d’environnement **PYTHON2** est définie sur, **sur** puis les Python 2.7 seront utilisée, sinon les Python 3.5 sera utilisé.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>PS.cmd

Les modèles Visual Studio doivent avoir créé un fichier **ps.cmd** dans le **. / bin** dossier. Ce script de shell appelle les scripts de wrapper PowerShell ci-dessus et permet la journalisation basée sur le nom du wrapper de PowerShell appelé. Si ce fichier n’a pas été créé, voici ce qu’il doit être. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Exécuter localement

Si vous définissez votre projet de service cloud comme projet de démarrage et appuyez sur F5, le service en nuage s’exécute dans l’émulateur Azure local.

Bien que PTVS prend en charge le lancement dans l’émulateur, débogage (points d’arrêt, par exemple) ne fonctionnera pas.

Pour déboguer vos rôles web et worker, vous pouvez définir le projet de rôle en tant que projet de démarrage et que déboguer à la place.  Vous pouvez également définir plusieurs projets de démarrage.  Avec le bouton droit de la solution, puis sélectionnez **Définir les projets de démarrage**.

![Propriétés de projet de démarrage de solution](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publier vers Azure

Pour publier, cliquez sur le projet de service cloud dans la solution et sélectionnez **Publier**.

![Microsoft Azure publier de connexion](./media/cloud-services-python-ptvs/publish-sign-in.png)

Suivez l’Assistant. Si vous le souhaitez, activer le Bureau à distance. Bureau à distance est utile lorsque vous devez déboguer quelque chose.

Lorsque vous avez terminé la configuration des paramètres, cliquez sur **Publier**.

Certains cours s’affiche dans la fenêtre Sortie, puis vous verrez la fenêtre journal d’activité de Microsoft Azure.

![Fenêtre de journal d’activité Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Déploiement prendra quelques minutes, puis vos rôles web ou travailleur fonctionnent sur Azure !

### <a name="investigate-logs"></a>Examinez les journaux

Une fois que la machine virtuelle de service cloud démarre et installe les Python, vous pouvez consulter les journaux à la recherche de messages d’échec. Ces journaux se trouvent dans le **C:\Resources\Directory\\{rôle} \LogFiles** dossier. Lorsque le script tente de détecter si les Python est installé et **PipInstaller.err.txt** peuvent se plaindre une version obsolète du pip, **PrepPython.err.txt** aura au moins une erreur qu’elle contient à partir de.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des rôles web et worker dans Python Tools pour Visual Studio, consultez la documentation de PTVS :

- [Projets de Service cloud][]

Pour plus d’informations sur l’utilisation des services Azure dans vos rôles web et worker, par exemple en utilisant le stockage Azure ou le Bus des services, consultez les articles suivants.

- [Service d’objet BLOB][]
- [Service de table][]
- [Service de file d’attente][]
- [Files d’attente de Bus de service][]
- [Rubriques de Bus de service][]


<!--Link references-->

[Quel est un Service en nuage ?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Service d’objet BLOB]: ../storage/storage-python-how-to-use-blob-storage.md
[Service de file d’attente]: ../storage/storage-python-how-to-use-queue-storage.md
[Service de table]: ../storage/storage-python-how-to-use-table-storage.md
[Files d’attente de Bus de service]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Rubriques de Bus de service]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Outils de Python pour Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Projets de Service cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Outils du SDK Azure pour VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du SDK Azure pour VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bits]: https://www.python.org/downloads/
[Python 3.5 32 bits]: https://www.python.org/downloads/
