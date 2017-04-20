<properties 
   pageTitle="Interfaces du matériel pour StorSimple 10 GbE | Microsoft Azure"
   description="Décrit les émetteurs-récepteurs pris en charge faible encombrement enfichables (SFP), des câbles et des commutateurs pour les interfaces de réseau 10 Gigabit Ethernet sur le périphérique StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Matériel pris en charge pour les interfaces de réseau 10 Gigabit Ethernet de votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des informations sur le matériel complémentaire qui fonctionne avec votre périphérique Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Liste des périphériques testés par Microsoft

Microsoft a testé suivants small form factor pluggable () émetteurs/récepteurs SFP, des câbles et des commutateurs pour vérifier qu’ils fonctionnent de manière optimale avec les périphériques. (Les tableaux suivants se verra que le nouveau matériel est testée.)

### <a name="sfp-transceivers"></a>Émetteurs-récepteurs SFP +

|Rendre|Modèle|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>Câbles

|S. N° |Rendre|Modèle|
|---|---|---|
| 1.|Cisco|SFP-H10GB-CU1M|
| 2.|Cisco|SFP-H10GB-CU2M|
| 3.|Cisco|SFP-H10GB-CU3M|
| 4.|Tripp-Lite|N820 - 05M (OM3)|

### <a name="switches"></a>Commutateurs

|S. N°|Rendre|Modèle|
|---|---|---|
| 1. |Cisco|N3K-C3172PQ-10 GE|
| 2. |Cisco|N3K-C3048-ZM-F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Liste des périphériques testés dans le champ

Cette section contient la liste des périphériques qui ont été déployées dans le champ par les clients de StorSimple. Ceux-ci n’ont pas été testés par Microsoft, mais sont susceptibles de travailler avec votre périphérique StorSimple.
 
| Paramètre                         | Valeur                                    |
|-----------------------------------|------------------------------------------|
| Assurez-vous de commutateur                     | Juniper                                  |
| Modèle de commutateur                    | ex4550-32F                               |
| Version de système d’exploitation de commutateur | JunOS 12.3R9.4                           |
| Modèle de la lame                     | Ports intégrés (PIC 0)                    |
| Assurez-vous de transmetteur                  | Juniper                                  |
| Modèle de transmetteur               | Numéro de référence 021308-740 <br></br> Numéro de référence 030658-740                   |
| Version du firmware de transmetteur    | 01 Version 0.0 (déclarée), Rév.            |
| Modèle de câble                     | Cavalier duplex LC/LC 50/125µ, OM3, LSZH |
| Modèle de StorSimple                | 8600                                     |
| Version du logiciel StorSimple     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Liste des périphériques testés par le fournisseur d’OEM (Mellanox)  

Mellanox a testé suivants small form factor pluggable () émetteurs/récepteurs SFP, des câbles et des commutateurs pour vérifier qu’ils fonctionnent de manière optimale avec les interfaces de réseau Mellanox, tels que les interfaces de réseau 10 Gigabit Ethernet sur le périphérique StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Câbles et modules de prise en charge par Mellanox 

Le tableau suivant répertorie les câbles et les modules de prise en charge par Mellanox. Ceux-ci n’ont pas été testés par Microsoft, mais sont susceptibles de travailler avec votre périphérique StorSimple.

