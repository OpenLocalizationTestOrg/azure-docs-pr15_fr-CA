<properties 
   pageTitle="Remplacement de composants matériels StorSimple | Microsoft Azure"
   description="Décrit comment remplacer en toute sécurité de la PCMs, batterie, modules de contrôleur, EBOD contrôleurs, disques et châssis d’un dispositif de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>Remplacement du composant matériel StorSimple

## <a name="overview"></a>Vue d’ensemble

Les didacticiels de remplacement de composant matériel décrivent les composants matériels de votre périphérique de la gamme Microsoft Azure StorSimple 8000 et la procédure à suivre supprimer et les remplacer. Cet article décrit les icônes de sécurité fournit des pointeurs vers des didacticiels détaillés et répertorie les composants qui sont remplaçables.

>[AZURE.IMPORTANT] Avant de tenter de supprimer ou de remplacer tout composant de StorSimple, assurez-vous que vous passez en revue les [conventions des icônes de sécurité](#safety-icon-conventions) et autres [précautions de sécurité](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Conventions des icônes de sécurité

Le tableau suivant décrit les icônes de sécurité utilisés dans ces didacticiels. Faites attention au ces icônes de sécurité tout au long de la procédure pour supprimer et remplacer les composants de l’appareil.

| Icône | Texte | Informations supplémentaires |
|:---- |:---- |:-----------|
|![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png)| **DANGER !** | Indique une situation dangereuse qui, si ne pas évité, entraîne la mort ou des blessures graves. Ce mot de signal est limité à des situations plus extrêmes.|
|![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png)| **AVERTISSEMENT !** | Indique une situation dangereuse qui, si ne pas évité, peut entraîner dans la mort ou des blessures graves.|
|![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Caution.png)| **ATTENTION !** |Indique une situation dangereuse qui, si ne pas évité, peut entraîner des blessures mineure ou modérée.|
|![Icône de notification](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **MISE EN GARDE :** | Indique des informations considérées comme importantes, mais non vitale.|
|![Icône de choc électrique](./media/storsimple-hardware-component-replacement/Electric.png) | **Risque de choc électrique** | Indique à haute tension.|
|![Icône de poids lourds](./media/storsimple-hardware-component-replacement/Weight.png)| **Poids lourd**| |
|![Aucune icône d’éléments dont la maintenance utilisateur](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **Aucun éléments dont la maintenance utilisateur** | N’accédez pas à moins que correctement formé.|
|![Icône des instructions de lecture](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Lisez d’abord toutes les Instructions**| |
|![Icône de risque de pointe](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Conseil de danger**| |

### <a name="before-you-begin"></a>Avant de commencer

Familiarisez-vous avec les informations de sécurité sur vos icônes de périphérique et de sécurité utilisés dans ce didacticiel. Pour plus d’informations, consultez [installer et faire fonctionner votre appareil StorSimple en toute sécurité](storsimple-safety.md) . Veillez à passer en revue les [précautions à prendre](storsimple-safety.md#handling-precautions) avant de la gérer de votre périphérique StorSimple. 

Avant d’essayer de remplacer un composant, tenez compte des informations suivantes.

![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png) ![icône de choc électrique](./media/storsimple-hardware-component-replacement/Electric.png) **Avertissement !** 

- La terre correctement à l’aide d’une décharge électrostatique ou le tapis antistatique lors de la gestion des modules et des composants de votre périphérique StorSimple.

- Ne touchez pas les circuits. Utilisez les guides et les poignées fournies lors de la manipulation des composants qui peuvent avoir exposé un circuit.

![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png) ![Notez l’icône](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **avis :**

Lorsque vous remplacez un module, et **ne laissez jamais une baie vide à l’arrière du boîtier**. Obtenir un remplacement ou un module vierge avant de supprimer la partie du problème.

## <a name="hardware-component-replacement-procedures"></a>Procédures de remplacement de composant matériel

Votre périphérique de la gamme 8000 de StorSimple se compose de plusieurs modules plug-in dans le principal et/ou les boîtiers EBOD. Le 8100 a un seul boîtier principal, alors que la 8600 est un périphérique de boîtier double avec un boîtier principal et d’un boîtier EBOD.

Les principaux composants matériels de votre périphérique sont résumés dans les tableaux suivants. Cliquez sur le lien dans la colonne de la **procédure de remplacement** pour accéder au didacticiel associé.

|Composants|# Présent|Module plug-in ?|Procédure de remplacement
|:---------|:--------|:--------------|:---------------------|
| Châssis|1|N°|[Remplacez le châssis de votre périphérique StorSimple](storsimple-chassis-replacement.md) |
|Contrôleurs principaux|2|Oui| [Remplacement d’un module de contrôleur sur votre périphérique de StorSimple](storsimple-controller-replacement.md) |
|Alimentation de 764W et de ventilation des Modules (PCM pour)|2|Oui| [Remplacer un Module de refroidissement et de puissance sur le périphérique StorSimple](storsimple-power-cooling-module-replacement.md) |
|Batterie de secours|2|Oui| [Remplacez le module de batterie de secours de votre périphérique StorSimple](storsimple-battery-replacement.md) |
|Lecteurs de disque|12|Oui| [Remplacer un lecteur de disque sur le périphérique StorSimple](storsimple-disk-drive-replacement.md) |

**Tableau 1** Composants matériels dans le boîtier principal

Le boîtier principal et le boîtier EBOD diffèrent dans les modules d’e/s. En outre, les PCMs ont puissance différente. Les PCMs dans le boîtier principal sont 764 W, considérant que ceux dans le boîtier EBOD 580 W. Les PCMs dans le boîtier principal contiennent également un module de batterie de secours.

|Composants|# Présent|Module plug-in ?| Procédure de remplacement
|:---------|:--------|:--------------|:---------------------|
|Châssis|1|N°| [Remplacez le châssis de votre périphérique StorSimple](storsimple-chassis-replacement.md) |
|Contrôleurs de EBOD|2|Oui| [Remplacer un contrôleur EBOD sur votre périphérique de StorSimple](storsimple-ebod-controller-replacement.md) |
|Alimentation de 580 w et de ventilation des Modules (PCM pour)|2|Oui| [Remplacer un Module de refroidissement et de puissance sur le périphérique StorSimple](storsimple-power-cooling-module-replacement.md) |
|Lecteurs de disque|12|Oui| [Remplacer un lecteur de disque sur le périphérique StorSimple](storsimple-disk-drive-replacement.md) |

**Le tableau 2** Composants matériels dans le boîtier de la EBOD

Les modules de plug-in sur le périphérique sont mis en surbrillance suivant avant et arrière diagrammes. Vous pouvez utiliser ces diagrammes pour déterminer l’emplacement des différents modules plug-in si un remplacement est nécessaire. Le diagramme avant affiche les lecteurs de disque et les diagrammes arrière de l’enceinte EBOD et de l’émission de boîtier principal les modules plug-in.

![Frontplane de périphérique avec les lecteurs de disque](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figure 1** Avant de l’appareil

|Étiquette|Description|
|:----|:----------|
|0 - 11|Lecteurs de disque (total de 12)|

Le boîtier principal et l’enceinte EBOD ont des modules de support de lecteur. Le châssis abrite douze lecteurs de disques 3,5" organisées dans un format de 3 par 4.

![Fond de panier de modules de boîtier principal de périphérique](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figure 2** À l’arrière du boîtier principal

|Étiquette|Description|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Contrôleur 0|
|4|Contrôleur 1|

![Fond de panier de modules plug-in du boîtier de dispositif EBOD](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figure 3** À l’arrière du boîtier EBOD

|Étiquette|Description|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Contrôleur de EBOD 0|
|4|Contrôleur de EBOD 1|

## <a name="field-replaceable-units"></a>Pièces remplaçables

Les suivant remplaçables (FRU) sont disponibles pour votre périphérique StorSimple :

- Châssis (y compris le panneau intégré d’opérations)

- PCM DE CA 764 W

- PCM DE CA 580 W

- Lecteur de disque dur avec module de lecteur de transporteur

- Module de contrôleur

- Module contrôleur EBOD

- Module de batterie de secours

- Kit de rails de montage en rack

Veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour commander une de ces unités de remplacement.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue toutes les [informations de sécurité](storsimple-safety.md) avant d’essayer de remplacer un composant matériel de StorSimple.
