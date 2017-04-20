<properties 
    pageTitle="Indicateurs de suivi de StorSimple | Microsoft Azure" 
    description="Décrit les diodes électroluminescentes (DEL) et les alarmes sonores permet de surveiller l’état du périphérique StorSimple."
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
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>StorSimple indicateurs de suivi permet de gérer le périphérique   

## <a name="overview"></a>Vue d’ensemble

Votre périphérique StorSimple inclut les diodes électroluminescentes (DEL) et alarmes que vous pouvez utiliser pour contrôler les modules et l’état global de l’unité StorSimple. Les indicateurs de suivi, vous pouvez trouver sur les composants matériels du boîtier de l’unité principale et le boîtier EBOD. Les indicateurs de suivi peuvent être des alarmes sonores ou voyants.

Il existe trois états du voyant utilisés pour indiquer l’état d’un module : vert, vert, rouge-orange ou rouge-orange clignotant.  

- Voyants de vert représentent un état d’exploitation sain.  
- Vert pour représenter les voyants rouge-orange clignotant lors de la présence de conditions non critiques pouvant nécessiter une intervention de l’utilisateur.  
- Les voyants rouge-orange indiquent qu’il y a une erreur critique présente au sein du module.  

Le reste de cet article décrit les différents voyants surveillance, leurs emplacements sur le périphérique StorSimple, l’état des périphériques basé sur les États des voyants et des alarmes sonores associés.

## <a name="front-panel-indicator-leds"></a>Voyants du panneau avant

Le panneau avant, également connu sous le nom le *Panneau des opérations* ou le *Panneau d’ops*, affiche l’état global de tous les modules du système. Le panneau avant est identique sur le StorSimple principal et le boîtier de la EBOD et est illustré ci-dessous.  

   ![Panneau avant du périphérique][1]
 
Le panneau avant contient les indicateurs suivants :  

