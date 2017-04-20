
<properties 
    pageTitle="Comment Azure RemoteApp enregistrer les paramètres et les données utilisateur ? | Microsoft Azure"
    description="Découvrez comment Azure RemoteApp enregistre les données de l’utilisateur à l’aide de la disquette de profil utilisateur."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Comment Azure RemoteApp enregistrer les paramètres et les données utilisateur ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure enregistre l’identité de l’utilisateur et des personnalisations sur les périphériques et les sessions. Ces données de l’utilisateur sont stockées dans un disque par utilisateur par collecte, connu comme étant un disque de profil utilisateur (UDP). Le disque suit l’utilisateur et permet à que l’utilisateur dispose d’une expérience cohérente, quel que soit l’endroit où ils se connectent. enregistre 

Disques de profil utilisateur sont complètement transparents pour l’utilisateur — les utilisateurs enregistrent des documents dans leur dossier de Documents (sur ce qui semble être un lecteur local) et modifier leurs paramètres de l’application normalement. Dans le même temps, tous les paramètres personnels sont conservées lors de la connexion pour Azure RemoteApp à partir de n’importe quel périphérique. Tout ce que l’utilisateur voit est leurs données au même endroit.

Chaque UPD a 50 Go de stockage permanent et contient les deux paramètres de données et des applications utilisateur. 

Continuez la lecture pour les caractéristiques de l’objet sur les données de profil utilisateur.

>[AZURE.NOTE] Vous avez besoin de désactiver l’UPD ? Vous pouvez effectuer ce maintenant - extraction de billet de blog du Pavithra, [Désactiver l’utilisateur profil disques (UPDs) dans Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), pour plus de détails.


## <a name="how-can-an-admin-get-to-the-data"></a>Comment un administrateur peut-il aux données ?

Si vous devez accéder aux données d’un de vos utilisateurs (pour la reprise après sinistre ou si l’utilisateur quitte la société), contactez le support technique d’Azure et fournir les informations d’abonnement et l’identité de l’utilisateur. L’équipe Azure RemoteApp vous fournira une URL sur le disque dur virtuel. Télécharger ce disque dur virtuel et récupérer des documents ou des fichiers dont vous avez besoin. Notez que le disque dur virtuel est 50 Go, il prendra un peu de le télécharger.


## <a name="is-the-data-backed-up"></a>Les données sont sauvegardées ?

Oui, nous allons enregistrer une sauvegarde des données utilisateur par emplacement géographique. Les données est en lecture seule et est accessible de la même manière que les données régulières serait (contact Azure RemoteApp pour l’obtenir), si le centre de données principal est hors service. Les données sont copiées en temps réel à l’emplacement de sauvegarde, et nous ne pas conserver des copies des différentes versions. Ainsi, à la corruption de données, nous ne serons pas en mesure de le restaurer à une bonne version précédemment connue, mais si le centre de données principal est en panne, vous ne pourrez pas obtenir les données de l’utilisateur à l’autre emplacement.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>Comment les utilisateurs voir le UPD côté serveur ?

Chaque utilisateur aura son propre répertoire sur le serveur qui correspond à leur UPD : c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>Quelle est la meilleure façon d’utiliser Outlook et UPD ?

RemoteApp Azure enregistre l’état d’Outlook (boîtes aux lettres, fichiers pst) entre deux sessions. Pour ce faire, nous devons le fichier PST à stocker dans les données de profil utilisateur (c:\users\<nom d’utilisateur >). Il s’agit de l’emplacement par défaut pour les données, par conséquent, tant que vous ne modifiez pas l’emplacement, les données seront persistantes entre les sessions.

Nous recommandons également que vous utilisez le mode « cache » dans Outlook et utilisez le mode serveur / « en ligne » pour la recherche.

Consultez [cet article](remoteapp-outlook.md) pour plus d’informations sur l’utilisation d’Outlook et Azure RemoteApp.

## <a name="what-about-redirection"></a>Qu’en est-il de la redirection ?
Vous pouvez configurer RemoteApp Azure pour permettre aux utilisateurs d’accéder aux périphériques locaux par la configuration de [la redirection](remoteapp-redirection.md). Périphériques locaux pourront ensuite accéder aux données sur le UPD.

