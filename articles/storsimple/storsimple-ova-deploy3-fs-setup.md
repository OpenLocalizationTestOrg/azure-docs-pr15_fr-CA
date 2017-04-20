<properties
   pageTitle="Déployer le tableau virtuel de StorSimple 3 - configurer le périphérique virtuel en tant que serveur de fichiers"
   description="Ce didacticiel tiers dans déploiement de tableau virtuel de StorSimple vous demande de configurer un périphérique virtuel en tant que serveur de fichiers."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Déployer tableau virtuel de StorSimple - ensemble en tant que serveur de fichiers

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduction 

Cet article s’applique à la version de Microsoft Azure StorSimple tableau virtuel (également connu sous le nom le périphérique virtuel local de StorSimple ou un périphérique virtuel de StorSimple) en cours d’exécution mars 2016 disponibilité générale (GA). Cet article décrit comment effectuer la configuration initiale, d’enregistrer votre serveur de fichiers StorSimple, de terminer la configuration de périphérique et de créer et de se connecter aux partages SMB. Cet article est le dernier de la série de didacticiels de déploiement requis pour déployer complètement de votre tableau virtuel comme un serveur de fichiers ou un serveur iSCSI.

Le processus d’installation et de configuration peut prendre 10 minutes environ.


## <a name="setup-prerequisites"></a>Paramétrage des conditions préalables

Avant de configurer et de configurer votre périphérique virtuel StorSimple, assurez-vous que :

