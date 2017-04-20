<properties
    pageTitle="Résoudre les problèmes de sauvegarde Azure virtual machine | Microsoft Azure"
    description="Résoudre les problèmes liés à la sauvegarde et la restauration des machines virtuelles Azure"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Résoudre les problèmes de sauvegarde Azure virtual machine

> [AZURE.SELECTOR]
- [Chambre forte de services de récupération](backup-azure-vms-troubleshoot.md)
- [Chambre forte de sauvegarde](backup-azure-vms-troubleshoot-classic.md)

Vous pouvez résoudre les erreurs rencontrées pendant qu’à l’aide de la sauvegarde d’Azure avec les informations répertoriées dans le tableau ci-dessous.

## <a name="backup"></a>Sauvegarde

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
|Sauvegarde|    N’a pas pu effectuer l’opération car la machine virtuelle n’existe plus. -Arrêter la protection de machines virtuelles sans supprimer les données de sauvegarde. Plus de détails au http://go.microsoft.com/fwlink/?LinkId=808124   |Cela se produit lorsque l’ordinateur virtuel principal est supprimé, mais la stratégie de sauvegarde continue à rechercher un ordinateur virtuel effectuer la sauvegarde. Pour corriger cette erreur : <ol><li> Recréer l’ordinateur virtuel avec le même nom et le même nom de groupe de ressources [nom de service cloud]<br>(OR)</li><li> Arrêter la protection de machines virtuelles avec ou sans supprimer les données de sauvegarde. [Plus de détails](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Sauvegarde|N’a pas pu communiquer avec l’agent de la machine virtuelle pour l’état de la capture instantanée. -S’assurer que les ordinateur virtuel a accès à internet. En outre, mettre à jour l’agent de la machine virtuelle comme indiqué dans le guide de dépannage à http://go.microsoft.com/fwlink/?LinkId=800034 |Cette erreur est générée si il y a un problème avec l’Agent de la machine virtuelle ou d’accès réseau vers l’infrastructure Azure est bloquée d’une certaine façon. Pour en savoir plus sur le débogage des VM problèmes de capture instantanée.<br> Si l’agent de la machine virtuelle n’est pas dû à des problèmes, puis redémarrez la machine virtuelle. Parfois un état incorrect de la machine virtuelle peut provoquer des problèmes et de redémarrage de la machine virtuelle réinitialise cet « mauvais état »|
|Sauvegarde|    Échec de l’opération d’extension de services de récupération. -Assurez-vous que cet agent plus récent de la machine virtuelle est présent sur l’ordinateur virtuel et le service de l’agent est en cours d’exécution. Veuillez réessayer l’opération de sauvegarde et en cas d’échec, contactez le support technique de Microsoft.|   Cette erreur est levée lorsque l’agent de la machine virtuelle est obsolète. Voir la rubrique « Mise à jour de l’Agent de machine virtuelle » ci-dessous pour mettre à jour de l’agent de la machine virtuelle.|
|Sauvegarde |Ordinateur virtuel n’existe pas. -Vérifiez que cet ordinateur virtuel existe ou sélectionnez une machine virtuelle différente. | Cela se produit lorsque l’ordinateur virtuel principal est supprimé, mais la stratégie de sauvegarde continue à rechercher un ordinateur virtuel effectuer la sauvegarde. Pour corriger cette erreur : <ol><li> Recréer l’ordinateur virtuel avec le même nom et le même nom de groupe de ressources [nom de service cloud]<br>(OR)<br></li><li>Arrêter la protection de machines virtuelles sans supprimer les données de sauvegarde. [Plus de détails](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Sauvegarde |Échec de l’exécution de la commande. -Une autre opération est en cours sur cet élément. Veuillez patienter jusqu'à la fin de l’opération précédente et recommencez |Une sauvegarde existante ou un travail de restauration de l’ordinateur virtuel est en cours d’exécution, et un nouveau travail ne peut pas être démarré pendant l’exécution de la tâche existante.|
| Sauvegarde | Copie de disques durs virtuels à partir de la sauvegarde coffre-fort dépassé - Veuillez recommencer l’opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft. | Cela se produit lorsqu’il existe trop de données à copier. Veuillez vérifier si vous avez moins de 16 disques de données. |
| Sauvegarde | La sauvegarde a échoué avec une erreur interne : Veuillez recommencer l’opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft | Vous pouvez obtenir cette erreur pour 2 raisons : <ol><li> Il existe un problème transitoire dans accès au stockage de la machine virtuelle. Veuillez vérifier [Statut d’Azure](https://azure.microsoft.com/en-us/status/) afin de vérifier si les questions d’en cours de calcul/stockage/réseau dans la région. Veuillez réessayer le problème de sauvegarde post est atténuée. <li>La machine virtuelle d’origine a été supprimée et par conséquent sauvegarde ne peut pas être effectuée. Pour conserver les données de sauvegarde pour un ordinateur virtuel supprimé, mais les sauvegarde erreurs stop, ôter la protection de la machine virtuelle et choisissez l’option Conserver les données. Cette commande arrête le planning de sauvegarde et également les messages d’erreur périodique. |
| Sauvegarde | Impossible d’installer les Services de récupération Azure extension sur l’élément sélectionné - Agent de la machine virtuelle est un pré-requis pour l’Extension des Services Azure récupération. Installez l’agent Azure VM et relancer l’opération d’enregistrement | <ol> <li>Vérifiez si l’agent de la machine virtuelle a été installé correctement. <li>Assurez-vous que l’indicateur sur la configuration de la machine virtuelle est correctement définie.</ol> [Pour en savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent de machine virtuelle et comment valider l’installation de l’agent de machine virtuelle. |
| Sauvegarde | Installation de l’extension a échoué avec l’erreur « COM + n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator | Cela signifie généralement que le service COM + n’est pas en cours d’exécution. Pour plus d’informations sur la résolution de ce problème, contactez le support technique de Microsoft. |
| Sauvegarde | Opération de capture instantanée a échoué avec l’erreur de l’opération VSS « ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur à partir du Panneau de configuration. | Désactiver BitLocker pour tous les lecteurs sur l’ordinateur virtuel et observer si VSS est résolu |
| Sauvegarde | Les machines virtuelles ayant des disques durs virtuels stockés sur le stockage de prime ne sont pas pris en charge pour la sauvegarde | Aucun |
| Sauvegarde | Machine virtuelle Azure introuvable. | Cela se produit lorsque l’ordinateur virtuel principal est supprimé, mais la stratégie de sauvegarde continue à rechercher un ordinateur virtuel effectuer la sauvegarde. Pour corriger cette erreur : <ol><li>Recréer l’ordinateur virtuel avec le même nom et le même nom de groupe de ressources [nom de service cloud] <br>(OR) <li> Désactiver la protection pour cet ordinateur virtuel afin que les tâches de sauvegarde ne seront pas créés. </ol> |
| Sauvegarde | Agent de l’ordinateur virtuel n’est pas présent sur l’ordinateur virtuel - installez le préalable indispensable, agent de l’ordinateur virtuel et redémarrez l’opération. | [Pour en savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et comment valider l’installation de l’agent de machine virtuelle. |

## <a name="jobs"></a>Travaux

| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Annuler le travail | L’annulation n’est pas pris en charge pour ce type de tâche - Veuillez patienter jusqu'à ce que le travail est terminé. | Aucun |
| Annuler le travail | La tâche n’est pas dans un état annulable - Veuillez patienter jusqu'à ce que le travail est terminé. <br>OU<br> La tâche sélectionnée n’est pas dans un état annulable - Veuillez attendre la fin du travail.| Selon toute probabilité la tâche est presque terminée ; Veuillez patienter jusqu'à la fin du travail |
| Annuler le travail | Impossible d’annuler le travail car il n’est pas en cours, l’annulation est pris en charge uniquement pour les travaux qui sont en cours. Veuillez annuler de tentative sur une progression en travail. | Cela se produit en raison d’un état transitoire. Attendez une minute et réessayez l’opération d’annulation |
| Annuler le travail | Impossible d’annuler la tâche - Veuillez patienter jusqu'à ce que la tâche se termine. | Aucun |


## <a name="restore"></a>Restauration
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restauration | Restauration a échoué avec l’erreur interne du nuage | <ol><li>Service de nuage à laquelle vous essayez de restaurer est configuré avec les paramètres DNS. Vous pouvez vérifier <br>$deployment = get-AzureDeployment - ServiceName « ServiceName »-emplacement « Production » Get-AzureDns - DnsSettings $deployment. DnsSettings<br>S’il existe des adresses configuré, cela signifie que les paramètres DNS sont configurés.<br> <li>Service cloud auquel vous essayez de restaurer est configuré avec l’adresse IP réservée et machines virtuelles existantes dans le service cloud sont dans l’état arrêté.<br>Vous pouvez vérifier qu'un service en nuage a réservé IP à l’aide des applets de commande powershell à la suite :<br>$deployment = get-AzureDeployment - ServiceName « servicename »-prévention d’emplacement « Production » $ ReservedIPName <br><li>Vous essayez de restaurer un ordinateur virtuel avec des configurations de réseau spéciales suivantes au même service de cloud. <br>-Machines virtuelles sous configuration de l’équilibrage de la charge (internes et externes)<br>-Machines virtuelles avec plusieurs IPs réservés<br>-Machines virtuelles avec plusieurs cartes réseau<br>Veuillez sélectionner un service en nuage dans l’interface utilisateur ou reportez-vous à [restaurer des considérations](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) pour les machines virtuelles avec des configurations de réseau spécial</ol> |
| Restauration | Le nom DNS sélectionné est déjà utilisé - Veuillez spécifier un nom DNS différent et essayez à nouveau. | Le nom DNS ici désigne le nom de service cloud (se terminant généralement avec. cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un nom différent de la machine virtuelle lors de la restauration. <br><br> Notez que cette erreur est uniquement affichée aux utilisateurs du portail Azure. Réussite de l’opération de restauration via PowerShell car il restaure les disques uniquement et que vous ne crée pas la machine virtuelle. Lorsque la machine virtuelle est explicitement créée par vous après l’opération de restauration de disque soit confronté à l’erreur. |
| Restauration | La configuration de réseau virtuel spécifié n’est pas correcte, veuillez spécifier une configuration de réseau virtuel différent et réessayez. | Aucun |
| Restauration | Le service nuage spécifié utilise une adresse IP réservée, ce qui ne correspond à la configuration de l’ordinateur virtuel en cours de restauration - spécifiez un service nuage différent qui n’utilise pas de IP réservée ou choisir un autre point de restauration pour restaurer à partir de. | Aucun |
| Restauration | Service cloud a atteint la limite du nombre de points de terminaison d’entrée - recommencez l’opération en spécifiant un service nuage différent ou à l’aide d’un point de terminaison existant. | Aucun |
| Restauration | Sauvegarde compte de stockage en chambre forte et cible se trouvent dans deux régions différentes - Assurez-vous que le compte de stockage spécifié dans l’opération de restauration est dans la même région Azure comme le coffre-fort de sauvegarde. | Aucun |
| Restauration | Compte de stockage spécifié pour l’opération de restauration n’est pas pris en charge - comptes de stockage uniquement Basique/Standard avec localement redondants ou les paramètres de réplication redondante geo sont pris en charge. Veuillez sélectionner un compte de stockage pris en charge | Aucun |
| Restauration | Type de compte de stockage spécifié pour l’opération de restauration n’est pas en ligne, assurez-vous que le compte de stockage spécifié dans l’opération de restauration est en ligne | Cela peut se produire en raison d’une erreur transitoire dans le stockage Azure ou en raison d’une panne. Veuillez choisir un autre compte de stockage. |
| Restauration | Quota de groupe de ressources a été atteinte, veuillez supprimer certains groupes de ressources à partir d’Azure portal ou contacter le support Azure pour augmenter les limites. | Aucun |
| Restauration | Sous-réseau sélectionné n’existe pas - Veuillez sélectionner un sous-réseau qui existe | Aucun |


## <a name="policy"></a>Stratégie de
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Créer des stratégies | Impossible de créer la stratégie - Veuillez réduire les choix de rétention pour poursuivre la configuration de la stratégie. | Aucun |


## <a name="vm-agent"></a>Agent de machine virtuelle

### <a name="setting-up-the-vm-agent"></a>Configuration de l’Agent de la machine virtuelle
En règle générale, l’Agent de la machine virtuelle est déjà présent dans les ordinateurs virtuels qui sont créés à partir de la galerie d’Azure. Cependant, les ordinateurs virtuels qui sont migrés à partir de centres de données sur site n’aurait pas l’Agent VM est installé. Pour ces machines virtuelles, l’Agent de la machine virtuelle doit être installé explicitement. Plus d’informations sur [l’installation de l’agent de la machine virtuelle sur un ordinateur virtuel existant](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Pour les machines virtuelles de Windows :

- Téléchargez et installez l' [agent MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin des privilèges d’administrateur pour terminer l’installation.
- [Mise à jour de la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.

Pour les machines virtuelles de Linux :

- Installez dernier [agent de Linux](https://github.com/Azure/WALinuxAgent) à partir de github.
- [Mise à jour de la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.


### <a name="updating-the-vm-agent"></a>Mise à jour de l’Agent de la machine virtuelle
Pour les machines virtuelles de Windows :

- Mise à jour de l’Agent de la machine virtuelle est aussi simple que de réinstaller les [binaires de l’Agent de la machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Toutefois, vous devez vous assurer qu’aucune opération de sauvegarde n’est en cours d’exécution alors que l’Agent de l’ordinateur virtuel est en cours de mise à jour.

Pour les machines virtuelles de Linux :

- Suivez les instructions de [Mise à jour de l’Agent d’une machine virtuelle de Linux](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Validation de l’installation de l’Agent de machine virtuelle
Comment faire pour vérifier la version de l’Agent de la machine virtuelle sur les ordinateurs virtuels de Windows :

1. Ouvrez une session sur l’ordinateur virtuel Azure et naviguez jusqu’au dossier *C:\WindowsAzure\Packages*. Vous devriez trouver le fichier WaAppAgent.exe.
2. Cliquez droit sur le fichier, cliquez sur **Propriétés**et puis sélectionnez l’onglet **Détails** . Le champ Version du produit doit être 2.6.1198.718 ou supérieur

## <a name="troubleshoot-vm-snapshot-issues"></a>Résoudre les problèmes de Snapshot de machine virtuelle
Sauvegarde de l’ordinateur virtuel s’appuie sur l’émission de commande de stockage sous-jacent. N’ayant ne pas accès au stockage ou retard dans l’exécution de la tâche de capture instantanée, la sauvegarde peut échouer. Les éléments suivants peuvent entraîner une défaillance de tâche de capture instantanée.

1. L’accès au stockage réseau est bloqué à l’aide du NSG<br>
   Pour en savoir plus sur la manière d' [Activer l’accès réseau](backup-azure-vms-prepare.md#2-network-connectivity) au stockage à l’aide d’une création de listes autorisées d’adresses IP ou via un serveur proxy.
2.  Les ordinateurs virtuels avec sauvegarde de Sql Server configuré peuvent entraîner de retard de la tâche instantanés <br>
    Par défaut VM problèmes de sauvegarde VSS sauvegarde complète sur les ordinateurs virtuels de Windows. Sur les ordinateurs virtuels qui exécutent les serveurs Sql Server et Sql Server est configuré de sauvegarde, cela peut provoquer le délai dans l’exécution de la capture instantanée. Veuillez définir clé de Registre suivante si vous rencontrez des échecs de sauvegarde en raison de problèmes de capture instantanée.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  État VM rapporté de manière incorrecte, car la machine virtuelle est arrêtée dans RDP.  <br>
    Si vous avez arrêté l’ordinateur virtuel dans RDP, vérifiez dans le portail qu’état de la machine virtuelle est correctement reflété. Si ce n’est pas le cas, arrêtez l’ordinateur virtuel dans un portail à l’aide des option de « Arrêt » dans le tableau de bord de machine virtuelle.
4.  Si plus de la part de quatre VM le même service de cloud, configurez plusieurs stratégies de sauvegarde pour les temps de sauvegarde donc pas que plus de quatre sauvegardes de machines virtuelles sont démarrés en même temps. Essayer de propager le début une heure de différence entre les stratégies de sauvegarde. 
5.  Machine virtuelle est en cours d’exécution au processeur/mémoire haute.<br>
    Si l’ordinateur virtuel est en cours d’exécution à haute usage(>90%) ou de la mémoire, la tâche de capture instantanée est en file d’attente, différé et s’obtient par la suite dépassé. Essayez de sauvegarde à la demande dans de telles situations.

<br>

## <a name="networking"></a>Mise en réseau
Comme toutes les extensions, extension de sauvegarde ont besoin d’accéder à l’internet public pour fonctionner. N’ayant ne pas accès à l’internet public peut se manifester de plusieurs façons :

- L’installation de l’extension peut échouer.
- Les opérations de sauvegarde (par exemple, snapshot de disque) peuvent échouer.
- Affichage de l’état de l’opération de sauvegarde peut échouer.

Le besoin de résoudre des adresses internet publiques a été articulé [ici](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Vous devez vérifier la configuration de DNS pour la VNET et de s’assurer que les URI Azure peuvent être résolu.

Une fois la résolution de noms est effectuée correctement, accès aux adresses IP Azure doit également être fourni. Pour débloquer l’accès à l’infrastructure d’Azure, suivez une des opérations suivantes :

1. Liste d’autorisation du centre de données Azure plages d’IP.
    - Obtenir la liste de [Centre de données Azure IPs](https://www.microsoft.com/download/details.aspx?id=41653) à liste d’autorisation.
    - Débloquer les adresses IP à l’aide de l’applet de commande [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Exécuter cette applet de commande au sein de la machine virtuelle d’Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécutée en tant qu’administrateur).
    - Ajouter des règles à la NSG (si vous en avez en place) pour autoriser l’accès aux adresses IP.
2. Créer un chemin d’accès pour le trafic HTTP
    - Si vous avez quelques restrictions réseau en place (groupe de sécurité réseau, par exemple) déployer un serveur de proxy HTTP pour acheminer le trafic. Étapes à suivre pour déployer un serveur de HTTP Proxy peuvent trouver [ici](backup-azure-vms-prepare.md#2-network-connectivity).
    - Ajouter des règles à la NSG (si vous en avez en place) pour autoriser l’accès à INTERNET à partir du HTTP Proxy.

>[AZURE.NOTE] DHCP doit être activé à l’intérieur de l’invité de l’ordinateur virtuel IaaS sauvegarde fonctionne.  Si vous avez besoin d’un IP privée statique, vous devez le configurer par l’intermédiaire de la plate-forme. L’option DHCP à l’intérieur de la machine virtuelle devrait être activée.
Permet d’afficher plus d’informations sur la [définition d’une adresse IP privée interne statique](../virtual-network/virtual-networks-reserved-private-ip.md).