1. Bouton Muet
2. Voyant d’alimentation (vert/rouge-orange)
3. Indicateur d’erreur de module voyant (rouge-orange/OFF)
4. Indicateur d’erreur logique voyant (rouge-orange/OFF
5. Affichage de l’identifiant unitaire  

La principale différence entre la façade voyants pour le périphérique et ceux pour le boîtier EBOD est le **Numéro d’Identification de système unitaire** indiqué sur l’affichage SCD. L’ID d’unité par défaut affiché sur le périphérique est **00**, alors qu’il est affiché sur le boîtier de la EBOD l’ID d’unité par défaut est **01**. Cela vous permet de distinguer rapidement le périphérique et le boîtier EBOD lorsque le périphérique est sous tension. Si votre périphérique est désactivé, utilisez les informations fournies dans [un nouveau périphérique sous tension](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) afin de différencier le périphérique du boîtier EBOD.  

## <a name="front-panel-led-status"></a>État des voyants du panneau avant du  

Utilisez le tableau suivant pour identifier l’état indiqué par les voyants sur le panneau avant pour le périphérique ou le boîtier EBOD.  

|Puissance du système | Défaillance du module | Erreur de logique | Alarme | État|
|-------------|---------------|-----------------|-------|-------|
|Rouge-orange | HORS TENSION     | HORS TENSION | N/A | CA perdu, fonctionne sur l’alimentation de secours ou l’alimentation CA sur et le contrôleur de modules ont été supprimés.|
|Vert | ON | ON | N/A | État de test OPS panneau sous tension (5 s)|
|Vert | HORS TENSION | HORS TENSION | N/A | Mise sous tension, toutes les fonctions de bonne|
|Vert | ON |N/A | PCM voyants, voyants de défaillance du ventilateur | Toute panne PCM, panne du ventilateur, au-dessus ou au-dessous de température|
| Vert | ON | N/A | Module d’e/s voyants  | Faute de module de contrôleur|
| Vert | ON | N/A | N/A | Erreur de logique de boîtier|
| Vert | Flash | N/A | Module voyant d’état de module de contrôleur. PCM voyants, voyants de défaillance du ventilateur | Type de module inconnu contrôleur installé, défaillances du bus I2C, erreur de configuration de contrôleur module produit indispensables données VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Alimentation, refroidissement des voyants du module (PCM)   

Vous trouverez la puissance refroidissement module (PCM) voyants à l’arrière du boîtier principal ou du boîtier de la EBOD sur chaque module PCM. Cette rubrique explique comment utiliser les voyants suivants pour surveiller l’état de votre périphérique StorSimple.  

- Voyants de PCM pour le boîtier principal
- Voyants de PCM pour le boîtier EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>Voyants de PCM pour le boîtier principal  

Le dispositif de StorSimple a un module PCM 764W avec une batterie supplémentaire. L’illustration suivante montre le panneau des voyants pour le périphérique.  

   ![Voyants de PCM sur le boîtier principal][2]

Légende du voyant :

1. Panne d’alimentation CA
2. Panne du ventilateur
3. Panne de batterie
4. PCM OK
5. Défaillance du contrôleur de domaine
6. Batterie bon  

L’état de la PCM est indiqué sur le panneau des voyants. Le panneau des voyants de PCM périphérique a six voyants. Quatre de ces voyants d’afficher l’état de l’alimentation et le ventilateur. Les deux voyants restants indiquent l’état du module de batterie de secours dans le PCM. Vous pouvez utiliser les tables suivantes pour déterminer l’état de la PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM voyants d’alimentation et ventilateur
| État | PCM OK (vert) | Échec de CA (orange) | Échec du ventilateur (orange) | Échec du contrôleur de domaine (orange) |
|--------|----------------|-----------------------|------------------|----------------------|
| Aucune alimentation (boîtier) | HORS TENSION | HORS TENSION | HORS TENSION | HORS TENSION|
| Aucune alimentation CA (ce PCM uniquement) | HORS TENSION | ON | HORS TENSION | ON |
| CA présenter sur PCM - OK     | ON | HORS TENSION | HORS TENSION | HORS TENSION |
| Échec PCM (panne du ventilateur) | HORS TENSION | HORS TENSION | ON | N/A |
| Panne PCM (sur a, sur la tension, sur en cours) | HORS TENSION | ON | ON | ON |
| PCM (ventilateur hors tolérance) | ON | HORS TENSION | HORS TENSION | ON |
| Mode veille | Clignotant | HORS TENSION | HORS TENSION | HORS TENSION |
| Téléchargement du microprogramme PCM | HORS TENSION | Clignotant | Clignotant | Clignotant |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM voyants de la batterie de secours  

| État | Batterie correcte (vert) | Panne de batterie (orange) |
|--------|----------------------|-----------------------|
| Batterie n’est pas présent | HORS TENSION | HORS TENSION |
| Batterie présent et facturé | ON | HORS TENSION |
| Décharge en cours de chargement ou de gestion de batterie | Clignotant | HORS TENSION |
| Erreur « soft » de batterie (récupérable) | HORS TENSION | Clignotant |
| Erreur « dur » de batterie (non récupérable) | HORS TENSION | ON |
| Batterie disarmed | Clignotant | HORS TENSION |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Voyants de PCM pour le boîtier EBOD  

Le boîtier EBOD a un PCM 580 w et aucune batterie supplémentaire. Le panneau PCM pour le boîtier EBOD a des voyants uniquement pour les blocs d’alimentation et du ventilateur. L’illustration suivante montre ces voyants.

   ![Voyants de PCM sur le boîtier de la EBOD][3] 
 
Vous pouvez utiliser le tableau suivant pour déterminer l’état de la PCM.  

| État | PCM OK (vert) | Échec de CA (orange) | Échec du ventilateur (orange) | Échec du contrôleur de domaine (orange) |
|--------|---------------|------------------------|------------------|----------------------|
| Aucune alimentation (boîtier) | HORS TENSION | HORS TENSION | HORS TENSION | HORS TENSION |
| Aucune alimentation CA (ce PCM uniquement) | HORS TENSION | ON | HORS TENSION | ON |
| CA présenter sur PCM-OK | ON | HORS TENSION | HORS TENSION | HORS TENSION |
| Échec PCM (panne du ventilateur) | HORS TENSION | HORS TENSION | ON | X |
| Panne PCM (sur a, sur la tension, sur en cours | HORS TENSION | ON | ON | ON |
| PCM (ventilateur hors tolérance) | ON | HORS TENSION | HORS TENSION | ON |
| Modèle de mise en veille | Clignotant | HORS TENSION | HORS TENSION | HORS TENSION |
| Téléchargement du microprogramme PCM | HORS TENSION | Clignotant | Clignotant | Clignotant |

## <a name="controller-module-indicator-leds"></a>Contrôleur module voyants  

Le périphérique StorSimple, comporte des voyants pour le contrôleur principal et les modules de contrôleur EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Voyants de surveillance pour le contrôleur principal
L’illustration suivante vous aide à identifier les voyants sur le contrôleur principal. (Tous les composants sont répertoriés pour faciliter l’orientation).  

   ![Surveillance des voyants - contrôleur principal][4]
 
Utilisez le tableau suivant pour déterminer si le module de contrôleur fonctionne correctement.  

### <a name="controller-indicator-leds"></a>Voyants du contrôleur  

| VOYANT | Description                                                                            
|---- | ----------- |
| CODE de voyant (bleu) | Indique que le module est identifié. Si le voyant bleu clignote sur un contrôleur en cours d’exécution, puis le contrôleur est le contrôleur actif et l’autre est le contrôleur de secours. Pour plus d’informations, reportez-vous à la section [identifier le contrôleur actif sur le périphérique](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Erreur de voyant (orange) | Indique une erreur dans le contrôleur.        
| VOYANT OK (vert) | Vert fixe indique que le contrôleur est OK. Vert clignotant indique qu’une erreur de configuration de VPD du contrôleur. |
| Activité SAS voyants (vert) | Vert fixe indique que la connexion avec aucune activité en cours. Vert clignotant indique la connexion a l’activité en cours. |
| Voyants d’état Ethernet | Côté droit indique une activité réseau/lien : active, (vert clignotant) à (vert fixe) lien activité réseau. Côté gauche indique la vitesse du réseau : (jaune) 1000 Mo/s, 100 Mbits/s (vert) et (OFF), 10 Mo/s. Selon le modèle de composant, ce voyant peut clignoter même si l’interface réseau n’est pas activé. |
| Voyants POST | Indique la progression du démarrage lorsque le contrôleur est activé. Si le périphérique StorSimple ne démarre pas, ce voyant aide Support Microsoft à identifier le point dans le processus de démarrage à laquelle l’erreur s’est produite. |

>[AZURE.IMPORTANT] 
Si le voyant de défaillance s’allume, il existe un problème avec le module de contrôleur qui peut être résolu en redémarrant le contrôleur. Veuillez contacter le Support Microsoft si redémarrer le contrôleur ne résout pas ce problème.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Voyants de surveillance de la EBOD (boîtier EBOD)  

Chacun des contrôleurs Gbit/s SAS EBOD 6 est doté de voyants indiquant son état comme indiqué dans l’illustration suivante.  

  ![Surveillance des voyants - boîtier EBOD][5]

Utilisez le tableau suivant pour déterminer si le module de contrôleur EBOD fonctionne normalement.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD contrôleur module voyants  

|État | Module d’e/s OK (vert) | Défaillance du module d’e/s (orange) | Activité du port hôte (vert) |
|-------|----------------------|-------------------------------|----------------------------|
| Module de contrôleur OK | ON | HORS TENSION | - |
| Défaillance du module contrôleur | HORS TENSION | ON | - |
| Aucune connexion au port hôte externe | - | - | HORS TENSION |
| Connexion au port hôte externe – aucune activité | - | - | ON |
| Connexion au port hôte externe - activité | - | - | Clignotant |
| Erreur du contrôleur module métadonnées | Clignotant | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lecteur de disque voyants pour le boîtier principal et d’un boîtier EBOD

Le dispositif de StorSimple a des lecteurs de disque qui se trouve dans le boîtier principal et le boîtier EBOD. Chaque disque contient la surveillance des voyants, comme décrit dans cette section. 

Pour les lecteurs de disque, l’état du disque est indiquée par un vert voyant et un voyant rouge-orange monté à l’avant de chaque module de support du lecteur. L’illustration suivante montre ces voyants.

  ![Voyants de disque dur][6]
 
Le tableau suivant permet de déterminer l’état de chaque lecteur de disque, qui à son tour affecte le global du panneau avant du voyant.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Lecteur de disque voyants pour le boîtier EBOD  

| État | VOYANT d’activité OK (vert) | Erreur de voyant (rouge-orange) | Associé à panneau d’ops DEL |
|-------|--------------------------|----------------------|-------------------------|
| Aucun lecteur installé | HORS TENSION | HORS TENSION | Aucun |
| Lecteur installé et opérationnel | Clignotement/arrêt avec activité | X | Aucun |
| Ensemble de Services de boîtier SCSI (SES) périphérique identité | ON | Clignotement/1 deuxième hors de 1 seconde | Aucun |
| Jeu de bits de SES périphérique pannes | ON | ON | Erreur de logique (rouge) |
| Défaillance du circuit de contrôle d’alimentation | HORS TENSION | ON | Défaillance du module (rouge) |

## <a name="audible-alarms"></a>Alarmes sonores  

Un périphérique StorSimple contient des alarmes sonores associées avec le boîtier principal et le boîtier EBOD. Une alarme sonore se trouve sur le panneau avant (également connu sous le nom du panneau ops) les deux boîtiers. L’alarme sonore indique quand une condition d’erreur. Les conditions suivantes activera l’alarme :  

- Échec ou panne de ventilateur
- Tension hors limites
- Sur ou dans des conditions de température
- Dépassement de capacité thermique
- Défaillance système
- Erreur de logique
- Défaillance de l’alimentation
- Suppression d’une puissance de refroidissement module (PCM)  

Le tableau suivant décrit les différents États d’alarme.  

### <a name="alarm-states"></a>États d’alarme  

| État d’alarme | Action | Action avec bouton Muet |
|------------|---------|---------------------------------|
| S0 | En mode normal : en mode silencieux | Émettre un signal sonore deux fois |
| S1 | Mode de défaillance : 1 seconde/1 seconde désactivé | Transition vers S2 ou S3 (voir notes) |
| S2 | Rappeler le mode : signal sonore intermittent | Aucun |
| S3 | Mode muted : en mode silencieux | Aucun |
| S4 | Mode d’erreur critique : alarme continue | Non disponible : muet pas active |

> [AZURE.NOTE] 

>  - Dans l’état d’alarme S1, si vous n’appuyez pas sur Muet dans les 2 minutes, l’état passe automatiquement à S2 ou S3.  
>  - Alarme états S1 à S4 revenir à S0 après l’effacement de la condition d’erreur.  
>  - État d’erreur critique S4 peut être entré à partir de n’importe quel autre état.  

Vous pouvez désactiver l’alarme sonore en appuyant sur le bouton Muet sur le panneau des opérations. Désactivation automatique se produit après deux minutes si le commutateur de mode Muet n’est pas activé manuellement. Lorsque l’alarme est coupé, il continuera à son avec des bips intermittents courts pour indiquer qu’il existe toujours un problème. L’alerte sera silencieuse lorsque tous les problèmes sont désactivées.  

Le tableau suivant décrit les différentes conditions d’alarme.  

### <a name="alarm-conditions"></a>Conditions d’alarme  

| État | Gravité | Alarme | OPS panneau DEL |
|--------|---------|--------|----------------|
| Alerte PCM : perte d’alimentation du contrôleur de domaine à partir d’un seul PCM | Erreur : aucune perte de redondance | S1 | Défaillance du module|
|Alerte PCM : perte d’alimentation du contrôleur de domaine à partir d’un seul PCM | Erreur : perte de redondance | S1 | Défaillance du module |
| Échec du ventilateur PCM | Erreur : perte de redondance | S1 | Défaillance du module |
| Panne PCM SB module détecté | Pannes | S1 | Défaillance du module |
| PCM supprimé | Erreur de configuration | Aucun | Défaillance du module |
| Erreur de configuration du boîtier | Erreur – critique | S1 | Défaillance du module |
| Alerte d’avertissement de faible température | Avertissement | S1 | Défaillance du module |
| Alerte d’avertissement de haute température | Avertissement | S1 | Défaillance du module |
| Sur les alarmes de température | Erreur – critique | S1 | Défaillance du module |
| Échec de bus I2C | Erreur : perte de redondance | S1 | Défaillance du module |
| OPS du panneau erreur de communication (I2C) | Erreur – critique     | S1 | Défaillance du module |
| Erreur du contrôleur | Erreur – critique | S1 | Défaillance du module |
| Défaillance du module SB interface | Erreur – critique | S1 | Défaillance du module |
| Défaillance du module SB interface – Aucun fonctionnement modules restant | Erreur – critique | S4 | Défaillance du module |
| Module d’interface de SB supprimé | Avertissement | Aucun | Défaillance du module |
| Erreur de contrôle d’alimentation du lecteur | Avertissement : aucune perte d’alimentation du lecteur | S1 | Défaillance du module |
| Erreur de contrôle d’alimentation du lecteur | Erreur – critique ; perte d’alimentation du lecteur | S1 | Défaillance du module |
| Lecteur retiré | Avertissement | Aucun | Défaillance du module |
| Puissance insuffisante disponible | Avertissement | Aucun | Défaillance du module |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’état et les composants matériels de StorSimple](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