## <a name="can-i-use-my-upd-as-a-network-share"></a>Puis-je utiliser mon UPD comme un partage réseau ?
N° UPDs ne peut pas être utilisés comme un partage réseau. Un UDP n’est disponible pour l’utilisateur lorsque l’utilisateur est connecté activement à Azure RemoteApp.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>Si je supprime un utilisateur d’une collection, est supprimé leur UPD ?

Non, lorsque vous supprimez un utilisateur, nous ne supprimez pas automatiquement le UPD - au lieu de cela, nous stockons les données jusqu'à ce que vous supprimiez la collection. 90 jours après la suppression de la collection, nous supprimons tous les UPDs. 

Si vous devez supprimer un UPD d’une collection, contactez Azure RemoteApp, nous pouvons supprimer UPD de notre côté.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>Puis-je accéder à UPDs mes utilisateurs (utilisateurs soit en cours soit supprimés) ?

Oui, si vous contactez [RemoteApp d’Azure](mailto:remoteappforum@microsoft.com), nous pouvons configurer vous avec une URL pour accéder aux données. Vous aurez environ 10 heures télécharger les données ou les fichiers à partir de l’UPD avant l’expiration de l’accès.

## <a name="are-upds-available-offline"></a>Sont disponibles hors connexion des UPDs ?

Pour le moment, nous ne fournissent pas d’accès hors connexion pour UPDs, au-delà de la fenêtre d’access 10 heures décrite ci-dessus. Cela signifie que nous n’avons pas actuellement un moyen pour vous permettre d’accéder pour long suffisamment pour effectuer des tâches plus complexes, comme un logiciel antivirus en cours d’exécution sur l’UPDs ou l’accès aux données pour un audit.

## <a name="do-registry-key-settings-persist"></a>Les paramètres de clé de Registre persistent ?
Oui, écriture dans HKEY_Current_User est partie la UPD.

## <a name="can-i-disable-upds-for-a-collection"></a>Puis-je désactiver UPDs pour une collection ?

Oui, vous pouvez demander à RemoteApp Azure pour désactiver les UPDs pour un abonnement, mais vous ne pouvez pas faire que vous-même. Cela signifie que UPDs seront désactivées pour toutes les collections de l’abonnement.

Vous pouvez, si vous le souhaitez, désactiver l’UPDs dans les situations suivantes : 

- Vous devez terminer accès et le contrôle des données utilisateur (pour audit et l’examen des fins comme les institutions financières).
- Vous avez 3 rd-party utilisateur profil solutions gestion sur les sites et souhaitez continuer à les utiliser dans votre déploiement d’Azure RemoteApp à un domaine. Cela nécessiterait l’agent de profil à charger dans l’image. 
- Vous n’avez pas besoin d’un stockage de données local, ou vous avez toutes les données dans le nuage ou partage de fichiers et que vous souhaitez au contrôle de l’enregistrement des données localement à l’aide de RemoteApp d’Azure.

Pour plus d’informations, consultez [Désactiver utilisateur profil disques (UPDs) dans Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) .

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>Puis-je empêcher les utilisateurs d’enregistrer des données sur le lecteur système ?

Oui, mais vous devez pour cette configuration dans l’image du modèle avant de créer la collection. Utilisez les étapes suivantes pour bloquer l’accès au lecteur système :

1. Exécutez **gpedit.msc** sur l’image du modèle.
2. Naviguez jusqu'à **Configuration utilisateur > modèles d’administration > composants Windows > Explorateur de**.
3. Sélectionnez les options suivantes :
    - **Masquer ces lecteurs spécifiés dans Poste de travail**
    - **Empêcher l’accès aux lecteurs à partir du poste de travail**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>Est-il possible de graines UPDs ? Je souhaite placer des données dans le UPD est disponible la première fois que l’utilisateur se connecte.

