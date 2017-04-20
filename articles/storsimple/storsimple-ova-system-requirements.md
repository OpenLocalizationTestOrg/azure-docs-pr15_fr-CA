<properties
   pageTitle="Configuration requise tableau virtuel de StorSimple"
   description="En savoir plus sur le logiciel et la configuration réseau requise pour votre tableau virtuel StorSimple"
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
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>Configuration requise tableau virtuel de StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit la configuration requise important pour votre tableau virtuel Microsoft Azure StorSimple (également connu sous le nom StorSimple local périphérique virtuel ou un périphérique virtuel de StorSimple) et pour les clients de stockage accès au tableau. Nous vous recommandons d’examiner attentivement les informations avant de déployer votre système de StorSimple, puis y faire revenir si nécessaire pendant le déploiement et l’exploitation.

La configuration système requise est les suivantes :

-   Décrit **configuration logicielle requise pour les clients de stockage** : les plates-formes de virtualisation prises en charge, les navigateurs web, les initiateurs iSCSI, clients SMB, les exigences minimales de périphérique virtuel et des exigences supplémentaires pour les systèmes d’exploitation.

-   **Configuration requise pour le périphérique StorSimple de mise en réseau** - fournit des informations sur les ports qui doivent être ouverts sur votre pare-feu pour autoriser le trafic de gestion, nuage ou iSCSI.

Les informations de configuration requise du système StorSimple publiées dans cet article s’applique uniquement aux tableaux de Virtual StorSimple.

- Pour les périphériques 8000 série, passez à la [configuration système requise pour votre périphérique de la gamme 8000 de StorSimple](storsimple-system-requirements.md).
 
