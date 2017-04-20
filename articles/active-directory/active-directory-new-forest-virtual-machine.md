<properties
    pageTitle="Installer une forêt Active Directory sur un réseau virtuel Azure | Microsoft Azure"
    description="Didacticiel qui explique comment créer une nouvelle forêt Active Directory sur une machine virtuelle (VM) sur un réseau virtuel d’Azure."
    services="active-directory, virtual-network"
    keywords="ordinateur virtuel Active directory, forêt active directory installation vidéos d’annuaire azure "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure

Cette rubrique montre comment créer un environnement Active Directory de Windows Server sur un réseau virtuel Azure sur une machine virtuelle (VM) sur un [réseau de virtuel Azure](../virtual-network/virtual-networks-overview.md). Dans ce cas, le réseau virtuel Azure n’est pas connecté à un réseau local.

Pourraient également vous intéresser dans ces rubriques connexes :

- Pour une vidéo qui illustre ces étapes, reportez-vous [à installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Vous pouvez éventuellement [configurer un VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) et puis installez une nouvelle forêt ou étendre une forêt local à un réseau virtuel Azure. Pour connaître ces étapes, consultez [installer un contrôleur de domaine réplica Active Directory dans un réseau virtuel d’Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Pour obtenir des instructions sur l’installation des Services de domaine Active Directory (AD DS) sur un réseau virtuel Azure conceptuelles, consultez [instructions pour déployer Windows Server Active Directory Azure machines virtuelles en fonctionnement](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagramme de scénario

Dans ce scénario, les utilisateurs externes ont besoin d’accéder aux applications qui s’exécutent sur des serveurs à un domaine. Les ordinateurs virtuels qui s’exécutent les serveurs d’applications et les ordinateurs virtuels qui s’exécutent de contrôleurs de domaine sont installés installés dans leur propre service de cloud dans un réseau virtuel Azure. Ils sont également inclus dans une disponibilité pour une meilleure tolérance.

![Forêt de Active Directory sur un ordinateurs virtuels dans le réseau virtuel d’Azure ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>En quoi il diffère sur site ?

Il n’est pas vraiment la différence entre l’installation d’un contrôleur de domaine sur Azure et sur site. Les principales différences sont répertoriés dans le tableau suivant.

Pour configurer...  | Sur site  | Azure réseau virtuel
------------- | -------------  | ------------
**Adresse IP du contrôleur de domaine**  | Attribuez une adresse IP statique sur les propriétés de la carte réseau   | Exécutez l’applet de commande Set-AzureStaticVNetIP pour attribuer une adresse IP statique
**Résolution de client DNS**  | Définir adresse de serveur par défaut et l’autre serveur DNS sur le réseau à propriétés de la carte des membres du domaine   | Définir l’adresse de serveur DNS dans les propriétés du réseau virtuel
**Stockage de base de données Active Directory**  | Vous pouvez également modifier l’emplacement de stockage par défaut à partir de C:\  | Vous devez modifier l’emplacement de stockage par défaut à partir de C:\



## <a name="create-an-azure-virtual-network"></a>Créer un réseau virtuel Azure

1. Connectez-vous au portail Azure classique.
2. Créer un réseau virtuel. Cliquez sur **réseaux** > **créer un réseau virtuel**. Utilisez les valeurs dans le tableau suivant pour terminer l’Assistant.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Détails du réseau virtuel**  | <p>Nom : Entrez un nom pour votre réseau virtuel</p><p>Région : Choisissez la région la plus proche</p>
    **DNS et VPN**  | <p>Laissez le serveur DNS vide</p><p>Ne sélectionnez pas l’option VPN</p>
    **Espaces d’adressage de réseau virtuel**  | <p>Nom du sous-réseau : Saisissez un nom pour votre sous-réseau.</p><p>Adresse IP de début : <b>10.0.0.0</b></p><p>CIDR : <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Créer des ordinateurs virtuels pour exécuter le contrôleur de domaine et les rôles des serveurs DNS

Répétez les étapes suivantes pour créer des ordinateurs virtuels pour héberger le rôle de contrôleur de domaine en fonction des besoins. Vous devez déployer au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de pannes. Si le réseau virtuel Azure comprend au moins deux contrôleurs de domaine qui sont configurés de la même façon (c'est-à-dire qu’ils sont les deux catalogues globaux, exécution serveur DNS, et ni détient un rôle FSMO et ainsi de suite) puis placer les ordinateurs virtuels qui s’exécutent les contrôleurs de domaine dans une disponibilité pour une meilleure tolérance.

Pour créer les ordinateurs virtuels au lieu de l’interface utilisateur à l’aide de Windows PowerShell, voir [Utilisation des PowerShell de Azure permet de créer et de préconfigurer des Machines virtuelles basées sur Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Dans le portail classique, cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **De la galerie**. Utilisez les valeurs suivantes pour terminer l’Assistant. Accepter la valeur par défaut pour un paramètre, sauf si une autre valeur suggérée ou requis.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Choisir une Image**  | Windows Server 2012 R2 Datacenter
    **Configuration de Machine virtuelle**  | <p>Nom de la Machine virtuelle : Tapez un nom unique (par exemple, AzureDC1).</p><p>Nouveau nom d’utilisateur : Tapez le nom d’un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur l’ordinateur virtuel. Vous aurez besoin de ce nom pour se connecter à la machine virtuelle pour la première fois. Le compte administrateur intégré ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmer : Tapez un mot de passe</p>
    **Configuration de Machine virtuelle**  | <p>Service de cloud : Choisissez de <b>créer un nouveau service en nuage</b> pour la machine virtuelle de premier et sélectionnez ce nom de service cloud même lorsque vous créez plusieurs machines virtuelles qui hébergeront le rôle de contrôleur de domaine.</p><p>Nom DNS de Service cloud : Spécifier un nom globalement unique</p><p>Région/groupe d’affinité/réseau virtuel : spécifiez le nom de réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez <b>d’utiliser un compte de stockage généré automatiquement</b> pour la machine virtuelle de premier et puis sélectionnez ce nom de compte de stockage même lorsque vous créez plusieurs machines virtuelles qui hébergeront le rôle de contrôleur de domaine.</p><p>Ensemble de disponibilité : Choisissez de <b>créer un jeu de disponibilité</b>.</p><p>Nom du jeu de disponibilité : tapez un nom pour la disponibilité d’ensemble lorsque vous créez la première VM et sélectionnez ensuite que même nom lorsque vous créez davantage d’ordinateurs virtuels.</p>
    **Configuration de Machine virtuelle**  | <p>Sélectionnez <b>l’installation de l’Agent de la machine virtuelle</b> et toutes les autres extensions dont vous avez besoin.</p>
2. Connectez un disque à chaque machine virtuelle qui exécutera le rôle de serveur contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple, 10 Go) et laissez la **Préférence de Cache hôte** défini sur **Aucun**. Pour la procédure, voir [comment attacher un disque de données pour une Machine virtuelle de Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Une fois que vous tout d’abord vous connecter à l’ordinateur virtuel, ouvrez le **Gestionnaire de serveur** > **de fichiers et Services de stockage** pour créer un volume sur ce disque à l’aide de NTFS.
4. Réserver une adresse IP statique pour les machines virtuelles qui exécuteront le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez Microsoft Web Platform Installer et [installer PowerShell d’Azure](../powershell-install-configure.md) et d’exécuter l’applet de commande Set-AzureStaticVNetIP. Par exemple :

    ' Get-AzureVM - ServiceName AzureDC1-nom AzureDC1 | Set-AzureStaticVNetIP - adresse IP 10.0.0.4 | Mise à jour-AzureVM

Pour plus d’informations sur la définition d’une adresse IP statique, consultez [configurer une adresse IP interne statique pour une machine virtuelle](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Installer Active Directory de Windows Server

Utilisez la même routine pour [installer AD DS](https://technet.microsoft.com/library/jj574166.aspx) que vous utilisez sur place (autrement dit, vous pouvez utiliser l’interface utilisateur, un fichier de réponses ou de Windows PowerShell). Vous devez fournir les informations d’identification d’administrateur pour installer une nouvelle forêt. Pour spécifier l’emplacement de la base de données Active Directory, les journaux et SYSVOL, modifier l’emplacement de stockage par défaut à partir du lecteur de système d’exploitation sur le disque de données supplémentaires que vous avez associé à la machine virtuelle.

Après la fin de l’installation du contrôleur de domaine, vous reconnecter à l’ordinateur virtuel et ouvrez une session sur le contrôleur de domaine. N’oubliez pas de spécifier les informations d’identification de domaine.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Réinitialiser le serveur DNS pour le réseau virtuel Azure

1. Réinitialiser le paramétrage d’un redirecteur DNS sur le nouveau contrôleur de domaine/serveur DNS.
  1. Dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS**.
  2. Dans le **Gestionnaire DNS**, cliquez sur le nom du serveur DNS, puis cliquez sur **Propriétés**.
  3. Sous l’onglet **redirecteurs** , cliquez sur l’adresse IP du redirecteur, puis cliquez sur **Modifier**.  Sélectionnez l’adresse IP et cliquez sur **Supprimer**.
  4. Cliquez sur **OK** pour fermer l’éditeur et sur **Ok** pour fermer les propriétés du serveur DNS.
2. Mettre à jour le paramètre de serveur DNS pour le réseau virtuel.
  1. Cliquez sur **Réseaux virtuels** > Double-cliquez sur le réseau virtuel que vous avez créée > **configurer** > **serveurs DNS**, tapez le nom et le DIP de l’un des ordinateurs virtuels qui exécute le rôle serveur de contrôleur de domaine/DNS et cliquez sur **Enregistrer**.
  2. Sélectionnez la machine virtuelle et cliquez sur **redémarrer** pour déclencher la machine virtuelle pour configurer les paramètres du programme de résolution DNS avec l’adresse IP du nouveau serveur DNS.


## <a name="create-vms-for-domain-members"></a>Créer des ordinateurs virtuels pour les membres du domaine

1. Répétez les étapes suivantes pour créer des ordinateurs virtuels pour s’exécuter en tant que serveurs d’applications. Accepter la valeur par défaut pour un paramètre, sauf si une autre valeur suggérée ou requis.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Choisir une Image**  | Windows Server 2012 R2 Datacenter
    **Configuration de Machine virtuelle**  | <p>Nom de la Machine virtuelle : Tapez un nom unique (par exemple, AppServer1).</p><p>Nouveau nom d’utilisateur : Tapez le nom d’un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur l’ordinateur virtuel. Vous aurez besoin de ce nom pour se connecter à la machine virtuelle pour la première fois. Le compte administrateur intégré ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmer : Tapez un mot de passe</p>
    **Configuration de Machine virtuelle**  | <p>Service de cloud : Choisissez de **créer un nouveau service en nuage** pour la machine virtuelle de premier et sélectionnez ce nom de service cloud même lorsque vous créez davantage d’ordinateurs virtuels qui héberge l’application.</p><p>Nom DNS de Service cloud : Spécifier un nom globalement unique</p><p>Région/groupe d’affinité/réseau virtuel : spécifiez le nom de réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez **d’utiliser un compte de stockage généré automatiquement** pour la machine virtuelle de premier et puis sélectionnez ce nom de compte de stockage même lorsque vous créez davantage d’ordinateurs virtuels qui héberge l’application.</p><p>Ensemble de disponibilité : Choisissez de **créer un jeu de disponibilité**.</p><p>Nom du jeu de disponibilité : tapez un nom pour la disponibilité d’ensemble lorsque vous créez la première VM et sélectionnez ensuite que même nom lorsque vous créez davantage d’ordinateurs virtuels.</p>
    **Configuration de Machine virtuelle**  | <p>Sélectionnez <b>l’installation de l’Agent de la machine virtuelle</b> et toutes les autres extensions dont vous avez besoin.</p>
2. Une fois que chaque machine virtuelle est mis en service, connectez-vous et joignez-le au domaine. Dans le **Gestionnaire de serveur**, cliquez sur **Serveur Local** > **groupe de travail** > **Modifier...** Sélectionnez **domaine** et tapez le nom de votre domaine local. Fournir des informations d’identification d’un utilisateur de domaine et puis redémarrez la machine virtuelle pour terminer la jonction de domaine.

Pour créer les ordinateurs virtuels au lieu de l’interface utilisateur à l’aide de Windows PowerShell, voir [Utilisation des PowerShell de Azure permet de créer et de préconfigurer des Machines virtuelles basées sur Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Pour plus d’informations sur l’utilisation de Windows PowerShell, reportez-vous à la section [Mise en route avec les applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) et [Azure applet de commande référence](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## <a name="see-also"></a>Voir aussi

-  [Comment faire pour installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Instructions pour le déploiement de Windows Server Active Directory sur les ordinateurs virtuels Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurer un VPN de Site à Site](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installer un contrôleur de domaine réplica Active Directory dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IaaS de Pro informatiques : principes de base des machines virtuelles (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS Pro d’informatique : (05) création des réseaux virtuels et connectivité de coexistence](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)
-  [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Référence d’applet de commande Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Définissez une adresse IP statique Azure VM](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Comment faire pour affecter des adresses IP statiques à Azure VM](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installer une nouvelle forêt Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introduction à la virtualisation de Services (AD DS) de domaine Active Directory (niveau 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
