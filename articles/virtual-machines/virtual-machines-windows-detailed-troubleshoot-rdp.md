<properties
    pageTitle="Dépannage détaillées de bureau à distance | Microsoft Azure"
    description="Consulter les étapes de dépannage détaillées pour les erreurs de bureau distant où vous ne pouvez pas des machines virtuelles Windows dans Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="Impossible de se connecter au Bureau à distance, résoudre les problèmes de bureau à distance, le Bureau à distance ne peut pas se connecter, des erreurs de bureau à distance, dépannage de bureau à distance, les problèmes de bureau à distance
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Étapes de dépannage détaillées pour les problèmes de connexion Bureau à distance aux ordinateurs virtuels Windows Azure

Cet article fournit des étapes de dépannage détaillées pour diagnostiquer et corriger les erreurs de bureau à distance complexes pour basée sur Windows Azure les machines virtuelles.

> [AZURE.IMPORTANT] Pour éliminer les erreurs de bureau à distance les plus courantes, assurez-vous de lire [l’article résolution des problèmes de base pour le Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md) avant de continuer.

Vous pouvez rencontrer un message d’erreur de bureau à distance qui ne ressemble pas à un des messages d’erreur spécifiques décrites dans [le guide de dépannage base de bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md). Suivez ces étapes pour déterminer pourquoi le client Bureau à distance (RDP) est impossible de se connecter au service de RDP sur l’ordinateur virtuel d’Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, vous pouvez contacter les experts Azure sur [l’Azure MSDN et les forums de débordement de pile](https://azure.microsoft.com/support/forums/). Sinon, vous pouvez également des fichiers un incident de support Azure. Accédez au [site de Support d’Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir la prise en charge**. Pour plus d’informations sur l’utilisation de prise en charge d’Azure, lire le [Forum aux questions sur la prise en charge de Microsoft Azure](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Composants d’une connexion Bureau à distance

Les composants suivants sont impliqués dans une connexion RDP :

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Avant de continuer, pour mieux mentalement passer en revue ce qui a changé depuis la dernière connexion Bureau à distance de la machine virtuelle. Par exemple :

- L’adresse IP publique de l’ordinateur virtuel ou le service en nuage contenant de la machine virtuelle (également appelée l’adresse IP virtuelle [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) a été modifié. L’échec RDP peut être parce que le cache du client DNS a toujours l' *ancienne adresse IP* enregistrée pour le nom DNS. Vider le cache du client DNS et essayez de reconnecter l’ordinateur virtuel. Ou essayez de vous connecter directement avec l’adresse IP virtuelle de nouveau.
- Vous utilisez une application tierce pour gérer vos connexions Bureau à distance au lieu d’utiliser la connexion générée par le portail Azure. Vérifiez que la configuration de l’application inclut le port TCP correct pour le trafic de bureau à distance. Vous pouvez vérifier ce port pour une machine virtuelle classique dans [Azure portal](https://portal.azure.com), en cliquant sur paramètres de l’ordinateur > points de terminaison.


## <a name="preliminary-steps"></a>Étapes préliminaires

Avant de passer à la procédure de dépannage détaillées,

- Vérifiez l’état de la machine virtuelle dans le portail classique Azure ou le portail Azure pour tous les problèmes évidents.
- Suivez les [étapes de solution rapide pour les erreurs courantes de RDP dans le guide de dépannage de base](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Essayez de vous reconnecter à la machine virtuelle via le Bureau à distance après ces étapes.


## <a name="detailed-troubleshooting-steps"></a>Étapes de résolution détaillées

Le client Bureau à distance n’est peut-être pas en mesure d’atteindre le service Bureau à distance sur l’ordinateur virtuel d’Azure en raison de problèmes dans les sources suivantes :

- [Ordinateur du client Bureau à distance](#source-1-remote-desktop-client-computer)
- [Dispositif de bord intranet organisation](#source-2-organization-intranet-edge-device)
- [Point de terminaison de service en nuage et accéder à la liste de contrôle (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Groupes de sécurité de réseau](#source-4-network-security-groups)
- [Basé sur Windows Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>La source 1 : Ordinateur client Bureau à distance

Vérifiez que votre ordinateur peut établir des connexions de bureau à distance à un autre sur site, ordinateur fonctionnant sous Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Si vous ne le pouvez pas, vérifiez les paramètres suivants sur votre ordinateur :

- Un paramètre du pare-feu local qui bloque le trafic de bureau à distance.
- Installé localement le logiciel client de proxy qui empêche les connexions Bureau à distance.
- Un logiciel qui empêche les connexions Bureau à distance de surveillance installés en local.
- Autres types de logiciels de sécurité qui surveillent le trafic ou autorisent/interdire les types spécifiques de trafic qui empêche les connexions Bureau à distance.

Dans tous ces cas, désactivez le logiciel temporairement et essayez de vous connecter à un ordinateur local via le Bureau à distance. Si vous pouvez déterminer la cause réelle de cette manière, travailler avec votre administrateur réseau afin de corriger les paramètres du logiciel pour autoriser des connexions Bureau à distance.

## <a name="source-2-organization-intranet-edge-device"></a>Source 2 : Dispositif de bord intranet organisation

Vérifiez qu’un ordinateur directement connecté à Internet peut établir des connexions de bureau à distance à votre machine virtuelle Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Si vous ne disposez pas d’un ordinateur qui est connecté directement à Internet, créer et tester avec un nouvel ordinateur virtuel Azure dans un service de nuage ou de groupe de ressources. Pour plus d’informations, consultez [créer un ordinateur virtuel exécutant Windows dans Azure](virtual-machines-windows-hero-tutorial.md). Vous pouvez supprimer l’ordinateur virtuel et le groupe de ressources ou le service en nuage, après l’essai.

Si vous pouvez créer une connexion Bureau à distance avec un ordinateur directement relié à Internet, vérifiez le périphérique de bord intranet organisation pour :

- Un pare-feu bloque les connexions HTTPS à Internet.
- Un serveur proxy empêche les connexions Bureau à distance.
- Détection d’intrusion ou de surveillance des logiciels exécutés sur des périphériques de votre réseau de périmètre qui empêche les connexions Bureau à distance du réseau.

Travailler avec votre administrateur réseau afin de corriger les paramètres de votre périphérique de périmètre organisation intranet pour autoriser les connexions basées sur HTTPS de bureau à distance à Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Source 3 : Point de terminaison de service Cloud et ACL

Pour les machines virtuelles créées avec le modèle de déploiement classique, vérifiez un autre Azure VM qui se trouve dans le même service en nuage ou de réseau virtuel peuvent les connexions Bureau à distance à votre machine virtuelle d’Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Pour les ordinateurs virtuels créés dans le Gestionnaire des ressources, passez à le [4 de Source : groupes de sécurité réseau](#source-4-network-security-groups).

Si vous ne disposez pas d’un autre ordinateur virtuel dans le même service en nuage ou de réseau virtuel, créez-en un. Suivez les étapes dans [créer un ordinateur virtuel exécutant Windows dans Azure](virtual-machines-windows-hero-tutorial.md). Supprimer la machine virtuelle de test une fois le test terminé.

Si vous pouvez vous connecter via le Bureau à distance à un ordinateur virtuel dans le même service en nuage ou de réseau virtuel, vérifiez ces paramètres :

- La configuration de point de terminaison pour le trafic de bureau à distance sur la machine virtuelle de la cible : le port TCP privé du point de terminaison doit correspondre le port TCP sur lequel le service de bureau à distance de la machine virtuelle est à l’écoute (valeur par défaut est 3389).
- La liste ACL pour le point de terminaison du trafic de bureau à distance sur la machine virtuelle de la cible : ACL permettre de spécifier autorisé ou refusé le trafic entrant à partir d’Internet en fonction de l’adresse IP source. Mauvaise configuration des ACL peuvent empêcher le trafic entrant sur le Bureau à distance pour le point de terminaison. Vérifiez vos ACL pour vous assurer que le trafic entrant à partir de vos adresses IP publiques de votre serveur proxy ou autre serveur de transport edge n’est autorisé. Pour plus d’informations, consultez [ce qu’est une liste de contrôle d’accès réseau (ACL) ?](../virtual-network/virtual-networks-acl.md)

Pour vérifier si le point de terminaison est la source du problème, supprimez le point de terminaison en cours et créer une nouvelle, le choix d’un port aléatoire dans la plage 49152 à 65535 pour le numéro de port externe. Pour plus d’informations, consultez [comment configurer des points de terminaison à une machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md).

## <a name="source-4-network-security-groups"></a>Source 4 : Groupes de sécurité de réseau

Les groupes de sécurité réseau permettent un contrôle plus granulaire sur le trafic entrant et sortant autorisé. Vous pouvez créer des règles de sous-réseaux et services dans un réseau virtuel Azure en nuage. Vérifiez vos règles de groupe de sécurité de réseau pour vous assurer que le trafic de bureau à distance à partir d’Internet est autorisé :

- Dans le portail Azure, sélectionnez votre machine virtuelle
- Cliquez sur **tous les paramètres** | **les interfaces réseau** et sélectionnez votre interface réseau.
- Cliquez sur **tous les paramètres** | **groupe de sécurité réseau** et sélectionnez votre groupe de sécurité du réseau.
- Cliquez sur **tous les paramètres** | **les règles de sécurité entrante** et assurez-vous que vous disposez d’une règle autorisant le RDP sur le port TCP 3389.
    - Si vous ne disposez pas d’une règle, cliquez sur **Ajouter** pour créer une règle. Entrez **TCP** pour le protocole, puis **3389** de la plage de ports de destination.
    - Assurez-vous que l’action est définie sur **Autoriser** , puis cliquez sur OK pour enregistrer votre nouvelle règle de trafic entrant.


Pour plus d’informations, consultez [ce qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Source de 5 : Basée sur Windows Azure VM

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Utiliser le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour voir si le problème provient de l’ordinateur virtuel Azure lui-même. Si ce package de diagnostics ne parvient pas à résoudre le problème de **connectivité RDP à une machine virtuelle d’Azure (redémarrage requis)** , suivez les instructions de [cet article](virtual-machines-windows-reset-rdp.md). Cet article réinitialise le service Bureau à distance sur l’ordinateur virtuel :

- Activer la règle par défaut de « Bureau à distance » Windows Firewall (port TCP 3389).
- Activer les connexions Bureau à distance en définissant la valeur de Registre HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections sur 0.

Réessayez de vous connecter à partir de votre ordinateur. Si vous ne parvenez toujours pas à se connecter via le Bureau à distance, recherchez les problèmes suivants :

- Le service Bureau à distance ne fonctionne pas sur la cible de la machine virtuelle.
- Le service Bureau à distance n’écoute pas sur le port TCP 3389.
- Le pare-feu Windows ou un autre pare-feu local a une règle sortante qui empêche le trafic du Bureau à distance.
- Détection d’intrusion ou de réseau de surveillance des logiciels en cours d’exécution sur l’ordinateur virtuel Azure empêche les connexions Bureau à distance.

Pour les machines virtuelles créées en utilisant le modèle de déploiement classique, vous pouvez utiliser une session Azure PowerShell à distance sur l’ordinateur virtuel Azure. Tout d’abord, vous devez installer un certificat pour hébergement cloud service la machine virtuelle. Accédez à [Configuration accès distant sécurisé PowerShell pour Azure Virtual Machines](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) et télécharger le fichier de script **InstallWinRMCertAzureVM.ps1** sur votre ordinateur local.

Ensuite, installez Azure PowerShell si vous ne l’avez pas déjà. Voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Ensuite, ouvrez une invite de commandes PowerShell d’Azure et modifier le dossier actif à l’emplacement du fichier de script **InstallWinRMCertAzureVM.ps1** . Pour exécuter un script PowerShell d’Azure, vous devez définir la stratégie d’exécution correct. Exécutez la commande **Get-ExecutionPolicy** pour déterminer votre niveau de stratégie actuel. Pour plus d’informations sur la définition du niveau approprié, consultez [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Ensuite, complétez le nom de votre abonnement Azure, le nom de service cloud et le nom de votre machine virtuelle (suppression les caractères < et >), puis exécutez ces commandes.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Vous pouvez obtenir le nom d’abonnement correct à partir de la propriété _SubscriptionName_ de l’affichage de la commande **Get-AzureSubscription** . Vous pouvez obtenir le nom de service de cloud pour la machine virtuelle à partir de la colonne de _ServiceName_ dans l’affichage de la commande **Get-AzureVM** .

Vérifiez si vous avez le nouveau certificat. Ouvrir un composant logiciel enfichable Certificats pour l’utilisateur actuel et rechercher dans le dossier **Authorities\Certificates de Certification racine de confiance** . Vous devez voir un certificat portant le nom DNS de votre service cloud dans la colonne délivré à (exemple : cloudservice4testing.cloudapp.net).

Ensuite, lancer une session Azure PowerShell à distance à l’aide de ces commandes.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Après avoir entré les informations d’identification administrateur valides, vous devez voir quelque chose de similaire à l’invite de PowerShell d’Azure suivante :

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La première partie de ce message est le nom de votre service cloud qui contient la cible virtuelle, qui peut être différent de « cloudservice4testing.cloudapp.net ». Vous pouvez maintenant émettre Azure PowerShell commandes pour ce service de cloud étudier les problèmes mentionnés et corriger la configuration.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Pour corriger manuellement les Services Bureau à distance écoute le port TCP

Si vous ne parvenez pas à exécuter le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour le problème de **connectivité RDP à une machine virtuelle d’Azure (redémarrage requis)** , à l’invite de session Azure PowerShell distant, exécutez la commande suivante.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propriété PortNumber indique le numéro de port en cours. Si nécessaire, modifiez le Bureau à distance de port numéro précédent à sa valeur par défaut (3389) à l’aide de cette commande.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Vérifiez que le port a été modifié à 3389 à l’aide de cette commande.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Quitter la session Azure PowerShell à distance à l’aide de cette commande.

    Exit-PSSession

Vérifiez que le point de terminaison du Bureau à distance pour la machine virtuelle d’Azure utilise également le port TCP 3398 comme son port interne. Redémarrez la machine virtuelle d’Azure et essayez à nouveau la connexion Bureau à distance.


## <a name="additional-resources"></a>Ressources supplémentaires

[Package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Comment faire pour réinitialiser un mot de passe ou le service de bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-rdp.md)

[Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md)

[Dépannage des connexions SSH (Secure Shell) à une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Résoudre les problèmes d’accès à une application en cours d’exécution sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)
