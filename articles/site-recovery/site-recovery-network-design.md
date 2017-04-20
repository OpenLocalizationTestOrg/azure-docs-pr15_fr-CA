<properties
    pageTitle="Conception de l’infrastructure de réseau pour la reprise après sinistre | Microsoft Azure"
    description="Cet article décrit les considérations de conception de réseau pour la récupération de Site Azure"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>Conception de l’infrastructure de réseau pour la reprise après sinistre

Cet article est dirigé vers les professionnels de l’informatique qui sont responsables de l’architecture, mise en œuvre et prise en charge de la continuité d’activité et infrastructure de reprise (BCDR) et qui souhaitent tirer parti de Microsoft Azure Site ASR (restauration) pour prendre en charge et améliorer leurs services BCDR. Ce document traite des considérations pratiques pour le déploiement du serveur System Center Virtual Machine Manager, les avantages et inconvénients de sous-réseaux étirés et basculement de sous-réseau et la structure de reprise après sinistre pour des sites virtuels dans Microsoft Azure.

## <a name="overview"></a>Vue d’ensemble

[Récupération de Site Azure (ASR)](https://azure.microsoft.com/services/site-recovery/) est un service de Microsoft Azure qui orchestre la protection et la récupération de vos applications virtualisées à des fins commerciales la continuité d’activité après sinistre récupération (BCDR). Ce document est conçu pour guider le lecteur à travers le processus de conception des réseaux, en mettant l’accent sur la conception des plages IP et les sous-réseaux sur le site de reprise, lors de la réplication des machines virtuelles (VM), à l’aide de la récupération de Site.

En outre, cet article explique comment restauration du Site permet d’architecture et l’implémentation d’un centre de données virtuel multisite pour prendre en charge des services BCDR au moment du test ou de sinistre.

Dans un monde où tout le monde attend 24 x 7 connectivité, il est plus important que jamais maintenir votre infrastructure et vos applications et en cours d’exécution. L’objectif de la continuité d’activité et de reprise après sinistre (BCDR) est de restaurer les composants défectueux afin que l’organisation peut reprendre rapidement le fonctionnement normal. Développement de stratégies de récupération d’urgence pour traiter les événements catastrophiques peu probable est très difficile. C’est en raison de la difficulté inhérente de prédire l’avenir, en particulier par rapport aux événements improbable et le coût élevé pour fournir des mesures adéquates de protection contre des catastrophes. 

Essentiel pour BCDR de planification, les objectifs de temps de récupération (RTO) et objectif de Point de récupération (RPO) doivent être définis dans le cadre d’un plan de récupération d’urgence. Lorsqu’un sinistre Datacenter du client, à l’aide de récupération de Site Azure, les clients peuvent rapidement (faible RTO) mettre en ligne leurs machines virtuelles répliquées situées dans le centre de données secondaire ou Microsoft Azure avec perte de données minimale (RPO faible). 

Basculement sur incident est rendue possible par la récupération automatique du système qui initialement copie des machines virtuelles désignés à partir du centre de données principal vers le centre de données secondaire ou vers Azure (selon le scénario), puis actualise régulièrement les réplicas. Au cours de la planification de l’infrastructure, la conception de réseau doit être considérée comme goulot d’étranglement potentiel qui peut empêcher de société de réunion RTO et les objectifs RPO.  

Lorsque les administrateurs envisagent de déployer une solution de reprise après sinistre, l’une des questions clées dans tous les esprits est comment l’ordinateur virtuel est accessible une fois le basculement terminé. Récupération automatique du système permet à l’administrateur de choisir le réseau auquel une machine virtuelle peut être connectée à après le basculement. Si le site principal est géré par un serveur VMM cela est accompli à l’aide du mappage du réseau. Pour plus d’informations, reportez-vous à la section [Préparation de mappage réseau](site-recovery-network-mapping.md) .

Lors de la conception du réseau pour le site de reprise, l’administrateur dispose de deux options :

- Utiliser une autre plage d’adresses IP pour le réseau sur le site de récupération. Dans ce scénario, l’ordinateur virtuel après basculement obtiendra une nouvelle adresse IP et l’administrateur doit effectuer une mise à jour DNS. Lire plus d’informations sur le DNS comment mettre à jour [ici](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Utiliser la même plage d’adresses IP pour le réseau sur le site de récupération. Dans certains scénarios, les administrateurs préfèrent conserver les adresses IP qu’ils ont sur le site principal, même après le basculement. Dans un scénario normal, un administrateur devrait mettre à jour les itinéraires pour indiquer le nouvel emplacement des adresses IP. Mais dans le cas où un VLAN étiré est déployé entre le principal et les sites de reprise, en conservant les adresses IP pour les ordinateurs virtuels est une option intéressante. Conserver la même adresse IP adresses simplifie le processus de récupération à prendre les décisions à n’importe quel réseau les étapes post-basculement.


Lorsque les administrateurs envisagent de déployer une solution de reprise après sinistre, l’une des questions clées leur esprit est comment les applications seront accessibles une fois le basculement terminé. Les applications modernes sont presque toujours dépendantes de la mise en réseau dans une certaine mesure, donc physiquement déplacer qu'un service à partir d’un site à un autre représente un défi de mise en réseau. Il existe deux manières principales que ce problème est traité dans les solutions de reprise après sinistre. La première approche consiste à gérer des adresses IP fixes. Malgré les déplacement de services et les serveurs d’hébergement dans différents emplacements physiques, les applications prennent la configuration d’adresse IP avec eux vers le nouvel emplacement. La seconde approche consiste à complètement la modification de l’adresse IP lors de la transition dans le site restauré. Chaque approche présente plusieurs variantes de mise en oeuvre qui sont résumées ci-dessous.

Lors de la conception du réseau pour le site de reprise, l’administrateur dispose de deux options :

## <a name="option-1-retain-ip-addresses"></a>Option 1 : Conserver les adresses IP 

À partir d’un point de vue processus de récupération d’urgence, à l’aide d’adresse IP fixe adresses semble la méthode la plus simple à implémenter, mais il a un certain nombre de défis potentiels qui rendent l’approche moins courants dans la pratique. Récupération de Site Azure offre la possibilité de conserver les adresses IP de tous les scénarios. Avant une décide de retenir les IP, pensée appropriée convient aux contraintes qu’il impose sur les capacités de basculement sur incident. Examinons les facteurs qui peuvent vous aider à prendre une décision de conserver les adresses IP, ou non. Pour ce faire de deux façons, en utilisant un sous-réseau étiré ou en effectuant un basculement de sous-réseau complet.

### <a name="stretched-subnet"></a>Sous-réseau étiré

Ici, le sous-réseau est rendu disponible simultanément dans le principal et les sites de reprise après sinistre. En termes simples, cela signifie que vous pouvez déplacer un serveur et sa configuration IP (couche 3) vers le deuxième site et le réseau achemine le trafic vers le nouvel emplacement automatiquement. Il est très facile à gérer à partir d’un point de vue du serveur, mais il a un certain nombre de défis :

- Layer 2 (couche de liaison de données) du point de vue, il nécessite des équipements réseau capable de gérer un réseau VLAN étiré, mais cela est devenu moins problématique car il est désormais largement disponible. Le problème de la deuxième et plus difficile qui est en étirant le VLAN du domaine d’erreur potentielle est étendu aux deux sites, essentiellement de devenir un point unique de défaillance. Bien que cela soit peu probable, il est arrivé qu’une bourrasque de diffusion a démarré mais qu’il ne peut pas être isolée. Nous ont trouvé mixte d’avis sur ce dernier problème et que vous avez vu les nombreuses implémentations réussies ainsi que « nous jamais implémentera cette technologie ici ».
- Étiré de sous-réseau n’est pas possible si vous utilisez Microsoft Azure que le site de reprise après sinistre.


### <a name="subnet-failover"></a>Basculement de sous-réseau

Il est possible d’implémenter le basculement de sous-réseau pour bénéficier des avantages de la solution de sous-réseau étiré décrites ci-dessus sans étirement du sous-réseau sur plusieurs sites. Ici n’importe quel sous-réseau donné est présent sur le Site de 1 ou 2 du Site, mais jamais sur les deux sites simultanément. Afin de maintenir l’espace d’adressage IP en cas de basculement, il est possible organiser par programme pour l’infrastructure de routeur déplacer les sous-réseaux d’un site à un autre. Dans un scénario de basculement sur incident qu'avec associé déplace les sous-réseaux protégés par VMs. Le principal inconvénient de cette approche est en cas de défaillance, que vous devez déplacer le sous-réseau entier, ce qui peut être OK, mais peut affecter les considérations de granularité de basculement. 

Examinons à présent comment une entreprise fictive nommée Contoso est en mesure de répliquer ses machines virtuelles à un emplacement lors de l’échec de la récupération sur le sous-réseau dans sa totalité. Nous allons tout d’abord étudier comment Contoso est en mesure de gérer leurs sous-réseaux lors de la réplication des machines virtuelles entre les deux emplacements, et locaux puis nous aborderons sous-réseau fonctionnement avec basculement lorsque [Azure est utilisé en tant que le site de reprise après sinistre](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Basculement vers un site secondaire sur site

Examinons un scénario où nous souhaitons conserver l’adresse IP de chacun des ordinateurs virtuels et le basculement sur le sous-réseau complet ensemble. Le site principal a des applications qui s’exécutent dans le sous-réseau 192.168.1.0/24. Lorsque le basculement se produit, tous les ordinateurs virtuels qui font partie de ce sous-réseau est alors basculés vers le site de reprise et de conserver leurs adresses IP. Les itinéraires doivent être modifiées en conséquence pour refléter le fait que tous les ordinateurs virtuels appartenant au sous-réseau 192.168.1.0/24 ont été déplacés vers le site de reprise. 

Dans l’illustration suivante les itinéraires entre le site principal et site de reprise, troisième site et site principal et troisième site et site de récupération doivent être modifiées en conséquence. 

Les images suivantes indique les sous-réseaux avant le basculement. Sous-réseau 192.168.0.1/24 est actif sur le Site principal avant le basculement et devient actif du Site de récupération après le basculement 

![Avant le basculement](./media/site-recovery-network-design/network-design2.png)

Avant le basculement


L’image ci-dessous affiche les réseaux et les sous-réseaux après le basculement.
    
![Après le basculement](./media/site-recovery-network-design/network-design3.png)

Après le basculement

Dans votre site secondaire est local et que vous utilisez un serveur VMM pour gérer puis lors de l’activation de la protection d’un ordinateur virtuel spécifique, ASR allouera les ressources réseau en fonction du flux suivant :

- ASR alloue une adresse IP pour chaque interface réseau sur l’ordinateur virtuel du pool d’adresses IP statiques défini sur le réseau approprié pour chaque instance de System Center VMM.
- Si l’administrateur définit le même pool d’adresses IP pour le réseau sur le site de récupération que le pool d’adresses IP du réseau sur le site principal, lors de l’attribution de l’adresse IP de la machine virtuelle de réplica ASR affecte la même adresse IP que celle de l’ordinateur virtuel principal.  La période d’enquête est réservé dans VMM mais non défini comme IP de basculement. Failover IP définie juste avant le basculement.

![Conserver l’adresse IP](./media/site-recovery-network-design/network-design4.png)
    
Figure 5

La figure 5 illustre les paramètres TCP/IP de la reprise de la machine virtuelle de réplica (sur la console du Hyper-V). Ces paramètres doit être remplis juste avant le démarrage de l’ordinateur virtuel après un basculement

Si la même adresse IP n’est pas disponible, ASR affecte certains autres adresses IP disponibles à partir du pool d’adresses IP défini. 

Une fois que la machine virtuelle est activée pour une protection qu'après l’exemple de script vous permet de vérifier l’adresse IP qui a été alloué à la machine virtuelle. La même adresse IP est définie en tant que Failover IP et affectée à la machine virtuelle au moment du basculement sur incident :

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] Dans le scénario où les ordinateurs virtuels utilisent DHCP, la gestion des adresses IP est entièrement en dehors du contrôle de la récupération automatique du système. Un administrateur doit s’assurer que le serveur DHCP desservant les adresses IP sur le site de reprise peut être utilisé de la même plage que celle du site principal.

#### <a name="failover-to-azure"></a>Basculement vers Azure

Récupération de Site Azure (ASR) permet de Microsoft Azure pour être utilisé comme un site de reprise après sinistre pour vos ordinateurs virtuels. Dans ce cas, vous devrez traiter avec une contrainte plus. 

Examinons un scénario où une société fictive nommée la Woodgrove Bank a l’infrastructure sur site qui héberge leur gamme d’applications d’entreprise, et ils hébergent leurs applications mobiles sur Azure. La connectivité entre les ordinateurs virtuels de banque Woodgrove dans Azure et sur site des serveurs est fournie par un site à site (S2S) virtuel réseau privé (VPN). S2S VPN permet de réseau virtuel de la banque Woodgrove dans Azure pour être considérée comme une extension du réseau local de Woodgrove Bank. Cette communication est activée par réseau privé virtuel de S2S entre le bord de la Woodgrove Bank et Azure réseau virtuel. Woodgrove souhaite à présent utiliser la récupération ASR pour répliquer ses charges de travail en cours d’exécution dans son centre de données vers Azure. Cette option répond aux besoins de Woodgrove, qui souhaite une option économique de reprise après sinistre et est capable de stocker des données dans les environnements de cloud public. Woodgrove a traiter les applications et configurations qui dépendent des adresses IP codée de manière irréversible, donc ils ont besoin de conserver les adresses IP pour leurs applications après le basculement vers Azure.

Woodgrove a décidé d’attribuer des adresses IP à partir de la plage d’adresses IP (172.16.1.0/24, 172.16.2.0/24) à ses ressources en cours d’exécution dans Azure.

Pour Woodgrove être en mesure de répliquer ses machines virtuelles vers Azure tout en conservant les adresses IP, un réseau virtuel Azure doit être créé. Il doit être une extension du réseau local pour que les applications puissent le basculement à partir du site local vers Azure en toute transparence. Azure vous permet d’ajouter des connectivité VPN site à site ainsi que point-à-site aux réseaux virtuels créés dans Azure. Lorsque vous configurez votre connexion site à site, Azure réseau vous permet d’acheminer le trafic vers l’emplacement local (Azure appelle réseau local) uniquement si la plage d’adresses IP est différente de la plage d’adresses IP locales, car Azure ne prend en charge des sous-réseaux d’étirement.  Cela signifie que si vous avez un 192.168.1.0/24 sous-réseau local, vous ne pouvez pas ajouter un 192.168.1.0/24 réseau local du réseau Azure. Ceci est dû au fait que Azure ne sait pas qu’il n’y a aucun VMs actives dans le sous-réseau et que le sous-réseau est créé uniquement à des fins de reprise après sinistre. Pour être en mesure de router correctement le trafic réseau dans un réseau Azure que les sous-réseaux dans le réseau et le réseau local ne doivent pas entrer en conflit. 

![Avant le basculement de sous-réseau](./media/site-recovery-network-design/network-design7.png)

Avant le basculement

Pour aider à répondre aux besoins de leur entreprise Woodgrove, nous devons implémenter les flux de travail suivant :

- Créer un réseau supplémentaire, appelez-la récupération réseau, où les ordinateurs virtuels de basculé serait créés.
- Pour vous assurer que l’adresse IP d’un ordinateur virtuel est conservée après un basculement, accédez à l’onglet Configurer, sous propriétés de la machine virtuelle, de spécifier la même adresse IP que l’ordinateur virtuel dispose sur site, puis cliquez sur Enregistrer. Lorsque la machine virtuelle est basculée, récupération de Site Azure affecte l’adresse IP fourni à l’ordinateur virtuel. 

![Propriétés de réseau](./media/site-recovery-network-design/network-design8.png)

Une fois le basculement sur incident est déclenché et que les ordinateurs virtuels sont créés dans le réseau de récupération avec l’adresse IP de votre choix, la connexion à ce réseau peut être établie à l’aide d’un [Vnet à Vnet connexion](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Si cette action peut être l’objet d’un script.  Comme expliqué dans la section précédente à propos du basculement de sous-réseau, même en cas de basculement sur incident vers Azure, itinéraires devrait être modifiées en conséquence pour refléter que 192.168.1.0/24 a maintenant déplacé vers Azure. 

![Après le basculement de sous-réseau](./media/site-recovery-network-design/network-design9.png)

Après le basculement

Si vous n’avez pas un « réseau Azure » comme indiqué dans l’image ci-dessus. Vous pouvez créer une connexion vpn de site à site entre votre « Site principal » et le « Réseau de reprise » après le basculement.  


## <a name="option-2-changing-ip-addresses"></a>Option 2 : Modifier les adresses IP

Cette approche semble le plus répandu en fonction de ce que nous avons vu. Il prend la forme de modification de l’adresse IP de chaque machine virtuelle qui est impliqué dans le basculement. L’inconvénient de cette approche nécessite le trafic réseau de « apprendre » que l’application qui a été à IPx est maintenant à IPy. Même si IPx et IPy sont des noms logiques, les entrées DNS doivent généralement être modifié ou vidées dans tout le réseau et les entrées mises en cache dans les tables de réseau doivent être mis à jour ou vidé, donc un temps d’arrêt peut se produire en fonction de la façon dont l’infrastructure DNS a été configuré. Peuvent atténuer ces problèmes à l’aide de valeurs de durée de vie faibles dans le cas d’applications intranet et à l’aide du [Gestionnaire de trafic Azure avec la récupération automatique du système](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) pour les applications internet en fonction

### <a name="changing-the-ip-addresses---illustration"></a>Modification des adresses IP - Illustration

Examinons le scénario dans lequel vous envisagez d’utiliser IPs différents sur le serveur principal et les sites de récupération. Dans l’exemple suivant, nous aussi avons un troisième site d’où les applications hébergeant sur principal ou de récupération, site est accessible.

![IP différent : avant le basculement](./media/site-recovery-network-design/network-design10.png)

Figure 11

Dans la Figure 11, il existe certaines applications hébergées dans le sous-réseau 192.168.1.0/24 de sous-réseau sur le site principal, et ils ont été configurés pour arriver sur le site de récupération dans le sous-réseau 172.16.1.0/24 après un basculement. Itinéraires de réseau et les connexions VPN ont été correctement configurés pour que les trois sites puissent accéder à l’autre.
 
Comme la figure 12 montre, après le basculement d’une ou plusieurs applications, ils seront restaurés dans le sous-réseau de récupération. Dans ce cas nous ne sommes pas limités à basculement le sous-réseau complet en même temps. Aucune modification ne doit reconfigurer les itinéraires VPN ou réseau. Un basculement sur incident et certaines mises à jour DNS va s’assurer que les applications restent accessibles. Si le serveur DNS est configuré pour autoriser les mises à jour dynamiques les ordinateurs virtuels s’inscrit lui-même à l’aide de la nouvelle période d’enquête une fois qu’ils démarrent après un basculement. 

![Autre IP - après basculement](./media/site-recovery-network-design/network-design11.png)

Figure 12

Après échec de la machine virtuelle de réplica peut avoir une adresse IP qui n’est pas le même que l’adresse IP de l’ordinateur virtuel principal. Machines virtuelles met à jour le serveur DNS qu’ils utilisent après leur démarrage. Les entrées DNS doivent généralement être modifié ou vidé sur le réseau et les entrées mises en cache dans les tables de réseau doivent être mis à jour ou vidé, il n’est pas rare d’être confronté à temps d’arrêt pendant que ces modifications ont lieu. Ce problème peut être atténué par :

- À l’aide de faible durée de vie pour les applications intranet.
- À l’aide du Gestionnaire de trafic Azure avec la récupération automatique du système pour internet des applications.
- À l’aide du script suivant au sein de votre plan de récupération pour mettre à jour le serveur DNS pour assurer une mise à jour en temps voulu (le script n’est pas nécessaire si l’inscription DNS dynamique est configurée)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Modification des adresses IP – DR vers Azure

[L’Installation de l’Infrastructure réseau pour Microsoft Azure comme un Site de reprise après sinistre](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) de blog explique comment l’infrastructure de réseau requise Azure l’installation lors de la conservation des adresses IP de n’est pas une exigence. Il commence par décrire l’application et que vous recherchez sur Azure et concluant par comment effectuer un test de basculement et un basculement planifié et comment le programme d’installation de réseau sur site.



## <a name="next-steps"></a>Étapes suivantes

[Découvrez](site-recovery-network-mapping.md) comment restauration du Site cartes réseaux source et de cible lorsqu’un serveur VMM est utilisé pour gérer le site principal. 
