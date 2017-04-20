<properties
    pageTitle="Répliquer les machines virtuelles VMware sur Azure à l’aide de la récupération de Site avec Azure Automation DSC | Microsoft Azure"
    description="Décrit comment utiliser des Azure Automation DSC pour déployer automatiquement le service de la mobilité de récupération de Site Azure et Azure agent pour les ordinateurs virtuels/physiques vers Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Répliquer les machines virtuelles VMware sur Azure à l’aide de la récupération de Site avec Azure Automation DSC

Dans la Suite de gestion des opérations, nous vous fournir une sauvegarde complète et une solution de reprise après sinistre que vous pouvez utiliser dans le cadre de votre plan de continuité d’activité d’entreprise.

Nous avons commencé ce voyage avec Hyper-V à l’aide de Hyper-V Replica. Mais nous avons étendu pour prendre en charge une installation hétérogène, car les clients ont plusieurs hyperviseurs et les plates-formes dans les nuages.

Si vous exécutez VMware de charges de travail et/ou serveurs physiques aujourd'hui, un serveur de gestion de tous les composants de la récupération de Site Azure s’exécute dans votre environnement pour gérer la réplication des données et de communication avec Azure, lorsque Azure est votre destination.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Déployer le service de la mobilité de récupération de Site à l’aide de Automation DSC
Nous allons commencer en effectuant une analyse rapide de ce que fait ce serveur d’administration.

Le serveur d’administration exécute plusieurs rôles de serveur. Un de ces rôles est de *configuration*, qui coordonne les communications et gère les processus de réplication et de récupération de données.

En outre, le rôle de *processus* fait Office de passerelle de réplication. Ce rôle reçoit des données de réplication à partir des machines d’origine protégée, il optimise avec la mise en cache, de compression et de cryptage et puis l’envoie à un compte de stockage Azure. L’une des fonctions pour le rôle de processus est également à distribuer l’installation du service de la mobilité pour les ordinateurs protégés et exécuter une découverte automatique des ordinateurs virtuels VMware.

S’il existe un retour en arrière à partir d’Azure, le rôle *cible maître* gère les données de réplication dans le cadre de cette opération.

Pour les ordinateurs protégés, nous s’appuient sur le *service de la mobilité*. Ce composant est déployé sur chaque ordinateur (VMware VM ou serveur physique) que vous souhaitez répliquer sur Azure. Il capture les écritures de données sur la machine et les transmet au serveur d’administration (rôle de processus).

Lorsque vous avez affaire à la continuité d’activité, il est important de comprendre vos charges de travail, votre infrastructure et les composants impliqués. Vous pouvez ensuite répondre à la configuration requise pour votre objectif de temps de récupération (RTO) et l’objectif de point de récupération (RPO). Dans ce contexte, le service de la mobilité est essentielle pour garantir que vos charges de travail sont protégés comme prévu.

Comment pouvez, d’une manière optimisée, assurez-vous d’avoir une configuration fiable protégée à l’aide de certains composants de la Suite de gestion des opérations ?

Cet article fournit un exemple de comment vous pouvez utiliser Azure Automation souhaité état Configuration (DSC), ainsi que de la récupération de Site, pour vous assurer que :

- Le service de la mobilité et l’agent d’Azure VM sont déployés sur les ordinateurs Windows que vous souhaitez protéger.
- Le service de la mobilité et l’agent d’Azure VM sont toujours en cours d’exécution lorsque Azure est la cible de réplication.

## <a name="prerequisites"></a>Conditions préalables

- Un référentiel pour stocker la configuration requise
- Un référentiel pour stocker le mot de passe requis pour enregistrer avec le serveur d’administration

 > [AZURE.NOTE] Un mot de passe unique est généré pour chaque serveur d’administration. Si vous envisagez de déployer plusieurs serveurs d’administration, vous devez vous assurer que le mot de passe correct est stocké dans le fichier passphrase.txt.

