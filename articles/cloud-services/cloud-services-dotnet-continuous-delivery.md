<properties
    pageTitle="Services de livraison continue de nuage avec TFS dans Azure | Microsoft Azure"
    description="Découvrez comment paramétrer une livraison continue pour les applications Azure cloud. Exemples de code pour les instructions de ligne de commande MSBuild et scripts PowerShell."
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/30/2016"
    ms.author="tarcher"/>

# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Fourniture continue de Services en nuage dans Azure

Le processus décrit dans cet article vous montre comment configurer une administration en continu pour applications de cloud Azure. Ce processus vous permet de créer automatiquement des packages et déployer le package vers Azure après l’archivage de chaque code. Le processus de génération de package décrit dans cet article équivaut à la commande de **Package** dans Visual Studio, et la procédure de publication est équivalente à la commande **Publier** de Visual Studio.
L’article décrit les méthodes que vous utiliseriez pour créer un serveur de builds avec les instructions de ligne de commande MSBuild et les scripts Windows PowerShell, et il montre également comment vous pouvez éventuellement configurer Visual Studio Team Foundation Server - définitions de Build d’équipe d’utiliser les commandes de MSBuild et les scripts PowerShell. Le processus est personnalisable pour votre environnement de génération et les environnements cibles Azure.

Vous pouvez également utiliser Visual Studio Team Services, une version de TFS qui est hébergé dans Azure, faire plus facilement. Pour plus d’informations, consultez [Fourniture continue vers Azure par à l’aide de Visual Studio Team Services][].

Avant de commencer, vous devez publier votre application à partir de Visual Studio.
Cela permet de garantir que toutes les ressources sont disponibles et initialisé lorsque vous tentez d’automatiser le processus de publication.

## <a name="1-configure-the-build-server"></a>1 : configurer le serveur de builds

Avant de pouvoir créer un package d’Azure en utilisant MSBuild, vous devez installer les outils et les logiciels requis sur le serveur de build.

Visual Studio n’est pas nécessaire de l’installer sur le serveur de build. Si vous souhaitez utiliser le Service Team Foundation Build pour gérer votre serveur de builds, suivez les instructions de la documentation du [Service Team Foundation Build][] .

