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

## <a name="discovery"></a>Découverte

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Découverte | Impossible de découvrir de nouveaux éléments - Azure de Microsoft Backup a rencontré et l’erreur interne. Attendez quelques minutes et essayez à nouveau l’opération. | Relancez le processus de découverte après 15 minutes.
| Découverte | Impossible de découvrir de nouveaux éléments : une autre opération est déjà en cours. Veuillez patienter jusqu'à ce que l’opération de découverte en cours terminée. | Aucun |

## <a name="register"></a>Enregistrer
| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Enregistrer | Nombre de disques de données liés à la machine virtuelle a dépassé la limite prise en charge - détacher des disques de données sur cet ordinateur virtuel et recommencez l’opération. Sauvegarde Azure prend en charge jusqu'à 16 disques de données attachés à une machine virtuelle Azure pour la sauvegarde | Aucun |
| Enregistrer | Microsoft Azure Backup a rencontré une erreur interne - attendre quelques minutes, puis essayez à nouveau l’opération. Si le problème persiste, contactez le Support Microsoft. | Vous pouvez obtenir cette erreur en raison d’une des configurations suivantes non pris en charge de la machine virtuelle sur LRS de la prime. <br> Stockage de prime ordinateurs virtuels peut être sauvegardé à l’aide de la chambre forte de services de récupération. [Pour en savoir plus](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| Enregistrer | Échec de l’enregistrement avec délai d’opération installation de l’Agent | Vérifiez si la version du système d’exploitation de la machine virtuelle est prise en charge. |
| Enregistrer | L’exécution de commande a échoué - une autre opération est en cours sur cet élément. Veuillez patienter jusqu'à la fin de l’opération précédente | Aucun |
| Enregistrer | Les machines virtuelles ayant des disques durs virtuels stockés sur le stockage de prime ne sont pas pris en charge pour la sauvegarde | Aucun |
| Enregistrer | Agent de l’ordinateur virtuel n’est pas présent sur l’ordinateur virtuel - installez le préalable indispensable, agent de l’ordinateur virtuel et redémarrez l’opération. | [Pour en savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et comment valider l’installation de l’agent de machine virtuelle. |

## <a name="backup"></a>Sauvegarde

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Sauvegarde | N’a pas pu communiquer avec l’agent de la machine virtuelle pour l’état de la capture instantanée. Tâche d’instantané d’ordinateur virtuel sub a expiré. -Consultez le guide de dépannage sur la façon de résoudre ce problème. | Cette erreur est générée si il y a un problème avec l’Agent de la machine virtuelle ou d’accès réseau vers l’infrastructure Azure est bloquée d’une certaine façon. En savoir plus sur les [problèmes de débogage de l’instantané d’ordinateur virtuel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Si l’agent de la machine virtuelle n’est pas dû à des problèmes, puis redémarrez la machine virtuelle. Parfois un état incorrect de la machine virtuelle peut provoquer des problèmes et de redémarrage de la machine virtuelle réinitialise cet « mauvais état » |
| Sauvegarde | La sauvegarde a échoué avec une erreur interne : Veuillez recommencer l’opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft | Veuillez Vérifiez s’il existe un problème transitoire dans accès au stockage de la machine virtuelle. Veuillez vérifier [Statut d’Azure](https://azure.microsoft.com/en-us/status/) afin de vérifier si les questions d’en cours de calcul/stockage/réseau dans la région. Veuillez réessayer le problème de sauvegarde post est atténuée. |
| Sauvegarde | N’a pas pu effectuer l’opération car la machine virtuelle n’existe plus. | Impossible d’effectuer la sauvegarde comme la machine virtuelle configurée pour la sauvegarde a été supprimée. Arrêter des sauvegardes supplémentaires en accédant à l’affichage des éléments de protection, sélectionnez l’élément protégé et cliquez sur Désactiver la Protection. Vous pouvez conserver les données en sélectionnant l’option de conserver une sauvegarde de données. Vous pouvez reprendre une protection pour cet ordinateur virtuel en cliquant sur Configurer la protection à partir de la vue enregistrée des éléments|
| Sauvegarde | Impossible d’installer les Services de récupération Azure extension sur l’élément sélectionné - Agent de la machine virtuelle est un pré-requis pour l’Extension des Services Azure récupération. Installez l’agent Azure VM et relancer l’opération d’enregistrement | <ol> <li>Vérifiez si l’agent de la machine virtuelle a été installé correctement. <li>Assurez-vous que l’indicateur sur la configuration de la machine virtuelle est correctement définie.</ol> [Pour en savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent de machine virtuelle et comment valider l’installation de l’agent de machine virtuelle. |
| Sauvegarde | L’exécution de commande a échoué - une autre opération est en cours sur cet élément. Veuillez patienter jusqu'à la fin de l’opération précédente et recommencez | Une sauvegarde existante ou un travail de restauration de l’ordinateur virtuel est en cours d’exécution, et un nouveau travail ne peut pas être démarré pendant l’exécution de la tâche existante. |
| Sauvegarde | Installation de l’extension a échoué avec l’erreur « COM + n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator | Cela signifie généralement que le service COM + n’est pas en cours d’exécution. Pour plus d’informations sur la résolution de ce problème, contactez le support technique de Microsoft. |
| Sauvegarde | Opération de capture instantanée a échoué avec l’erreur de l’opération VSS « ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur à partir du Panneau de configuration. | Désactiver BitLocker pour tous les lecteurs sur l’ordinateur virtuel et observer si VSS est résolu |
| Sauvegarde | Les machines virtuelles ayant des disques durs virtuels stockés sur le stockage de prime ne sont pas pris en charge pour la sauvegarde | Aucun |
| Sauvegarde | Machine virtuelle Azure introuvable. | Cela se produit lorsque l’ordinateur virtuel principal est supprimé, mais la stratégie de sauvegarde continue à rechercher un ordinateur virtuel effectuer la sauvegarde. Pour corriger cette erreur : <ol><li>Recréer l’ordinateur virtuel avec le même nom et le même nom de groupe de ressources [nom de service cloud] <br>(OR) <li> Désactiver la protection pour cet ordinateur virtuel afin que les sauvegardes suivantes ne seront pas déclenchées. </ol> |
| Sauvegarde | Agent de l’ordinateur virtuel n’est pas présent sur l’ordinateur virtuel - installez le préalable indispensable, agent de l’ordinateur virtuel et redémarrez l’opération. | [Pour en savoir plus](#vm-agent) sur l’installation de l’agent de machine virtuelle et comment valider l’installation de l’agent de machine virtuelle. |

## <a name="jobs"></a>Travaux
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Annuler le travail | L’annulation n’est pas pris en charge pour ce type de tâche - Veuillez patienter jusqu'à ce que le travail est terminé. | Aucun |
| Annuler le travail | La tâche n’est pas dans un état annulable - Veuillez patienter jusqu'à ce que le travail est terminé. <br>OU<br> La tâche sélectionnée n’est pas dans un état annulable - Veuillez attendre la fin du travail.| Selon toute probabilité, le travail est presque terminé ; Veuillez patienter jusqu'à la fin du travail |
| Annuler le travail | Impossible d’annuler le travail car il n’est pas en cours, l’annulation est pris en charge uniquement pour les travaux qui sont en cours. Veuillez annuler de tentative sur une progression en travail. | Cela se produit en raison d’un état transitoire. Attendez une minute et réessayez l’opération d’annulation |
| Annuler le travail | Impossible d’annuler la tâche - Veuillez patienter jusqu'à ce que la tâche se termine. | Aucun |


## <a name="restore"></a>Restauration
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restauration | Restauration a échoué avec l’erreur interne du nuage | <ol><li>Service de nuage à laquelle vous essayez de restaurer est configuré avec les paramètres DNS. Vous pouvez vérifier <br>$deployment = get-AzureDeployment - ServiceName « ServiceName »-emplacement « Production » Get-AzureDns - DnsSettings $deployment. DnsSettings<br>S’il existe des adresses configuré, cela signifie que les paramètres DNS sont configurés.<br> <li>Service cloud auquel vous essayez de restaurer est configuré avec l’adresse IP réservée et machines virtuelles existantes dans le service cloud sont dans l’état arrêté.<br>Vous pouvez vérifier qu'un service en nuage a réservé IP à l’aide des applets de commande powershell à la suite :<br>$deployment = get-AzureDeployment - ServiceName « servicename »-prévention d’emplacement « Production » $ ReservedIPName <br><li>Vous essayez de restaurer un ordinateur virtuel avec des configurations de réseau spéciales suivantes au même service de cloud. <br>-Machines virtuelles sous configuration de l’équilibrage de la charge (internes et externes)<br>-Machines virtuelles avec plusieurs IPs réservés<br>-Machines virtuelles avec plusieurs cartes réseau<br>Veuillez sélectionner un service en nuage dans l’interface utilisateur ou reportez-vous à [restaurer des considérations](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) pour les machines virtuelles avec des configurations de réseau spécial</ol> |
| Restauration | Le nom DNS sélectionné est déjà utilisé - Veuillez spécifier un nom DNS différent et essayez à nouveau. | Le nom DNS ici désigne le nom de service cloud (se terminant généralement avec. cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un nom différent de la machine virtuelle lors de la restauration. <br><br> Cette erreur est uniquement affichée aux utilisateurs du portail Azure. L’opération de restauration via PowerShell réussit, car il restaure les disques uniquement et que vous ne crée pas la machine virtuelle. Lorsque la machine virtuelle est explicitement créée par vous après l’opération de restauration de disque soit confronté à l’erreur. |
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





