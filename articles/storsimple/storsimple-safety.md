<properties 
   pageTitle="La sécurité de votre périphérique StorSimple | Microsoft Azure"
   description="Décrit les considérations, instructions et conventions de sécurité et explique comment installer et utiliser votre équipement StorSimple en toute sécurité."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="safely-install-and-operate-your-storsimple-device"></a>Installer et utiliser votre équipement StorSimple en toute sécurité

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)
![icône de notification de sécurité de lecture](./media/storsimple-safety/IC740885.png) **lecture des informations de santé et de sécurité**

Lire toutes les informations de sécurité et de santé dans cet article s’applique à votre périphérique Microsoft Azure StorSimple. Conserver tous les guides imprimés fournis avec le périphérique StorSimple pour référence ultérieure. Échec, suivez les instructions et configurer, utiliser, et pour ce produit peut augmenter le risque de dommage grave ou de décès, ou endommager l’ou les périphériques en charge. Une [version téléchargeable de ce guide](http://www.microsoft.com/download/details.aspx?id=44233) est également disponible.

## <a name="safety-icon-conventions"></a>Conventions des icônes de sécurité

Voici les icônes que vous trouverez lorsque vous passez en revue les mesures de sécurité doivent être respectées lors de la configuration et l’exécution de votre périphérique Microsoft Azure StorSimple.

| Icône  | Description  |
|:------|:-------------| 
|![Icône de danger](./media/storsimple-safety/IC740879.png) **DANGER !**|Indique une situation dangereuse qui, si ne pas évité, entraîne la mort ou des blessures graves. Ce mot de signal est limitée à des situations plus extrêmes.| 
|![Icône d’avertissement](./media/storsimple-safety/IC740879.png) **Avertissement !**|Indique une situation dangereuse qui, si ne pas évité, peut entraîner dans la mort ou des blessures graves.|
|![Icône d’avertissement](./media/storsimple-safety/IC740879.png) **Attention !**|Indique une situation dangereuse qui, si ne pas évité, peut entraîner des blessures mineure ou modérée.|
|![Notez l’icône](./media/storsimple-safety/IC740881.png) **avis :**|Indique des informations considérées comme importantes, mais non vitale.|
|![Icône de choc électrique](./media/storsimple-safety/IC740882.png) **risque de choc électrique** |Haute tension|
|![Icône de poids lourds](./media/storsimple-safety/IC740883.png) **poids lourd**| |
|![Aucun utilisateur maintenance ne pièces icône](./media/storsimple-safety/IC740879.png) **Aucun éléments dont la maintenance utilisateur**|N’accédez pas à moins que correctement formé.|
|![Lire la sécurité avis icône](./media/storsimple-safety/IC740885.png)**Lisez d’abord toutes les Instructions**| |
|![Icône de danger Conseil](./media/storsimple-safety/IC740886.png) **risque de conseil**| |


## <a name="handling-precautions"></a>Gestion des précautions

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) ![icône de poids lourds](./media/storsimple-safety/IC740883.png) **Avertissement !** 


Pour réduire le risque de blessure :

- Un boîtier entièrement configuré peut peser jusqu'à 32 kg (70 lb) ; n’essayez pas de soulever il par vous-même.
- Avant de déplacer le boîtier, assurez-vous toujours que deux personnes sont disponibles pour gérer le poids. Sachez qu’une personne tente de soulevez ce poids peut supporter des blessures.
- Ne soulevez pas le boîtier par les poignées de la puissance et le refroidissement des Modules (PCM pour) situé à l’arrière de l’unité. Ils ne sont pas conçus pour supporter le poids.

## <a name="connection-precautions"></a>Précautions de connexion

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) ![icône de choc électrique](./media/storsimple-safety/IC740882.png) **Avertissement !**

Pour réduire le risque de blessure, de choc électrique ou de décès :

- Avec une alimentation par plusieurs sources CA, déconnectez toute la puissance de fourniture pour une isolation complète.

- Définitivement débrancher l’unité avant de la déplacer, ou si vous pensez qu’il a été endommagé en aucune façon.

