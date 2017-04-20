<properties
   pageTitle="Configuration requise StorSimple | Microsoft Azure"
   description="Décrit des logiciels, mise en réseau et les exigences de haute disponibilité et meilleures pratiques pour une solution Microsoft Azure StorSimple."
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
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>Configuration réseau requise, haute disponibilité et logiciel de StorSimple

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple. Cet article décrit la configuration système importantes et conseillées pour votre périphérique StorSimple et pour les clients de stockage l’accès au périphérique. Nous vous recommandons d’examiner attentivement les informations avant de déployer votre système de StorSimple, puis y faire revenir si nécessaire pendant le déploiement et l’exploitation.

La configuration système requise est les suivantes :

- **Configuration logicielle requise pour les clients de stockage** - décrit les systèmes d’exploitation pris en charge et des exigences supplémentaires pour les systèmes d’exploitation.
- **Configuration requise pour le périphérique StorSimple de mise en réseau** - fournit des informations sur les ports qui doivent être ouverts sur votre pare-feu pour autoriser le trafic de gestion, nuage ou iSCSI.
- **Exigences de haute disponibilité pour StorSimple** - décrit les exigences de haute disponibilité et des meilleures pratiques pour votre ordinateur de périphérique et de l’hôte StorSimple. 


## <a name="software-requirements-for-storage-clients"></a>Configuration logicielle requise pour les clients de stockage

La configuration logicielle minimale suivante est pour les clients de stockage qui accèdent à votre périphérique de StorSimple.

