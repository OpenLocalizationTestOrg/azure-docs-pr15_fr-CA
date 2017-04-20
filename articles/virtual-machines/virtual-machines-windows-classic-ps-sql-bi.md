<properties
    pageTitle="SQL Server Business Intelligence | Microsoft Azure"
    description="Cette rubrique utilise les ressources créées avec le modèle classique de déploiement et décrit les fonctionnalités de Business Intelligence (BI) disponibles pour SQL Server en cours d’exécution sur les ordinateurs virtuels Azure (VMs)."
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

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence dans des Machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

La bibliothèque de la Machine virtuelle de Microsoft Azure inclut les images qui contiennent une installation de SQL Server. Les éditions de SQL Server prises en charge dans les images de la galerie sont les mêmes fichiers d’installation que vous pouvez installer sur les ordinateurs locaux et les machines virtuelles. Cette rubrique résume les fonctionnalités de SQL Server Business Intelligence (BI) installées sur les images et les étapes de configuration requises une fois un ordinateur virtuel est en service. Cette rubrique décrit les topologies de déploiement prises en charge pour les fonctionnalités d’analyse Décisionnelle et les meilleures pratiques.

## <a name="license-considerations"></a>Considérations relatives à la licence

Il existe deux façons de licence de SQL Server dans Microsoft Azure Virtual Machines :

