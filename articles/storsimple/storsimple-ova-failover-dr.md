<properties
   pageTitle="Dispositif et la récupération des basculements sur incident pour votre tableau virtuel StorSimple"
   description="Pour en savoir plus sur la façon de basculer votre tableau virtuel StorSimple."
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
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Dispositif et la récupération des basculements sur incident pour votre tableau virtuel StorSimple


## <a name="overview"></a>Vue d’ensemble

Cet article décrit la récupération d’urgence pour votre tableau virtuel Microsoft Azure StorSimple (également connu comme le StorSimple sur site périphérique virtuel) y compris les étapes détaillées permettant de basculer sur un autre périphérique virtuel en cas de sinistre. Un basculement sur incident vous permet de faire migrer vos données à partir d’un périphérique *source* dans le centre de données à un autre équipement *cible* que qui se trouve dans le même ou un autre emplacement géographique. Le basculement de périphérique est pour l’ensemble de l’unité. Au cours du basculement, les données de nuage pour le périphérique source modifie la propriété à celle de l’équipement cible.

Basculement du périphérique est orchestré via la fonctionnalité de reprise après sinistre et est lancée à partir de la page **de périphériques** . Cette page classifie tous les StorSimple de périphériques connectés à votre service de gestionnaire de StorSimple. Pour chaque périphérique, le nom convivial, le statut, la capacité maximale et mis en service, le type et le modèle sont affichés.

![](./media/storsimple-ova-failover-dr/image15.png)