| Systèmes d’exploitation pris en charge | Version requise | Exigences/Remarques supplémentaires |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008 R2 SP1, 2012, 2012R2 |StorSimple des volumes iSCSI sont pris en charge uniquement les types de disque de Windows suivants :<ul><li>Volume simple sur un disque de base</li><li>Volume simple et mis en miroir sur un disque dynamique</li></ul>Provisionnement fin de Windows Server 2012 et les fonctionnalités du fichier ODX sont pris en charge si vous utilisez un volume iSCSI de StorSimple.<br><br>StorSimple pouvez créer provisionnée et entièrement mis en service des volumes. Il ne peut pas créer de volumes partiellement mis en service.<br><br>Le reformatage d’un volume provisionné peut prendre beaucoup de temps. Nous vous recommandons de supprimer le volume, puis en créant un nouveau au lieu de remise en forme. Cependant, si vous avez toujours préférable de reformater un volume :<ul><li>Exécutez la commande suivante avant du reformater pour éviter des délais de récupération d’espace : <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Une fois la mise en forme terminée, utilisez la commande suivante pour ré-activer la récupération de l’espace :<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Appliquer le correctif Windows Server 2012, comme décrit dans la [base de connaissances 2878635](https://support.microsoft.com/kb/2870270) sur votre ordinateur Windows Server.</li></ul></li></ul></ul> Si vous configurez le Gestionnaire de snapshots de StorSimple ou de la carte de StorSimple pour SharePoint, consultez [configuration logicielle requise pour les composants facultatifs](#software-requirements-for-optional-components).|
| VMWare ESX | 5.5 et 6.0 | Prise en charge avec VMWare vSphere en tant que client iSCSI. VAAI-bloc est prise en charge avec VMware vSphere sur les périphériques StorSimple.
| Linux RHEL/CentOS | 5, 6 et 7 | Prise en charge pour les clients Linux iSCSI avec les versions open-iSCSI initiator 5, 6 et 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX n’est actuellement pas pris en charge avec StorSimple.

## <a name="software-requirements-for-optional-components"></a>Configuration logicielle requise pour les composants optionnels

La configuration logicielle minimale suivante est des composants facultatifs StorSimple (Gestionnaire de snapshots StorSimple et carte de StorSimple pour SharePoint).

| Composant | Plate-forme de l’hôte | Exigences/Remarques supplémentaires |
| --------------------------- | ---------------- | ------------- |
| Gestionnaire de capture instantanée StorSimple | Windows Server 2008 R2 Service Pack 1, 2012, 2012R2 | Gestionnaire de capture instantanée StorSimple sur Windows Server est nécessaire pour la sauvegarde/restauration de disques dynamiques en miroir et des sauvegardes cohérentes avec les applications.<br> Gestionnaire de snapshots StorSimple est pris en charge uniquement sur Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 et Windows Server 2012.<ul><li>Si vous utilisez la fenêtre Server 2012, vous devez installer .NET 3.5 – 4.5 avant d’installer le Gestionnaire de snapshots de StorSimple.</li><li>Si vous utilisez Windows Server 2008 R2 SP1, vous devez installer Windows Management Framework 3.0 avant d’installer le Gestionnaire de snapshots de StorSimple.</li></ul> |
| Carte StorSimple pour SharePoint | Windows Server 2008 R2 Service Pack 1, 2012, 2012R2 |<ul><li>StorSimple adaptateur pour SharePoint est uniquement pris en charge dans SharePoint 2010 et SharePoint 2013.</li><li>RBS nécessite SQL Server Enterprise Edition, version 2008 R2 ou 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Exigences de mise en réseau de votre périphérique StorSimple

Votre périphérique StorSimple est verrouillé. Cependant, les ports doivent être ouverts sur votre pare-feu pour permettre le trafic de gestion iSCSI et nuage. Le tableau suivant répertorie les ports qui doivent être ouverts sur votre pare-feu. Dans cette table, *en* *entrant* ou fait référence à la direction à partir de laquelle les demandes clientes entrantes accèdent à votre périphérique. *Out* ou *sortant* fait référence à la direction dans laquelle votre périphérique StorSimple envoie des données à l’extérieur, au-delà du déploiement : par exemple, sortant à Internet.

| Port n°<sup>1, 2</sup> | Ou arrière | Étendue de port | Obligatoire | Notes |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  Sortie |  RÉSEAU ÉTENDU | N° |<ul><li>Port de sortie est utilisé pour accéder à Internet pour récupérer des mises à jour.</li><li>Le proxy web sortant est configurable par l’utilisateur.</li><li>Pour autoriser les mises à jour du système, ce port doit également être ouvert pour le contrôleur fixé IPs.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Sortie | RÉSEAU ÉTENDU | Oui |<ul><li>Port de sortie est utilisé pour accéder aux données dans le nuage.</li><li>Le proxy web sortant est configurable par l’utilisateur.</li><li>Pour autoriser les mises à jour du système, ce port doit également être ouvert pour le contrôleur fixé IPs.</li><li>Ce port est également utilisé sur les deux contrôleurs pour le garbage collection.</li></ul>|
|UDP 53 (DNS) | Sortie | RÉSEAU ÉTENDU | Dans certains cas ; reportez-vous à la section notes. |Ce port est nécessaire uniquement si vous utilisez un serveur DNS basé sur Internet. |
| UDP 123 (NTP) | Sortie | RÉSEAU ÉTENDU | Dans certains cas ; reportez-vous à la section notes. |Ce port est nécessaire uniquement si vous utilisez un serveur NTP de basées sur Internet. |
| TCP 9354 | Sortie | RÉSEAU ÉTENDU | Oui |Le port de sortie est utilisé par le périphérique de le StorSimple de communiquer avec le service Gestionnaire de StorSimple. |
| 3260 (iSCSI) | Dans | RÉSEAU LOCAL | N° | Ce port est utilisé pour accéder aux données via iSCSI.|
| 5985 | Dans | RÉSEAU LOCAL | N° | Port d’entrée est utilisée par le Gestionnaire de snapshots StorSimple pour communiquer avec le périphérique StorSimple.<br>Ce port est également utilisé lorsque vous se connectez à distance de Windows PowerShell pour StorSimple sur HTTP. |
| 5986 | Dans | RÉSEAU LOCAL | N° | Ce port est utilisé lorsque vous vous connectez à distance à Windows PowerShell pour StorSimple via HTTPS. |

<sup>1</sup> aucun des ports entrants ne doivent être ouverts sur le réseau Internet public.

<sup>2</sup> si plusieurs ports comporte une configuration de passerelle, l’ordre de trafic routé sortant sera déterminé selon l’ordre de routage de port ci-dessous dans le [routage de Port](#routing-metric).

<sup>3</sup> le contrôleur fixé IPs sur votre périphérique de StorSimple doit être routable et capable de se connecter à Internet. Le des adresses IP fixes sont utilisés pour traiter les mises à jour pour le périphérique. Si les contrôleurs de périphérique ne peut pas se connecter à Internet via les adresses IP fixe, vous ne serez pas en mesure de mettre à jour votre périphérique StorSimple.

> [AZURE.IMPORTANT] Assurez-vous que le pare-feu ne pas modifier ou décrypter tout trafic SSL entre le dispositif de StorSimple et Azure.

### <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour les règles de pare-feu

Administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur les modèles d’URL de filtrage entrant et le trafic sortant. Votre périphérique StorSimple et le service Gestionnaire de StorSimple dépendent d’autres applications Microsoft telles que les Bus des services Azure, Azure le contrôle d’accès Active Directory, comptes de stockage et les serveurs de Microsoft Update. Les modèles d’URL associés à ces applications permet de configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Cela à son tour nécessite l’administrateur réseau de contrôler et de mettre à jour les règles de pare-feu pour votre StorSimple en tant qu’et lorsque nécessaire.

Nous vous conseillons de définir vos règles de pare-feu pour le trafic sortant, basé sur des adresses IP fixé, recourir largement dans la plupart des cas de StorSimple. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancé qui sont nécessaires pour créer des environnements sécurisés.

> [AZURE.NOTE] L’adresses IP (source) du périphérique doit toujours être défini pour toutes les interfaces réseau activée. La destination IPs doit être définie de [Centre de données Azure des plages IP](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>Modèles d’URL pour Azure portal
| Modèle d’URL                                                      | Fonctionnalités des composants                                           | DISPOSITIF IPs                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Service Gestionnaire de StorSimple<br>Service de contrôle d’accès<br>Bus des services Azure| Interfaces réseau de nuage        |
|`https://*.backup.windowsazure.com`|Enregistrement de dispositif| DONNÉES 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Révocation de certificats |Interfaces réseau de nuage |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Comptes de stockage Azure et surveillance | Interfaces réseau de nuage        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Serveurs Microsoft Update<br>                             | Contrôleur fixe IPs uniquement               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Contrôleur fixe IPs uniquement   |
| `https://*.partners.extranet.microsoft.com/*`                    | Prise en charge                                                  | Interfaces réseau de nuage        |

#### <a name="url-patterns-for-azure-government-portal"></a>Modèles d’URL pour le portail d’administration d’Azure
| Modèle d’URL                                                      | Fonctionnalités des composants                                           | DISPOSITIF IPs                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Service Gestionnaire de StorSimple<br>Service de contrôle d’accès<br>Bus des services Azure| Interfaces réseau de nuage        |
|`https://*.backup.windowsazure.us`|Enregistrement de dispositif| DONNÉES 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Révocation de certificats |Interfaces réseau de nuage |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Comptes de stockage Azure et surveillance | Interfaces réseau de nuage        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Serveurs Microsoft Update<br>                             | Contrôleur fixe IPs uniquement               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Contrôleur fixe IPs uniquement   |
| `https://*.partners.extranet.microsoft.com/*`                    | Prise en charge                                                  | Interfaces réseau de nuage        |

### <a name="routing-metric"></a>Métrique de l’itinéraire

Une métrique de routage est associé avec les interfaces et la passerelle qui acheminent les données sur les réseaux spécifiés. Métrique de l’itinéraire est utilisé par le protocole de routage pour calculer le meilleur chemin vers une destination donnée, si elle apprend à que plusieurs chemins d’accès existent pour la même destination. La plus faible le métrique de routage, plus la préférence est élevée.

Dans le cadre de StorSimple, si plusieurs passerelles et interfaces réseau sont configurées pour le trafic du canal, les métriques de l’itinéraire entreront en ligne pour déterminer l’ordre relatif dans lequel les interfaces va obtenir utilisés. Les métriques de routage ne peut pas être modifiés par l’utilisateur. Toutefois, vous pouvez utiliser la `Get-HcsRoutingTable` applet de commande pour imprimer la table de routage (et métriques) sur le périphérique StorSimple. Plus d’informations sur l’applet de commande Get-HcsRoutingTable de [StorSimple de résolution des problèmes de déploiement](storsimple-troubleshoot-deployment.md).

Les algorithmes de métriques de routage sont différentes selon la version du logiciel en cours d’exécution sur le périphérique StorSimple.

**Versions antérieures à la mise à jour 1**

Cela inclut les versions de logiciel avant la mise à jour 1 comme le GA, 0,1, 0,2 et 0,3 de version. L’ordre en fonction des métriques de l’itinéraire est la suivante :

   *Interface de réseau 10 Gigabit Ethernet configuré en dernier > interface de réseau Gigabit Ethernet de 10 autres > interface de réseau 1 Gigabit Ethernet configuré en dernier > interface de réseau Gigabit Ethernet de 1 autres*


**Versions de mise à jour 1 et avant la mise à jour 2**

Cela inclut les versions de logiciels tels que 1, 1.1 ou 1.2. L’ordre en fonction des métriques de l’itinéraire est déterminé comme suit :

   *DONNÉES 0 > configuré interface de réseau 10 Gigabit Ethernet en dernier > interface de réseau Gigabit Ethernet de 10 autres > interface de réseau 1 Gigabit Ethernet configuré en dernier > interface de réseau Gigabit Ethernet de 1 autres*

   Mise à jour 1, la métrique de routage de données 0 est faite le plus bas ; Par conséquent, tout le trafic de nuage est acheminé par l’intermédiaire de données 0. Prenez note de ce s’il y a plus d’une interface réseau compatible cloud sur le périphérique StorSimple.


**Versions de mise à jour 2**

Mise à jour 2 a plusieurs améliorations en matière de réseau et les métriques de l’itinéraire a été modifié. Le comportement peut être expliqué comme suit.

- Un ensemble de valeurs prédéterminées ont été affectés aux interfaces réseau.   

- Considérez une table exemple illustrée ci-dessous, avec les valeurs affectées aux différentes interfaces réseau lorsqu’ils sont activé pour le cloud ou nuage est désactivée, mais avec une passerelle configurée. Notez les valeurs affectés ici sont des exemples de valeurs uniquement.


  	| Interface réseau | Activé pour le cloud | Nuage-désactivé avec la passerelle |
  	|-----|---------------|---------------------------|
  	| Données 0  | 1            | -                        |
  	| Données 1  | 2            | 20                       |
  	| Données 2  | 3            | 30                       |
  	| Données 3  | 4            | 40                       |
  	| Données 4  | 5            | 50                       |
  	| Données 5  | 6            | 60                       |


- L’ordre dans lequel le trafic nuage sera routé via les interfaces de réseau est :

    *Données 0 > données 1 > Date 2 > données 3 > 4 de données > données 5*

    Cela peut s’expliquer par l’exemple suivant.

    Envisagez un dispositif de StorSimple avec deux interfaces réseau compatible cloud, données 0 et 5 des données. Nuage-désactivé sont données 1 à 4 de données mais ont une passerelle configurée. L’ordre dans lequel le trafic sera acheminé de ce périphérique seront :

    *0 (1) de données > données 5 (6) > 1 (20) de données > données 2 (30) > données 3 (40) > données 4 (50)*

    *Lorsque les nombres entre parenthèses indiquent la métrique de routage respectif.*

    En cas d’échouent de données de 0, le trafic de nuage est routé par le biais de données 5. Étant donné qu’une passerelle est configurée sur le réseau, si les données 0 et 5 des données ont été échoue, le trafic de nuage passe par le biais de données 1.


- En cas de défaillance d’une interface réseau de nuage, puis sont 3 tentatives avec un délai de 30 secondes pour se connecter à l’interface. Si toutes les tentatives échouent, le trafic est dirigée vers la suivante disponible activé pour le cloud tel que déterminé par la table de routage. Si tout le réseau activé pour le cloud interfaces échouent, puis le périphérique basculent sur l’autre contrôleur (aucun redémarrage dans ce cas).

- S’il existe une défaillance VIP pour une interface réseau iSCSI, il y aura 3 tentatives avec un retard de 2 secondes. Ce comportement a pas changé depuis les versions précédentes. Si toutes les interfaces réseau iSCSI échouent, un basculement sur incident du contrôleur produira (accompagné d’un redémarrage).


- Une alerte est également déclenchée sur votre appareil StorSimple lorsqu’il existe une défaillance VIP. Pour plus d’informations, accédez à [l’alerte de référence rapide](storsimple-manage-alerts.md).

- En termes de tentatives, iSCSI ont priorité sur le nuage.

    Prenons l’exemple suivant : StorSimple d’un dispositif a deux interfaces de réseau activé, données 0 et 1 de données. Données 0 soient activé pour le cloud que données 1 est à la fois cloud et prenant en charge l’iSCSI. Aucune autre interface réseau sur ce périphérique n’est activés pour nuage ou iSCSI.

    Si 1 des données échoue, étant donné qu’il est la dernière interface de réseau iSCSI, ainsi, un basculement sur incident du contrôleur 1 des données sur l’autre contrôleur.


### <a name="networking-best-practices"></a>Méthodes conseillées pour la mise en réseau

Outre les exigences réseau ci-dessus, pour des performances optimales de votre solution StorSimple, veuillez respecter les meilleures pratiques suivantes :

- Assurez-vous que votre périphérique de StorSimple a un dédié 40 Mbits/s de bande passante (ou plus) disponibles à tout moment. Cette bande passante ne doit pas être partagée (ou l’allocation doit être garantie au moyen de stratégies QoS) avec d’autres applications.

- Vérifiez la connectivité réseau à Internet est disponible à tout moment. Les connexions Internet sporadiques ou peu fiables pour les périphériques, y compris sans que ce soit, de connectivité Internet entraîne une configuration non prise en charge.

- Isoler le trafic iSCSI et nuage par dédiés interfaces réseau sur le périphérique d’accès iSCSI et le nuage. Pour plus d’informations, consultez Comment [modifier des interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces) sur le périphérique StorSimple.

- N’utilisez pas une configuration de protocole de contrôle des agrégation de liens (LACP) pour vos interfaces réseau. Il s’agit d’une configuration non prise en charge.


## <a name="high-availability-requirements-for-storsimple"></a>Exigences de haute disponibilité pour StorSimple

La plate-forme matérielle qui est fournie avec la solution de StorSimple a des fonctionnalités de disponibilité et de fiabilité qui fournissent une base pour une infrastructure de stockage hautement disponibles et à tolérance de pannes dans votre centre de données. Toutefois, il existe des exigences et meilleures pratiques qui vous devez se conformer afin de garantir la disponibilité de votre solution de StorSimple. Avant de déployer StorSimple, lisez attentivement les conditions suivantes et les meilleures pratiques pour le dispositif de StorSimple et ordinateurs hôtes connectés.

Pour plus d’informations sur la surveillance et la maintenance des composants matériels de votre périphérique StorSimple, accédez à [utiliser le service Gestionnaire de StorSimple pour surveiller l’état et les composants matériels](storsimple-monitor-hardware-status.md) et de [remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Exigences de haute disponibilité et des procédures de votre périphérique StorSimple

Passez en revue les informations suivantes avec soin pour en garantir la haute disponibilité de votre périphérique StorSimple.

#### <a name="pcms"></a>PCMs

StorSimple périphériques incluent l’alimentation redondante, remplaçable à chaud et des modules (PCM pour) ventilation. Chaque PCM possède une capacité suffisante pour fournir un service pour le châssis de l’ensemble. Pour garantir une haute disponibilité, les deux PCMs doivent être installés.

- Se connecter à votre PCMs aux différentes sources électriques pour offrir une disponibilité en cas de défaillance d’une source d’alimentation.
- En cas de défaillance d’un PCM, demander un remplacement immédiatement.
- Supprimer un échec PCM uniquement lorsque vous disposez le remplacement et prêt à l’installer.
- Ne supprimez pas simultanément les deux PCMs. Le module PCM inclut le module de batterie de secours. Suppression des PCMs entraînera un arrêt sans protection de batterie et l’état du périphérique n’est pas enregistrée. Pour plus d’informations sur la batterie, accédez à [tenir à jour le module de batterie de secours](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Modules de contrôleur

StorSimple périphériques incluent des modules de contrôleur redondants et remplaçables à chaud. Les modules de contrôleur fonctionnent en mode actif/passif. À un moment donné, un seul module de contrôleur est actif et fournit le service, tandis que l’autre module de contrôleur est passif. Le module de contrôleur passif est sous tension et opérationnel si le module de contrôleur actif échoue ou est supprimé. Chaque module de contrôleur possède une capacité suffisante pour fournir un service pour le châssis de l’ensemble. Les deux modules de contrôleur doivent être installés pour garantir une haute disponibilité.

- Assurez-vous que les deux modules de contrôleur sont installés en permanence.

- En cas de défaillance d’un module de contrôleur, demander un remplacement immédiatement.

- Supprimer un module de contrôleur défaillant uniquement lorsque vous disposez le remplacement et prêt à l’installer. Retrait d’un module pour des périodes étendues aura une incidence sur la circulation de l’air et, par conséquent, le refroidissement du système.

- Assurez-vous que les connexions réseau pour les deux modules de contrôleur sont identiques et les interfaces de réseau connecté ont une configuration réseau identiques.

- Si un module de contrôleur tombe en panne ou doit être remplacé, assurez-vous que l’autre module de contrôleur est dans un état actif avant de remplacer le module de contrôleur défaillant. Pour vérifier qu’un contrôleur est actif, passez à [identifier le contrôleur actif sur le périphérique](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Ne supprimez pas les deux modules de contrôleur en même temps. Si un basculement sur incident du contrôleur est en cours, ne pas arrêter le module contrôleur de secours ou retirez-la du châssis.

- Après un basculement sur incident du contrôleur, attendez au moins cinq minutes avant de supprimer un module de contrôleur.

#### <a name="network-interfaces"></a>Interfaces réseau

StorSimple dispositif contrôleur les modules chaque ont quatre 1 Gigabit et deux 10 interfaces réseau Gigabit Ethernet.

- Assurez-vous que les connexions réseau pour les deux modules de contrôleur sont identiques, et que les interfaces de module de contrôleur sont connectés pour avoir une configuration réseau identiques des interfaces réseau.

- Lorsque cela est possible, déployer des connexions réseau sur les différents commutateurs pour assurer la disponibilité du service en cas de panne de périphérique réseau.

- Lorsque vous déconnectez le seul ou dans l’interface compatible iSCSI restante dernier (avec les adresses IP affectée), désactiver tout d’abord de l’interface, puis débranchez les câbles. Si l’interface est tout d’abord déconnecté, puis il entraînera le contrôleur actif basculer vers le contrôleur passif. Si le contrôleur passif a également ses interfaces correspondantes est débranchés, les deux contrôleurs va redémarrer plusieurs fois avant de s’installer sur un contrôleur.

- Au moins deux interfaces de données de se connecter au réseau de chaque module de contrôleur.

- Si vous avez activé les deux interfaces de 10 GbE, déployez celles sur les différents commutateurs.

- Dans la mesure du possible, utilisez MPIO sur les serveurs afin de garantir que les serveurs peuvent tolérer un lien, un réseau ou une défaillance de l’interface.

Pour plus d’informations sur la mise en réseau votre périphérique pour la haute disponibilité et performances, atteindre [installer votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs et disques durs

StorSimple périphériques incluent les disques SSD (SSD) et disques durs (HDD) qui sont protégées à l’aide de mise en miroir des espaces. Utilisation d’espaces de mise en miroir garantit que le périphérique est en mesure de tolérer l’échec d’un ou plusieurs disques durs ou de SSDs.

- Assurez-vous que tous les modules SSD et les disques durs sont installés.

- En cas de défaillance d’un serveur ou un disque dur SSD, demander un remplacement immédiatement.

- Si un SSD ou un disque dur tombe en panne ou doit être remplacé, assurez-vous que vous supprimez uniquement les SSD ou un disque dur qui doit être remplacé.

- Ne supprimez pas plusieurs SSD ou disque dur du système à n’importe quel point dans le temps.
Perte de données potentielle et une défaillance système peut entraîner une défaillance des disques de 2 ou plus d’un type donné (disque dur, SSD) ou consécutives dans un délai court. Si cela se produit, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide.

- En cas de remplacement, surveiller l' **État du matériel** dans la page **gestion** des disques dans les disques durs SSD. Un état avec une coche verte indique que les disques sont en bon état ou OK, considérant qu’un point d’exclamation rouge point indique un SSD ou un disque dur défaillant.

- Nous vous recommandons de configurer des captures instantanées de cloud pour tous les volumes dont vous avez besoin pour protéger en cas de défaillance du système.

#### <a name="ebod-enclosure"></a>Boîtier EBOD

StorSimple 8600 comprend un boîtier étendu grappe de disques (EBOD) en plus du boîtier principal. Un EBOD contient des contrôleurs de EBOD et de mise en miroir de disques durs (HDD) qui sont protégées à l’aide d’espaces. Utilisation d’espaces de mise en miroir garantit que le périphérique est en mesure de tolérer l’échec d’un ou plusieurs disques durs. Le boîtier de la EBOD est connecté au boîtier principal via des câbles SAS redondants.

- Assurez-vous que les deux modules de contrôleur du boîtier EBOD, à la fois les câbles SAS et tous les disques durs sont installés en permanence.

- En cas de défaillance d’un module de contrôleur du boîtier EBOD, demander un remplacement immédiatement.

- Si un module de contrôleur du boîtier EBOD échoue, assurez-vous que l’autre module de contrôleur est actif avant de remplacer le module défectueux. Pour vérifier qu’un contrôleur est actif, passez à [identifier le contrôleur actif sur le périphérique](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Au cours d’un remplacement de module de contrôleur EBOD, surveiller en permanence l’état du composant dans le service Gestionnaire de StorSimple en accédant à la **Gestion des** > **état du matériel**.

- Si un câble SAS tombe en panne ou doit être remplacé (Microsoft Support doit intervenir pour effectuer une telle détermination), assurez-vous que vous supprimez uniquement le câble SAS qui doit être remplacé.

- Ne pas simultanément supprimez les câbles SAS du système à n’importe quel point dans le temps.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recommandations de haute disponibilité pour vos ordinateurs hôtes

Lisez attentivement ces meilleures pratiques pour garantir la haute disponibilité d’hôtes connectés à votre périphérique de StorSimple.

- Configurer StorSimple avec des [configurations de cluster de serveurs de deux nœuds de fichiers][1]. En supprimant les points uniques de défaillance et la création de redondance du côté hôte, l’ensemble de la solution devienne hautement disponible.

- Utiliser des partages (CA) disponibles en permanence disponibles avec Windows Server 2012 (SMB 3.0) pour une haute disponibilité au cours du basculement des contrôleurs de stockage. Pour plus d’informations pour la configuration des clusters de serveurs de fichiers et les partages en permanence disponibles avec Windows Server 2012, consultez cette [vidéo de démonstration](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les limites du système StorSimple](storsimple-limits.md).
- [Découvrez comment déployer votre solution StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
