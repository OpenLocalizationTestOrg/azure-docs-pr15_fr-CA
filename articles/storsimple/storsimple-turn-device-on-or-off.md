<properties 
   pageTitle="Activer ou désactiver les votre périphérique StorSimple | Microsoft Azure"
   description="Explique comment activer un nouveau périphérique StorSimple, activer un périphérique qui a été arrêté ou coupure et désactiver un périphérique en cours d’exécution."
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
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Activer ou désactiver les votre périphérique StorSimple 

## <a name="overview"></a>Vue d’ensemble

Arrêt d’un périphérique Microsoft Azure StorSimple n’est pas nécessaire dans le cadre d’un fonctionnement normal du système. Toutefois, vous devrez activer un nouveau périphérique ou un périphérique qui devait être arrêté. En règle générale, un arrêt est nécessaire dans les cas où vous devez remplacer le matériel défaillant, physiquement déplacer une unité ou mettre un périphérique hors service. Ce didacticiel décrit la procédure requise pour la mise sous tension et d’arrêt de votre périphérique StorSimple dans des scénarios différents.

Le tableau suivant répertorie les différents scénarios de mise sous tension et d’arrêt de votre périphérique StorSimple et fournit des liens vers les procédures appropriées.

|Scénario|Rubriques de référence|
|:-------|:---------------|
|Activer un nouveau périphérique|[Activer un nouveau périphérique](#turn-on-a-new-device)<ul><li>[Nouveau périphérique avec le boîtier principal uniquement](#new-device-with-primary-enclosure-only)</li><li>[Nouveau périphérique avec le boîtier de la EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Activer un périphérique après l’arrêt|[Activer un périphérique après l’arrêt](#turn-on-a-device-after-shutdown)<ul><li>[Appareil avec boîtier principal uniquement](#device-with-primary-enclosure-only)</li><li>[DISPOSITIF avec le boîtier de la EBOD](#device-with-ebod-enclosure)</li></ul>|
|Activer un périphérique après une coupure d’alimentation|[Activer un périphérique après une coupure d’alimentation](#turn-on-a-device-after-a-power-loss)<ul><li>[Appareil avec boîtier principal uniquement](#8100)</li><li>[DISPOSITIF avec le boîtier de la EBOD](#8600)</li></ul>|
|Activer un périphérique après le boîtier principal et EBOD la connexion est perdue|[Activer un périphérique après la principale et la connexion du boîtier EBOD est perdue](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Arrêter un périphérique en cours d’exécution|[Désactiver un périphérique en cours d’exécution](#turn-off-a-running-device)<ul><li>[Appareil avec boîtier principal uniquement](#8100a)</li><li>[DISPOSITIF avec le boîtier de la EBOD](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Activer un nouveau périphérique

Les étapes pour activer un périphérique StorSimple pour la première fois diffèrent selon que le périphérique est un 8100 ou un modèle 8600. Le 8100 a un seul boîtier principal, alors que la 8600 est un dispositif de double-enceinte avec un boîtier principal et d’un boîtier EBOD. Les étapes détaillées pour les deux modèles sont traités dans les sections suivantes.

- [Nouveau périphérique avec le boîtier principal uniquement](#new-device-with-primary-enclosure-only)

- [Nouveau périphérique avec le boîtier de la EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nouveau périphérique avec le boîtier principal uniquement

Le modèle StorSimple 8100 est un périphérique de boîtier unique. Votre périphérique inclut redondants alimentation et Modules de refroidissement (PCM pour). Les PCMs doivent être installés et connectés à plusieurs sources d’alimentation pour garantir une haute disponibilité.

Effectuez les opérations suivantes pour le câble de votre périphérique d’alimentation.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Pour le programme d’installation de périphérique complet et les instructions de câblage, accédez à [installation de votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md). Assurez-vous que vous suivez les instructions.

### <a name="new-device-with-ebod-enclosure"></a>Nouveau périphérique avec le boîtier de la EBOD

Le modèle StorSimple 8600 dispose d’un boîtier principal et un boîtier EBOD. Cette opération nécessite les unités à être câblés ensemble d’alimentation et de connectivité de Serial Attached SCSI (SAS).

Lors de la configuration de ce périphérique pour la première fois, exécutez les étapes pour SAS câblage tout d’abord et puis suivez les étapes pour le câblage d’alimentation.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Pour le programme d’installation de périphérique complet et les instructions de câblage, accédez à [installation de votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md). Assurez-vous que vous suivez les instructions.

## <a name="turn-on-a-device-after-shutdown"></a>Activer un périphérique après l’arrêt

Les étapes pour activer un périphérique StorSimple après que qu’il a été arrêté sont différents selon que le périphérique est un 8100 ou un modèle 8600. Le 8100 a un seul boîtier principal, alors que la 8600 est un dispositif de double-enceinte avec un boîtier principal et d’un boîtier EBOD.

- [Appareil avec boîtier principal uniquement](#device-with-primary-enclosure-only)

- [DISPOSITIF avec le boîtier de la EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Appareil avec boîtier principal uniquement

Après un arrêt, utilisez la procédure suivante pour activer un périphérique StorSimple avec un boîtier principal et aucun boîtier EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Pour activer un périphérique avec un boîtier principal uniquement

1. Assurez-vous que la puissance passe sur les deux d’alimentation et Modules de refroidissement (PCM pour) en position d’arrêt. Si les commutateurs ne sont pas en position d’arrêt, puis les retourner à la position OFF et attendez que les témoins lumineux s’éteignent.

2. Allumez le périphérique en le retournant les interrupteurs d’alimentation sur les deux PCMs sur la position ON. Le périphérique doit activer.

3. Vérifiez les éléments suivants pour vérifier que le périphérique est entièrement sur :

    1. Les voyants de OK dans les deux modules PCM sont verts.

    2. Les voyants d’état sur les deux contrôleurs sont vert et fixe.

    3. Le voyant bleu sur un des contrôleurs est clignotant, qui indique que le contrôleur est actif.

    Si ces conditions ne sont pas remplies, votre périphérique n’est pas en bon état. Veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>DISPOSITIF avec le boîtier de la EBOD

Après un arrêt, utilisez la procédure suivante pour activer un périphérique StorSimple avec un boîtier principal et d’un boîtier EBOD. Effectuer chaque étape dans une séquence exactement comme indiqué. Cela peut provoquer la perte de données.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Pour activer un périphérique avec un serveur principal et un boîtier EBOD

1. Assurez-vous que le boîtier EBOD est connecté au boîtier principal. Pour plus d’informations, consultez [installer votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assurez-vous que la puissance et les Modules de refroidissement (PCM pour) EBOD et boîtiers principales sont en position d’arrêt. Si les commutateurs ne sont pas en position d’arrêt, puis les retourner à la position OFF et attendez que les témoins lumineux s’éteignent.

3. Allumez le boîtier EBOD première en inversant les interrupteurs d’alimentation sur les deux PCMs sur la position ON. PCM voyants doivent être verts. Un contrôleur de EBOD voyant vert sur cette unité indique que le boîtier EBOD.

4. Allumez le boîtier principal en inversant les interrupteurs d’alimentation sur les deux PCMs sur la position ON. L’ensemble du système doit apparaître.

5. Vérifiez que les voyants de SAS sont verts, ce qui garantit que la connexion entre le boîtier de la EBOD et le boîtier principal est bonne.

## <a name="turn-on-a-device-after-a-power-loss"></a>Activer un périphérique après une coupure d’alimentation

Une panne de courant ou d’interruption peut arrêter un périphérique StorSimple. La panne peut se produire dans un des blocs d’alimentation ou les deux blocs d’alimentation. Les étapes de récupération sont différentes selon que le périphérique est un modèle 8100 ou 8600. Le 8100 a un seul boîtier principal, alors que la 8600 est un dispositif de double-enceinte avec un boîtier principal et d’un boîtier EBOD. Cette section décrit la procédure de récupération pour chaque scénario.

- [Appareil avec boîtier principal uniquement](#8100)

- [DISPOSITIF avec le boîtier de la EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Appareil avec boîtier principal uniquement<a name="8100">

Le système peut continuer son fonctionnement normal en cas de perte de puissance à l’un de ses blocs d’alimentation. Toutefois, pour garantir une haute disponibilité de l’équipement, restaurez l’alimentation de l’alimentation dès que possible.

S’il existe une panne de courant ou d’une coupure de courant sur les deux blocs d’alimentation, le système s’arrêtera de façon ordonnée et contrôlée. Lorsque le courant est rétabli, le système se met automatiquement sous tension.

### <a name="device-with-ebod-enclosure-a-name8600"></a>DISPOSITIF avec le boîtier de la EBOD<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Fournir de perte d’alimentation sur une alimentation

Le système peut continuer son fonctionnement normal si la perte de puissance à l’un de ses blocs d’alimentation sur le boîtier principal ou le boîtier EBOD. Toutefois, pour garantir une haute disponibilité de l’équipement, Veuillez restaurer l’alimentation du bloc d’alimentation dès que possible.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perte de puissance sur les deux blocs d’alimentation sur le principal et les boîtiers EBOD

S’il existe une panne de courant ou d’une coupure de courant sur les deux blocs d’alimentation, le boîtier de la EBOD s’arrêtera immédiatement et le boîtier principal va arrêter de manière ordonnée et contrôlée. Lorsque l’alimentation est restaurée, la solution matérielle-logicielle démarrera automatiquement.

Si l’est mis hors tension manuellement, puis procédez comme suit pour restaurer le système d’alimentation.

1. Allumez le boîtier EBOD.

2. Une fois le boîtier EBOD sur, allumez le boîtier principal.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perte de puissance sur les deux blocs d’alimentation sur le boîtier de la EBOD

Lorsque vous configurez les câbles, vous devez vous assurer que la EBOD seul n’est jamais connecté à une PDU distincte. Si le EBOD et le boîtier principal échouent en même temps, le système permet de récupérer.

Si seul le boîtier EBOD échoue dans les deux blocs d’alimentation, le système ne récupère pas automatiquement. Effectuez les étapes suivantes pour activer le système et le restaurer vers un état sain :

1. Si le boîtier principal est activé, désactivez à la fois puissance et Modules de refroidissement (PCM pour).

2. Attendez quelques minutes pour arrêter le système.

3. Allumez le boîtier EBOD.

4. Une fois le boîtier EBOD sur, allumez le boîtier principal.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Activer un périphérique après la principale et la connexion du boîtier EBOD est perdue

Si la connexion est perdue entre le contrôleur de secours et le contrôleur EBOD correspondant, le périphérique continue à fonctionner. Si la connexion entre le contrôleur active du système et le contrôleur EBOD correspondant est perdue, reprise doit avoir lieu et le périphérique doit continuer à fonctionner normalement.

Lorsque les deux câbles Serial Attached SCSI (SAS) sont supprimés ou que la connexion entre le boîtier de la EBOD et le boîtier principal est interrompue, le périphérique fonctionnent plus. À ce stade, effectuez les opérations suivantes.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Pour activer le périphérique une fois que la connexion est perdue

1. Accéder à l’arrière de l’appareil.

2. Si la connexion du câble SAS entre le boîtier de la EBOD et le boîtier principal est rompue, lane SAS tous les voyants du boîtier EBOD sera désactivé.

3. Arrêter à la fois puissance et Modules de refroidissement (PCM pour) le boîtier EBOD et le boîtier principal.

4. Attendez que tous les voyants situés à l’arrière des deux boîtiers désactiver.

5. Réinsérez les câbles SAS et vérifiez qu’il existe une bonne connexion entre le boîtier de la EBOD et le boîtier principal.

6. Allumez le boîtier EBOD première en désactivant les deux commutateurs PCM sur la position ON.

7. Vérifiez que le boîtier EBOD en vérifiant que le voyant vert est ON.

8. Allumez le boîtier principal.

9. Vérifiez que le boîtier principal en vérifiant que le voyant vert du contrôleur est ON.

10. Vérifiez que la connexion du boîtier EBOD avec le boîtier principal est correcte en vérifiant que le couloir SAS voyants (quatre par contrôleur EBOD) sont tous sur.

>[AZURE.IMPORTANT] Si les câbles SAS sont défectueux ou la connexion entre le boîtier de la EBOD et le boîtier principal n’est pas bonne, lorsque vous activez le système, il passe en mode de récupération. Dans ce cas, veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) .

## <a name="turn-off-a-running-device"></a>Désactiver un périphérique en cours d’exécution

Un périphérique en cours d’exécution de StorSimple devrez peut-être de s’arrêter s’il est déplacé, hors service, ou a un composant défectueux qui doit être remplacé. Les étapes sont différentes selon que le dispositif de StorSimple est un 8100 ou un modèle 8600. Le 8100 a un seul boîtier principal, alors que la 8600 est un dispositif de double-enceinte avec un boîtier principal et d’un boîtier EBOD. Cette section détaille les étapes à arrêter un périphérique en cours d’exécution.

- [Appareil avec boîtier principal](#8100a)

- [DISPOSITIF avec le boîtier de la EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Appareil avec boîtier principal<a name="8100a"> 

Pour arrêter le périphérique de manière ordonnée et contrôlée, vous pouvez le faire via le portail classique Azure ou le Windows PowerShell pour StorSimple. 

>[AZURE.IMPORTANT] N’arrêtez pas un périphérique en cours d’exécution en utilisant le bouton d’alimentation à l’arrière de l’appareil.
>
>Avant d’arrêter le périphérique, vérifiez que tous les composants de périphériques sont en bon état. Dans le portail classique Azure, accédez aux **périphériques** > **Maintenance** > **État du matériel**et vérifiez que l’état de tous les composants est verte. Cela est vrai uniquement pour un système sain. Si le système est en cours d’arrêt vers le bas pour remplacer un composant ne fonctionne pas correctement, vous verrez un échec (rouge) ou état (jaune) pour le composant concerné dans l' **État du matériel**de dégradé.

Après avoir accédé à la Windows PowerShell pour StorSimple ou l’Azure portal classique, suivez les étapes dans [l’arrêt d’un périphérique StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>DISPOSITIF avec le boîtier de la EBOD<a name="8600a">

>[AZURE.IMPORTANT] Avant d’arrêter le boîtier principal et le boîtier EBOD, assurez-vous que tous les composants de périphériques sont en bon état. Dans le portail classique Azure, accédez aux **périphériques** > **Maintenance** > **État du matériel**et vérifiez que tous les composants sont en bon état.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Pour arrêter un périphérique en cours d’exécution avec le boîtier de la EBOD

1. Suivez les étapes répertoriées dans l' [arrêt d’un dispositif de StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) pour le boîtier principal.

2. Une fois le boîtier principal est arrêté, arrêter la EBOD par inversion de commutateurs à la fois puissance et Module de refroidissement (PCM).

3. Pour vérifier que le EBOD est arrêté, vérifiez que tous les voyants à l’arrière du boîtier EBOD sont désactivés.

>[AZURE.NOTE] Les câbles SAS qui servent à connecter le boîtier de la EBOD pour le boîtier principal ne doivent pas être supprimés qu’une fois que le système est arrêté.

## <a name="next-steps"></a>Étapes suivantes

[Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) si vous rencontrez des problèmes lors de la mise sous tension ou l’arrêt d’un périphérique StorSimple.

