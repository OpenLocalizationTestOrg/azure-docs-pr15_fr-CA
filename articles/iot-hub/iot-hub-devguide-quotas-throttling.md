<properties
 pageTitle="Guide du développeur - quotas et des limitations | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - description des quotas qui s’appliquent à IoT Hub et comportement de régulation attendu"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="reference---quotas-and-throttling"></a>Référence - des Quotas et des limitations

## <a name="quotas-and-throttling"></a>Quotas et limitation

Chaque abonnement Azure peut avoir au plus 10 moyeux IoT.

Chaque concentrateur IoT est mis en service avec un certain nombre d’unités dans un point de stock spécifique (pour plus d’informations, reportez-vous à la section [Tarification du concentrateur Azure IoT][lnk-pricing]). Permet de déterminer le quota quotidien maximal des messages que vous pouvez envoyer les références SKU et le nombre d’unités.

Le point de stock détermine également les limites de régulation IoT concentrateur applique pour toutes les opérations.

## <a name="operation-throttles"></a>Limitations de l’opération

Limitations de l’opération sont les limitations de vitesse qui sont appliquées dans les plages de minute et qui sont destinées à éviter des abus. IoT concentrateur essaie d’éviter le renvoi d’erreurs chaque fois que possible, mais il commence à retourner des exceptions si l’accélérateur est violée trop longtemps.

Voici la liste des limitations appliquées. Valeurs font référence à un concentrateur individuel.

| Manette des gaz | Valeur de par-hub |
| -------- | ------------- |
| Les opérations de Registre identité (créer, récupérer, répertorier, mettre à jour, supprimer) | min/5000/unité (pour S3) <br/> min/100/unité (pour S1 et S2). |
| Connexions du périphérique | s/6000/unité (pour S3), 120/s/unité (pour (S2), s/12/unité (pour S1). <br/>Minimum de 100 par seconde. <br/> Par exemple, deux unités de S1 sont 2\*12 = 24/s, mais que vous disposez au moins 100/s entre les unités. Avec neuf unités de S1, vous avez 108/s (9\*12) sur vos unités. |
| Périphérique-nuage envoie | s/6000/unité (pour S3), 120/s/unité (pour (S2), s/12/unité (pour S1). <br/>Minimum de 100 par seconde. <br/> Par exemple, deux unités de S1 sont 2\*12 = 24/s, mais que vous disposez au moins 100/s entre les unités. Avec neuf unités de S1, vous avez 108/s (9\*12) sur vos unités. |
| Nuage-DISPOSITIF envoie | min/5000/unité (pour S3), 100/min/unité (pour S1 et S2). |
| Nuage-DISPOSITIF reçoit | min/50000/unité (pour S3), 1000/min/unité (pour S1 et S2). |
| Opérations de téléchargement de fichier | téléchargement de fichiers de 5000 notifications/min/unité (pour S3), téléchargement de fichier 100 notifications/min/unité (pour S1 et S2). <br/> 10000 SAS URI peut être à la fois pour un compte de stockage Azure.<br/> URI SAS 10/périphérique peut être à la fois des. | 

Il est important de préciser que la limitation des *connexions du périphérique* régit la fréquence à laquelle les nouvelles connexions de périphérique peuvent être établies avec un concentrateur IoT et pas le nombre maximal de périphériques connectés simultanément. L’accélérateur dépend du nombre d’unités qui sont mis en service pour le concentrateur.

Par exemple, si vous achetez une seule unité S1, vous obtenez un accélérateur de 100 connexions par seconde. Cela signifie que pour connecter des périphériques de 100 000, il faut au moins 1 000 secondes (environ 16 minutes). Toutefois, vous pouvez avoir autant de périphériques connectés simultanément que vous avez enregistrés dans le Registre d’identité de périphérique des périphériques.

Pour une discussion détaillée du concentrateur de IoT limitation de comportement, consultez le blog [concentrateur IoT la limitation et vous][lnk-throttle-blog].

>[AZURE.NOTE] À un moment donné, il est possible d’augmenter les quotas ou les limites de l’accélérateur en augmentant le nombre d’unités mis en service dans un concentrateur IoT.

>[AZURE.IMPORTANT] Les opérations de Registre identité sont destinées au moment de l’exécution dans la gestion des périphériques et des scénarios de mise en service. Lecture ou mise à jour d’un grand nombre d’identités de périphérique est pris en charge via [l’importation et l’exportation des travaux][lnk-importexport].

## <a name="next-steps"></a>Étapes suivantes

D’autres rubriques de référence dans ce guide de développeur IoT concentrateur incluent :

- [Les points de terminaison IoT concentrateur][lnk-devguide-endpoints]
- [Langage de requête pour jumeaux, des méthodes et des tâches][lnk-devguide-query]
- [Prise en charge de la MQTT de concentrateur IoT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md