-   Vous avez configuré un périphérique virtuel et connecté à celui-ci, comme détaillé dans la [disposition d’un tableau virtuel de StorSimple dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou la [disposition d’un tableau virtuel de StorSimple dans les environnements VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Vous disposez de la clé d’inscription de service à partir du service Gestionnaire de StorSimple que vous avez créé pour gérer les périphériques virtuels StorSimple. Pour plus d’informations, consultez [étape 2 : obtention de la clé d’inscription de service](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pour tableau virtuel de StorSimple.

-   Si c’est le périphérique virtuel deuxième ou ultérieur que vous enregistrez avec un gestionnaire de StorSimple de service existant, vous devez disposer de la clé de cryptage de données de service. Cette clé a été générée lors de la première unité a été enregistrée avec succès avec ce service. Si vous avez perdu cette clé, reportez-vous à la section [obtenir la clé de cryptage de données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de votre tableau virtuel StorSimple.

## <a name="step-by-step-setup"></a>Programme d’installation étape par étape

Utilisez la procédure à suivre pour installer et configurer votre périphérique virtuel StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Étape 1 : Terminer la configuration de l’interface utilisateur web local et inscrire votre appareil 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Pour terminer l’installation et inscrire l’appareil

1.  Ouvrez une fenêtre de navigateur et connectez-vous à l’interface utilisateur de web local. Type : 

    `https://<ip-address of network interface>`

    Utiliser l’URL de connexion noté à l’étape précédente. Vous verrez une erreur indiquant qu’il existe un problème avec le certificat de sécurité du site Web. Cliquez sur **Continuer pour cette page Web**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Connectez-vous à l’interface utilisateur de votre périphérique virtuel web en tant que **StorSimpleAdmin**. Entrez le mot de passe d’administrateur de périphérique que vous avez modifié à l’étape 3 : démarrer le périphérique virtuel dans la [disposition d’un tableau virtuel de StorSimple dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou de [disposition d’un tableau virtuel de StorSimple dans les environnements VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Vous allez être dirigé vers la page **d’accueil** . Cette page décrit les différents paramètres requis pour configurer et enregistrer le périphérique virtuel avec le service Gestionnaire de StorSimple. Notez que les **paramètres réseau**, **les paramètres de proxy Web**et **les paramètres de temps** sont facultatifs. Les seuls paramètres obligatoires sont **les paramètres de périphérique** et **les paramètres de nuage**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  Dans la page **paramètres réseau** sous **interfaces réseau**, données 0 seront automatiquement configurées pour vous. Chaque interface de réseau est défini par défaut pour obtenir les adresse IP automatiquement (DHCP). Par conséquent, une adresse IP, le sous-réseau et la passerelle seront automatiquement affectés (pour IPv4 et IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Si vous avez ajouté plus d’une interface de réseau au cours de la mise en service du dispositif, vous pouvez les configurer ici. Notez que vous pouvez configurer votre interface de réseau IPv4 uniquement ou en tant qu’à la fois IPv4 et IPv6. Seules les configurations IPv6 ne sont pas pris en charge.

1.  Les serveurs DNS sont nécessaires car ils sont utilisés lorsque votre périphérique tente de communiquer avec vos fournisseurs de service de stockage cloud ou résoudre votre périphérique par nom lorsqu’il est configuré comme un serveur de fichiers. Dans la page **paramètres réseau** sous les **serveurs DNS**:

    1.  Un serveur DNS principal et secondaire sera configuré automatiquement. Si vous choisissez de configurer des adresses IP statiques, vous pouvez spécifier les serveurs DNS. Pour une disponibilité élevée, nous vous conseillons de configurer un principal et un serveur DNS secondaire.

    2.  Cliquez sur **Appliquer**. Cela s’applique et valider les paramètres de réseau.

2.  Dans la page **paramètres de l’appareil** :

    1.  Affecter un unique **nom** à votre périphérique. Ce nom peut être de 1 à 15 caractères et peut contenir des lettres, des chiffres et des traits d’union.

    2.  Cliquez sur l’icône du **serveur de fichiers** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) pour le **Type** de périphérique que vous créez. Un serveur de fichiers vous permettra de créer des dossiers partagés.

    3.  Que votre périphérique est un serveur de fichiers, vous devez connecter le périphérique à un domaine. Entrez un **nom de domaine**.

    1.  Cliquez sur **Appliquer**.

2.  Une boîte de dialogue s’affiche. Entrez vos informations d’identification de domaine dans le format spécifié. Cliquez sur l’icône de vérification. Les informations d’identification de domaine vont être vérifiées. Vous verrez un message d’erreur si les informations d’identification sont incorrectes.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Cliquez sur **Appliquer**. Cela s’applique et valider les paramètres du périphérique.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Assurez-vous que votre tableau virtuel se trouve dans sa propre unité d’organisation (UO) Active Directory et aucun objet de stratégie de groupe (GPO) est appliqués ou hérités. Stratégie de groupe peut-être installer des applications, telles que des logiciels antivirus sur le tableau virtuel StorSimple. L’installation de logiciels supplémentaires n’est pas pris en charge et peut entraîner une corruption des données. 

1.  (Facultatif) configurer votre serveur proxy web. Configuration du proxy web est facultative, sachez que si vous utilisez un proxy web, vous ne pouvez configurer qu’il ici.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    Dans la page **proxy Web** :

    1.  Fournissez l' **URL du proxy Web** dans ce format : *http://&lt;adresse d’hôte IP ou FQDN&gt;: numéro de Port*. Notez que les URL HTTPS ne sont pas pris en charge.

    2.  Spécifier **l’authentification** sous la forme de **base** ou **Aucune**.

    3.  Si l’authentification, vous devez également fournir un **nom d’utilisateur** et le **mot de passe**.

    4.  Cliquez sur **Appliquer**. Cela sera valider et d’appliquer les paramètres de proxy web configurés.

1.  (Facultatif) configurer les paramètres de temps pour votre périphérique, tels que le fuseau horaire et les serveurs NTP principales et secondaires. Serveurs NTP sont nécessaires parce que le périphérique doit synchroniser l’heure afin qu’il peut authentifier avec vos fournisseurs de services cloud.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    Dans la page **paramètres de temps** :

    1.  Dans la liste déroulante, sélectionnez le **fuseau horaire** en fonction de la zone géographique dans laquelle le périphérique est en cours de déploiement. Le fuseau horaire par défaut de votre périphérique est le fichier PST. Votre périphérique utilise ce fuseau horaire pour toutes les opérations planifiées.

    2.  Spécifier un **serveur NTP principal** pour votre périphérique, ou acceptez la valeur par défaut de time.windows.com. Assurez-vous que votre réseau autorise le trafic NTP passer de votre centre de données à Internet.

    3.  Si vous le souhaitez spécifier un **serveur NTP secondaire** pour votre périphérique.

    4.  Cliquez sur **Appliquer**. Cela sera valider et d’appliquer les paramètres de temps configurée.

1.  Configurer les paramètres de nuage pour votre périphérique. Dans cette étape, vous terminez la configuration de périphérique local et puis enregistrez le périphérique avec votre service de gestionnaire de StorSimple.

    1.  Entrez la **clé d’inscription du Service** que vous avez dans [étape 2 : obtention de la clé d’inscription de service](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pour tableau virtuel de StorSimple.

    2.  Ignorez cette étape si il s’agit de votre premier périphérique enregistrement avec ce service et passez à l’étape suivante. Si ce n’est pas le périphérique que vous enregistrez avec ce service, vous devez fournir la **clé de cryptage de données de Service**. Cette clé est obligatoire avec la clé d’inscription de service pour enregistrer des périphériques supplémentaires avec le service Gestionnaire de StorSimple. Pour plus d’informations, reportez-vous à la section pour obtenir la [clé de cryptage de données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de votre interface utilisateur de site web local.

    3.  Cliquez sur **Enregistrer**. Cela va redémarrer le périphérique. Vous devrez peut-être attendre 2 à 3 minutes avant que le périphérique est correctement enregistré. Une fois que le périphérique a redémarré, vous affichera la page de connexion.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Retourner au portail Azure classique. Dans la page des **périphériques** , vérifiez que le périphérique est connecté au service en consultant l’état. L’état du périphérique doit être **actif**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Étape 2 : Exécutez le programme d’installation de périphérique requis

Pour terminer la configuration du périphérique de votre périphérique StorSimple, vous devez :

-   Sélectionnez un compte à associer à votre périphérique de stockage.

-   Choisissez les paramètres de cryptage pour les données qui sont envoyées vers le cloud.

Effectuez les opérations suivantes dans [Azure portal classique](https://manage.windowsazure.com/) pour terminer la configuration de périphérique requis.

#### <a name="to-complete-the-minimum-device-setup"></a>Pour terminer l’installation de périphériques

1.  Dans la page des **périphériques** , sélectionnez le périphérique que vous venez de créer. Ce périphérique apparaît comme **actif**. Cliquez sur la flèche sur le nom du périphérique, puis cliquez sur **Démarrage rapide**.

2.  Cliquez sur **le programme d’installation de périphérique complet** pour démarrer l’Assistant de configuration de périphérique.

3.  Dans l’Assistant de périphérique configurer dans la page **Paramètres de base** , effectuez les opérations suivantes :

    1.  Spécifiez un compte de stockage pour être utilisé avec votre périphérique. Vous pouvez sélectionner un compte de stockage existant dans cet abonnement dans la liste déroulante ou spécifier **Ajouter plus** pour choisir un compte à partir d’un autre abonnement.

    2.  Définir les paramètres de cryptage pour toutes le données au repos (cryptage AES) qui sera envoyé vers le nuage. Pour crypter vos données, vérifiez la zone de liste déroulante pour **Activer la clé de cryptage de stockage cloud**. Entrez un cryptage de stockage cloud qui contient 32 caractères. Entrez à nouveau la clé pour le confirmer. Une clé AES 256 bits sera utilisée avec la clé définie par l’utilisateur pour le cryptage.

    3.  Cliquez sur l’icône de vérification ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Les paramètres seront désormais être mises à jour. Une fois que les paramètres sont mis à jour, le bouton d’installation complet sera grisé. Vous reviendrez à la page de **Démarrage rapide** du périphérique.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Vous pouvez modifier tous les autres paramètres de périphérique à tout moment en accédant à la page **configurer** .

## <a name="step-3-add-a-share"></a>Étape 3 : Ajouter un partage

Effectuez les opérations suivantes dans [Azure portal classique](https://manage.windowsazure.com/) pour créer un partage.

#### <a name="to-create-a-share"></a>Pour créer un partage

1.  Sur la page de **Démarrage rapide** de périphérique, cliquez sur **Ajouter un partage**. Cette opération démarre l’Assistant Ajout d’un partage.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Dans la page **Paramètres de base** , effectuez le des opérations suivantes :

    1.  Spécifiez un nom unique pour le partage. Le nom doit être une chaîne qui contient 3 à 127 caractères.

    2.  (Facultatif) Fournissez une description pour le partage. La description vous aidera aux propriétaires de partage.

    3.  Sélectionnez un type d’utilisation pour le partage. Le type d’utilisation peut être **hiérarchisé** ou **localement épinglé**, hiérarchisé étant la valeur par défaut. Pour les charges de travail nécessitant des garanties locales, faible latence et des performances supérieures, sélectionnez un partage **épinglé localement** . Pour toutes les autres données, sélectionnez un partage **hiérarchisé** .

    Un partage local épinglé est thickly mis en service et s’assure que les données principales sur le partage restent locales sur le périphérique et qu’il ne pas transférée sur le nuage. D’autre part un partage hiérarchisé est exactement provisionné. Lorsque vous créez un partage hiérarchisé, 10 % de l’espace est mis en service au niveau local et 90 % de l’espace est mis en service dans le nuage. Par exemple, si vous en service d’un volume de 1 To, 100 Go résident dans l’espace local et 900 Go doit être utilisée dans le nuage lorsque les couches de données. À son tour, cela implique que si vous exécutez sur tout l’espace local sur le périphérique, vous ne peut pas prévoir un partage hiérarchisé.

1.  Spécifier la capacité de mise en service de votre partage. Notez que la capacité spécifiée doit être inférieure à la capacité disponible. Si vous utilisez un partage hiérarchisé, la taille de partage doit être entre 500 Go et 20 To. Pour un partage local épinglé, spécifiez une taille de partage entre 50 Go et 2 To. Utiliser la capacité disponible sous la forme d’un guide pour configurer un partage. Si la capacité locale disponible est 0 Go, puis vous ne pas être autorisé à provisionner des partages locaux ou à plusieurs niveaux.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Cliquez sur l’icône de flèche ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) pour accéder à la page suivante.

1.  Dans la page **Paramètres supplémentaires** , affectez les autorisations à l’utilisateur ou le groupe qui auront accès à ce partage. Spécifiez le nom de l’utilisateur ou le groupe d’utilisateurs dans *<john@contoso.com>* format. Nous vous recommandons d’utiliser un groupe d’utilisateurs (au lieu d’un seul utilisateur) pour permettre des privilèges d’administrateur accéder à ces partages. Après avoir affecté les autorisations ici, vous pouvez ensuite utiliser l’Explorateur Windows pour modifier ces autorisations.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Cliquez sur l’icône de vérification ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Un partage sera créé avec les paramètres spécifiés. Par défaut, de surveillance et de sauvegarde va être activés pour le partage.

## <a name="step-4-connect-to-the-share"></a>Étape 4 : Se connecter au partage

Vous devez maintenant vous connecter aux partages que vous avez créé à l’étape précédente. Effectuez ces étapes sur votre hôte Windows.

#### <a name="to-connect-to-the-share"></a>Pour vous connecter au partage

1.  Appuyez sur la touche ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + r. Dans la fenêtre Exécuter, spécifiez la * \\ * comme le chemin d’accès, remplaçant le *nom de serveur de fichier* avec le nom du périphérique que vous avez affecté à votre serveur de fichiers. Cliquez sur **OK**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  L’Explorateur s’ouvre. Vous devez désormais être en mesure de voir les partages que vous avez créé en tant que dossiers. Sélectionnez et double-cliquez sur un partage (dossier) pour afficher le contenu.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Vous pouvez maintenant ajouter des fichiers à ces partages et effectuer une sauvegarde.

![icône de vidéo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez configurer et inscrire un tableau virtuel de StorSimple sous la forme d’un serveur de fichiers.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser l’interface utilisateur web locaux pour [administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