1.  Sur le serveur de builds, installez le [.NET Framework 4.5.2][], qui inclut MSBuild.
2.  Installez les derniers [Outils de création de Azure pour .NET](https://azure.microsoft.com/develop/net/).
3.  Installez les [bibliothèques Azure pour .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4.  Copiez le fichier Microsoft.WebApplication.targets à partir d’une installation de Visual Studio au serveur de build.

    Sur un ordinateur avec Visual Studio installé, ce fichier se trouve dans le répertoire C:\\programme Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Vous devez le copier dans le même répertoire sur le serveur de build.
5.  Installez les [Outils Azure pour Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2 : création d’un Package à l’aide des commandes de MSBuild

Cette section décrit comment construire une commande MSBuild qui crée un package Azure. Exécutez cette étape sur le serveur de builds pour vérifier que tout est configuré correctement et que la commande MSBuild est ce que vous souhaitez faire. Vous pouvez ajouter cette ligne de commande à des scripts de build existants sur le serveur de build, ou vous pouvez utiliser la ligne de commande dans une définition de Build TFS, comme décrit dans la section suivante. Pour plus d’informations sur les paramètres de ligne de commande MSBuild, consultez [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1.  Si Visual Studio est installé sur le serveur de builds, recherchez et cliquez sur **Invite de commande Visual Studio** dans le dossier de **Visual Studio Tools** dans Windows.

    Si Visual Studio n’est pas installé sur le serveur de build, ouvrez une invite de commande et assurez-vous que MSBuild.exe est accessible par le chemin d’accès. MSBuild est installé avec le.NET Framework dans le chemin d’accès %Windir%\\Microsoft.NET\\Framework\\*Version*. Par exemple, pour ajouter MSBuild.exe à la variable d’environnement PATH, lorsque le.NET Framework 4 est installé, tapez la commande suivante à l’invite de commande :

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  À l’invite de commandes, accédez au dossier contenant le fichier de projet Azure que vous souhaitez générer.

3.  Exécutez MSBuild avec l’option /target : publier option comme dans l’exemple suivant :

        MSBuild /target:Publish

    Cette option peut être abrégée sous /t : publier. L’option /t:Publish dans MSBuild ne doit pas être confondue avec les commandes de publication disponibles dans Visual Studio lorsque vous avez installé le kit SDK Azure. Le /t : ne publier que les builds option les packages Azure. Il ne déploie pas les packages comme les commandes de publication dans Visual Studio.

    Si vous le souhaitez, vous pouvez spécifier le nom du projet sous la forme d’un paramètre de MSBuild. Si non spécifié, le répertoire en cours est utilisé. Pour plus d’informations sur les options de ligne de commande MSBuild, consultez [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

4.  Recherchez la sortie. Par défaut, cette commande crée un répertoire pour le dossier racine du projet, par exemple *ProjectDir*\\emplacement\\*Configuration*\\app.publish\\. Lorsque vous générez un projet Azure, vous générez deux fichiers, le fichier du package et le fichier de configuration qui l’accompagne :

    -   Project.cspkg
    -   ServiceConfiguration. *TargetProfile*.cscfg

    Par défaut, chaque projet Azure comprend un fichier de configuration de service (fichier .cscfg) pour les générations (débogage) locales et l’autre pour les générations de nuage (intermédiaire ou production), mais vous pouvez ajouter ou supprimer des fichiers de configuration de service selon vos besoins. Lorsque vous créez un package dans Visual Studio, vous devez le fichier de configuration de service à inclure avec le package.

5.  Spécifiez le fichier de configuration de service. Lorsque vous créez un package à l’aide de MSBuild, le fichier de configuration de service local est inclus par défaut. Pour inclure un fichier de configuration de service différent, définissez la propriété TargetProfile de la commande MSBuild, comme dans l’exemple suivant :

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Spécifier l’emplacement de la sortie. Définir le chemin d’accès à l’aide de la /p:PublishDir =*répertoire* \\ option, y compris le séparateur de barre oblique inverse à droite, comme dans l’exemple suivant :

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Une fois que vous avez construit et testé une ligne de commande MSBuild appropriés pour générer vos projets et de les combiner dans un package d’Azure, vous pouvez ajouter cette ligne de commande à vos scripts de compilation. Si votre serveur de builds utilise des scripts personnalisés, ce processus varient selon les spécificités de votre processus de génération personnalisé. Si vous utilisez TFS comme un environnement de génération, vous pouvez suivre les instructions de l’étape suivante pour ajouter la package Azure build à votre processus de génération.

## <a name="3-build-a-package-using-tfs-team-build"></a>3 : création d’un Package à l’aide de TFS Team Build

Si vous avez configuré comme un contrôleur de build et le serveur de build Team Foundation Server (TFS) défini comme un ordinateur de build TFS, puis vous pouvez éventuellement définir une génération automatisée pour votre package Azure. Pour plus d’informations sur comment configurer et utiliser le serveur Team Foundation sous la forme d’un système de génération, reportez-vous à la section [mise à l’échelle votre système de génération][]. En particulier, la procédure suivante suppose que vous avez configuré votre serveur de builds comme décrit dans [déployer et configurer un serveur de création de][], et que vous avez créé un projet d’équipe, créé un projet de service cloud dans le projet d’équipe.

Pour configurer TFS pour créer des packages d’Azure, effectuez les opérations suivantes :

1.  Dans Visual Studio sur votre ordinateur de développement, dans le menu Affichage, choisissez **Team Explorer**ou Ctrl +\\, Ctrl + M. Dans la fenêtre Team Explorer, développez le nœud **Builds** ou choisissez la page **génère** et choisissez **Nouvelle définition de Build**.

    ![Nouvelle option de définition de Build][0]

2.  Cliquez sur l’onglet **déclencheur** et spécifiez les conditions souhaitées pour lorsque vous souhaitez que le package à générer. Par exemple, spécifier l' **Intégration continue** pour créer le package chaque fois qu’une source contrôle vérification se produit.

3.  Cliquez sur l’onglet **Paramètres de la Source** et vérifiez que votre dossier de projet est répertorié dans la colonne **Dossier du contrôle de code Source** , le statut est **actif**.

4.  Cliquez sur l’onglet **Générer valeurs par défaut** et sous contrôleur de Build, vérifiez le nom du serveur de build.  En outre, choisissez l’option **Copier sortie de build dans le dossier de destination suivant** et spécifier l’emplacement de déplacement souhaitée.

5.  Cliquez sur l’onglet **processus** . Sous l’onglet processus, cliquez sur le modèle par défaut, sous **créer**, cliquez sur le projet s’il n’est pas déjà sélectionné, développez la section **Avancé** , dans la section **génération** de la grille.

6.  Choisissez **Arguments MSBuild**et définissez les arguments de ligne de commande MSBuild appropriés, comme décrit à l’étape 2 ci-dessus. Par exemple, entrez **/t : publier /p:PublishDir =\\\\myserver\\supprime\\ ** pour créer un package et copier les fichiers du package à l’emplacement \\ \\myserver\\supprime\\:

    ![Arguments MSBuild][2]

    **Remarque :** Copie des fichiers vers un partage public rend plus facile à déployer manuellement les packages à partir de votre ordinateur de développement.

5.  Tester la réussite de votre étape de génération en vérifiant une modification à votre projet, ou la file d’attente une nouvelle build. Pour la file d’attente une nouvelle build, dans le Team Explorer, cliquez sur **Toutes les définitions de Build** et puis cliquez sur **File d’attente une nouvelle Build**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4 : publier un Package à l’aide d’un PowerShell Script

Cette section explique comment construire un script Windows PowerShell de publication la sortie de package d’application Cloud sur Azure à l’aide de paramètres facultatifs. Ce script peut être appelé après l’étape de la génération de votre automation de génération personnalisée. Il peut également être appelée à partir d’activités de flux de travail de modèle de processus dans Visual Studio TFS Team Build.

1.  Installer les [applets de commande PowerShell de Azure][] (v0.6.1 ou version ultérieure).
    Au cours de la phase d’installation de l’applet de commande, choisir d’installer un composant logiciel enfichable. Notez que cette version officiellement pris en charge remplace l’ancienne version proposée par le biais de CodePlex, bien que les versions précédentes étaient numérotées 2.x.x.

2.  Démarrer PowerShell Azure à l’aide du menu Démarrer ou la page de démarrage. Si vous démarrez de cette manière, les applets de commande PowerShell de Azure sera chargé.

3.  À l’invite de PowerShell, vérifiez que les applets de commande PowerShell sont chargés en entrant la commande partielle `Get-Azure` puis en appuyant sur la touche de tabulation pour la saisie semi-automatique des instructions.

    Si vous appuyez sur la touche Tab à plusieurs reprises, vous devriez voir les différentes commandes PowerShell d’Azure.

4.  Vérifiez que vous pouvez vous connecter à votre abonnement Azure en important des informations sur votre abonnement à partir du fichier .publishsettings.

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    Puis entrez la commande

    `Get-AzureSubscription`

    Il affiche des informations sur votre abonnement. Vérifiez que tout est correct.

4.  Enregistrer le modèle de script fourni à la fin de cet article dans votre dossier de scripts sous la forme c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Passez en revue la section Paramètres du script. Ajouter ou modifier des valeurs par défaut. Ces valeurs peuvent toujours être substituées par le passage de paramètres explicites.

6.  Assurez-vous que les comptes de service et de stockage cloud valide créés dans votre abonnement qui peut être ciblé par le script de publication. Le compte de stockage (stockage blob) servira pour transférer et stocker temporairement le fichier de package et de la configuration de déploiement, tandis que le déploiement est en cours de création.

    -   Pour créer un nouveau service en nuage, vous pouvez appeler ce script ou utiliser le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). Le nom de service cloud servira un préfixe dans un nom de domaine pleinement qualifié et, par conséquent, il doit être unique.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Pour créer un nouveau compte de stockage, vous pouvez appeler ce script, ou utiliser le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). Le nom de compte de stockage sert un préfixe dans un nom de domaine pleinement qualifié et, par conséquent, il doit être unique. Vous pouvez essayer à l’aide du même nom que le service de cloud.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Appeler le script directement à partir de PowerShell d’Azure, ou associer ce script à votre automation de génération hôte après la génération du package.

    >[AZURE.IMPORTANT] Le script sera toujours supprimer ou remplacer vos déploiements existants par défaut si ces derniers sont détectés. Ce n’est nécessaire pour permettre la remise continue d’automation où aucune invite utilisateur possible.

    **Exemple de scénario 1 :** déploiement continue dans l’environnement intermédiaire d’un service :

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Il est généralement suivi tests de vérification et un échange de VIP. Le remplacement de l’adresse IP virtuelle peut être effectué via le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885) ou à l’aide de l’applet de commande Move-déploiement.

    **Exemple de scénario 2 :** déploiement continue de l’environnement de production d’un service de test dédié

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Bureau à distance :**

    Si le Bureau à distance est activé dans votre projet Azure, vous devez effectuer des étapes supplémentaires uniques afin de garantir le bon certificat de Service Cloud est téléchargé pour tous les services en nuage ciblés par ce script.

    Recherchez les valeurs d’empreinte de certificat attendus par vos rôles. Les valeurs d’empreinte sont visibles dans la section certificats le nuage du fichier de configuration (par exemple, ServiceConfiguration.Cloud.cscfg). Il est également visible dans la boîte de dialogue Configuration du Bureau à distance dans Visual Studio lorsque vous affichez les Options et afficher le certificat sélectionné.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Télécharger les certificats de bureau à distance sous la forme d’une étape de configuration uniques à l’aide de l’applet de commande script suivant :

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Par exemple :

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Vous pouvez également exporter le fichier PFX du certificat avec la clé privée et télécharger les certificats pour chaque service de cloud de cible à l’aide d' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). 
    Lisez l’article suivant pour en savoir plus : [] de [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx].

    **Mise à niveau du déploiement de déploiement par rapport à SUPPR -\> nouveau déploiement**

    Le script par défaut effectue un déploiement de mise à niveau ($enableDeploymentUpgrade = 1) lorsque aucun paramètre n’est passé, ou la valeur 1 est explicitement passée. Pour les instances uniques, cela a l’avantage de prendre moins de temps qu’un déploiement complet. Pour les instances qui nécessitent une disponibilité élevée que cela a également l’avantage de laisser des instances en cours d’exécution tandis que d’autres sont mis à niveau (parcours de votre domaine de mise à jour), ainsi que votre adresse IP virtuelle ne sera pas supprimé.

    Déploiement de mise à niveau peut être désactivée dans le script ($enableDeploymentUpgrade = 0) ou en passant *enableDeploymentUpgrade - 0* comme paramètre, qui modifie le comportement de script tout d’abord supprimer tous les déploiements existants et de créer un nouveau déploiement.

    >[AZURE.IMPORTANT] Le script sera toujours supprimer ou remplacer vos déploiements existants par défaut si ces derniers sont détectés. C’est nécessaire pour permettre la remise continue d’automation où sans afficher d’invite utilisateur/opérateur possible.

## <a name="5-publish-a-package-using-tfs-team-build"></a>5 : publier un Package à l’aide de TFS Team Build

Cette étape facultative connecte TFS Team Build pour le script créé à l’étape 4, qui gère la publication de la version de package pour Azure. Cela implique la modification du modèle de processus utilisé par votre définition de build pour qu’il exécute une activité de publication à la fin du flux de travail. L’activité de publication exécute votre commande PowerShell en passant les paramètres de la génération. Sortie de MSBuild cible et publier le script va être transmis dans la sortie de génération standard.

1.  Modifier la définition de Build responsable continu déployer.

2.  Sélectionnez l’onglet **processus** .

3.  Suivez [ces instructions](http://msdn.microsoft.com/library/dd647551.aspx) pour ajouter un projet d’activité pour le modèle de processus de génération, téléchargez le modèle par défaut, ajoutez-le au projet et l’archiver. Renommez le modèle de processus de génération, tels que AzureBuildProcessTemplate.

3.  Revenir à l’onglet **processus** et **Afficher les détails** permet d’afficher une liste des modèles de processus de génération disponibles. Cliquez sur le bouton **nouveau...** et localisez le projet que vous venez d’ajouter et archivé. Recherchez le modèle que vous venez de créer et cliquez sur **OK**.

4.  Ouvrez le modèle de processus sélectionné pour modification. Vous pouvez ouvrir directement dans le Concepteur de flux de travail ou dans l’éditeur XML pour travailler avec le code XAML.

5.  Ajouter la liste suivante de nouveaux arguments en tant que lignes séparées dans l’onglet arguments du Concepteur de flux de travail. Tous les arguments doivent avoir la direction = dans et tapez = chaîne. Ces servira aux paramètres de flux à partir de la définition de build dans le flux de travail, qui ensuite obtenir utilisé pour appeler le script de publication.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![Liste d’arguments][3]

    Le code XAML correspondant se présente comme suit :

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Ajouter une nouvelle séquence à la fin de l’exécuter sur l’Agent :

    1.  Commencez par ajouter une activité de l’instruction If pour rechercher un fichier de script valide. Définissez la condition à cette valeur :

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Dans l’ensuite le cas de l’instruction If, ajouter une nouvelle activité de séquence. Définir le nom complet « Publier début »

    3.  Avec le début publier la séquence étant toujours sélectionné, ajouter la liste suivante de nouvelles variables en tant que lignes séparées dans l’onglet variables du Concepteur de flux de travail. Toutes les variables doivent avoir le type de Variable = chaîne et portée = début publier. Ces servira aux paramètres de flux à partir de la définition de build dans le flux de travail, qui ensuite obtenir utilisé pour appeler le script de publication.

        -   SubscriptionDataFilePath, de type chaîne

        -   PublishScriptFilePath, de type chaîne

            ![Nouvelles variables][4]

    4.  Si vous utilisez TFS 2012 ou versions antérieures, ajoutez une activité ConvertWorkspaceItem au début de la nouvelle séquence. Si vous utilisez TFS 2013 ou version ultérieure, ajouter une activité de GetLocalPath au début de la nouvelle séquence. Pour un ConvertWorkspaceItem, définissez les propriétés comme suit : Direction = ServerToLocal, DisplayName = « Convertir publier de nom de fichier de script », entrée = 'PublishScriptLocation', résultat = 'PublishScriptFilePath', espace de travail = 'Espace de travail'. Pour une activité GetLocalPath, définissez la propriété IncomingPath à « PublishScriptLocation » et le résultat à 'PublishScriptFilePath'. Cette activité convertit le chemin d’accès au script de publication à partir des emplacements de serveur TFS (si applicable) à un chemin d’accès du disque local standard.

    5.  Si vous utilisez TFS 2012 ou antérieure, ajoutez une autre activité ConvertWorkspaceItem à la fin de la nouvelle séquence. Direction = ServerToLocal, DisplayName = 'Convertir le nom du fichier d’abonnement', entrée = 'SubscriptionDataFileLocation', résultat = 'SubscriptionDataFilePath', espace de travail = 'Espace de travail'. Si vous utilisez TFS 2013 ou version ultérieure, ajoutez un autre GetLocalPath. IncomingPath = 'SubscriptionDataFileLocation' et le résultat = 'SubscriptionDataFilePath'.

    6.  Ajouter une activité InvokeProcess à la fin de la nouvelle séquence.
        Cette activité appelle PowerShell.exe avec des arguments passés par la définition de Build.

        1.  Arguments = String.Format ("-fichier""{0}" "- serviceName {1} - storageAccountName {2} - packageLocation""{3}" "- cloudConfigLocation""{4}" "- subscriptionDataFile""{5}" "selectedSubscription - {6}-environnement""{7}" "", PublishScriptFilePath, ServiceName%, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, environnement)

        2.  DisplayName = Execute publier le script

        3.  FileName = « PowerShell » (en incluant les guillemets)

        4.  OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Dans la zone de section **Handle Standard Output** de la InvokeProcess, définissez la valeur de la zone de texte à « données ». Il s’agit d’une variable pour stocker les données de sortie standard.

    8.  Ajouter une activité WriteBuildMessage juste en dessous de la section **Gérer la sortie Standard** . Définissez l’Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' et le Message = « données ». Cela garantit que la sortie standard du script sera écrite à la sortie de génération.

    9.  Dans la zone de section **Handle Error Output** de la InvokeProcess, définissez la valeur de la zone de texte à « données ». Il s’agit d’une variable pour stocker les données d’erreur standard.

    10. Ajouter une activité WriteBuildError juste en dessous de la section de la **Sortie d’erreur de gérer** . Définir le Message = « données ». Ainsi, que les erreurs standard du script sera écrite à la sortie d’erreur de génération.

    11. Corrigez les erreurs, indiqués par des points d’exclamation bleus. Survolez les points d’exclamation pour obtenir des information sur l’erreur. Enregistrer le flux de travail pour effacer des erreurs.

    Le résultat final des activités de workflow de publication ressemble à ceci dans le concepteur :

    ![Activités de flux de travail][5]

    Le résultat final des activités de workflow de publication ressemble à ceci en XAML :

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>


7.  Enregistrer le workflow de modèle de processus de génération et vérifier dans ce fichier.

8.  Modifier la définition de build (fermer s’il est déjà ouvert), puis cliquez sur **Nouveau** si vous ne voyez pas encore le nouveau modèle dans la liste des modèles de processus.

9.  Définissez le paramètre des valeurs de propriété dans la section divers comme suit :

    1.  CloudConfigLocation ='c:\\supprime\\app.publish\\ServiceConfiguration.Cloud.cscfg' *cette valeur est dérivée à partir de : ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = ' c:\\supprime\\app.publish\\ContactManager.Azure.cspkg' *cette valeur est dérivée à partir de : ($PublishDir)($ProjectName) .cspkg*

    3.  PublishScriptLocation = ' c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename', *Utilisez le nom de service cloud appropriés ici*

    5.  Environnement = 'Test'

    6.  StorageAccountName = 'mystorageaccountname', *Utilisez le nom de compte de stockage approprié ici*

    7.  SubscriptionDataFileLocation = ' c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![Valeurs de propriété de paramètre][6]

10. Enregistrer les modifications apportées à la définition de Build.

11. File d’attente une Build à exécuter à la fois la version de package et la publication. Si vous avez un déclencheur défini pour l’intégration continue, vous exécuterez ce comportement à chaque archivage.

### <a name="publishcloudserviceps1-script-template"></a>Modèle de script de PublishCloudService.ps1

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Étapes suivantes

Pour activer le débogage à distance lorsque vous utilisez la livraison continue, voir [Activer le débogage lors de l’utilisation de livraison continue de publier sur Azure à distance](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

  [Livraison continue vers Azure à l’aide de Visual Studio Team Services]: cloud-services-continuous-delivery-use-vso.md  
  [Service Team Foundation Build]: https://msdn.microsoft.com/library/ee259687.aspx
  [.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
  [.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
  [.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
  [Faire évoluer votre système de génération]: https://msdn.microsoft.com/library/dd793166.aspx
  [Déployer et configurer un serveur de builds]: https://msdn.microsoft.com/library/ms181712.aspx
  [Applets de commande PowerShell Azure]: powershell-install-configure.md
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
