> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

Cette procédure pas à pas de l' [exemple de nuage de périphérique simulé télécharger] montre comment utiliser le [Kit de développement logiciel Azure IoT passerelle] [ lnk-sdk] d’envoyer des périphérique-nuage télémétrie à IoT concentrateur de périphériques simulés.

Cette procédure pas à pas couvre :

1. **Architecture**: architecture des informations importantes l’exemple simulée de télécharger de nuage périphérique.

2. **Générer et exécuter**: les étapes nécessaires pour générer et exécuter l’exemple.

## <a name="architecture"></a>Architecture

L’exemple de nuage de périphérique simulé télécharger montre comment utiliser le SDK pour créer une passerelle qui envoie de télémétrie de simulations de périphériques à un concentrateur IoT. Les simulations de périphériques ne peut pas se connecter directement au concentrateur de IoT car :

- Les périphériques n’utilisent pas un protocole de communication compris par IoT concentrateur.
- Les périphériques ne sont pas suffisamment intelligents pour mémoriser l’identité attribuée par IoT concentrateur.

La passerelle résout ces problèmes pour les périphériques simulés dans l’une des manières suivantes :

- La passerelle comprend le protocole utilisé par les périphériques simulés, reçoit de télémétrie de périphérique-nuage à partir des périphériques et transfère les messages au concentrateur IoT à l’aide d’un protocole compris par le concentrateur.
- La passerelle stocke les identités IoT Hub pour les simulations de périphériques et agit comme un proxy lorsque les simulations de périphériques envoient des messages à IoT concentrateur.

Le diagramme suivant illustre les principaux composants de l’échantillon, y compris les modules de passerelle :

![][1]


> [AZURE.NOTE] Les modules ne passent pas des messages directement entre eux. Les modules de publier des messages à un courtier interne qui remet les messages aux autres modules à l’aide d’un mécanisme d’abonnement comme indiqué dans le schéma ci-dessous. Pour plus d’informations, consultez [mise en route avec le Kit de développement de passerelle IoT][lnk-gw-getstarted].

### <a name="protocol-ingestion-module"></a>Module de réception du protocole

Ce module est le point de départ pour obtenir des données à partir de périphériques par le biais de la passerelle et dans le nuage. Dans l’exemple, le module effectue quatre tâches :

1.  Il crée des données de température simulé.
    
    Remarque : Si vous utilisez des périphériques réels, le module doit lire les données à partir de ces périphériques physiques.

2.  Il place les données de simulation de température dans le contenu d’un message.

3.  Il ajoute une propriété avec une adresse MAC fictive au message qui contient les données de température simulé.

4.  Il rend le message disponible au module suivant dans la chaîne.

> [AZURE.NOTE] Le module appelé **ingestion du protocole X** dans le diagramme ci-dessus est appelé **simulée de périphérique** dans le code source.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; module de code de concentrateur IoT

Ce module analyse les messages qui comprennent une propriété qui contient l’adresse MAC, ajouté par le module de réception de protocole, du périphérique simulé. Si le module détecte une telle propriété, il ajoute une autre propriété avec une clé de périphérique IoT concentrateur au message et ensuite le message rend disponibles au module suivant dans la chaîne. Voici comment l’exemple associe les identités des appareils IoT Hub avec les périphériques simulés. Le développeur définit le mappage entre les adresses MAC et les identités IoT concentrateur manuellement dans le cadre de la configuration du module. 

> [AZURE.NOTE]  Cet exemple utilise une adresse MAC sous la forme d’un identificateur de périphérique unique et il est en corrélation avec une identité de périphérique IoT concentrateur. Toutefois, vous pouvez écrire votre propre module qui utilise un autre identificateur unique. Par exemple, vous devrez périphériques avec numéros de série uniques ou les données de télémétrie qui a un nom de périphérique unique intégré que vous pouvez utiliser pour déterminer l’identité du périphérique IoT concentrateur.

### <a name="iot-hub-communication-module"></a>Module de communication IoT concentrateur

Ce module prend les messages avec un concentrateur IoT identité attribuée par le module précédent de cette unité et envoie le contenu du message au concentrateur IoT à l’aide de HTTP. HTTP est un des trois protocoles compris par IoT concentrateur.

Au lieu d’ouvrir une connexion au concentrateur de IoT pour chaque périphérique simulé, ce module s’ouvre une connexion HTTP à partir de la passerelle vers le concentrateur IoT et multiplexe les connexions à partir de tous les périphériques simulés sur cette connexion. Cela permet une passerelle unique à beaucoup plus de périphériques, simulées ou autres, que ne serait possible si elle ouvre une connexion unique pour tous les périphériques de se connecter.

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Exemple de téléchargement de nuage périphérique simulé]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md