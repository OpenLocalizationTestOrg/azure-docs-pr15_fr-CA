<properties 
    pageTitle="Utilisation de PowerShell pour créer une machine virtuelle avec un serveur de rapports en Mode natif | Microsoft Azure"
    description="Cette rubrique décrit et vous guide à travers le déploiement et la configuration d’un serveur de rapports SQL Server Reporting Services en mode natif dans une Machine virtuelle Azure. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Utilisation de PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en Mode natif

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Cette rubrique décrit et vous guide à travers le déploiement et la configuration d’un serveur de rapports SQL Server Reporting Services en mode natif dans une Machine virtuelle Azure. Les étapes de ce document utilisent une combinaison d’étapes manuelles pour créer l’ordinateur virtuel et un script Windows PowerShell pour configurer Reporting Services sur l’ordinateur virtuel. Le script de configuration comprend l’ouverture d’un port du pare-feu pour HTTP ou HTTPs.

>[AZURE.NOTE] Si vous n’avez pas besoin de **HTTPS** sur le serveur de rapports, **ignorez l’étape 2**.
>
>Après avoir créé l’ordinateur virtuel à l’étape 1, passez à la section utiliser un script pour configurer le serveur de rapports et de HTTP. Après avoir exécuté le script, le serveur de rapports est prêt à l’emploi.

## <a name="prerequisites-and-assumptions"></a>Conditions préalables

