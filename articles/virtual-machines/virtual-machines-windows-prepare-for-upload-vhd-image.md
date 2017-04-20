<properties
    pageTitle="Préparer un disque dur virtuel de Windows pour télécharger vers Azure | Microsoft Azure"
    description="Pratiques recommandées pour la préparation d’un disque dur virtuel de Windows avant de le télécharger vers Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Préparer un disque dur virtuel de Windows pour télécharger vers Azure
Pour télécharger une VM Windows en local sur Azure, vous devez correctement préparer le disque dur virtuel (VHD). Il existe plusieurs étapes recommandées que vous pouvez effectuer avant de télécharger un disque dur virtuel vers Azure. Cet article vous indique comment préparer un disque dur virtuel de Windows pour télécharger vers Microsoft Azure, et explique [quand et comment utiliser Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Préparer le disque virtuel

>[AZURE.NOTE] 
> Azure prend uniquement en charge les [ordinateurs virtuels de génération 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) qui sont dans le format de fichier de disque dur virtuel. Le nouveau format VHDX n’est pas pris en charge dans Azure. 
>
> Le disque dur virtuel doit être d’une taille fixe, non dynamique. Si nécessaire, les instructions ci-dessous en détail la conversion de disques VHDX ou dynamique. La taille maximale autorisée pour le disque dur virtuel est 1 023 go.


Assurez-vous que le disque dur virtuel de Windows fonctionne correctement sur le serveur local. Résolvez toutes les erreurs dans la machine virtuelle elle-même avant d’essayer de convertir ou télécharger dans Azure.

Si vous devez convertir votre disque virtuel dans le format requis pour Azure, utilisez une des méthodes décrites dans les sections suivantes. Sauvegarder la machine virtuelle avant d’exécuter tout processus de conversion de disque virtuel ou de Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Convertir à l’aide du Gestionnaire Hyper-V
- Ouvrez le Gestionnaire Hyper-V et sélectionnez votre ordinateur local sur la gauche. Dans le menu au-dessus de lui, cliquez sur **Action** > **Modifier le disque**.
    - Sur l’écran de **disque dur virtuel de localiser** , recherchez et sélectionnez votre disque virtuel.
    - Sélectionnez **convertir** dans l’écran suivant
        - Si vous avez besoin convertir à partir de VHDX, sélectionnez le **disque dur virtuel** et cliquez sur **suivant**
        - Si vous voulez convertir en disque dynamique, sélectionnez **taille fixe** et cliquez sur **suivant**

    - Recherchez et sélectionnez le **chemin d’accès du nouveau fichier de disque dur virtuel**.
    - Cliquez sur **Terminer** pour fermer.

### <a name="convert-using-powershell"></a>Convertir à l’aide de PowerShell
Vous pouvez convertir un disque virtuel à l’aide de l' [applet de commande PowerShell de convertir-disque dur virtuel](http://technet.microsoft.com/library/hh848454.aspx). Dans l’exemple suivant, nous sommes conversion à partir d’un VHDX en disque dur virtuel et la conversion d’une dynamique de type fixe :

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertir d’un format de disque VMware VMDK
Si vous avez une image de machine virtuelle de Windows dans le [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertir en un disque dur virtuel à l’aide du [Convertisseur de Machine virtuelle Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Lire le blog [comment convertir un VMDK de VMware pour le disque dur virtuel d’Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) pour plus d’informations.

## <a name="prepare-windows-configuration-for-upload"></a>Préparer la configuration de Windows pour le téléchargement

> [AZURE.NOTE] Exécuter toutes les commandes suivantes avec [des privilèges d’administrateur](https://technet.microsoft.com/library/cc947813.aspx).

1. Supprimer un itinéraire statique permanent sur la table de routage :

    - Pour afficher la table de routage, exécutez `route print`.
    - Consultez les sections de **Routes de persistance** . S’il existe un itinéraire persistant, utilisez [itinéraire supprimer](https://technet.microsoft.com/library/cc739598.apx) pour le supprimer.

2. Supprimer le proxy de WinHTTP :

    ```
    netsh winhttp reset proxy
    ```

3. Configurer la stratégie SAN à [Onlineall](https://technet.microsoft.com/library/gg252636.aspx)de disque :

    ```
    diskpart san policy=onlineall
    ```

4. Utiliser l’heure de temps universel coordonné (UTC) pour Windows et affectez le type de démarrage du service de temps de Windows (w32time) **automatiquement**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurer des services Windows
5. Assurez-vous que chacun des services Windows suivants est défini sur les **valeurs par défaut de Windows**. Ils sont configurés avec les paramètres de démarrage indiquées dans la liste suivante. Vous pouvez exécuter ces commandes pour réinitialiser les paramètres de démarrage :

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Configurer le Bureau à distance
6. S’il existe des certificats auto-signés liées à l’écouteur du protocole RDP (Remote Desktop), supprimez-les :

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Pour plus d’informations sur la configuration des certificats pour le port d’écoute RDP, consultez [Configurations de certificat de port d’écoute dans Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configurez les valeurs [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) pour service RDP :

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configurer le mode d’authentification pour le service RDP :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Activer le service RDP en ajoutant les sous-clés suivantes au Registre :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurer les règles du pare-feu Windows
10. Autoriser WinRM les trois profils de pare-feu (domaine, privé et Public) et activer le service distant de PowerShell :

    ```
    Enable-PSRemoting -force
    ```

11. Assurez-vous que les règles de pare-feu du système d’exploitation invité suivant sont en place :

    - Trafic entrant

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - Entrant et sortant

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Sortant

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Étapes supplémentaires de configuration de Windows
12. Exécutez `winmgmt /verifyrepository` pour confirmer que le référentiel de l’Instrumentation de gestion Windows (WMI) est cohérent. Si le référentiel est endommagé, consultez [ce billet de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Assurez-vous que les paramètres de données de Configuration de démarrage (BCD) correspondent aux actions suivantes :

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Supprimer tous les filtres de Transport Driver Interface supplémentaires, tel que le logiciel analyse les paquets TCP.
15. Pour vous assurer que le disque est sain et cohérent, exécutez le `CHKDSK /f` commande.
16. Désinstallation d’autres logiciels tiers et pilote liés aux composants physiques ou toute autre technologie de virtualisation.
17. Assurez-vous qu’une application tiers n’utilise pas le Port 3389. Ce port est utilisé pour le service du protocole RDP dans Azure. Vous pouvez utiliser la `netstat -anob` commande pour vérifier les ports utilisé par les applications.
18. Si le disque dur virtuel de Windows que vous voulez télécharger est un contrôleur de domaine, suivez [ces étapes supplémentaires](https://support.microsoft.com/kb/2904015) pour préparer le disque.
19. Redémarrage de la machine virtuelle pour vous assurer que Windows est toujours sain peut être atteint à l’aide de la connexion RDP.
20. Réinitialiser le mot de passe administrateur local et vérifiez que vous pouvez utiliser ce compte pour ouvrir une session Windows par le biais de la connexion RDP.  Cet accès est contrôlé par l’objet de stratégie « Autoriser le journal par les Services Bureau à distance ». Cet objet se trouve sous « Ordinateur Configuration\Windows Settings\Security sécurité\Stratégies Locales\attribution des droits. »


## <a name="install-windows-updates"></a>Installer les mises à jour Windows
22. Installez les dernières mises à jour pour Windows. Si cela n’est pas possible, assurez-vous que les mises à jour suivantes sont installées :

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VM ne récupérer d’une panne de réseau et les problèmes de corruption de données se produisent

    - [KB3115224](https://support.microsoft.com/kb/3115224) Améliorations de la fiabilité pour les machines virtuelles qui sont exécutent sur un hôte Windows Server 2012 R2 ou de Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour pour Windows de Microsoft une élévation d’adresse des privilèges : le 8 mars 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) De nombreux événements ID 129 sont enregistrés lorsque vous exécutez une machine virtuelle de Windows Server 2012 R2 dans Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VM ne récupérer d’une panne de réseau et les problèmes de corruption de données se produisent

    - [KB3114025](https://support.microsoft.com/kb/3114025) Ralentissement des performances lorsque vous accédez à stockage de fichiers Azure des 8.1 de Windows Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Correctif augmente la limite de 64 Ko dans des mémoires tampons de RIO par processus pour le service Windows Azure

    - [KB3004545](https://support.microsoft.com/kb/3004545) Impossible d’accéder aux ordinateurs virtuels qui sont hébergés sur des services d’hébergement via une connexion VPN dans Windows Azure

    - [KB3082343](https://support.microsoft.com/kb/3082343) Connectivité VPN de coexistence est perdue lorsque les tunnels VPN site à site Azure utilisent RRAS de Windows Server 2012 R2

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour pour Windows de Microsoft une élévation d’adresse des privilèges : le 8 mars 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048 : Description la mise à jour de sécurité pour le processus CSRSS : 12 avril 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) Système se fige pendant les e/s dans Windows<a id="step23"></a>
23. Si vous souhaitez créer une image pour déployer plusieurs ordinateurs à partir de celui-ci, vous devez généraliser l’image en exécutant `sysprep` avant de télécharger le disque dur virtuel sur Azure. Vous n’avez pas besoin d’exécuter `sysprep` pour l’utilisation d’un disque dur virtuel spécialisé. Pour plus d’informations sur la façon de créer une image généralisée, consultez les articles suivants :

    - [Créer une image de machine virtuelle à partir d’une VM Azure existante en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-create-vm-generalized.md)
    - [Créer une image de machine virtuelle à partir d’une VM Azure existante en utilisant le modem de déploiement standard](virtual-machines-windows-classic-capture-image.md)
    - [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurations supplémentaires suggérées

Les paramètres suivants n’affectent pas le transfert de disque dur virtuel. Toutefois, nous recommandons fortement que vous avez configurés.

- Installer l' [Agent d’Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Après l’installation de l’agent, vous pouvez activer les extensions de la machine virtuelle. Les extensions de la machine virtuelle implémentent la plupart des fonctionnalités critiques que vous souhaitez utiliser avec vos ordinateurs virtuels comme la réinitialisation des mots de passe, la configuration de RDP et beaucoup d’autres.

- Le journal vidage peut être utile pour dépanner des problèmes de blocage de Windows. Activer la collection de journal vidage :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Une fois que la machine virtuelle est créée dans Azure, configurer le fichier d’échange de taille définie par le système sur le lecteur D:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Étapes suivantes

- [Télécharger une image de machine virtuelle de Windows Azure pour les déploiements du Gestionnaire de ressources](virtual-machines-windows-upload-image.md)
