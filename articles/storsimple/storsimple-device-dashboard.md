<properties
   pageTitle="Utilisez le panneau de commandes de périphérique StorSimple Manager | Microsoft Azure"
   description="Décrit le tableau de bord périphérique de service Gestionnaire de StorSimple et comment l’utiliser pour afficher des indicateurs de stockage et les initiateurs connectés et trouver le numéro de série et le nom qualifié."
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

# <a name="use-the-storsimple-manager-device-dashboard"></a>Utilisez le panneau de commandes de périphérique StorSimple Manager

## <a name="overview"></a>Vue d’ensemble

Le tableau de bord de périphérique StorSimple Manager vous donne une vue d’ensemble des informations pour un périphérique spécifique de StorSimple, à la différence du tableau de bord de service, qui fournit des informations sur tous les périphériques inclus dans votre solution Microsoft Azure StorSimple.

![Page de tableau de bord de périphérique](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Le tableau de bord contient les informations suivantes :

- **Zone de graphique** , vous pouvez voir les mesures de stockage appropriées dans la zone de graphique en haut du tableau de bord. Dans ce graphique, vous pouvez afficher des métriques pour le stockage principal total (la quantité de données écrites par les hôtes pour votre périphérique) et le stockage en nuage total consommé par votre appareil sur une période donnée.

     Dans ce contexte, fait référence à la quantité de données écrites par l’hôte de *stockage principal* et peuvent être ventilés par type de volume : *stockage hiérarchisé principal* inclut à la fois les données stockées localement et hiérarchisé des données dans le nuage ; *principal localement épinglé stockage* inclut uniquement les données stockées localement. Le *stockage en nuage*, est d’autre part, une mesure de la quantité totale de données stockées dans le nuage. Cela inclut les sauvegardes et hiérarchisée des données. Notez que les données stockées dans le nuage sont dédupliquées et compressées, alors que le stockage principal indique la quantité de stockage utilisée avant que les données sont dédupliquées et compressées. (Vous pouvez comparer ces deux valeurs pour avoir une idée du taux de compression). Pour les deux principaux et de stockage en nuage, les montants affichés dépend de la fréquence de suivi que vous configurez. Par exemple, si vous choisissez une fréquence d’une semaine, le graphique affiche données pour chaque jour de la semaine précédente.

     Vous pouvez configurer le graphique comme suit :

     - Pour voir la quantité de stockage cloud consommé au fil du temps, sélectionnez l’option **Utilisée de stockage CLOUD** . Pour voir le stockage total qui a été écrite par l’hôte, sélectionnez les options de **Principal hiérarchisé stockage utilisé** et **principal localement ÉPINGLÉ stockage** . Dans l’illustration, les deux options sont sélectionnées ; Par conséquent, le graphique affiche les montants de stockage pour le nuage et de stockage principal. Notez que tout stockage principal utilisé avant d’installer la mise à jour 2 est représenté par la ligne **Primaire hiérarchisé le stockage utilisé** .
     - Pour spécifier une période de temps 1 semaine, 1 mois, 3 mois ou 1 an, utilisez le menu déroulant en haut à droite du graphique. Notez que le graphique de niveau supérieur est actualisé qu’une seule fois par jour et reflète donc les totaux du jour précédent.

     Pour plus d’informations, voir [utiliser le service de gestionnaire de StorSimple pour analyser votre périphérique StorSimple](storsimple-monitor-device.md).

- **Vue d’ensemble de l’utilisation** – dans la zone de **Présentation de l’utilisation** , vous pouvez voir la quantité de stockage utilisé, la quantité de stockage mis en service et la capacité de stockage maximale pour votre périphérique. En comparant ces numéros de l’utilisation de la quantité maximale de stockage disponible, vous pouvez voir en un coup de œil si vous avez besoin d’espace de stockage supplémentaire. Notez que cette vue d’ensemble de mise à jour toutes les 15 minutes et, en raison de la différence de la fréquence de mise à jour, peut afficher des nombres différents de ceux indiqués dans la zone de graphique ci-dessus, qui est mis à jour quotidiennement. Pour plus d’informations, voir [utiliser le service de gestionnaire de StorSimple pour analyser votre périphérique StorSimple](storsimple-monitor-device.md).


- **Alertes** – la zone **alertes** contient une vue d’ensemble des alertes pour votre périphérique. Alertes sont classées par degré de gravité et fourni un décompte du nombre d’alertes pour chaque niveau de gravité. Cliquez sur la gravité d’alerte pour ouvrir une vue étendue de l’onglet alertes pour afficher uniquement les alertes de ce niveau de gravité pour ce périphérique.

- **Tâches** – le module **projets** vous montre le résultat de l’activité de travail récente. Cela peut vous assurer que le système fonctionne comme prévu, ou il peut vous informer que vous devez prendre des mesures correctives. Pour plus d’informations sur les tâches récemment terminées, cliquez sur **tâches a réussi au cours des 24 dernières heures**.

- La zone de **coup de œil** sur la droite du tableau de bord fournit des informations utiles telles que le modèle de périphérique, numéro de série, état, description et nombre de volumes.

Vous pouvez également configurer le basculement sur incident et afficher des initiateurs connectés à partir du tableau de bord du périphérique.

Les tâches courantes qui peuvent être effectuées sur cette page sont les suivants :

- Afficher des initiateurs connectés

- Rechercher le numéro de série du périphérique

- Trouver la cible du dispositif IQN

## <a name="view-connected-initiators"></a>Afficher des initiateurs connectés

Vous pouvez afficher les initiateurs iSCSI qui sont connectés à votre périphérique en cliquant sur le lien **Afficher connectés initiateurs** dans la zone **Aperçu rapide** de votre tableau de bord du périphérique. Cette page fournit une liste sous forme de tableau les initiateurs connectés correctement à votre périphérique. Pour chaque initiateur, vous pouvez voir :

- L’iSCSI nom qualifié (IQN) de l’initiateur connecté.

- Le nom de l’enregistrement de contrôle d’accès (ACR) qui permet à cet initiateur connecté.

- L’adresse IP de l’initiateur connecté.

- Les interfaces de réseau que l’initiateur est connecté à votre périphérique de stockage. Celles-ci peuvent varier de données de 0 à 5 des données.

- Tous les volumes qui l’initiateur connecté est autorisé à accéder en fonction de la configuration actuelle de blocage.

Si vous consultez les initiateurs inattendues dans cette liste ou que vous ne voyez pas ceux attendus, passez en revue votre configuration de blocage. Un maximum de 512 initiateurs peut se connecter à votre périphérique.

## <a name="find-the-device-serial-number"></a>Rechercher le numéro de série du périphérique

Vous devrez peut-être le numéro de série du périphérique lorsque vous configurez Microsoft MPIO (Multipath i / o) sur le périphérique. Effectuez les opérations suivantes pour rechercher le numéro de série du périphérique.

#### <a name="to-find-the-device-serial-number"></a>Pour trouver le numéro de série du périphérique

1. Accédez à des **périphériques** > **tableau de bord**.

2. Dans le volet de droit du tableau de bord, localisez la zone **d’Aperçu rapide** .

3. Faites défiler vers le bas et recherchez le numéro de série.

## <a name="find-the-device-target-iqn"></a>Trouver la cible du dispositif IQN

Vous devrez peut-être la cible de périphérique IQN lorsque vous configurez le CHAP Challenge Handshake Authentication Protocol () sur le périphérique StorSimple. Procédez comme suit pour trouver la cible du dispositif IQN.

### <a name="to-find-the-device-target-iqn"></a>Pour trouver la cible du dispositif IQN

1. Accédez à des **périphériques** > **tableau de bord**.

1. Dans le volet de droit du tableau de bord, localisez la zone **d’Aperçu rapide** .

1. Défiler vers le bas et recherchez la nom qualifié de la cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le [tableau de bord de service Gestionnaire de StorSimple](storsimple-service-dashboard.md).
- En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