- **Abonnement Azure**: vérifier le nombre de cœurs disponibles dans votre abonnement Azure. Si vous créez la taille recommandée de la mémoire virtuelle de **A3**, vous devez les cœurs disponibles **4** . Si vous utilisez une taille de mémoire virtuelle de **A2**, vous devez les cœurs disponibles **2** .
    
    - Pour vérifier la limite de la base de votre abonnement, dans Azure portal classique, cliquez sur paramètres dans le volet gauche, puis cliquez sur une activité dans le menu supérieur.
    
    - Pour augmenter le quota de base, contactez le [Support technique d’Azure](https://azure.microsoft.com/support/options/). Pour plus d’informations de taille de mémoire virtuelle, voir [Taille de Machine virtuelle pour Azure](virtual-machines-linux-sizes.md).

- **L’écriture de scripts Windows PowerShell**: la rubrique suppose que vous disposez d’une connaissance de base de Windows PowerShell. Pour plus d’informations sur l’utilisation de Windows PowerShell, consultez les rubriques suivantes :

    - [Démarrage de Windows PowerShell sur Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Mise en route avec Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Étape 1 : Mettre en service un ordinateur virtuel Azure

1. Accédez au portail Azure classique.

1. Dans le volet gauche, cliquez sur **ordinateurs virtuels** .

    ![machines virtuelles Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Cliquez sur **Nouveau**.

    ![bouton Nouveau](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Cliquez sur **à partir de la galerie**.

    ![nouvelle machine virtuelle à partir de la galerie](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Cliquez sur **SQL Server 2014 RTM Standard – Windows Server 2012 R2** , puis cliquez sur la flèche pour continuer.

    ![suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Si vous avez besoin de données Reporting Services piloté par la fonctionnalité d’abonnement, sélectionnez **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Pour plus d’informations sur les éditions de SQL Server et prise en charge de la fonctionnalité, voir [Fonctionnalités prises en charge par les éditions de SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Sur la page de **configuration de machine virtuelle** , modifiez les champs suivants :
                                    
    - S’il existe plus d’une **DATE de publication de la VERSION**, sélectionnez la version la plus récente.
    
    - **Nom de la Machine virtuelle**: le nom de l’ordinateur est également utilisé sur la page de configuration suivante sous le nom de Cloud Service DNS par défaut. Le nom DNS doit être unique au sein du service Azure. Envisagez de configurer la machine virtuelle avec un nom d’ordinateur qui décrit l’utilisation de la machine virtuelle. Par exemple, ssrsnativecloud.
    
    - **Niveau**: Standard
    
    - **Taille : A3** est la taille recommandée de la mémoire virtuelle pour les charges de travail de SQL Server. Si un ordinateur virtuel est uniquement utilisé comme un serveur de rapports, une taille de mémoire virtuelle de A2 est suffisante, sauf si le serveur de rapports subit une charge de travail importante. Machine virtuelle informations de tarification, consultez [Tarification des Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/).
    
    - **Nouveau nom d’utilisateur**: le nom que vous fournissez est créé en tant qu’administrateur sur l’ordinateur virtuel.
    
    - **Nouveau mot de passe** et le **Confirmer**. Ce mot de passe est utilisé pour le nouveau compte d’administrateur et il est recommandé de qu'utiliser un mot de passe.
    
    - Cliquez sur **suivant**. ![suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Sur la page suivante, modifiez les champs suivants :

    - **Service Cloud**: sélectionnez **créer un nouveau Service en nuage**.
    
    - **Nom DNS du Service cloud**: c’est le nom DNS public du Service Cloud qui est associé à la machine virtuelle. Le nom par défaut est le nom que vous avez tapé dans le nom de la machine virtuelle. Si dans les étapes ultérieures de la rubrique, vous créez un certificat SSL fiable, et le nom DNS est utilisé pour la valeur de la «**délivré à**» du certificat.
    
    - **Région/affinité/virtuels du groupe réseau**: choisissez la région plus proche de vos utilisateurs finaux.
    
    - **Compte de stockage**: utilisez un compte de stockage généré automatiquement.
    
    - **Disponibilité définie**: aucun.
    
    - **Points de terminaison** Conserver les points de terminaison de **Bureau à distance** et **PowerShell** , puis ajoutez le point de terminaison HTTP ou HTTPS, selon votre environnement.

        - **HTTP**: ports publics et privés par défaut sont **80**. Notez que si vous utilisez un port autre que 80, de privé modifier **$HTTPport = 80** dans le script de http.

        - **HTTPS**: ports publics et privés par défaut sont **443**. Meilleure pratique de sécurité est de modifier le port privé et la configuration de votre pare-feu et le serveur de rapports pour utiliser le port privé. Pour plus d’informations sur les points de terminaison, consultez [comment définir les Communication avec un ordinateur virtuel](virtual-machines-windows-classic-setup-endpoints.md). Notez que si vous utilisez un port autre que 443, modifiez le paramètre **$HTTPsport = 443** dans le script HTTPS.
    
    - Cliquez sur Suivant. ![suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Sur la dernière page de l’Assistant, conservez la valeur par défaut **l’installation de l’agent de la machine virtuelle** sélectionnée. Les étapes de cette rubrique n’utilisent pas l’agent de la machine virtuelle, mais si vous envisagez de conserver cet ordinateur virtuel, les extensions et l’agent de la machine virtuelle vous permettra d’améliorer il CM.  Pour plus d’informations sur l’agent de la machine virtuelle, voir [Agent de la machine virtuelle et Extensions – partie 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). L’annonce d’extensions sont installées par défaut en cours d’exécution est l’extension « BGINFO » qui s’affiche sur le bureau de l’ordinateur virtuel, les informations système telles que l’adresse IP interne et l’espace libre sur le disque.

1. Cliquez sur terminé. ![Bien](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. L' **état** de la machine virtuelle affiche comme **départ (Provisioning)** pendant le processus de disposition, puis affiche **en cours d’exécution** lorsque l’ordinateur virtuel est configuré et prêt à l’emploi.

## <a name="step-2-create-a-server-certificate"></a>Étape 2 : Créer un certificat de serveur

>[AZURE.NOTE] Si vous n’exigez pas HTTPS sur le serveur de rapports, vous pouvez **Ignorer l’étape 2** et passer à la section **utiliser un script pour configurer le serveur de rapports et de HTTP**. Le script HTTP permet de configurer rapidement le serveur de rapports et le serveur de rapports sera prêt à être utilisé.

Pour utiliser le protocole HTTPS sur l’ordinateur virtuel, vous avez besoin d’un certificat SSL fiable. En fonction de votre scénario, vous pouvez utiliser une des deux méthodes suivantes :

- Un certificat SSL valide émis par une autorité de Certification (CA) et approuvé par Microsoft. Les certificats racines d’autorité de certification doivent être distribué via le programme de certificat racine Microsoft. Pour plus d’informations sur ce programme, consultez [Windows et Windows Phone 8 SSL Root Certificate Program (CAs de membre)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) et [Présentation du programme de certificat racine Microsoft](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Un certificat auto-signé. Les certificats auto-signés ne sont pas recommandés pour les environnements de production.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Pour utiliser un certificat créé par une autorité de certification fiable (CA)

1. **Demander un certificat de serveur pour le site Web à partir d’une autorité de certification**. 

    Vous pouvez utiliser l’Assistant certificat de serveur Web pour générer un fichier de demande de certificat (Certreq.txt) que vous envoyez à une autorité de certification, ou générer une demande pour une autorité de certification en ligne. Par exemple, Services de certificats Microsoft dans Windows Server 2012. Selon le niveau d’assurance d’identification offert par votre certificat de serveur, il s’agit de plusieurs jours à plusieurs mois pour l’autorité de certification à approuver votre demande et vous envoyer un fichier de certificat. 

    Pour plus d’informations sur la demande d’un certificat de serveur, consultez les rubriques suivantes : 
    
    - Utilisez [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Outils de sécurité pour administrer Windows Server 2012.

    [Outils de sécurité pour administrer Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] Le champ **délivré** de ce certificat SSL doit être le même que le **Nom DNS du Service nuage** de la nouvelle machine virtuelle.

1. **Installer le certificat de serveur sur le serveur Web**. Dans ce cas, le serveur Web est la machine virtuelle qui héberge le serveur de rapports et le site Web est créé dans les étapes suivantes lorsque vous configurez Reporting Services. Pour plus d’informations sur l’installation du certificat de serveur sur le serveur Web à l’aide du composant logiciel enfichable MMC Certificats, consultez [installer un certificat de serveur](https://technet.microsoft.com/library/cc740068).
    
    Si vous souhaitez utiliser le script inclus dans cette rubrique, pour configurer le serveur de rapports, la valeur des certificats **empreinte** est requise en tant que paramètre du script. Consultez la section suivante pour plus d’informations sur la façon d’obtenir l’empreinte numérique du certificat.

1. Attribuer le certificat de serveur pour le serveur de rapports. L’assignation est effectuée dans la section suivante lorsque vous configurez le serveur de rapports.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Pour utiliser le certificat auto-signé de Machines virtuelles

Un certificat auto-signé a été créé sur l’ordinateur virtuel lorsque l’ordinateur virtuel a été mis en service. Le certificat a le même nom que le nom DNS de la VM. Afin d’éviter les erreurs de certificat, il est nécessaire que le certificat est approuvé sur l’ordinateur virtuel lui-même, ainsi que par tous les utilisateurs du site.

1. Pour faire confiance à l’autorité de certification racine du certificat sur l’ordinateur virtuel Local, ajoutez le certificat aux **Autorités de Certification racine de confiance**. Voici un résumé des étapes requises. Pour obtenir des procédures détaillées sur la façon d’approuver l’autorité de certification, consultez [installer un certificat de serveur](https://technet.microsoft.com/library/cc740068).

    1. À partir du portail Azure classique, sélectionnez la machine virtuelle et cliquez sur se connecter. En fonction de la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.
    
        ![se connecter à des machines virtuelles azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom d’utilisateur machine virtuelle, le nom d’utilisateur et le mot de passe que vous configuré lorsque vous avez créé l’ordinateur virtuel. 
    
        Par exemple, dans l’image suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur est **testuser**.
        
        ![nom de machine virtuelle comprend](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Exécutez mmc.exe. Pour plus d’informations, consultez [Comment : afficher des certificats avec le composant logiciel enfichable MMC,](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. Dans le menu **fichier** de l’application console, ajoutez le composant logiciel enfichable **certificats** , sélectionnez **Compte d’ordinateur** lorsque vous y êtes invité, puis cliquez sur **suivant**.
    
    1. Sélectionnez **Ordinateur Local** pour gérer et puis cliquez sur **Terminer**.
    
    1. Cliquez sur **Ok** , puis développez le **- des certificats personnels** nœuds, puis cliquez sur **certificats**. Le certificat est nommé d’après le nom DNS de l’ordinateur virtuel et se termine par **cloudapp.net**. Le bouton droit sur le certificat, puis cliquez sur **Copier**.
    
    1. Développez le nœud **Autorités de Certification racines de confiance** , puis cliquez sur **certificats** et puis cliquez sur **Coller**.
    
    1. Pour valider, cliquez deux fois sur le nom du certificat sous **Autorités de Certification racine de confiance** et vérifiez qu’il n’y a aucune erreur et que vous consultez votre certificat. Si vous souhaitez utiliser l’HTTPS de script inclus dans cette rubrique, pour configurer le serveur de rapports, la valeur des certificats **empreinte** est requise en tant que paramètre du script. **Pour obtenir la valeur de l’empreinte numérique**, effectuez les opérations suivantes. Il existe également un exemple de PowerShell pour récupérer l’empreinte dans la section [utiliser un script pour configurer le serveur de rapports et HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Double-cliquez sur le nom du certificat, par exemple ssrsnativecloud.cloudapp.net.
        
        1. Cliquez sur l’onglet **Détails** .
        
        1. Cliquez sur **Aperçu**. La valeur de l’empreinte numérique est affichée dans le champ Détails, par exemple a6 08 3c df f9 0c f7 e3 7c 25 ed a4 ed 7e CA 91 9c 2c fb 2f.
        
        1. Copiez l’empreinte numérique et enregistrez la valeur pour plus tard ou modifier le script maintenant.
        
        1. (*) Avant d’exécuter le script, supprimez les espaces entre les paires de valeurs. Par exemple l’empreinte de noter avant est désormais a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Attribuer le certificat de serveur pour le serveur de rapports. L’assignation est effectuée dans la section suivante lorsque vous configurez le serveur de rapports.

Si vous utilisez un certificat SSL auto-signé, le nom du certificat correspond déjà le nom d’hôte de la machine virtuelle. Par conséquent, le serveur DNS de l’ordinateur est a déjà été inscrit globalement et est accessible à partir de n’importe quel client.

## <a name="step-3-configure-the-report-server"></a>Étape 3 : Configurer le serveur de rapports

Cette section vous guide dans le processus de configuration de la machine virtuelle sous la forme d’un serveur de rapports Reporting Services en mode natif. Vous pouvez utiliser une des méthodes suivantes pour configurer le serveur de rapports :

- Utilisez le script pour configurer le serveur de rapports

- Utilisez le Gestionnaire de Configuration pour configurer le serveur de rapports.

Pour obtenir la procédure détaillée, voir la section [se connecter à l’ordinateur virtuel et de démarrer le Gestionnaire de Configuration de Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**D’authentification Remarque :** L’authentification Windows est la méthode d’authentification recommandée et l’authentification de Reporting Services par défaut. Seuls les utilisateurs qui sont configurés sur l’ordinateur virtuel peuvent accéder aux Services de création de rapports et affectés à des rôles de Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Utiliser un script pour configurer le serveur de rapports et HTTP

Pour utiliser le script Windows PowerShell pour configurer le serveur de rapports, procédez comme suit. La configuration inclut le trafic HTTP, HTTPS pas :

1. À partir du portail Azure classique, sélectionnez la machine virtuelle et cliquez sur se connecter. En fonction de la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.

    ![se connecter à des machines virtuelles azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom d’utilisateur machine virtuelle, le nom d’utilisateur et le mot de passe que vous configuré lorsque vous avez créé l’ordinateur virtuel. 

    Par exemple, dans l’image suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur est **testuser**.
    
    ![nom de machine virtuelle comprend](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Sur l’ordinateur virtuel, ouvrez **Windows PowerShell ISE** avec des privilèges d’administrateur. PowerShell ISE est installé par défaut sur Windows server 2012. Il est recommandé de qu'utiliser l’ISE au lieu d’une fenêtre Windows PowerShell standard afin que vous pouvez coller le script dans l’ISE, modifier le script et puis exécuter le script.

1. Dans Windows PowerShell ISE de, cliquez sur le menu **affichage** et puis cliquez sur **Afficher la fenêtre de Script**.

1. Copiez le script suivant et collez le script dans la fenêtre de script Windows PowerShell ISE.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Si vous avez créé l’ordinateur virtuel avec un port HTTP autre que 80, modifiez le paramètre $HTTPport = 80.

1. Le script est actuellement configuré pour Reporting Services. Si vous souhaitez exécuter le script pour Reporting Services, modifier la partie de la version du chemin d’accès à l’espace de noms « 11 », sur l’instruction Get-WmiObject.

1. Exécutez le script.

**Validation**: pour vérifier que la fonctionnalité de serveur de rapport de base fonctionne, reportez-vous à la section [vérifier la configuration](#verify-the-configuration) plus loin dans cette rubrique.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Utiliser un script pour configurer le serveur de rapports et HTTPS

Pour utiliser Windows PowerShell pour configurer le serveur de rapports, procédez comme suit. La configuration inclut HTTPS et non HTTP.

1. À partir du portail Azure classique, sélectionnez la machine virtuelle et cliquez sur se connecter. En fonction de la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.

    ![se connecter à des machines virtuelles azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom d’utilisateur machine virtuelle, le nom d’utilisateur et le mot de passe que vous configuré lorsque vous avez créé l’ordinateur virtuel. 

    Par exemple, dans l’image suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur est **testuser**.

    ![nom de machine virtuelle comprend](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Sur l’ordinateur virtuel, ouvrez **Windows PowerShell ISE** avec des privilèges d’administrateur. PowerShell ISE est installé par défaut sur Windows server 2012. Il est recommandé de qu'utiliser l’ISE au lieu d’une fenêtre Windows PowerShell standard afin que vous pouvez coller le script dans l’ISE, modifier le script et puis exécuter le script.

1. Pour activer les scripts en cours d’exécution, exécutez la commande Windows PowerShell suivante :

        Set-ExecutionPolicy RemoteSigned

    Vous pouvez ensuite exécuter la commande suivante pour vérifier la stratégie :

        Get-ExecutionPolicy

1. Dans **Windows PowerShell ISE**, cliquez sur le menu **affichage** et puis cliquez sur **Afficher la fenêtre de Script**.

1. Copiez le script suivant et collez-le dans la fenêtre de script Windows PowerShell ISE.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Modifiez le paramètre **$certificatehash** dans le script :

    - Il s’agit d’un paramètre **obligatoire** . Si vous n’avez pas enregistré la valeur du certificat à partir de la procédure précédente, utilisez une des méthodes suivantes pour copier la valeur de hachage du certificat de l’empreinte des certificats. :

        Sur l’ordinateur virtuel, ouvrez Windows PowerShell ISE et exécutez la commande suivante :

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        Le résultat sera semblable au suivant. Si le script renvoie une ligne vide, la machine virtuelle ne pas disposer d’un certificat configuré par exemple, reportez-vous à la section [pour utiliser le certificat auto-signé de Machines virtuelles](#to-use-the-virtual-machines-self-signed-certificate).
    
    OU
    
    - Sur l’ordinateur virtuel, exécutez mmc.exe et ajoutez le composant logiciel enfichable **certificats** .
    
    - Sous le nœud **Autorités de certification racine de confiance** , double-cliquez sur le nom de votre certificat. Si vous utilisez le certificat auto-signé de la machine virtuelle, le certificat est nommé d’après le nom DNS de l’ordinateur virtuel et se termine par **cloudapp.net**.
    
    - Cliquez sur l’onglet **Détails** .
    
    - Cliquez sur **Aperçu**. La valeur de l’empreinte numérique est affichée dans le champ Détails, par exemple af 11 60 b6 4 b 28 8 d 89 0 a 82 12 ff 6 b a9 c3 66 4f 31 90 48
    
    - **Avant d’exécuter le script**, supprimez les espaces entre les paires de valeurs. Par exemple af1160b64b288d890a8212ff6ba9c3664f319048

1. Modifiez le paramètre **$httpsport** : 

    - Si vous avez utilisé le port 443 pour le point de terminaison HTTPS, vous n’avez pas besoin de ce paramètre dans le script de mise à jour. Dans le cas contraire, utilisez la valeur de port que vous avez sélectionné lorsque vous avez configuré le point de terminaison HTTPS privée sur l’ordinateur virtuel.

1. Modifiez le paramètre **$DNSName** : 

    - Le script est configuré pour un certificat générique $DNSName = « + ». Si vous le faites sans que vous souhaitez configurer pour une liaison de certificat générique, commentez $DNSName = » + « et activez la ligne suivante, la référence de $DNSNAme complet ## $DNSName="$server.cloudapp.net ».

        Modifiez la valeur $DNSName si vous ne souhaitez pas utiliser le nom DNS de la machine virtuelle pour Reporting Services. Si vous utilisez le paramètre, le certificat doit également utiliser ce nom et vous inscrivez le nom globalement sur un serveur DNS.

1. Le script est actuellement configuré pour Reporting Services. Si vous souhaitez exécuter le script pour Reporting Services, modifier la partie de la version du chemin d’accès à l’espace de noms « 11 », sur l’instruction Get-WmiObject.

1. Exécutez le script.

**Validation**: pour vérifier que la fonctionnalité de serveur de rapport de base fonctionne, reportez-vous à la section [vérifier la configuration](#verify-the-connection) plus loin dans cette rubrique. Pour vérifier le certificat de liaison ouvrir une invite de commande avec des privilèges d’administrateur et puis exécutez la commande suivante :

    netsh http show sslcert

Le résultat comprendra les éléments suivants :

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utilisez le Gestionnaire de Configuration pour configurer le serveur de rapports

Si vous ne souhaitez pas exécuter le script PowerShell pour configurer le serveur de rapports, suivez les étapes de cette section pour utiliser le Gestionnaire de configuration de Reporting Services en mode natif pour configurer le serveur de rapports.

1. À partir du portail Azure classique, sélectionnez la machine virtuelle et cliquez sur se connecter. Utilisez le nom d’utilisateur et le mot de passe que vous configuré lorsque vous avez créé l’ordinateur virtuel.

    ![se connecter à des machines virtuelles azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Exécutez Windows update et installer les mises à jour de la machine virtuelle. Si un redémarrage de la machine virtuelle est nécessaire, redémarrez l’ordinateur virtuel et vous reconnecter à la machine virtuelle à partir du portail classique Azure.

1. Dans le menu Démarrer sur l’ordinateur virtuel, tapez **Reporting Services** et ouvrir le **Gestionnaire de Configuration de Reporting Services**.

1. Laissez les valeurs par défaut pour le **Nom du serveur** et **l’Instance de serveur de rapports**. Cliquez sur **se connecter**.

1. Dans le volet gauche, cliquez sur **l’URL du Service Web**.

1. Par défaut, RS est configuré pour le port HTTP 80 avec IP « Toutes non attribuées ». Pour ajouter le protocole HTTPS :

    1. Dans le **Certificat SSL**: sélectionnez le certificat que vous souhaitez utiliser, par exemple [nom de la machine virtuelle]. cloudapp.net. Si aucun certificat n’est répertorié, reportez-vous à la section **étape 2 : créer un certificat de serveur** pour plus d’informations sur la façon d’installer et d’approuver le certificat sur l’ordinateur virtuel.
    
    1. Sous **Port SSL**: choisissez 443. Si vous avez configuré le point de terminaison HTTPS privée de l’ordinateur virtuel avec un autre port privé, utilisez cette valeur ici.
    
    1. Cliquez sur **Appliquer** et attendez que l’opération se termine.

1. Dans le volet gauche, cliquez sur **base de données**.

    1. Cliquez sur **Modifier Databas**e.
    
    1. Cliquez sur **créer une nouvelle base de données de serveur de rapport** , puis sur **suivant**.
    
    1. Le **Nom du serveur**par défaut : nom de l’ordinateur virtuel et laissez la valeur par défaut du **Type d’authentification** en tant qu' **Utilisateur actuel** - **Sécurité intégrée**. Cliquez sur **suivant**.
    
    1. Laissez la valeur par défaut du **Nom de la base de données** **ReportServer** et cliquez sur **suivant**.
    
    1. Laisser le **Type d’authentification** en tant que **Service d’informations d’identification** par défaut et cliquez sur **suivant**.
    
    1. Dans la page **Résumé** , cliquez sur **suivant** .
    
    1. Une fois la configuration terminée, cliquez sur **Terminer**.

1. Dans le volet gauche, cliquez sur **Gestionnaire de rapports**. Laissez la valeur par défaut du **Répertoire virtuel** sous forme de **rapports** , puis cliquez sur **Appliquer**.

1. Cliquez sur **Quitter** pour fermer le Gestionnaire de Configuration de Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Étape 4 : Ouvrir le Port pare-feu Windows

>[AZURE.NOTE] Si vous avez utilisé un des scripts pour configurer le serveur de rapports, vous pouvez ignorer cette section. Le script inclus une étape pour ouvrir le port du pare-feu. La valeur par défaut est le port 80 pour HTTP et le port 443 pour HTTPS.

Pour vous connecter à distance au Gestionnaire de rapports ou le serveur de rapports sur l’ordinateur virtuel, un point de terminaison TCP est requis sur l’ordinateur virtuel. Il est nécessaire d’ouvrir le même port dans le pare-feu de l’ordinateur virtuel. Le point de terminaison a été créé lorsque la machine virtuelle a été mis en service.

Cette section fournit des informations de base sur la façon d’ouvrir le port du pare-feu. Pour plus d’informations, consultez [configurer un pare-feu pour accéder au serveur de rapports](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Si vous avez utilisé le script pour configurer le serveur de rapports, vous pouvez ignorer cette section. Le script inclus une étape pour ouvrir le port du pare-feu.

Si vous avez configuré un port privé pour le protocole HTTPS autre que 443, modifier en conséquence le script suivant. Pour ouvrir le port **443** sur le pare-feu Windows, effectuez les opérations suivantes :

1. Ouvrez une fenêtre Windows PowerShell avec des privilèges d’administrateur.

1. Si vous avez utilisé un port autre que 443 lors de la configuration du point de terminaison HTTPS sur l’ordinateur virtuel, mettez à jour le port dans la commande suivante, puis exécutez la commande :

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Lorsque la commande a terminé, **Ok** s’affiche dans l’invite de commande.

Pour vérifier que le port est ouvert, ouvrez une fenêtre de Windows PowerShell et exécutez la commande suivante :

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Vérification de la configuration

Pour vérifier que la fonctionnalité de serveur de rapport de base fonctionne maintenant, ouvrir votre navigateur avec des privilèges d’administrateur et accédez ensuite au suivant ad rapport Gestionnaire de report server URL :

- Sur la machine virtuelle, accédez à l’URL du serveur de rapports :

        http://localhost/reportserver

- Sur l’ordinateur virtuel, accédez à l’URL du Gestionnaire de rapports :

        http://localhost/Reports

- À partir de votre ordinateur local, accédez à l’état **remote** Manager sur l’ordinateur virtuel. Mettre à jour le nom DNS dans l’exemple suivant, selon le cas. Lorsque vous y êtes invité pour un mot de passe, utilisez les informations d’identification administrateur créé lors de la machine virtuelle a été mis en service. Le nom d’utilisateur est dans la section [domaine]\[nom d’utilisateur] format, où domaine est le nom d’ordinateur de machine virtuelle, par exemple ssrsnativecloud\testuser. Si vous n’utilisez pas HTTP**S**, supprimer le **s** dans l’URL. Consultez la section suivante pour plus d’informations sur la création d’utilisateurs supplémentaires sur la machine virtuelle.

        https://ssrsnativecloud.cloudapp.net/Reports

- À partir de votre ordinateur local, accédez à l’URL de serveur de rapports distant. Mettre à jour le nom DNS dans l’exemple suivant, selon le cas. Si vous n’utilisez pas le protocole HTTPS, supprimez la lettre s dans l’URL.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Créer des utilisateurs et attribuer des rôles

Après la configuration et la vérification du serveur de rapports, une tâche administrative courante est de créer un ou plusieurs utilisateurs et affecter des utilisateurs aux rôles de Reporting Services. Pour plus d’informations, consultez les rubriques suivantes :

- [Créer un compte d’utilisateur local](https://technet.microsoft.com/library/cc770642.aspx)

- [Cet accès à un serveur de rapports (Gestionnaire de rapports)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Créer et gérer des affectations de rôle](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Pour créer et publier des rapports sur l’ordinateur virtuel Azure

Le tableau suivant résume quelques-unes des options disponibles pour publier des rapports existants à partir d’un ordinateur local vers le serveur de rapports hébergé sur la Machine virtuelle de Microsoft Azure :

- **Script de RS.exe**: script utilisation RS.exe à copier à partir des éléments de rapports et report server existant à votre Machine virtuelle de Microsoft Azure. Pour plus d’informations, consultez la section « Mode natif en mode natif – Machine virtuelle de Microsoft Azure » dans [l’exemple Reporting Services rs.exe Script pour migrer le contenu entre des serveurs de rapports](https://msdn.microsoft.com/library/dn531017.aspx).

- **Générateur de rapports**: l’ordinateur virtuel inclut le clic-une fois la version du Générateur de rapports de Microsoft SQL Server. Pour démarrer le temps de générateur le premier rapport sur la machine virtuelle :

    1. Démarrez votre navigateur avec des privilèges d’administrateur.
    
    1. Accédez au Gestionnaire de rapports sur l’ordinateur virtuel, puis cliquez sur **Générateur de rapports** dans le ruban.

    Pour plus d’informations, reportez-vous à la section [installation, désinstallation et prenant en charge le Générateur de rapports](https://technet.microsoft.com/library/dd207038.aspx).

- **Outils de données de SQL Server : machine virtuelle**: Si vous avez créé l’ordinateur virtuel avec SQL Server 2012, puis Outils de données de SQL Server est installé sur l’ordinateur virtuel et peut être utilisé pour créer des **Projets de serveur de rapports** et les rapports sur l’ordinateur virtuel. Outils de données de SQL Server peuvent les publier sur le serveur de rapports sur l’ordinateur virtuel.
    
    Si vous avez créé l’ordinateur virtuel avec le serveur SQL 2014, vous pouvez installer les données de SQL Server Outils - BI pour visual Studio. Pour plus d’informations, consultez les rubriques suivantes :

    - [Outils de données de Microsoft SQL Server - Business Intelligence pour Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Outils de données de Microsoft SQL Server - Business Intelligence pour Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

    - [Outils de données de SQL Server et SQL Server Business Intelligence (BI-SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **Outils de données de SQL Server : distant**: sur votre ordinateur local, créez un projet de Reporting Services dans les outils de données de SQL Server qui contient les rapports Reporting Services. Configurez le projet pour se connecter à l’URL du service web.

    ![ssdt des propriétés de projet pour le projet SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Utiliser un script**: utiliser un script pour copier le contenu du serveur rapport. Pour plus d’informations, consultez [exemple Reporting Services rs.exe Script pour migrer le contenu entre des serveurs de rapports](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Réduire les coûts si vous n’utilisez pas la machine virtuelle

>[AZURE.NOTE] Pour réduire les frais de vos Machines virtuelles de Azure pas en utilisation, arrêtez l’ordinateur virtuel à partir du portail classique Azure. Si vous utilisez les options d’alimentation Windows à l’intérieur d’une machine virtuelle à l’arrêt de la machine virtuelle, vous sont toujours le même montant débité de la machine virtuelle. Afin de réduire les frais, vous devez arrêter l’ordinateur virtuel dans Azure portal classique. Si vous n’avez plus la machine virtuelle, n’oubliez pas de supprimer la machine virtuelle et les fichiers .vhd associés pour éviter des frais de stockage. Pour plus d’informations, consultez la section Forum aux questions sur les [Détails de la tarification des Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Plus d’informations

### <a name="resources"></a>Ressources

- Pour contenu similaire à un déploiement de serveur unique de SQL Server Business Intelligence et SharePoint 2013, consultez [Utilisation des de Windows PowerShell pour créer une machine virtuelle Azure avec SQL Server BI et SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Pour un contenu similaire relatives à un déploiement sur plusieurs serveurs d’analyse décisionnelle SQL Server et SharePoint 2013, voir [Déployer SQL Server Business Intelligence dans Azure Virtual Machines](https://msdn.microsoft.com/library/dn321998.aspx).

- Pour des informations générales relatives aux déploiements de SQL Server Business Intelligence dans Azure Virtual Machines, consultez [SQL Server Business Intelligence dans Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).

- Pour plus d’informations sur les coûts d’Azure compute frais, reportez-vous à l’onglet ordinateurs virtuels de la [Calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Contenu de la Communauté

- Pour obtenir des instructions étape par étape comment créer un serveur de rapports en mode natif des Services de création de rapports sans utiliser le script, reportez-vous à la section [SQL Reporting Service d’hébergement sur Azure Virtual Machine](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Liens vers d’autres ressources pour SQL Server dans Azure VM

[SQL Server sur des Machines virtuelles Azure présentation](virtual-machines-windows-sql-server-iaas-overview.md)
