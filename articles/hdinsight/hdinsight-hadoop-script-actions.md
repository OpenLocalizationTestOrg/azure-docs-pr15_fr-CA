<properties
    pageTitle="Développement de l’Action avec HDInsight de script | Microsoft Azure"
    description="Apprenez à personnaliser les clusters Hadoop avec Action de Script. Action de script peut être utilisée pour installer d’autres logiciels s’exécutant sur un cluster Hadoop ou modifier la configuration des applications installées sur un cluster."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Développer des scripts d’Action de Script pour les clusters basés sur les HDInsight Windows

Apprenez à écrire des scripts d’Action de Script pour HDInsight. Pour plus d’informations sur l’utilisation de scripts d’Action de Script, voir [clusters de HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md). Pour le même article écrit pour les clusters basés sur Linux de HDInsight, consultez [scripts de développer une Action de Script pour HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] Les informations contenues dans ce document sont spécifiques aux clusters de HDInsight de basées sur Windows. Pour plus d’informations sur l’utilisation des actions de script avec les clusters basés sur Windows, consultez [développement d’action de Script avec HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Action de script peut être utilisée pour installer d’autres logiciels s’exécutant sur un cluster Hadoop ou modifier la configuration des applications installées sur un cluster. Actions de script sont des scripts qui s’exécutent sur les nœuds de cluster lors du déploiement de clusters de HDInsight, et qu’elles sont exécutées une fois les nœuds dans le cluster de terminer la configuration de HDInsight. Une action de script est exécutée avec les privilèges de compte d’administrateur système et fournit des droits d’accès complets aux nœuds du cluster. Chaque cluster peut être fourni avec une liste d’actions de script à être exécutées dans l’ordre dans lequel elles sont définies.

> [AZURE.NOTE] Si vous rencontrez le message d’erreur suivant :
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> Il est, car vous n’avez pas inclus les méthodes d’assistance.  Consultez les [méthodes d’assistance pour les scripts personnalisés](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Exemples de scripts

Pour la création de clusters de HDInsight sur le système d’exploitation Windows, l’Action du Script est le script PowerShell d’Azure. L’exemple suivant est un exemple de script pour configurer les fichiers de configuration de site :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Le script prend quatre paramètres, le nom de fichier de configuration, la propriété que vous souhaitez modifier, la valeur que vous souhaitez définir, et une description. Par exemple :

    hive-site.xml hive.metastore.client.socket.timeout 90

Ces paramètres définit la valeur de hive.metastore.client.socket.timeout à 90 dans le fichier de la ruche-site.xml.  La valeur par défaut est de 60 secondes.

Cet exemple de script peut également trouver sur [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight propose plusieurs scripts pour installer des composants supplémentaires sur les clusters de HDInsight :

Nom | Script
----- | -----
**Installation d’allumage** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-installer-v03.ps1. Reportez-vous à la section [installation et utilisation et sur les clusters de HDInsight][hdinsight-install-spark].
**Installation de R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-installer-v02.ps1. Reportez-vous à la section [installation et utilisation R sur les clusters de HDInsight][hdinsight-r-scripts].
**Installer le mode série sur LAN.r** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-installer-v01.ps1. Voir [installation et l’utilisation des clusters de mode série sur LAN.r sur HDInsight](hdinsight-hadoop-solr-install.md).
- **Installation de Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-installer-v01.ps1. Voir [installation et l’utilisation des clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

Action de script peut être déployée à partir du portail Azure, Azure PowerShell ou en utilisant le Kit de développement .NET HDInsight.  Pour plus d’informations, voir [clusters de HDInsight de personnaliser à l’aide de Script Action][hdinsight-cluster-customize].

> [AZURE.NOTE] Les exemples de scripts fonctionnent uniquement avec la version de cluster HDInsight 3.1 ou supérieur. Pour plus d’informations sur les versions de cluster HDInsight, consultez [versions de cluster HDInsight](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Méthodes d’assistance pour les scripts personnalisés

Méthodes d’application d’assistance de script Action sont des utilitaires que vous pouvez utiliser lors de l’écriture de scripts personnalisés. Ceux-ci sont définis dans [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)et peuvent être incluses dans vos scripts en utilisant les éléments suivants :

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Voici les méthodes d’assistance fournies par ce script :

Méthode d’assistance | Description
-------------- | -----------
**Enregistrer-HDIFile** | Télécharger un fichier à partir de la spécifié identificateur URI (Uniform Resource) vers un emplacement sur le disque local qui est associé au nœud d’Azure VM affecté au cluster.
**Développez-HDIZippedFile** | Décompressez un fichier compressé.
**Appel de code non-HDICmdScript** | Exécuter un script à partir de cmd.exe.
**Écriture-HDILog** | Écrire la sortie à partir du script personnalisé utilisé pour une action de script.
**Get-Services** | Obtenir la liste des services en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-Service** | Avec le nom de service spécifique en tant qu’entrée, obtenir des informations détaillées pour un service spécifique (nom du service, l’identificateur de processus, état, etc.) sur l’ordinateur où le script s’exécute.
**Get-HDIServices** | Obtenir la liste des services de HDInsight en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIService** | Avec le nom de service HDInsight spécifique en tant qu’entrée, obtenir des informations détaillées pour un service spécifique (nom du service, l’identificateur de processus, état, etc.) sur l’ordinateur où le script s’exécute.
**Serviceexécution de l’obtenir** | Obtenir la liste des services qui s’exécutent sur l’ordinateur où le script s’exécute.
**Get-ServiceRunning** | Vérifier si un service particulier (par nom) est en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIServicesRunning** | Obtenir la liste des services de HDInsight en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIServiceRunning** | Vérifier si un service de HDInsight spécifique (par nom) est en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIHadoopVersion** | Obtenez la version d’Hadoop installé sur l’ordinateur où le script s’exécute.
**Test-IsHDIHeadNode** | Vérifiez si l’ordinateur où le script s’exécute est un nœud de tête.
**Test-IsActiveHDIHeadNode** | Vérifiez si l’ordinateur où le script s’exécute est un nœud de tête actif.
**Test-IsHDIDataNode** | Vérifiez si l’ordinateur où le script s’exécute est un nœud de données.
**Modifier-HDIConfigFile** | Modifier la ruche de fichiers config-site.xml, core-site.xml, très-site.xml, mapred-site.xml ou les fils-site.xml.


## <a name="best-practices-for-script-development"></a>Méthodes conseillées pour le développement de scripts

Lorsque vous développez un script personnalisé pour un cluster de HDInsight, il existe plusieurs pratiques recommandées pour la garder à l’esprit :

- Pour obtenir la version Hadoop

    Seulement HDInsight version 3.1 (Hadoop 2.4) et supérieure prennent en charge à l’aide des actions de Script pour installer des composants personnalisés sur un cluster. Dans votre script personnalisé, vous devez utiliser la méthode d’assistance de **Get-HDIHadoopVersion** pour vérifier la version d’Hadoop avant de procéder à l’exécution d’autres tâches dans le script.


- Fournir des liens stables aux ressources de script

    Les utilisateurs doivent s’assurer que tous les scripts et les autres artefacts utilisées dans la personnalisation d’un cluster restent disponibles pendant la durée de vie du cluster et que les versions de ces fichiers ne changent pas pendant toute la durée. Ces ressources sont requises si la recréation de l’image de nœuds dans le cluster est requis. La meilleure solution consiste à télécharger et archiver tous les éléments dans un compte de stockage qui contrôle de l’utilisateur. Il peut s’agir du compte de stockage par défaut ou l’un des comptes de stockage supplémentaires spécifiés au moment du déploiement pour un cluster personnalisée.
    L’allumage et R personnalisée exemples de cluster fournies dans la documentation, par exemple, nous avons une copie locale des ressources dans ce compte de stockage : https://hdiconfigactions.blob.core.windows.net/.


- Assurez-vous que le script de personnalisation du cluster est idempotent

    Vous devez attendre que les nœuds d’un cluster d’HDInsight sera ré-image pendant la durée de vie du cluster. Le script de personnalisation de cluster est exécuté chaque fois qu’un cluster est à nouveau mis en image. Ce script doit être conçu pour être idempotents en ce sens que lors de la recréation de l’image, le script doit s’assurer que le cluster est retourné à la même personnaliser état dans lequel il était juste après que le script est exécuté pour la première fois lorsque le cluster a été initialement créé. Par exemple, si un script personnalisé installé une application de D:\AppLocation sur sa première exécution, puis lors de chaque exécution ultérieure, lors de la recréation de l’image, le script doit vérifier si l’application existe à l’emplacement D:\AppLocation avant de continuer avec d’autres étapes dans le script.


- Installer des composants personnalisés dans l’emplacement optimal

    Lorsque les nœuds de cluster sont ré-image, le lecteur de ressource C:\ et le lecteur du système D:\ peuvent être remise en forme, ce qui entraîne la perte de données et des applications qui avaient été installées sur ces lecteurs. Cela peut également se produire si un nœud d’Azure virtual machine (VM) qui fait partie du cluster tombe en panne et est remplacé par un nouveau nœud. Vous pouvez installer des composants sur le lecteur D:\ ou à l’emplacement de C:\apps sur le cluster. Tous les autres emplacements sur le lecteur C:\ sont réservés. Spécifiez l’emplacement où les applications ou bibliothèques doivent être installés dans le script de personnalisation du cluster.


- Garantir la haute disponibilité de l’architecture de cluster

    HDInsight a une architecture actif-passif pour une haute disponibilité, dans lequel un nœud principal est en mode actif (où les services HDInsight sont en cours d’exécution) et que le nœud de tête est en mode veille (dans le HDInsight services exécutent pas). Les nœuds de basculer entre les modes actifs et passifs si HDInsight services sont interrompus. Si une action de script est utilisée pour installer les services sur les deux nœuds de tête pour une disponibilité élevée, notez que le mécanisme de basculement HDInsight ne sera pas en mesure de basculer automatiquement sur ces services installés par l’utilisateur. Donc installés par l’utilisateur des services sur les nœuds de tête des HDInsight qui sont supposés être hautement disponible doivent ont leur propre mécanisme de basculement en mode actif-passif ou en mode actif-actif.

    Une commande d’Action de Script HDInsight s’exécute sur les deux nœuds en tête lorsque le rôle de nœud principal est spécifié comme une valeur dans le paramètre *ClusterRoleCollection* . Lorsque vous créez un script personnalisé, assurez-vous que votre script est conscient de ce programme d’installation. Vous ne devez pas exécuter dans des problèmes où les mêmes services sont installés et démarrés sur les deux nœuds siège et ils finissent en concurrence avec eux. En outre, n’oubliez pas que les données seront perdues lors de la recréation de l’image, afin que les logiciels installés par une Action de Script doit être résilient à ces événements. Les applications doivent être conçues pour fonctionner avec des données hautement disponibles sont réparties sur plusieurs nœuds. Notez que jusqu'à 1/5 des nœuds dans un cluster peut être remise en image en même temps.


- Configurer les composants personnalisés pour utiliser le stockage des objets Blob Azure

    Les composants personnalisés que vous installez sur les nœuds du cluster peuvent avoir une configuration par défaut d’utiliser le stockage de système de fichier distribué (très) Hadoop. Vous devez modifier la configuration pour utiliser à la place de stockage des objets Blob Azure. Sur un cluster image, le système de fichiers très obtient mis en forme et vous perdriez toutes les données qui y sont stockées. Stockage des objets Blob Azure plutôt garantit que vos données sont conservées.

## <a name="common-usage-patterns"></a>Modes d’utilisation courants

Cette section fournit des conseils sur l’implémentation des modes d’utilisation courants que vous pourriez rencontrer lors de l’écriture de votre propre script personnalisé.

### <a name="configure-environment-variables"></a>Configurer les variables d’environnement

Dans le développement d’actions de script, vous sembleront souvent le besoin de définir des variables d’environnement. Par exemple, un scénario le plus probable est lorsque vous téléchargez un fichier binaire à partir d’un site externe, installez sur le cluster et ajoutez l’emplacement du où il est installé dans votre variable d’environnement « PATH ». L’extrait de code suivant vous montre comment définir les variables d’environnement dans le script personnalisé.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Cette instruction définit la variable d’environnement **MDS_RUNNER_CUSTOM_CLUSTER** la valeur 'true' et définit également la portée de cette variable soit à l’échelle de l’ordinateur. Parfois, il est important que les variables d’environnement sont définies à la portée appropriée – ordinateur ou utilisateur. Consultez [ici] [ 1] pour plus d’informations sur la définition des variables d’environnement.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accès aux emplacements où sont stockés les scripts personnalisés

Les scripts permettent de personnaliser un cluster doivent être soit dans le compte de stockage par défaut pour le cluster ou dans un conteneur en lecture seule public sur un autre compte de stockage. Si votre script accède aux ressources situées ailleurs ces doivent être dans un accessibles publiquement (publics au moins en lecture seule). Vous pouvez par exemple souhaiter accéder à un fichier et enregistrez-le à l’aide de la commande HDI SaveFile.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Dans cet exemple, vous devez vous assurer que le conteneur 'somecontainer' dans le compte de stockage 'somestorageaccount' est accessible au public. Sinon, le script lèvent une exception « Introuvable » et échouent.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Passer des paramètres à l’applet de commande Add-AzureRmHDInsightScriptAction

Pour passer plusieurs paramètres à l’applet de commande Add-AzureRmHDInsightScriptAction, vous devez mettre en forme la valeur de chaîne pour contenir tous les paramètres du script. Par exemple :

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

ou

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Lève une exception pour le déploiement de cluster défaillant

Si vous souhaitez être averti avec précision du fait que le cluster de personnalisation n’a pas pu comme prévu, il est important de lever une exception et l’échec de la création du cluster. Par exemple, vous souhaiterez traiter un fichier s’il existe et gérer le cas d’erreur dans laquelle le fichier n’existe pas. Cela garantie que le script s’arrête correctement et l’état du cluster est correctement reconnu. L’extrait de code suivant donne un exemple de comment atteindre ce but :

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Dans cet extrait de code, si le fichier n’existe pas, il aboutirait à un état où le script effectivement s’arrête correctement après le message d’erreur d’impression, et que le cluster atteint l’état en cours d’exécution, en supposant qu’il « » terminé les processus de personnalisation de cluster. Si vous souhaitez être averti précisément du fait que le cluster essentiellement de personnalisation n’a pas pu comme prévu en raison d’un fichier manquant, il est plus approprié de lever une exception et faire échouer l’étape de personnalisation du cluster. Pour ce faire, vous devez plutôt utiliser l’extrait de code d’exemple suivant.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Liste de contrôle pour le déploiement d’une action de script
Voici les étapes que nous avons pris avant de déployer ces scripts :

1. Placez les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible par les nœuds de cluster au cours du déploiement. Il peut s’agir d’une des autres comptes de stockage spécifiés au moment du déploiement du cluster, ou tout autre conteneur de stockage accessible au public ou par défaut.
2. Ajouter des contrôles à des scripts pour vous assurer qu’ils exécutent idempotently, afin que le script peut être exécuté plusieurs fois sur le même nœud.
3. Utilisez l’applet de commande **Write-Output** Azure PowerShell pour imprimer à STDOUT comme STDERR. N’utilisez pas de **Write-Host**.
4. Utilisez un dossier de fichiers temporaires, par exemple $env : TEMP, pour conserver le fichier téléchargé est utilisé par les scripts et puis les nettoyer après les scripts ont été exécutés.
5. Installez un logiciel personnalisé uniquement au D:\ C:\apps. Autres emplacements sur le lecteur C: ne doivent pas être utilisées comme ils sont réservés. Notez que l’installation des fichiers sur le lecteur C: en dehors du dossier C:\apps peut entraîner des échecs d’installation au cours d’une nouvelle image du nœud.
6. Dans le cas où les paramètres au niveau du système d’exploitation ou les fichiers de configuration de service Hadoop ont été modifiés, vous souhaiterez redémarrez HDInsight services afin qu’ils peuvent intercepter tous les paramètres au niveau du système d’exploitation, tels que les variables d’environnement définies dans les scripts.

## <a name="debug-custom-scripts"></a>Débogage de scripts personnalisés

Les journaux d’erreurs de script sont stockées, ainsi que d’autre sortie, dans le compte de stockage par défaut que vous avez spécifié pour le cluster lors de sa création. Les journaux sont stockés dans une table avec le nom *u < \cluster-name-fragment >< \time-stamp > setuplog*. Il s’agit de journaux agrégées qui ont des enregistrements à partir de tous les nœuds (nœud de tête et les nœuds de travailleur) sur lequel le script s’exécute dans le cluster.
Un moyen facile de vérifier les journaux consiste à utiliser des outils de HDInsight pour Visual Studio. Pour installer les outils, consultez [mise en route à l’aide des outils Visual Studio Hadoop pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Pour vérifier le journal à l’aide de Visual Studio**

1. Ouvrez Visual Studio.
2. Cliquez sur **affichage**, puis cliquez sur **Explorateur de serveurs**.
3. Droit « Azure » et cliquez sur se connecter à **Microsoft Azure abonnements**, puis entrez vos informations d’identification.
4. Développez **stockage**, développez le compte de stockage Azure utilisé comme système de fichiers par défaut, développez le nœud **Tables**et puis double-cliquez sur le nom de la table.


Vous pouvez également à distance dans les nœuds de cluster pour voir les deux STDOUT et STDERR de scripts personnalisés. Les journaux sur chaque nœud sont spécifiques à ce nœud et sont enregistrés dans **C:\HDInsightLogs\DeploymentAgent.log**. Ces fichiers journaux enregistrent toutes les sorties à partir du script personnalisé. Un extrait du journal exemple pour une action de script étincelle ressemble à ceci :

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Dans ce journal, il est clair que l’action de script d’allumage a été exécutée sur l’ordinateur virtuel nommé HEADNODE0, et qu’aucune exception a été levée lors de l’exécution.

Dans le cas où une erreur d’exécution se produit, la sortie description sera également figurer dans ce fichier journal. Les informations contenues dans ces journaux doivent être utiles dans le débogage des problèmes de script qui peuvent survenir.


## <a name="see-also"></a>Voir aussi

- [Personnaliser des clusters HDInsight à l’aide des actions de Script][hdinsight-cluster-customize]
- [Installer et utiliser l’allumage sur des clusters de HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur les clusters de HDInsight][hdinsight-r-scripts]
- [L’installation et l’utilisation des clusters de mode série sur LAN.r sur HDInsight](hdinsight-hadoop-solr-install.md).
- [Installer et utiliser les clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
