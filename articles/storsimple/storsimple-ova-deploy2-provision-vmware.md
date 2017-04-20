<properties
   pageTitle="Déployer le tableau virtuel de StorSimple - fourniture dans les environnements VMware"
   description="Ce didacticiel deuxième série de déploiement StorSimple Virtual Array implique la mise en service d’un périphérique virtuel de VMware."
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Déployer le tableau virtuel de StorSimple - mettre en service une baie virtuelle dans VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Vue d’ensemble 
Ce didacticiel de mise en service s’applique à la version de StorSimple les réseaux virtuels (également appelés périphériques virtuels locaux de StorSimple ou StorSimple des périphériques virtuels) en cours d’exécution mars 2016 disponibilité générale (GA). Ce didacticiel explique comment configurer et vous connecter à un tableau virtuel de StorSimple sur un système hôte VMware ESXi 5.5 et versions ultérieures. Cet article s’applique au déploiement de réseaux virtuels de StorSimple dans Azure portal classique, ainsi que le gouvernement Microsoft Azure Cloud.

Vous devez disposer de privilèges d’administrateur à disposition et vous connecter à un périphérique virtuel. L’installation de la mise en service et initiale peut prendre 10 minutes environ.


## <a name="provisioning-prerequisites"></a>Mise en service de composants requis

Vous trouverez ici les conditions requises pour configurer un périphérique virtuel sur un système hôte VMware ESXi 5.5 et versions ultérieures.

### <a name="for-the-storsimple-manager-service"></a>Pour le service Gestionnaire de StorSimple

Avant de commencer, assurez-vous que :

