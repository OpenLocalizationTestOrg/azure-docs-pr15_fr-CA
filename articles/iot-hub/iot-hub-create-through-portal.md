<properties
     pageTitle="Utilisez le portail Azure pour créer un concentrateur IoT | Microsoft Azure"
     description="Une vue d’ensemble de la façon de créer et de gérer les concentrateurs Azure IoT via le portail Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Créer un concentrateur IoT via le portail Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introduction

Cet article décrit comment faire pour rechercher le service IoT concentrateur dans Azure portal et comment les créer et de gérer les concentrateurs IoT.

## <a name="where-to-find-iot-hubs"></a>Où trouver les concentrateurs IoT

Il existe divers emplacements où vous pouvez trouver les concentrateurs IoT.

1. **+ Nouveau**: **Azure IoT concentrateur** est un service IoT et se trouve dans la catégorie **Internet des objets**, sous **+ Nouveau**, similaires à d’autres services.

2. IoT concentrateurs sont également accessibles par le biais du marché que le service héros sous **l’Internet des objets**.

## <a name="create-an-iot-hub"></a>Créer un concentrateur IoT

Vous pouvez créer un concentrateur IoT à l’aide des méthodes suivantes :

- Création d’un concentrateur IoT via l’option **+ Nouveau** entraîne la lame illustrée dans la capture d’écran suivante. Les étapes de création du concentrateur IoT via cette méthode et sur le marché sont identiques.

- Création d’un concentrateur IoT via le Marketplace : cliquez sur **créer** pour ouvrir une lame est identique à la précédente lame pour l’expérience **+ nouvelle** . Les sections suivantes répertorient les étapes impliquées dans la création d’un concentrateur IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Choisissez le nom du concentrateur IoT

Pour créer un concentrateur IoT, vous devez nommer le concentrateur. Ce nom doit être unique dans les concentrateurs. Aucun duplication des concentrateurs n’est autorisé sur le back-end, il est donc recommandé que ce concentrateur est nommé de manière unique que possible.

### <a name="choose-the-pricing-tier"></a>Choisissez le niveau de tarification

Vous pouvez choisir parmi quatre niveaux : **libre**, **Standard 1** et **2 Standard**et **S3 Standard**. La couche libre permet uniquement de 500 périphériques d’être connectés au concentrateur IoT et jusqu'à 8 000 messages par jour.

**S1 Standard**: IoT concentrateurs S1 edition est conçue pour les solutions IoT ayant un grand nombre de petites quantités de données par dispositif de génération de périphériques. Chaque unité de l’édition de S1 autorise jusqu'à 400 000 messages par jour sur tous les périphériques connectés.

**S2 Standard**: IoT concentrateur S2 edition est conçue pour les solutions IoT dans lequel les périphériques génèrent de grandes quantités de données. Chaque unité de S2 edition permet jusqu'à 6 millions de messages par jour entre tous les périphériques connectés.

**S3 Standard**: IoT concentrateur S3 edition est conçue pour les solutions IoT qui génèrent de grandes quantités de données. Chaque unité de l’édition de S3 permet jusqu'à 300 millions de messages par jour entre tous les périphériques connectés.

![][4]

> [AZURE.NOTE] IoT Hub autorise uniquement un hub disponible par abonnement Azure.

### <a name="iot-hub-units"></a>Unités de concentrateur IoT

Une unité de moyeu IoT inclut un certain nombre de messages par jour. Le nombre total de messages pris en charge pour ce concentrateur est le nombre d’unités, multiplié par le nombre de messages par jour pour cette couche. Par exemple, si vous souhaitez que le concentrateur IoT pour prendre en charge la pénétration de 700 000 messages, vous choisissez deux unités de couche de S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositif pour les partitions de cloud et le groupe de ressources

Vous pouvez modifier le nombre de partitions d’un concentrateur IoT. Partitions par défaut sont définies à 4 ; Toutefois, vous pouvez choisir un autre nombre de partitions à partir d’une liste déroulante.

Pour les groupes de ressources, il est inutile de créer explicitement un groupe de ressources vide. Lors de la création d’une ressource, vous pouvez choisir d’utiliser un groupe de ressources existant ou créer un nouveau groupe de ressources.

![][5]

### <a name="choose-subscriptions"></a>Choisissez les abonnements

Concentrateur de IoT Azure affiche automatiquement la liste des abonnements Azure auquel le compte d’utilisateur est lié. Vous pouvez choisir une des options ici à associer le concentrateur IoT cet abonnement Azure.

### <a name="choose-the-location"></a>Choisissez l’emplacement

L’option emplacement fournit une liste des régions dans lesquelles IoT concentrateur est proposé. IoT Hub n’est disponible pour le déploiement dans les emplacements suivants : nord-est de l’Australie, Australie sud-est, Asie de l’est, Asie du Sud-est, Europe du Nord, Europe occidentale, orientale du Japon, du Japon ouest, nous East, West nous.

### <a name="create-the-iot-hub"></a>Créer le concentrateur IoT

Lorsque toutes les étapes précédentes sont terminées, le concentrateur IoT est prêt à créer. Cliquez sur **créer** pour lancer le processus principal de la création de ce concentrateur IoT avec les options spécifiques et à le déployer à l’emplacement spécifié.

Il peut prendre quelques minutes pour le moyeu IoT doit être créé comme le temps requis pour le déploiement de back-end dans les serveurs de l’emplacement approprié.

## <a name="change-the-settings-of-the-iot-hub"></a>Modifier les paramètres du concentrateur IoT

Vous pouvez modifier les paramètres d’un concentrateur IoT existant après sa création à partir de la blade IoT concentrateur.

