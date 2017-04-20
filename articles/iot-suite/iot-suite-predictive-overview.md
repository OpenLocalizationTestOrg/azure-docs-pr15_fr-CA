<properties
 pageTitle="Solution préconfigurée de maintenance PREVENTIVE | Microsoft Azure"
 description="Description de la solution de gestion prédictive préconfigurée IoT d’Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Présentation de la solution préconfigurée de maintenance PREVENTIVE

La solution de *gestion prédictive* préconfiguré est une des [solutions préconfigurées] [ lnk_preconfigured_solutions] publié dans le cadre de [Microsoft Azure IoT Suite][lnk_iot_suite]. Cette solution s’intègre une collection de télémétrie de périphérique en temps réel avec un modèle de prévision créé à l’aide de la [Formation de Machine Azure][lnk_machine_learning].


Avec la Suite de IoT Azure, entreprises peuvent rapidement et facilement se connecter à surveiller les ressources et analyser les données en temps réel. La solution de gestion prédictive préconfigurée prend les données et utilise des tableaux de bord riches et des visualisations fournissant aux entreprises nouvelle intelligence capable de lecteur de l’efficacité et améliorer les flux de revenus.

## <a name="the-scenario"></a>Le scénario

Fabrikam est une compagnie aérienne régionale qui se concentre sur l’expérience des clients à des prix compétitifs. Une cause de retards de vol est des problèmes de maintenance et de maintenance de moteur d’aéronef est particulièrement complexe. Défaillance du moteur au cours du vol doit être évitée à tout prix, Fabrikam vérifie ses moteurs régulièrement et adhère à un programme de maintenance planifiée. Toutefois, moteurs ne portez toujours la même. Une maintenance inutile est effectuée sur les moteurs. Plus important encore, les problèmes surviennent qui peuvent au sol d’un aéronef jusqu'à ce que la maintenance est exécutée. Cela entraîne des retards coûteux, en particulier si un aéronef est à un emplacement où les techniciens droite ou pièces de rechange ne sont pas disponibles.

Les moteurs d’avions de Fabrikam sont instrumentées avec des capteurs qui surveillent les conditions du moteur au cours du vol. Fabrikam permet de collecter les données de capteurs collectées pendant le vol Azure IoT Suite. Après l’accumulation d’années de fonctionnement du moteur et les données de panne, scientifiques des données de Fabrikam modélisé un moyen de prévoir le restant utile vie (RUL) un moteur d’avion. Ils ont identifié est une corrélation entre les données de quatre des capteurs moteur avec l’usure du moteur qui conduit à une éventuelle défaillance. Tout en continuant de Fabrikam effectuer des inspections régulières pour garantir la sécurité, il peut maintenant utiliser les modèles pour calculer le RUL pour chaque moteur après que chaque vol à l’aide de la télémétrie recueillies à partir de moteurs pendant le vol. Fabrikam peut maintenant prédire les futurs points de défaillance et plan de maintenance et réparation à l’avance.

> [AZURE.NOTE] Le modèle de solution utilise des données d’usure de moteur réel.

La prédiction du point lorsque la maintenance est requise, Fabrikam peut optimiser ses opérations pour réduire les coûts. Les coordinateurs de maintenance fonctionnent avec les planificateurs de planifier la maintenance coïncidant avec un aéronef d’arrêt à un emplacement particulier et garantir suffisamment de temps pour l’aéronef soit hors service sans provoquer de perturbation de la planification. Fabrikam peut planifier les techniciens en conséquence ; afin d’assurer des aéronefs sont traités efficacement sans délai d’attente. Gestionnaires de contrôle de stock reçoivent des plans de maintenance, ils peuvent optimiser leur processus de commande et stock de pièces de rechange. Tout cela permet de Fabrikam pour minimiser le temps de sol d’avion et à réduire les coûts d’exploitation tout en assurant la sécurité des passagers et l’équipage.

Pour comprendre comment [Azure IoT Suite] [ lnk_iot_suite] fournit les fonctionnalités les clients doivent exploiter le potentiel de gestion prédictive, veuillez consulter ce [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Mode de génération de la solution de maintenance PREVENTIVE

La solution s’appuie sur un modèle existant de formation de Machine Azure disponible sous la forme d’un modèle pour afficher ces fonctionnalités à partir de télémétrie de périphérique collectée par les services de IoT Suite. Microsoft a créé un [modèle de régression] [ lnk_regression_model] d’un moteur d’aéronef et publié le modèle terminé, les données<sup>\[1\]</sup>et obtenir des conseils sur la façon d’utiliser le modèle.

La solution de gestion prédictive préconfigurée Azure IoT utilise le modèle de régression créé à partir de ce modèle ; Il est déployé dans votre abonnement Azure et exposé via une API générée automatiquement. La solution comprend un sous-ensemble des données de tests représentant 4 (de 100 total) les moteurs et les 4 (21 total) des flux de données de capteur qui fournissent un résultat précis à partir du modèle formé.

*\[1\] Saxena d’a. et K. Goebel (2008). « Réacteur à double flux moteur dégradation Simulation ensemble de données », référentiel de données pronostic NASA Ames (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), centre de recherche Ames NASA, champ de Moffett, CA*

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur comment Azure IoT permet des scénarios de maintenance PREVENTIVE, lire [capturer la valeur à partir de l’Internet des objets][lnk_capture_value].

Prendre une [procédure pas à pas] [ lnk-predictive-walkthrough] de la gestion prédictive des solution préconfigurée.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Vous pouvez également Explorer les capacités des solutions Suite IoT préconfiguré et certaines autres fonctionnalités :

- [Forum aux questions sur la Suite de IoT][lnk-faq]
- [Sécurité IoT de bas en haut][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
