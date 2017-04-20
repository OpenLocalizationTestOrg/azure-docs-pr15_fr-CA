<properties 
   pageTitle="Pratiques recommandées pour le tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit les méthodes conseillées pour le déploiement et la gestion de la baie virtuelle StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Meilleures pratiques de tableau virtuel de StorSimple

## <a name="overview"></a>Vue d’ensemble

Tableau virtuel de Microsoft Azure StorSimple est une solution de stockage intégrée qui gère les tâches de stockage entre un périphérique virtuel sur site s’exécutant dans un hyperviseur et le stockage en nuage Microsoft Azure. Tableau virtuel de StorSimple est une alternative efficace et économique, à la baie physique 8000. Le tableau virtuel peut s’exécuter sur votre infrastructure de l’hyperviseur, prend en charge l’iSCSI et les protocoles SMB et est parfaitement adapté aux scénarios de bureaux distants/succursales. Pour plus d’informations sur les solutions de StorSimple, passez à la [Vue d’ensemble de Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Cet article décrit les meilleures pratiques mises en oeuvre au cours de l’installation initiale, le déploiement et la gestion du tableau virtuel StorSimple. Ces meilleures pratiques fournissent des instructions validées pour la configuration et la gestion de votre tableau virtuel. Cet article est ciblé sur les administrateurs informatiques qui déploient et de gérer les baies virtuels dans leurs centres de données.

Nous vous recommandons une révision périodique des meilleures pratiques afin de garantir que votre périphérique est toujours en conformité lorsque des modifications sont apportées au flux d’installation ou d’opération. Si vous rencontrez des problèmes lors de la mise en œuvre de ces meilleures pratiques sur votre tableau virtuel, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide.

## <a name="configuration-best-practices"></a>Meilleures pratiques de configuration 

Ces meilleures pratiques couvrent les instructions à suivre au cours de l’installation initiale et le déploiement des tableaux virtuels. Ces bonnes pratiques incluent celles liées à la mise en service de l’ordinateur virtuel, les paramètres de stratégie de groupe, dimensionnement, configuration de la mise en réseau, la configuration des comptes de stockage et création des partages et des volumes pour le tableau virtuel. 

### <a name="provisioning"></a>Mise en service 

StorSimple Virtual Array est une machine virtuelle (VM) sur l’hyperviseur (Hyper-V ou VMware) de votre serveur hôte. Lors de la mise en service de l’ordinateur virtuel, assurez-vous que votre hôte est en mesure de consacrer des ressources suffisantes. Pour plus d’informations, accédez aux [ressources minimales requises](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) pour un tableau de mise en service. 

Implémenter les méthodes conseillées suivantes lors de la mise en service de la baie virtuelle :


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Type de machine virtuelle**   | **Génération 2** VM pour utilisation avec Windows Server 2012 ou version ultérieure et d’une image *.vhdx* . <br></br> **Génération 1** VM pour utilisation avec un serveur Windows Server 2008 ou version ultérieure et une image *.vhd* .                                                                                                              | Utilisez la version de machine virtuelle 8-11 lorsque *.vmdk* image.                                                                      |
| **Type de mémoire**            | Configurer en tant que **mémoire statique**. <br></br> N’utilisez pas l’option de **mémoire dynamique** .            |                                                    |
| **Type de données de disque**         | Mise en service en tant que de **taille dynamique**.<br></br> **Taille fixe** prend beaucoup de temps. <br></br> N’utilisez pas l’option **de différenciation** .                                                                                                                   | Utilisez l’option **d’un provisionnement fin** .                                                                                      |
| **Modification des données disque** | Le développement ou la réduction n’est pas autorisée. Une tentative d’effectuer cette opération entraîne la perte de toutes les données sur le périphérique locales.                       | Le développement ou la réduction n’est pas autorisée. Une tentative d’effectuer cette opération entraîne la perte de toutes les données sur le périphérique locales. |

### <a name="sizing"></a>Dimensionnement

Lors du dimensionnement de votre tableau virtuel StorSimple, tenez compte des facteurs suivants :

- Réservation des locale pour les volumes ou des partages. Environ 12 % de l’espace est réservé au niveau local pour chaque volume de hiérarchisé mis en service ou d’un partage. Environ 10 % de l’espace est également réservé pour un volume localement épinglé pour le système de fichiers.
- Instantané de surcharge. Environ 15 % de l’espace au niveau local est réservé aux instantanés.
- Besoin de restaurations. Si cette restauration comme une nouvelle opération, dimensionnement doit comptabiliser l’espace nécessaire pour la restauration. La restauration est terminé pour un partage ou d’un volume de même taille ou plus grand.
- Une mémoire tampon doit être alloué pour toute croissance inattendue.

Selon les facteurs ci-dessus, les exigences de dimensionnement peuvent être représentées par l’équation suivante :

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Les exemples suivants illustrent comment vous pouvez redimensionner un tableau virtuel selon vos besoins.

#### <a name="example-1"></a>Exemple 1 :
Sur votre tableau virtuel, vous voulez être en mesure de 

- provisionner un to 2 hiérarchisé volume ou partage.
- provisionner un to 1 volume monté en cascade ou partage.
- mettre en service un volume localement épinglé 300 Go ou partager.


Pour les volumes ou les partages précédent, nous calculer l’espace requis au niveau local. 

Tout d’abord, pour chaque volume/partage, hiérarchisé réservation locale serait égale à 12 % de la taille de volume/partage. Pour le partage/volume localement épinglé, réservation locale est 10 % de la taille de volume/partage localement épinglé (en plus de la taille de la mise en service). Dans cet exemple, vous avez besoin

- Réservation de local de 240 Go (pour un 2 To hiérarchisé volume/partage)
- Réservation de local de 120 Go (pour un 1 to hiérarchisé volume/partage)
- 330 Go pour volume localement épinglé ou partage (en ajoutant 10 % de réservation locale à la taille de la mise en service de 300 Go)

L’espace total requis jusqu’au niveau local est : 240 Go + 120 Go + 330 = 690 go.

Ensuite, nous devons au moins autant d’espace au niveau local comme la réservation unique plus grande. Ce montant supplémentaire est utilisé au cas où vous devez restaurer à partir d’une capture instantanée du nuage. Dans cet exemple, la plus grande locale réservation est Go 330 (y compris la réservation pour le système de fichiers), afin que vous ajoutez à la Go : 690 690 + 330 Go = 1020 go.
Si nous avons effectué des restaurations supplémentaires suivantes, nous pouvons toujours libérer de l’espace à partir de l’opération de restauration précédente.

Troisièmement, nous devez 15 % de l’espace total local jusqu’ici pour stocker des snapshots locaux, afin que 85 % de celui-ci est disponible. Dans cet exemple, qui serait d’environ 1020 Go = 0,85&ast;to de disques de données mis en service. Ainsi, le disque de données mis en service est (1020&ast;(1/0,85)) = 1 200 Go = 1,20 To ~ 1,25 To (arrondi au plus proche quartile)

Factorisation de croissance inattendue et les restaurations de nouveau, vous devez prévoir un disque local du autour de 1,25-1,5 To.

> [AZURE.NOTE] Nous recommandons également que le disque local est exactement provisionné. Cette recommandation est comme l’espace de restauration est seulement nécessaire lorsque vous souhaitez restaurer les données antérieures à cinq jours. Restauration au niveau élément vous permet de restaurer les données sans nécessiter de l’espace supplémentaire pour la restauration des cinq derniers jours.

#### <a name="example-2"></a>Exemple 2 : 
Sur votre tableau virtuel, vous voulez être en mesure de 

- provisionner un to 2 niveaux de volume
- prévoir un volume localement épinglé de 300 Go

En fonction de 12 % de réservation d’espace local de volumes/partages hiérarchisés et 10 % pour les volumes/partages ajoutés localement, nous devons

- Réservation de local de 240 Go (2 To hiérarchisé volume/partage)
- 330 Go pour volume localement épinglé ou partage (en ajoutant 10 % de réservation locale à l’espace de mise en service de 300 Go)

Espace total requis sur la couche locale est : 240 Go + 330 = 570 Go

Espace local minimal requis pour la restauration est 330 go. 

15 % de votre disque total est utilisé pour stocker les instantanés afin que seul 0,85 est disponible. Par conséquent, la taille du disque est (900&ast;(1/0,85)) = to 1,06 ~ 1,25 To (arrondi au plus proche quartile) 

Factorisation dans toute croissance inattendue, vous pouvez prévoir un disque local 1,25-1,5 To.


### <a name="group-policy"></a>Stratégie de groupe

Stratégie de groupe est une infrastructure qui vous permet d’implémenter des configurations spécifiques pour les utilisateurs et les ordinateurs. Les paramètres de stratégie de groupe sont contenus dans des objets de stratégie de groupe (GPO), qui sont liés aux conteneurs Active Directory Domain Services (AD DS) suivants : sites, domaines ou unités d’organisation (UO). 

Si votre tableau virtuel est joint au domaine, objets stratégie de groupe peut être appliqué. Ces objets de stratégie de groupe peuvent installer des applications telles qu’un logiciel antivirus qui risque de nuire au fonctionnement de la baie virtuelle StorSimple.

Par conséquent, nous recommandons que vous :

-   Assurez-vous que votre tableau virtuel est dans sa propre unité d’organisation (UO) pour Active Directory. 

-   Assurez-vous qu’aucun objet de stratégie de groupe (GPO) n’est appliqués à votre tableau virtuel. Vous pouvez bloquer l’héritage pour vous assurer que le tableau virtuel (nœud enfant) n’hérite pas automatiquement les objets de stratégie de groupe à partir du parent. Pour plus d’informations, accédez à [Bloquer l’héritage](https://technet.microsoft.com/library/cc731076.aspx).


### <a name="networking"></a>Mise en réseau

La configuration du réseau pour votre tableau virtuel s’effectue via l’interface utilisateur de web local. Une interface de réseau virtuel est activée par le biais de l’hyperviseur, dans lequel le tableau virtuel est mis en service. Utilisez la page [Paramètres du réseau](storsimple-ova-deploy3-fs-setup.md) pour configurer l’adresse IP de l’interface réseau virtuel, le sous-réseau et la passerelle.  Vous pouvez également configurer les serveurs DNS principal et secondaire, les paramètres de temps et des paramètres facultatifs pour votre périphérique. La majeure partie de la configuration du réseau est une configuration unique. Passez en revue les [exigences de mise en réseau de StorSimple](storsimple-ova-system-requirements.md#networking-requirements) avant de déployer le tableau virtuel.

Lorsque vous déployez votre tableau virtuel, nous vous recommandons de suivre ces meilleures pratiques :

-   Assurez-vous que le réseau dans lequel le tableau virtuel est toujours déployé a la capacité de dédier la bande passante de 5 Mbits/s Internet (ou plus). 

    -   Nécessité de la bande passante Internet varie selon les caractéristiques de la charge de travail et de la fréquence de modification des données.

    -   La modification des données qui peut être gérée est directement proportionnelle à votre bande passante Internet. Ainsi, lorsqu’une sauvegarde, une bande passante de 5 Mbits/s peut prendre en charge une modification de données de 18 Go environ 8 heures. Avec quatre fois plus de bande passante (20 Mbits/s), vous pouvez gérer la modification de quatre fois plus de données (72 Go). 

-   Vérifiez la connectivité à Internet est toujours disponible. Les connexions Internet sporadiques et non fiables aux périphériques peuvent entraîner une perte d’accès aux données dans le nuage et pourraient aboutir à une configuration non prise en charge.

-   Si vous envisagez de déployer votre périphérique en tant qu’un serveur iSCSI : 
    -   Nous vous recommandons de désactiver l’option **adresse IP d’obtenir automatiquement** (DHCP). 
    -   Configurer des adresses IP statiques. Vous devez configurer un serveur principal et un serveur DNS secondaire.

    -   Si la définition de plusieurs interfaces réseau sur votre serveur virtuel de tableau, la première interface de réseau (par défaut, cette interface est **Ethernet**) peut atteindre le nuage. Pour contrôler le type de trafic, vous pouvez créer plusieurs interfaces réseau virtuel sur votre tableau virtuel (configuré comme un serveur iSCSI) et connecter ces interfaces à différents sous-réseaux.

-   Pour limiter la bande passante nuage uniquement (utilisée par le tableau virtuel), configurer la régulation sur le routeur ou le pare-feu. Si vous définissez la limitation dans votre hyperviseur, il accélèrera tous les protocoles y compris iSCSI et SMB au lieu de simplement la bande passante de nuage. 

-   Assurez-vous que la synchronisation horaire des hyperviseurs sont activée. Si l’utilisation de Hyper-V, de sélectionner votre tableau virtuel dans le Gestionnaire Hyper-V, passez à le **paramètres &gt; Integration Services**et assurez-vous que la **synchronisation de l’heure** est activée.

### <a name="storage-accounts"></a>Comptes de stockage

Tableau virtuel de StorSimple peut être associé à un compte de stockage unique. Ce compte de stockage peut être un compte de stockage généré automatiquement, un compte dans l’abonnement de même que le service, ou un compte de stockage lié à un autre abonnement. Pour plus d’informations, consultez Comment [Gérer les comptes de stockage pour votre tableau virtuel](storsimple-ova-manage-storage-accounts.md).

Utilisez les recommandations suivantes pour les comptes de stockage associées à votre tableau virtuel.

-   Lorsque vous liez plusieurs réseaux virtuels avec un compte de stockage unique, prendre en compte la capacité maximale (to 64) pour un tableau virtuel et la taille maximale (500 To) pour un compte de stockage. Ce paramètre limite le nombre de réseaux virtuels en pleine taille qui peut être associé à ce compte de stockage à environ 7.

-   Lors de la création d’un nouveau compte de stockage
    -   Nous vous recommandons de créer il dans la région le plus proche de l’office de bureaux distants/succursales où votre tableau virtuel StorSimple est déployé afin de réduire les latences.

    -   L’esprit que vous ne pouvez pas déplacer un compte de stockage entre les différentes régions. Également Impossible de déplacer un service sur abonnement.

    -   Utilisez un compte de stockage qui implémente la redondance entre les centres de données. Stockage localement redondants (LRS), géo-redondants stockage (GRS) et stockage redondant de Zone (ZRS) sont toutes prises en charge pour une utilisation avec votre tableau virtuel. Pour plus d’informations sur les différents types de comptes de stockage, accédez à [la réplication du stockage Azure](../storage/storage-redundancy.md).


### <a name="shares-and-volumes"></a>Partages et des volumes

Pour votre tableau virtuel de StorSimple, vous pouvez prévoir des actions lorsqu’il est configuré comme un serveur de fichiers et les volumes lorsqu’il est configuré comme un serveur iSCSI. Les méthodes conseillées pour la création de partages et volumes sont liées à la taille et le type configuré.

#### <a name="volumeshare-size"></a>Taille de volume/partage

Dans votre tableau virtuel, vous pouvez prévoir des actions lorsqu’il est configuré comme un serveur de fichiers et les volumes lorsqu’il est configuré comme un serveur iSCSI. Les méthodes conseillées pour la création de partages et volumes se rapportent à la taille et le type configuré. 

Gardez à l’esprit les méthodes conseillées suivantes lors de la mise en service des partages ou des volumes sur votre périphérique virtuel.

-   La taille des fichiers par rapport à la taille de la mise en service d’un partage hiérarchisé peut affecter les performances de hiérarchisation. Travailler avec des fichiers volumineux peut entraîner un niveau lent out. Lorsque vous travaillez avec des fichiers volumineux, il est recommandé que le plus grand fichier inférieur à 3 % de la taille du partage.

-   Un maximum de 16 volumes/partages peut être créé sur le tableau virtuel. Si localement épinglé, les volumes/partages possible entre 50 Go et 2 To. Si plusieurs niveaux, les volumes/partages doit être entre 500 Go et 20 To. 

-   Lors de la création d’un volume, facteur dans la consommation de données attendues, ainsi que la croissance future. Alors que le volume ne peut pas être développé ultérieurement, vous pouvez toujours restaurer vers un volume plus important.

-   Une fois que le volume a été créé, vous ne pouvez pas réduire la taille du volume sur StorSimple.
   
-   Lors de l’écriture sur un volume monté en cascade sur StorSimple, lorsque les données du volume atteint un certain seuil (par rapport à l’espace local réservée pour ce volume), l’e/s est limité. Continuer à écrire sur ce volume ralentit considérablement le l’e/s. Si vous disposez d’un volume monté en cascade au-delà de sa capacité de mise en service (nous n’activement arrêtez pas l’utilisateur à partir de l’écriture au-delà de la capacité de mise en service), vous voyez une notification d’alerte à l’effet que vous avez expérimente beaucoup. Une fois que vous consultez l’alerte, il est impératif de prendre des mesures de réparation de supprimer les données du volume ou de la restauration du volume vers un volume plus important (extension de volume est actuellement pas pris en charge).

-   Pour des exemples d’utilisation de reprise après sinistre, comme le nombre de partages/volumes autorisées est 16 et le nombre maximal de partages/volumes qui peuvent être traitées en parallèle est également 16, le nombre de partages/volumes n’a pas une incidence sur vos RPO et le RTO. 

#### <a name="volumeshare-type"></a>Type de volume/partage

StorSimple prend en charge deux types de volume/partage en fonction de l’utilisation : localement mis en attente et hiérarchisé. Volumes/partages ajoutés localement sont thickly en service alors que les volumes/partages hiérarchisés sont exactement provisionnés. 

Nous vous recommandons de mettre en œuvre les méthodes conseillées suivantes lors de la configuration des volumes/partages de StorSimple :

-   Identifiez le type de volume basé sur les charges de travail que vous projetez de déployer avant de créer un volume. Utiliser les volumes ajoutés localement pour les charges de travail nécessitant des garanties locales des données (même suite à une panne de nuage) et qui requièrent des latences de nuage faible. Une fois que vous créez un volume sur votre tableau virtuel, vous ne pouvez pas modifier le type de volume à partir de localement par épinglé à plusieurs niveaux ou *vice versa*. Par exemple, créer des volumes ajoutés localement lors du déploiement de charges de travail SQL ou de charges de travail hébergeant des machines virtuelles (VM) ; Utilisez des volumes hiérarchisés pour les charges de travail de partage de fichier.

-   Vérifiez l’option pour les données d’archivage moins fréquemment utilisées lors du traitement de fichiers volumineux. Une plus grande taille de fragment de déduplication de 512 Ko est utilisée lorsque cette option est activée afin d’accélérer le transfert des données vers le nuage.

#### <a name="volume-format"></a>Format de volume

Après avoir créé les volumes StorSimple sur votre serveur iSCSI, vous devez initialiser, de montage et de formater les volumes. Cette opération est exécutée sur l’hôte connecté à votre périphérique de StorSimple. Meilleures pratiques suivantes sont recommandées lors de la mise en forme de volumes sur l’hôte StorSimple et de montage.

-   Effectuer un formatage rapide sur tous les volumes de StorSimple.

-   Lors du formatage d’un volume de StorSimple, utilisez une taille d’unité d’allocation (Australie) de 64 Ko (valeur par défaut est 4 Ko). L’Australie de 64 Ko est basé sur les tests effectués en interne pour les charges de travail communes StorSimple et autres charges de travail.

-   Lorsque vous utilisez le tableau virtuel StorSimple configuré comme un serveur iSCSI, n’utilisez pas de volumes fractionnés ou disques dynamiques en tant que ces volumes ou disques ne sont pas pris en charge par StorSimple.

#### <a name="share-access"></a>Accès au partage

Lors de la création de partages sur le serveur de fichier tableau virtuel, suivez ces instructions :

-   Lors de la création d’un partage, affecter un groupe d’utilisateurs en tant qu’un administrateur de partage au lieu d’un seul utilisateur.

-   Vous pouvez gérer les autorisations NTFS une fois que le partage est créé en modifiant les partages via l’Explorateur Windows.

#### <a name="volume-access"></a>Accès aux volumes

Lors de la configuration les volumes iSCSI sur votre tableau virtuel StorSimple, il est important de contrôler l’accès si nécessaire. Pour déterminer les serveurs hôtes peuvent accéder à volumes, créer et associer les enregistrements de contrôle d’accès (ACRs) StorSimple volumes.

Utilisez les méthodes conseillées suivantes lors de la configuration ACRs pour les volumes de StorSimple :

-   Toujours associer au moins un blocage à un volume.

-   Permet de définir plusieurs ACRs uniquement dans un environnement en clusters.

-   Lors de l’affectation de plus d’un blocage sur un volume, assurez-vous que le volume n’est pas exposé d’une manière où il est accessible simultanément par plusieurs hôtes en cluster. Si vous avez attribué plusieurs ACRs pour un volume, un message d’avertissement s’affiche pour vous permettre de vérifier votre configuration.

### <a name="data-security-and-encryption"></a>Cryptage et sécurité des données

Votre tableau virtuel StorSimple a des fonctionnalités de sécurité et de cryptage données qui garantissent la confidentialité et l’intégrité de vos données. Lors de l’utilisation de ces fonctionnalités, il est recommandé de suivre ces méthodes conseillées : 

-   Définir une clé de cryptage de stockage cloud pour générer le chiffrement AES-256 avant que les données sont envoyées à partir de votre tableau virtuel vers le nuage. Cette clé n’est pas requise si vos données sont cryptées dans un premier temps. La clé peut être générée et protégée à l’aide d’un système de gestion de clés comme [coffre-fort de clé Azure](../key-vault/key-vault-whatis.md).

-   Lorsque vous configurez le compte de stockage via le service Gestionnaire de StorSimple, assurez-vous que vous activez le mode SSL créer un canal sécurisé pour la communication réseau entre votre périphérique StorSimple et le nuage.

-   Régénérer les clés pour vos comptes de stockage (via le service de stockage Azure) périodiquement sur compte pour accéder à toutes les modifications en fonction de la liste modifiée des administrateurs.

-   Les données de votre tableau virtuel sont compressées et dédupliquées avant d’être envoyée vers Azure. Nous ne vous recommandons d’utiliser le service de rôle de déduplication des données sur votre hôte Windows Server.


## <a name="operational-best-practices"></a>Meilleures pratiques opérationnelles

Les meilleures pratiques sont des instructions qui doivent être suivies lors de la gestion au jour le jour ou l’opération du tableau virtuel. Ces pratiques traitent des tâches de gestion spécifiques tels que les sauvegardes, la restauration à partir d’un jeu de sauvegarde, effectue un basculement sur incident, la désactivation et la suppression de la matrice, la surveillance de la santé et l’utilisation du système et l’exécution des virus analyse sur votre tableau virtuel.

### <a name="backups"></a>Sauvegardes

Les données de votre tableau virtuel sont sauvegardées dans le cloud de deux façons, par défaut automatique de sauvegarde quotidienne du périphérique entier commençant à 22 h 30 ou via une sauvegarde manuelle de la demande. Par défaut, le périphérique crée automatiquement des captures instantanées quotidiennes de nuage de toutes les données résidant sur celui-ci. Pour plus d’informations, accédez à [sauvegarder votre tableau virtuel StorSimple](storsimple-ova-backup.md).

La fréquence et la rétention associé aux sauvegardes par défaut ne peut pas être modifiées, mais vous pouvez configurer l’heure à laquelle les sauvegardes quotidiennes sont lancés chaque jour. Lors de la configuration de l’heure de début pour les sauvegardes automatisées, nous vous recommandons :

-   Planifiez vos sauvegardes pendant les heures creuses. Heure de début de la sauvegarde ne doit pas coïncider avec hôte de nombreuses e/s.

-   Initier une sauvegarde manuelle de la demande lors de la planification effectuer un basculement de périphérique ou un préalable à la fenêtre de maintenance, pour protéger les données de votre tableau virtuel.

### <a name="restore"></a>Restauration

Vous pouvez restaurer à partir d’un jeu de deux manières différentes de sauvegarde : restaurer vers un autre volume ou un partage ou d’effectuer une restauration au niveau élément (disponible uniquement sur un groupe virtuel configuré comme un serveur de fichiers). Restauration au niveau élément vous permet d’effectuer une restauration granulaire des fichiers et des dossiers à partir d’une sauvegarde de nuage de toutes les actions sur le périphérique StorSimple. Pour plus d’informations, accédez à [restaurer à partir d’une sauvegarde](storsimple-ova-restore.md).

Lors d’une restauration, tenez compte des instructions suivantes :

-   Votre tableau virtuel StorSimple ne supporte pas la restauration sur place. Cela peut cependant être facilement obtenus par un processus en deux étapes : libérer de l’espace sur le serveur virtuel de tableau, puis restaurer vers un autre volume/partage.

-   Lors de la restauration à partir d’un volume local, gardez à l’esprit la restauration sera une opération longue. Bien que le volume peut rapidement en ligne, les données continuent à être hydraté en arrière-plan.

-   Le type de volume reste la même pendant le processus de restauration. Un volume monté en cascade est restauré vers un autre volume monté en cascade et un volume localement épinglé à une autre localement épinglé le volume.

-   Lorsque vous tentez de restaurer un volume ou un partage à partir d’une sauvegarde de la valeur, si l’opération de restauration échoue, un volume cible ou partage peut encore être créé dans le portail. Il est important de supprimer ce volume cible inutilisées ou partager le portail afin de minimiser les problèmes futurs découlant de cet élément.

### <a name="failover-and-disaster-recovery"></a>Basculement sur incident et de reprise après sinistre

Un périphérique de basculement vous permet migrer vos données à partir d’un périphérique *source* dans le centre de données à un autre équipement *cible* que qui se trouve dans le même ou un autre emplacement géographique. Le basculement de périphérique est pour l’ensemble de l’unité. Au cours du basculement, les données de nuage pour le périphérique source modifie la propriété à celle de l’équipement cible.

Pour votre tableau virtuel StorSimple, vous pouvez uniquement basculer sur un autre tableau virtuel géré par le service de gestionnaire de StorSimple même. Un basculement vers un périphérique 8000 série ou un tableau géré par un autre service de gestionnaire de StorSimple (celui du périphérique source) n’est pas autorisé. Pour en savoir plus sur les considérations de basculement sur incident, accédez à [conditions préalables pour le basculement entre périphériques](storsimple-ova-failover-dr.md).

Lors d’un basculement sur pour votre tableau virtuel, tenez compte des éléments suivants :

-   Pour un basculement planifié, il est recommandé de prendre tous les volumes/partages en mode hors connexion avant de lancer le basculement. Suivez les instructions spécifiques au système d’exploitation et de prendre les volumes/partages en mode hors connexion sur l’hôte du premier puis déconnecter ceux sur votre périphérique virtuel.

-   Un fichier serveur reprise après sinistre (DR), nous vous recommandons de rejoindre le périphérique cible dans le même domaine que la source afin que les autorisations de partage sont automatiquement résolues. Cette version prend en charge seulement le basculement vers un équipement cible dans le même domaine.

-   Une fois la reprise après sinistre est effectuée avec succès, le périphérique source est supprimé automatiquement. Si le périphérique n’est plus disponible, l’ordinateur virtuel que vous avez configuré sur le système hôte utilise toujours des ressources. Nous vous recommandons de supprimer cet ordinateur virtuel à partir de votre système hôte pour empêcher l’accumulation des frais.

-   Notez que même si le basculement échoue, **les données sont toujours sûres dans le nuage**. Prenez en compte les trois scénarios suivants dans lesquels le basculement n’a pas réussi :

    -   Une erreur s’est produite dans les étapes initiales du basculement, par exemple lorsque les contrôles avant de reprise après sinistre sont en cours d’exécution. Dans ce cas, votre périphérique cible est toujours utilisable. Vous pouvez réessayer le basculement sur le même périphérique cible.

    -   Une erreur s’est produite au cours du processus de basculement sur incident réel. Dans ce cas, le périphérique cible est marqué inutilisable. Vous devez fournir et configurer un autre tableau virtuel cible et l’utiliser pour le basculement.

    -   Le basculement était complet suivant lequel le périphérique source a été supprimé mais présente des problèmes de l’équipement cible et vous ne pouvez pas accéder aux données. Les données sont toujours sûrs dans le nuage et peuvent être facilement extrait par la création d’un autre tableau virtuel et ensuite l’utiliser comme un périphérique cible pour la reprise après sinistre.

### <a name="deactivate"></a>Désactiver

Lorsque vous désactivez un tableau virtuel de StorSimple, vous rompt la connexion entre le périphérique et le service de gestionnaire de StorSimple correspondant. La désactivation est **définitive** et ne peut pas être annulée. Un périphérique désactivé ne peut pas être enregistré avec le service Gestionnaire de StorSimple de nouveau. Pour plus d’informations, accédez à [désactiver et supprimer votre tableau virtuel StorSimple](storsimple-deactivate-and-delete-device.md).

Tenez compte des recommandations suivantes lors de la désactivation de votre tableau virtuel :

-   Prendre un instantané de nuage de toutes les données avant la désactivation d’un périphérique virtuel. Lorsque vous désactivez un tableau virtuel, toutes les données de périphérique local est perdue. Prendre un instantané de cloud vous permettra de récupérer des données à un stade ultérieur.

-   Avant de désactiver un tableau virtuel StorSimple, assurez-vous d’arrêter ou de supprimer des clients et des hôtes qui dépendent de ce périphérique.

-   Supprimer un périphérique désactivé si vous n’utilisez plus afin qu’il ne cumule pas de frais.

### <a name="monitoring"></a>Surveillance

Pour vous assurer que votre tableau virtuel StorSimple est dans un état sain en continu, vous devez surveiller le groupe et vous assurer de recevoir les informations du système telles que les alertes. Pour contrôler la santé générale du tableau virtuel, mettre en œuvre les meilleures pratiques suivantes :

- Configurer l’analyse pour effectuer le suivi de l’utilisation du disque de votre disque de données tableau virtuel ainsi que le disque du système d’exploitation. Si Hyper-V en cours d’exécution, vous pouvez utiliser une combinaison de System Center Virtual Machine Manager (SCVMM) et System Center Operations Manager (SCOM) pour surveiller vos hôtes de virtualisation.   

- Configurer les notifications par courrier électronique sur votre tableau virtuel pour envoyer des alertes à certains niveaux d’utilisation.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Applications d’analyse antivirus et recherche de l’Index

Un tableau virtuel de StorSimple de couche peut automatiquement les données de la couche de locale vers le nuage Microsoft Azure. Lorsqu’une application comme un index de recherche ou une analyse de virus est utilisée pour analyser les données stockées sur StorSimple, vous avez besoin prendre en charge que les données de nuage ne pas obtenir accessible et tirées en arrière au niveau local.

Nous vous recommandons de mettre en œuvre les méthodes conseillées suivantes lors de la configuration de l’analyse de virus ou de recherche d’index sur votre tableau virtuel :

-   Désactiver toutes les opérations configuré automatiquement l’analyse complète.

-   Pour les volumes hiérarchisés, configurer l’application index d’analyse antivirus ou de recherche pour effectuer une analyse incrémentielle. Il recherche uniquement les nouvelles données susceptibles de résidant au niveau local. Les données qui sont monté en cascade vers le nuage ne sont pas accessible lors d’une opération incrémentielle.

-   Vérifiez les filtres de recherche correcte et les paramètres sont configurés pour que seuls les types prévus de fichiers sont analysés. Par exemple, images (JPEG, GIF ou TIFF) et les dessins techniques ne doivent pas être analysés au cours de la reconstruction d’index intégrales ou incrémentielles.

Si vous utilisez le processus d’indexation de Windows, suivez ces instructions :

-   N’utilisez pas l’indexeur de Windows pour les volumes hiérarchisés comme il rappelle les grandes quantités de données (TBs) à partir du cloud, si l’index doit être reconstruit fréquemment. Reconstruction de l’index permet de récupérer tous les types de fichiers pour l’indexation de son contenu.

-   Utilisez les fenêtres d’indexation des processus pour les volumes ajoutés localement comme cela peut accéder aux données uniquement sur les couches locales pour générer l’index (il est possible que les données de nuage ne seront pas accessible).

### <a name="byte-range-locking"></a>Verrouillage de plage d’octets

Les applications peuvent verrouiller une plage d’octets spécifiée dans les fichiers. Si l’octet le verrouillage est activé sur les applications qui écrivent dans votre StorSimple, puis la hiérarchisation ne fonctionne pas sur votre tableau virtuel. Pour le montage en cascade de travail, toutes les zones des fichiers accédés doivent être déverrouillés. Verrouillage de plage d’octets n’est pas pris en charge avec les volumes hiérarchisés sur votre tableau virtuel.

Les mesures recommandées pour atténuer le verrouillage d’étendues de byte sont les suivantes :

-   Désactiver la plage d’octets de verrouillage dans votre logique d’application.

-   Utilisez localement épinglé volumes (au lieu de monté en cascade) pour les données associées à cette application. Volumes ajoutés localement pas monter en cascade dans le nuage.

-   Lorsqu’à l’aide d’épinglé localement les volumes avec l’octet le verrouillage activé, le volume peut en ligne avant que la restauration est terminée. Dans ces cas, vous devez attendre la fin de la restauration.

## <a name="multiple-arrays"></a>Plusieurs baies

Plusieurs réseaux virtuels peut-être doivent être déployés au compte pour un ensemble croissant de données qui pourraient renversez sur le nuage, affectant ainsi les performances du périphérique. Dans ces cas, il est préférable de dispositifs à l’échelle à mesure que le jeu de travail augmente. Cette opération nécessite un ou plusieurs périphériques à ajouter dans le centre de données sur site. Lors de l’ajout de périphériques, vous pouvez :

-   Fractionner l’ensemble actuel des données.
-   Déployer les nouvelles charges de travail sur les nouveaux périphériques.
-   Si vous déployez plusieurs réseaux virtuels, nous vous recommandons de l’équilibrage de charge point de vue, distribuer le tableau sur hyperviseur différents hôtes.

-  Plusieurs réseaux virtuels (lorsqu’il est configuré comme un serveur de fichiers ou un serveur iSCSI) peut être déployé dans un Namespace de système de fichiers distribués. Pour obtenir la procédure détaillée, accédez au [Fichier système Namespace Solution Distributed avec Guide de déploiement de stockage Cloud hybride](https://www.microsoft.com/download/details.aspx?id=45507). Réplication de système de fichiers distribués n’est actuellement pas recommandée pour une utilisation avec le tableau virtuel. 


## <a name="see-also"></a>Voir aussi
Apprenez comment [administrer votre tableau virtuel StorSimple](storsimple-ova-manager-service-administration.md) via le service Gestionnaire de StorSimple.
