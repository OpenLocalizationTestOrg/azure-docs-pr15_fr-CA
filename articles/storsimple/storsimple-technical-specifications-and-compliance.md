<properties 
   pageTitle="Spécifications techniques de StorSimple | Microsoft Azure"
   description="Décrit les spécifications techniques et les informations de conformité des réglementations pour les composants matériels de StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Spécifications techniques et la conformité pour le périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Les composants matériels de votre périphérique Microsoft Azure StorSimple respectent les spécifications techniques et les normes décrites dans cet article. Les spécifications techniques décrivent l’alimentation et Modules de ventilation (PCM pour) les lecteurs de disque, capacité de stockage et boîtiers. Les informations de conformité couvrent notamment les normes internationales, sécurité et émissions et le câblage.


## <a name="power-and-cooling-module-specifications"></a>Spécifications d’alimentation et Module de refroidissement  

Le dispositif de StorSimple a deux 100-240 v double ventilateur, SB compatible avec Modules d’alimentation de refroidissement (PCM pour). Cela permet une configuration d’alimentation redondante. Si un PCM échoue, le périphérique continue à fonctionner normalement sur l’autre PCM jusqu'à ce que le module défaillant est remplacé.  

Le boîtier EBOD utilise un PCM W 580 et boîtier principal utilise un PCM W 764. Les tableaux suivants répertorient les spécifications techniques liées aux PCMs.

| Spécification           | 580 W PCM (EBOD)                                    | 764 W PCM (principal)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Puissance de sortie maximale    | 580 W                                               | 764                                                |
| Fréquence               | 50/60 Hz                                            | 50/60 Hz                                           |
| Sélection de plage de tension | Auto compris : V CA, 47/63 Hz de 90-264               | Auto compris : 90-264 V CA, 47/63 Hz               |
| Courant maximal  | 20 A                                                | 20 A                                               |
| Correction du facteur de puissance | > 95 % de tension d’entrée nominale                          | > 95 % de tension d’entrée nominale                         |
| Harmoniques               | Répond aux EN61000-3-2                                   | Répond aux EN61000-3-2                                  |
| Sortie                  | Tension de mode veille 5V @ 2.0 A                          | Tension de mode veille 5V @ 2.7 A                         |
|                         | + 5 v @ 42 A                                          | + 5 v @ 40 A                                         |
|                         | + 12 v @ 38 A                                         | + 12 v @ 38 A                                        |
| Connectable à chaud           |  Oui                                                | Oui                                                |
| Commutateurs et voyants       | Interrupteur marche/arrêt de CA et les voyants d’état de quatre     | Interrupteur marche/arrêt de CA et les voyants d’état six     |
| Enceinte de refroidissement       | Axial ventilateurs avec contrôle de la vitesse variable de ventilateur de refroidissement  | Axial ventilateurs avec contrôle de la vitesse variable de ventilateur de refroidissement |

 
## <a name="power-consumption-statistics"></a>Statistiques de la consommation d’énergie  

Le tableau suivant répertorie les données de consommation de courant normale (les valeurs réelles peuvent varier à partir de la publication) pour les divers modèles de dispositif de StorSimple. 
 
 Conditions | 240 V C.A. | 240 V C.A. | 240 V C.A. | 110 V C.A. | 110 V C.A. | 110 V C.A. 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Disques lents, de ventilateurs inactifs | 1,45 A  |0,31 kW | 1057.76 BTU/h | 3.19 A | 0.34 kW | 1160.13 BTU/h 
 Ralentir les ventilateurs, l’accès à des lecteurs | 1,54 A | 0,33 kW | 1126.01 BTU/h | 3.27 A | 0,36 kW | 1228.37 BTU/h 
 Ventilateurs rapides, disques inactifs, deux panneau sous tension | 2.14 A | 0,49 kW  | 1671.95 BTU/h | 4,99 A | 0.54 kW | 1842.56 BTU/h 
 Ventilateurs rapides, disques inactifs, un bloc d’alimentation sous tension une inactivité | 2.05 A | 0.48 kW | 1637.83 BTU/h | 4.58 A | 0,50 kW | 1706.07 BTU/h 
 Lecteurs de ventilateurs rapides, accès, deux panneau sous tension | 2.26 A | 0.51 kW | 1740.19 BTU/h | 4,95 A | 0.54 kW | 1842.56 BTU/h 
 Ventilateurs rapides, lecteurs, accéder à, un bloc d’alimentation sous tension une inactivité | 2.14 A |0,49 kW | 1671.95 BTU/h | 4.81 A  | 0,53 kW | 1808.44 BTU/h 

