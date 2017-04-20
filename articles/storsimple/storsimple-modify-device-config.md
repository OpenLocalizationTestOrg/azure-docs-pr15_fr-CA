<properties 
   pageTitle="Modifier la configuration de périphérique StorSimple | Microsoft Azure" 
   description="Décrit comment utiliser le service Gestionnaire de StorSimple de reconfigurer un périphérique StorSimple qui a déjà été déployé." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Le service Gestionnaire de StorSimple permet de modifier la configuration de votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble 

La page de **configuration** du portail classique Azure contient tous les paramètres de périphérique vous pouvez reconfigurer sur un dispositif de StorSimple qui est géré par un service de gestionnaire de StorSimple. Ce didacticiel explique comment vous pouvez utiliser la page de **configuration** pour effectuer les tâches suivantes au niveau du périphérique :

- Modifier les paramètres de périphérique 
- Modifier les paramètres de temps 
- Modifier les paramètres DNS 
- Modifier des interfaces réseau
- Remplacer ou réaffecter les IPs

## <a name="modify-device-settings"></a>Modifier les paramètres de périphérique

Les paramètres de périphérique incluent le nom convivial du périphérique et la description de périphérique.

Un nom par défaut est affecté à un périphérique StorSimple qui est connecté au service Gestionnaire de StorSimple. Le nom par défaut reflète généralement le numéro de série du périphérique. Par exemple, le nom de périphérique par défaut est de 15 caractères, tel que SHX0991003G44HT-8600, indique les informations suivantes :

- **8600** – indique le modèle de périphérique.
- **SHX** – indique le site de fabrication.
- **0991003** - indique un produit spécifique.
- **G44HT**- les 5 derniers chiffres sont incrémentés pour créer des numéros de série uniques. Cela peut-être pas une série séquentielle.

Vous pouvez utiliser le portail classique Azure pour modifier le nom du périphérique et de lui attribuer un nom unique et convivial de votre choix. Le nom convivial peut contenir tous les caractères et un maximum de 64 caractères.

Vous pouvez également spécifier une description de périphérique. Une description de périphérique permet généralement d’identifier le propriétaire et l’emplacement physique du périphérique. Le champ de description doit contenir moins de 256 caractères.
 
## <a name="modify-time-settings"></a>Modifier les paramètres de temps

Votre appareil doit synchroniser le temps pour s’authentifier auprès de votre fournisseur de service de stockage cloud. Sélectionnez votre fuseau horaire dans la liste déroulante et spécifiez jusqu'à deux serveurs du protocole de temps réseau (NTP). Le serveur NTP principal est requis et est spécifié lorsque vous utilisez Windows PowerShell pour StorSimple pour configurer votre périphérique. Vous pouvez spécifier le de Windows Server par défaut **time.windows.com** comme votre serveur NTP. Vous pouvez afficher la configuration du serveur NTP principale via le portail classique Azure, mais vous devez utiliser l’interface de Windows PowerShell pour le modifier.

La configuration du serveur NTP secondaire est facultative. Vous pouvez utiliser le portail classique pour configurer un serveur NTP secondaire. 

Lorsque vous configurez le serveur NTP, assurez-vous que votre réseau autorise le trafic NTP à partir de votre centre de données sur Internet. Lorsque vous spécifiez un serveur NTP public, il se peut que vous devez vous assurer que votre pare-feu de réseau et autres périphériques de sécurité sont configurés pour autoriser le trafic NTP d’et vers le réseau externe. Si le trafic du protocole NTP bidirectionnelle n’est pas autorisé, vous devez utiliser un serveur NTP interne (un contrôleur de domaine Windows fournit cette fonction). Si votre périphérique ne peut pas synchroniser le temps, il n’est peut-être pas en mesure de communiquer avec votre fournisseur de stockage cloud.

