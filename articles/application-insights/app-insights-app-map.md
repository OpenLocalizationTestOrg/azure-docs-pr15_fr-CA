<properties 
    pageTitle="Mappage d’application dans les perspectives d’Application | Microsoft Azure" 
    description="Une présentation visuelle des dépendances entre les composants de l’application, accompagnés d’indicateurs de performance clés et les alertes." 
    services="application-insights" 
    documentationCenter=""
    authors="SoubhagyaDash" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/15/2016" 
    ms.author="awills"/>
 
# <a name="application-map-in-application-insights"></a>Mappage d’application dans les perspectives de l’Application

Dans [Visual Studio perspectives d’Application](app-insights-overview.md), mappage d’Application est une mise en page visuelle des relations de dépendance des composants de votre application. Chaque composant affiche les indicateurs de performance clés tels que la charge, performances, défaillances et les alertes, pour vous aider à découvrir un composant à l’origine d’une défaillance ou un problème de performances. Vous pouvez cliquer sur par le biais de n’importe quel composant de diagnostic plus détaillées, à la fois à partir de l’aperçu de l’Application, et - si votre application utilise des services Azure - diagnostics de Windows Azure, tels que les recommandations du Gestionnaire de base de données SQL.

Comme les autres graphiques, vous pouvez ajouter un mappage d’application pour le tableau de bord Azure, où il est entièrement fonctionnel. 

## <a name="open-the-application-map"></a>Ouvrez le mappage de l’application

Ouvrez la carte de la lame de présentation pour votre application :

![ouvrir la table de l’application](./media/app-insights-app-map/01.png)

![mappage de l’application](./media/app-insights-app-map/02.png)

La carte indique :

* Tests de disponibilité
* Composant du côté client (surveillé avec le SDK de JavaScript)
* Composant côté serveur
* Dépendances des composants client et serveur

Vous pouvez développer et réduire des groupes de liens de dépendance :

![réduire](./media/app-insights-app-map/03.png)
 
Si vous avez un grand nombre de dépendances d’un type (SQL, etc. HTTP), elles apparaissent groupés. 


![dépendances groupées](./media/app-insights-app-map/03-2.png)
 
 
## <a name="spot-problems"></a>Problèmes de tons directs

Chaque nœud possède des indicateurs de performances pertinents, tels que les taux de charge, de performances et d’échec pour ce composant. 

Icônes d’avertissement mettre en évidence les problèmes possibles. Un avertissement orange signifie que sont en panne dans les requêtes, les vues de page ou des appels de dépendance. Rouge signifie un taux de défaillance plus de 5 %.


![icônes de défaillance](./media/app-insights-app-map/04.png)

 
Active les alertes également afficher des : 


![alertes actives](./media/app-insights-app-map/05.png)
 
Si vous utilisez SQL Azure, il est une icône qui indique quand il existe des recommandations sur la façon dont vous pouvez améliorer les performances. 


![Recommandation Azure](./media/app-insights-app-map/06.png)

Cliquez sur une icône pour obtenir plus de détails :


![recommandation Azure](./media/app-insights-app-map/07.png)
 
 
## <a name="diagnostic-click-through"></a>Cliquez sur diagnostic

Chacun des nœuds sur la carte propose des clics ciblé pour les tests de diagnostic. Les options varient en fonction du type du nœud.

![options de serveur](./media/app-insights-app-map/09.png)

 
Pour les composants qui sont hébergés dans Azure, les options incluent des liens directs vers les.


## <a name="filters-and-time-range"></a>Les filtres et la plage de temps

Par défaut, le mappage résume toutes les données disponibles pour la période choisie. Mais vous pouvez la filtrer afin d’inclure uniquement les noms d’opération spécifique ou les dépendances.

* Nom de l’opération : Ceci inclut les vues de page et les types de demandes côté serveur. Avec cette option, la carte affiche l’indicateur de performance clé sur le nœud du côté client/serveur pour les opérations sélectionnées uniquement. Il montre les dépendances appelés dans le contexte de ces opérations spécifiques.
* Nom de base de dépendance : Ceci inclut les dépendances de côté de navigateur AJAX et côté serveur. Si vous signalez télémétrie dépendance personnalisée avec l’API TrackDependency, ils s’affichent également ici. Vous pouvez sélectionner les dépendances à afficher sur la carte. Veuillez noter que pour l’instant, cela ne filtrera pas les requêtes de côté serveur ou les vues de page côté client.


![Définir les filtres](./media/app-insights-app-map/11.png)

 
 
## <a name="save-filters"></a>Enregistrer les filtres

Pour enregistrer les filtres que vous avez appliqués, accrocher la vue filtrée sur un [tableau de bord](app-insights-dashboards.md).


![Ajouter au tableau de bord](./media/app-insights-app-map/12.png)
 


## <a name="feedback"></a>Commentaires

Veuillez [envoyer vos commentaires par le biais de l’option de commentaires de portail](app-insights-get-dev-support.md).


![Image de MapLink-1](./media/app-insights-app-map/13.png)