-   Vous avez terminé toutes les étapes de [Préparation du portail pour tableau virtuel de StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Vous avez téléchargé l’image du périphérique virtuel pour VMware à partir du portail Azure. Pour plus d’informations, consultez [étape 3 : téléchargement de l’image du périphérique virtuel](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Pour le périphérique virtuel StorSimple 

Avant de déployer un périphérique virtuel, assurez-vous que :

-   Vous avez accès à un système hôte Hyper-V (2008 R2 ou version ultérieure) qui peut être utilisé à une disposition d’un périphérique.

-   Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre périphérique virtuel :

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau.

    -   Un disque virtuel de 500 Go pour les données du système.

### <a name="for-the-network-in-datacenter"></a>Pour le réseau dans le centre de données 

Avant de commencer, assurez-vous que :

-   Vous avez examiné la configuration réseau requise pour le déploiement d’un périphérique virtuel StorSimple et configuré le réseau de centres de données selon les besoins. Pour plus d’informations, consultez [tableau virtuel de StorSimple requise](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Pas à pas la mise en service 

Pour mettre en service et de se connecter à un périphérique virtuel, vous devez effectuer les opérations suivantes :

1.  Assurez-vous que le système hôte dispose des ressources suffisantes pour satisfaire aux exigences minimales de périphérique virtuel.

2.  Configurer un périphérique virtuel dans votre hyperviseur.

3.  Démarrez le périphérique virtuel et obtenir l’adresse IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Étape 1 : Vérifier le système hôte répond aux exigences minimales périphérique virtuel

Pour créer un périphérique virtuel, vous devez :

-   Accès à un système hôte VMware ESXi Server 5.5 et versions ultérieures.

-   VMware vSphere client sur votre système pour gérer l’hôte ESXi.

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau connecté au réseau capable de routage du trafic sur Internet. La bande passante Internet minimale doit être de 5 Mbits/s pour permettre une utilisation optimale du périphérique.

    -   Un disque virtuel de 500 Go de données.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Étape 2 : Configurer un périphérique virtuel de l’hyperviseur

Effectuez les étapes suivantes pour configurer un périphérique virtuel dans votre hyperviseur.

1.  Copier l’image du périphérique virtuel sur votre système. Il s’agit de l’image que vous avez téléchargé via le portail classique Azure. 
    1.  Assurez-vous que c’est le dernier fichier image que vous avez téléchargé. Si vous avez téléchargé l’image précédemment, téléchargez-le à nouveau pour vous assurer que vous disposez la dernière image. La dernière image a deux fichiers (au lieu d’un).
    2.  Prenez note de l’emplacement où vous avez copié l’image que vous utilisez cela plus loin dans la procédure.

2.  Connectez-vous au serveur ESXi en utilisant le client vSphere. Vous devez disposer des privilèges d’administrateur pour créer un ordinateur virtuel.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  Dans le client vSphere, dans la section de stock dans le volet gauche, sélectionnez le serveur ESXi.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Vous allez tout d’abord télécharger le VMDK au serveur ESXi. Accédez à l’onglet **Configuration** dans le volet droit. Sous **matériel**, sélectionnez le **stockage**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  Dans le volet droit, sous les **magasins de données**, sélectionnez le magasin de données où vous voulez télécharger le VMDK. Le magasin de données doit avoir suffisamment d’espace libre pour les disques du système d’exploitation et des données.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Cliquez le bouton droit et sélectionnez **Parcourir le magasin de données**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Une fenêtre de **Navigateur de la banque de données** s’affiche.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Dans la barre d’outils, cliquez sur ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) icône pour créer un nouveau dossier. Spécifiez le nom du dossier et prenez note de ce dernier. Vous utiliserez ce nom de dossier ultérieurement lors de la création d’une machine virtuelle (recommandée). Cliquez sur **OK**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  Le nouveau dossier s’affiche dans le volet gauche du **Navigateur de magasin de données**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Cliquez sur l’icône de téléchargement ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) et sélectionnez **Télécharger le fichier**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Vous devez maintenant parcourir et pointez sur les fichiers VMDK que vous avez téléchargé. Il y a deux fichiers. Sélectionnez un fichier à télécharger.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Cliquez sur **Ouvrir**. Ceci va démarrer le téléchargement du fichier VMDK au magasin de données spécifié. Elle peut prendre plusieurs minutes pour que le fichier à télécharger.


1.  Une fois le téléchargement terminé, vous verrez le fichier dans le magasin de données dans le dossier que vous avez créé. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    Vous devez maintenant télécharger le fichier VMDK deuxième au même magasin de données.

1.  Revenir à la fenêtre du client vSphere. Serveur de ESXi sélectionné, avec le bouton droit et sélectionnez **nouvel ordinateur virtuel**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Une fenêtre de **créer un nouvel ordinateur virtuel** s’affiche. Dans la page **Configuration** , sélectionnez l’option **personnalisée** . Cliquez sur **suivant**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Dans la page **nom et emplacement** , spécifiez le nom de votre machine virtuelle. Ce nom doit correspondre au nom de dossier (méthode recommandée) que vous avez spécifié à l’étape 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Sur la page de **stockage** , sélectionnez un magasin de données que vous souhaitez utiliser pour configurer votre machine virtuelle.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Sur la page de la **Version de la Machine virtuelle** , sélectionnez **Version de Machine virtuelle : 8**. Notez que les versions 8 à 11 sont toutes prises en charge.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Sur la page **système d’exploitation invité** , sélectionnez un **système d’exploitation invité** **Windows**. **Version**, dans la liste déroulante, sélectionnez **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Sur la page de **processeurs** , réglez le **nombre de sockets virtuels** et le **nombre de cœurs par socket virtuel** afin que le **nombre Total de noyaux** est 4 (ou plus). Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Sur la page de la **mémoire** , spécifiez 8 Go (ou plus) de mémoire vive. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Sur la page **réseau** , spécifiez le nombre d’interfaces réseau. Le minimum requis est une interface réseau.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Sur la page de **Contrôleur SCSI** , acceptez la valeur par défaut **contrôleur LSI logique SAS**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Dans la page **Sélectionner un disque** , cliquez sur **utiliser un disque virtuel existant**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Dans la page **Sélectionner un disque existant** , sous **Chemin d’accès de disque**, cliquez sur **Parcourir**. Cette action ouvre une boîte de dialogue **Parcourir les magasins de données** . Naviguez jusqu'à l’emplacement où vous avez téléchargé le VMDK. Vous verrez maintenant qu’un seul fichier dans le magasin de données que les deux fichiers que vous avez téléchargé au départ ont été fusionnés. Sélectionnez le fichier, puis cliquez sur **OK**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Dans la page **Options avancées** , acceptez la valeur par défaut et cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Dans la page **prêt à terminer** , passez en revue tous les paramètres associés à la nouvelle machine virtuelle. Vérifiez que **Modifier les paramètres de la machine virtuelle avant la fin**. Cliquez sur **Continuer**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Sur la page de **Propriétés des ordinateurs virtuels** , dans l’onglet **matériel** , recherchez le matériel du périphérique. Sélectionnez le **nouveau disque dur**. Cliquez sur **Ajouter**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  La fenêtre **Ajout de matériel** s’affiche. Dans la page **Type de périphérique** , sous **Choisissez le type de périphérique que vous souhaitez ajouter**, sélectionnez le **disque dur** , puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Dans la page **Sélectionner un disque** , sélectionnez **créer un nouveau disque virtuel**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Dans la page **créer un disque** , modifier la **Taille du disque** de 500 Go (ou plus). Sous **Configuration du disque**, sélectionnez **Un provisionnement fin**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Dans la page **Options avancées** , acceptez la valeur par défaut.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Dans la page **prêt à terminer** , passez en revue les options de disque. Cliquez sur **Terminer**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Vous revenez ensuite à la page de propriétés de la Machine virtuelle. Un nouveau disque dur est ajouté à votre machine virtuelle. Cliquez sur **Terminer**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Avec votre ordinateur virtuel sélectionné dans le volet droit, accédez à l’onglet **Résumé** . Vérifiez les paramètres de votre machine virtuelle.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Votre machine virtuelle est maintenant configurée. L’étape suivante consiste à cet ordinateur sous tension et obtenir l’adresse IP.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Étape 3 : Démarrer le périphérique virtuel et obtenir l’adresse IP

Procédez comme suit pour démarrer votre périphérique virtuel et s’y connecter.

#### <a name="to-start-the-virtual-device"></a>Pour démarrer le périphérique virtuel

1.  Démarrez le périphérique virtuel. Dans le Gestionnaire de Configuration, vSphere dans le volet gauche, sélectionnez votre périphérique et avec le bouton droit pour afficher le menu contextuel. Sélectionnez **alimentation** , puis **Mettez sous tension**. Il doit s’allumer votre ordinateur virtuel. Vous pouvez afficher l’état dans le volet de **Tâches récentes** inférieur du client vSphere.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Les tâches de configuration prendra quelques minutes pour terminer. Une fois que le périphérique est en cours d’exécution, accédez à l’onglet **Console** . Envoyer Ctrl + Alt + Suppr pour ouvrir une session dans le périphérique. Vous pouvez également, pointez le curseur sur la fenêtre de console et appuyez sur Ctrl + Alt + ins. L’utilisateur par défaut est *StorSimpleAdmin* et le mot de passe *mot de passe1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Pour des raisons de sécurité, le mot de passe administrateur expire en premier. Vous devrez modifier le mot de passe.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Entrez un mot de passe contenant au moins 8 caractères. Le mot de passe doit contenir 3 sur 4 de ces exigences : les caractères majuscules, minuscules, numériques et spéciaux. Entrez de nouveau le mot de passe pour le confirmer. Vous serez averti que le mot de passe a été modifié.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Une fois le mot de passe est modifié, le périphérique virtuel peut redémarrer. Attendez le redémarrage terminer. La console Windows PowerShell du périphérique peut être affichée avec une barre de progression.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Les étapes 6 à 8 s’appliquent uniquement lors de l’amorçage dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes, puis passez à l’étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l’écran suivante. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    Vous devez maintenant configurer le réseau.

1.  Utilisez le `Get-HcsIpAddress` commande pour répertorier les interfaces réseau activées sur votre périphérique virtuel. Si votre périphérique possède une seule interface réseau activée, le nom par défaut assigné à cette interface est `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Utilisez le `Set-HcsIpAddress` applet de commande pour configurer le réseau. Un exemple est illustré ci-dessous :


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Après l’installation initiale est terminée et que le périphérique a démarré, vous verrez le texte de bannière du périphérique. Prenez note de l’adresse IP et l’URL affichée dans le texte de la bannière à gérer le périphérique. Vous allez utiliser cette adresse IP pour se connecter à l’interface utilisateur de votre périphérique virtuel web et terminer l’installation locale et l’enregistrement.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Facultatif) Effectuez cette étape uniquement si vous déployez votre périphérique dans le nuage de gouvernement. Vous allez maintenant activer le mode États-Unis FIPS Federal Information Processing Standard () sur votre périphérique. La norme FIPS 140 définit les algorithmes cryptographiques approuvés pour une utilisation par les systèmes d’ordinateur gouvernement fédéral nous pour la protection des données sensibles.
    1. Pour activer le mode FIPS, exécutez l’applet de commande suivante :
        
        `Enter-HcsFIPSMode`

    2. Redémarrez votre périphérique après avoir activé le mode FIPS pour que les validations de chiffrement prennent effet.

        > [AZURE.NOTE] Vous pouvez activer ou désactiver le mode FIPS sur votre périphérique. Remplacement du périphérique entre mode FIPS et non-FIPS n’est pas pris en charge.


Si votre périphérique ne répond pas à la configuration minimale requise, vous verrez une erreur dans le texte de la bannière (voir ci-dessous). Vous devrez modifier la configuration du périphérique de sorte qu’il dispose des ressources appropriées à la configuration minimale requise. Vous pouvez ensuite redémarrer et de se connecter au périphérique. Reportez-vous à la configuration minimale requise de [étape 1 : Assurez-vous que le système hôte répond aux exigences minimales périphérique virtuel](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Si vous êtes confronté à d’autres erreurs lors de la configuration initiale à l’aide de l’interface utilisateur de web local, consultez les flux de travail suivant dans [Gérer votre tableau virtuel StorSimple à l’aide de l’interface utilisateur de web local](storsimple-ova-web-ui-admin.md).

-   Exécutez les tests de diagnostic pour [résoudre les problèmes d’installation de l’interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Package de journaux de génération et affichage des fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package)...

## <a name="next-steps"></a>Étapes suivantes

-   [Définir votre tableau virtuel de StorSimple sous la forme d’un serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)

-   [Définir votre tableau virtuel StorSimple comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