- Fournissent une connexion sûre terre électrique pour les cordons d’alimentation. Vérifiez que la mise à la terre du boîtier répond aux exigences nationales et locales avant l’application de l’alimentation.

- Assurez-vous que le câble d’alimentation est toujours déconnecté avant la suppression d’un PCM du châssis.

- Étant donné que la prise d’alimentation est le principal déconnecter le périphérique, assurez-vous que les prises de socket se trouvent près de l’équipement et sont facilement accessible.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) ![icône de choc électrique](./media/storsimple-safety/IC740882.png) **Avertissement !**

Pour réduire le risque de surchauffe ou se déclenchent à partir de connexions électriques :

- Fournir une source d’alimentation appropriée avec protection de surcharge électrique pour satisfaire aux exigences décrites dans la spécification technique.

- N’utilisez pas les cordons BIFURQUÉ (« Y » prospects).

- Pour respecter les exigences thermiques, d’émission et de sécurité applicables, aucune couverture ne doit être supprimé et toutes les baies doivent être remplis avec des modules plug-in ou lecteur vides.

- Assurez-vous que l’équipement est utilisé de la manière spécifiée par le fabricant. Si cet équipement est utilisé d’une manière non spécifiée par le fabricant, la protection assurée par l’équipement peut être altérée.

![Notez l’icône](./media/storsimple-safety/IC740881.png) **avis :**

Pour le bon fonctionnement de votre matériel et afin d’éviter des dommages de produit :

- Les ports RJ45 à l’arrière de l’appareil sont pour une connexion Ethernet uniquement. Ces ne doivent pas être connectés à un réseau de télécommunications.

- Veillez à installer le périphérique dans un rack capable d’accueillir une conception de refroidissement avant à l’arrière.

- Plug-in de tous les modules et les plaques vierges font partie de l’enceinte du système. Il doivent être supprimés uniquement lorsqu’un remplacement est immédiatement ajouté. Le système ne doit pas être exécuté sans tous les modules ou espaces en place.

## <a name="rack-system-precautions"></a>Précautions de système en rack

Les exigences de sécurité suivantes prendre en considération lorsque vous montez le périphérique dans une armoire de rack.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) ![icône de danger Conseil](./media/storsimple-safety/IC740886.png) **Avertissement !**
 
Pour réduire le risque d’un préjudice important à partir d’une info-bulle sur :

- La conception en rack doit de prendre en charge le poids total des boîtiers installés et doit incorporer des fonctionnalités de stabilisation pouvant empêcher le rack de dépôt ou poussé dans lors de l’installation ou l’utilisation normale.

- Lors du chargement d’un rack, remplir le rack de bas en et vide à partir du haut vers le bas.

- Réaliser de diapositives plus d’un boîtier du rack à la fois pour éviter le risque d’une télévision rack.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) ![icône de choc électrique](./media/storsimple-safety/IC740882.png) **Avertissement !**

Pour réduire le risque de blessure, de choc électrique ou de décès :

- Le rack doit disposer d’un système sécurisé de distribution électrique. Il doit fournir pour le boîtier de protection contre les surintensités et ne doit pas être surchargée par le nombre total de pièces jointes installé. L’évaluation de la consommation d’énergie électrique indiquée sur la plaque signalétique doit être observée.

- Le système de distribution électrique doit fournir une base fiable pour chaque boîtier dans le rack.

- La conception du système de distribution électrique doit prendre en considération la fuite de sol total en cours à partir de tous les blocs d’alimentation de tous les boîtiers. Notez que chaque bloc d’alimentation de chaque boîtier dispose d’un courant de fuite de terre 1.0 maximum de mA à 60 Hz, 264 volts. Le rack peut nécessiter d’étiquetage avec « courant de fuite élevé. Connexion à la terre (terre) est essentielle avant de vous connecter à une fourniture. »

- Le rack, lorsqu’il est configuré avec les boîtiers, doit répondre aux exigences de sécurité de UL 60950-1 et IEC 60950-1/EN 60950-1.

