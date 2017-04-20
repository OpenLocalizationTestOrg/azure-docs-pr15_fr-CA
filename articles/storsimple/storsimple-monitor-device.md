<properties 
   pageTitle="Contrôler votre appareil StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le service Gestionnaire de StorSimple pour surveiller les performances, l’utilisation des capacités, le débit réseau et performances des périphériques d’e/s."
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
   ms.workload="TBD"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Le service Gestionnaire de StorSimple permet de contrôler votre périphérique StorSimple 

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser le service Gestionnaire de StorSimple pour surveiller des périphériques spécifiques au sein de votre solution de StorSimple. Vous pouvez créer des graphiques personnalisés en fonction des performances, l’utilisation des capacités, débit réseau et les métriques de performances de périphérique d’e/s. 

Pour afficher les informations d’analyse pour un périphérique spécifique, dans Azure portal classique, sélectionnez le service Gestionnaire de StorSimple. Cliquez sur l’onglet **Moniteur** , puis sélectionnez dans la liste des périphériques. La page de **l’écran** contient les informations suivantes.

## <a name="io-performance"></a>Performances d’e/s 

Performances de pistes **d’e/s** liées au nombre de lecture et les opérations d’écriture entre soit les interfaces d’initiateur iSCSI sur le serveur hôte et le périphérique ou le périphérique et le nuage. Cette performance peut être mesurée pour un volume spécifique, un conteneur de volume spécifique ou tous les conteneurs de volume.

Le graphique ci-dessous indique les e/s de votre périphérique pour tous les volumes pour un dispositif de production de l’initiateur. Les métriques tracées sont lire et écrivent des octets par seconde, lire et écrire des opérations d’e/s par seconde et lire et latences d’écriture.

