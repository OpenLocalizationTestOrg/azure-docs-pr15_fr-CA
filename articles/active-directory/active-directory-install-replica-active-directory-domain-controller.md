<properties
    pageTitle="Installer un contrôleur de domaine Active Directory réplique dans Azure | Microsoft Azure"
    description="Didacticiel qui explique comment installer un contrôleur de domaine d’une forêt d’Active Directory sur site sur une machine virtuelle Azure."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installer un contrôleur de domaine de réplica Active Directory dans un réseau virtuel Azure

Cette rubrique montre comment installer des contrôleurs de domaine supplémentaires (également connu sous le nom de réplicas contrôleurs de domaine) pour un domaine d’Active Directory sur site sur Azure des machines virtuelles (VM) contenus dans un réseau virtuel Azure.

Pourraient également vous intéresser dans ces rubriques connexes :

-  Vous pouvez éventuellement installer une nouvelle forêt Active Directory sur un réseau virtuel Azure. Pour connaître ces étapes, voir [installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Pour obtenir des instructions sur l’installation des Services de domaine Active Directory (AD DS) sur un réseau virtuel Azure conceptuelles, consultez [instructions pour déployer Windows Server Active Directory Azure machines virtuelles en fonctionnement](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Diagramme de scénario

Dans ce scénario, les utilisateurs externes ont besoin d’accéder aux applications qui s’exécutent sur des serveurs à un domaine. Les ordinateurs virtuels qui s’exécutent sur les serveurs d’application et les contrôleurs de domaine de réplica sont installés dans un réseau virtuel Azure. Le réseau virtuel peut être connecté au réseau local par une connexion [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) , comme illustré dans le diagramme suivant, ou vous pouvez utiliser [ExpressRoute](../expressroute/expressroute-locations-providers.md) pour une connexion plus rapide.

Les serveurs d’application et les contrôleurs de domaine sont déployés dans les services en nuage distinct pour répartir le traitement de compute et [jeux de disponibilité](../virtual-machines/virtual-machines-windows-manage-availability.md) d’une meilleure tolérance.
Les contrôleurs de domaine répliquent entre eux et avec les contrôleurs de domaine locaux à l’aide de la réplication Active Directory. Aucun outil de synchronisation n’est nécessaires.

![Contrôleur de domaine Active Directory diagramme pf répliqué un vnet Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Créer un site Active Directory pour l’ordinateur virtuel Azure

Il est conseillé de créer un site dans Active Directory qui représente la zone du réseau correspondant au réseau virtuel. Qui vous aide à optimiser l’authentification, de réplication et d’autres opérations de localisation du contrôleur de domaine. Les étapes suivantes expliquent comment créer un site et pour plus d’informations, consultez [Ajout d’un nouveau Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Ouvrez Sites et Services Active Directory : **Le Gestionnaire de serveur** > **Outils** > **Sites et Services Active Directory**.
2. Créer un site pour représenter la zone où vous avez créé un réseau virtuel Azure : cliquez sur les **Sites** > **Action** > **Nouveau site** > tapez le nom du nouveau site, par exemple Azure nous ouest > sélectionnez un lien de site > **OK**.
3. Créer un sous-réseau et l’associer avec le nouveau site : double-cliquez sur **Sites** > droit **sous-réseaux** > **nouveau sous-réseau** > tapez la plage d’adresses IP du réseau virtuel (par exemple, 10.1.0.0/16 dans le diagramme du scénario) > sélectionnez le nouveau site Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Créer un réseau virtuel Azure

1. Dans [Azure portal classique](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > valeurs**Personnalisées Créez** et utilisez les éléments suivants pour terminer l’Assistant.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Détails du réseau virtuel**  | <p>Nom : Entrez un nom pour le réseau virtuel, tel que WestUSVNet.</p><p>Région : Choisissez la région la plus proche.</p>
    **Connectivité VPN et de DNS**  | <p>Serveurs DNS : Permet de spécifier le nom et l’adresse IP d’un ou plusieurs des serveurs DNS sur site.</p><p>Connectivité : Sélectionnez **configurer un réseau VPN de site à site**.</p><p>Réseau local : spécifier un nouveau réseau local.</p><p>Si vous utilisez ExpressRoute au lieu d’une connexion VPN, voir [configuration d’une connexion ExpressRoute de via un fournisseur d’Exchange](../expressroute/expressroute-locations-providers.md).</p>
    **Connectivité de site à site**  | <p>Nom : Tapez un nom pour le réseau local.</p><p>Adresse IP du périphérique VPN : spécifiez l’adresse IP publique du périphérique qui se connecte au réseau virtuel. Impossible de trouver le périphérique VPN derrière un NAT.</p><p>Adresse : Indiquez les plages d’adresses pour votre réseau local (par exemple 192.168.0.0/16 dans le diagramme du scénario).</p>
    **Espaces d’adressage de réseau virtuel**  | <p>Espace d’adressage : Spécifiez la plage d’adresses IP pour les machines virtuelles que vous souhaitez exécuter dans le réseau virtuel Azure (par exemple, 10.1.0.0/16 dans le diagramme du scénario). Cette plage d’adresses ne peut pas chevaucher les plages d’adresses du réseau local.</p><p>Sous-réseaux : Spécifiez un nom et une adresse pour un sous-réseau pour les serveurs d’application (par exemple frontaux, 10.1.1.0/24) et pour les contrôleurs de domaine (tels que back-end, 10.1.2.0/24).</p><p>Cliquez sur **Ajouter un sous-réseau de la passerelle**.</p>

2. Ensuite, vous allez configurer la passerelle réseau virtuel pour créer une connexion VPN de site à site sécurisée. Pour les instructions, voir [configuration d’une passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) .
3. Créer la connexion VPN de site à site entre le nouveau réseau virtuel et un dispositif VPN sur site. Pour les instructions, voir [configuration d’une passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) .


## <a name="create-azure-vms-for-the-dc-roles"></a>Créer des ordinateurs virtuels d’Azure pour les rôles de contrôleur de domaine

Répétez les étapes suivantes pour créer des ordinateurs virtuels pour héberger le rôle de contrôleur de domaine en fonction des besoins. Vous devez déployer au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de pannes. Si le réseau virtuel Azure comprend au moins deux contrôleurs de domaine qui sont configurés de la même façon (c'est-à-dire qu’ils sont les deux catalogues globaux, exécution serveur DNS, et ni détient un rôle FSMO et ainsi de suite) puis placer les ordinateurs virtuels qui s’exécutent les contrôleurs de domaine dans une disponibilité pour une meilleure tolérance.
Pour créer les ordinateurs virtuels au lieu de l’interface utilisateur à l’aide de Windows PowerShell, voir [Utilisation des PowerShell de Azure permet de créer et de préconfigurer des Machines virtuelles basées sur Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Dans [Azure portal classique](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **De la galerie**. Utilisez les valeurs suivantes pour terminer l’Assistant. Accepter la valeur par défaut pour un paramètre, sauf si une autre valeur suggérée ou requis.

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

## <a name="install-ad-ds-on-azure-vms"></a>Installer les services AD DS sur Azure VM

Se connecter à un ordinateur virtuel et vérifiez que vous êtes connecté via la connexion de site à site VPN ou ExpressRoute aux ressources de votre réseau local. Puis installez les services AD DS sur les ordinateurs virtuels d’Azure. Vous pouvez utiliser le même processus que vous utilisez pour installer un contrôleur de domaine supplémentaire sur votre réseau local (interface utilisateur, Windows PowerShell ou un fichier de réponses). Lorsque vous installez les services AD DS, assurez-vous de que spécifier le nouveau volume de l’emplacement de la base de données Active Directory, les journaux et SYSVOL. Si vous avez besoin d’un rappel sur l’installation des services AD DS, voir [Installation de Services domaine Active Directory (niveau 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [installer un contrôleur de domaine de réplica Windows Server 2012 dans un domaine existant (niveau 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigurer le serveur DNS pour le réseau virtuel

1. Dans [Azure portal classique](https://manage.windowsazure.com), cliquez sur le nom du réseau virtuel, puis cliquez sur l’onglet **configurer** pour [reconfigurer les adresses IP de serveur DNS pour votre réseau virtuel](../virtual-network/virtual-networks-manage-dns-in-vnet.md) à utiliser les adresses IP affectées à la réplique de contrôleurs de domaine et non les adresses IP de des serveurs DNS sur site.

2. Pour vous assurer que tous les réplicas ordinateurs virtuels de contrôleur de domaine sur le réseau virtuel sont configurés avec pour utiliser des serveurs DNS sur le réseau virtuel, cliquez sur **ordinateurs virtuels**, cliquez sur la colonne d’état de chaque ordinateur virtuel, puis cliquez sur **redémarrer**. Patientez jusqu'à ce que la machine virtuelle affiche **en cours d’exécution** avant d’essayer de vous connecter à elle.

## <a name="create-vms-for-application-servers"></a>Créer des ordinateurs virtuels pour les serveurs d’application

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

## <a name="additional-resources"></a>Ressources supplémentaires

-  [Instructions pour le déploiement de Windows Server Active Directory sur les ordinateurs virtuels Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Comment faire pour télécharger des contrôleurs de domaine existants sur site Hyper-V vers Azure à l’aide de PowerShell d’Azure](http://support.microsoft.com/kb/2904015)
-  [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Azure réseau virtuel](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IaaS de Pro informatiques : principes de base des machines virtuelles (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS Pro d’informatique : (05) création des réseaux virtuels et connectivité de coexistence](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlets de gestion Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
