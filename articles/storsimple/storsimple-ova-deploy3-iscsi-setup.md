<properties 
   pageTitle="Configuration de serveur iSCSI tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit comment effectuer la configuration initiale, inscrire votre serveur iSCSI de StorSimple et terminer l’installation du périphérique."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Déployer le tableau de Virtual StorSimple – configurer votre périphérique virtuel comme un serveur iSCSI

![flux du processus d’installation iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel de déploiement s’applique à la version de mise à disposition générale (GA) de mars 2016 en cours d’exécution Microsoft Azure StorSimple tableau virtuel (également appelé le périphérique virtuel local de StorSimple ou le périphérique virtuel de StorSimple). Ce didacticiel explique comment effectuer la configuration initiale, enregistrer votre serveur iSCSI de StorSimple, terminer l’installation du périphérique, puis créer, monter, initialiser et formater des volumes sur votre serveur iSCSI du périphérique virtuel StorSimple. Les informations de configuration StorSimple dans cet article s’applique uniquement aux tableaux de Virtual StorSimple. 

Ici, les procédures décrites durent environ 30 minutes à 1 heure. Les informations publiées dans cet article s’applique uniquement aux tableaux de Virtual StorSimple.

## <a name="setup-prerequisites"></a>Paramétrage des conditions préalables

Avant de configurer et de configurer votre périphérique virtuel StorSimple, assurez-vous que :

- Vous avez configuré un périphérique virtuel et connectés comme indiqué dans [Déploiement de StorSimple tableau virtuel - fourniture d’un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Déployer StorSimple tableau virtuel - fourniture d’un tableau virtuel de VMware](storsimple-ova-deploy2-provision-vmware.md).

- Vous disposez de la clé d’inscription de service à partir du service Gestionnaire de StorSimple que vous avez créé pour gérer les périphériques virtuels StorSimple. Pour plus d’informations, consultez **étape 2 : obtenir la clé d’inscription du service** de [Déployer StorSimple tableau virtuel - préparer le portail](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Si c’est le périphérique virtuel deuxième ou ultérieur que vous enregistrez avec un gestionnaire de StorSimple de service existant, vous devez disposer de la clé de cryptage de données de service. Cette clé a été générée lors de la première unité a été enregistrée avec succès avec ce service. Si vous avez perdu cette clé, consultez **obtenir la clé de cryptage de données de service** en cours [d’utilisation, l’interface Web pour administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Programme d’installation étape par étape 

Utilisez la procédure à suivre pour installer et configurer votre périphérique virtuel StorSimple :

-  [Étape 1 : Terminer la configuration de l’interface utilisateur web local et inscrire votre appareil](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Étape 2 : Exécutez le programme d’installation de périphérique requis](#step-2-complete-the-required-device-setup)
-  [Étape 3 : Ajouter un volume](#step-3-add-a-volume)
-  [Étape 4 : Montage, initialiser et formatage d’un volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Étape 1 : Terminer la configuration de l’interface utilisateur web local et inscrire votre appareil 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Pour terminer l’installation et inscrire l’appareil

1. Ouvrez une fenêtre de navigateur et connectez-vous à l’interface utilisateur web en tapant :

    `https://<ip-address of network interface>`

    Utiliser l’URL de connexion noté à l’étape précédente. Vous verrez une erreur indiquant qu’il existe un problème avec le certificat de sécurité du site Web. Cliquez sur **Continuer pour cette page web**.

    ![Erreur de certificat de sécurité](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Connectez-vous à l’interface utilisateur de votre périphérique virtuel web en tant que **StorSimpleAdmin**. Entrez le mot de passe d’administrateur de périphérique que vous avez modifié à l’étape 3 : démarrer le périphérique virtuel dans le [Déploiement StorSimple tableau virtuel - fourniture d’un périphérique virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Déployer StorSimple tableau virtuel - fourniture d’un périphérique virtuel de VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Page de connexion](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Vous allez être dirigé vers la page **d’accueil** . Cette page décrit les différents paramètres requis pour configurer et enregistrer le périphérique virtuel avec le service Gestionnaire de StorSimple. Notez que les **paramètres réseau**, **les paramètres de proxy Web**et **les paramètres de temps** sont facultatifs. Les seuls paramètres obligatoires sont **les paramètres de périphérique** et **les paramètres de nuage**.

    ![Page d’accueil](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Dans la page **paramètres réseau** sous **interfaces réseau**, données 0 seront automatiquement configurées pour vous. Chaque interface de réseau est défini par défaut pour obtenir une adresse IP automatiquement (DHCP). Par conséquent, une adresse IP, le sous-réseau et la passerelle seront automatiquement affectés (pour IPv4 et IPv6).

    Si vous envisagez de déployer votre périphérique en tant qu’un serveur iSCSI (pour le provisionnement du stockage de bloc), nous vous recommandons de désactiver l’option **automatiquement une adresse IP d’obtenir** et de configurer des adresses IP statiques.

    ![Page des paramètres réseau](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Si vous avez ajouté plus d’une interface de réseau au cours de la mise en service du dispositif, vous pouvez les configurer ici. Notez que vous pouvez configurer votre interface de réseau IPv4 uniquement ou en tant qu’à la fois IPv4 et IPv6. Seules les configurations IPv6 ne sont pas pris en charge.

5. Les serveurs DNS sont nécessaires car ils sont utilisés lorsque le périphérique tente de communiquer avec vos fournisseurs de service de stockage cloud ou pour résoudre votre périphérique par son nom, s’il est configuré comme un serveur de fichiers. Dans la page **paramètres réseau** sous les **serveurs DNS**:

    1. Un serveur DNS principal et secondaire sera configuré automatiquement. Si vous choisissez de configurer des adresses IP statiques, vous pouvez spécifier les serveurs DNS. Pour une disponibilité élevée, nous vous conseillons de configurer un principal et un serveur DNS secondaire.

    2. Cliquez sur **Appliquer**. Cela s’applique et valider les paramètres de réseau.

6. Dans la page **paramètres de l’appareil** :

    1. Affecter un unique **nom** à votre périphérique. Ce nom peut être de 1 à 15 caractères et peut contenir des lettres, des chiffres et des traits d’union.

    2. Cliquez sur l’icône du **serveur iSCSI** ![l’icône du serveur iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) pour le **Type** de périphérique que vous créez. Un serveur iSCSI vous permettra à provisionner le stockage de bloc.

    3. Spécifiez si vous souhaitez que ce périphérique soit à un domaine. Si votre périphérique est un serveur iSCSI, puis rejoindre le domaine est facultatif. Si vous décidez de ne pas joindre votre serveur iSCSI à un domaine, cliquez sur **Appliquer**, attendez que les paramètres à appliquer et passez à l’étape suivante.

        Si vous voulez connecter le périphérique à un domaine. Entrez un **nom de domaine**, puis cliquez sur **Appliquer**.

        > [AZURE.NOTE] Si vous joignez votre serveur iSCSI à un domaine, assurez-vous que votre tableau virtuel se trouve dans sa propre unité d’organisation (UO) Active Directory de Microsoft Azure, et aucun objet de stratégie de groupe (GPO) n’est appliqués à cette.

    5. Une boîte de dialogue s’affiche. Entrez vos informations d’identification de domaine dans le format spécifié. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Les informations d’identification de domaine vont être vérifiées. Vous verrez un message d’erreur si les informations d’identification sont incorrectes.

        ![informations d’identification](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Cliquez sur **Appliquer**. Cela s’applique et valider les paramètres du périphérique.
 
7. (Facultatif) configurer votre serveur proxy web. Configuration du proxy web est facultative, sachez que si vous utilisez un proxy web, vous ne pouvez configurer qu’il ici.

    ![configurer le proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Dans la page **proxy Web** :

    1. Fournissez l' **URL du proxy Web** dans ce format : *http://host-IP adresse* ou *numéro de FDQN:Port*. Notez que les URL HTTPS ne sont pas pris en charge.

    2. Spécifier **l’authentification** sous la forme de **base** ou **Aucune**.

    3. Si vous utilisez l’authentification, vous devez également fournir un **nom d’utilisateur** et le **mot de passe**.

    4. Cliquez sur **Appliquer**. Cela sera valider et d’appliquer les paramètres de proxy web configurés.
 
8. (Facultatif) configurer les paramètres de temps pour votre périphérique, tels que le fuseau horaire et les serveurs NTP principales et secondaires. Serveurs NTP sont nécessaires parce que le périphérique doit synchroniser l’heure afin qu’il peut authentifier avec vos fournisseurs de services cloud.

    ![Paramètres de temps](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Dans la page **paramètres de temps** :

    1. Dans la liste déroulante, sélectionnez le **fuseau horaire** en fonction de la zone géographique dans laquelle le périphérique est en cours de déploiement. Le fuseau horaire par défaut de votre périphérique est le fichier PST. Votre périphérique utilise ce fuseau horaire pour toutes les opérations planifiées.

    2. Spécifier un **serveur NTP principal** pour votre périphérique, ou acceptez la valeur par défaut de time.windows.com. Assurez-vous que votre réseau autorise le trafic NTP passer de votre centre de données à Internet.

    3. Si vous le souhaitez spécifier un **serveur NTP secondaire** pour votre périphérique.

    4. Cliquez sur **Appliquer**. Cela sera valider et d’appliquer les paramètres de temps configurée.

9. Configurer les paramètres de nuage pour votre périphérique. Dans cette étape, vous terminez la configuration de périphérique local et puis enregistrez le périphérique avec votre service de gestionnaire de StorSimple.

    1. Entrez la **clé d’inscription du Service** que vous avez dans **étape 2 : obtenir la clé d’inscription du service** de [Déployer StorSimple tableau virtuel - préparer le portail](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Si ce n’est pas le périphérique que vous enregistrez avec ce service, vous devez fournir la **clé de cryptage de données de Service**. Cette clé est obligatoire avec la clé d’inscription de service pour enregistrer des périphériques supplémentaires avec le service Gestionnaire de StorSimple. Pour plus d’informations, reportez-vous à la section pour [obtenir la clé de cryptage de données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) sur votre interface utilisateur de site web local.

    3. Cliquez sur **Enregistrer**. Cela va redémarrer le périphérique. Vous devrez peut-être attendre 2 à 3 minutes avant que le périphérique est correctement enregistré. Une fois que le périphérique a redémarré, vous affichera la page de connexion.

       ![Dispositif de Registre](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Retourner au portail Azure classique. Dans la page des **périphériques** , vérifiez que le périphérique est connecté au service en consultant l’état. L’état du périphérique doit être **actif**.

    ![Page de périphériques](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Étape 2 : Exécutez le programme d’installation de périphérique requis

Pour terminer la configuration du périphérique de votre périphérique StorSimple, vous devez :

- Sélectionnez un compte à associer à votre périphérique de stockage.

- Choisissez les paramètres de cryptage pour les données qui sont envoyées vers le cloud.

Effectuez les opérations suivantes dans le portail Azure classique pour terminer la configuration de périphérique requis.

#### <a name="to-complete-the-minimum-device-setup"></a>Pour terminer l’installation de périphériques

1. Dans la page des **périphériques** , sélectionnez le périphérique que vous venez de créer. Ce périphérique apparaît comme **actif**. Cliquez sur la flèche en regard du nom du périphérique, puis cliquez sur **Démarrage rapide**.

    ![Page de périphériques](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Cliquez sur **le programme d’installation de périphérique complet** pour démarrer l’Assistant de configuration de périphérique.

    ![Configurer l’Assistant de périphérique](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. Dans l’Assistant de périphérique configurer, dans la page **Paramètres de base** , effectuez les opérations suivantes :

   1. Spécifiez un compte de stockage pour être utilisé avec votre périphérique. Dans cet abonnement, vous pouvez sélectionner un compte de stockage existant dans la liste déroulante, ou vous pouvez spécifier **plus d’ajouter** pour choisir un compte à partir d’un autre abonnement.

   2. Définir les paramètres de cryptage pour toutes les données au repos qui sera envoyé vers le nuage. (StorSimple utilise le chiffrement AES-256). Pour crypter vos données, sélectionnez la case à cocher **Activer le chiffrement stockage cloud** . Entrez un cryptage de stockage cloud qui contient 32 caractères. Entrez à nouveau la clé pour le confirmer.

   3. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Paramètres de base](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Les paramètres seront désormais être mises à jour. Une fois que les paramètres sont mis à jour, le bouton de configuration complète des périphériques n’est pas disponible. Vous reviendrez à la page de **Démarrage rapide** du périphérique.                                                        

>[AZURE.NOTE]Vous pouvez modifier tous les autres paramètres de périphérique à tout moment en accédant à la page **configurer** .

## <a name="step-3-add-a-volume"></a>Étape 3 : Ajouter un volume

Effectuez les opérations suivantes dans le portail classique Azure pour créer un volume.

#### <a name="to-create-a-volume"></a>Pour créer un volume

1. Sur la page de **Démarrage rapide** de périphérique, cliquez sur **Ajouter un volume**. Cette opération démarre l’Assistant Ajout d’un volume.

2. Dans l’Assistant Ajout d’un volume, sous **Paramètres de base**, effectuez les opérations suivantes :

    1. Indiquez un nom unique pour le volume. Le nom doit être une chaîne qui contient 3 à 127 caractères.

    2. Fournissez une description pour le volume. La description vous aidera aux propriétaires de volume.

    3. Sélectionnez un type d’utilisation pour le volume. Le type d’utilisation peut être **hiérarchisé volume** ou **localement épinglé volume.** (**Volume de hiérarchisé** est la valeur par défaut). Pour les charges de travail nécessitant des garanties locales, faible latence et des performances supérieures, sélectionnez **localement épinglé** le **volume**. Pour toutes les autres données, sélectionnez **hiérarchisé** **volume**.

        Un volume localement épinglé est thickly mis en service et s’assure que les données principales du volume restent sur le périphérique et ne pas transférée sur le nuage. Si vous créez un volume localement épinglé, le périphérique vérifie d’espace disponible sur les couches locales pour prévoir un volume de la taille demandée. Création d’un volume local épinglé peut-être nécessiter débordement des données existantes à partir du périphérique vers le nuage, et le temps nécessaire pour créer le volume peut être long. La durée totale dépend de la taille du volume mis en service, la bande passante réseau disponible et les données sur votre périphérique.

        D’autre part, un volume monté en cascade est exactement provisionné et peut être créé très rapidement. Lorsque vous créez un volume monté en cascade, environ 10 % de l’espace est mis en service au niveau local et 90 % de l’espace est mis en service dans le nuage. Par exemple, si vous en service d’un volume de 1 To, 100 Go résident dans l’espace local et 900 Go doit être utilisée dans le nuage lorsque les couches de données. Cela implique à son tour est que si vous manquez de tout l’espace local sur le périphérique, vous ne peut pas prévoir un partage hiérarchisé (car les 10 % ne sera pas disponibles).

    4. Spécifier la capacité de mise en service pour le volume. Notez que la capacité spécifiée doit être inférieure à la capacité disponible. Si vous créez un volume monté en cascade, la taille doit être entre 500 Go et 5 To. Pour un volume localement épinglé, spécifiez une taille de volume entre 50 et 500 Go. Utiliser la capacité disponible sous la forme d’un guide de mise en service d’un volume. Si la capacité locale disponible est 0 Go, puis vous ne pas être autorisé à mettre en service un localement épinglés ou un volume monté en cascade.

        ![Paramètres de base](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Cliquez sur l’icône de la flèche ![icône de flèche](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) Pour accéder à la page suivante.

3. Dans la page **Paramètres supplémentaires** , ajoutez un nouvel enregistrement de contrôle d’accès (ACR) :

    1. Fournissez un **nom** pour votre ACR.

    2. Sous **nom de l’initiateur iSCSI**, fournir l’iSCSI nom qualifié (IQN) de votre hôte Windows. Si vous n’avez pas le nom IQN, passez à [l’annexe A: Get le nom qualifié d’un hôte Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Nous recommandons d’activer une sauvegarde par défaut en activant la case à cocher **activer une sauvegarde par défaut pour ce volume** . La sauvegarde par défaut crée une stratégie qui s’exécute à 22:30 par jour (heure du périphérique) et crée un instantané du nuage de ce volume.

        ![paramètres supplémentaires](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Démarre le travail de création de volume. Vous verrez un message de progression semblable à la suivante.

        ![message de progression](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Un volume sera créé avec les paramètres spécifiés. Par défaut, de surveillance et de sauvegarde va être activés pour le volume.

    5. Pour confirmer que le volume a été créé, accédez à la page de **Volumes** . Vous devez voir le volume indiqué.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Étape 4 : Montage, initialiser et formatage d’un volume

Effectuez les étapes suivantes pour monter, initialiser et formater les volumes StorSimple sur un hôte Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Pour monter, initialiser et formatage d’un volume

1. Démarrez Microsoft iSCSI initiator.

2. Dans la fenêtre **Propriétés de l’initiateur iSCSI** , sous l’onglet **découverte** , cliquez sur **Découvrir le portail**.

    ![portail de découverte](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Dans la boîte de dialogue de **Découvrir un portail cible** , indiquez l’adresse IP de votre interface réseau iSCSI et puis cliquez sur **OK**.

    ![Adresse IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Dans la fenêtre **Propriétés de l’initiateur iSCSI** , sous l’onglet **cibles** , localiser les **cibles de découvert**. (Chaque volume sera une cible de découverte). L’état du périphérique doit s’afficher comme **inactif**.

    ![cibles découvertes](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Sélectionnez un équipement cible, puis sur **se connecter**. Une fois que le périphérique est connecté, l’état doit passer à **connecté**. (Pour plus d’informations sur l’utilisation de l’initiateur Microsoft iSCSI, reportez-vous à la section [installation et configuration de Microsoft iSCSI Initiator] [1].

    ![Sélectionnez l’équipement cible](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Sur votre hôte Windows, appuyez sur la touche Windows + X, puis cliquez sur **exécuter**.

7. Dans la boîte de dialogue **exécuter** , tapez **Diskmgmt.msc**. Cliquez sur **OK**, et la boîte de dialogue **Gestion des disques** s’affiche. Le volet de droite affiche les volumes sur votre hôte.

8. Dans la fenêtre **Gestion des disques** , les volumes montés seront affiche comme indiqué dans l’illustration suivante. Cliquez sur le volume en découvert (cliquez sur le nom du disque), puis cliquez sur **en ligne**.

    ![gestion des disques](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Avec le bouton droit et sélectionnez **Initialiser le disque**.

    ![initialiser le disque 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Dans la boîte de dialogue, sélectionnez l’ou les disques à initialiser, puis cliquez sur **OK**.

    ![initialiser le disque 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Démarrage de l’Assistant Nouveau Volume Simple. Sélectionnez une taille de disque, puis cliquez sur **suivant**.

    ![Assistant Création de volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Affecter une lettre de lecteur au volume, puis cliquez sur **suivant**.

    ![Assistant Création de volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Entrez les paramètres pour formater le volume. **Uniquement le système NTFS est pris en charge sur Windows Server.** Définir l’Australie à 64 Ko. Fournir une étiquette pour le volume. Il est recommandé pour ce nom devant être identiques pour le nom de volume fourni sur votre périphérique virtuel StorSimple. Cliquez sur **suivant**.

    ![Assistant Création de volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Vérifiez les valeurs pour le volume, puis cliquez sur **Terminer**.

    ![Assistant Création de volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Sur la page **Gestion des disques** , les volumes seront affiche comme étant **en ligne** .

    ![volumes en ligne](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser l’interface utilisateur web locaux pour [administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Annexe a : obtenir le nom qualifié d’un hôte Windows Server

Effectuez les opérations suivantes pour obtenir l’iSCSI nom qualifié (IQN) d’un hôte Windows qui exécute Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Pour obtenir le nom qualifié d’un hôte Windows

1. Démarrez Microsoft iSCSI initiator sur votre hôte Windows.

2. Dans la fenêtre **iSCSI Initiator Properties** , dans l’onglet **Configuration** , sélectionnez et copiez la chaîne à partir du champ **Nom de l’initiateur** .

    ![propriétés de l’initiateur iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Enregistrer cette chaîne.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



