<properties
    pageTitle="Dépannage de SSH détaillées pour une machine virtuelle d’Azure | Microsoft Azure"
    description="Plus de dépannage pour les problèmes de connexion à un ordinateur virtuel Azure de SSH"
    keywords="SSH connexion refusée, ssh erreur, azure ssh, SSH Échec de la connexion"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Étapes de résolution détaillées de SSH

Il existe plusieurs raisons le client SSH n’est peut-être pas en mesure d’atteindre le service SSH sur l’ordinateur virtuel. Si vous avez suivi les [étapes de dépannage générales SSH](virtual-machines-linux-troubleshoot-ssh-connection.md)plus, vous avez besoin résoudre le problème de connexion. Cet article vous guide tout au long des étapes de dépannage détaillées pour déterminer où la connexion SSH est défectueux et comment le résoudre.

## <a name="take-preliminary-steps"></a>Étapes préliminaires

Le diagramme suivant illustre les composants qui sont impliqués.

![Diagramme qui affiche les composants de service SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Les étapes suivantes vous aider à isoler la source du problème et déterminer les solutions ou les solutions de contournement.

Tout d’abord, vérifiez l’état de la machine virtuelle dans le portail.

Dans le [portail Azure](https://portal.azure.com):

1. Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, sélectionnez **Parcourir** > **(classique) des machines virtuelles** > *nom de la machine virtuelle*.

    -OU-

    Pour les machines virtuelles créées à l’aide du modèle de gestionnaire de ressources, sélectionnez **Parcourir** > **machines virtuelles** > *nom de la machine virtuelle*.

    Le volet d’état de la machine virtuelle doit afficher **en cours d’exécution**. Faites défiler vers le bas pour afficher l’activité récente de calcul, de stockage et les ressources réseau.

2. Sélectionnez **paramètres** pour examiner les points de terminaison, adresses IP et d’autres paramètres.

    Pour identifier les points de terminaison dans les ordinateurs virtuels qui ont été créés à l’aide du Gestionnaire de ressources, vérifiez qu’un [groupe de sécurité de réseau](../virtual-network/virtual-networks-nsg.md) a été défini. Vérifiez également que les règles ont été appliqués au groupe de sécurité réseau et que celles-ci ne soient référencées dans le sous-réseau.

Dans [Azure portal classique](https://manage.windowsazure.com), pour les machines virtuelles qui ont été créés à l’aide du modèle de déploiement classique :

1. Sélectionnez les **ordinateurs virtuels** > *nom de la machine virtuelle*.
2. Sélectionnez de la machine virtuelle **tableau de bord** pour vérifier son état.
3. Sélectionnez **Moniteur** pour voir l’activité récente de calcul, de stockage et les ressources réseau.
4. Sélectionnez les **points de terminaison** pour s’assurer qu’il existe un point de terminaison pour le trafic SSH.

Pour vérifier la connectivité réseau, vérifiez les points de terminaison configurés et voir si vous pouvez atteindre la machine virtuelle via un autre protocole tel que HTTP ou un autre service.

Après ces étapes, essayez de rétablir la connexion SSH.


## <a name="find-the-source-of-the-issue"></a>Trouver la source du problème

Le client SSH sur votre ordinateur peut échouer atteindre le service SSH sur l’ordinateur virtuel d’Azure en raison de problèmes ou erreurs de configuration dans le code suivant :

- [Ordinateur de client SSH](#source-1-ssh-client-computer)
- [Dispositif de bord d’entreprise](#source-2-organization-edge-device)
- [Point de terminaison de service en nuage et accéder à la liste de contrôle (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Groupes de sécurité de réseau](#source-4-network-security-groups)
- [Basé sur Linux Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Source 1 : SSH ordinateur client

Afin d’éliminer la source de l’échec de votre ordinateur, vérifiez qu’il peut identifier les connexions SSH vers un autre sur site, ordinateur fonctionnant sous Linux.

![Diagramme qui met en évidence les composants des ordinateurs client SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Si la connexion échoue, vérifiez les points suivants sur votre ordinateur :

- Un paramètre du pare-feu local qui bloque le trafic entrant ou sortant SSH (22 TCP)
- Installé localement le logiciel client de proxy qui empêche les connexions SSH
- Un logiciel qui empêche les connexions SSH de surveillance installés en local
- Autres types de logiciels de sécurité qui surveillent le trafic ou autorisent/interdire les types de trafic spécifiques

Si une de ces conditions s’appliquent, désactivez le logiciel temporairement et essayez une connexion SSH vers un ordinateur local pour connaître la raison pour laquelle que la connexion est bloquée sur votre ordinateur. Ensuite travailler avec votre administrateur réseau afin de corriger les paramètres du logiciel pour permettre d’identifier les connexions SSH.

Si vous utilisez l’authentification par certificat, vérifiez que vous possédez ces autorisations dans le dossier .ssh dans votre répertoire de base :

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*.pub
- Chmod 600 ~/.ssh/id_rsa (ou tout autre fichier ayant vos clés privées stockées dans les)
- ~/.Ssh/known_hosts chmod 644 (contient des hôtes auxquels vous êtes connecté via le protocole SSH)

## <a name="source-2-organization-edge-device"></a>Source 2 : Périphérique de périmètre organisation

Afin d’éliminer la source de l’échec de votre périphérique de bord d’entreprise, vérifiez un ordinateur qui est connecté directement à Internet peut identifier les connexions SSH à votre machine virtuelle d’Azure. Si vous accédez à l’ordinateur virtuel via un réseau VPN de site à site ou d’une connexion ExpressRoute d’Azure, passez à le [4 de Source : groupes de sécurité de réseau](#nsg).

![Diagramme qui met en surbrillance le périphérique de bord d’entreprise](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Si vous n’avez pas un ordinateur qui est directement connecté à Internet, créer un nouveau Azure VM dans son propre groupe de ressources ou service de cloud et l’utiliser. Pour plus d’informations, consultez [créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-quick-create-cli.md). Lorsque vous avez terminé votre test, supprimez le groupe de ressources ou d’un service de machine virtuelle et le nuage.

Si vous pouvez créer une connexion SSH avec un ordinateur qui est connecté directement à Internet, vérifiez votre périphérique de bord d’entreprise pour :

- Un pare-feu qui bloque le trafic SSH avec Internet
- Un serveur proxy qui empêche les connexions SSH
- Détection d’intrusion ou de surveillance des logiciels exécutés sur des périphériques de votre réseau de périmètre qui empêche les connexions SSH du réseau

Travailler avec votre administrateur réseau afin de corriger les paramètres de vos équipements de bord d’entreprise pour autoriser le trafic SSH avec Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Source 3 : Point de terminaison de service Cloud et ACL

> [AZURE.NOTE] Cette source s’applique uniquement aux ordinateurs virtuels qui ont été créés à l’aide du modèle de déploiement classique. Pour les machines virtuelles qui ont été créés à l’aide du Gestionnaire de ressources, passez à le [4 de source : groupes de sécurité de réseau](#nsg).

Pour éliminer le point de terminaison de service cloud et ACL comme source de l’échec, vérifiez un autre Azure VM sur le même réseau virtuel peuvent identifier les connexions SSH à votre machine virtuelle.

![Diagramme qui met en évidence le point de terminaison de service cloud et ACL](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Si vous n’avez pas une autre VM sur le même réseau virtuel, vous pouvez facilement créer un nouveau. Pour plus d’informations, voir [Création d’une machine virtuelle Linux sur Azure à l’aide de l’interface CLI](virtual-machines-linux-quick-create-cli.md). Supprimer la machine virtuelle supplémentaire lorsque vous avez terminé avec votre test.

Si vous pouvez créer une connexion SSH avec un ordinateur virtuel sur le même réseau virtuel, vérifiez les points suivants :

- **La configuration de point de terminaison pour le trafic SSH sur la cible de la machine virtuelle.** Le port TCP privé du point de terminaison doit correspondre le port TCP sur lequel le service SSH sur l’ordinateur virtuel est à l’écoute. (Le port par défaut est 22). Pour les machines virtuelles créées à l’aide du modèle de déploiement du Gestionnaire de ressources, vérifiez le numéro de port TCP SSH dans le portail Azure en sélectionnant **Parcourir** > **Virtual machines (v2)** > *nom de l’ordinateur virtuel* > **paramètres** > **points de terminaison**.

- **La liste ACL pour le point de terminaison du trafic SSH sur l’ordinateur virtuel cible.** Une liste ACL permet de spécifier autorisé ou refusé le trafic entrant à partir d’Internet, en fonction de l’adresse IP source. Mauvaise configuration des ACL peuvent empêcher le trafic entrant de SSH pour le point de terminaison. Vérifiez vos ACL pour vous assurer que le trafic entrant provenant des adresses IP publiques de votre serveur proxy ou autre serveur de transport edge n’est autorisé. Pour plus d’informations, consultez [à propos de l’accès au réseau les listes de contrôle (ACL)](../virtual-network/virtual-networks-acl.md).

Pour éliminer le point de terminaison en tant que source du problème, supprimez le point de terminaison en cours, créer un nouveau point de terminaison et spécifier le nom SSH (port TCP 22 pour le numéro de port publics et privés). Pour plus d’informations, voir [configurer les points de terminaison sur une machine virtuelle dans Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Source 4 : Groupes de sécurité de réseau

Les groupes de sécurité réseau permettent d’avoir un contrôle plus granulaire sur le trafic entrant et sortant autorisé. Vous pouvez créer des règles qui couvrent des sous-réseaux et de services dans un réseau virtuel Azure en nuage. Vérifiez vos règles de groupe de sécurité de réseau pour vous assurer que le trafic SSH vers et à partir d’Internet est autorisé.
Pour plus d’informations, consultez [à propos des groupes de sécurité de réseau](../virtual-network/virtual-networks-nsg.md).

## <a name="source-5-linux-based-azure-virtual-machine"></a>Source de 5 : Linux Azure machine virtuelle

La dernière source de problèmes possibles est l’ordinateur virtuel Azure lui-même.

![Diagramme qui met en évidence la machine virtuelle Azure Linux](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Si vous ne l’avez pas déjà fait, suivez les instructions [pour réinitialiser un mot de passe ou le SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md).

Réessayez de vous connecter à partir de votre ordinateur. Si elle ne fonctionne toujours pas, voici quelques-uns des problèmes possibles :

- Le service SSH ne fonctionne pas sur l’ordinateur virtuel cible.
- Le service SSH n’écoute pas sur le port TCP 22. Pour tester cela, installez un client telnet sur votre ordinateur local et exécutez « telnet *cloudServiceName*. cloudapp.net 22 ». Cette option détermine si la machine virtuelle permet les communications entrantes et sortantes au point de terminaison SSH.
- Le pare-feu local sur l’ordinateur virtuel cible comporte des règles qui empêchent le trafic SSH entrant ou sortant.
- Détection d’intrusion ou de surveillance du logiciel qui s’exécute sur l’ordinateur virtuel Azure du réseau empêche les connexions SSH.


## <a name="additional-resources"></a>Ressources supplémentaires
Pour plus d’informations sur la résolution des problèmes d’application, voir [dépanner l’accès à une application s’exécutant sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)