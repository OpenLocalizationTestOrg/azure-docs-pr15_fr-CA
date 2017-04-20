<properties 
    pageTitle="Comment faire pour surveiller un service en nuage | Microsoft Azure" 
    description="Découvrez comment surveiller les services en nuage à l’aide du portail classique Azure." 
    services="cloud-services" 
    documentationCenter="" 
    authors="rboucher" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="robb"/>


# <a name="how-to-monitor-cloud-services"></a>Comment faire pour surveiller les Services en nuage

[AZURE.INCLUDE [disclaimer](../../includes/disclaimer.md)]

Vous pouvez surveiller `key` des mesures de performances pour vos services en nuage dans Azure portal classique. Vous pouvez définir le niveau de contrôle minimale et détaillée pour chaque rôle de service et que vous pouvez personnaliser le contrôle s’affiche. Les données de surveillance détaillées sont stockées dans un compte de stockage, auquel vous pouvez accéder à l’extérieur du portail. 

Surveillance affiche dans Azure portal classique est hautement configurable. Vous pouvez choisir les mesures que vous souhaitez surveiller dans la liste des mesures sur la page de **l’écran** , et vous pouvez choisir les mesures à tracer dans les graphiques mesures sur le **Moniteur** et le tableau de bord. 

## <a name="concepts"></a>Concepts

Par défaut, le contrôle minimal est fournie pour un nouveau service en nuage à l’aide de compteurs de performance collectées à partir du système d’exploitation hôte pour les instances de rôles (machines virtuelles). Les mesures minimales sont limités à un pourcentage de l’UC, données dans, données sortantes, débit de lecture de disque et le débit d’écriture disque. Configuration de l’analyse détaillée, vous pouvez recevoir des mesures supplémentaires en fonction des données de performances dans les machines virtuelles (instances de rôle). Les métriques détaillées permettent une analyse plus approfondie des problèmes qui se produisent pendant des opérations de l’application.

Par défaut, des données de compteur de performance à partir des instances de rôle sont échantillonnées et transférées à partir de l’instance de rôle à intervalles de 3 minutes. Lorsque vous activez une analyse détaillée, les données de compteur de performances brutes sont regroupées pour chaque instance de rôle et des instances de rôle pour chaque rôle à intervalles de 5 minutes, 1 heure et 12 heures. Les données agrégées sont purgées après 10 jours.

Après avoir activé l’analyse détaillée, les surveillance des données agrégées sont stockées dans les tables de votre compte de stockage. Pour activer l’analyse détaillée pour un rôle, vous devez configurer une chaîne de connexion de diagnostic que les liens vers le compte de stockage. Vous pouvez utiliser des comptes de stockage différents pour les différents rôles.

Notez que l’activation de l’analyse détaillée augmentera vos coûts de stockage liés au stockage des données, le transfert de données et les transactions de stockage. Contrôle minimal ne nécessite pas un compte de stockage. Les données pour les mesures qui sont exposés au niveau minimal de surveillance ne sont pas stockées dans votre compte de stockage, même si vous définissez le niveau de surveillance explicite.


## <a name="how-to-configure-monitoring-for-cloud-services"></a>Comment : configurer l’analyse pour les services en nuage

Utilisez les procédures suivantes pour configurer une surveillance détaillée ou minimale dans Azure portal classique. 

### <a name="before-you-begin"></a>Avant de commencer

- Créer un compte de stockage pour stocker les données d’analyse. Vous pouvez utiliser des comptes de stockage différents pour les différents rôles. Pour plus d’informations, consultez l’aide de **Comptes de stockage**, ou voir [comment créer un compte de stockage](/manage/services/storage/how-to-create-a-storage-account/).

