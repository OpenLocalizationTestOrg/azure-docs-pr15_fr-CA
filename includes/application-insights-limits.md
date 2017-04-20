Il existe certaines limites sur le nombre de mesures et les événements par application (c'est-à-dire par clé d’instrumentation). 

Limites dépendent du [niveau de prix](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

**Ressources** | **Limite par défaut** | **Limite maximale**
-------- | ------------- | -------------
Les points de données de la session<sup>1, 2</sup> par mois | un nombre illimité | 
Points de données total par mois de la demande, événement, dépendance, trace, exception et affichage de la page | 5 millions | 50 millions de<sup>3</sup>
Taux de données de [suivi et journaux](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Taux de données [exception](../articles/application-insights/app-insights-asp-net-exceptions.md) | 50 dp/s | 50 dp/s
Taux de données total de la demande, d’événements, de dépendance et de télémétrie d’affichage de page | 200 dp/s | 500 dp/s
Rétention de données brutes pour la [recherche](../articles/application-insights/app-insights-diagnostic-search.md) et [Analytique](../articles/application-insights/app-insights-analytics.md) | 7 derniers jours
Rétention des données agrégées pour [explorer des mesures](../articles/application-insights/app-insights-metrics-explorer.md) | 90 jours
Nombre de nom de [propriété](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longueur du nom de propriété | 150 | 
Longueur de la propriété valeur | 8192 | 
Longueur de message de trace et de l’Exception | 10000 |
Nom de [mesure](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) nombre | 100 |
Longueur du nom de métriques |  150 | 
[Tests de disponibilité](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> un point de données est une valeur métrique individuelle ou un événement, avec les propriétés attachées et des mesures.

<sup>2</sup> une session point de données connecte le début ou la fin d’une session et enregistre l’identité de l’utilisateur.

<sup>3</sup> vous pouvez acheter une capacité supplémentaire au-delà de 50 millions.
 
[Sur les prix et les quotas dans les conseils d’Application](../articles/application-insights/app-insights-pricing.md)