Oui, lorsque vous créez l’image du modèle, vous pouvez ajouter des informations dans le profil par défaut. Cette information est ensuite ajoutée à l’UPD.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>Puis-je changer la taille de l’UPD selon la quantité souhaitée pour stocker de données ?

Non, tous les UPDs ont 50 Go de stockage. Si vous souhaitez stocker les différentes quantités de données, effectuez les opérations suivantes :

1. Désactiver la UPDs pour la collection.
2. Configurez un partage de fichiers accessible aux utilisateurs.
3. Charger le partage de fichiers à l’aide d’un script de démarrage. Voir ci-dessous pour plus d’informations sur les scripts de démarrage dans Azure RemoteApp.
4. Utilisateurs directs d’enregistrer toutes les données pour le partage de fichiers.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Comment exécuter un script de démarrage dans Azure RemoteApp ?

Si vous souhaitez exécuter un script de démarrage, démarrer en créant une tâche planifiée dans l’image de modèle que vous souhaitez utiliser pour la collection. (Ce *avant* d’exécuter sysprep.) 

![Créer une tâche système](./media/remoteapp-upd/upd1.png)

![Créer une tâche système qui s’exécute lorsqu’un utilisateur ouvre une session](./media/remoteapp-upd/upd2.png)

Sous l’onglet **Général** , assurez-vous de changer le **Compte d’utilisateur** , sous sécurité à « BUILTIN\Utilisateurs ».

![Modifier le compte d’utilisateur à un groupe](./media/remoteapp-upd/upd4.png)

La tâche planifiée s’exécutera le script de démarrage, à l’aide des informations d’identification de l’utilisateur. Planifier la tâche à exécuter chaque une fois qu’un utilisateur ouvre une session.

![Définir le déclencheur de la tâche comme « à l’ouverture de session »](./media/remoteapp-upd/upd3.png)

Vous pouvez également utiliser des [scripts de démarrage de stratégie de groupe](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>Qu’en est-il placer un script de démarrage dans le menu Démarrer ? Qui fonctionnerait ?

En d’autres termes, puis-je créer un fichier .bat qui exécute un script de fenêtre config et enregistrez-le dans le dossier Démarrer\Programmes\Démarrage c:\ProgramData\Microsoft\Windows\Start et puis que ce script à exécuter lorsqu’un utilisateur démarre une session RemoteApp ?

Non, qui n’est pas supporté avec Azure RemoteApp, qui utilise RDSH, également ne pas prend en charge les scripts de démarrage dans le menu Démarrer.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>Puis-je utiliser mstsc.exe (le programme de bureau à distance) pour configurer des scripts d’ouverture de session ?

Non, pas pris en charge par Azure RemoteApp.

## <a name="can-i-store-data-on-the-vm-locally"></a>Est-il possible de stocker des données sur l’ordinateur virtuel localement ?

NON, les données stockées de n’importe où sur l’ordinateur virtuel autre que dans le UPD seront perdues. Il existe une probabilité élevée, l’utilisateur ne recevra pas la machine virtuelle même la prochaine fois que leur inscription dans Azure RemoteApp. Nous ne gèrent pas la persistance de la machine virtuelle de l’utilisateur, afin que l’utilisateur ne signe pas la machine virtuelle même, et les données seront perdues. En outre, lors de la mise à jour de la collection, les ordinateurs virtuels existants sont remplacés par un nouvel ensemble d’ordinateurs virtuels - ce qui signifie que toutes les données stockées sur l’ordinateur virtuel lui-même sont perdues. Il est recommandé de stocker les données dans le stockage partagé comme fichiers d’Azure, un serveur de fichiers à l’intérieur d’un VNET, ou dans le cloud à l’aide d’un système de stockage cloud tels que boîte de dépôt UPD.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>Comment monter un partage de fichiers d’Azure sur une machine virtuelle, à l’aide de PowerShell ?

Vous pouvez utiliser l’applet de commande Net-PSDrive pour monter le lecteur, comme suit :

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


Vous pouvez également enregistrer vos informations d’identification en exécutant la commande suivante :

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Qui vous permet d’ignorer la - paramètre d’informations d’identification dans l’applet de commande New-PSDrive.
