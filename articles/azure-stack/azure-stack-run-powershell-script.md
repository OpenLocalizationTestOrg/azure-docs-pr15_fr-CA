<properties
    pageTitle="Déployer la pile Azure VT | Microsoft Azure"
    description="Apprenez à préparer le test de pile Azure et d’exécuter le script PowerShell pour le déploiement de la VT de pile Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Déployer la pile Azure POC
Pour déployer le contrôle du concept pile Azure, vous devez d’abord [préparer l’ordinateur de déploiement](#prepare-the-deployment-machine) , puis [exécutez le script de déploiement PowerShell](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Téléchargez et extrayez le protocole TP2 Microsoft Azure pile POC

Avant de commencer, assurez-vous que vous au moins 85 Go d’espace.

1. Le téléchargement du protocole TP2 POC de pile Azure se compose d’un fichier zip contenant les fichiers suivants de 12, environ 20 Go au total :
    - 1 MicrosoftAzureStackPOC.EXE
2. Passez en revue l’écran contrat de licence et les informations de l’Assistant de Self-Extractor et puis cliquez sur **suivant**.
3. Passer en revue les informations de l’Assistant de Self-Extractor et un écran de la déclaration de confidentialité, puis sur **suivant**.
4. Sélectionnez la Destination pour les fichiers extraits, cliquez sur **suivant**.
    - La valeur par défaut est : <drive letter>:\<dossier actuel > \Microsoft Azure pile POC
5. Passez en revue les informations de l’Assistant de Self-Extractor et un écran de l’emplacement de Destination, puis cliquez sur **Extraire** pour extraire les fichiers de la ThirdPartyLicenses.rtf et de CloudBuilder.vhdx (~44.5 Go).

> [AZURE.NOTE] Après avoir extrait les fichiers, vous pouvez supprimer le fichier zip pour récupérer de l’espace sur l’ordinateur. Ou bien, vous pouvez déplacer le fichier zip vers un autre emplacement afin que si vous nécessaire de redéployer vous n’êtes pas obligé de télécharger de nouveau les fichiers zip.

## <a name="prepare-the-deployment-machine"></a>Préparez l’ordinateur de déploiement

1. Assurez-vous que vous pouvez physiquement se connecter à l’ordinateur de déploiement, ou avoir accès à la console physique (par exemple KVM). Vous aurez besoin de ce type d’accès après le redémarrage de l’ordinateur de déploiement à l’étape 9.

2. Assurez-vous que l’ordinateur de déploiement répond à [configuration minimale requise](azure-stack-deploy.md). Vous pouvez utiliser le [Vérificateur de déploiement pour Azure pile technique Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) pour confirmer vos besoins.

3. Connectez-vous en tant qu’administrateur Local sur votre machine de VT.

4. Copiez le fichier CloudBuilder.vhdx dans C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Si vous choisissez de ne pas utiliser le script recommandé pour préparer votre ordinateur hôte POC (étapes 5 – étape 7), n’entrez pas une clé de licence à la page d’activation. Une version d’évaluation de Windows Server 2016 image est incluse, et entrer une clé de licence entraîne l’expiration des messages d’avertissement.

5. Sur l’ordinateur du contact, exécutez le script PowerShell suivant pour télécharger les fichiers de prise en charge le protocole TP2 pile Azure :

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Ce script télécharge les fichiers de prise en charge le protocole TP2 pile Azure dans le dossier spécifié par le paramètre $LocalPath.
    
6. Ouvrez une console PowerShell élevée et modifiez le répertoire où vous avez copié les fichiers.
    - 11 MicrosoftAzureStackPOC-N.BIN (où N est 1-11)
7. Avec le bouton droit sur le MicrosoftAzureStackPOC.EXE > Exécuter en tant qu’administrateur.

8. Exécutez le script PrepareBootFromVHD.ps1. Ce script et les fichiers d’installation sans assistance sont disponibles avec les autres scripts de prise en charge fournis avec cette version.
    Il existe cinq paramètres de ce script PowerShell :
    - CloudBuilderDiskPath (obligatoire) : le chemin d’accès à la CloudBuilder.vhdx sur l’hôte.
    - DriverPath (facultatif) – permet d’ajouter des pilotes supplémentaires pour l’hôte dans le disque dur virtuel.
    - ApplyUnattend (facultatif) – permet de spécifier ce paramètre de commutateur pour automatiser la configuration du système d’exploitation. Si spécifié, l’utilisateur doit fournir le AdminPassword pour configurer le système d’exploitation au démarrage (nécessite une condition qui accompagne le fichier unattend_NoKVM.xml).
    Si vous n’utilisez pas ce paramètre, le fichier unattend.xml générique est utilisé sans poursuivre la personnalisation. Vous devrez KVM une personnalisation complète une fois qu’il redémarre.
    - AdminPassword (facultatif) – utilisé uniquement lorsque le paramètre ApplyUnattend est défini, nécessite un minimum de six caractères.
    - VHDLanguage (facultatif) – spécifie le langage VHD, une valeur par défaut de « en-US ».
    Le script est décrit et contient des exemples d’utilisation, bien que l’utilisation la plus courante est :
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Si vous exécutez cette commande exacte, vous devez entrer AdminPassword à l’invite de commandes.

9. Lorsque le script est terminé, vous devez confirmer le redémarrage. Si des utilisateurs sont connectés, cette commande échoue. Si la commande échoue, exécutez la commande suivante :`Restart-Computer -force` 

10. Redémarrage de l’hôte dans le système d’exploitation de la CloudBuilder.vhdx, où le déploiement se poursuit.

> [AZURE.IMPORTANT] Pile Azure requiert l’accès à Internet, soit directement, soit via un proxy transparent. Le déploiement de la VT du protocole TP2 prend en charge qu’une seule carte d’interface réseau pour le réseau. Si vous avez plusieurs cartes réseau, assurez-vous que seul est activé (et tous les autres sont désactivés) avant d’exécuter le script de déploiement dans la section suivante.

## <a name="run-the-powershell-deployment-script"></a>Exécutez le script de déploiement PowerShell

1. Connectez-vous en tant qu’administrateur Local sur votre machine de VT. Utilisez les informations d’identification spécifiées dans les étapes précédentes.

2. Ouvrez une console PowerShell avec élévation de privilèges.

3. Dans PowerShell, exécutez la commande suivante :`cd C:\CloudDeployment\Configuration`

4. Exécutez la commande déployer :`.\InstallAzureStackPOC.ps1`

5. À l’invite **Entrez le mot de passe** , entrez un mot de passe et confirmez-le. Il s’agit du mot de passe pour tous les ordinateurs virtuels. Veillez à l’enregistrer.

6. Entrez les informations d’identification de votre compte Azure Active Directory. Cet utilisateur doit être l’administrateur Global dans le client de l’annuaire.

7. Le processus de déploiement peut prendre quelques heures, au cours de laquelle le système redémarre automatiquement une seule fois.

    > [AZURE.IMPORTANT] Si vous voulez surveiller la progression du déploiement, ouvrez une session en tant qu’azurestack\AzureStackAdmin. Si vous vous connectez en tant qu’un administrateur local une fois que l’ordinateur est joint au domaine, vous ne verrez pas la progression du déploiement. Ne pas relancer le déploiement, au lieu de cela vous connecter en tant qu’azurestack\AzureStackAdmin à valider qu’il s’exécute.

    Lorsque le déploiement réussit, la console PowerShell affiche : **complète : Action « Déploiement »**.

    Si le déploiement échoue, vous pouvez essayer de [réexécuter](azure-stack-rerun-deploy.md). Ou bien, vous pouvez [redéployer](azure-stack-redeploy.md) à partir de zéro.

### <a name="deployment-script-examples"></a>Exemples de script de déploiement

Si votre identité DAS n’est associé à un répertoire DAS :

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Si votre identité DAS est associée supérieure à un DAS répertoire :

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Si votre environnement n’a pas DHCP activé, vous devez inclure les paramètres supplémentaires suivants à l’une des options ci-dessus (utilisation de l’exemple fourni) :

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>Paramètres facultatifs InstallAzureStackPOC.ps1

| Paramètre | Obligatoire ou facultatif | Description |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Facultatif | Définit le nom d’utilisateur Azure Active Directory et le mot de passe. Ces informations d’identification Azure peuvent être un ID d’organisation ou un Account de Microsoft. Pour utiliser les informations d’identification Microsoft Account, omettez ce paramètre dans l’applet de commande. L’omission de ce paramètre d’invite la fenêtre contextuelle de l’authentification d’Azure pendant le déploiement. Cette opération crée les jetons d’authentification et d’actualisation utilisés pendant le déploiement. |
| AADDirectoryTenantName | Obligatoire | Définit le répertoire client. Utilisez ce paramètre pour spécifier un répertoire spécifique dans lequel le compte DAS dispose des autorisations pour gérer plusieurs répertoires. Complète le nom d’un client de répertoire DAS dans le format de <directoryName>. onmicrosoft.com. |
| AdminPassword | Obligatoire | Définit le compte d’administrateur local et tous les autres comptes utilisateur sur tous les ordinateurs virtuels créés dans le cadre du déploiement de la VT. Ce mot de passe doit correspondre le mot de passe de l’administrateur local actif sur l’hôte. |
| AzureEnvironment | Facultatif | Sélectionnez l’environnement Azure avec lequel vous souhaitez enregistrer ce déploiement d’Azure pile. Les options incluent *Public Azure*, *Azure - Chine*, *Azure - gouvernement des États-Unis*. |
| EnvironmentDNS | Facultatif | Un serveur DNS est créé dans le cadre du déploiement de pile d’Azure. Pour permettre aux ordinateurs à l’intérieur de la solution pour résoudre les noms en dehors de l’empreinte du cachet, fournissent à votre serveur DNS infrastructure. Le serveur DNS de timbre à transfère les demandes de résolution de nom inconnu à ce serveur. |
| NatIPv4Address | Requis pour la prise en charge DHCP NAT | Définit une adresse IP statique pour MAS-BGPNAT01. Utilisez uniquement ce paramètre si le protocole DHCP ne peut pas attribuer une adresse IP valide pour accéder à Internet. |
| NatIPv4DefaultGateway | Requis pour la prise en charge DHCP NAT | Définit la passerelle par défaut utilisée avec l’adresse IP statique pour MAS-BGPNAT01. Utilisez uniquement ce paramètre si le protocole DHCP ne peut pas attribuer une adresse IP valide pour accéder à Internet.  |
| NatIPv4Subnet | Requis pour la prise en charge DHCP NAT | Préfixe de sous-réseau IP utilisé pour DHCP prise en charge de NAT. Utilisez uniquement ce paramètre si le protocole DHCP ne peut pas attribuer une adresse IP valide pour accéder à Internet.  |
| PublicVLan | Facultatif | Définit l’ID de réseau VLAN. Utilisez uniquement ce paramètre si l’hôte et les agents de gestion-BGPNAT01 doivent configurer ID VLAN pour accéder au réseau physique (et à Internet). Par exemple,`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Relancer | Facultatif | Utilisez cet indicateur à nouveau le déploiement.  Toutes les entrées précédentes sont utilisée. Ressaisir les données précédemment fournies ne sont pas pris en charge car plusieurs valeurs uniques sont générés et utilisés pour le déploiement. |
| Serveurs de temps | Facultatif | Utilisez ce paramètre si vous devez spécifier un serveur de temps spécifique. |

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à la pile Azure](azure-stack-connect-azure-stack.md)
