<properties
    pageTitle="Comment faire pour surveiller un compte de stockage | Microsoft Azure"
    description="Apprenez à surveiller un compte de stockage dans Azure via le portail d’Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Surveiller un compte de stockage dans le portail Azure

## <a name="overview"></a>Vue d’ensemble

Vous pouvez surveiller votre compte de stockage à partir du [Portail Azure](https://portal.azure.com). Lorsque vous configurez votre compte de stockage pour la surveillance via le portail, le stockage Azure utilise [Analytique de stockage](http://msdn.microsoft.com/library/azure/hh343270.aspx) pour suivre les mesures de votre compte et enregistre les données de la demande.

> [AZURE.NOTE]Les coûts supplémentaires associés à examiner des données de surveillance dans le [Portail Azure](https://portal.azure.com). Pour plus d’informations, reportez-vous à la section <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Analytique de stockage et de la facturation</a>. <br />

> Stockage de fichier Azure prend en charge des métriques de stockage Analytique actuellement, mais ne prend pas en charge l’enregistrement. Vous pouvez activer des métriques de stockage Azure via le [Portail Azure](https://portal.azure.com).

> Des comptes de stockage avec un type de réplication de stockage redondant Zone (ZRS) n’ont pas les métriques ou la fonction de journalisation pour l’instant. 

> Pour obtenir un guide détaillé sur l’utilisation de stockage Analytique et autres outils pour identifier, de diagnostiquer et de résoudre les problèmes liés au stockage d’Azure, voir [moniteur, diagnostiquer et de résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Comment : configurer l’analyse pour un compte de stockage

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **stockage**, puis cliquez sur le nom de compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **configurer**, puis faites défiler les paramètres de **surveillance** pour les blob, table et les services de file d’attente.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. Dans le **contrôle**, définissez le niveau de contrôle et de la stratégie de rétention des données pour chaque service :

    -  Pour définir le niveau de surveillance, sélectionnez une des opérations suivantes :

      **Minimal** - collecte des mesures telles que les pourcentages d’ENTREE/sortie, de disponibilité, de latence et de réussite qui sont regroupées pour le blob, table et les services de file d’attente.

      **Verbose** - outre les mesures minimales, collecte le même ensemble de mesures pour chaque opération de stockage dans l’API de Service de stockage Azure. Mesures détaillées permettent une analyse plus approfondie des problèmes qui se produisent pendant des opérations de l’application.

      **Off** - désactive le suivi. Données de surveillance existant est conservé jusqu'à la fin de la période de rétention.

- Pour définir la stratégie de rétention de données, de la **rétention (en jours)**, tapez le nombre de jours de données à conserver de 1 à 365 jours. Si vous ne souhaitez pas définir une stratégie de rétention, entrez zéro. S’il n’existe aucune stratégie de rétention, c’est à vous de supprimer les données d’analyse. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée pendant laquelle vous souhaitez conserver les données d’analytique de stockage pour votre compte afin que les données d’analytique anciens et inutilisés peuvent être supprimées par le système sans coût supplémentaire.

4. Lorsque vous avez terminé la configuration de l’analyse, cliquez sur **Enregistrer**.

Vous devez commencer à voir analyse des données sur le tableau de bord et la page **Moniteur** après une heure environ.

Avant de configurer l’analyse pour un compte de stockage, aucune des données d’analyse ne collectées et les graphiques de mesures sur le tableau de bord et **l’Analyseur de** page sont vides.

Après avoir défini les niveaux de contrôle et les stratégies de rétention, vous pouvez choisir parmi les mesures disponibles pour surveiller dans [Azure Portal](https://portal.azure.com)et les mesures à tracer des graphiques de mesures. Un jeu par défaut de mesures s’affiche à chaque niveau de surveillance. **Ajouter des mesures** permet d’ajouter ou de supprimer des mesures dans la liste mesures.

Les mesures sont stockées dans le compte de stockage dans quatre tables nommées $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue et $MetricsCapacityBlob. Pour plus d’informations, consultez [à propos des indicateurs de stockage Analytique](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Comment : personnaliser le tableau de bord de surveillance

Sur le tableau de bord, vous pouvez choisir jusqu'à six mesures pour tracer dans le graphique de métriques de neuf mesures disponibles. Pour chaque service (blob, table et file d’attente), les mesures de disponibilité, le pourcentage de réussite et nombre Total de demandes sont disponibles. Les mesures disponibles dans le tableau de bord sont les mêmes pour la surveillance de minimum ou de commentaires.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Pour modifier les mesures qui sont tracées sur le graphique, vous pouvez prendre une des actions suivantes :

    - Pour ajouter une mesure pour le graphique, cliquez sur la case à cocher couleur en regard de l’en-tête de métrique de la table sous le graphique.

    - Pour masquer une mesure qui est tracée sur le graphique, désactivez la case à cocher couleur en regard de l’en-tête métrique.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Par défaut, le graphique montre les tendances, afficher uniquement la valeur actuelle de chaque mesure (l’option par **rapport** à la partie supérieure du graphique). Pour afficher un axe Y afin de voir les valeurs absolues, sélectionnez **absolu**.

4. Pour modifier la plage de temps la métrique du graphique s’affiche, sélectionnez 6 heures, 24 heures, soit 7 jours en haut du graphique.


## <a name="how-to-customize-the-monitor-page"></a>Comment : personnaliser la page Moniteur

Sur la page de **l’écran** , vous pouvez afficher l’ensemble des mesures pour votre compte de stockage.

- Si votre compte de stockage a un minimum de surveillance configuré, les mesures d’ENTREE/sortie, la disponibilité, la latence et les pourcentages de réussite sont regroupées depuis le blob, table et les services de file d’attente.

- Si votre compte de stockage a configuré de surveillance détaillée, les mesures sont disponibles à une résolution plus fine des opérations de stockage en plus les agrégats de niveau de service.

Utilisez les procédures suivantes pour choisir les mesures de stockage à afficher dans les graphiques de mesures et d’une table qui s’affichent sur la page de **l’écran** . Ces paramètres n’affectent pas la collection, l’agrégation et le stockage des données dans le compte de stockage de surveillance.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Comment : ajouter des mesures à la table de mesures


1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur le **Moniteur**.

    **L’Analyseur de** page s’ouvre. Par défaut, le tableau à métriques affiche un sous-ensemble des mesures qui sont disponibles pour l’analyse. L’illustration montre l’affichage du moniteur par défaut pour un compte de stockage grâce à une surveillance détaillée configuré pour trois services. **Ajouter des mesures** permet de sélectionnez les mesures que vous souhaitez surveiller à partir de toutes les mesures disponibles.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Prendre en compte les coûts lorsque vous sélectionnez les mesures. Il y a des transactions et des coûts de sortie associés à l’actualisation de surveillance affiche. Pour plus d’informations, reportez-vous à la section [Analytique de stockage et de la facturation](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Cliquez sur **Ajouter des mesures**.

    Les mesures d’agrégation qui sont disponibles dans surveillance minimale sont en haut de la liste. Si la case à cocher est activée, la mesure s’affiche dans la liste de mesures.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Pointez sur le côté droit de la boîte de dialogue pour afficher une barre de défilement que vous pouvez faire glisser pour défiler des mesures supplémentaires dans l’affichage.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Cliquez sur la flèche vers le bas par une métrique permettant de développer une liste d’opérations de que la mesure est étendue pour inclure. Sélectionnez chaque opération que vous souhaitez afficher dans le tableau de mesures dans le [Portail Azure](https://portal.azure.com).

    Dans l’illustration suivante, la métrique de pourcentage d’erreur d’autorisation a été développée.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Après avoir sélectionné la métrique pour tous les services, cliquez sur OK (coche) pour mettre à jour de la configuration de l’analyse. Les mesures sélectionnées sont ajoutées à la table de mesures.

7. Pour supprimer une mesure à partir de la table, cliquez sur la mesure pour le sélectionner, puis cliquez sur **Supprimer la mesure**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Comment : personnaliser le graphique de mesures sur la page de moniteur

1. Sur la page de **Moniteur** pour le compte de stockage, dans le tableau à métriques, sélectionnez jusqu'à 6 mesures à tracer dans le graphique des mesures. Pour sélectionner une mesure, cliquez sur la case à cocher sur son côté gauche. Pour supprimer une mesure à partir du graphique, désactivez la case à cocher.

2. Pour changer le graphique entre (valeur finale affichée uniquement) de valeurs relatives et absolue (affichage de l’axe Y), sélectionnez **Relative** ou **absolue** du haut du graphique.

3.  Pour modifier le temps aller les mesures de graphique affiche, sélectionnez **6 heures**, **24 heures**ou **7 jours** , en haut du graphique.



## <a name="how-to-configure-logging"></a>Comment : configurer la journalisation

Pour chacun des services de stockage disponibles avec votre compte de stockage (blob, table et file d’attente), vous pouvez enregistrer des journaux de diagnostics pour des requêtes de lecture, d’écrire des requêtes et/ou supprimer les demandes et pouvez de définir la stratégie de rétention des données pour chacun des services.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **configurer**, puis utilisez la flèche bas du clavier pour faire défiler vers **l’enregistrement**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Pour chaque service (blob, table et file d’attente), vous devez configurer les éléments suivants :

    - Les types de demande à se connecter : supprimer les demandes, écrire des requêtes et des requêtes de lecture.

    - Le nombre de jours de conservation des données enregistrées. Entrez zéro est si vous ne souhaitez pas définir une stratégie de rétention. Si vous ne définissez pas une stratégie de rétention, c’est à vous de supprimer les journaux.

4. Cliquez sur **Enregistrer**.

Les journaux de diagnostic sont enregistrés dans un conteneur d’objet blob nommé $logs dans votre compte de stockage. Pour plus d’informations sur l’accès du conteneur $logs, reportez-vous à la section [Enregistrement Analytique stockage](http://msdn.microsoft.com/library/azure/hh343262.aspx).