- Windows Management Framework (WMF) 5.0 est installé sur les ordinateurs que vous souhaitez activer la protection (il s’agit d’une exigence d’automatisation DSC)

 > [AZURE.NOTE] Si vous voulez utiliser des ordinateurs DSC pour Windows qui disposent de WMF 4.0, reportez-vous à la section [Utilisation de DSC dans les environnements déconnectés](#Use DSC in disconnected environments).

Le service de la mobilité peut être installé par le biais de la ligne de commande et accepte plusieurs arguments. C’est pourquoi vous devez avoir les fichiers binaires (après les extraire à partir de votre programme d’installation) et de les stocker dans un endroit où vous pouvez les récupérer à l’aide d’une configuration de DSC.

## <a name="step-1-extract-binaries"></a>Étape 1 : Fichiers binaires extrait

1. Pour extraire les fichiers dont vous avez besoin de ce programme d’installation, recherchez le répertoire suivant sur votre serveur de gestion :

    **\Microsoft Site azure Recovery\home\svsystems\pushinstallsvc\repository**

    Dans ce dossier, vous devriez voir un fichier MSI nommé :

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Utilisez la commande suivante pour extraire le programme d’installation :

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Sélectionnez tous les fichiers et les envoyer vers un dossier de compressé (zippé).

Vous avez maintenant les fichiers binaires dont vous avez besoin pour automatiser la configuration du service de la mobilité à l’aide de Automation DSC.

### <a name="passphrase"></a>Mot de passe

Ensuite, vous devez déterminer où vous souhaitez placer ce dossier compressé. Vous pouvez utiliser un compte de stockage Azure, comme indiqué ultérieurement, pour stocker le mot de passe dont vous avez besoin pour le programme d’installation. L’agent sera ensuite enregistré avec le serveur d’administration dans le cadre du processus.

Le mot de passe que vous avez reçu lorsque vous avez déployé le serveur d’administration peut être enregistré dans un fichier texte en tant que passphrase.txt.

Placer le dossier compressé et le mot de passe dans un conteneur dédié dans le compte de stockage Azure.

![Emplacement du dossier](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si vous souhaitez conserver ces fichiers sur un partage sur votre réseau, vous pouvez le faire. Vous devez simplement vous assurer que la ressource DSC que vous utiliserez ultérieurement dispose d’un accès et qu’il peut obtenir la configuration et le mot de passe.

## <a name="step-2-create-the-dsc-configuration"></a>Étape 2 : Créer la configuration DSC

Le programme d’installation dépend de WMF 5.0. Pour l’ordinateur appliquer la configuration par l’intermédiaire de Automation DSC, WMF 5.0 doit être présent.

L’environnement utilise l’exemple de configuration DSC suivante :

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configuration inclut les tâches suivantes :

- Les variables indiquera la configuration où obtenir les fichiers binaires pour les services de mobilité et de l’agent Azure VM, où obtenir le mot de passe et l’endroit où stocker la sortie.
- La configuration va importer la ressource xPSDesiredStateConfiguration DSC, afin que vous puissiez utiliser `xRemoteFile` pour télécharger les fichiers à partir du référentiel.
- La configuration crée un répertoire dans lequel vous souhaitez stocker les fichiers binaires.
- La ressource archive extrait les fichiers à partir du dossier compressé.
- La ressource d’installation installera le service de la mobilité de la UNIFIEDAGENT. Programme d’installation EXE avec les arguments spécifiques. (Les variables qui construisent les arguments doivent être modifiées pour refléter votre environnement.)
- Le package AzureAgent ressource va installer l’agent d’Azure VM, ce qui est recommandé sur chaque machine virtuelle qui s’exécute dans Azure. L’agent Azure VM rend également possible d’ajouter des extensions à la machine virtuelle après le basculement.
- L’ou les ressources service veillera à ce que les services connexes de la mobilité et les services Azure sont exécutent en permanence.

Enregistrer la configuration en tant que **ASRMobilityService**.

>[AZURE.NOTE] N’oubliez pas de remplacer le CSIP dans votre configuration afin de refléter le serveur d’administration réelle afin que l’agent est connecté correctement et qu’il utilise le mot de passe correct.

## <a name="step-3-upload-to-automation-dsc"></a>Étape 3 : Téléchargement à l’Automation de DSC

Dans la mesure où la configuration de DSC que vous avez apportées va importer un module de ressources DSC requis (xPSDesiredStateConfiguration), vous devez importer ce module dans Automation avant de télécharger la configuration DSC.

Connectez-vous à votre compte de Automation, recherchez **actifs** > **Modules**, cliquez sur **Parcourir la galerie**.

Ici, vous pouvez rechercher pour le module et l’importer dans votre compte.

![Module d’importation](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Lorsque vous avez terminé ceci, accédez à votre ordinateur où vous avez les modules Azure Resource Manager installés et passez à importer la configuration DSC nouvellement créée.

### <a name="import-cmdlets"></a>Importer des applets de commande

Dans PowerShell, connectez-vous à votre abonnement Azure. Modifier les applets de commande pour refléter votre environnement et de capturer les informations de votre compte d’Automation dans une variable :

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Télécharger la configuration à DSC d’Automation à l’aide de l’applet de commande suivante :

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compiler la configuration dans Automation DSC

Ensuite, vous devez compiler la configuration dans Automation DSC, afin que vous pouvez commencer à enregistrer les nœuds pour elle. Vous effectuer cette opération en exécutant l’applet de commande suivante :

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Cela peut prendre quelques minutes, étant donné que vous déployiez fondamentalement la configuration pour le service d’extraction de DSC hébergé.

Après avoir compilé la configuration, vous pouvez récupérer des informations sur les tâche à l’aide de PowerShell (Get-AzureRmAutomationDscCompilationJob) ou à l’aide du [portail Azure](https://portal.azure.com/).

![Récupérer le travail](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Vous avez maintenant correctement publié et téléchargé votre configuration DSC à DSC d’Automation.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Étape 4 : Les ordinateurs intégrés à DSC d’Automation
>[AZURE.NOTE] L’une des conditions préalables à la fin de ce scénario est que vos ordinateurs Windows sont mis à jour avec la dernière version de WMF. Vous pouvez télécharger et installer la version appropriée à votre plate-forme à partir du [Centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=50395).

Vous allez maintenant créer un metaconfig pour DSC que vous appliquerez à vos nœuds. Pour réussir avec cela, vous devez récupérer l’URL du point de terminaison et la clé primaire pour votre compte d’Automation sélectionné dans Azure. Vous pouvez trouver ces valeurs sous **clés** sur la lame de **tous les paramètres** pour le compte de l’Automation.

![Valeurs de clé](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Dans cet exemple, vous disposez d’un serveur physique Windows Server 2012 R2 que vous voulez protéger à l’aide de restauration du Site.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Recherchez tout en attente les opérations de changement de nom de fichier dans le Registre

Avant de commencer à associer le serveur avec le point de terminaison DSC d’Automation, nous vous recommandons de vérifier tout en attente les opérations de changement de nom de fichier dans le Registre. Ils peuvent interdire le programme d’installation de terminer en raison d’un redémarrage en attente.

Exécutez l’applet de commande suivante pour vérifier qu’il n’existe aucun redémarrage en attente sur le serveur :

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si cette montre vide, vous êtes OK pour continuer. Si ce n’est pas le cas, vous devez le résoudre en redémarrant le serveur au cours d’une période de maintenance.

Pour appliquer la configuration sur le serveur, démarrez l’environnement de script intégré PowerShell (ISE) et exécutez le script suivant. C’est essentiellement une configuration DSC locale qui propose le moteur Local Configuration Manager pour inscrire avec le service DSC d’Automation et de récupérer la configuration spécifique (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Cette configuration provoque le moteur Local Configuration Manager s’enregistrer auprès de DSC d’Automation. Il détermine également comment le moteur doit fonctionner, ce qu’il doit faire s’il y a une dérive de configuration (ApplyAndAutoCorrect) et comment il doit procéder à la configuration si un redémarrage est nécessaire.

Après avoir exécuté ce script, le nœud doit commencer à enregistrer avec DSC d’Automation.

![Enregistrement du nœud en cours](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si vous revenez sur le portail Azure, vous pouvez voir que le nœud récemment enregistré apparaît maintenant dans le portail.

![Nœud inscrit sur le portail](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Sur le serveur, vous pouvez exécuter l’applet de commande PowerShell suivante pour vérifier que le nœud a été correctement enregistré :

```powershell
Get-DscLocalConfigurationManager
```

Une fois que la configuration a été chargée et appliquée au serveur, vous pouvez vérifier cela en exécutant l’applet de commande suivante :

```powershell
Get-DscConfigurationStatus
```

La sortie montre que le serveur a tiré avec succès sa configuration :

![Sortie](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

En outre, le programme d’installation du service de mobilité possède son propre journal, consultez *lecteur_système*\ProgramData\ASRSetupLogs.

Voilà. Vous avez maintenant correctement déployé et inscrit au service de la mobilité sur la machine que vous voulez protéger à l’aide de restauration du Site. DSC va s’assurer que les services requis sont toujours en cours d’exécution.

![Déploiement réussi](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Une fois que le serveur d’administration détecte le déploiement réussi, vous pouvez configurer la protection et activer la réplication sur l’ordinateur à l’aide de restauration du Site.

## <a name="use-dsc-in-disconnected-environments"></a>DSC d’utilisation dans les environnements déconnectés

Si vos ordinateurs ne sont pas connectés à Internet, vous pouvez toujours compter sur DSC pour déployer et configurer le service de la mobilité sur les charges de travail que vous souhaitez protéger.

Vous pouvez instancier votre propre serveur d’extraction de DSC dans votre environnement essentiellement les mêmes fonctionnalités que vous obtenez des commentaires DSC d’Automation. Autrement dit, les clients extrait la configuration (après son inscription) au point de terminaison DSC. Toutefois, une autre option consiste à distribuer de manuellement la configuration de DSC sur vos ordinateurs, localement ou à distance.

Notez que dans cet exemple, il existe un paramètre ajouté pour le nom de l’ordinateur. Les fichiers distants sont maintenant situés sur un partage distant qui doit être accessible par les machines que vous souhaitez protéger. La fin du script active la configuration et commence ensuite à appliquer la configuration de DSC sur l’ordinateur cible.

### <a name="prerequisites"></a>Conditions préalables

Assurez-vous que le module PowerShell de xPSDesiredStateConfiguration est installé. Pour les machines Windows où WMF 5.0 est installé, vous pouvez installer le module de xPSDesiredStateConfiguration en exécutant la cmdlet suivante sur les ordinateurs cibles :

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Vous pouvez également télécharger et enregistrez le module dans le cas où vous avez besoin pour le distribuer aux ordinateurs Windows qui ont WMF 4.0. Exécuter cette applet de commande sur un ordinateur où PowerShellGet (WMF 5.0) est présent :

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Également pour WMF 4.0, vérifiez que le [8.1 Windows KB2883200 de mise à jour](https://www.microsoft.com/download/details.aspx?id=40749) est installée sur les ordinateurs.

La configuration suivante peut être installée sur les ordinateurs Windows qui ont WMF 5.0 et 4.0 de WMF :

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Si vous souhaitez instancier votre propre serveur d’extraction de DSC sur votre réseau d’entreprise pour reproduire les fonctionnalités que vous pouvez obtenir des commentaires DSC d’Automation, reportez-vous à la section [configuration d’un serveur d’extraction de web DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Facultatif : Déployer une configuration DSC à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article s’est concentré sur comment vous pouvez créer votre propre configuration de DSC pour déployer le service de la mobilité et l’Agent de la machine virtuelle Azure--automatiquement et de vous assurer qu’ils sont en cours d’exécution sur les ordinateurs que vous souhaitez protéger. Nous avons également un modèle de gestionnaire de ressources Azure déployer cette configuration DSC à un compte Azure Automation nouvel ou existant. Le modèle utilise des paramètres d’entrée pour créer des éléments Automation qui contiendront les variables de votre environnement.

Après avoir déployé le modèle, vous pouvez simplement faire référence à l’étape 4 de ce guide pour intégré vos ordinateurs.

Le modèle effectue les opérations suivantes :

1. Utiliser un compte d’Automation existant ou en créer un nouveau
2. Prendre des paramètres d’entrée :
    - ASRRemoteFile : l’emplacement où vous avez stocké le programme d’installation du service de mobilité
    - ASRPassphrase : l’emplacement où vous avez stocké le fichier passphrase.txt
    - ASRCSEndpoint : l’adresse IP de votre serveur de gestion
3. Importer le module PowerShell de xPSDesiredStateConfiguration
4. Créer et compiler la configuration DSC

Toutes les étapes précédentes se produira dans le bon ordre, afin que vous pouvez démarrer arrivant de vos ordinateurs pour la protection.

Le modèle, avec des instructions pour le déploiement, se trouve sur [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Déployer le modèle à l’aide de PowerShell :

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir déployé les agents de service de la mobilité, vous pouvez [Activer la réplication](site-recovery-vmware-to-azure.md#step-6-replicate-applications) pour les ordinateurs virtuels.
