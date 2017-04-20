<properties
    pageTitle="Forum aux questions pour les machines virtuelles de Windows | Microsoft Azure"
    description="Fournit des réponses à certaines questions courantes à propos de Windows une machine virtuelle créée avec le modèle de gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Forum aux questions sur le Machines virtuelles Windows 


Cet article répond à certaines questions fréquemment posées à propos de Windows une machine virtuelle créée dans Azure en utilisant le modèle de déploiement du Gestionnaire de ressources. Pour la version Linux de cette rubrique, reportez-vous à la section [Forum aux questions propos des ordinateurs virtuels Linux](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Que puis-je exécuter sur une machine virtuelle d’Azure ?

Tous les abonnés peuvent exécuter le logiciel serveur sur une machine virtuelle Azure. Pour plus d’informations sur la stratégie de prise en charge pour l’exécution de logiciels Microsoft server dans Azure, reportez-vous à la section [logiciels serveurs de Microsoft prennent en charge des Machines de virtuelles pour Azure](https://support.microsoft.com/kb/2721672)

Certaines versions de Windows 7 et Windows 8.1 sont disponibles pour les abonnés de MSDN Azure avantage et des abonnés MSDN développement et Test de paiement à l’utilisation, pour les tâches de développement et de test. Pour plus d’informations, y compris les instructions et les limitations, consultez [Client Windows d’images pour les abonnés MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>L’espace de stockage puis-je utiliser avec une machine virtuelle ?

Chaque disque de données peut être jusqu'à 1 To. Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour plus d’informations, reportez-vous à la section [tailles pour les Machines virtuelles](virtual-machines-windows-sizes.md).

Un compte de stockage Azure fournit un stockage pour le disque du système d’exploitation et les disques de données. Chaque disque est un fichier .vhd stocké comme un objet blob de page. Pour connaître les prix, voir les [Détails de la tarification de stockage](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Comment puis-je accéder à mon ordinateur virtuel ?

Établir une connexion à distance à l’aide de la connexion RDP (Remote Desktop) pour un ordinateur virtuel de Windows. Pour obtenir des instructions, reportez-vous à la section [comment vous connecter et ouvrir une session sur un ordinateur virtuel Azure exécutant Windows](virtual-machines-windows-connect-logon.md). Un maximum de deux connexions simultanées sont prises en charge, à moins que le serveur est configuré comme un hôte de session des Services Bureau à distance.  


Si vous rencontrez des problèmes avec le Bureau à distance, consultez [résoudre les problèmes de bureau à distance, connexions à un Windows Azure Machine virtuelle](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Si vous êtes familiarisé avec Hyper-V, vous risquez de rechercher les un outil semblable à VMConnect. Azure n’offre pas un outil similaire, car l’accès à la console à un ordinateur virtuel n’est pas pris en charge.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Puis-je utiliser le disque temporaire (le lecteur D: par défaut) pour stocker les données ?

N’utilisez pas le disque temporaire pour stocker des données. C’est uniquement un stockage temporaire, vous risque de perte de données qui ne peuvent pas être récupérées. Perte de données peut se produire lorsque la machine virtuelle se déplace vers un hôte différent. Redimensionnement d’une machine virtuelle, mise à jour de l’hôte, ou une défaillance matérielle sur l’hôte est quelques-unes des raisons qu'une machine virtuelle peuvent se déplacer.

Si vous avez une application qui doit utiliser la lettre de lecteur D:, vous pouvez réaffecter les lettres de lecteur afin que le disque temporaire n’utilise pas de D:. Pour plus d’informations, voir [modification de la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Comment puis-je modifier la lettre de lecteur du disque temporaire ?

Vous pouvez modifier la lettre de lecteur par déplacement du fichier de la page et la réaffectation des lettres de lecteur, mais vous devez vous assurer que vous effectuez les étapes dans un ordre spécifique. Pour plus d’informations, voir [modification de la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Puis-je ajouter un ordinateur virtuel existant à un jeu de disponibilité ?

N° Si vous souhaitez que votre ordinateur virtuel faisant partie d’un ensemble de disponibilité, vous devez créer la machine virtuelle dans le jeu. Il n’existe actuellement pas d’une façon d’ajouter un ordinateur virtuel pour une disponibilité après qu’il a été créé.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Puis-je télécharger une machine virtuelle vers Azure ?

Oui. Pour plus d’informations, consultez [télécharger une image de machine virtuelle de Windows Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>Est-il possible de redimensionner le disque du système d’exploitation ?

Oui. Pour obtenir des instructions, reportez-vous à la section [comment développer le lecteur du système d’exploitation d’un ordinateur virtuel dans un groupe de ressources Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Puis-je copier ou cloner un Azure VM existant ?

Oui. Pour obtenir des instructions, reportez-vous à la section [comment créer une copie d’une machine virtuelle de Windows dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Pourquoi je ne vois pas Canada Central et les régions d’orientale du Canada par l’intermédiaire du Gestionnaire de ressources Azure ?

Deux nouvelles régions du Canada Central et orientale du Canada ne sont pas automatiquement inscrits pour la création de la machine virtuelle pour les abonnements d’Azure existantes. Cet enregistrement s’effectue automatiquement lorsqu’un ordinateur virtuel est déployé via le portail Azure à n’importe quelle autre région à l’aide du Gestionnaire de ressources Azure. Une fois un ordinateur virtuel est déployé sur n’importe quelle autre région d’Azure, de nouvelles zones doivent être disponibles pour les ordinateurs virtuels suivants.

## <a name="does-azure-support-linux-vms"></a>Azure prend en charge les ordinateurs virtuels de Linux ?

Oui. Pour créer rapidement une VM Linux à essayer, voir [Création d’une machine virtuelle Linux sur Azure via le portail](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Ajouter une carte réseau pour mon ordinateur virtuel après sa création ?

N° Ajout d’une carte réseau n’est possible lors de sa création.

## <a name="are-there-any-computer-name-requirements"></a>Existe-t-il des conditions nom ordinateur ?

Oui. Le nom de l’ordinateur peut être de 15 caractères au maximum. Pour plus d’informations d’attribution à vos ressources, reportez-vous à la section [instructions d’attribution de noms d’Infrastructure](virtual-machines-windows-infrastructure-naming-guidelines.md) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quelles sont les exigences de nom d’utilisateur lors de la création d’une machine virtuelle ?

Noms d’utilisateur peut être un maximum de 20 caractères et ne peut pas finir par un point («. »). 

Les noms d’utilisateurs suivants ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">administrateur </td><td style="text-align:center"> Admin </td><td style="text-align:center"> utilisateur </td><td style="text-align:center"> Utilisateur1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> utilisateur2 </td><td style="text-align:center"> Test1 </td><td style="text-align:center"> util_3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> un</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">sauvegarde </td><td style="text-align:center"> console </td><td style="text-align:center"> David </td><td style="text-align:center"> invité</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> propriétaire </td><td style="text-align:center"> racine </td><td style="text-align:center"> serveur</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> prise en charge </td><td style="text-align:center"> Support_388945a0 </td><td style="text-align:center"> Sys</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> utilisateur 4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quelles sont les exigences de mot de passe lors de la création d’une machine virtuelle ?

Les mots de passe doivent être des caractères 8-123 et répondre aux 3 des exigences de 4 complexité suivant :

- Que les caractères inférieurs
- Des caractères supérieures
- Un chiffre
- Avoir un caractère spécial (expression régulière correspond à [\W_])

Les mots de passe suivants ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">PA$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Mot de passe !</td><td style="text-align:center">Mot de passe1</td><td style="text-align:center">Password22</td><td style="text-align:center">ILOVEYOU !</td>
    </tr>
</table>