- Activer les Diagnostics Azure pour vos rôles de service cloud. Reportez-vous à la section [Configuration des Diagnostics pour les Services en nuage](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Assurez-vous que la chaîne de connexion de diagnostic est présente dans la configuration du rôle. Vous ne pouvez pas activer le contrôle détaillé jusqu'à ce que vous activez les Diagnostics de Azure et incluez une chaîne de connexion de diagnostic dans la configuration du rôle.   

> [AZURE.NOTE] Projets ciblant Azure SDK 2.5 automatiquement inclure la chaîne de connexion de diagnostic dans le modèle de projet. Pour ces projets, vous devez ajouter manuellement la chaîne de connexion de diagnostic pour la configuration du rôle.

**Pour ajouter manuellement des chaîne de connexion de diagnostic à la configuration de rôle**

1. Ouvrez le projet de Service Cloud dans Visual Studio
2. Cliquez deux fois sur le **rôle** pour ouvrir le Concepteur de rôles et sélectionnez l’onglet **paramètres**
3. Recherchez un paramètre nommé **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Si ce paramètre n’est pas présent puis cliquez sur le bouton **Ajouter un paramètre** à ajouter à la configuration et remplacez le type pour le nouveau paramètre **ConnectionString**
5. Définissez la valeur de chaîne de connexion l’en cliquant sur le bouton **...** . Ceci ouvrira une boîte de dialogue qui vous permet de sélectionner un compte de stockage.

    ![Paramètres de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Pour modifier le niveau de surveillance détaillée ou minimale

1. Dans [Azure portal classique](https://manage.windowsazure.com/), ouvrez la page de **configuration** pour le déploiement de service cloud.

2. Dans un **niveau**, cliquez sur le mode **Verbose** ou **minimale**. 

3. Cliquez sur **Enregistrer**.

Après l’activation de l’analyse détaillée, vous devez commencer à voir les données d’analyse dans le portail classique Azure dans l’heure.

Les données du compteur de performances brutes et agrégée des données d’analyse sont stockées dans le compte de stockage dans les tables qualifié par le code de déploiement pour les rôles. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Comment : recevoir des alertes pour les métriques de service cloud

Vous pouvez recevoir des alertes en fonction de votre service cloud les mesures de surveillance. Dans la page **Des Services de gestion** du portail Azure classique, vous pouvez créer une règle pour déclencher une alerte lorsque la mesure que vous choisissez atteint une valeur que vous spécifiez. Vous pouvez également choisir d’avoir la notification par e-mail lorsque l’alerte est déclenchée. Pour plus d’informations, consultez [Comment : recevoir des Notifications d’alerte et de gérer les règles d’alerte dans Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Comment : ajouter des mesures à la table de mesures

1. Dans [Azure portal classique](http://manage.windowsazure.com/), ouvrez la page de **Moniteur** pour le service en nuage.

    Par défaut, la table de mesures affiche un sous-ensemble des mesures disponibles. L’illustration montre les métriques de commentaires par défaut pour un service en nuage, qui est limité au compteur de performance Memory\Available MBytes, avec des données recueillies au niveau du rôle. **Ajouter des mesures** permet de sélectionner des statistiques agrégées et au niveau du rôle supplémentaires à surveiller dans Azure portal classique.

    ![Affichage détaillé](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Pour ajouter des mesures à la table de mesures :

    1. Cliquez sur **Ajouter des mesures** pour ouvrir **Choisissez les mesures**, illustré ci-dessous.

        La première mesure disponible est développée pour afficher les options disponibles. Pour chaque mesure, l’option supérieure affiche les données de surveillance agrégées pour tous les rôles. En outre, vous pouvez choisir les rôles individuels pour afficher les données de.

        ![Ajouter des mesures](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)

    2. Permet de sélectionner les mesures à afficher

        - Cliquez sur la flèche vers le bas par la valeur métrique pour développer les options d’analyse.
        - Activez la case à cocher pour chaque option de contrôle que vous souhaitez afficher.

        Vous pouvez afficher jusqu'à 50 métrique dans le tableau à métriques.

        > [AZURE.TIP] Dans une analyse détaillée, la liste de mesures peut contenir des dizaines de mesures. Pour afficher une barre de défilement, placez le pointeur sur le côté droit de la boîte de dialogue. Pour filtrer la liste, cliquez sur l’icône de recherche et entrer du texte dans la zone de recherche, comme illustré ci-dessous.
    
        ![Ajouter une recherche de mesures](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)


3. Lorsque vous avez terminé la sélection des mesures, cliquez sur OK (coche).

    Les mesures sélectionnées sont ajoutées à la table de mesures, comme indiqué ci-dessous.

    ![mesures de moniteur](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Pour supprimer une mesure à partir de la table de mesures, cliquez sur la mesure pour le sélectionner, puis cliquez sur **Supprimer la métrique**. (Vous seulement voir **Supprimer une mesure** lorsque vous avez une mesure sélectionnée.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Pour ajouter des mesures personnalisées à la table de mesures

Les **commentaires** niveau de surveillance fournit une liste des mesures par défaut que vous pouvez surveiller sur le portail. De plus, vous pouvez surveiller n’importe quel mesures personnalisées ou les compteurs de performances définis par votre application via le portail.

Les étapes suivantes supposent que vous avez activé **Verbose** niveau de surveillance et que la configuration de votre application pour collecter et transférer des compteurs de performance personnalisés. 

Pour afficher les compteurs de performance personnalisés dans le portail, vous devez mettre à jour la configuration dans un conteneur de contrôle wad :
 
1. Ouvrez le blob de conteneur de contrôle wad dans votre compte de stockage des tests de diagnostic. Pour ce faire, vous pouvez utiliser Visual Studio ou tout autre Explorateur de stockage.

    ![Explorateur de serveurs Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)

2. Recherchez le chemin d’accès de l’objet blob en utilisant le modèle **RoleName/DeploymentId/RoleInstance** pour trouver la configuration de votre instance de rôle. 

    ![Explorateur de stockage de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Téléchargez le fichier de configuration de l’instance du rôle et de mettre à jour pour inclure des compteurs de performance personnalisés. Par exemple, pour surveiller *des écritures disque, octets/s* pour le *lecteur C* , ajoutez ce qui suit sous le nœud de **PerformanceCounters\Subscriptions**

    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Enregistrer les modifications et charger le fichier de configuration à l’emplacement même remplacer le fichier existant dans le blob.
5. Basculer en mode détaillé dans la configuration du portail Azure classique. Si vous êtes déjà en mode détaillé, vous devrez basculer minimale et en clair.
6. Le compteur de performance personnalisé seront désormais disponible dans la boîte de dialogue **Ajouter des mesures** . 

## <a name="how-to-customize-the-metrics-chart"></a>Comment : personnaliser le graphique de mesures

1. Dans le tableau de mesures, sélectionnez jusqu'à 6 mesures à tracer dans le graphique des mesures. Pour sélectionner une mesure, cliquez sur la case à cocher sur son côté gauche. Pour supprimer une mesure à partir du plan de mesures, désactivez sa case à cocher de la table de mesures.

    Lorsque vous sélectionnez des mesures dans le tableau de mesures, les mesures sont ajoutées au graphique de mesures. Sur un affichage étroit, une liste déroulante de **n plus** contienne des en-têtes métriques qui ne tient pas l’affichage.

 
2. Pour basculer entre l’affichage des valeurs relatives (valeur finale uniquement pour chaque mesure) et les valeurs absolues (axe des Y affiché), sélectionnez Relative ou absolue du haut du graphique.

    ![Relatif ou absolu](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Pour modifier la plage de temps la métrique du graphique s’affiche, sélectionnez 1 heure, 24 heures, soit 7 jours en haut du graphique.

    ![Période d’affichage de moniteur](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

    Sur le plan de mesures du tableau de bord, la méthode pour le traçage des mesures est différente. Un ensemble standard de métriques est disponible, et métriques sont ajoutés ou supprimés en sélectionnant l’en-tête métrique.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Pour personnaliser le graphique de mesures sur le tableau de bord

1. Ouvrez le panneau de commandes pour le service en nuage.

2. Ajouter ou supprimer des mesures dans le graphique :

    - Pour tracer une nouvelle mesure, sélectionnez la case à cocher pour la mesure dans les en-têtes du graphique. Sur un affichage étroit, cliquez sur la flèche vers le bas par ** *n*??metrics** pour tracer une mesure de que la zone d’en-tête de graphique ne peut pas afficher.

    - Pour supprimer une mesure qui est tracée sur le graphique, désactivez la case à cocher par son en-tête.

3. Basculer entre **Relative** et **absolue** affiche.

4. Cliquez sur 1 heure, 24 heures, soit 7 jours de données à afficher.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Comment : accès détaillé d’analyse des données à l’extérieur du portail classique Azure

Les données de surveillance détaillées sont stockées dans les tables dans les comptes de stockage que vous spécifiez pour chaque rôle. Pour chaque déploiement de service cloud, six tables sont créées pour le rôle. Deux tables sont créées pour chaque (5 minutes, 1 heure et 12 heures). Une de ces tables stocke les agrégations au niveau du rôle. l’autre table stocke les agrégations pour les instances de rôles. 

Les noms de table ont le format suivant :

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

où :

- *deploymentID* correspond au GUID assigné au déploiement de service cloud

- *aggregation_interval* = 5 M, 1 H ou 12 H

- les agrégations au niveau du rôle = R

- les agrégations pour les instances de rôle = RI

Les tableaux suivants, par exemple, stockez les données de surveillance détaillées agrégées à intervalles de 1 heure :

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
