<properties
    pageTitle="Réplication en local des machines virtuelles VMware ou des serveurs physiques vers un site secondaire | Microsoft Azure"
    description="Cet article permet de répliquer des ordinateurs virtuels VMware ou Windows/Linux serveurs physiques sur un site secondaire avec récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Répliquer les machines virtuelles VMware sur site ou des serveurs physiques vers un site secondaire


## <a name="overview"></a>Vue d’ensemble

InMage Scout de récupération de Site Azure fournit une réplication en temps réel entre les sites de VMware sur site. InMage Scout est inclus dans les abonnements aux services de récupération de Site Azure.


## <a name="prerequisites"></a>Conditions préalables

**Compte Azure**: vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.


## <a name="step-1-create-a-vault"></a>Étape 1 : Création d’un coffre-fort
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur New > Gestion > sauvegarde et restauration de Site (OMS). Vous pouvez également cliquer sur Parcourir > coffre-fort de Services de récupération > Ajouter.
3. Dans la zone **nom** , spécifiez un nom convivial pour identifier le coffre-fort. Si vous avez plus d’un abonnement, sélectionnez un d'entre eux.
4. Dans le **groupe de ressources** , créez un nouveau groupe de ressources ou sélectionnez-en une existante. Permet de spécifier une région Azure pour compléter les champs obligatoires. 
5.  Dans **emplacement**, sélectionnez la zone géographique pour la chambre forte. Pour vérifier les régions pris en charge, consultez [Récupération de Site Azure tarification](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Si vous souhaitez accéder rapidement le coffre-fort du tableau de bord cliquez sur fixer au tableau de bord, puis sur Créer.
6. Le nouveau coffre-fort s’affiche sur le tableau de bord > toutes les ressources et sur les Services de récupération principal coffres-forts lame.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Étape 2 : Configurer le coffre-fort et télécharger les composants Scout d’InMage
7. Dans les Services de récupération de lame de coffres-forts Sélectionnez votre coffre-fort et cliquez sur paramètres.
8. Dans les **paramètres** > **Mise en route** cliquez sur **Restauration du Site** > étape 1 : **Préparation de l’Infrastructure** > **objectif de Protection**.
9. Dans **l’objectif de Protection** permet au site de reprise et sélectionnez Oui, avec l’hyperviseur VMware vSphere. Puis cliquez sur OK.
10. Dans **le programme d’installation de Scout**, cliquez sur la clé pour télécharger InMage Scout 8.0.1 GA logiciel et d’enregistrement. Les fichiers d’installation pour tous les composants requis se trouvent dans le fichier .zip téléchargé.


## <a name="step-3-install-component-updates"></a>Étape 3 : Installer les mises à jour du composant

Découvrez les plus récentes [mises à jour](#updates). Vous allez installer les fichiers de mise à jour sur les serveurs dans l’ordre suivant :

1. Serveur RX s’il existe
2. Configuration des serveurs
3. Serveurs de traitement
3. Serveurs cible principale
4. serveurs vContinuum
5. Serveur source (serveur Linux et Windows)

Installez les mises à jour comme suit :

1. Téléchargez le fichier .zip de [mettre à jour](https://aka.ms/asr-scout-update4) . Ce fichier .zip contient les fichiers suivants :

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - Bits pour update4 UA RHEL5, OL5, OL6, SUSE 10, SUSE 11 : UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Extrayez les fichiers .zip.<br>
3. **Pour le RX serveur**: copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** vers le serveur de réception et de l’extraire. Dans le dossier d’extraction, **exécution/installation**.<br>
4. **Pour le serveur de processus/serveur de configuration**: copie **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** vers le serveur de la configuration et le serveur de traitement. Double-cliquez sur pour l’exécuter.<br>
5. **Serveur cible de maître pour Windows**: pour mettre à jour l’agent unifié, copiez **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** vers le serveur cible principale. Double-cliquez dessus pour l’exécuter. Notez que l’agent unifié est également applicable au serveur source. Installez-le sur le serveur source, ainsi que mentionné plus loin dans cette liste.<br>
7. **Pour le serveur vContinuum**: copier des **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** sur le serveur de vContinuum.  Assurez-vous que vous avez fermé l’Assistant vContinuum. Double-cliquez sur le fichier pour l’exécuter.<br>
8. **Serveur de cible principale pour Linux**: pour mettre à jour l’agent unifié, copiez **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur cible principale et l’extraire. Dans le dossier d’extraction, **exécution/installation**.<br>
9. **Serveur de source de Windows**: pour mettre à jour l’agent unifié, copiez **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** sur le serveur source. Double-cliquez dessus pour l’exécuter.<br>
10. **Serveur de source pour Linux**: pour mettre à jour l’agent unifié, copier la version correspondante du fichier de l’UA sur le serveur Linux et de l’extraire. Dans le dossier d’extraction, **exécution/installation**.  Exemple : Pour serveur de 6,7 64 bits RHEL, copiez **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur et l’extraire. Dans le dossier d’extraction, **exécution/installation**.

## <a name="step-4-set-up-replication"></a>Étape 4 : Configurer la réplication
1. Configurer la réplication entre la source et cible les sites de VMware.
2. Pour obtenir des instructions, utilisez la documentation InMage Scout est téléchargée avec le produit. Vous pouvez également accéder à la documentation comme suit :

    - [Notes de publication](https://aka.ms/asr-scout-release-notes)
    - [Matrice de compatibilité](https://aka.ms/asr-scout-cm)
    - [Guide de l’utilisateur](https://aka.ms/asr-scout-user-guide)
    - [Guide de l’utilisateur RX](https://aka.ms/asr-scout-rx-user-guide)
    - [Guide d’installation rapide](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Mises à jour

### <a name="azure-site-recovery-scout-801-update-4"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 4
La mise à jour de Scout 4 est une mise à jour cumulative. Il possède tous les correctifs de clic1 jusqu'à ce que les améliorations et les nouveaux correctifs de bogue suivants et update3.

**Nouveau support de plate-forme** 

- Prise en charge a été ajoutée pour vCenter/vSphere 6.0, 6.1 et 6.2
- Prise en charge a été ajoutée pour les systèmes d’exploitation Linux suivants
    - Red Hat Enterprise Linux (RHEL) 7.0, 7.1 et 7.2 
    - CentOS 7.0, 7.1 et 7.2
    - Red Hat Enterprise Linux (RHEL) 6.8
    - CentOS 6.8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** est fourni avec le package de base Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Télécharger Scout GA de portail comme indiqué dans [l’étape 1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault).

**Correctifs de bogues et améliorations** 

- Arrêt amélioré pour suivant Linux OSes et des clones éviter les problèmes de re-synchronisation indésirables.
    - Red Hat Enterprise Linux (RHEL) 6.x
    - Oracle Linux (OL) 6.x
- Pour Linux, terminer l’accès à un dossier dans le répertoire d’installation de l’agent unifié sont désormais restreint uniquement à l’utilisateur local.
- Sur Windows délai de problème lors de l’envoi de marque de livre commun cohérence distribuées sur très chargé comme clusters SQL et le Point de partage des applications distribuées.
- Ajout du connexes correctif de programme d’installation de base CX.
- Lien de téléchargement de VMware vCLI 6.0 est ajouté au programme d’installation de base maître de Windows cible.
- Ajouter plus de chèques et de journaux pour les modifications des configurations de réseau pendant le basculement et les exercices de récupération après incident.
- Les informations de rétention parfois ne sont pas signalées pour le CX.  
- Avec cluster physique, redimensionner une opération via l’Assistant de vContinuum de volume échoue lors de la réduction du volume source s’est produit.
- Protection du cluster a échoué avec l’erreur « Impossible de trouver la signature de disque » lorsque le disque de cluster est disque PRDM.
- cxps panne du serveur de transport en raison de l’exception hors-limites. 
- Nom du serveur et les colonnes IP sont désormais redimensionnables dans la page d’installation push de l’Assistant de vContinuum.
- Améliorations de l’API RX
    - Fournit cinq dernières disponible la cohérence des points communs (balises garantie uniquement).
    - Fournit la capacité et les détails de l’espace libre pour tous les périphériques protégés.
    - Fournit l’état de pilote Scout sur le serveur source. 
    
>[AZURE.NOTE] 
>
>- Package de base de **InMage_Scout_Standard_8.0.1_GA.zip** a maintenant mis à jour l’installateur CX base **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** et maître de Windows cible base **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Pour toutes les installations de nouveau utiliser les bits de GA CX et maître de Windows cible nouvelles.
>- Mise à jour 4 peut être appliqué directement sur la version 8.0.1 GA.
>- Le serveur de configuration et les mises à jour de la réception ne peut pas être annulées après que elles sont appliquées sur le système.

### <a name="azure-site-recovery-scout-801-update-3"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 3
Mise à jour 3 inclut les correctifs de bogues et les améliorations suivantes :

- Le serveur de configuration et RX Impossible d’inscrire dans le coffre-fort de la récupération de Site lorsqu’ils sont derrière le serveur proxy.
- Le nombre d’heures que l’objectif de point de récupération (RPO) n’est pas remplie pas mise à jour de l’état de santé.
- Le serveur de configuration synchronise pas avec le RX lorsque les détails du matériel ESX ou les détails de réseau contiennent des caractères UTF-8.
- Les contrôleurs de domaine Windows Server 2008 R2 ne pas démarrer après la récupération.
- Synchronisation hors connexion ne fonctionne pas comme prévu.
- Après le basculement de la machine virtuelle (VM), suppression de paires de réplication reste bloquée dans l’UI CX pendant une longue période, et les utilisateurs ne peuvent pas effectuer le retour arrière ou reprendre l’opération.
- Ensemble instantané des opérations qui sont réalisées par la tâche de cohérence ont été optimisées afin de réduire l’application déconnecte comme clients SQL.
- Les performances de l’outil de cohérence (VACP.exe) a été améliorée en réduisant l’utilisation de la mémoire qui est nécessaire pour créer des instantanés de Windows.
- L’installation par diffusion installer service se bloque lorsque le mot de passe est supérieure à 16 caractères.
- vContinuum n’est pas la vérification et demander de nouvelles informations d’identification de vCenter lorsque les informations d’identification sont modifiées.
- Sous Linux, le Gestionnaire de cache cible principale (cachemgr) ne télécharge pas les fichiers du serveur de traitement, qui se traduit par la limitation de paires de réplication.
- Lorsque l’ordre de disque de cluster (MSCS) de basculement physique n’est pas le même sur tous les nœuds, la réplication n’est pas définie pour certains des volumes de cluster.
<br/>Notez que le cluster doit être reprotected pour profiter de ce correctif.  
- La fonctionnalité SMTP ne fonctionne pas comme prévu après que RX est mis à niveau à partir de Scout 7.1 Scout 8.0.1.
- Plus les statistiques ont été ajoutées dans le journal pour l’opération de restauration suivre le temps qu’il a prises pour la compléter.
- Prise en charge a été ajoutée pour les systèmes d’exploitation Linux sur le serveur source :
    - Mise à jour de Red Hat Enterprise Linux (RHEL) 6 7
    - Mise à jour de centOS 6 7
- L’interface de RX et le CX affiche désormais la notification de la paire, passe en mode bitmap.
- Les correctifs de sécurité suivantes ont été ajoutées dans RX :

**Description du problème**|**Procédures de mise en œuvre**
---|---
Autorisation de contourner via le paramètre falsification|Restreint l’accès aux utilisateurs non applicable.
Falsification de la demande de site à site|Mise en œuvre le concept de jeton de la page, ce qui génère de façon aléatoire pour chaque page. <br/>Avec ceci, vous verrez : <li> Il existe une seule connexion instance pour le même utilisateur.</li><li>Actualisation de la page ne fonctionne pas--il redirige vers le tableau de bord.</li>
Téléchargement de fichiers malveillants|Fichiers à accès réservé à certaines extensions. Autorisé les extensions sont : 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, journal, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml et zip.
Permanente des scripts inter-sites | Ajouter des contrôles d’entrée.


>[AZURE.NOTE]
>
>-  Toutes les mises à jour de récupération de Site sont cumulatifs. Mise à jour 3 comporte tous les correctifs de mise à jour 1 et 2 de la mise à jour. Mise à jour 3 peut être appliqué directement sur la version 8.0.1 GA.
>-  Le serveur de configuration et les mises à jour de la réception ne peut pas être annulées après que elles sont appliquées sur le système.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 2 (mise à jour 03 15 Dec)

Correctifs de mise à jour 2 sont les suivantes :

- **Serveur de configuration**: correctif pour un problème qui empêchait la fonctionnalité de contrôle de 31 jours libre de fonctionner comme prévu lorsque le serveur de configuration a été enregistré dans la récupération de Site.
- **Agent d’unifiée**: correctif pour un problème dans la mise à jour 1 qui a abouti à la mise à jour, ne pas en cours d’installation sur le serveur cible principale lorsqu’il a été mis à niveau à partir de la version 8.0 vers la version 8.0.1.


### <a name="azure-site-recovery-scout-801-update-1"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 1

Mise à jour 1 inclut les correctifs de bogue suivants et les nouvelles fonctionnalités :

- 31 jours de protection gratuite par instance de serveur. Cela vous permet de tester les fonctionnalités ou configurer un preuve de concept.
    - Toutes les opérations sur le serveur, y compris le basculement et le rétablissement, sont gratuites pour les 31 premiers jours, à partir de l’heure à laquelle un serveur est protégé d’abord avec Scout de récupération de Site.
    - À partir du jour 32nd et supérieur, chaque serveur protégé facturera au taux d’instance standard pour la protection de récupération de Site Azure à un site appartenant à un client.
    - À tout moment, le nombre de serveurs protégés qui sont actuellement pratiqué, est disponible sur la page de tableau de bord de la chambre forte de récupération de Site Azure.
- Prise en charge ajoutée pour l’Interface de ligne de commande (vCLI) de vSphere 5.5 de la mise à jour 2.
- Prise en charge pour les systèmes d’exploitation Linux sur le serveur source :
    - Mise à jour de RHEL 6 6
    - RHEL 5 mise à jour 11
    - Mise à jour de centOS 6 6
    - Mise à jour de centOS 5 11
- Correctifs des bogues à résoudre les problèmes suivants :
    - Inscription de coffre-fort échoue pour le serveur de configuration ou RX.
    - Volumes de cluster ne s’affichent pas comme prévu lorsque les ordinateurs virtuels en cluster sont reprotected lors de leur reprise.
    - Retour arrière échoue lorsque le serveur cible principale est hébergé sur un autre serveur ESXi depuis les machines virtuelles de production sur site.
    - Les autorisations de fichiers de configuration sont modifiées lorsque vous passez à la version 8.0.1, ce qui affecte les opérations de protection et d’utilisation.
    - Le seuil de resynchronisation n’est pas appliqué comme prévu, qui entraîne un comportement incohérent de la réplication.
    - Les paramètres de RPO ne s’affichent pas correctement dans l’interface de configuration de serveur. La valeur de données non compressées affiche de manière erronée la valeur compressée.
    -  L’opération de suppression ne supprime pas comme prévu dans l’Assistant de vContinuum, et que la réplication n’est pas supprimée de l’interface de configuration de serveur.
    -  Dans l’Assistant de vContinuum, le disque est automatiquement désactivée lorsque vous cliquez sur **Détails** dans la vue disque au cours de la protection des machines virtuelles MSCS.
    - Au cours du scénario physique-vers-virtuel (P2V), les services HP requis, tels que CIMnotify et CqMgHost, ne sont pas déplacés manuel de récupération de l’ordinateur virtuel. Cela entraîne des temps de démarrage supplémentaires.
    - Protection de machine virtuelle Linux échoue lorsqu’il y a plus de 26 disques sur le serveur cible principale.

## <a name="next-steps"></a>Étapes suivantes

Publier des questions sur le [forum des Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