## <a name="disk-drive-specifications"></a>Spécifications de lecteur de disque  

Votre périphérique StorSimple prend en charge jusqu'à 12 disques Serial Attached SCSI (SAS) facteur format 3,5 pouces. Les disques en question peuvent être une combinaison de disques SSD (SSD) ou des disques durs (HDD), en fonction de la configuration du produit. Les 12 slots de disque se trouvent dans une configuration de 3 par 4 en face de l’enceinte. Le boîtier EBOD permet de stockage supplémentaire pour un autre 12 lecteurs de disques. Il s’agit toujours de disques durs.  

## <a name="storage-specifications"></a>Spécifications du stockage
Les périphériques StorSimple ont une combinaison de disques durs et des lecteurs à l’état solide pour le 8100 et 8600. La capacité totale utilisable pour le 8100 et 8600 sont à peu près 15 et 38 To respectivement. Le tableau suivant répertorie les détails de SSD, disque dur et la capacité du cloud dans le contexte de la capacité de la solution StorSimple.

| Modèle de périphérique / capacité                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Nombre de lecteurs de disque dur (HDD)              |   8                                                     |  19                                                     |
| Nombre de disques SSD (SSD)            |   4                                                     |  5                                                      |
| Capacité de disque dur unique                            |   4 TO                                                  |  4 TO                                                   |
| Capacité SSD unique                            |   400 GO                                                |  800 GO                                                 |
| Disque de secours                                 |   4 TO                                                  | 4 TO                                                    |
| Capacité du disque dur                            | 14 TO                                                   | 36 TO                                                   |
| Capacité utile de SSD                            | 800 GO                                                  | 2 TO                                                    |
| Total capacité utilisable *                         | ~ 15 TO                                                 | ~ 38 TO                                                 |
| Capacité maximale de solution (y compris le nuage)    | 200 TO                                                  | 500 TO                                                  |


<sup> * </sup> -  *La capacité totale utilisable inclut la capacité disponible pour les données, les métadonnées et buffers.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Les spécifications de poids et de dimensions du boîtier  

Les tableaux suivants répertorient les différentes spécifications de boîtier pour les dimensions et le poids.  

### <a name="enclosure-dimensions"></a>Dimensions du boîtier
Le tableau suivant répertorie les dimensions du boîtier en millimètres et en pouces.

|Boîtier |Millimètres |Pouces |
|----------|------------|-------| 
| Hauteur |87,9 | 3.46 |
| Largeur entre la bride de montage | 483 | 19.02 |
| Largeur entre les corps de boîtier | 443 | 17.44 |
| Profondeur de bride de montage à l’extrémité supérieure du corps de boîtier | 577 | 22.72 |
| Profondeur du panneau des opérations à l’extrémité la plus éloignée de boîtier | 630.5 | 24.82 |
| Profondeur de bride de montage à l’extrémité la plus éloignée de boîtier |   603 | 23.74 |

### <a name="enclosure-weight"></a>Poids du boîtier  

Selon la configuration, un boîtier principal entièrement chargé peut peser de 21 à 33 kg et nécessite deux personnes à la gérer. 
 
| Boîtier | Poids |
|-----------|--------| 
| Poids maximal (dépend de la configuration) |30 kg – 33 kg |
| Vide (pas de lecteurs montés) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Caractéristiques de l’environnement boîtier  

Cette section répertorie les spécifications relatives à l’environnement du boîtier. Température, humidité, altitude, aux chocs, aux vibrations, orientation, sécurité et compatibilité électromagnétique (CEM) sont inclus dans cette catégorie.  

### <a name="temperature-and-humidity"></a>Température et humidité

