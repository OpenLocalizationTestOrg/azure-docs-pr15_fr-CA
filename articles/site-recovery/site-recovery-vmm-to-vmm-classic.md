<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM | Microsoft Azure"
    description="Cet article décrit comment répliquer les VM Hyper-V dans les nuages VMM sur un site secondaire de VMM avec récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM

> [AZURE.SELECTOR]
- [Azure portal](site-recovery-vmm-to-vmm.md)
- [Portail classique](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment répliquer les machines virtuelles de Hyper-V sur les serveurs hôtes Hyper-V qui sont gérés dans des nuages VMM à site secondaire de VMM à l’aide de la récupération de Site Azure.

L’article inclut les conditions préalables, vous montre comment configurer un coffre-fort de récupération de Site, installez le fournisseur de récupération de Site Azure sur la source de VMM serveurs cibles, inscrivez les serveurs dans le coffre-fort, configurer les paramètres de protection pour les nuages VMM et puis permettre une protection pour les ordinateurs virtuels Hyper-V. Terminez en tester le basculement sur incident pour vous assurer que tout fonctionne comme prévu.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Architecture

L’image ci-dessous affiche les canaux de communication différents et ports utilisés par la récupération de Site Azure pour l’orchestration et la réplication

![Topologie de E2E](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Avant de commencer

Assurez-vous que vous avez ces conditions préalables en place :

**Conditions préalables** | **Détails**
--- | ---
**Azure**| Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**VMM** | Vous avez besoin d’au moins un serveur VMM.<br/><br/>Le serveur VMM doit être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour cumulatives.<br/><br/>Si vous souhaitez définir la protection avec un seul serveur VMM, vous devez au moins deux clouds configurés sur le serveur.<br/><br/>Si vous souhaitez déployer la protection avec deux serveurs VMM, chaque serveur doit avoir au moins un nuage configuré sur le serveur VMM principal que vous souhaitez protéger et un nuage configuré sur le serveur VMM secondaire que vous souhaitez utiliser pour la protection et la restauration<br/><br/>Tous les nuages VMM doivent avoir le profil de fonctionnalité de Hyper-V.<br/><br/>Le nuage source que vous souhaitez protéger doit contenir un ou plusieurs groupes d’hôtes VMM.<br/><br/>Pour en savoir plus sur la configuration des nuages VMM dans [procédure pas à pas : création des nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) sur le blog de Keith Mayer.
**Hyper-V** | Vous avez besoin d’un ou plusieurs des serveurs hôtes Hyper-V dans les groupes d’hôtes VMM principales et secondaires et un ou plusieurs ordinateurs virtuels sur chaque serveur d’hôte Hyper-V.<br/><br/>Les serveurs Hyper-V hôte et cible doivent être en cours d’exécution au moins Windows Server 2012 avec le rôle Hyper-V et installé les dernières mises à jour.<br/><br/>N’importe quel serveur Hyper-V qui contient les ordinateurs virtuels que vous souhaitez protéger doit se trouver dans un nuage VMM.<br/><br/>Si vous utilisez Hyper-V dans un cluster, notez que broker de cluster n’est pas créé automatiquement si vous disposez d’un cluster de base d’adresses IP statique. Vous devez configurer le service broker de cluster manuellement. [En savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) dans une entrée de blog de Marie Finn.
**Mappage réseau** | Vous pouvez configurer le mappage réseau pour vous assurer que les machines virtuelles dupliquées sont optimales placés sur les serveurs hôtes Hyper-V secondaires après le basculement, et qu’ils peuvent se connecter à des réseaux d’ordinateur virtuel appropriés. Si vous ne configurez pas mappage réseau, VM de réplica n’est pas connecté à n’importe quel réseau après le basculement.<br/><br/>Pour définir le mappage de réseau au cours du déploiement, assurez-vous que les ordinateurs virtuels sur le serveur hôte source Hyper-V sont connectés à un réseau de VMM VM. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage. < br /<br/>Le nuage cible sur le serveur VMM secondaire que vous utilisez pour la restauration doit avoir un réseau de machine virtuelle correspondant configuré, et il doit à son tour être lié à un réseau logique correspondant qui est associé avec le nuage cible.<br/><br/>[En savoir plus](site-recovery-network-mapping.md) sur le mappage réseau.
**Mappage du stockage** | Par défaut lorsque vous répliquez un ordinateur virtuel sur un serveur hôte de Hyper-V source sur un serveur hôte de Hyper-V cible, les données répliquées sont stockées dans l’emplacement par défaut qui est indiqué pour l’hôte Hyper-V de cible dans le Gestionnaire Hyper-V. Pour plus de contrôle sur lequel sont stockées les données répliquées, vous pouvez configurer le mappage de stockage<br/><br/> Pour configurer un mappage de stockage, vous devez paramétrer des classifications de stockage sur la source et la cible des serveurs VMM avant de commencer le déploiement. [En savoir plus](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Étape 1 : Créer un coffre-fort de récupération de Site

1. Connectez-vous au [Portail de gestion](https://portal.azure.com) à partir du serveur VMM, que vous souhaitez enregistrer.

2. Développez **Services de données** > **Services de récupération** et cliquez sur le **Coffre de récupération de Site**.

3. Cliquez sur **créer une nouvelle** > **Création rapide**.

4. Dans la zone **nom**, entrez un nom convivial pour identifier le coffre-fort.

5. Dans la **zone** sélectionner la zone géographique pour la chambre forte. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Cliquez sur **créer coffre-fort**.

    ![Créer le coffre-fort](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

Dans la barre d’état, vérifiez que le coffre-fort a été créé. Le coffre-fort apparaît comme **actif** sur la page principale des Services de récupération.

## <a name="step-2-generate-a-vault-registration-key"></a>Étape 2 : Générer une clé d’enregistrement de coffre-fort

Générer une clé d’enregistrement dans la chambre forte. Après le téléchargement du fournisseur de récupération de Site Azure et l’installer sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans la chambre forte.

1. Dans la page **Services de restauration** , cliquez sur le coffre pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. Dans la liste déroulante, sélectionnez **entre deux sites VMM sur site**.
3. **Préparer les serveurs VMM**, cliquez sur **Générer un fichier clé d’enregistrement**. Le fichier de clé est généré automatiquement et est valable 5 jours après sa création. Si vous accédez pas le portail Azure à partir du serveur VMM, vous devez copier ce fichier sur le serveur.

    ![Clé d’enregistrement](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Étape 3 : Installer le fournisseur de récupération de Site Azure

4. Dans la page de **Démarrage rapide** , dans **VMM de préparer les serveurs**, cliquez sur **Télécharger Microsoft Azure récupération fournisseur de Site pour une installation sur les serveurs VMM** pour obtenir la dernière version du fichier d’installation de fournisseur.

2. Exécutez ce fichier sur le serveur VMM de source.

    >[AZURE.NOTE] Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois l’installer sur un nœud actif et terminer l’installation pour inscrire le serveur VMM dans la chambre forte. Puis, installez le fournisseur sur les autres nœuds. Notez que si vous mettez à niveau le fournisseur vous devez mettre à niveau sur tous les nœuds, car ils doivent tous exécuter la même version du fournisseur.

3. Le programme d’installation effectue quelques autorisation de demandes et de **Vérifier les conditions requises** pour arrêter le service VMM pour commencer l’installation du fournisseur. Le Service VMM sera redémarré automatiquement lorsque le programme d’installation se termine. Si vous installez sur un cluster VMM que vous serez invité à arrêter le rôle Cluster.

4. Mise à jour de **Microsoft** , vous pouvez choisir dans les mises à jour. Avec ce paramètre activé fournisseur mises à jour seront installées en fonction de votre stratégie de Microsoft Update.

    ![Mises à jour Microsoft](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. L’emplacement d’installation est défini sur ** <SystemDrive>\Program Files\Microsoft Manager\bin de System Center 2012 R2\Virtual Machine**. Cliquez sur le bouton Installer pour démarrer l’installation du fournisseur.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Après avoir installé le fournisseur cliquez sur **Enregistrer** pour enregistrer le serveur dans la chambre forte.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
9. Dans la zone **nom de coffre-fort**, vérifiez le nom du coffre-fort dans lequel le serveur est inscrit. Cliquez sur *suivant*.

    ![Inscription du serveur](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. **Connexion à Internet** de spécifier comment le fournisseur en cours d’exécution sur le serveur VMM se connecte à Internet. Sélectionnez **se connecter avec les paramètres de serveur proxy existants** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur. Lorsque vous configurez les paramètres de proxy personnalisés, un test s’exécute pour vérifier la connexion du proxy.
    - Si vous utilisez un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez entrer les détails de proxy, y compris l’adresse proxy et le port.
    - URL suivantes doit être accessible depuis le serveur VMM et sur les hôtes Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Autoriser les adresses IP décrits dans des [Plages d’IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) et HTTPS protocole (443). Vous seriez obligé de plages IP de liste blanche de la région Azure que vous prévoyez d’utiliser et de l’ouest des États-Unis.
    - Si vous utilisez un proxy personnalisé qu'un compte RunAs de VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifié. Configurer le serveur proxy afin que ce compte peut s’authentifier avec succès. Les paramètres de compte de VMM RunAs peuvent être modifiés dans la console VMM. Pour ce faire, ouvrez l’espace de **paramètres** , développez **sécurité**, cliquez sur **Exécuter en tant que comptes**et puis modifiez le mot de passe pour DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.


8. Dans la **Clé d’enregistrement**, sélectionnez la clé que vous avez téléchargé à partir de la récupération de Site Azure copié sur le serveur VMM.


10.  Le paramètre de cryptage est uniquement utilisé lors de la réplication de machines virtuelles de Hyper-V dans les nuages VMM à Azure. Si vous utilisez la réplication sur un site secondaire n’est pas utilisée.

11.  Dans **nom du serveur**, spécifiez un nom convivial pour identifier le serveur VMM dans la chambre forte. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
12.  Dans **les métadonnées de nuage synchroniser** , sélectionnez si vous souhaitez synchroniser les métadonnées pour tous les nuages sur le serveur VMM à la chambre forte. Cette action ne doit se produire qu’une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les nuages, vous pouvez laisser ce paramètre désactivé et synchroniser chaque nuage individuellement dans les propriétés du nuage dans la console VMM.

13.  Cliquez sur **suivant** pour terminer le processus. Après l’inscription, les métadonnées du serveur VMM sont récupérée lors de la récupération de Site Azure. Le serveur s’affiche dans **Les serveurs VMM** > **serveurs** dans la chambre forte.

    ![Serveurs](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure peut également être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur un Server CORE de Windows Server 2012 R2.

1. Téléchargez la clé de l’enregistrement de fichiers et d’installation de fournisseur dans un dossier. Par exemple, C:\ASR.
2. Arrêtez le Service Virtual Machine Manager de System Center
3. Extraire le programme d’installation du fournisseur en exécutant ces commandes à partir d’une invite de commandes avec des privilèges **d’administrateur** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installez le fournisseur en exécutant :

        C:\ASR> setupdr.exe /i

5. Enregistrer le fournisseur en exécutant :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Où les paramètres sont :

 - **/Credentials**: un paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
 - **/FriendlyName**: un paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
 - **/EncryptionEnabled**: un paramètre facultatif que vous devez utiliser uniquement dans le Gestionnaire de mémoire virtuelle Azure scénario si vous avez besoin de cryptage de vos machines virtuelles au repos dans Azure. Vérifiez que le nom du fichier que vous fournissez a une extension **.pfx** .
 - **/ProxyAddress**: paramètre optionnel qui spécifie l’adresse du serveur proxy.
 - **/ProxyPort**: paramètre optionnel qui spécifie le port du serveur proxy.
 - **/proxyUsername**: paramètre optionnel qui spécifie le nom d’utilisateur Proxy (si le proxy exige une authentification).
 - **/proxyPassword**: paramètre optionnel qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si le proxy exige une authentification).  

## <a name="step-4-configure-cloud-protection-settings"></a>Étape 4 : Configurez le nuage paramètres de protection

Une fois les serveurs VMM sont enregistrés, vous pouvez configurer les paramètres de protection de nuage. Si vous avez activé l’option **synchroniser les données de nuage avec le coffre-fort** lorsque vous avez installé le fournisseur donc tous les nuages sur le serveur VMM seront affiche dans l’onglet **Éléments de protection** dans la chambre forte. Si vous n’avez pas peuvent synchroniser un nuage spécifique Azure récupération de Site dans l’onglet **Général** de la page de propriétés de nuage dans la console VMM.

![Nuage publié](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. Sur la page de démarrage rapide, cliquez sur **configurer la protection pour les nuages VMM**.
2. Sous l’onglet **Nuages VMM** , sélectionnez le nuage que vous souhaitez configurer et cliquez sur l’onglet **Configuration** .
3. Dans la **cible**, sélectionnez **VMM**.
4. Dans l' **emplacement cible**, sélectionnez le serveur VMM sur site qui gère le nuage que vous souhaitez utiliser pour la restauration.
4. Dans le **nuage de la cible**, sélectionnez le nuage cible que vous souhaitez utiliser pour le basculement des ordinateurs virtuels dans le nuage de source. Notez que :

    - Nous vous conseillons de sélectionner un nuage cible qui répond aux exigences de récupération pour les ordinateurs virtuels que vous permettent de protéger.
    - Un nuage ne peut appartenir qu’à une paire unique de nuage, en tant que principal ou un nuage de cible.

5. Dans la zone **fréquence de copie**, spécifiez la fréquence à laquelle données doivent être synchronisées entre les 5he les emplacements source et cible. Notez que ce paramètre est pertinent uniquement lorsque l’hôte Hyper-V exécute Windows Server 2012 R2. Pour les autres serveurs un paramètre par défaut de cinq minutes est utilisé.
6. Dans les **points de récupération supplémentaires**, spécifiez si vous souhaitez créer des points de récupération supplémentaires. La valeur par défaut zéro indique que seul le dernier point de récupération pour un ordinateur virtuel principal est stocké sur un serveur hôte de réplica. Notez que l’activation de plusieurs points de récupération nécessite le stockage supplémentaire pour les captures instantanées qui sont stockées au niveau de chaque point de récupération. Par défaut, les points de restauration sont créés toutes les heures, afin que chaque point de récupération contient la valeur d’une heure de données. La valeur de point de récupération que vous affectez pour la machine virtuelle dans la console VMM ne doit pas être inférieure à la valeur que vous attribuez dans la console de récupération de Site Azure.
7. Dans **fréquence des snapshots cohérents au niveau application**, spécifiez la fréquence de création des snapshots cohérents avec l’application. Hyper-V utilise deux types de captures instantanées, une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et une capture instantanée cohérente de l’application qui prend un instantané de point-à-temps des données de l’application à l’intérieur de l’ordinateur virtuel. Des snapshots cohérents avec l’application utilisent Volume Shadow Copy Service (VSS) pour s’assurer que les applications sont dans un état cohérent lorsque l’instantané est pris. Notez que si vous activez des snapshots cohérents avec l’application, elle affectera les performances des applications s’exécutant sur des machines virtuelles de source. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.

    ![Configurer les paramètres de protection](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. **Compression de transfert de données**, de spécifier si les données répliquées transférées doivent être compressées.
9. Dans **authentification**, spécifiez comment le trafic est authentifié entre les serveurs de l’hôte Hyper-V principaux et de restauration. Sélectionnez HTTPS à moins d’avoir un environnement Kerberos configuré du travail. Récupération de Site Azure pour configurer automatiquement des certificats pour l’authentification HTTPS. Aucune configuration manuelle n’est requise. Si vous n’activez pas Kerberos, vous servira un ticket Kerberos pour l’authentification mutuelle des serveurs hôtes. Par défaut, port 8083 et 8084 (pour les certificats) s’ouvre dans le pare-feu Windows sur les serveurs hôtes Hyper-V. Notez que ce paramètre n’est pertinent que pour les serveurs hôte Hyper-V dans Windows Server 2012 R2.
10. Dans **Port**, modifiez le numéro de port sur lequel écoutent les ordinateurs hôtes source et cible pour le trafic de réplication. Par exemple, vous pouvez modifier le paramètre si vous souhaitez appliquer la qualité de Service (QoS) réseau de bande passante pour le trafic de réplication. Vérifiez que le port n’est pas utilisé par une autre application et qu’il est ouvert dans les paramètres du pare-feu.
11. Dans **la méthode de réplication**, spécifiez comment la réplication initiale des données à partir de la source aux emplacements cibles est gérée, avant le démarrage de la réplication normale :

    - **Réseau**: copie de données sur le réseau peut être longue et gourmandes en ressources. Nous vous recommandons d’utiliser cette option si le nuage contient des machines virtuelles avec des disques durs virtuels relativement petites, et si le site principal est connecté au site secondaire via une connexion à large bande. Vous pouvez spécifier que la copie doit démarrer immédiatement, ou sélectionnez une heure. Si vous utilisez la réplication de réseau, nous vous recommandons de planifier pendant les heures creuses.
    - **Hors connexion**, cette méthode indique que la réplication initiale sera effectuée à l’aide d’un support externe. Il est utile si vous souhaitez éviter une dégradation des performances du réseau, ou pour les sites géographiquement distants. Pour utiliser cette méthode, vous spécifiez l’emplacement d’exportation sur le nuage de source et l’emplacement d’importation sur le nuage cible. Lorsque vous activez la protection d’une machine virtuelle, le disque dur virtuel est copié à l’emplacement d’exportation spécifié. Vous envoyez au site cible et le copiez à l’emplacement d’importation. Le système copie les informations importées pour les ordinateurs virtuels de réplica.

12. Sélectionnez **Supprimer le réplica Virtual Machine** pour spécifier que la machine virtuelle de réplicas doivent être supprimée si vous arrêtez la protection de l’ordinateur virtuel en sélectionnant l’option **Supprimer la protection de l’ordinateur virtuel** sur l’onglet ordinateurs virtuels des propriétés du nuage. Avec ce paramètre activé, lorsque vous désactivez la protection l’ordinateur virtuel est supprimé de la récupération de Site d’Azure, les paramètres de récupération de Site pour l’ordinateur virtuel sont supprimés dans la console VMM et la réplique est supprimée.

    ![Configurer les paramètres de protection](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Après avoir enregistré les paramètres d’une tâche est créée et peut être surveillée dans l’onglet **tâches** . Tous les serveurs hôtes Hyper-V nuage VMM source seront configurés pour la réplication. Les paramètres de nuage peuvent être modifiés dans l’onglet **configurer** . Si vous souhaitez modifier le nuage d’emplacement ou de la cible de cible vous devez supprimer la configuration cloud et puis reconfigurer le nuage.

### <a name="prepare-for-offline-initial-replication"></a>Préparation pour la réplication initiale en mode hors connexion

Vous devez effectuer les actions suivantes pour préparer pour la réplication initiale en mode hors connexion :

- Sur le serveur source, indiquez un emplacement de chemin d’accès à partir de laquelle l’exportation de données aura lieu. Attribuer le contrôle total des autorisations NTFS et de partage au service VMM sur le chemin d’exportation. Sur le serveur cible, indiquez un emplacement de chemin d’accès à partir duquel l’importation de données aura lieu. Affecter les mêmes autorisations sur ce chemin d’accès d’importation.
- Si le chemin d’accès d’importation ou d’exportation est partagée, attribuer l’appartenance au groupe administrateur, utilisateur avec pouvoir, opérateur d’impression ou opérateurs de serveur pour le compte de service VMM sur l’ordinateur distant sur lequel le partagé se trouve.
- Si vous utilisez des comptes d’exécuter en tant que pour ajouter des hôtes, sur les chemins d’accès d’importation et d’exportation, affecter en lecture et en écriture à l’exécuter en tant que comptes dans VMM.
- Les actions d’importation et d’exportation ne doivent pas être trouver sur tout ordinateur utilisé comme serveur hôte Hyper-V, car la configuration de bouclage n’est pas pris en charge par Hyper-V.
- Dans Active Directory, sur chaque Hyper-V server hôte qui contient les ordinateurs virtuels que vous souhaitez protéger, d’activer et de configurer la délégation contrainte pour autoriser les ordinateurs à distance sur laquelle les chemins d’accès d’importation et d’exportation sont situés, comme suit :
    1. Sur le contrôleur de domaine, ouvrez **Active Directory utilisateurs et ordinateurs**.
    2. Dans l’arborescence de la console, cliquez sur **nom_domaine** > **ordinateurs**.
    3. Cliquez sur le nom du serveur hôte Hyper-V > **Propriétés**.
    4. Sous l’onglet **délégation** , cliquez sur T**rouille cet ordinateur pour la délégation aux services spécifiés**.
    5. Cliquez sur **utiliser tout protocole d’authentification**.
    6. Cliquez sur **Ajouter des** > **les utilisateurs et les ordinateurs**.
    7. Tapez le nom de l’ordinateur qui héberge le chemin d’exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l’ordinateur qui héberge le chemin d’accès d’importation. Répétez cette procédure pour les autres serveurs d’hôte Hyper-V.

## <a name="step-5-configure-network-mapping"></a>Étape 5 : Configuration de mappage réseau
1. Sur la page de démarrage rapide, cliquez sur **mapper les réseaux**.
2. Sélectionnez le serveur VMM de source à partir duquel vous souhaitez mapper des réseaux, puis le serveur VMM cible auquel les réseaux seront mappés. La liste des réseaux source et de leurs réseaux cibles associés sont affichés. Une valeur vide est affichée pour les réseaux qui ne sont pas mappés actuellement.
3. Sélectionnez un réseau dans un **réseau de source** > **carte**. Le service détecte les réseaux de l’ordinateur virtuel sur le serveur cible et les affiche. Cliquez sur l’icône d’informations en regard du nom de réseau source et cible pour afficher les sous-réseaux pour chaque réseau.

    ![Configurer le mappage réseau](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. Dans la boîte de dialogue Sélectionnez un des réseaux VM à partir du serveur VMM de cible.

    ![Sélectionnez le réseau cible](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Lorsque vous sélectionnez un réseau de la cible, les nuages protégés qui utilisent le réseau source sont affichent. Réseaux de cibles disponibles qui sont associés avec les nuages de protection sont également affichés. Nous vous conseillons de sélectionner un réseau cible qui est disponible pour tous les nuages que vous utilisez pour la protection. Ou bien, vous pouvez également atteindre le serveur VMM et modifier les propriétés du nuage pour ajouter le réseau logique correspondant au réseau de la machine virtuelle que vous souhaitez choisir.
6. Cliquez sur la case à cocher pour terminer le processus de mappage. Il démarre un travail pour suivre la progression de la mise en correspondance. Vous pouvez l’afficher dans l’onglet **tâches** .


## <a name="step-6-configure-storage-mapping"></a>Étape 6 : Configuration de mappage du stockage
Par défaut lorsque vous répliquez un ordinateur virtuel sur un serveur hôte de Hyper-V source sur un serveur hôte de Hyper-V cible, les données répliquées sont stockées dans l’emplacement par défaut qui est indiqué pour l’hôte Hyper-V de cible dans le Gestionnaire Hyper-V. Pour plus de contrôle sur lequel sont stockées les données répliquées, vous pouvez configurer des mappages de stockage comme suit :



1. Permet de définir des classifications de stockage sur les serveurs VMM à la fois la source et la cible. [En savoir plus](https://technet.microsoft.com/library/gg610685.aspx). Classifications doivent être disponibles pour les serveurs hôtes Hyper-V dans des nuages de source et cible. Classifications n’est pas nécessaire d’avoir le même type de stockage. Par exemple, vous pouvez mapper une classification de source qui contient les partages SMB pour une classification de cible qui contient CSVs.
2. Vous pouvez créer des mappages après que les classifications sont en place. Pour ce faire, sur la page de **Démarrage rapide** > **mapper le stockage**.
3. Cliquez sur l’onglet **stockage** > **mapper des classifications de stockage**.
4. Sous l’onglet **mappage des classifications de stockage** , sélectionnez classifications sur la source et VMM serveurs cibles. Enregistrer vos paramètres.

    ![Sélectionnez le réseau cible](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Étape 7 : Activer la protection de l’ordinateur virtuel
Une fois que les serveurs, les réseaux et les nuages sont correctement configurés, vous pouvez activer la protection pour les ordinateurs virtuels dans le nuage.

1. Dans l’onglet **ordinateurs virtuels** dans le nuage dans lequel se trouve l’ordinateur virtuel, cliquez sur **Activer la protection** > **Ajouter des ordinateurs virtuels**.
2. Dans la liste des ordinateurs virtuels dans le nuage, sélectionnez celui que vous souhaitez protéger.

    ![Activer la protection de l’ordinateur virtuel](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Suivre la progression de l’action d’activation de la Protection dans l’onglet **tâches** , y compris la réplication initiale. Après l’exécution de la tâche de Protection de finaliser l’ordinateur virtuel est prêt pour le basculement. Une fois la protection est activée et les machines virtuelles sont répliqués, vous serez en mesure de les afficher dans Azure.

    ![Tâche de protection d’ordinateur virtuel](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] Vous pouvez également activer la protection des machines virtuelles dans la console VMM. Dans la barre d’outils dans l’onglet **Récupération de Site Azure** dans les propriétés de l’ordinateur virtuel, cliquez sur **Activer la Protection** .

### <a name="on-board-existing-virtual-machines"></a>Machines virtuelles existantes intégrés

Si vous avez des ordinateurs virtuels existants dans VMM qui répliquent avec Hyper-V Replica vous devrez intégré les pour la protection de la récupération de Site Azure comme suit :

1. Vérifiez que vous disposez de nuages principales et secondaires. Assurez-vous que le serveur Hyper-V qui héberge l’ordinateur virtuel se trouve dans le nuage principal et que le serveur Hyper-V qui héberge l’ordinateur virtuel de réplica se trouve dans le nuage secondaire. Assurez-vous que vous avez configuré les paramètres de protection pour les nuages. Les paramètres doivent correspondre à ceux qui sont actuellement configurés pour Hyper-V Replica. Dans le cas contraire les réplication de machine virtuelle peut ne pas fonctionne comme prévu.
2. Puis activez la protection de l’ordinateur virtuel principal. Azure de reprise et de VMM garantit que le même hôte pour la duplication et la machine virtuelle est détectée et reprise de Site Azure réutiliser et rétablir la réplication en utilisant les paramètres définis lors de la configuration de nuage.


## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération qui se compose de plusieurs machines virtuelles et exécuter un test de basculement pour le plan.  Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé.

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Sous l’onglet **Plans de récupération** , cliquez sur **Créer un Plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM source et cible. Le serveur source doit avoir les ordinateurs virtuels qui sont activés pour le basculement et la restauration. Sélectionnez **Hyper-V** pour afficher uniquement les nuages qui sont configurés pour la réplication de Hyper-V.

    ![Créer le plan de récupération](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. Dans **Sélectionner l’ordinateur virtuel**, sélectionnez groupes de réplication. Tous les ordinateurs virtuels associés au groupe de réplication est sélectionnés et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutés au groupe par défaut de plan de récupération : groupe 1. Vous pouvez ajouter plusieurs groupes si nécessaire. Notez que, une fois que les ordinateurs virtuels de réplication démarre selon l’ordre des groupes du plan de récupération.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Après une restauration plan a été créé, il apparaît dans la liste sous l’onglet **Plans de reprise** .

###<a name="run-a-test-failover"></a>Exécutez un test de basculement

1. Sous l’onglet **Plans de reprise** , sélectionnez le plan, cliquez sur **Test de basculement**.
2. Dans la page **Confirmer le basculement Test** , sélectionnez **Aucun**. Notez qu’avec cette option activée de l’échec de machines virtuelles de réplica n’est pas connecté à un réseau. Cela va tester que l’ordinateur virtuel bascule comme prévu mais qu’elle ne teste pas votre environnement de réseau de réplication. Observez comment [exécuter un test de basculement](site-recovery-failover.md#run-a-test-failover) pour plus de détails sur l’utilisation des différentes options de mise en réseau.
3. La machine virtuelle de test sera créée sur le même hôte que l’hôte sur lequel la machine virtuelle de réplica existe. Il est ajouté dans le même nuage dans lequel se trouve l’ordinateur virtuel de réplica.

### <a name="run-a-recovery-plan"></a>Exécuter un plan de récupération
Après la réplication, la machine virtuelle de réplica ne peut-être pas une adresse IP qui est le même que l’adresse IP de l’ordinateur virtuel principal. Machines virtuelles met à jour le serveur DNS qu’ils utilisent après leur démarrage. Vous pouvez également ajouter un script pour mettre à jour le serveur DNS pour garantir une mise à jour en temps voulu.

#### <a name="script-to-retrieve-the-ip-address"></a>Script pour extraire l’adresse IP
Exécuter cet exemple de script pour récupérer l’adresse IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Script de mise à jour DNS
Exécuter cet exemple de script pour mettre à jour le DNS, en spécifiant l’adresse IP que vous avez récupéré à l’aide de l’exemple de script précédent.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Informations confidentielles relatives à la récupération de Site

Cette section fournit des informations de confidentialité supplémentaires pour le service de récupération de Site Microsoft Azure (« Service »). Pour afficher la déclaration de confidentialité pour les services de Microsoft Azure, consultez [Déclaration de confidentialité de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Fonction : enregistrement**

- **Ce qu’il fait**: inscrit le serveur avec le service afin que les ordinateurs virtuels peuvent être protégés.
- **Les informations collectées**: après l’enregistrement du Service de collecte, traite et transmet les informations de certificat de gestion de serveur VMM qui a désigné pour la reprise après sinistre en utilisant le nom du Service du serveur VMM et le nom de la machine virtuelle nuages sur votre serveur VMM.
- **Utilisation des informations**:
    - Certificat de gestion, il est utilisé pour aider à identifier et d’authentifier le serveur VMM enregistré pour l’accès au Service. Le Service utilise la clé publique du certificat pour sécuriser un jeton seulement le serveur VMM enregistré peut y accéder. Le serveur doit utiliser ce jeton pour accéder aux fonctionnalités du Service.
    - Nom du serveur VMM : nom du serveur VMM l’est nécessaire pour identifier et communiquer avec le serveur VMM approprié sur lequel se trouvent les nuages.
    - Nuage de noms provenant du serveur VMM, le nom de nuage est requis lors de l’utilisation du cloud Service appariement/unpairing une fonctionnalité décrite ci-dessous. Lorsque vous décidez d’appariement de votre nuage à partir d’un centre de données principal avec un autre nuage dans le centre de récupération des données, les noms de tous les nuages à partir du centre de récupération des données sont présentées.

- **Choix**: cette information est une partie essentielle du processus d’inscription du Service, car il permet de vous et le Service pour identifier le serveur VMM pour lequel vous souhaitez protéger Azure récupération de Site, ainsi que pour identifier le serveur VMM enregistré correct. Si vous ne souhaitez pas envoyer ces informations au Service, n’utilisez pas ce Service. Si vous enregistrez votre serveur et que vous souhaitez ensuite annuler son inscription, vous pouvez le faire en supprimant les informations sur le serveur VMM à partir du portail de services (qui est le portail Azure).

**Fonction : Activer la protection de la récupération de Site Azure**

- **Ce qu’il fait**: le fournisseur de récupération de Site Azure installé sur le serveur VMM est le canal d’échange pour communiquer avec le Service. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Après avoir installé le fournisseur, la fonctionnalité de « Récupération de Datacenter » obtient activée dans la console Administrateur VMM. Les ordinateurs virtuels nouveaux ou existants dans un nuage peut activer une propriété appelée « Récupération de centre de données » pour protéger l’ordinateur virtuel. Une fois que cette propriété est définie, le fournisseur envoie le nom et l’ID de l’ordinateur virtuel pour le Service. La protection virtuelle est activée par la technologie de réplication Windows Server 2012 ou de Windows Server 2012 R2 Hyper-V. Les machine virtuelle de données répliquées à partir d’un hôte Hyper-V à l’autre (généralement situé dans un centre de données différent de « récupération »).

- **Les informations collectées**: le Service de collecte, traite et transmet les métadonnées pour la machine virtuelle, ce qui inclut le nom, ID, réseau virtuel et le nom de nuage à laquelle elle appartient.

- **Utilisation des informations**: le Service utilise les informations ci-dessus pour remplir les informations de l’ordinateur virtuel sur votre portail de Service.

- **Choix**: Ceci est une partie essentielle du service et ne peut pas être désactivé. Si vous ne souhaitez pas que ces informations envoyées au Service, ne pas activer la protection de récupération de Site Azure pour toutes les machines virtuelles. Notez que toutes les données envoyées par le fournisseur pour le Service est envoyé via HTTPS.

**Fonctionnalité : Plan de récupération**

- **Ce qu’il fait**: cette fonctionnalité vous aide à créer un plan d’orchestration pour le centre de données de « récupération ». Vous pouvez définir l’ordre dans lequel les ordinateurs virtuels ou un groupe de machines virtuelles doit être démarré sur le site de récupération. Vous pouvez également spécifier des scripts automatisés pour être ou l’exécution manuelle de toute action à entreprendre, au moment de la récupération pour chaque machine virtuelle. Basculement (traité dans la section suivante) est généralement déclenché au niveau du Plan de récupération pour récupération coordonnée.

- **Les informations collectées**: le Service de collecte, traite et transmet les métadonnées pour le plan de récupération, y compris la machine virtuelle et les métadonnées de tous les scripts d’automatisation et notes d’action manuelle.

- **Utilisation des informations**: les métadonnées décrite ci-dessus sont utilisée pour générer le plan de récupération dans votre portail de Service.

- **Choix**: Ceci est une partie essentielle du service et ne peut pas être désactivé. Si vous ne souhaitez pas que ces informations envoyées au Service, créer des Plans de reprise dans ce Service.

**Fonctionnalité : Mappage réseau**

- **Ce qu’il fait**: cette fonctionnalité vous permet de mapper des informations de réseau à partir du centre de données principal vers le centre de récupération des données. Lorsque les ordinateurs virtuels sont récupérés sur le site de reprise, ce mappage vous permet de l’établissement de la connectivité réseau pour les.

- **Les informations collectées**: dans le cadre de la fonctionnalité de mappage réseau, le Service de collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (primaire et centre de données).

- **Utilisation des informations**: le Service utilise les métadonnées pour remplir votre portail de Service dans lequel vous pouvez mapper les informations réseau.

- **Choix**: Ceci est une partie essentielle du Service et ne peut pas être désactivé. Si vous ne souhaitez pas que ces informations envoyées au Service, n’utilisez pas la fonctionnalité de mappage réseau.

**Fonction : Basculement - test planifié et non planifié,**

- **Ce qu’il fait**: cette fonctionnalité permet le basculement d’un ordinateur virtuel à partir d’un centre de données géré de VMM à un autre centre de données géré de VMM. L’action de basculement est déclenchée par l’utilisateur sur le portail du Service. Les raisons possibles pour un basculement incluent un événement non planifié (par exemple dans le cas d’un disaster0 naturel ; un événement planifié (par exemple datacenter équilibrage de la charge), un basculement sur incident test (par exemple une simulation de plan de récupération).

Le fournisseur sur le serveur VMM reçoit des notifications de l’événement à partir du Service et exécute une action de basculement sur incident sur l’hôte Hyper-V via des interfaces VMM. Basculement sur incident réel de l’ordinateur virtuel à partir d’un hôte Hyper-V à l’autre (généralement en cours d’exécution dans un centre de données différent de « récupération ») est géré par la technologie de réplication Windows Server 2012 ou de Windows Server 2012 R2 Hyper-V. Une fois le basculement terminé, le fournisseur est installé sur le serveur VMM du centre de données « récupération » envoie les informations de réussite pour le Service.

- **Les informations collectées**: le Service utilise les informations ci-dessus pour remplir l’état des informations action basculement sur votre portail de Service.

- **Utilisation des informations**: le Service utilise les informations ci-dessus comme suit :

    - Certificat de gestion, il est utilisé pour aider à identifier et d’authentifier le serveur VMM enregistré pour l’accès au Service. Le Service utilise la clé publique du certificat pour sécuriser un jeton seulement le serveur VMM enregistré peut y accéder. Le serveur doit utiliser ce jeton pour accéder aux fonctionnalités du Service.
    - Nom du serveur VMM : nom du serveur VMM l’est nécessaire pour identifier et communiquer avec le serveur VMM approprié sur lequel se trouvent les nuages.
    - Nuage de noms provenant du serveur VMM, le nom de nuage est requis lors de l’utilisation du cloud Service appariement/unpairing une fonctionnalité décrite ci-dessous. Lorsque vous décidez d’appariement de votre nuage à partir d’un centre de données principal avec un autre nuage dans le centre de récupération des données, les noms de tous les nuages à partir du centre de récupération des données sont présentées.

- **Choix**: Ceci est une partie essentielle du service et ne peut pas être désactivé. Si vous ne souhaitez pas que ces informations envoyées au Service, n’utilisez pas ce Service.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez exécuté un basculement sur incident de test pour vérifier que votre environnement fonctionne comme prévu, [en savoir plus sur](site-recovery-failover.md) les types différents de basculement.
