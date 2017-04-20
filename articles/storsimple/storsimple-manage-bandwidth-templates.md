<properties
   pageTitle="Gérer vos modèles de bande passante StorSimple | Microsoft Azure"
   description="Décrit comment gérer les modèles de la bande passante de StorSimple, qui vous permettent de contrôler la consommation de bande passante."
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Le service Gestionnaire de StorSimple permet de gérer les modèles de la bande passante StorSimple

## <a name="overview"></a>Vue d’ensemble

Les modèles de la bande passante vous permettent de vous permet de configurer l’utilisation de la bande passante réseau entre plusieurs planifications d’heure de hiérarchiser les données à partir du périphérique StorSimple vers le nuage.

Avec agendas de limitation de bande passante, vous pouvez :

- Spécifier des planifications de la bande passante personnalisés selon les utilisations de réseau de la charge de travail.

- Centraliser la gestion et la réutilisation des planifications sur plusieurs périphériques de manière simple et transparente.

> [AZURE.NOTE] Cette fonctionnalité est disponible uniquement pour les périphériques physiques StorSimple et non pour les périphériques virtuels.

Tous les modèles de la bande passante de votre service sont affichés dans un format tabulaire et contiennent les informations suivantes :

- **Nom** : un nom unique attribué au modèle de la bande passante lors de sa création.

- **Planification** – le nombre de planifications contenus dans un modèle donné de la bande passante.

- **Utilisé par** : le nombre de volumes à l’aide des modèles de la bande passante.

La page de **configuration** du service Gestionnaire de StorSimple dans Azure portal classique vous permet de gérer les modèles de la bande passante.

Vous trouverez également des informations supplémentaires permettant de configurer des modèles de la bande passante dans :

- Questions et réponses à propos des modèles de la bande passante
- Méthodes conseillées pour les modèles de la bande passante

## <a name="add-a-bandwidth-template"></a>Ajouter un modèle de la bande passante

Procédez comme suit pour créer un nouveau modèle de la bande passante.

#### <a name="to-add-a-bandwidth-template"></a>Pour ajouter un modèle de la bande passante

1. Sur la page de **configuration** du service Gestionnaire de StorSimple, cliquez sur **Ajouter/modifier le modèle de la bande passante**.

2. Dans la boîte de dialogue **Ajouter/modifier le modèle de la bande passante** :

   1. Dans la liste déroulante **modèle** , sélectionnez **Créer nouveau** pour ajouter un nouveau modèle de la bande passante.
   2. Spécifiez un nom unique pour le modèle de la bande passante.