![Notez l’icône](./media/storsimple-safety/IC740881.png) **avis :**

Pour le refroidissement du système de rack :

- Assurez-vous que la conception en rack prend en considération le boîtier maximum température ambiante de 35 degrés Celsius (95 degrés Fahrenheit).

- Le système est exploité avec installation de basse pression, d’échappement de l’arrière (contre-pression créée par les portes des racks et des obstacles pour ne pas dépasser 5 Pascal [0,5 mm de colonne d’eau]).

## <a name="power-cooling-module-pcm-precautions"></a>Précautions de Module de refroidissement (PCM) de puissance

Le périphérique est conçu pour fonctionner avec les deux PCMs. Chacun des PCMs a une alimentation et un ventilateur de deux axes. Au cours d’une condition critique, le système permet d’une panne d’un bloc d’alimentation tout en continuant d’opérations normales. Deux PCMs (et, par conséquent, les blocs d’alimentation) doit toujours être installé. Un seul PCM ne fournit pas d’alimentation redondante. Par conséquent, la défaillance d’un seul PCM peut entraîner d’interruption ou de perte de données possible.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) ![icône de choc électrique](./media/storsimple-safety/IC740882.png) **Avertissement !**

Pour réduire le risque de blessure, de choc électrique ou de décès :

- Ne supprimez pas les coulisses de la PCM. Il existe un risque de choc électrique à l’intérieur. Pour renvoyer le PCM et obtenir un remplacement, [contactez le support technique de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

![Notez l’icône](./media/storsimple-safety/IC740881.png) **avis :**

Pour le bon fonctionnement de votre matériel et afin d’éviter des dommages de produit :

- Vous devez remplacer le PCM a échoué dans les 24 heures. Après la suppression d’un PCM de remplacement, le remplacement doit être terminé dans les 10 minutes après la suppression.

- Ne supprimez pas un PCM, sauf si un remplacement peut être installé immédiatement. L’enceinte ne doit être manoeuvré sans tous les modules en place.

## <a name="electrostatic-discharge-esd-precautions"></a>Précautions de décharge électrostatique (ESD)

![Notez l’icône](./media/storsimple-safety/IC740881.png) **avis :**

Les précautions suivantes liées antistatique.

- Vérifiez que vous avez installé et activé une erse de cheville ou de poignet antistatique appropriée.

- Observez toutes les précautions contre les décharges électrostatiques conventionnelles lors de la gestion des composants et des modules.

- Éviter le contact avec les composants de fond de panier et les connecteurs du module.

- Les dommages antistatique ne sont pas couvert par la garantie.

## <a name="battery-disposal-precautions"></a>Précautions de cession de batterie

L’alimentation utilise une batterie spéciale pour protéger le contenu de la mémoire lors des coupures temporaires, à court terme. La batterie est insérée dans le PCM. Gardez les informations suivantes à l’esprit de la batterie.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png) **Avertissement !**

Pour réduire le risque de short, incendie, explosion, blessure ou de décès :

- Mettre les batteries usagées au rebut conformément aux réglementations nationales/régionales.

- Ne pas désassembler, broyer, ou de chaleur au-dessus de 60 degrés Celsius (140 degrés Fahrenheit) ou incinération. Remplacez la batterie PCM avec une batterie fournie uniquement. Utilisation d’une autre batterie peut présenter un risque d’incendie ou d’explosion.

- Utilisez les capuchons de protection sur les batteries si elles sont supprimées du bloc d’alimentation.

![Notez l’icône](./media/storsimple-safety/IC740881.png) **avis :**

Lors de l’expédition ou au transport sinon des piles par voie aérienne, suivez le document Guide de batterie au Lithium IATA disponible à l’adresse [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Après avoir consulté ces avis de sécurité, les étapes suivantes consistent à décompresser, rack et le câbler votre périphérique.

## <a name="next-steps"></a>Étapes suivantes

- Pour un dispositif 8100, accédez à [installation de votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md).

- Pour un dispositif 8600, accédez à [installation de votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md).

