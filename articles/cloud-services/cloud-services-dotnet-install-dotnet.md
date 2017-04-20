<properties
   pageTitle="Installez .NET sur un rôle de Service nuage | Microsoft Azure"
   description="Cet article explique comment installer manuellement le .NET framework sur le Web des services de Cloud et rôles Worker"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Installez .NET sur un rôle de Service Cloud 

Cet article décrit comment installer le .NET framework sur le Cloud Service rôles Web et Worker. Vous pouvez utiliser ces étapes pour installer .NET 4.6.1 sur le 4 de famille de système d’exploitation invité Azure. Pour les dernières informations sur les versions de système d’exploitation invité voir [Azure mises à jour de système d’exploitation invité et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).

Le processus d’installation .NET sur vos rôles web et worker implique notamment le package de programme d’installation de .NET dans le cadre de votre projet de nuage et lancer le programme d’installation dans le cadre du rôle tâches de démarrage.  

## <a name="add-the-net-installer-to-your-project"></a>Ajouter le programme d’installation de .NET à votre projet
- Télécharger du web à installer pour le .NET framework que vous souhaitez installer
    - [.NET 4.6.1 web Installer](http://go.microsoft.com/fwlink/?LinkId=671729)
- Pour un rôle Web
  1. Dans l' **Explorateur de solutions**, sous dans des **rôles** dans le projet de service cloud cliquez droit sur le rôle et le sélectionnez **Ajouter > Nouveau dossier**. Créez un dossier nommé *bin*
  2. Cliquez avec le bouton droit sur le dossier **bin** et sélectionnez **Ajouter > élément existant**. Sélectionnez le programme d’installation de .NET et l’ajouter au dossier bin.
- Pour un rôle de collaborateur
  1. Cliquez avec le bouton droit sur le rôle et le sélectionnez **Ajouter > élément existant**. Sélectionnez le programme d’installation de .NET et l’ajouter au rôle. 

Fichiers ajoutés de cette façon dans le dossier de contenu du rôle est automatiquement ajoutée au package de service cloud et déployée sur le même emplacement sur l’ordinateur virtuel. Répétez cette procédure pour tous les rôles web et worker dans votre Service Cloud afin que tous les rôles ont une copie de l’installer.

> [AZURE.NOTE] Même si votre application cible .NET 4.6, vous devez installer .NET 4.6.1 de votre rôle de Service Cloud. Le système d’exploitation invité de Azure inclut les mises à jour [3098779](https://support.microsoft.com/kb/3098779) et [3097997](https://support.microsoft.com/kb/3097997). L’installation de .NET 4.6 sur ces mises à jour peut entraîner des problèmes lors de l’exécution de vos applications .NET, afin que vous devez installer directement de .NET 4.6.1 et non .NET 4.6. Pour plus d’informations, reportez-vous à la section [3118750 de la base de connaissances](https://support.microsoft.com/kb/3118750).

![Contenu du rôle avec les fichiers du programme d’installation][1]

## <a name="define-startup-tasks-for-your-roles"></a>Définir des tâches de démarrage pour vos rôles
Tâches de démarrage vous permettent des opérations avant le début d’un rôle. L’installation du.NET Framework dans le cadre de la tâche de démarrage veillera à ce que le framework est installé avant l’exécution de votre code d’application. Pour plus d’informations sur le démarrage des tâches, consultez : [Exécuter des tâches de démarrage dans Azure](cloud-services-startup-tasks.md). 

1. Ajoutez ce qui suit dans le fichier *ServiceDefinition.csdef* sous le nœud **WebRole** ou **WorkerRole** pour tous les rôles :
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    La configuration ci-dessus s’exécutera commande de console *install.cmd* avec des privilèges d’administrateur, il peut installer le .NET framework. Cette configuration crée également un LocalStorage avec le nom *NETFXInstall*. Le script de démarrage définit le dossier temporaire à utiliser cette ressource de stockage local afin que le programme d’installation de .NET framework sera téléchargé et installé à partir de cette ressource. Il est important de définir la taille de cette ressource d’au moins 1 024 Mo pour garantir que le framework s’installe correctement. Pour plus d’informations sur le démarrage de tâches, consultez : [tâches de démarrage de Service de Cloud commun](cloud-services-startup-tasks-common.md) 

2. Créer un fichier **install.cmd** et l’ajouter à tous les rôles par un clic droit sur le rôle, sélectionnez **Ajouter > élément existant...**. Pour tous les rôles doivent maintenant avoir le fichier de programme d’installation de .NET ainsi que le fichier install.cmd.
    
    ![Contenu du rôle avec tous les fichiers][2]

    > [AZURE.NOTE] Utiliser un simple éditeur de texte tel que le bloc-notes pour créer ce fichier. Si vous utilisez Visual Studio pour créer un fichier texte et renommez-le à « .cmd » le fichier peut encore contenir une marque d’ordre d’octets UTF-8 et de la première ligne du script en cours d’exécution génère une erreur. Si vous deviez utiliser Visual Studio pour créer le congé fichier ajouter un REM (Remarque) à la première ligne du fichier afin qu’il est ignoré lors de l’exécution. 

3. Ajoutez le script suivant dans le fichier **install.cmd** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    Le script d’installation vérifie si la version spécifiée du .NET framework est déjà installée sur l’ordinateur en interrogeant le Registre. Si la version .NET n’est pas installée, alors le .net Web Installer est lancé. Pour aider à résoudre les éventuels problèmes le script consigne toutes les activités dans un fichier nommé *startuptasklog-(currentdatetime) .txt* stockés dans le stockage local de *InstallLogs* .

    > [AZURE.NOTE] Le script affiche toujours l’installation .NET 4.5.2 ou 4.6 de .NET pour la continuité d’activité. Il est inutile d’installer .NET 4.5.2 manuellement, car il est déjà disponible sur le système d’exploitation invité de Azure. Au lieu d’installer .NET 4.6, vous devez directement installer .NET 4.6.1 en raison de le [3118750 de la base de connaissances](https://support.microsoft.com/kb/3118750).
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurer les diagnostics pour transférer les journaux des tâches de démarrage pour le stockage blob 
Pour simplifier le dépannage des problèmes d’installation, vous pouvez configurer les Diagnostics Azure pour transférer tous les fichiers journaux générés par le script de démarrage ou le programme d’installation de .NET pour le stockage des objets blob. Avec cette approche, vous pouvez afficher les journaux par simplement télécharger les fichiers journaux depuis le stockage blob au lieu d’avoir à Bureau à distance dans le rôle.

Pour configurer les diagnostics ouvrir le *diagnostics.wadcfgx* et ajoutez le code suivant sous le nœud de **répertoires** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Cela configurera les diagnostics de Windows azure pour transférer tous les fichiers dans le répertoire du *journal* sous la ressource de *NETFXInstall* vers le compte de stockage de diagnostics dans le conteneur d’objet blob *netfx-installation* .

## <a name="deploying-your-service"></a>Déploiement de votre service 
Lorsque vous déployez votre service les tâches de démarrage exécute et installe le .NET framework, s’il n’est pas déjà installé. Vos rôles sera dans l’état occupé pendant que le framework est installé et peut redémarrer même si l’installation de framework requiert. 

## <a name="additional-resources"></a>Ressources supplémentaires

- [L’installation du.NET Framework][]
- [Comment : déterminer les Versions de.NET Framework sont installées.][]
- [Dépannage des Installations du.NET Framework][]

[Comment : déterminer les Versions de.NET Framework sont installées.]: https://msdn.microsoft.com/library/hh925568.aspx
[L’installation du.NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Dépannage des Installations du.NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