| Boîtier        | Gamme de températures ambiantes  | Taux d’humidité ambiante | Température humide maximale de   |
|------------------|----------------------------|---------------------------|--------------------|
| Opérationnelle      | 5° C - 35° C (41° F - 95° F)    | 20-80 % non-condensation- | 28° C (82° F)        |
| Non opérationnels  | -40° C - 70° C (40° F - 158° F) | 5 % à 100 % sans condensation  | 29° C (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Circulation de l’air, l’altitude, chocs, vibrations, orientation, sécurité et EMC
 
| Boîtier          | Spécifications opérationnelles                                                |
|--------------------|---------------------------------------------------------------------------| 
| Système de ventilation            | Aération est avant vers l’arrière. Système doit être actionné par une installation de basse pression, d’échappement de l’arrière. Contre-pression créée par les portes des racks et des obstacles ne doit pas dépasser 5 pascals (0,5 mm de colonne d’eau). |
| Altitude, opérationnelle  | compteurs de-30 mètres 3045 (-100 pieds à 10 000 pieds) à la température de fonctionnement maximale nominale de 5 ° C au-dessus de 7000 pieds. |
| Altitude, non opérationnel  | compteurs de-305 à 12 192 m (-1,000 pieds à 40 000 pieds) |
| En réponse au choc, opérationnelle  | sinus de 10 ms ½ 5g |
| En réponse au choc, non opérationnel  | sinus de 10 ms ½ 30g |
| Vibrations, opérationnelle  | 0.21g RMS aléatoire à 5 à 500 Hz |
| Vibrations, non opérationnel  | 1,04 g RMS aléatoire à 2-200 Hz |
| Vibrations, déplacement  | sinus 3g 2 et 200 Hz |
| Orientation et montage  | montage en rack 19"(2 unités EIA) |
| Rails de rack  | Pour ajuster la profondeur de minimum 700 mm (31.50 pouces) racks conforme CEI 297 |
| Sécurité et approbations  |   CE et UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC  | EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Conformité aux normes internationales
Votre périphérique Microsoft Azure StorSimple est conforme aux normes internationales suivantes :  

- CE - LA NORME EN 60950-1  
- Rapport CB pour IEC 60950-1  
- UL et cUL à UL 60950-1  

## <a name="safety-compliance"></a>CONFORMITE et SECURITE  

Votre périphérique Microsoft Azure StorSimple respecte les niveaux de sécurité suivants :  

- Approbation de type système produit : UL, cUL, CE  
- Sécurité : UL 60950, IEC 60950, la norme EN 60950  

## <a name="emc-compliance"></a>Conformité EMC 

Votre périphérique Microsoft Azure StorSimple répond aux évaluations EMC suivantes.  

### <a name="emissions"></a>Émissions

Le périphérique est compatible avec EMC pour les niveaux d’émissions effectuées et radiée.  

- Émissions effectués sur le nombre de niveaux : CFR 47 partie 15 classe A EN55022 Classe A CISPR classe A  
- Le nombre de niveaux des émissions radiées : CFR 47 partie 15 classe A EN55022 Classe A CISPR classe A   

### <a name="harmonics-and-flicker"></a>Harmoniques et scintillement  

Le dispositif est conforme EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Niveaux d’immunité limite  
Le dispositif est conforme aux EN55024.  

## <a name="ac-power-cord-compliance"></a>Conformité de cordon d’alimentation CA
  
La prise et l’assembly du cordon d’alimentation complète doivent respecter les normes appropriées pour le pays dans lequel le périphérique est utilisé, et ils doivent disposer d’autorisations de sécurité qui sont acceptables dans ce pays. Les tableaux suivants répertorient les normes pour l’Europe et aux États-Unis.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cordons d’alimentation CA - USA (doit être le NRTL répertorié)

| Composant       | Spécification                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Type de cordon       | VP ou SVT, minimum AWG 18, 3 conducteurs, la longueur maximale de 2.0 mètres |
| Plug            | Plug de pièce jointe de type mise à la terre NEMA 5-15P nominale 120 V, 10 A ; ou IEC 320 C14, 250 V, 10 A |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

### <a name="ac-power-cords---europe"></a>Cordons d’alimentation - Europe

| Composant       | Spécification                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Type de cordon       | Harmonisées, H05-VVF-3G1.0                                         |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

## <a name="supported-network-cables"></a>Câbles réseau pris en charge  

Pour les interfaces de réseau 10 Gigabit Ethernet, données 2 et données 3, reportez-vous à la [liste des modules et des câbles de réseau prises en charge](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à déployer un dispositif de StorSimple dans votre centre de données. Pour plus d’informations, consultez [déploiement de votre périphérique local](storsimple-deployment-walkthrough-u2.md).  
