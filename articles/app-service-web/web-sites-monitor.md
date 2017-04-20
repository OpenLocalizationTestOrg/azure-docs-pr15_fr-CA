<properties
    pageTitle="Applications de moniteur dans le Service d’application Azure"
    description="Apprenez à surveiller les applications de Service d’application Azure via le portail d’Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Comment : surveiller des applications de Service d’application Azure

[Service d’application](http://go.microsoft.com/fwlink/?LinkId=529714) fournit des fonctionnalités de surveillance intégrées dans le [Portail Azure](https://portal.azure.com).
Cela inclut la possibilité de passer en revue **les quotas** et les **mesures** d’une application, ainsi que le plan de Service d’application, configuration des **alertes** et la même **mise à l’échelle** automatiquement en fonction de ces mesures.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Les mesures et les Quotas de présentation

### <a name="quotas"></a>Quotas

Applications hébergées dans le Service d’application sont soumis à certaines *limites* sur les ressources qu’ils peuvent utiliser. Les limites sont définies par le **plan de Service de l’application** associée à l’application.

Si l’application est hébergée dans un plan **libre** ou **partagée** , les limites sur les ressources de que l’application peut utiliser sont définis par les **Quotas**.

Si l’application est hébergée dans une **base**, plan **Standard** ou **Premium** , puis les limites sur les ressources qu’ils peuvent utiliser sont définies par la **taille** (petite, moyenne ou grande) et le **nombre d’instances** (1, 2, 3,...) du **plan de Service de l’application**.

**Quotas** pour les applications **libre** ou **Shared** sont les suivantes :

* **CPU(short)**
   * Quantité d’UC autorisée pour cette application dans un intervalle de 3 minutes. Ce contingent réinitialise toutes les trois minutes.
* **CPU(Day)**
   * Montant total de l’UC autorisée pour cette application en un jour. Ce contingent réinitialise toutes les 24 heures à minuit UTC.
* **Mémoire**
   * Quantité totale de mémoire autorisée pour cette application.
* **Bande passante**
   * Montant total de la bande passante sortante est autorisée pour cette application dans une journée.
   Ce contingent réinitialise toutes les 24 heures à minuit UTC.
* **Système de fichiers**
   * Quantité totale de stockage autorisé.

Le quota uniquement applicable aux applications hébergées sur **basique**, **Standard** et **Premium** plans est un **système de fichiers**.

Plus d’informations sur les quotas spécifiques, les limites et les fonctionnalités disponibles pour les différentes références SKU du Service application se trouve ici : [Limites de Service d’abonnement Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Application des quotas

Si une application dans son utilisation dépasse l' **unité centrale (court)**, **processeur (jour)**, ou le quota de **la bande passante** puis l’application est arrêtés jusqu'à ce que le quota de redéfinitions. Pendant ce temps, toutes les demandes entrantes entraînera un **HTTP 403**.
![][http403]

En cas de dépasse du quota de **mémoire** d’application, l’application sera relancée.

Si le **système de fichiers** de quota est dépassé, une écriture échoue, cela inclut l’écriture dans les journaux.

Les quotas peuvent être augmentés ou supprimés à partir de votre application en mettant à niveau de votre plan de Service de l’application.

### <a name="metrics"></a>Mesures

**Métriques** fournissent des informations sur l’application, ou un comportement du plan de Service de l’application.

Pour une **Application**, les mesures disponibles sont les suivantes :

* **Temps de réponse moyen**
   * Temps moyen nécessaire pour l’application à répondre à des demandes en ms.
* **Jeu de travail de mémoire moyenne**
   * La quantité moyenne de mémoire dans la MIB utilisé par l’application.
* **Temps processeur**
   * La quantité d’UC, en secondes, utilisé par l’application. Pour plus d’informations sur cet métrique, voir : [pourcentage du CPU processeur temps vs](#cpu-time-vs-cpu-percentage)
* **Données dans**
   * La quantité de bande passante entrante utilisée par l’application dans la MIB.
* **Données**
   * Le montant de la bande passante sortante est consommée par l’application dans la MIB.
* **HTTP 2xx**
   * Nombre de requêtes dans un code d’état http > = 200 mais < 300.
* **HTTP 3xx**
   * Nombre de requêtes dans un code d’état http > = 300, mais < 400.
* **HTTP 401**
   * Nombre de requêtes dans le code d’état HTTP 401.
* **HTTP 403**
   * Nombre de requêtes dans le code d’état HTTP 403.
* **HTTP 404**
   * Nombre de requêtes dans le code d’état HTTP 404.
* **HTTP 406**
   * Nombre de requêtes dans le code d’état HTTP 406.
* **4xx de http**
   * Nombre de requêtes dans un code d’état http > = 400 mais < 500.
* **Erreurs de serveur HTTP**
   * Nombre de requêtes dans un code d’état http > = 500 mais < 600.
* **Jeu de travail de mémoire**
   * Quantité de mémoire actuellement utilisée par l’application dans la MIB.
* **Demandes**
   * Nombre total de demandes, quel que soit leur code d’état HTTP qui en résulte.

Pour un **plan de Service d’application**, les mesures disponibles sont les suivantes :

>[AZURE.NOTE] Métriques de plan de Service d’application ne sont disponibles que pour les plans de **base**, **Standard** et **Premium** SKU.

* **Pourcentage de l’UC**
   * Moyenne UC utilisée par toutes les instances du plan.
* **Pourcentage de mémoire**
   * La mémoire de moyenne utilisée par toutes les instances du plan.
* **Données dans**
   * La bande passante entrante moyenne, utilisée par toutes les instances du plan.
* **Données**
   * La moyenne sortant de la bande passante utilisée par toutes les instances du plan.
* **Longueur de file d’attente du disque**
   * Le nombre moyen de lire et d’écrire des requêtes qui ont été mises en attente sur le stockage. Une longueur de file d’attente de disque élevée est une indication d’une application qui peut ralentir en raison des e/s disque excessif.
* **Longueur de la file d’attente de http**
   * Le nombre moyen de demandes HTTP qui devaient se trouvent dans la file d’attente avant d’être remplies. Une longueur de file d’attente de HTTP élevée ou croissante est le symptôme d’un plan sous une charge lourde.

### <a name="cpu-time-vs-cpu-percentage"></a>Pourcentage de vs UC de temps processeur
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Il existe 2 mesures qui reflètent l’utilisation de l’UC. **Temps processeur** et le **pourcentage de l’UC**

**Temps CPU** est utile pour les applications hébergées dans les plans de **libre** ou **Shared** dans la mesure où une de leurs quotas est définie en minutes du processeur utilisés par l’application.

D’autre part, **pourcentage de l’UC** est utile pour les applications hébergées dans les plans de **base**, **standard** et **premium** dans la mesure où ils peuvent être étendus et cette mesure est une bonne indication de l’utilisation par toutes les instances.

##<a name="metrics-granularity-and-retention-policy"></a>Granularité de mesures et de la stratégie de rétention

Métrique pour une application et un plan de service d’application est connectée et agrégée par le service avec les stratégies de rétention granularités suivantes :

 * Les mesures de granularité **minute** sont conservés pendant **48 heures**
 * Mesures de granularité **d’heure** sont conservés pendant **30 jours**
 * Les mesures de granularité **jour** sont conservées pendant **90 jours**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Surveillance des Quotas et des mesures dans le portail Azure.

Vous pouvez consulter l’état des différents **contingents** et **mesures** , qui affectent une application dans [Azure Portal](https://portal.azure.com).

![][quotas]
**Les quotas** peuvent être trouvées sous Paramètres >**Quotas**. L’expérience utilisateur vous permet de consulter : (1) le nom de quotas, (2) de son intervalle de réinitialisation, (3), sa limite actuelle et la valeur (4) en cours.

![][metrics]
**Les mesures** peuvent être accès directement à partir de la blade de ressource. Vous pouvez également personnaliser le graphique par : (1) **cliquez sur** et sélectionnez (2) **Modifier graphique**.
À partir de là, vous pouvez modifier la **plage de temps**de (3), (4) **type de graphique**et (5) **les mesures** à afficher.  

Plus d’informations sur les mesures ici : [métriques de service Moniteur](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertes et échelle
Métrique d’un plan de l’application ou le Service d’application peut être raccordé à des alertes, pour en savoir plus à ce sujet, consultez [notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Les applications de Service d’application hébergées dans base, standard ou premium App les programmes de Service prend en charge **l’échelle automatique**. Ainsi, vous permet de configurer des règles qui surveillent les métriques de plan de Service de l’application et peuvent augmenter ou diminuer le nombre d’instances de fournir des ressources supplémentaires le cas échéant, ou l’enregistrement money lorsque l’application est sur-approvisionner. Vous pouvez apprendre ici à l’échelle automatique : [l’échelle](../monitoring-and-diagnostics/insights-how-to-scale.md) et ici [les méthodes conseillées pour moniteur d’Azure autoscaling](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