Pour afficher la liste des serveurs NTP publics, aller sur le [Web de serveurs NTP](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Que se passe-t-il si le périphérique est déployé dans un autre fuseau horaire ?

Si le périphérique est déployé dans un autre fuseau horaire, le fuseau horaire de périphérique change. Étant donné que toutes les stratégies de sauvegarde utilisent le fuseau horaire de périphérique, les stratégies de sauvegarde ajuste automatiquement selon le nouveau fuseau horaire. Aucune intervention de l’utilisateur n’est requise.

## <a name="modify-dns-settings"></a>Modifier les paramètres DNS

Un serveur DNS est utilisé lorsque le périphérique tente de communiquer avec votre fournisseur de service de stockage cloud. Pour une disponibilité élevée, vous devez configurer à la fois le serveur principal et les serveurs DNS secondaires au cours du déploiement initial de périphérique. Pour reconfigurer le serveur DNS principal, vous devez utiliser l’interface Windows PowerShell sur le périphérique StorSimple.

Pour modifier le serveur DNS secondaire, vous pouvez utiliser le portail classique Azure.



## <a name="modify-network-interfaces"></a>Modifier des interfaces réseau

Votre périphérique a six interfaces réseau périphérique, dont quatre sont 1 Gigabit Ethernet et deux 10 GbE. Ces interfaces sont étiquetées en tant que données 0 – données 5. DONNÉES 0, données 1, données 4 et 5 de données sont 1 Gigabit Ethernet, tandis que données 2 et données 3 sont des interfaces de réseau 10 Gigabit Ethernet.

Configurer les **Paramètres de l’Interface réseau** pour chacune des interfaces à utiliser. Pour garantir une haute disponibilité, nous vous recommandons d’avoir au moins deux interfaces iSCSI et les deux interfaces de nuage sur votre périphérique. Nous vous recommandons, mais ne nécessitent pas que désactivé les interfaces inutilisées.

Lorsque vous configurez une des interfaces réseau, vous devez configurer un IP virtuelle (VIP).

DONNÉES 0 sont activé pour le cloud par défaut. Lors de la configuration des données de 0, vous devez également configurer deux adresses IP fixes, une pour chaque contrôleur. Ces adresses IP fixes peuvent être utilisés pour accéder directement aux contrôleurs de périphérique et sont utiles lorsque vous installez des mises à jour sur le périphérique, ou lorsque vous accédez aux contrôleurs pour effectuer le dépannage.

Dans le StorSimple 8000 série 1, la métrique de routage de données 0 est définie à la plus basse ; Par conséquent, si votre périphérique est en cours d’exécution StorSimple 8000 série de mise à jour 1, tout le trafic de nuage sera routé via données 0. Prenez note de ceci si vous avez plus d’une interface réseau compatible cloud sur le périphérique StorSimple.

>[AZURE.NOTE] Les adresses IP fixes pour le contrôleur sont utilisés pour traiter les mises à jour pour le périphérique. Par conséquent, les adresses IP fixe doivent être routable et capable de se connecter à Internet.

Pour chaque interface réseau, les paramètres suivants sont affichent :

- **Vitesse** -pas un paramètre configurable par l’utilisateur. DONNÉES 0, données 1, données 4 et 5 de données sont toujours 1 Gigabit Ethernet, tandis que données 2 et données 3 sont des interfaces Gigabit Ethernet 10.

     >[AZURE.NOTE] Vitesse et duplex sont toujours négocié automatiquement. Les trames étendues ne sont pas pris en charge.
 
- **État de l’interface** , une interface peut être activée ou désactivée. Si activée, le périphérique tente d’utiliser l’interface. Nous vous recommandons d’activer uniquement les interfaces qui sont connectés au réseau et utilisés que. Désactiver les interfaces que vous n’utilisez pas.

- **Type d’interface** : ce paramètre vous permet d’isoler le trafic iSCSI du trafic de stockage cloud. Ce paramètre peut être une des opérations suivantes :

    - **Nuage activé** : lorsqu’il est activé, le périphérique utilisera cette interface pour communiquer avec le nuage.
    - **iSCSI activé** : lorsqu’il est activé, le périphérique utilisera cette interface pour communiquer avec l’hôte iSCSI.

    Il est recommandé d’isoler le trafic iSCSI du trafic de stockage cloud. Notez également que si votre hôte est dans le même sous-réseau que votre périphérique, vous ne devez pas affecter une passerelle ; Toutefois, si votre hôte est dans un sous-réseau différent de celui de votre périphérique, vous devrez attribuer une passerelle.

- **Adresse IP** – il peut s’agir IPv4 ou IPv6 ou les deux. Les familles d’adresses IPv4 et IPv6 sont prises en charge pour les interfaces du réseau périphérique. Lorsque vous utilisez IPv4, spécifiez une adresse IP de 32 bits (*xxx.xxx.xxx.xxx*) en notation décimale à points. Lorsque vous utilisez IPv6, simplement fournir un préfixe de 4 chiffres, et une adresse 128 bits est générée automatiquement pour votre interface de réseau périphérique basé sur le préfixe.

- **Sous-réseau** – cela fait référence au masque de sous-réseau et est configuré via l’interface de Windows PowerShell.

- **Passerelle** – c’est la passerelle par défaut qui doit être utilisée par cette interface lorsqu’il tente de communiquer avec les nœuds qui ne sont pas dans le même espace d’adressage IP (sous-réseau). La passerelle par défaut doit être dans le même espace d’adresses (sous-réseau) comme interface d’adresse IP, tel que déterminé par le masque de sous-réseau.

- **Adresse IP fixe** – ce champ n’est disponible que lorsque vous configurez les données 0 interface. Pour les opérations telles que les mises à jour ou de la résolution des problèmes de périphérique, vous devrez peut-être se connecter directement au contrôleur de périphérique. L’adresse IP fixe peut être utilisé pour accéder à l’actif et le passif contrôleur sur votre périphérique.

Vous pouvez reconfigurer le contrôleur 0 et 1 de contrôleur via le portail classique Azure.

>[AZURE.NOTE] 
>
>- Pour assurer un fonctionnement correct, vérifiez la vitesse de l’interface et de duplex sur le commutateur de chaque interface de périphérique est connecté à. Interfaces de commutateur doit négocier avec ou configurés pour Gigabit Ethernet (1000 Mbit/s) et full duplex. Interfaces fonctionnant à des vitesses plus lentes ou en mode half duplex entraînera des problèmes de performances.
>
>- Pour limiter les perturbations et les interruptions de service, nous vous recommandons de que vous activez portfast sur chacun des ports de commutateur qui se connectent à l’interface de réseau iSCSI de votre périphérique. Cela garantit que la connectivité réseau peut être établie rapidement en cas de basculement.
 
## <a name="swap-or-reassign-ips"></a>Remplacer ou réaffecter les IPs

Actuellement, si n’importe quelle interface réseau sur le contrôleur est attribué une adresse IP virtuelle qui est en cours d’utilisation (par le même périphérique ou un autre périphérique sur le réseau), puis le contrôleur basculent. Par conséquent, vous devez suivre la procédure appropriée si vous sont échange VIP pour l’interface de réseau périphérique, parce que vous allez créer une situation IP en double.

Effectuez les étapes suivantes pour remplacer ou réaffecter la VIP pour une des interfaces réseau :

#### <a name="to-reassign-ips"></a>Pour réaffecter les IPs

1. Effacer l’adresse IP pour les deux interfaces.

2. Une fois que les adresses IP sont désactivées, affecter les nouvelles adresses IP pour les interfaces respectives.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer MPIO pour votre périphérique StorSimple](storsimple-configure-mpio-windows-server.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
     