![][8]

**Stratégies d’accès partagés**: ces stratégies définissent les autorisations pour les services et les périphériques pour se connecter à un concentrateur de IoT. Vous pouvez accéder à ces stratégies en cliquant sur les **Stratégies d’accès partagé** sous **Général**. Dans cette carte, vous pouvez modifier les stratégies existantes ou ajouter une nouvelle stratégie.

### <a name="create-a-policy"></a>Créer une stratégie

- Cliquez sur **Ajouter** pour ouvrir une lame. Ici, vous pouvez saisir le nouveau nom de stratégie et les autorisations que vous souhaitez associer à cette stratégie, comme illustré dans la figure suivante.

    Il existe plusieurs autorisations qui peuvent être associées à ces stratégies partagées. Les tout d’abord deux stratégies, **Registre de lecture** et **l’écriture de Registre**, les autorisations de lecture et les droits d’accès en écriture à la banque d’identités de périphériques ou dans le Registre de l’identité. L’option écriture automatiquement choisit l’option de lecture.

    La stratégie de **Service se connecter** accorde l’autorisation d’accéder les points de terminaison côté nuage tel que le groupe de consommateurs pour les services de connexion au concentrateur IoT. La stratégie de **connexion de périphérique** accorde des autorisations pour l’envoi et la réception des messages sur les points de terminaison côté périphérique du concentrateur IoT.

- Cliquez sur **créer** pour ajouter ce nouveau stratégie à la liste existante.

![][10]

## <a name="messaging"></a>De messagerie

Cliquez sur **messagerie** pour afficher une liste de messagerie des propriétés pour le moyeu IoT qui est en cours de modification. Il existe deux principaux types de propriétés que vous pouvez modifier ou copier : **nuage pour dispositif** et **dispositif de nuage**.

- Les paramètres de **nuage au périphérique** : ce paramètre a deux sous-Paramètres : **nuage pour dispositif de durée de vie** (time-to-live) et la **durée de rétention** pour les messages. Lorsque vous créez le concentrateur IoT, ces deux paramètres sont créées avec une valeur par défaut d’une heure. Pour ajuster ces valeurs, utilisez les curseurs ou tapez les valeurs.

- Paramètres de **l’appareil au nuage** : ce paramètre n’a plusieurs sous-Paramètres, dont certaines sont nommés/affecté lorsque le concentrateur IoT est créé et ne peut être copié pour d’autres sous-paramètres qui sont personnalisables. Ces paramètres sont répertoriés dans la section suivante.

**Partitions**: cette valeur est définie lorsque le concentrateur IoT est créé et peut être modifié par l’intermédiaire de ce paramètre.

**Point de terminaison et le nom de l’événement compatible avec le concentrateur**: lorsque IoT le concentrateur est créé, un concentrateur d’événements est créé en interne que vous devez pouvoir accéder à certaines conditions. Ce nom d’événement compatible avec le Hub et le point de terminaison ne peuvent pas être personnalisées, mais est disponibles pour utilisation via le bouton de **copie** .

**Temps de rétention**: un jour la valeur par défaut mais peut être personnalisée à d’autres valeurs à l’aide de la liste déroulante. Cette valeur est en jours pour dispositif de nuage et non en heures, ainsi que le paramètre similaire pour le nuage pour le périphérique.

**Groupes de consommateurs**: groupes de consommateurs sont un paramètre similaire à d’autres systèmes de messagerie qui peuvent être utilisés pour extraire des données d’une manière spécifique pour connecter d’autres applications ou services IoT concentrateur. Chaque concentrateur IoT est créé avec un groupe de consommateurs de par défaut. Toutefois, vous pouvez ajouter ou supprimer des groupes de consommateurs pour vos concentrateurs IoT.

> [AZURE.NOTE] Le groupe de consommateurs de par défaut ne peuvent pas être modifié ni supprimé.

![][11]

## <a name="pricing-and-scale"></a>Tarification et échelle

Le prix d’un concentrateur IoT existant peut être modifié via les paramètres de **tarification** , avec les exceptions suivantes :

- Dans l’implémentation actuelle, un concentrateur IoT avec un SKU libre ne pouvez pas modifier les niveaux à l’un de l’UGS payés, ou vice versa.
- Il peut être uniquement un hub de IoT couche libre dans l’abonnement Azure.

![][12]

Déplacement d’un niveau supérieur (S2 ou S3) diminuer le niveau (S1 ou S2) est autorisé uniquement lorsque le nombre de messages envoyés pour ce jour n’est pas en conflit. Par exemple, si le nombre de messages par jour dépasse 400 000, la couche pour le concentrateur IoT peut ensuite être modifiée. Toutefois, si vous remplacez le niveau %s1 le concentrateur est limité pour ce jour.

## <a name="delete-the-iot-hub"></a>Supprimez le concentrateur IoT

Vous pouvez parcourir le concentrateur IoT que vous souhaitez supprimer en cliquant sur **Parcourir**, puis en choisissant le concentrateur approprié pour supprimer. Cliquez sur le bouton **Supprimer** sous le nom de concentrateur pour supprimer le concentrateur.

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur ces liens pour en savoir plus sur la gestion d’Azure IoT Hub :

- [En vrac gérer les périphériques IoT][lnk-bulk]
- [Mesures de l’utilisation][lnk-metrics]
- [Surveillance des opérations][lnk-monitor]

Pour Explorer les fonctionnalités de IoT concentrateur, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un périphérique avec le SDK de passerelle IoT][lnk-gateway]
- [Sécuriser votre solution IoT dès le départ des][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md