- Pour les périphériques série 7000, accédez à la [configuration système requise pour votre périphérique de la gamme 5000-7000 StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## <a name="software-requirements"></a>Configuration logicielle requise

La configuration logicielle requise pour inclure les informations sur les navigateurs web pris en charge, les versions SMB, les plates-formes de virtualisation et les exigences minimales de périphérique virtuel.

### <a name="supported-virtualization-platforms"></a>Plates-formes de virtualisation prises en charge


| **Hyperviseur** | **Version**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 et versions ultérieures |
| VMware ESXi    | 5.5 et version ultérieure                        |

### <a name="virtual-device-requirements"></a>Exigences relatives aux périphériques virtuels

| **Composant**                                | **Exigence**            |
|----------------------------------------------|----------------------------|
| Nombre de processeurs virtuels (noyaux) | 4                          |
| Minimum de mémoire (RAM)                         | 8 GO                       |
| Espace de disque<sup>1</sup>                       | Disque du système d’exploitation - 80 Go <br></br>Disque de données - 500 Go à 8 To|
| Nombre minimal d’interfaces de réseau       | 1                          |
| Configuration minimale de la bande passante Internet<sup>2</sup>       | 5 Mbits/s                     |

<sup>1</sup> - mince mis en service.

<sup>2</sup> - configuration réseau requise peut varier selon le taux de modification quotidien de données. Par exemple, si un périphérique a besoin de sauvegarder jusqu'à 10 Go ou plus de modifications pendant la journée, puis la sauvegarde quotidienne sur une connexion de Mbits/s 5 peut prendre jusqu'à 4,25 heures (si les données de ne peuvent pas être compressées ou dédupliquées).

### <a name="supported-web-browsers"></a>Navigateurs web pris en charge

| **Composant**     | **Version** | **Exigences/Remarques supplémentaires** |
|-------------------|-----------------|-----------------------------------|
| Bord de Microsoft    | Version la plus récente  |                                   |
| Internet Explorer | Version la plus récente  | Testé avec Internet Explorer 11  |
| Google Chrome     | Version la plus récente  | Testé avec Chrome 46             |

### <a name="supported-storage-clients"></a>Clients de stockage pris en charge 

La configuration logicielle minimale suivante est des initiateurs iSCSI qui accèdent à votre tableau virtuel StorSimple (configuré comme un serveur iSCSI).

| **Systèmes d’exploitation pris en charge** | **Version requise** | **Exigences/Remarques supplémentaires** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008 R2 SP1, 2012, 2012R2 |StorSimple pouvez créer provisionnée et entièrement mis en service des volumes. Il ne peut pas créer de volumes partiellement mis en service. StorSimple des volumes iSCSI sont uniquement pris en charge pour : <ul><li>Volumes simples sur des disques de base Windows.</li><li>Windows NTFS pour le formatage d’un volume.</li>|


La configuration logicielle minimale suivante est pour les clients SMB qui accèdent à votre tableau virtuel StorSimple (configuré comme un serveur de fichiers).

| **Version SMB** |
|-------------|
| SMB 2.x     |
| SMB 3.0     |
| SMB 3.02    |

> [AZURE.IMPORTANT] Ne pas copier ou stocker des fichiers protégés par Windows EFS (ENCRYPTING file) pour le serveur de fichiers tableau virtuel de StorSimple ; Ainsi, dans une configuration non prise en charge. 

## <a name="networking-requirements"></a>Exigences de mise en réseau 

Le tableau suivant répertorie les ports qui doivent être ouverts sur votre pare-feu afin de permettre d’iSCSI, SMB, nuage ou le trafic de gestion. Dans cette table, *en* *entrant* ou fait référence à la direction à partir de laquelle les demandes clientes entrantes accèdent à votre périphérique. *Out* ou *sortant* fait référence à la direction dans laquelle votre périphérique StorSimple envoie des données à l’extérieur, au-delà du déploiement : par exemple, sortant à Internet.

| **Port n°<sup>1</sup>** | **Ou arrière** | **Étendue de port** | **Obligatoire**              | **Notes**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | Sortie           | RÉSEAU ÉTENDU            | N°                        | Port de sortie est utilisé pour accéder à Internet pour récupérer des mises à jour. <br></br>Le proxy web sortant est configurable par l’utilisateur. |
| TCP 443 (HTTPS)          | Sortie           | RÉSEAU ÉTENDU            | Oui                       | Port de sortie est utilisé pour accéder aux données dans le nuage. <br></br>Le proxy web sortant est configurable par l’utilisateur. |
| UDP 53 (DNS)             | Sortie           | RÉSEAU ÉTENDU            | Dans certains cas ; reportez-vous à la section notes. | Ce port est nécessaire uniquement si vous utilisez un serveur DNS basé sur Internet. <br></br> **Remarque**: Si vous déployez un serveur de fichiers, nous vous recommandons d’utiliser le serveur DNS local.|
| UDP 123 (NTP)            | Sortie           | RÉSEAU ÉTENDU            | Dans certains cas ; reportez-vous à la section notes. | Ce port est nécessaire uniquement si vous utilisez un serveur NTP de basées sur Internet.<br></br> **Remarque :** Si vous déployez un serveur de fichiers, nous vous recommandons de synchroniser l’heure avec vos contrôleurs de domaine Active Directory.  |
| TCP 80 (HTTP)           | Dans            | RÉSEAU LOCAL            | Oui                       | C’est le port d’entrée pour l’interface utilisateur local sur le périphérique StorSimple pour la gestion locale. <br></br> **Remarque**: l’accès à l’interface utilisateur locale sur HTTP redirigera automatiquement vers HTTPS.|
| TCP 443 (HTTPS)          | Dans            | RÉSEAU LOCAL            | Oui                       | C’est le port d’entrée pour l’interface utilisateur local sur le périphérique StorSimple pour la gestion locale.|
| TCP 3260 (iSCSI)         | Dans            | RÉSEAU LOCAL            | N°                        | Ce port est utilisé pour accéder aux données via iSCSI.|

<sup>1</sup> aucun des ports entrants ne doivent être ouverts sur le réseau Internet public.

> [AZURE.IMPORTANT] Assurez-vous que le pare-feu ne pas modifier ou décrypter tout trafic SSL entre le dispositif de StorSimple et Azure.


### <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour les règles de pare-feu 

Administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur les modèles d’URL de filtrage entrant et le trafic sortant. Votre tableau virtuel et le service Gestionnaire de StorSimple dépendent d’autres applications Microsoft telles que les Bus des services Azure, Azure le contrôle d’accès Active Directory, comptes de stockage et les serveurs de Microsoft Update. Les modèles d’URL associés à ces applications permet de configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Cela à son tour nécessite l’administrateur réseau de contrôler et de mettre à jour les règles de pare-feu pour votre StorSimple en tant qu’et lorsque nécessaire. 

Nous vous conseillons de définir vos règles de pare-feu pour le trafic sortant, basé sur des adresses IP fixé, recourir largement dans la plupart des cas de StorSimple. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancé qui sont nécessaires pour créer des environnements sécurisés.

> [AZURE.NOTE] 
> 
> - L’adresses IP (source) du périphérique doit toujours être défini pour toutes les interfaces réseau de nuage. 
> - La destination IPs doit être définie de [Centre de données Azure des plages IP](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| Modèle d’URL                                                      | Fonctionnalités des composants                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Service Gestionnaire de StorSimple<br>Service de contrôle d’accès<br>Bus des services Azure|
|`http://*.backup.windowsazure.com`|Enregistrement de dispositif|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Révocation de certificats |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Comptes de stockage Azure et surveillance |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Serveurs Microsoft Update<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*`                    | Prise en charge                                                  |
| `http://*.data.microsoft.com `                   | Service de télémétrie dans Windows, consultez [mise à jour pour la satisfaction du client et de télémétrie de diagnostic](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Étape suivante

-   [Préparer le portail pour déployer votre tableau virtuel StorSimple](storsimple-ova-deploy1-portal-prep.md)
