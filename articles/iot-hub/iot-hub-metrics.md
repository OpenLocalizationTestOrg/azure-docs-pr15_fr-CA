<properties
 pageTitle="Mesures de diagnostic IoT concentrateur"
 description="Une vue d’ensemble des mesures d’Azure IoT concentrateur, permettant aux utilisateurs d’évaluer la santé générale de leurs ressources"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-diagnostic-metrics"></a>Introduction aux mesures de Diagnostics

Mesures de diagnostics vous donnent une meilleure données sur l’état des ressources Azure votre abonnement Azure. Mesures permettent d’évaluer l’intégrité globale du service et les périphériques connectés. Les statistiques présentées à l’utilisateur sont importants car ils vous permettent de voir ce qui se passe vos problèmes de causes IoT concentrateur et l’aide sans avoir à contacter le support technique Azure.

Vous pouvez activer les mesures de diagnostics à partir du portail Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Comment faire pour activer les mesures de Diagnostics

1. Créer un concentrateur IoT. Vous trouverez des instructions sur la création d’un concentrateur IoT dans la [Mise en route] [ lnk-get-started] guide.

2. Ouvrez la lame de votre concentrateur IoT. À partir de là, cliquez sur **Diagnostics**.

    ![][1]

3. Configurer vos tests de diagnostic en définissant le statut sur **** et en sélectionnant un compte de stockage Azure pour stocker les données de diagnostic. Vérifier les **mesures**et cliquez sur **Enregistrer**. Notez que le compte de stockage Azure doit être créé à l’avance et que vous sont facturés séparément pour le stockage. Vous pouvez également choisir d’envoyer vos données de tests de diagnostic à un point de terminaison de concentrateurs de l’événement.

    ![][2]

4. Une fois que vous avez configuré les tests de diagnostic, revenir à la lame de concentrateur IoT **vue d’ensemble** . Informations de métriques sont remplies dans la section **analyse** de la lame. En cliquant sur le graphique, l’onglet metrics dans lequel vous pouvez afficher un résumé des informations de métriques pour votre concentrateur IoT et modifier la sélection des mesures figurant dans le graphique s’ouvre. Vous pouvez également configurer des alertes en fonction des valeurs métriques.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Mesures et leur utilisation

IoT concentrateur fournit plusieurs mesures pour vous donner une vue d’ensemble de l’état de santé de votre concentrateur et le nombre total de périphériques connectés. Vous pouvez combiner des informations provenant de plusieurs mesures pour peindre une image plus grande de l’état du concentrateur IoT. Le tableau suivant décrit les mesures que chaque concentrateur IoT assure le suivi et comment chaque mesure se rapporte à l’état global du concentrateur IoT.

| Métrique | Description de la mesure | À quoi sert la métrique |
| ---- | ---- | ---- |
| d2c.telemetry.Ingress.allProtocol | Le nombre de messages envoyés sur tous les périphériques | Vue d’ensemble des données sur l’envoie de message |
| d2c.telemetry.Ingress.Success | Le nombre de tous les messages de réussis dans le concentrateur | Vue d’ensemble d’entrée de message réussie dans le concentrateur |
| c2d.Commands.egress.Complete.Success | Le nombre de tous les messages de commande exécutée par le périphérique de réception sur tous les périphériques | Avec les métriques sur abandon et rejeter, donne un aperçu des taux de réussite global C2D commande |
| c2d.Commands.egress.Abandon.Success | Le nombre de tous les messages avec succès abandonnée par le périphérique de réception sur tous les périphériques | Met en évidence les problèmes potentiels si les messages sont mise en route abandonnées plus souvent que prévu |
| c2d.Commands.egress.Reject.Success | Le nombre de tous les messages correctement rejeté par le périphérique de réception sur tous les périphériques | Met en évidence les problèmes potentiels si les messages sont plus souvent que prévu rejetés |
| devices.totalDevices | La moyenne, min et max du nombre d’unités enregistrées au concentrateur IoT | Le nombre d’unités enregistrées sur le concentrateur |
| devices.connectedDevices.allProtocol | La moyenne, min et max du nombre d’équipements simultanées | Vue d’ensemble du nombre de périphériques connectés au concentrateur |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez vu une vue d’ensemble des mesures de diagnostics, suivez ce lien pour en savoir plus sur la gestion d’Azure IoT Hub :

- [Surveillance des opérations][lnk-monitor]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
