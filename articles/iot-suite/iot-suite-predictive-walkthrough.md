<properties
 pageTitle="Procédure pas à pas une maintenance PREVENTIVE | Microsoft Azure"
 description="Une procédure pas à pas de la solution de gestion prédictive préconfigurée IoT d’Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Procédure pas à pas de maintenance PREVENTIVE préconfigurée solution

## <a name="introduction"></a>Introduction

La solution de gestion prédictive préconfigurée IoT Suite est une solution de bout en bout pour un scénario d’entreprise qui prévoit le point lors de l’échec est susceptible de se produire. Vous pouvez utiliser cette solution préconfigurée proactive pour les activités telles que l’optimisation de la maintenance. La solution combine des services Azure IoT Suite clés, y compris une [Formation de Machine Azure] [ lnk_machine_learning] espace de travail. Cet espace de travail contient des expériences, basées sur un ensemble de données exemple public, pour prévoir le restant utile vie (RUL) un moteur d’avion. La solution implémente entièrement au scénario d’entreprise IoT comme point de départ pour planifier et implémenter une solution qui répond à vos besoins professionnels spécifiques.

## <a name="logical-architecture"></a>Architecture logique

Le schéma suivant présente les composants logiques de la solution préconfigurée :

![][img-architecture]

Les éléments en bleus sont des services Azure qui sont mis en service dans l’emplacement que vous sélectionnez lorsque vous configurez la solution préconfigurée. Vous pouvez configurer la solution préconfigurée dans la région des États-Unis, Europe du Nord ou Asie de l’est.

Certaines ressources ne sont pas disponibles dans les régions où vous mettez en service la solution préconfigurée. Les éléments oranges dans le diagramme représentent les services Azure mis en service dans la plus proche disponible région (Sud, ouest de l’Europe ou en Asie du Sud-est) compte tenue de la région sélectionnée.

L’élément vert est un périphérique simulé qui représente un moteur d’avion. Vous pouvez en savoir plus sur ces périphériques simulés dans la section suivante.

