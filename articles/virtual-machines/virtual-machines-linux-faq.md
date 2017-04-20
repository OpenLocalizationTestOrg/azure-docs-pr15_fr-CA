<properties
    pageTitle="Forum aux questions pour les machines virtuelles de Linux | Microsoft Azure"
    description="Fournit des réponses à certaines questions courantes à propos de Linux une machine virtuelle créée avec le modèle de gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Forum aux questions propos des ordinateurs virtuels Linux

Cet article répond à certaines questions fréquemment posées à propos de Linux une machine virtuelle créée dans Azure en utilisant le modèle de déploiement du Gestionnaire de ressources. Pour la version Windows de cette rubrique, reportez-vous à la section [Forum aux questions sur le Machines virtuelles Windows](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Que puis-je exécuter sur une machine virtuelle d’Azure ?

Tous les abonnés peuvent exécuter le logiciel serveur sur une machine virtuelle Azure. Pour plus d’informations, reportez-vous à la section [Linux sur les Distributions de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>L’espace de stockage puis-je utiliser avec une machine virtuelle ?

Chaque disque de données peut être jusqu'à 1 To. Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour plus d’informations, reportez-vous à la section [tailles pour les Machines virtuelles](virtual-machines-linux-sizes.md).

Un compte de stockage Azure fournit un stockage pour le disque du système d’exploitation et les disques de données. Chaque disque est un fichier .vhd stocké comme un objet blob de page. Pour connaître les prix, voir les [Détails de la tarification de stockage](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Comment puis-je accéder à mon ordinateur virtuel ?

Établir une connexion à distance pour vous connecter à l’ordinateur virtuel, à l’aide de SSH (Secure Shell). Consultez les instructions sur la façon de se connecter [à partir de Windows](virtual-machines-linux-ssh-from-windows.md) ou [de Linux et Mac](virtual-machines-linux-mac-create-ssh-keys.md). Par défaut, SSH autorise un maximum de 10 connexions simultanées. Vous pouvez augmenter ce nombre en modifiant le fichier de configuration.


Si vous rencontrez des problèmes, vérifiez les [connexions de résoudre les problèmes de SSH (Secure Shell)](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Puis-je utiliser le disque temporaire (/ dev/sdb1) pour stocker les données ?

N’utilisez pas le disque temporaire (/ dev/sdb1) pour stocker des données. C’est là uniquement pour le stockage temporaire. Vous risquez de perdre des données qui ne peuvent pas être récupérées.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Puis-je copier ou cloner un Azure VM existant ?

Oui. Pour obtenir des instructions, reportez-vous à la section [comment créer une copie d’une machine virtuelle de Linux dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Pourquoi je ne vois pas Canada Central et les régions d’orientale du Canada par l’intermédiaire du Gestionnaire de ressources Azure ?

Deux nouvelles régions du Canada Central et orientale du Canada ne sont pas automatiquement inscrits pour la création de la machine virtuelle pour les abonnements d’Azure existantes. Cet enregistrement s’effectue automatiquement lorsqu’un ordinateur virtuel est déployé via le portail Azure à n’importe quelle autre région à l’aide du Gestionnaire de ressources Azure. Une fois un ordinateur virtuel est déployé sur n’importe quelle autre région d’Azure, de nouvelles zones doivent être disponibles pour les ordinateurs virtuels suivants.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Ajouter une carte réseau pour mon ordinateur virtuel après sa création ?

N° Ajout d’une carte réseau n’est possible lors de sa création.


## <a name="are-there-any-computer-name-requirements"></a>Existe-t-il des conditions nom ordinateur ?

Oui. Le nom de l’ordinateur peut être un maximum de 64 caractères. Pour plus d’informations d’attribution à vos ressources, reportez-vous à la section [instructions d’attribution de noms d’Infrastructure](virtual-machines-linux-infrastructure-naming-guidelines.md) .


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quelles sont les exigences de nom d’utilisateur lors de la création d’une machine virtuelle ?

Les noms d’utilisateur doivent être de 1 à 64 caractères.

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

Les mots de passe doivent être des 6-72 caractères et répondre aux 3 des exigences de 4 complexité suivant :

- Que les caractères inférieurs
- Des caractères supérieures
- Un chiffre
- Avoir un caractère spécial (expression régulière correspond à [\W_])

Les mots de passe suivants ne sont pas autorisés :

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Mot de passe !</td>
        <td style="text-align:center">Mot de passe1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU !</td>
    </tr>
</table>
