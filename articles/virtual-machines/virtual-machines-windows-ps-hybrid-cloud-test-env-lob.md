<properties 
    pageTitle="Environnement de test d’application métier | Microsoft Azure" 
    description="Apprenez à créer une ligne basée sur le web, d’applications d’entreprise dans un environnement de cloud hybride pour IT pro ou tests de développement." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurer une application web métier dans un nuage hybride pour le test

Cette rubrique vous guide dans le processus de création d’un environnement de cloud hybride simulée pour le test d’une ligne sur le web d’une application sectorielle (LOB) hébergée dans Microsoft Azure. Voici la configuration obtenue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Cette configuration se compose de :

- Un réseau simulé sur site hébergé dans Azure (le TestLab VNet).
- Un réseau virtuel coexistence hébergé dans Azure (TestVNET).
- Une connexion VPN de VNet-à-VNet.
- Un serveur web métier, SQL server et contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun à partir de laquelle vous pouvez :

- Développer et tester les applications professionnelles hébergées sur Internet Information Services (IIS) avec un serveur de base de données SQL Server 2014 principal dans Azure.
- Réaliser des tests de cette charge de travail simulée hybride en nuage informatique.

Il existe trois grandes phases à configurer cet environnement de test de cloud hybride :

1.  Permet de paramétrer l’environnement de cloud hybride simulé.
2.  Configurer l’ordinateur du serveur SQL (SQL1).
3.  Configurer le serveur métier (LOB1).

