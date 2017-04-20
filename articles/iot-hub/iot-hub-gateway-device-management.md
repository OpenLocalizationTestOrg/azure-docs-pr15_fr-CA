<properties
 pageTitle="Activer les périphériques gérés derrière une passerelle IoT | Microsoft Azure"
 description="Rubrique de guide à l’aide d’une passerelle IoT créés à l’aide de le Kit de développement de passerelle IoT ainsi que des périphériques gérés par IoT concentrateur."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Activer les périphériques gérés derrière une passerelle IoT

## <a name="basic-device-isolation"></a>Isolation de périphériques de base

Souvent, les organisations utilisent des passerelles de IoT pour augmenter la sécurité globale de leurs solutions IoT. Certains périphériques ont besoin envoyer des données vers le nuage, mais ne sont pas capables de se protéger contre les menaces sur internet. Vous pouvez protéger ces périphériques à partir de threads externes en leur demandant de communiquer avec le monde extérieur, via une passerelle.

La passerelle se trouve sur la frontière entre un environnement sécurisé et internet ouverte. Périphériques de communiquer avec la passerelle et la passerelle transmet les messages le long vers la destination correcte de nuage. La passerelle est DURCIE contre les threads externes, bloque les demandes non autorisées, autorise de trafic entrant autorisé et transmet ce trafic entrant vers le périphérique correct.

![][1]

Vous pouvez également placer des périphériques qui peuvent se protéger derrière une passerelle pour une couche de sécurité supplémentaire. Dans ce scénario, il vous suffit de maintenir la passerelle du système d’exploitation corrigés contre les vulnérabilités les plus récentes, au lieu de la mise à jour du système d’exploitation sur tous les périphériques.

## <a name="isolation-plus-intelligence"></a>Isolation et intelligence

Un routeur de sécurité renforcé est une passerelle suffisante pour simplement isoler les périphériques. Toutefois, les solutions IoT nécessitent souvent qu’une passerelle fournit une intelligence plus que simplement isoler des périphériques. Par exemple, vous souhaiterez gérer vos périphériques à partir du cloud. Vous êtes à même d’utiliser [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), un protocole de gestion de périphérique standard, pour la partie de gestion de cloud de la solution. Toutefois, les périphériques envoient télémétrie à l’aide d’un protocole TCP/IP non activé le protocole. En outre, les périphériques produisent beaucoup de données et que vous ne souhaitez pas télécharger un sous-ensemble filtré de la télémétrie. Vous pouvez créer une solution qui intègre une passerelle IoT capable de traiter avec deux flux distincts de données. La passerelle doit :

-   Comprendre la **télémétrie**, filtrer et ensuite de le télécharger sur le cloud via la passerelle. La passerelle n’est plus un simple routeur qui transfère simplement les données entre le périphérique et le nuage.

-   Simplement l’échange de **données de gestion de périphérique LWM2M** entre les périphériques et le nuage. La passerelle n’a pas besoin de comprendre les données entrantes dans il et ne doit s’assurer que les données sont passées de va-et-vient entre les périphériques et le nuage.

La figure suivante illustre ce scénario :

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>La solution : gestion des périphériques IoT Azure et le Kit de développement de passerelle IoT 

Le public prévisualiser la version de [Gestion des périphériques Azure IoT] [ lnk-device-management] et de la version bêta du [Kit de développement logiciel Azure IoT passerelle] activer ce scénario. La passerelle gère chaque flux de données comme suit :

-   **Télémétrie**: vous pouvez utiliser le Kit de développement de passerelle IoT pour construire un pipeline qui comprend, filtre et envoie les données de télémétrie vers le nuage. Le Kit de développement de passerelle IoT fournit le code qui implémente des parties de ce pipeline pour le développeur. Vous trouverez plus d’informations sur l’architecture du Kit de développement dans le [SDK de passerelle IoT - mise en route] [ lnk-gateway-get-started] didacticiel.

-   **Gestion des périphériques**: gestion des périphériques Azure fournit un client de LWM2M qui s’exécute sur le périphérique ainsi que d’une interface en nuage pour émettre des commandes de gestion sur le périphérique.
    
    Vous ne nécessitez pas de logique particulière sur la passerelle car il n’a pas besoin de traiter les données LWM2M échangées entre le périphérique et votre concentrateur IoT. Vous pouvez activer le partage de connexion internet une fonctionnalité de nombreux systèmes d’exploitation modernes, sur la passerelle pour permettre l’échange de données de LWM2M. Vous pouvez choisir un système d’exploitation approprié pour ce scénario car le Kit de développement de passerelle IoT prend en charge une variété de systèmes d’exploitation. Vous trouverez ici des instructions pour l’activation d’ICS sur [Windows 10] et [Ubuntu], deux des systèmes d’exploitation pris en charge de nombreux.

L’illustration suivante montre l’architecture de haut niveau utilisée pour activer ce scénario à l’aide de la [Gestion des périphériques Azure IoT] [ lnk-device-management] et le [SDK de passerelle IoT Azure].

![][3]

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser le Kit de développement de passerelle IoT, consultez les didacticiels suivants :

- [IoT passerelle SDK - mise en route à l’aide de Linux][lnk-gateway-get-started]
- [Passerelle IoT SDK – envoyer des messages de périphérique-nuage avec un périphérique simulé à l’aide de Linux][lnk-gateway-simulated]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Passerelle de IoT Azure SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md