<properties
   pageTitle="L’accès à des ID de machine virtuelle"
   description="Décrit l’accès et à l’aide du numéro d’identification Unique Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>L’accès et à l’aide du numéro d’identification Unique Azure VM

Numéro d’identification unique Azure VM est un identificateur de 128 bits codé et stocké dans SMBIOS du tous les Azure IaaS VM et peut actuellement être lus à l’aide des commandes de BIOS de plate-forme.

Numéro d’identification unique Azure VM est une propriété en lecture seule. ID de machine virtuelle Unique Azure ne changera pas lors de la fermeture de redémarrage (soit prévue non planifié), Start/Stop désalloue, rétablissement de service ou restaurer en place. Toutefois, si la machine virtuelle est un instantané et copié pour créer une nouvelle instance, nouvel ID de machine virtuelle Azure est configuré.

> [AZURE.NOTE] Si vous disposez d’anciens ordinateurs virtuels créés et en cours d’exécution dans la mesure où cette nouvelle fonctionnalité a été déployée (18 septembre 2014), veuillez redémarrer votre machine virtuelle d’obtenir automatiquement un Azure unique ID.


Pour accéder à Azure VM ID Unique à partir de la machine virtuelle :


## <a name="create-a-vm"></a>Créer un ordinateur virtuel
 

Pour plus d’informations, voir [Création d’une Machine virtuelle](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Se connecter à la machine virtuelle
 

Pour plus d’informations, reportez-vous à la section [SSH de Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>ID Unique de requête VM

Commande (exemple utilise **Ubuntu**) :

    sudo dmidecode | grep UUID
    
Exemple de résultats attendus :

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Gros-boutiste bit de commande, l’ID de machine virtuelle Unique réel dans ce cas sera :

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Numéro d’identification unique Azure VM peut servir dans des scénarios différents si la machine virtuelle s’exécute sur Azure ou sur site et peut aider vos spécifications de suivi de la licence, création de rapports ou général que peut avoir sur vos déploiements Azure IaaS. De nombreux éditeurs de logiciels indépendants et les certifier sur Azure et de création d’applications peuvent imposer l’identification d’un Azure VM tout au long de leur cycle de vie et à savoir si la machine virtuelle s’exécute sur Azure, sur site ou sur d’autres fournisseurs de nuage. Cet identificateur de plate-forme par exemple peut aider à détecter si le logiciel est concédé sous licence correctement ou vous aider à mettre en corrélation des données VM à sa source tels que pour aider sur la définition de la métrique de droite de la plate-forme et à suivre et établir une corrélation entre ces mesures entre autres utilisations.
