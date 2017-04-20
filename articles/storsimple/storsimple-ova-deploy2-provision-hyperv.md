<properties
   pageTitle="Déployer le tableau virtuel de StorSimple - fourniture d’Hyper-V"
   description="Ce deuxième didacticiel dans déploiement de tableau virtuel de StorSimple implique la mise en service d’un périphérique virtuel dans Hyper-V."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Déployer le tableau virtuel de StorSimple - provisionner un tableau virtuel dans Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel de mise en service s’applique à la version de Microsoft Azure StorSimple tableaux virtuels (également appelés périphériques virtuels locaux de StorSimple ou StorSimple des périphériques virtuels) en cours d’exécution mars 2016 disponibilité générale (GA). Ce didacticiel explique comment configurer un tableau virtuel de StorSimple sur un système hôte exécutant Hyper-V sur Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Cet article s’applique au déploiement de réseaux virtuels de StorSimple dans Azure portal classique, ainsi que le gouvernement Microsoft Azure Cloud.

Vous devez disposer de privilèges d’administrateur à la disposition, vous configurez un périphérique virtuel. L’installation de la mise en service et initiale peut prendre 10 minutes environ.


## <a name="provisioning-prerequisites"></a>Mise en service de composants requis

Vous trouverez ici les conditions requises pour configurer un périphérique virtuel sur un système hôte exécutant Hyper-V sur Windows Server 2008 R2, Windows Server 2012 R2 ou Windows Server 2012.

### <a name="for-the-storsimple-manager-service"></a>Pour le service Gestionnaire de StorSimple

Avant de commencer, assurez-vous que :

