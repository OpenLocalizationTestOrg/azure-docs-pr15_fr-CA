<properties
   pageTitle="Passerelle de protocole IoT Azure | Microsoft Azure"
   description="Décrit l’utilisation de passerelle de protocole Azure IoT pour étendre les fonctionnalités et la prise en charge du protocole d’Azure IoT concentrateur."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Prise en charge de protocoles supplémentaires pour IoT concentrateur

Azure IoT Hub en mode natif prend en charge la communication via les protocoles HTTP, AMQP et MQTT. Dans certains cas, les périphériques ou les passerelles de champ ne peuvent pas être en mesure d’utiliser un de ces protocoles standard et nécessiteront une adaptation du protocole. Dans ce cas, vous pouvez utiliser une passerelle personnalisée. Une passerelle personnalisée permettent à adaptation de protocole pour les points de terminaison IoT concentrateur par le trafic vers et à partir de IoT concentrateur de pontage. Vous pouvez utiliser la [passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) comme passerelle personnalisée pour permettre l’adaptation du protocole IoT concentrateur.

## <a name="azure-iot-protocol-gateway"></a>Passerelle de protocole IoT Azure

La passerelle de protocole Azure IoT est un framework d’adaptation de protocole qui est destiné à grande échelle, la communication bidirectionnelle périphérique avec IoT concentrateur. La passerelle de protocole est un composant pass-through qui accepte les connexions du périphérique via un protocole spécifique. Elle réduit le trafic vers le concentrateur de IoT sur AMQP 1.0. La passerelle de protocole Azure IoT est disponible sous la forme d’un projet open source software offre une grande souplesse pour l’ajout de la prise en charge pour une variété de protocoles et les versions de protocole.

Vous pouvez déployer la passerelle de protocole dans Azure de manière hautement évolutive à l’aide de rôles worker de Services Cloud Azure. En outre, la passerelle de protocole peut être déployée dans les environnements sur site, telles que des passerelles de champ.

La passerelle de protocole Azure IoT comprend un adaptateur de protocole MQTT qui vous permet de personnaliser le comportement du protocole MQTT, si nécessaire. Étant donné que IoT Hub prend en charge le protocole de v3.1.1 MQTT, n’envisagez l’utilisation de la carte du protocole MQTT si vous avez besoin pour les personnalisations de protocole ou des exigences spécifiques pour des fonctionnalités supplémentaires.

La carte MQTT illustre également le modèle de programmation pour la création d’adaptateurs de protocole pour d’autres protocoles. En outre, le modèle de programmation Azure IoT protocole passerelle vous permet de connecter des composants personnalisés pour des traitements spécialisés tels que l’authentification personnalisée, les transformations de message, compression/décompression ou chiffrement/déchiffrement du trafic entre les périphériques et les IoT concentrateur.

Pour une flexibilité, la passerelle de protocole et l’implémentation de MQTT sont fournis dans un projet open source software. Cela vous permet de vous permettent de personnaliser la mise en oeuvre, le cas échéant.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la passerelle de protocole Azure IoT et comment les utiliser et à déployer dans le cadre de votre solution IoT, voir :

* [Azure référentiel passerelle de protocole IoT sur GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guide du développeur de passerelle du protocole IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Pour plus d’informations sur la planification de votre déploiement IoT concentrateur, voir :

- [Comparer avec les concentrateurs d’événement][lnk-compare]
- [Mise à l’échelle, la haute disponibilité et la reprise après sinistre][lnk-scaling]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