3. Définir une **planification de la bande passante**. Pour créer une planification :

   1. Dans la liste déroulante, choisissez les jours de la semaine, pour que le calendrier est configuré. Vous pouvez sélectionner plusieurs jours en activant les cases à cocher que qui se trouve avant les jours respectifs dans la liste.
   2. Sélectionnez l’option **Tous les jours** , si la planification est appliquée pour toute la journée. Lorsque cette option est activée, vous ne pouvez plus spécifier **L’heure de début** ou une **Heure de fin**. Exécution de la planification à partir de 12 h 00 à 11:59 PM.
   3. Dans la liste déroulante, sélectionnez une **Heure de début**. C’est lors de la planification va commencer.
   4. Dans la liste déroulante, sélectionnez une **Heure de fin**. C’est lors de la planification s’arrêtera.

         > [AZURE.NOTE] Planifications qui se chevauchent ne sont pas autorisées. Si les heures de début et de fin entraîne un chevauchement, vous verrez un message d’erreur à cet effet.

   5. Indiquez le **taux de la bande passante**. Il s’agit de la bande passante en mégabits par seconde (Mbps) utilisé par votre périphérique StorSimple dans les opérations impliquant le nuage (chargements et téléchargements). Indiquez un nombre compris entre 1 et 1 000 pour ce champ.

   6. Cliquez sur l’icône de vérification ![icône de vérification](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Le modèle que vous avez créé sera ajouté à la liste des modèles de la bande passante sur la page de **configuration** de service.

    ![Créez le nouveau modèle de la bande passante](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Cliquez sur **Enregistrer** en bas de la page, puis sur **Oui** lorsque vous êtes invité à confirmer. Ceci enregistrera les modifications de configuration que vous avez apportées.

## <a name="edit-a-bandwidth-template"></a>Modifier un modèle de la bande passante

Effectuez les opérations suivantes pour modifier un modèle de la bande passante.

### <a name="to-edit-a-bandwidth-template"></a>Pour modifier un modèle de la bande passante

1. Cliquez sur **Ajouter/modifier le modèle de la bande passante**.

2. Dans la boîte de dialogue **Ajouter/modifier le modèle de la bande passante** :

   1. Dans la liste déroulante **modèle** , sélectionnez un modèle existant de la bande passante que vous souhaitez modifier.
   2. Effectuez les modifications souhaitées. (Vous pouvez modifier les paramètres existants).
   3. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Vous verrez le modèle modifié dans la liste des modèles de la bande passante sur la page de configuration de service.

3. Pour enregistrer vos modifications, cliquez sur **Enregistrer** en bas de la page. Cliquez sur **Oui** lorsque vous êtes invité à confirmer.

> [AZURE.NOTE] Vous ne pas être autorisé à enregistrer vos modifications, si le programme modifié chevauche un tableau existant dans le modèle de la bande passante que vous modifiez.

## <a name="delete-a-bandwidth-template"></a>Supprimer un modèle de la bande passante

Effectuez les étapes suivantes pour supprimer un modèle de la bande passante.

#### <a name="to-delete-a-bandwidth-template"></a>Pour supprimer un modèle de la bande passante

1. Dans la liste des modèles de la bande passante pour votre service sous forme de tableau, sélectionnez le modèle que vous souhaitez supprimer. Une icône Supprimer (**x**) apparaît à l’extrême droite du modèle sélectionné. Cliquez sur l’icône **x** pour supprimer le modèle.

2. Vous serez invité à confirmer. Cliquez sur **OK** pour continuer.

Si le modèle est en cours d’utilisation par les volumes, vous ne pas être autorisé à supprimer. Vous verrez un message d’erreur indiquant que le modèle est en cours d’utilisation. Une boîte de dialogue message s’affiche vous indiquant que toutes les références au modèle doivent être supprimés.

Vous pouvez supprimer toutes les références au modèle en accédant à la page de **Conteneurs de Volume** et en modifiant les conteneurs de volume qui utilisent ce modèle afin qu’ils utilisent un autre modèle ou un paramètre personnalisé ou un nombre illimité de bande passante. Lorsque toutes les références ont été supprimés, vous pouvez supprimer le modèle.

## <a name="use-a-default-bandwidth-template"></a>Utiliser un modèle de la bande passante par défaut

Un modèle de la bande passante par défaut est fourni et est utilisé par les conteneurs de volume par défaut d’appliquer des contrôles de la bande passante lors de l’accès le nuage. Le modèle par défaut sert également de référence prête pour les utilisateurs de créer leurs propres modèles. Les détails de ce modèle par défaut sont :

- **Nom** – un nombre illimité de nuit et les week-ends

- **Planification** – une seule planification de lundi à vendredi auquel s’applique un taux de bande passante de 1 Mbit/s entre 8 h et 17 h 00 heure du périphérique. Pour le reste de la semaine, la bande passante est définie à Unlimited.

Le modèle par défaut peut être modifié. Suivi de l’utilisation de ce modèle (y compris les versions modifiées).

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Créer un modèle de la bande passante sur une journée entière qui démarre à une heure spécifiée

Suivez cette procédure pour créer une planification qui démarre à l’heure indiquée et exécute tous les jours. Dans l’exemple, la planification commence à 9 heures du matin et s’exécute jusqu'à 9 h du matin. Il est important de noter que les heures de début et de fin pour un programme donné doit se trouver sur la même planification de 24 heures et ne peut pas couvrir plusieurs jours. Si vous avez besoin configurer des modèles de la bande passante qui couvrent plusieurs jours, vous devrez utiliser plusieurs calendriers (comme illustré dans l’exemple).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Pour créer un modèle de la bande passante sur une journée entière

1. Créer une planification qui commence à 9 heures du matin et s’exécute jusqu'à minuit.

2. Ajouter une autre planification. Configurer la deuxième planifiée pour s’exécuter à partir de minuit jusqu'à 9 heures du matin.

3. Enregistrez le modèle de la bande passante.

La planification composite ensuite démarrer à la fois de votre choix et exécuter toute la journée.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Questions et réponses à propos des modèles de la bande passante

**Q**. Que se passe-t-il pour les contrôles de la bande passante lorsque vous avez entre les planifications ? (Une planification est terminée et une autre n’a pas encore démarré.)

**A**. Dans ce cas, aucun contrôle de bande passante n’est mises en place. Cela signifie que le périphérique peut utiliser un nombre illimité de la bande passante lors de hiérarchisation des données dans le cloud.

**Q**. Pouvez-vous modifier les modèles de la bande passante sur un périphérique hors connexion ?

**A**. Vous ne pourrez pas modifier les modèles de la bande passante sur les conteneurs de volumes si le périphérique correspondant est en mode hors connexion.

**Q**. Vous pouvez modifier un modèle de la bande passante associé à un conteneur de volume lorsque les volumes associés sont en mode hors connexion ?

**A**. Vous pouvez modifier un modèle de la bande passante associé à un conteneur de volume dont les volumes sont en mode hors connexion. Notez que lorsque les volumes sont en mode hors connexion, aucune donnée ne sera en cascade à partir du périphérique vers le nuage.

**Q**. Vous pouvez supprimer un modèle par défaut ?

**A**. Mais vous pouvez supprimer un modèle par défaut, il n’est pas conseillé de le faire. L’utilisation d’un modèle par défaut, y compris les versions modifiées, est l’objet d’un suivi. Les données de suivi sont analysées et au fil du temps, sont utilisées pour améliorer le modèle par défaut.

**Q**. Comment déterminez-vous que vos modèles de bande passante doivent être modifiées ?

**A**. Un des signes que vous devez modifier les modèles de la bande passante est lorsque vous commencez à voir le réseau ralentissent ou effet Maigri plusieurs fois en une journée. Dans ce cas, surveiller le réseau de stockage et d’utilisation en examinant les graphiques de performances et le débit du réseau.

Le débit de données du réseau, d’identifier l’heure de la journée et les conteneurs du volume dans lequel a lieu le goulot d’étranglement du réseau. Si cela se produit lorsque les données sont en cours de niveaux vers le nuage (obtenir ces informations à partir des performances d’e/s pour tous les conteneurs de volume pour le périphérique vers le cloud), vous devez modifier les modèles de la bande passante associées à vos conteneurs de volume.

Une fois les modèles modifiés sont en cours d’utilisation, vous devrez surveiller le réseau pour des latences importantes. Si elles existent toujours, vous devrez revoir vos modèles de bande passante.

**Q**. Que se passe-t-il si plusieurs conteneurs de volume de l’appareil ont planifie ce chevauchement mais différentes limites s’appliquent à chaque ?

**A**. Supposons que vous disposez d’un périphérique avec des conteneurs de volume 3. Les programmes associés à ces conteneurs complètement se chevauchent. Pour chacun de ces conteneurs, les limites de la bande passante utilisées sont respectivement 5, 10 et 15 Mbits/s. Lorsque les e/s se produisent sur l’ensemble de ces conteneurs en même temps, la valeur minimale de la limite de bande passante 3 peut être appliquée : dans ce cas, 5 Mbits/s en tant que ces sortant des demandes d’e/s partagent la même file d’attente.

## <a name="best-practices-for-bandwidth-templates"></a>Méthodes conseillées pour les modèles de la bande passante

Méthodes conseillées pour votre périphérique StorSimple :

- Configurer des modèles de la bande passante sur votre périphérique pour activer variable la limitation du débit réseau par le périphérique à différents moments de la journée. Ces modèles de bande passante, lorsqu’il est utilisé avec des plannings de sauvegarde peuvent exploiter efficacement la bande passante réseau supplémentaires pour les opérations de nuage pendant les heures creuses.

- Calculer la bande passante réelle requise pour un déploiement particulier en fonction de la taille du déploiement et de l’objectif de temps de récupération nécessaire (RTO).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