1. Avantages de mobilité de licence qui font partie de la Software Assurance. Pour plus d’informations, reportez-vous à la section [Mobilité licence par le biais de Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Payer par le taux horaire d’Azure Virtual Machines avec SQL Server installé. Consultez la section « SQL Server » dans le [Prix des Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Pour plus d’informations sur les taux en cours et de licences, consultez le [Forum aux questions sur le Gestionnaire de licences d’ordinateurs virtuels](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server les Images disponibles dans la galerie de Machine virtuelle Azure

La galerie de la Machine virtuelle de Microsoft Azure comprend plusieurs images qui contiennent de Microsoft SQL Server. Le logiciel installé sur les images de machine virtuelle varie en fonction de la version du système d’exploitation et la version de SQL Server. La liste des images disponibles dans la galerie des machines virtuelles Azure change fréquemment.

![Image SQL azure galerie de machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Le script PowerShell suivant retourne la liste d’images Azure qui contiennent « SQL Server » dans le ImageName :

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Pour plus d’informations sur les éditions et les composants pris en charge par SQL Server, consultez les rubriques suivantes :

- [Éditions de SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Fonctionnalités prises en charge par les éditions de SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Fonctionnalités BI installées sur les Images de bibliothèque d’ordinateur virtuel de SQL Server

Le tableau suivant résume les fonctionnalités de Business Intelligence installées sur les Machine virtuelle de Microsoft Azure des images Galerie communes pour SQL Server »

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- SQL Server 2014 SP1 Standard

- Entreprise de Service Pack 2 de SQL Server 2012

- SP2 Édition Standard de SQL Server 2012

|Fonctionnalité d’analyse Décisionnelle SQL Server|Installé sur la photo de la galerie|Notes|
|---|---|---|
|**Création de rapports en Mode natif des Services**|Oui|Installé mais nécessite une configuration, y compris l’URL du Gestionnaire de rapports. Reportez-vous à la section [Configurer Reporting Services](#configure-reporting-services).|
|**Mode de SharePoint pour Reporting Services**|N°|La photo de la galerie de Machine virtuelle de Microsoft Azure n’inclut pas de SharePoint ou SharePoint fichiers d’installation. <sup>1</sup>|
|**Exploration de données Analysis Services multidimensionnels et données (OLAP)**|Oui|Installé et configuré en tant que l’instance d’Analysis Services par défaut|
|**Analysis Services sous forme de tableau**|N°|Prise en charge dans SQL Server 2012, 2014 et 2016 images, mais il n'est pas installé par défaut. Installer une autre instance d’Analysis Services. Reportez-vous à la section installer des autres Services de SQL Server et les fonctionnalités de cette rubrique.|
|**Tableau croisé dynamique de la puissance pour Analysis Services pour SharePoint**|N°|La photo de la galerie de Machine virtuelle de Microsoft Azure n’inclut pas de SharePoint ou SharePoint fichiers d’installation. <sup>1</sup>|

<sup>1</sup> pour plus d’informations sur SharePoint et les machines virtuelles Azure, consultez [Les Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) et [Le déploiement de SharePoint Microsoft Azure machines virtuelles en fonctionnement](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Exécutez la commande PowerShell suivante pour obtenir la liste des services installés qui contiennent « SQL » dans le nom du service.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recommandations générales et les meilleures pratiques

- La taille minimale recommandée pour un ordinateur virtuel est **A3** lors de l’utilisation de SQL Server Enterprise Edition. La machine virtuelle au format **A4** est recommandé pour les déploiements de BI de SQL Server Analysis Services et Reporting Services.

    Pour plus d’informations sur les tailles actuelles de la machine virtuelle, voir [Taille de Machine virtuelle pour Azure](virtual-machines-linux-sizes.md).

- Meilleure pratique pour la gestion des disques consiste à stocker les données, les journaux et les fichiers de sauvegarde sur des lecteurs autres que **C**: et **D**:. Par exemple, créer des disques de données **E**: et **F**:.

    - Le lecteur de la mise en cache de la stratégie pour le lecteur par défaut **C**: n’est pas optimal pour travailler avec des données.

    - Le **D**: lecteur est un lecteur temporaire qui est principalement utilisé pour le fichier d’échange. Le **D**: lecteur n’est pas rendue persistante et n’est pas enregistré dans le stockage blob. Tâches de gestion comme une modification de la taille de la machine virtuelle réinitialiser le **D**: lecteur. Il est recommandé de **pas** utiliser le **D**: lecteur pour les fichiers de base de données, y compris de tempdb.

    Pour plus d’informations sur la création et la connexion de disques, voir [comment attacher un disque de données à une Machine virtuelle](virtual-machines-windows-classic-attach-disk.md).

- Arrêter ou désinstaller les services que vous n’envisagez pas d’utiliser. Par exemple si l’ordinateur virtuel est uniquement utilisé pour Reporting Services, arrêter ou désinstallez Analysis Services et SQL Server Integration Services. L’image suivante est un exemple des services qui sont démarrés par défaut.

    ![Services SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] Le moteur de base de données SQL Server est requis dans les scénarios d’analyse Décisionnelle pris en charge. Dans un topologie de machine virtuelle de serveur unique, le moteur de base de données doit être en cours d’exécution sur l’ordinateur virtuel même.

    Pour plus d’informations, consultez la rubrique suivante : [Désinstallez les Services de création de rapports](https://msdn.microsoft.com/library/hh479745.aspx) et de [désinstaller une Instance d’Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Vérifier la **Mise à jour de Windows** nouvelle « mises à jour importantes ». Les images de Machine virtuelle de Microsoft Azure sont actualisés fréquemment ; Toutefois les mises à jour importantes pourraient devenir disponibles à partir de **Windows Update** après la dernière actualisation de l’image de machine virtuelle.

## <a name="example-deployment-topologies"></a>Exemples de Topologies de déploiement

Vous trouverez ci-dessous des exemples de déploiement qui utilisent des ordinateurs virtuels de Microsoft Azure. Les topologies dans ces diagrammes sont uniquement les topologies possibles que vous pouvez utiliser avec les fonctionnalités d’analyse Décisionnelle de SQL Server et les ordinateurs virtuels de Microsoft Azure.

### <a name="single-virtual-machine"></a>Machine virtuelle unique

Analysis Services, Reporting Services, moteur de base de données SQL Server et sources de données sur une seule machine virtuelle.

![scénario d’IAS BI avec les ordinateur virtuel 1](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Deux ordinateurs virtuels

- Analysis Services, Reporting Services et le moteur de base de données SQL Server sur une seule machine virtuelle. Ce déploiement comprend les bases de données de serveur de rapports.

- Sources de données sur un ordinateur virtuel deuxième. La machine virtuelle deuxième inclut le moteur de base de données SQL Server comme source de données.

![scénario de BI iaas avec 2 machines virtuelles](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Mixte Azure – données sur la base de données d’Azure SQL

- Analysis Services, Reporting Services et le moteur de base de données SQL Server sur une seule machine virtuelle. Ce déploiement comprend les bases de données de serveur de rapports.

- Source de données est la base de données de SQL d’Azure.

![machine virtuelle de scénarios iaas BI et AzureSQL en tant que source de données](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybride – données sur site

- Dans cet exemple de déploiement Analysis Services, Reporting Services et le moteur de base de données SQL Server s’exécutent sur une seule machine virtuelle. L’ordinateur virtuel héberge les bases de données de serveur de rapports. L’ordinateur virtuel est joint à un domaine sur site via le réseau virtuel Azure, ou certaines autre tunneling de solution VPN.

- Source de données est sur site.

![machine virtuelle de scénarios iaas BI et sur les sources de données de site](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuration en Mode natif des Services de création de rapports

La photo de la galerie de machine virtuelle pour SQL Server inclut le mode natif des Services de Reporting installé, mais le serveur de rapports n’est pas configuré. Les étapes de cette section Configurer le serveur de rapports Reporting Services. Pour obtenir des informations plus détaillées sur la configuration de mode natif des Services de création de rapports, consultez [Installer Reporting Services Native Mode rapport serveur (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Pour un contenu similaire qui utilise des scripts de Windows PowerShell pour configurer le serveur de rapports, voir [Utiliser PowerShell créer un Azure VM avec un serveur en Mode natif rapport](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Se connecter à l’ordinateur virtuel et de démarrer le Gestionnaire de Configuration Reporting Services

Il existe deux flux de travail communs pour la connexion à un ordinateur virtuel de Azure :

- Se connecter dans la barre d’outils, cliquez sur le nom de l’ordinateur virtuel, puis cliquez sur **se connecter**. Une connexion Bureau à distance s’ouvre et le nom de l’ordinateur est automatiquement rempli.

    ![se connecter à des machines virtuelles azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Se connecter à l’ordinateur virtuel avec connexion Bureau à distance de Windows. Dans l’interface utilisateur du Bureau à distance :

    1. Tapez le **nom de service de cloud** comme nom d’ordinateur.

    1. Tapez le signe deux-points ( :) et le numéro de port public est configuré pour TCP bureau point de terminaison distant.

        Myservice.cloudapp.NET:63133

        Pour plus d’informations, consultez [ce qu’est un service en nuage ?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Démarrer le Gestionnaire de Configuration des Services de création de rapports.**

1. Dans **Windows Server 2012**:

1. À partir de l’écran de **démarrage** , tapez **Reporting Services** pour afficher la liste des applications.

1. Cliquez sur **Gestionnaire de Configuration de Reporting Services** , puis cliquez sur **Exécuter en tant qu’administrateur**.

1. Dans **Windows Server 2008 R2**:

1. Cliquez sur **Démarrer**, puis cliquez sur **Tous les programmes**.

1. Cliquez sur **Microsoft SQL Server 2016**.

1. Cliquez sur **Outils de Configuration**.

1. Cliquez sur **Gestionnaire de Configuration de Reporting Services** , puis cliquez sur **Exécuter en tant qu’administrateur**.

Ou

1. Cliquez sur **Démarrer**.

1. Dans la boîte de dialogue **Rechercher les programmes et les fichiers de** type **reporting services**. Si l’ordinateur virtuel est en cours d’exécution Windows Server 2012, tapez sur l’écran de démarrage de Windows Server 2012 **reporting services** .

1. Cliquez sur **Gestionnaire de Configuration de Reporting Services** , puis cliquez sur **Exécuter en tant qu’administrateur**.

    ![recherche du Gestionnaire de configuration de ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurer Reporting Services

**Compte de service et l’URL du service web :**

1. Vérifiez que le **Nom du serveur** est le nom du serveur local et cliquez sur **se connecter**.

1. Notez le **Nom de base de données de serveur de rapports**. La base de données est créé lorsque la configuration est terminée.

1. Vérifiez que l' **État de Report Server** est **démarré**. Si vous souhaitez vérifier que le service dans le Gestionnaire de serveur Windows, le service est le Service Windows de **SQL Server Reporting Services** .

1. Cliquez sur le **Compte de Service** et de modifier le compte, le cas échéant. Si l’ordinateur virtuel est utilisé dans un environnement hors domaine, le compte intégré **ReportServer** est suffisant. Pour plus d’informations sur le compte de service, reportez-vous à la section [Compte de Service](https://msdn.microsoft.com/library/ms189964.aspx).

1. Dans le volet gauche, cliquez sur **URL du Service Web** .

1. Cliquez sur **Appliquer** pour configurer les valeurs par défaut.

1. Notez les **URL de Service Web serveur rapport**. Notez que le port TCP par défaut est de 80 et fait partie de l’URL. Dans une étape ultérieure, vous créez un point de terminaison Microsoft Azure Machine virtuelle pour le port.

1. Dans le volet de **résultats** , vérifiez que les actions s’est terminées correctement.

**Base de données :**

1. Dans le volet gauche, cliquez sur **base de données** .

1. Cliquez sur **Modifier base de données**.

1. Assurez-vous de **que créer une nouvelle base de données de serveur de rapport** est activée et puis cliquez sur Suivant.

1. Vérifiez le **Nom du serveur** , puis cliquez sur **Tester la connexion**.

1. Si le résultat est le **Test de connexion a réussi**, cliquez sur **OK** , puis sur **suivant**.

1. Notez le nom de la base de données est **ReportServer** et le **mode du serveur de rapports** est **natif** , puis cliquez sur **suivant**.

1. Sur la page **d’informations d’identification** , cliquez sur **suivant** .

1. Dans la page **Résumé** , cliquez sur **suivant** .

1. Sur la page de **progression et fin** , cliquez sur **suivant** .

**Web l’URL du portail, ou le Gestionnaire de rapports pour 2012 et 2014 :**

1. Cliquez sur **URL du portail Web**, ou **URL du Gestionnaire de rapports** 2014 et 2012, dans le volet gauche.

1. Cliquez sur **Appliquer**.

1. Dans le volet de **résultats** , vérifiez que les actions s’est terminées correctement.

1. Cliquez sur **Quitter**.

Pour plus d’informations sur les autorisations de serveur de rapports, consultez [Octroi d’autorisations sur un serveur de rapports en Mode natif](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Accédez au Gestionnaire de rapport local

Pour vérifier la configuration, accédez au Gestionnaire de rapports sur l’ordinateur virtuel.

1. Sur l’ordinateur virtuel, démarrez Internet Explorer avec des privilèges d’administrateur.

1. Accédez à http://localhost/reports sur l’ordinateur virtuel.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Pour se connecter au portail web à distance, ou le Gestionnaire de rapports pour 2014 et 2012

Si vous souhaitez vous connecter au portail web, ou le Gestionnaire de rapports pour 2014 et 2012, sur l’ordinateur virtuel à partir d’un ordinateur distant, créez une nouvelle machine virtuelle point de terminaison TCP. Par défaut, le serveur de rapports est à l’écoute pour les demandes HTTP sur le **port 80**. Si vous configurez les URL du serveur de rapport pour utiliser un port différent, vous devez spécifier ce numéro de port dans les instructions suivantes.

1. Créer un point de terminaison pour l’ordinateur virtuel du Port TCP 80. Pour plus d’informations, consultez la section [points de terminaison de Machine virtuelle et les Ports de pare-feu](#virtual-machine-endpoints-and-firewall-ports) dans ce document.

1. Ouvrir le port 80 dans le pare-feu de l’ordinateur virtuel.

1. Accédez au portail web, ou le Gestionnaire de rapports, à l’aide d’Azure Virtual Machine **Nom DNS** comme nom de serveur dans l’URL. Par exemple :

    **Serveur de rapports**: http://uebi.cloudapp.net/reportserver  **portail Web**: http://uebi.cloudapp.net/reports

    [Configurer un pare-feu pour accéder au serveur de rapports](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Pour créer et publier des rapports sur l’ordinateur virtuel Azure

Le tableau suivant résume quelques-unes des options disponibles pour publier des rapports existants à partir d’un ordinateur local vers le serveur de rapports hébergé sur la Machine virtuelle de Microsoft Azure :

- **Générateur de rapports**: l’ordinateur virtuel inclut le clic-une fois la version de Microsoft SQL Server Générateur de rapports de SQL 2014 et 2012. Pour démarrer le temps de générateur le premier rapport sur la machine virtuelle avec SQL 2016 :

    1. Démarrez votre navigateur avec des privilèges d’administrateur.

    1. Accédez au portail web, sur l’ordinateur virtuel et sélectionnez l’icône de **téléchargement** dans le coin supérieur droit.
    
    1. Sélectionnez le **Générateur de rapports**.

    Pour plus d’informations, reportez-vous à la section [Démarrer le Générateur de rapports](https://msdn.microsoft.com/library/ms159221.aspx).

- **Outils de données de SQL Server**: machine virtuelle : outils de données de SQL Server est installé sur l’ordinateur virtuel et peut être utilisé pour créer des **Projets de serveur de rapports** et les rapports sur l’ordinateur virtuel. Outils de données de SQL Server peuvent les publier sur le serveur de rapports sur l’ordinateur virtuel.

- **Outils de données de SQL Server : distant**: sur votre ordinateur local, créez un projet de Reporting Services dans les outils de données de SQL Server qui contient les rapports Reporting Services. Configurez le projet pour se connecter à l’URL du service web.

    ![ssdt des propriétés de projet pour le projet SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Créer un. Disque dur virtuel disque dur qui contient des rapports, puis télécharger et connectez le disque.

    1. Créer un. Disque dur de disque dur virtuel sur votre ordinateur local qui contient vos rapports.

    1. Créez et installez un certificat de gestion.

    1. Téléchargez le fichier de disque dur virtuel vers Azure à l’aide de l’applet de commande Add-AzureVHD [créer et télécharger un disque dur virtuel du serveur de Windows Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Connectez le disque à l’ordinateur virtuel.

## <a name="install-other-sql-server-services-and-features"></a>Installer d’autres Services de SQL Server et les fonctionnalités

Pour installer des services supplémentaires de SQL Server, tels que les Services d’analyse sous forme de tableau, exécutez l’Assistant Installation de SQL server. Les fichiers d’installation sont sur le disque local de la machine virtuelle.

1. Cliquez sur **Démarrer** , puis sur **Tous les programmes**.

1. Cliquez sur **Microsoft SQL Server 2016**, **2014 de Microsoft SQL Server** ou **Microsoft SQL Server 2012** , puis sur **Outils de Configuration**.

1. Cliquez sur **Centre d’Installation SQL Server**.

Ou d’exécuter C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] La première fois que vous exécutez le programme d’installation de SQL Server, plusieurs fichiers de configuration peuvent être téléchargés et requièrent un redémarrage de l’ordinateur virtuel et un redémarrage du programme d’installation de SQL Server.
>
>Si vous avez besoin personnaliser de plusieurs fois l’image sélectionnée à partir de la Machine virtuelle de Microsoft Azure, envisagez de créer votre propre image de SQL Server. Fonctionnalités de SysPrep de Services d’analyse a été activée avec SQL Server 2012 SP1 CU2. Pour plus d’informations, consultez [Considérations sur l’installation des de SQL Server à l’aide de SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) et la [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="to-install-analysis-services-tabular-mode"></a>Pour installer Analysis Services en Mode tabulaire

La procédure décrite dans cette section **résument** l’installation du mode tabulaire de Analysis Services. Pour plus d’informations, consultez les rubriques suivantes :

- [Installez Analysis Services sous forme de tableau](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modélisation sous forme de tableau (didacticiel de Works Adventure)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Pour installer Analysis Services en Mode tabulaire :**

1. Dans l’Assistant d’installation de SQL Server, cliquez sur **Installation** dans le volet gauche, puis cliquez sur **installation autonome de nouveau SQL server ou ajout de fonctionnalités à une installation existante**.

    - Si vous voyez le **Rechercher un dossier**, accédez à c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full, puis sur **Ok**.

1. Sur la page mises à jour de produit, cliquez sur **suivant** .

1. Dans la page **Type d’Installation** , sélectionnez **effectuer une nouvelle installation de SQL Server** , cliquez sur **suivant**.

1. Dans la page **Rôle du programme d’installation** , cliquez sur **Installation de fonctionnalités de SQL Server**.

1. Dans la page **Sélection de composant** , cliquez sur **Analysis Services**.

1. Dans la page **Configuration de l’Instance** , tapez un nom descriptif, comme **tabulaire** dans les zones de texte **Nommé l’Instance** et **l’Id de l’Instance** .

1. Dans la page **Configuration d’Analysis Services** , sélectionnez le **Mode sous forme de tableau**. Ajoutez l’utilisateur actuel à la liste d’autorisations d’administration.

1. Terminer et fermer l’Assistant d’installation de SQL Server.

## <a name="analysis-services-configuration"></a>Configuration d’Analysis Services

### <a name="remote-access-to-analysis-services-server"></a>Accès à distance à un serveur Analysis Services

Le serveur Analysis Services prend en charge uniquement l’authentification windows. Pour accéder à distance à Analysis Services à partir d’applications clientes telles que SQL Server Management Studio ou les outils de données de SQL Server, l’ordinateur virtuel doit être joint à votre domaine local, à l’aide de la mise en réseau virtuel d’Azure. Pour plus d’informations, voir [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md).

Une **instance par défaut** d’Analysis Services est à l’écoute sur TCP port **2383**. Ouvrir le port dans le pare-feu de machines virtuelles. Une instance nommée en clusters des Services d’analyse sont également écoute sur le port **2383**.

Pour une **instance nommée** d’Analysis Services, le service SQL Server Browser est requis pour gérer l’accès de port. Le navigateur de SQL Server est par défaut le port **2382**.

Dans le pare-feu de machines virtuelles, ouvrez le port **2382** et créer un static Analysis Services est appelé port de l’instance.

1. Pour vérifier les ports qui sont déjà en cours d’utilisation sur la machine virtuelle et quel processus utilise les ports, exécutez la commande suivante avec des privilèges d’administrateur :

        netstat /ao

1. Utilisez SQL Server Management Studio pour créer un static Analysis Services nommé port de l’instance en mettant à jour « Port » sous forme de tableau comme valeur des propriétés générales de l’instance. Pour plus d’informations, consultez la « utiliser un port fixe pour une valeur par défaut ou instance nommée » dans [configurer le pare-feu Windows pour autoriser l’accès aux Services d’analyse](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Redémarrez l’instance de tableau du service Analysis Services.

Pour plus d’informations, consultez la section **points de terminaison de Machine virtuelle et les Ports de pare-feu** dans ce document.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Points de terminaison de Machine virtuelle et les Ports de pare-feu

Cette section résume les points de terminaison de Machine virtuelle Microsoft Azure pour créer et les ports à ouvrir dans le pare-feu de l’ordinateur virtuel. Le tableau suivant résume les ports **TCP** pour créer des points de terminaison pour et les ports à ouvrir sur le pare-feu des ordinateurs virtuels.

- Si vous utilisez un ordinateur virtuel unique et les deux éléments suivants sont remplies, vous n’avez pas besoin de créer des points de terminaison de machine virtuelle et que vous n’avez pas besoin d’ouvrir les ports dans le pare-feu sur l’ordinateur virtuel.

    - Vous ne connectez pas à distance aux fonctionnalités de SQL Server sur l’ordinateur virtuel. Établissement d’une connexion Bureau à distance de la machine virtuelle et l’accès aux fonctions de SQL Server localement sur l’ordinateur virtuel ne sont pas considérée comme une connexion à distance aux fonctionnalités de SQL Server.

    - Vous ne joignez pas l’ordinateur virtuel à un domaine sur site via le réseau virtuel Azure ou une autre solution de tunneling VPN.

- Si l’ordinateur virtuel n’est pas joint à un domaine mais que vous souhaitez à distance à se connecter aux fonctionnalités de SQL Server sur une machine virtuelle :

    - Ouvrir les ports dans le pare-feu sur l’ordinateur virtuel.

    - Créer un ordinateur virtuel de points de terminaison pour les ports indiqués (*).

- Si l’ordinateur virtuel est joint à un domaine à l’aide d’un tunnel VPN, tels que la mise en réseau virtuel d’Azure, les points de terminaison ne sont pas nécessaires. Toutefois, ouvrez les ports dans le pare-feu sur l’ordinateur virtuel.

  	|Port|Type de|Description|
|---|---|---|
|**80**|TCP|Serveur de rapports distant accès (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP|Navigateur de SQL Server. Cela est nécessaire lorsque la machine virtuelle dans joint à un domaine.|
|**2382**|TCP|Navigateur de SQL Server.|
|**2383**|TCP|Instance par défaut de SQL Server Analysis Services et instances nommées ordonnées en clusters.|
|**Défini par l’utilisateur**|TCP|Créer un static Analysis Services nommé port de l’instance pour un numéro de port que vous choisissez, puis débloquez le numéro de port dans le pare-feu.|

Pour plus d’informations sur la création de points de terminaison, consultez les rubriques suivantes :

- Créer des points de terminaison :[comment configurer des points de terminaison à une Machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md).

- De SQL Server : Consultez la section « Étapes de Configuration complète pour se connecter à l’ordinateur virtuel à l’aide de SQL Server Management Studio » [d’une machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

Le diagramme suivant illustre les ports à ouvrir dans le pare-feu de l’ordinateur virtuel pour permettre l’accès à distance à des fonctionnalités et des composants sur l’ordinateur virtuel.

![ports à ouvrir pour les applications bi dans Azure VM](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Ressources

- Passez en revue la politique de support des logiciels de serveur de Microsoft utilisé dans l’environnement virtuel Azure. La rubrique suivante récapitule la prise en charge des fonctionnalités de BitLocker, le Clustering avec basculement et équilibrage de charge réseau. [Prend en charge les logiciels serveurs de Microsoft pour Azure Virtual Machines](http://support.microsoft.com/kb/2721672).

- [SQL Server sur des Machines virtuelles Azure présentation](virtual-machines-windows-sql-server-iaas-overview.md)

- [Ordinateurs virtuels](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Une machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [Comment attacher un disque de données à une Machine virtuelle](virtual-machines-windows-classic-attach-disk.md)

- [La migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md)

- [Déterminer le Mode de serveur d’une Instance Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modélisation multidimensionnelle (didacticiel de Works Adventure)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centre de Documentation Azure](https://azure.microsoft.com/documentation/)

- [À l’aide de BI de puissance dans un environnement hybride](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Envoyer des commentaires et des informations de contact via la connexion de Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenu de la Communauté

- [Gestion de base de données SQL Azure avec PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