-   Vous avez terminé toutes les étapes de [Préparation du portail pour tableau virtuel de StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Vous avez téléchargé l’image du périphérique virtuel pour Hyper-V à partir du portail Azure. Pour plus d’informations, consultez [étape 3 : téléchargement de l’image du périphérique virtuel](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] Le logiciel s’exécutant sur la baie virtuelle StorSimple est utilisable uniquement conjointement avec le service Gestionnaire de Storsimple.

### <a name="for-the-storsimple-virtual-device"></a>Pour le périphérique virtuel StorSimple

Avant de déployer un périphérique virtuel, assurez-vous que :

-   Vous avez accès à un système hôte Hyper-V sur Windows Server 2008 R2 ou version ultérieure qui peut être utilisé à une disposition d’un périphérique.

-   Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre périphérique virtuel :

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau.

    -   Un disque virtuel de 500 Go pour les données du système.

### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer, passez en revue la configuration réseau requise pour déployer un périphérique virtuel de StorSimple et de configurer le réseau de centre de données de manière appropriée. Pour plus d’informations, voir [Configuration requise de la mise en réseau de tableau virtuel de StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Pas à pas la mise en service

Pour mettre en service et de se connecter à un périphérique virtuel, vous devez effectuer les opérations suivantes :

1.  Assurez-vous que le système hôte dispose des ressources suffisantes pour satisfaire aux exigences minimales de périphérique virtuel.

2.  Configurer un périphérique virtuel dans votre hyperviseur.

3.  Démarrez le périphérique virtuel et obtenir l’adresse IP.

Chacune de ces étapes est expliquée dans les sections suivantes.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Étape 1 : Vérifiez que le système hôte aux exigences minimales de périphérique virtuel

Pour créer un périphérique virtuel, vous devez :

-   Le rôle Hyper-V est installé sur Windows Server 2008 R2 SP1, Windows Server 2012 R2 ou Windows Server 2012.

-   Gestionnaire de Microsoft Hyper-V sur un client Microsoft Windows connecté à l’hôte.

Vous devez vous assurer que le matériel sous-jacent (système hôte) sur lequel vous créez le périphérique virtuel est en mesure de dédier les ressources suivantes sur le périphérique virtuel :

- Un minimum de 4 cœurs.
- Au moins 8 Go de RAM.
- Une interface réseau.
- Un disque virtuel de 500 Go pour les données du système.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Étape 2 : Configurer un périphérique virtuel de l’hyperviseur

Effectuez les étapes suivantes pour configurer un périphérique dans votre hyperviseur.

#### <a name="to-provision-a-virtual-device"></a>Pour configurer un périphérique virtuel

1.  Sur votre hôte Windows, copiez l’image du périphérique virtuel sur un lecteur local. Il s’agit de l’image (disque dur virtuel ou VHDX) que vous avez téléchargé via le portail Azure. Prenez note de l’emplacement où vous avez copié l’image que vous utilisez cela plus loin dans la procédure.

2.  Ouvrez le **Gestionnaire de serveur**. Dans le coin supérieur droit, cliquez sur **Outils** et sélectionnez **Gestionnaire Hyper-V**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Si vous exécutez Windows Server 2008 R2, ouvrez le Gestionnaire Hyper-V. Dans le Gestionnaire de serveur, cliquez sur **rôles > Hyper-V > Gestionnaire Hyper-V**.

1.  Dans le **Gestionnaire Hyper-V**, dans le volet de l’arborescence, cliquez sur le nœud de votre système pour ouvrir le menu contextuel, puis cliquez sur **Nouveau** > **Machine virtuelle**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Sur la page **avant de commencer** de l’Assistant Nouvel ordinateur virtuel, cliquez sur **suivant**.

1.  Dans la page **emplacement et spécifier un nom** , fournir un **nom** pour votre périphérique virtuel. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Dans la page **génération de spécifier** , choisissez le type d’image de périphériques et puis cliquez sur **suivant**. Cette page ne s’affiche pas si vous utilisez Windows Server 2008 R2.

    * Choisissez la **génération 2** si vous avez téléchargé une image .vhdx de Windows Server 2012, ou une version ultérieure.
    * Choisir **1 de génération** si vous avez téléchargé une image .vhd pour Windows Server 2008 R2 ou version ultérieure.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  Dans la page **attribuer de la mémoire** , spécifiez au moins une **mémoire de démarrage** de **8192 Mo**, ne pas activer la mémoire dynamique, puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Dans la page **configurer le réseau** , spécifiez le commutateur virtuel qui est connecté à Internet, puis sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Sur la page du **disque dur virtuel de connexion** , choisissez **d’utiliser un disque dur virtuel existant**, spécifiez l’emplacement de l’image de périphérique virtuel (.vhdx ou .vhd) et puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Passez en revue le **Résumé** et puis cliquez sur **Terminer** pour créer l’ordinateur virtuel. Mais ne pas aller plus loin encore : vous devez toujours ajouter quelques cœurs d’UC et un second lecteur. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Pour répondre à la configuration minimale requise, vous devez 4 cœurs. Pour ajouter des processeurs virtuels, votre système hôte sélectionné dans la fenêtre du **Gestionnaire Hyper-V** , dans le volet de droite sous la liste des **Machines virtuelles**, recherchez l’ordinateur virtuel que vous venez de créer. Sélectionnez et cliquez sur le nom de l’ordinateur et sélectionnez **paramètres**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Dans la page **paramètres** , dans le volet gauche, cliquez sur **processeur**. Dans le volet de droite, définissez le **nombre de processeurs virtuels** à 4 (ou plus). Cliquez sur **Appliquer**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Pour répondre à la configuration minimale requise, vous devez également ajouter un disque de données virtuelles de 500 Go. Dans la page **paramètres** :

    1.  Dans le volet gauche, sélectionnez le **Contrôleur SCSI**.
    2.  Dans le volet de droite, sélectionnez le **Disque dur,** puis sur **Ajouter**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Sur la page du **disque dur** , sélectionnez l’option de **disque virtuel** et cliquez sur **Nouveau**. Cela démarre l' **Assistant Création de disque dur virtuel**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Sur la page **avant de commencer** de l’Assistant Création de disque dur virtuel, cliquez sur **suivant**.

1.  Dans la **page Choisir le Format du disque**, acceptez l’option par défaut du format **VHDX** . Cliquez sur **suivant**. Vous ne verrez pas cet écran si vous exécutez Windows Server 2012 R2 ou Windows Server 2008 R2.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Dans la **page Choisir un Type de disque**, définissez les type de disque dur virtuel **de taille dynamique** (recommandé). Si vous choisissez le disque de **taille fixe** , il peut également fonctionner, mais vous devrez peut-être attendre un certain temps. Nous vous recommandons de ne pas utiliser l’option de **différenciation** . Cliquez sur **suivant**. Notez que **l’extension dynamique** est la valeur par défaut dans Windows Server 2012 R2 et de Windows Server 2012. Dans Windows Server 2008 R2, la valeur par défaut est de **taille fixe**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Dans la page **spécifier le nom et l’emplacement** , fournir un **nom** ainsi **emplacement** (vous pouvez parcourir une) pour le disque de données. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Sur la page de **Configuration de disque** , sélectionnez l’option **créer un nouveau disque dur virtuel vide** et spécifiez la taille de **500 Go** (ou plus). 500 Go est la configuration minimale requise, vous pouvez toujours configurer un disque de plus grande capacité. Notez que vous ne peut pas développer ou réduire le disque une fois mis en service. Pour plus d’informations sur la taille du disque à disposition, consultez la section dimensionnement du document de [meilleures pratiques](storsimple-ova-best-practices.md#configuration-best-practices) . Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Sur la page de **Résumé** , examinez les détails de votre disque de données virtuel et si vous êtes satisfait, cliquez sur **Terminer** pour créer la disquette. L’Assistant se ferme et un disque dur virtuel sera ajouté à votre ordinateur.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Vous renvoie à la page **paramètres** . Cliquez sur **OK** pour fermer la page **paramètres** et revenir à la fenêtre du Gestionnaire Hyper-V.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Étape 3 : Démarrer le périphérique virtuel et obtenir l’adresse IP

Procédez comme suit pour démarrer votre périphérique virtuel et s’y connecter.

#### <a name="to-start-the-virtual-device"></a>Pour démarrer le périphérique virtuel

1.  Démarrez le périphérique virtuel.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Une fois que le périphérique est en cours d’exécution, sélectionnez le périphérique, cliquez le bouton droit et sélectionnez **se connecter**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Il se peut que vous deviez attendre 5 à 10 minutes pour le périphérique soit prêt. Un message d’état s’affiche sur la console pour indiquer la progression. Une fois que le périphérique est prêt, passez à **l’Action**. Appuyez sur la touche `Ctrl + Alt + Delete` pour vous connecter à l’unité virtuelle. L’utilisateur par défaut est *StorSimpleAdmin* et le mot de passe *mot de passe1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Pour des raisons de sécurité, le mot de passe administrateur expire en premier. Vous devrez modifier le mot de passe.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Entrez un mot de passe contenant au moins 8 caractères. Le mot de passe doit satisfaire au moins 3 des 4 suivants : caractères majuscules, minuscules, numériques et spéciaux. Entrez de nouveau le mot de passe pour le confirmer. Vous serez averti que le mot de passe a été modifié.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Une fois le mot de passe est modifié, le périphérique virtuel peut redémarrer. Attendez que le périphérique pour démarrer.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    La console Windows PowerShell du périphérique est affichée avec une barre de progression.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Les étapes 6 à 8 s’appliquent uniquement lors de l’amorçage dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes, puis passez à l’étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l’écran suivante.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    Vous devez maintenant configurer le réseau.

1.  Utilisez le `Get-HcsIpAddress` commande pour répertorier les interfaces réseau activées sur votre périphérique virtuel. Si votre périphérique possède une seule interface réseau activée, le nom par défaut assigné à cette interface est `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Utilisez le `Set-HcsIpAddress` applet de commande pour configurer le réseau. Un exemple est illustré ci-dessous :

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Après l’installation initiale est terminée et que le périphérique a démarré, vous verrez le texte de bannière du périphérique. Prenez note de l’adresse IP et l’URL affichée dans le texte de la bannière à gérer le périphérique. Vous allez utiliser cette adresse IP pour se connecter à l’interface utilisateur de votre périphérique virtuel web et terminer l’installation locale et l’enregistrement.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Facultatif) Effectuez cette étape uniquement si vous déployez votre périphérique dans le nuage de gouvernement. Vous allez maintenant activer le mode États-Unis FIPS Federal Information Processing Standard () sur votre périphérique. La norme FIPS 140 définit les algorithmes cryptographiques approuvés pour une utilisation par les systèmes d’ordinateur gouvernement fédéral nous pour la protection des données sensibles.
    1. Pour activer le mode FIPS, exécutez l’applet de commande suivante :

        `Enter-HcsFIPSMode`

    2. Redémarrez votre périphérique après avoir activé le mode FIPS pour que les validations de chiffrement prennent effet.

        > [AZURE.NOTE] Vous pouvez activer ou désactiver le mode FIPS sur votre périphérique. Remplacement du périphérique entre mode FIPS et non-FIPS n’est pas pris en charge.

Si votre périphérique ne répond pas à la configuration minimale requise, vous verrez une erreur dans le texte de la bannière (voir ci-dessous). Vous devrez modifier la configuration du périphérique de sorte qu’il dispose des ressources appropriées à la configuration minimale requise. Vous pouvez ensuite redémarrer et de se connecter au périphérique. Reportez-vous à la configuration minimale requise de [étape 1 : Assurez-vous que le système hôte répond aux exigences minimales périphérique virtuel](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Si vous êtes confronté à d’autres erreurs lors de la configuration initiale à l’aide de l’interface utilisateur de web local, consultez les flux de travail suivant dans [Gérer votre tableau virtuel StorSimple à l’aide de l’interface utilisateur de web local](storsimple-ova-web-ui-admin.md).

-   Exécutez les tests de diagnostic pour [résoudre les problèmes d’installation de l’interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Package de journaux de génération et affichage des fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icône de vidéo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez configurer un tableau virtuel de StorSimple dans Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

-   [Définir votre tableau virtuel de StorSimple sous la forme d’un serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)

-   [Définir votre tableau virtuel StorSimple comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)