| S. N° | Vitesse | Modèle                 | Description                                            | Rendre                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | 10 GbE| CAB-SFP-SFP - 1M        | câble en cuivre passif SFP + 10 Gbit/s, 1m                   | Arista                |
| 2.     | 10 GbE| CAB-SFP-SFP - 2M        | câble en cuivre passif SFP + 10 Gbit/s 2m                   | Arista                |
| 3.     | 10 GbE| CAB-SFP-SFP - 3M        | câble en cuivre passif SFP + 10 Gbit/s 3m                   | Arista                |
| 4.     | 10 GbE| CAB-SFP-SFP - 5M        | câble en cuivre passif SFP + 10 Gbit/s 5m                   | Arista                |
| 5.     | 10 GbE| Cisco SFP-H10GBCU1M   | Câble de Cisco SFP +                                       | Cisco                 |
| 6.     | 10 GbE| Cisco SFP-H10GBCU3M   | Câble de Cisco SFP +                                       | Cisco                 |
| 7.     |10 GbE | Cisco SFP-H10GBCU5M   | Câble de Cisco SFP +                                       | Cisco                 |
| 8.     | 10 GbE| J9281B HP X242 10 G    | SFP + SFP + 1m connexion directe de câble en cuivre             | HP                    |
| 9.     | 10 GbE| 455883-B21 HP BLc     | 10 Go SR SFP + Opt                                       | HP                    |
| 10.    | 10 GbE| 455886-B21 HP BLc     | 10 Go LR SFP + Opt                                       | HP                    |
| 11.    |10 GbE | 487649-B21 HP BLc     | Câble en cuivre à 0,5 m 10GbE SFP +                           | HP                    |
| 12.    |10 GbE | 487652-B21 HP BLc     | Câble en cuivre à 1m 10GbE SFP +                             | HP                    |
| 13.    |10 GbE | 487655-B21 HP BLc     | Câble en cuivre à SFP + 10GbE de 3m                             | HP                    |
| 14.    |10 GbE | 487658-B21 HP BLc     | Câble en cuivre à 7m 10GbE SFP +                             | HP                    |
| 15.    |10 GbE | 537963-B21 HP BLc     | Câble de cuivre 5m 10GbE SFP +                             | HP                    |
| 16.    |10 GbE | AP784A HP             | C-series passif cuivre SFP + câble de 3m                  | HP                    |
| 17.    |10 GbE | AP785A HP             | C-series passif cuivre SFP + câble de 5m                  | HP                    |
| 18.    |10 GbE | AP818A HP             | série B Active SFP cuivre + câble de 1m                   | HP                    |
| 19.    |10 GbE | AP819A HP             | série B Active SFP cuivre + câble de 3m                   | HP                    |
| 20.    |10 GbE | J9150A HP             | X132 10 G SFP + LC SR EMETTEUR-RECEPTEUR                        | HP                    |
| 21.    |10 GbE | J9151A HP             | X132 10 G SFP + LC LR EMETTEUR-RECEPTEUR                        | HP                    |
| 22.    |10 GbE | J9283B HP             | X242 10 G SFP + SFP + DAC câble de 3 m                        | HP                    |
| 23.    |10 GbE | J9285B HP             | X242 10 G SFP + SFP + 7 m DAC câble                        | HP                    |
| 24.    | 10 GbE| JD095B HP             | X240 10 G SFP + SFP + 0,65 m DAC câble                     | HP                    |
| 25.    | 10 GbE| JD096B HP             | X240 10 G SFP + SFP + 1,2 m DAC câble                      | HP                    |
| 26.    | 10 GbE| JD097B HP             | X240 10 G SFP + SFP + Papa câble de 3 m                        | HP                    |
| 27.    | 10 GbE| MAM1Q00A-QSA Mellanox | QSFP à adaptateur SFP +                                   | Mellanox Technologies |
| 28.    | 10 GbE| MC2309124-006 Mt      | X SFP+ câble en cuivre passif 1 à QSFP 10 Gbit/s 24awg 7 m   | Mellanox Technologies |
| 29.    | 10 GbE| MC2309124-007 Mt      | X SFP+ câble en cuivre passif 1 à QSFP 10 Gbit/s 24awg 7 m   | Mellanox Technologies |
| 30.    | 10 GbE| MC2309130-003 Mt      | X SFP+ câble en cuivre passif 1 à QSFP 10 Gbit/s 30awg 3 m   | Mellanox Technologies |
| 31.    | 10 GbE| MC2309130-00A Mt      | X SFP+ câble en cuivre passif 1 à QSFP 10 Gbit/s 30awg 0,5 m | Mellanox Technologies |
| 32.    | 10 GbE| MC3309124-005 Mt      | 1 24awg de 10 Gbit/s de x SFP+ du câble passif cuivre 5 m           | Mellanox Technologies |
| 33.    | 10 GbE| MC3309124-007 Mt      | 1 24awg de 10 Gbit/s de x SFP+ de câble de cuivre passif 7 m           | Mellanox Technologies |
| 34.    | 10 GbE| MC3309130-003 Mt      | Passif cuivre 1 30awg de 10 Gbit/s de x SFP+ de câble 3 m           | Mellanox Technologies |
| 35.    | 10 GbE| MC3309130-00A Mt      | 1 30awg de 10 Gbit/s de x SFP+ de câble de cuivre passif 0,5 m         | Mellanox Technologies |

### <a name="switches-supported-by-mellanox"></a>Commutateurs pris en charge par Mellanox 

Le tableau suivant répertorie les commutateurs pris en charge par Mellanox. Ceux-ci n’ont pas été testés par Microsoft, mais sont susceptibles de travailler avec votre périphérique StorSimple.

| S. N° | Vitesse | Modèle | Description                                                         | Rendre              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | 10GbE | 516733-B21  | Commutateur lame Ethernet HP ProCurve 6120XG 10GbE                      | HP    |
| 2.     | 10GbE | 538113-B21  | Module de passerelle 10GbE HP (PTM)                                  | HP    |
| 3.     | 10GbE | EN4093      | Module de commutateur évolutif IBM PureFlex système Fabric EN4093 10 Gigabit | IBM   |
| 4.     | 1GbE  | 3020        | Lame de commutateur Cisco Catalyst 3020 1GbE                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Lame de commutateur Cisco Catalyst 3020 X 1GbE                              | Cisco |
| 6.     | 1GbE  | 438030-B21  | Module de commutation 1GbE HP - commutateur de lame de Ethernet couche 2/3 GbE2c       | HP    |
| 7.     | 1GbE  | 6120G       | Lame de commutateur HP ProCurve 6120G/XG 1GbE                              | HP    |

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les composants matériels de StorSimple et de statut](storsimple-monitor-hardware-status.md).
