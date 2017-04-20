<properties
 pageTitle="IoT Azure préconfiguré solutions | Microsoft Azure"
 description="Une description de la IoT Azure préconfiguré solutions et leur architecture, avec des liens vers des ressources supplémentaires."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quelles sont les solutions de la Suite de IoT Azure préconfiguré ?

Les solutions de la Suite de IoT Azure préconfigurés sont mises en œuvre de modèles de solution IoT courants que vous pouvez déployer sur Azure à l’aide de votre abonnement. Vous pouvez utiliser les solutions préconfigurées :

- Comme point de départ pour vos propres solutions IoT.
- Pour obtenir des informations sur les modèles courants dans le développement et la conception de la solution IoT.

Chaque solution préconfigurée est une implémentation complète, de bout en bout qui utilise les périphériques simulés pour générer de télémétrie.

Outre le déploiement et l’exécution des solutions dans Azure, vous pouvez télécharger le code source complet et personnaliser et étendre la solution pour vos besoins IoT.

> [AZURE.NOTE] Pour déployer une des solutions préconfigurées, visitez le site [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L’article [mise en route avec les solutions IoT préconfiguré] [ lnk-getstarted-preconfigured] fournit plus d’informations sur la façon de déployer et d’exécuter une des solutions.

Le tableau suivant montre la correspondance entre les solutions et les spécificités IoT :

| Solution | Réception de données | Identité du périphérique | Commande et contrôle | Règles et Actions | Analytique prédictive |
|------------------------|-----|-----|-----|-----|-----|
| [Surveillance à distance][lnk-getstarted-preconfigured] | Oui | Oui | Oui | Oui | -   |
| [Maintenance PREVENTIVE][lnk-predictive-maintenance] | Oui | Oui | Oui | Oui | Oui |

- *Réception de données*: l’entrée de données à l’échelle vers le nuage.
- *Identité du périphérique*: gestion des identités uniques de tous les périphériques connectés.
- *Commande et contrôle*: envoyer des messages à un périphérique à partir du cloud pour provoquer le périphérique entreprendre une action.
- *Règles et actions*: le back-end de solution utilise des règles pour agir sur les données de périphérique-nuage spécifique.
- *Analytique prédictive*: le back-end de solution s’applique analyse les données du périphérique-nuage pour prévoir quand des actions spécifiques doivent prendre place. Par exemple, analyse de télémétrie de moteur d’avion pour déterminer quand la maintenance de moteur est due.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Surveillance à distance préconfigurées de présentation de la solution

Nous avons choisi décrire la solution préconfigurée d’analyse distante dans cet article car il illustre de nombreux éléments de conception communs qui partagent d’autres solutions.

Le diagramme suivant illustre les éléments clés de la solution de surveillance à distance. Les sections suivantes fournissent plus d’informations sur ces éléments.

![Surveillance à distance préconfiguré d’architecture de la solution][img-remote-monitoring-arch]

## <a name="devices"></a>Périphériques

Lorsque vous déployez la solution préconfigurée surveillance à distance, quatre périphériques simulés sont approvisionnées dans la solution qui simulent un dispositif de refroidissement. Ces simulations de périphériques ont un modèle intégré de température et d’humidité qui émet la télémétrie. Ces simulations de périphériques sont incluses pour illustrer le flux de bout en bout des données par le biais de la solution et pour fournir une source commode de télémétrie et une cible pour les commandes, si vous êtes un développeur principal à l’aide de la solution comme point de départ pour une implémentation personnalisée.

Lorsqu’un périphérique se connecte d’abord à IoT concentrateur dans la solution préconfigurée surveillance à distance, le message d’information de dispositif envoyé au concentrateur IoT énumère la liste des commandes que le périphérique peut répondre à. Dans la solution préconfigurée de contrôle à distance, les commandes sont les suivantes : 

- *Dispositif de commande ping*: le dispositif répond à cette commande par un accusé de réception. Cela est utile pour vérifier que le périphérique est toujours active et à l’écoute.
- *Démarrer la télémétrie*: indique au périphérique de commencer l’envoi de télémétrie.
- *Arrêter la télémétrie*: demande au périphérique pour arrêter l’envoi de télémétrie.
- *Température de définir le Point de modifier*: contrôle les valeurs de télémétrie de température simulé le périphérique envoie. Cela est utile pour tester la logique du back-end.
- *Diagnostic de télémétrie*: contrôle si le périphérique doit envoyer la température externe comme télémétrie.
- *Dispositif de changement d’état*. : définit la propriété de métadonnées d’état du périphérique le périphérique signale. Cela est utile pour tester la logique du back-end.

Vous pouvez ajouter plus de périphériques simulés à la solution que l’émission de la télémétrie même et répondre aux mêmes commandes. 

## <a name="iot-hub"></a>Concentrateur de IoT

Dans cette solution préconfigurée, l’instance IoT concentrateur correspond à la *Passerelle du Cloud* dans une [architecture de solution IoT]type[lnk-what-is-azure-iot].

Un concentrateur IoT reçoit la télémétrie à partir des périphériques à un seul point de terminaison. Un concentrateur IoT gère également les points finals spécifiques au périphérique où chaque périphérique peut récupérer les commandes qui lui sont envoyés.

Le concentrateur IoT rend la télémétrie reçu disponibles par le biais de la télémétrie côté service lire le point de terminaison.

## <a name="azure-stream-analytics"></a>Analytique de flux Azure

La solution préconfigurée utilise trois [Azure flux Analytique] [ lnk-asa] les travaux (ASA) pour filtrer le flux de télémétrie à partir des périphériques :


- *Travail de DeviceInfo* - génère des données à un concentrateur d’événements qui route les messages spécifiques périphérique d’enregistrement, envoyés lorsqu’un périphérique se connecte tout d’abord, ou en réponse à une commande **d’état de périphérique de modification** , dans le Registre de périphérique solution (une base de données DocumentDB). 
- *Travail de télémétrie* - envoie toutes les télémétrie brute au stockage blob Azure d’entreposage frigorifique et calculant des agrégations de télémétrie qui s’affichent dans le tableau de bord de solution.
- Les filtres de *tâche de règles* - le flux de télémétrie pour les valeurs qui dépassent les seuils de règle et renvoie les données à un concentrateur d’événements. Lorsqu’une règle est déclenchée, l’affichage du tableau de bord de portail solution affiche cet événement sous la forme d’une nouvelle ligne dans la table de l’historique d’alarme et déclenche une action basée sur les paramètres définis dans les affichages de règles et Actions dans le portail de la solution.

Dans cette solution préconfigurée, les travaux ASA constituent la **solution de IoT back-end** dans une [architecture de solution IoT]type[lnk-what-is-azure-iot].

## <a name="event-processor"></a>Processeur d’événements

Dans cette solution préconfigurée, le processeur d’événements fait partie de la **solution de IoT back-end** dans une [architecture de solution IoT]type[lnk-what-is-azure-iot].

Les traitements **DeviceInfo** et **règles** ASA envoyer leur sortie à des concentrateurs d’événement pour la remise à d’autres services back-end. La solution utilise un [EventPocessorHost] [ lnk-event-processor] instance, qui s’exécute dans un [WebJob]de[lnk-web-job], pour lire les messages de concentrateurs de ces événements. L' **EventProcessorHost** utilise les données **DeviceInfo** mettre à jour les données de périphérique dans la base de données DocumentDB et utilise les données de **règles** pour appeler la logique app et mettre à jour que les alertes affichent sur le portail de la solution.

## <a name="device-identity-registry-and-documentdb"></a>Registre d’identité de périphérique et de DocumentDB

Chaque concentrateur IoT comprend un [Registre d’identité de périphérique] [ lnk-identity-registry] qui stocke les clés de périphérique. IoT Hub utilise ces informations authentifier les périphériques - un périphérique doit être enregistré et disposer d’une clé valide avant qu’il peut se connecter au concentrateur.

Cette solution stocke plus d’informations sur les périphériques tels que les commandes qu’ils prennent en charge, leur état et d’autres métadonnées. La solution utilise une base de données DocumentDB pour stocker les données de cette solution spécifique au périphérique et le portail de solution extrait des données à partir de cette base de données de DocumentDB pour l’affichage et la modification.

La solution doit également conserver les informations dans le Registre d’identité de périphérique synchronisé avec le contenu de la base de données DocumentDB. L' **EventProcessorHost** utilise les données de tâche d’analytique **DeviceInfo** flux pour gérer la synchronisation.

## <a name="solution-portal"></a>Portail de solution

![Tableau de bord de solution][img-dashboard]

Le portail de solution est une interface utilisateur web qui est déployée dans le nuage en tant que partie de la solution préconfigurée. Il vous permet de :

- Permet d’afficher l’historique de télémétrie et d’alarme dans un tableau de bord.
- Configurer de nouveaux appareils.
- Gérer et surveiller les périphériques.
- Envoyer des commandes vers des périphériques spécifiques.
- Gérer les règles et les actions.

Dans cette solution préconfigurée, le portail de solution fait partie de la **solution de IoT back-end** et de **transformation et la connectivité de l’entreprise** dans [l’architecture de la solution IoT]typique[lnk-what-is-azure-iot].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les architectures de solution IoT, voir [des services de Microsoft Azure IoT : Architecture de référence][lnk-refarch].

Maintenant, vous savez quel une solution préconfigurée est, vous pouvez commencer par le déploiement de la solution de *surveillance à distance* préconfigurées : [mise en route avec les solutions préconfigurées][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md