Cet article est uniquement applicable aux tableaux virtuels de StorSimple. Pour basculer un périphérique 8000 série, atteindre le [basculement et la reprise après sinistre de votre périphérique StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>Quelle est la reprise après sinistre ?

Dans un scénario de reprise après sinistre, le principal périphérique cesse de fonctionner. Dans ce cas, vous pouvez déplacer les données de nuage associées avec le périphérique défaillant vers un autre périphérique en utilisant le périphérique principal comme *source* et en spécifiant un autre périphérique comme *cible*. Ce processus est appelé *basculement*. Au cours du basculement, tous les volumes ou les partages du périphérique source changent de propriétaire et sont transférées vers le périphérique cible. Aucun filtrage des données n’est autorisé.

Reprise après sinistre est modélisé sous la forme d’une restauration complète de périphériques à l’aide de la chaleur basé sur la carte la hiérarchisation et le suivi. Une carte thermique est définie par l’affectation d’une valeur de chaleur à la base de données en lecture et en écriture des modèles. Cette chaleur mapper puis niveaux les segments de données plus faible chaleur vers le nuage tout d’abord tout en conservant les segments de données de chaleur élevée (plus utilisé) au niveau local. Au cours d’une reprise après sinistre, la carte thermique sert à restaurer et réalimenter les données dans le nuage. Le périphérique extrait tous les volumes/partages dans la dernière sauvegarde récente (comme déterminé en interne) et procède à une restauration à partir de cette sauvegarde. Tout le processus de reprise après sinistre est orchestré par le périphérique.


## <a name="prerequisites-for-device-failover"></a>Conditions préalables pour le basculement entre périphériques


### <a name="prerequisites"></a>Conditions préalables

Pour le basculement de tout périphérique, les conditions préalables suivantes doivent être satisfaites :

- Le périphérique de source doit être dans un état **désactivé** .

- Le périphérique cible doit s’affiche comme étant **Active** dans le portail classique Azure. Vous devez configurer un périphérique virtuel de la cible de la capacité identique ou supérieur. Utilisez ensuite l’interface utilisateur web de local pour configurer et enregistrer correctement le périphérique virtuel.

    > [AZURE.IMPORTANT] Ne pas configurer le périphérique virtuel inscrit via le service en cliquant sur **le programme d’installation complet**. Aucune configuration de périphérique ne doit être effectuée via le service.

- Le périphérique source et cible doivent être du même type. Vous pouvez uniquement basculer vers un périphérique virtuel est configuré comme un serveur de fichiers vers un autre serveur de fichiers. Il en est de même pour un serveur iSCSI.

- Pour un reprise après sinistre du serveur de fichiers, nous vous recommandons de rejoindre le périphérique cible dans le même domaine que celui de la source afin que les autorisations de partage sont automatiquement résolues. Cette version prend en charge seulement le basculement vers un équipement cible dans le même domaine.

### <a name="other-considerations"></a>Autres considérations

- Nous recommandons de prendre tous les volumes ou les partages sur le périphérique source en mode hors connexion.

- S’il s’agit d’un basculement planifié, il est recommandé que vous effectuez une sauvegarde du périphérique, puis continuez le basculement afin de minimiser la perte de données. S’il s’agit d’un basculement non planifié, la sauvegarde la plus récente servira à restaurer le périphérique.

- Les périphériques cibles disponibles pour la reprise après sinistre sont des périphériques qui ont la capacité égale ou supérieure par rapport à l’appareil source. Les périphériques qui sont connectés à votre service, mais qui ne répondent pas aux critères d’un espace suffisant ne sera pas disponibles en tant qu’équipements cibles.

### <a name="dr-prechecks"></a>Prechecks de reprise après sinistre

Avant le début de la reprise après sinistre, les prechecks sont effectuées sur le périphérique. Ces vérifications permettent de s’assurer qu’aucune erreur ne se produira lors de la reprise après sinistre commence. Les prechecks sont les suivantes :

- Validation du compte de stockage

- Vérification de la connectivité du nuage vers Azure

- Vérification de l’espace disponible sur le périphérique cible

- Vérifier si un périphérique source du serveur iSCSI possède des noms valides de blocage, IQN (ne dépassant pas 220 caractères) et mot de passe CHAP (12 et 16 caractères) correspondant aux volumes

Si d’échec des prechecks ci-dessus, ne peut pas procéder à la reprise après sinistre. Vous devez résoudre ces problèmes et recommencez DR.

Une fois la reprise après sinistre est effectuée avec succès, la propriété des données sur le périphérique source nuage est transférée vers le périphérique cible. Le périphérique source puis n’est plus disponible sur le portail. L’accès à tous les volumes/partages sur le périphérique source est bloqué et l’équipement cible devient actif.

> [AZURE.IMPORTANT]
> 
> Si le périphérique n’est plus disponible, l’ordinateur virtuel que vous avez configuré sur le système hôte utilise toujours des ressources. Une fois la reprise après sinistre terminée avec succès, vous pouvez supprimer cet ordinateur virtuel à partir de votre système hôte.

## <a name="fail-over-to-a-virtual-array"></a>Basculer vers un tableau virtuel

Nous vous recommandons de vous un autre tableau virtuel StorSimple mis en service, configuré via l’interface utilisateur de web local et enregistré avec le service de gestionnaire de StorSimple avant d’exécuter cette procédure.


> [AZURE.IMPORTANT]
> 
> - Vous n’êtes pas autorisé à basculer d’un périphérique de la gamme 8000 de StorSimple à un périphérique virtuel de 1200.
> - Vous pouvez basculer d’un périphérique virtuel traitement Standard FIPS (Federal Information) activé déployé dans le portail du gouvernement à un périphérique virtuel dans Azure portal classique. L’inverse est également vrai.

Procédez comme suit pour restaurer le périphérique pour un périphérique virtuel de StorSimple cible.

1. Prendre des volumes/partages en mode hors connexion sur l’hôte. Consultez les instructions spécifiques au système d’exploitation sur l’hôte à déconnecter les volumes/partages. Si pas déjà en mode hors connexion, vous devez prendre tous les volumes/partages en mode hors connexion sur le périphérique en accédant à **périphériques > partages** (ou **périphérique > Volumes**). Sélectionnez un partage/volume et cliquez sur **Déconnecter** dans la partie inférieure de la page. Lorsque vous êtes invité à confirmer une opération, cliquez sur **Oui**. Répétez cette procédure pour tous les partages/volumes sur le périphérique.

2. Dans la page des **périphériques** , sélectionnez le périphérique de source pour le basculement, puis cliquez sur **désactiver**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Vous serez invité à confirmer. Désactivation de périphérique est un processus permanent qui ne peut pas être annulé. Vous rappellera également de prendre vos partages/volumes en mode hors connexion sur l’hôte.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. Après confirmation, la désactivation va démarrer. Une fois la désactivation terminée avec succès, vous serez averti.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. Dans la page des **périphériques** , l’état passe maintenant pour **désactivé**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Sélectionnez le périphérique désactivé et en bas de la page, cliquez sur **Basculer**.

6. Dans l’Assistant basculement de confirmation qui s’affiche, effectuez le des opérations suivantes :

    1. Dans la liste des périphériques disponibles, choisissez un **périphérique cible.** Uniquement les périphériques qui ont une capacité suffisante s’affichent dans la liste déroulante.

    2. Examinez les détails associés avec le périphérique source tels que le nom du périphérique, capacité totale et les noms des partages ayant basculé.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. **J’accepte que le basculement est une opération permanente et du périphérique source est supprimé une fois que le basculement est terminé,**Vérifiez.

8. Cliquez sur l’icône de vérification ![](./media/storsimple-ova-failover-dr/image1.png).


9. Un travail de basculement sera lancé et vous en serez averti. Cliquez sur **Afficher la tâche** pour contrôler le basculement.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. Dans la page **tâches** , vous verrez un travail de basculement créé pour le périphérique source. Ce travail effectue les prechecks de reprise après sinistre.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Après que les prechecks de reprise après sinistre sont réussies, le travail de basculement génèrera des travaux de restauration pour chaque volume/partage qui existe sur le périphérique source.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Une fois le basculement terminé, accédez à la page de **périphériques** .

    une barre d’outils. Sélectionnez le périphérique virtuel StorSimple qui a été utilisé comme le périphérique cible pour le processus de basculement.

    b. Accédez à la page de **partages** (ou **Volumes** si serveur iSCSI). Tous les partages (volumes) à partir de l’ancien périphérique doivent maintenant être affichés.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Vidéo disponible**

Cette vidéo vous montre comment vous pouvez basculer vers un périphérique virtuel local de StorSimple à un autre périphérique virtuel.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Continuité d’activité reprise (BCDR)

Un scénario de récupération (BCDR) business continuité d’activité après sinistre se produit lorsque la totalité du datacenter Azure s’arrête de fonctionner. Cela peut affecter votre service de gestionnaire de StorSimple et le StorSimple de périphériques associés.

S’il y a StorSimple les périphériques qui ont été enregistrés juste avant qu’un incident, ces périphériques de StorSimple devrez peut-être à supprimer. Après l’incident, vous pouvez recréer et configurer les périphériques.

## <a name="errors-during-dr"></a>Erreurs lors de la reprise après sinistre

**Panne de connectivité cloud lors de la reprise après sinistre**

Si la connectivité du nuage est interrompue après le début de reprise après sinistre et avant la fin de la restauration du périphérique, le DR échouera et vous en serez averti. L’équipement qui a été utilisé pour la reprise après sinistre est ensuite marqué comme *inutilisable.* Le même équipement cible ne peut pas servir ensuite de DRs futures.

**Aucun équipement cible compatible**

Si les périphériques cibles disponibles ne disposent pas de suffisamment d’espace, vous verrez une erreur indiquant qu’il n’y a aucun équipement cible compatible.

**Échecs de vérification préalable**

Si un de le prechecks n’est pas satisfait, vous verrez les défaillances precheck.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’administration de votre tableau virtuel StorSimple à l’aide de l’interface utilisateur de web local](storsimple-ova-web-ui-admin.md).