![Performances d’e/s de l’initiateur au périphérique](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Pour le même périphérique, les opérations d’e/s sont tracées pour les données de l’appareil vers le nuage pour tous les conteneurs de volume. Sur ce périphérique, les données sont uniquement dans la couche linéaire et rien n’a fui vers le nuage. Il n’y a aucune opération en lecture-écriture en cours du périphérique vers le nuage. Par conséquent, les pics du graphique sont à un intervalle de 5 minutes qui correspond à la fréquence à laquelle la pulsation est activée entre le périphérique et le service. 

![Performances d’e/s de périphérique vers le cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Pour le même périphérique, un nuage instantané aux données de volume commençant à 2:00 pm. Cela a entraîné de données à partir du périphérique vers le nuage. Lecture-écriture ont été pris en charge vers le nuage dans cette durée. Le graphique d’e/s affiche un pic dans les diverses métriques correspondant au temps lorsque l’instantané a été pris. 

![Performances d’e/s de périphérique vers le cloud après la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Utilisation des capacités 

Les caractéristiques relatives à la quantité d’espace de stockage de données qui est utilisé par les volumes, les conteneurs de volume ou périphérique effectue le suivi de **l’utilisation des capacités** . Vous pouvez créer des rapports basés sur l’utilisation de la capacité de votre stockage principal, votre stockage en nuage ou de stockage de votre périphérique. Utilisation de la capacité peut être mesurée sur un volume spécifique, un conteneur de volume spécifique ou sur tous les conteneurs de volume.


Le principal, nuage, et de la capacité de stockage de périphérique peut être décrit comme suit :

###<a name="primary-storage-capacity-utilization"></a>Utilisation de capacité de stockage principal
 
Ces graphiques montrent la quantité de données écrites sur les volumes de StorSimple avant que les données sont dédupliquées et compressées. Vous pouvez afficher l’utilisation du stockage principal par tous les volumes ou d’un seul volume.

Lorsque vous permet d’afficher les graphiques de l’utilisation de capacité de volume stockage principal pour tous les volumes par rapport à chacun des volumes individuels et résumer les données principales dans ces deux cas, il existe peut-être une incompatibilité entre les deux nombres. Les données principales totale sur tous les volumes peuvent s’ajoutent pas à la somme totale des données principales de chacun des volumes. Cela peut être dû à l’une des opérations suivantes :

- **Inclus pour tous les volumes de données de capture instantanée**: ce comportement est visible uniquement si vous utilisez version antérieure à la mise à jour 3. Les données principales affichées pour tous les volumes sont la somme des données principales pour chaque volume et les données de capture instantanée. Les données principales affichées pour un volume donné correspondant à uniquement la quantité de données allouées sur le volume (et n’incluent pas les données de capture instantanée de volume correspondant).

    Il peut également s’expliquer par l’équation suivante :

    *Données principales (tous les volumes) = somme (données principales (volume i) + taille des données de capture instantanée (volume i))*
    
    *WHERE, données principales (volume i) = taille des données principales allouées au volume i*
 
    Si les captures instantanées sont supprimées par le biais du service, la suppression s’effectue de façon asynchrone en arrière-plan. Il peut prendre un certain temps pour la taille de données de volume être mis à jour à la suite de la suppression de l’instantané. 

    Si vous exécutez la mise à jour 3 ou version ultérieure, les données de capture instantanée ne sont pas incluses dans les données du volume. Et l’utilisation principale est calculée comme suit :

    * Les principales données (tous les volumes) = somme des (données principales (volume i)
    
    *WHERE, données principales (volume i) = taille des données principales allouées au volume i*
 
- **Volumes avec surveillance désactivé inclus dans tous les volumes**: Si vous avez des volumes sur votre périphérique pour lesquelles la surveillance est désactivée, les données d’analyse pour ces volumes individuels ne sera pas disponibles dans les graphiques. Toutefois, les données de tous les volumes dans le graphique comprennent les volumes pour lesquelles la surveillance est désactivée. 
 
- **Suppression de volumes avec les sauvegardes à la volée associés inclus pour tous les volumes**: si les volumes contenant des données de capture instantanée sont supprimés mais les snapshots associés existent toujours, puis vous pouvez voir une incompatibilité.

- **Les volumes supprimés inclus pour tous les volumes**: dans certains cas, les anciens volumes peuvent exister même si ceux-ci ont été supprimés. L’effet de suppression n’est pas visible et le périphérique peut afficher moins de capacité disponible. Vous devez contacter le Support Microsoft pour supprimer ces volumes.

Les graphiques suivants montrent l’utilisation de capacité de stockage principal d’un dispositif de StorSimple avant et après un nuage instantané. Comme il s’agit uniquement des données de volume, un instantané de nuage ne doit pas modifier le stockage principal. Comme vous pouvez le voir, le graphique n’affiche aucune différence entre la capacité principale en raison de la prise d’un instantané du nuage. Le snapshot de nuage démarré à environ 2 h 00 sur ce périphérique.

![Utilisation des capacités principales avant la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilisation des capacités principales après la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Si vous exécutez mise à jour 2 ou une version ultérieure, vous pouvez décomposer la capacité du stockage principal par un volume individuel, tous les volumes, tous les volumes hiérarchisés et tous les volumes ajoutés localement comme indiqué ci-dessous. Répartir par tous les volumes ajoutés localement vous permettra de déterminer rapidement combien de la couche locale est utilisé.

![Utilisation des capacités principales pour tous les volumes ajoutés localement](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Capacité de stockage cloud

Ces graphiques montrent l’importance du stockage en nuage utilisé. Ces données sont dédupliquées et compressées. Ce montant inclut des instantanés de nuage qui peuvent contenir des données qui n’est pas reflétées dans n’importe quel volume principal et sont conservées à des fins de conservation héritée ou requis. Vous pouvez comparer la principale et nuage des chiffres de la consommation de stockage pour avoir une idée du taux de réduction de données, bien que le numéro ne sera pas exact. Les graphiques suivants montrent l’utilisation de capacité de stockage cloud d’un dispositif de StorSimple avant et après un nuage instantané. Le snapshot de nuage démarré à environ 2:00 pm sur ce périphérique et que la capacité du nuage sont passées en même temps, augmentant de 5.73 Mo à Go 4.04.

![Utilisation des capacités nuage avant la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilisation des capacités nuage après la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Capacité de stockage de périphérique

Ces graphiques montrent l’utilisation totale du périphérique, qui sera l’utilisation du stockage principal plus parce qu’il inclut la couche linéaire SSD. Cette couche contient une quantité de données qui existe également sur le périphérique d’autres niveaux. La capacité de la couche linéaire SSD est revenue à la normale afin que lorsqu’il reçoit de nouvelles données, les anciennes données sont déplacées au niveau du disque dur (date à laquelle il est dédupliqué et compressé) et par la suite dans le nuage.

Au fil du temps, l’utilisation des capacités principales et utilisation des capacités plus augmentera probablement ensemble jusqu'à ce que les données commencent à être monté en cascade dans le nuage. À ce stade, l’utilisation des capacités du périphérique commencera probablement à plateau, mais l’utilisation de capacité principal augmenteront à mesure que des données sont écrites.

Les graphiques suivants montrent l’utilisation de capacité de stockage principal d’un dispositif de StorSimple avant et après un nuage instantané. Le snapshot de nuage démarré à 2 h 00, et l’utilisation des capacités du périphérique démarré réduisant à ce moment. L’utilisation de capacité de stockage de périphérique sont tombées à 7.48 Go de 11.58 go. Cela indique que probablement des données non compressées dans la couche SSD linéaire a été dédupliquées, compressées et déplacées dans la couche de disque dur. Notez que si le périphérique possède déjà une grande quantité de données dans les couches les SSD et le disque dur, vous pouvez voir pas cette baisse. Dans cet exemple, le périphérique a une petite quantité de données.

![Utilisation de capacité de périphériques avant la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilisation de capacité de périphériques après la capture instantanée du nuage](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Débit réseau

**Débit réseau** effectue le suivi des mesures associées à la quantité de données transférées à partir des interfaces de réseau d’initiateur iSCSI sur le serveur hôte et le périphérique et entre le périphérique et le nuage. Vous pouvez surveiller cette mesure pour chacune des interfaces réseau iSCSI sur votre périphérique.

Les graphiques suivants montrent le débit réseau des données 0 et des données 4, les deux interfaces de réseau 1 Gigabit Ethernet sur votre périphérique. Dans ce cas, données 0 a été activé pour le cloud considérant 4 de données a été activée d’iSCSI. Vous pouvez voir à la fois entrant et le trafic sortant de votre périphérique StorSimple. La courbe plate dans le graphique à partir de 3:24 pm est en raison du fait que nous rassembler des données de seulement 5 minutes et doit être ignorées. 

![Débit réseau pour données4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Débit réseau pour données4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Performances des périphériques 

Les caractéristiques relatives aux performances de votre périphérique effectue le suivi des **performances des périphériques** . Le graphique suivant affiche les statistiques d’utilisation du processeur pour un périphérique de production.

![Utilisation de l’UC pour le périphérique](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le tableau de bord périphérique de service Gestionnaire de StorSimple](storsimple-device-dashboard.md).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