Les élément s en gris représente des composants qui implémentent les fonctions de *gestion de périphérique* . La version actuelle de la solution de gestion prédictive préconfigurée ne configure pas de ces ressources. Pour en savoir plus sur l’administration des périphériques, reportez-vous à la [solution préconfigurée de surveillance à distance][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Simulations de périphériques

Dans la solution préconfigurée, un périphérique simulé représente un moteur d’avion. La solution est dotée de deux moteurs qui correspondent à un seul avion. Chaque moteur émet des quatre types de télémétrie : capteur 9, 11 de capteur, capteur 14 et 15 du capteur de fournissent les données nécessaires pour le modèle d’apprentissage automatique calculer le reste utile vie (RUL) pour le moteur. Chaque périphérique simulé envoie les messages de télémétrie suivant à IoT concentrateur :

*Nombre de cycles*. Un cycle représente un vol terminé de longueur variable entre 2-10 heures, dans lequel les données de télémétrie sont capturées toutes les demi-heures pendant le vol.

*Télémétrie*. Il existe quatre capteurs qui représentent les attributs du moteur. Les capteurs sont étiquetés générique de capteur 9, 11 du capteur, capteur 14 et 15 du capteur. Ces 4 capteurs représentent la télémétrie suffisante pour obtenir des résultats pertinents à partir du modèle d’apprentissage automatique pour RUL. Ce modèle est créé à partir d’un jeu de données public qui contient des données de capteur de moteur réel. Pour plus d’informations sur comment le modèle a été créé à partir du jeu de données d’origine, consultez le [Modèle de gestion prédictive Cortana Intelligence galerie][lnk-cortana-analytics].

Les simulations de périphériques peuvent gérer les commandes suivantes, envoyées à partir d’un concentrateur IoT :

| Commande | Description |
|---------|-------------|
| StartTelemetry | Contrôle l’état de la simulation.<br/>Démarre le périphérique d’envoi de télémétrie     |
| StopTelemetry  | Contrôle l’état de la simulation.<br/>Arrête le périphérique d’envoi de télémétrie |

IoT concentrateur fournit un accusé de réception de commande dispositif.

## <a name="azure-stream-analytics-job"></a>Tâche de flux de données Analytique Azure

**Travail : télémétrie** fonctionne sur le flux entrant de télémétrie périphérique à l’aide de deux instructions. La première sélectionne tous les télémétrie à partir des périphériques et envoie ces données blob de stockage dans lequel il est affiché dans l’application web. La deuxième instruction calcule les valeurs des capteurs moyen via une fenêtre glissante deux minutes et envoie ces données via le concentrateur d’événements à un **processeur d’événements**.

## <a name="event-processor"></a>Processeur d’événements

Le **processeur d’événements** prend les valeurs moyennes de capteur pour un cycle complet. Il les passes formés de ces valeurs à une API qui expose l’apprentissage Machine modèle pour calculer la RUL pour un moteur.

## <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Pour plus d’informations sur comment le modèle a été créé à partir du jeu de données d’origine, consultez le [Modèle de gestion prédictive Cortana Intelligence galerie][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Commençons la marche

Cette section vous guide à travers les composants de la solution, décrit le cas de l’utilisation prévue et fournit des exemples.

### <a name="predictive-maintenance-dashboard"></a>Tableau de bord de gestion prédictive

Cette page de l’application web utilise les contrôles PowerBI JavaScript (voir le [référentiel d’objets visuels de la PowerBI][lnk-powerbi]) à visualiser :

- Les données de sortie dans les tâches de flux de données Analytique dans le stockage blob.
- Le nombre RUL et cycle par moteur d’avion.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observez le comportement de la solution de cloud

Dans le portail Azure, accédez au groupe de ressources portant le nom de solution que vous avez choisi d’afficher vos ressources de mise en service.

![][img-resource-group]

Lorsque vous configurez la solution préconfigurée, vous recevez un e-mail avec un lien vers l’espace de travail d’apprentissage automatique. Vous pouvez également accéder à l’espace de travail d’apprentissage automatique à partir de [azureiotsuite.com] [ lnk-azureiotsuite] page de votre solution de mise en service lorsqu’il est dans l’état **prêt** .

![][img-machine-learning]

Dans le portail de la solution, vous pouvez voir que l’échantillon est mis en service avec quatre périphériques simulés pour représenter deux aéronefs équipés de moteurs à deux par avion, chacun avec quatre capteurs. Lorsque vous naviguez tout d’abord sur le portail de la solution, la simulation est arrêtée.

![][img-simulation-stopped]

Cliquez sur **simulation de démarrer** pour lancer la simulation dans laquelle vous consultez l’historique du capteur, RUL, Cycles, et l’historique RUL remplir le tableau de bord.

![][img-simulation-running]

Lorsque RUL est inférieur à 160 (un seuil arbitraire choisi pour la démonstration), le portail de solution affiche un symbole d’avertissement en regard de l’affichage RUL et souligne le moteur d’aéronef en jaune. Notez comment les valeurs RUL ont une générale tendance à la baisse globale, mais ont tendance à rebondir haut et le bas. Ce problème provoque du cycle de longueurs variables et la précision du modèle.

![][img-simulation-warning]

La simulation complète prend environ 35 minutes 148 cycles. Le seuil RUL 160 est atteint pour la première fois à environ 5 minutes, et les deux moteurs atteint le seuil à environ 8 minutes.

La simulation s’exécute par le biais du groupe de données complet pour les cycles de 148 et règle sur les valeurs finales RUL et cycle.

Vous pouvez arrêter la simulation à tout moment, mais en cliquant sur la **Simulation de démarrer** relit la simulation à partir du début du groupe de données.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez exécuter la solution de gestion prédictive préconfigurée que vous souhaiterez peut-être modifier, reportez-vous à [aide sur la personnalisation de solutions préconfigurées][lnk-customize].

Le billet de blog [Une Maintenance PREVENTIVE IoT Suite - sous le capot -](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) TechNet fournit des détails supplémentaires sur la solution de gestion prédictive préconfigurée.

Vous pouvez également Explorer les capacités des solutions Suite IoT préconfiguré et certaines autres fonctionnalités :

- [Forum aux questions sur la Suite de IoT][lnk-faq]
- [Sécurité IoT de bas en haut][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