Cette charge de travail requiert un abonnement Azure. Si vous avez un abonnement MSDN ou Visual Studio, reportez-vous à la section [crédit mensuel Azure pour les abonnés de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Pour obtenir un exemple d’une application cœur de métier hébergée dans Azure de production, consultez le plan d’architecture **des applications professionnelles** à des [diagrammes d’Architecture de logiciels de Microsoft et de modèles](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Phase 1 : Définir l’environnement de cloud hybride simulé

Créer la [simulation d’environnement de test de cloud hybride](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Dans la mesure où cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau du réseau d’entreprise, vous pouvez l’arrêter pour le moment.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Phase 2 : Configurer l’ordinateur du serveur SQL (SQL1)

À partir du portail Azure, démarrez l’ordinateur de DC2 si nécessaire.

Ensuite, créez un ordinateur virtuel pour SQL1 avec les commandes suivantes à une invite de commandes PowerShell d’Azure sur votre ordinateur local. Avant d’exécuter ces commandes, remplir les valeurs de variable et de supprimer les caractères < et >.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Le portail Azure permet de se connecter à SQL1 en utilisant le compte administrateur local de SQL1.

Ensuite, configurez les règles du pare-feu Windows pour autoriser le trafic de SQL Server et de test de la connectivité de base. À partir d’une invite de commandes Windows PowerShell au niveau de l’administrateur sur le serveur SQL1, exécuter ces commandes.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 192.168.0.4.

Ensuite, ajoutez le disque de données supplémentaires sur le serveur SQL1 sous la forme d’un nouveau volume avec la lettre de lecteur F:.

1.  Dans le volet gauche du Gestionnaire de serveur, cliquez sur **fichier et Services de stockage**, puis cliquez sur **les disques**.
2.  Dans le volet de contenu, dans le groupe de **disques** , cliquez sur **le disque 2** (avec la **Partition de** valeur **inconnue**).
3.  Cliquez sur **tâches**, puis cliquez sur **Nouveau Volume**.
4.  Sur l’avant vous commencer de la page de l’Assistant Nouveau Volume, cliquez sur **suivant**.
5.  Sur la page Sélectionner le serveur et le disque, cliquez sur le **disque 2**, puis cliquez sur **suivant**. Lorsque vous y êtes invité, cliquez sur **OK**.
6.  Dans la zone spécifier la taille de la page de volume, cliquez sur **suivant**.
7.  Sur l’attribuer à une page de lettre ou d’un dossier du lecteur, cliquez sur **suivant**.
8.  Dans la page Paramètres de système de fichier, cliquez sur **suivant**.
9.  Dans la page Confirmer les sélections, cliquez sur **créer**.
10. Lorsque vous avez terminé, cliquez sur **Fermer**.

Exécuter ces commandes à l’invite de commande Windows PowerShell sur SQL1 :

    md f:\Data
    md f:\Log
    md f:\Backup

Ensuite, joindre le domaine CORP Windows Server Active Directory avec les commandes suivantes à l’invite de Windows PowerShell sur SQL1 SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Utiliser le compte de CORP\User1 lorsque vous êtes invité à fournir des informations d’identification du compte de domaine pour la commande **Ajouter à un ordinateur** .

Après le redémarrage, utilisez le portail Azure pour se connecter à SQL1 *avec le compte administrateur local de SQL1*.

Ensuite, configurez 2014 de SQL Server pour utiliser le lecteur F: pour les nouvelles bases de données et pour les autorisations de compte d’utilisateur.

1.  À partir de l’écran de démarrage, tapez **Gestion de SQL Server**, puis cliquez sur **SQL Server 2014 Management Studio**.
2.  Dans **connexion à un serveur**, cliquez sur **se connecter**.
3.  Dans le volet d’arborescence de l’Explorateur d’objets, cliquez sur **SQL1**et puis cliquez sur **Propriétés**.
4.  Dans la fenêtre **Propriétés du serveur** , cliquez sur **Paramètres de la base de données**.
5.  Repérez les **emplacements de base de données par défaut** et définir ces valeurs : 
    - Pour les **données**, tapez le chemin d’accès **f:\Data**.
    - Pour le **journal**, tapez le chemin d’accès **f:\Log**.
    - Pour la **sauvegarde**, tapez le chemin d’accès **f:\Backup**.
    - Remarque : Seules les nouvelles bases de données utilisent ces emplacements.
6.  Cliquez sur **OK** pour fermer la fenêtre.
7.  Dans le volet d’arborescence de **l’Explorateur d’objets** , ouvrez **sécurité**.
8.  Cliquez droit sur les **connexions d’accès** , puis sur **Nouvelle connexion**.
9.  Dans la zone **nom**, tapez **CORP\User1**.
10. Dans la page **Rôles de serveur** , cliquez sur **sysadmin**, puis cliquez sur **OK**.
11. Fermez Microsoft SQL Server Management Studio.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Phase 3 : Configurer le serveur métier (LOB1)

Tout d’abord, créez un ordinateur virtuel pour LOB1 avec les commandes suivantes à l’invite de commande PowerShell de Azure sur votre ordinateur local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, utilisez le portail Azure pour se connecter à LOB1 avec les informations d’identification du compte administrateur local de LOB1.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d’une invite de commandes au niveau de l’administrateur de Windows PowerShell sur LOB1, exécuter ces commandes.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 192.168.0.4.

Joignez la table LOB1 pour le domaine CORP Active Directory avec les commandes suivantes à l’invite de Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Utiliser le compte de CORP\User1 lorsque vous êtes invité à fournir des informations d’identification du compte de domaine pour la commande **Ajouter à un ordinateur** .

Après le redémarrage, utilisez le portail Azure pour se connecter à LOB1 avec le compte de le CORP\User1 et le mot de passe.

Ensuite, configurez LOB1 pour IIS et tester l’accès à partir de CLIENT1.

1.  Dans le Gestionnaire de serveur, cliquez sur **Ajouter des rôles et fonctionnalités**.
2.  Sur la page **avant de commencer** , cliquez sur **suivant**.
3.  Dans la page **Sélectionner le type d’installation** , cliquez sur **suivant**.
4.  Dans la page **Sélectionner le serveur de destination** , cliquez sur **suivant**.
5.  Dans la page **rôles de serveur** , cliquez sur **Serveur Web (IIS)** dans la liste des **rôles**.
6.  Lorsque vous y êtes invité, cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **suivant**.
7.  Dans la page **Sélectionner les fonctionnalités** , cliquez sur **suivant**.
8.  Sur la page du **Serveur Web (IIS)** , cliquez sur **suivant**.
9.  Dans la page **Sélectionner les services de rôle** , activez ou désactivez les cases à cocher pour les services que vous avez besoin pour test de votre application cœur de métier et puis cliquez sur **suivant**.
10. Dans la page des **sélections pour confirmer l’installation** , cliquez sur **installer**.
11. Attendre la fin de l’installation des composants, puis sur **Fermer**.
12. À partir du portail Azure, se connecter à l’ordinateur CLIENT1 avec les informations d’identification du compte CORP\User1, puis démarrez Internet Explorer.
13. Dans la barre d’adresses, tapez **http://lob1/** et appuyez sur ENTRÉE. Vous devez voir la page web de IIS 8 par défaut.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Cet environnement est maintenant prêt à déployer votre application web basée sur LOB1 et testez les fonctionnalités à partir de CLIENT1 sur le sous-réseau du réseau d’entreprise.

## <a name="next-step"></a>Étape suivante

- Ajouter une nouvelle machine virtuelle via le [portail Azure](virtual-machines-windows-hero-tutorial.md).
