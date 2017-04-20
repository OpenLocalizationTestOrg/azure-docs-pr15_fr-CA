<properties
 pageTitle="Vue d’ensemble de concentrateur de IoT Azure | Microsoft Azure"
 description="Vue d’ensemble du service d’Azure IoT concentrateur : nouveautés iot concentrateur, connectivité des périphériques, internet des modèles de communication de choses et modèle assistés par service de communication"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>Quel est le concentrateur de IoT Azure ?

Bienvenue dans Azure IoT concentrateur. Cet article fournit une vue d’ensemble d’Azure IoT concentrateur et explique pourquoi vous devez utiliser ce service pour mettre en œuvre une solution Internet des objets (IoT). Concentrateur de IoT Azure est un service entièrement géré qui permet la communication bidirectionnelle fiables et sécurisées entre des millions de périphériques de IoT et un back-end de solution. Concentrateur de IoT Azure :

- Fournit fiable périphérique-nuage et nuage-dispositif de messagerie à l’échelle.
- Permet de sécuriser les communications à l’aide des informations d’identification de sécurité de chaque équipement et contrôle d’accès.
- Fournit une surveillance étendue pour la connectivité des périphériques et des événements de gestion de périphérique identité.
- Inclut des bibliothèques de périphérique pour les plates-formes et les langues les plus populaires.

L’article de la [comparaison de IoT Hub et concentrateurs d’événement] [ lnk-compare] décrit les principales différences entre ces deux services et souligne les avantages de l’utilisation de IoT concentrateur dans vos solutions IoT.

![Concentrateur de IoT Azure comme passerelle de nuage dans internet de solution de choses][img-architecture]

> [AZURE.NOTE] Pour une présentation détaillée de l’architecture de IoT, reportez-vous à l' [Architecture de référence Microsoft Azure IoT][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>Défis du périphérique-connectivité IoT

IoT concentrateur et les bibliothèques de périphérique vous aident à relever les défis de comment vous connecter les périphériques pour le back-end de solution fiable et sécurisée. Périphériques IoT :

- Sont souvent des systèmes intégrés avec aucun opérateur humain.
- Peuvent être dans des emplacements distants, où l’accès physique est coûteuse.
- Peut uniquement être accessible par le biais de la dorsale de la solution.
- Peut avoir limité des ressources de traitement et d’alimentation.
- Peut avoir une connectivité réseau intermittente, lente ou coûteuse.
- Devrez peut-être utiliser des protocoles d’application spécifique à l’industrie, propriétaire ou personnalisé.
- Peuvent être créés à l’aide d’un large éventail de plates-formes de matériel et les logiciels les plus courants.

Outre les exigences ci-dessus, toute solution IoT doit aussi offrir évolutivité, de sécurité et de fiabilité. Le jeu de spécifications de connectivité est long et complexe mettre en œuvre lorsque vous utilisez des technologies traditionnelles, telles que les conteneurs du web et de messagerie courtiers.

## <a name="why-use-azure-iot-hub"></a>Pourquoi utiliser Azure IoT concentrateur

Concentrateur de IoT Azure résout les problèmes de connectivité des périphériques dans l’une des manières suivantes :

-   **L’authentification par périphérique et la connectivité sécurisée**. Vous pouvez configurer chaque périphérique avec sa propre [clé de sécurité] [ lnk-devguide-security] pour lui permettre de se connecter au concentrateur de IoT. Le [Registre d’identité IoT concentrateur] [ lnk-devguide-identityregistry] stocke les identités des appareils et des clés dans une solution. Un back-end de solution peut ajouter des périphériques individuels pour autoriser ou refuser des listes pour activer le contrôle total sur l’accès au périphérique.

-   **Surveillance des opérations de connexion de périphérique**. Vous pouvez recevoir des journaux détaillés sur les opérations de gestion d’identité périphérique et les événements de connexion de périphérique. Cette capacité de surveillance permet votre solution IoT identifier les problèmes de connectivité, tels que les périphériques qui tentent de se connecter avec les informations d’identification incorrectes, envoyer des messages trop fréquemment ou rejeter tous les messages du nuage vers le périphérique.

-   **Un jeu complet de bibliothèques de périphérique**. [Dispositif de IoT Azure SDK] [ lnk-device-sdks] sont pris en charge pour les différentes langues et plates-formes--C pour de nombreuses distributions Linux, Windows et les systèmes d’exploitation en temps réel. Kits SDK de périphériques IoT Azure prennent également en charge les langages managés tels que C#, Java et JavaScript.

-   **Extensibilité et IoT protocoles**. Si votre solution ne peut pas utiliser les bibliothèques de périphérique, IoT concentrateur expose un protocole public qui permet aux périphériques vers une utilisation native du MQTT v3.1.1, HTTP 1.1 ou AMQP 1.0 protocoles. Vous pouvez également étendre le concentrateur IoT pour prendre en charge les protocoles personnalisés par :

    - Création d’une passerelle de champ avec le [SDK de passerelle Azure IoT] [ lnk-gateway-sdk] qui convertit votre protocole personnalisé à l’un des trois protocoles compris par IoT concentrateur. 
    - Personnalisation de la [passerelle de protocole Azure IoT][protocol-gateway], un composant de l’open source qui s’exécute dans le nuage.

-   **Échelle**. Concentrateur de IoT Azure s’adapte à des millions de périphériques connectés simultanément et de millions d’événements par seconde.

Ces avantages sont génériques à de nombreux modèles de communication. IoT concentrateur actuellement vous permet de mettre en œuvre des modèles de communication spécifiques suivants :

-   **Réception du périphérique-nuage basé sur des événements.** IoT concentrateur peuvent recevoir fiable des millions d’événements par seconde à partir de vos périphériques. Il peut ensuite les traiter sur votre chemin réactif à l’aide d’un moteur de processeur d’événements. Il peut également les stocker sur votre chemin à froid pour l’analyse. Concentrateur de IoT conserve les données d’événement pour sept jours afin de garantir le traitement fiable et à absorber les pics de charge.

-   * *Des échanges de nuage vers le périphérique (ou *commandes*). ** le back-end de solution peut utiliser IoT Hub pour envoyer des messages avec une garantie de livraison d’au moins une des équipements individuels. Chaque message possède un paramètre de durée de vie individuel, et le back-end peut demander des bons de livraison et d’expiration à la fois. Ces réceptions garantir une visibilité complète sur le cycle de vie d’un message du nuage vers le périphérique. Vous pouvez alors implémenter la logique métier qui inclut des opérations qui s’exécutent sur des périphériques.

-   **Permet de télécharger des fichiers et des données de capteur de mise en cache dans le nuage.** Les périphériques peuvent télécharger des fichiers vers le stockage Azure à l’aide d’URI SAS géré par IoT concentrateur. IoT concentrateur peut générer des notifications lorsque les fichiers arrivent dans le nuage pour activer le back-end pour les traiter.

## <a name="gateways"></a>Passerelles

Une passerelle dans une solution IoT est généralement une [passerelle de protocole] [ lnk-gateway] qui est déployé dans le nuage ou une [passerelle de champ] [ lnk-field-gateway] qui est déployé localement avec vos périphériques. Une passerelle de protocole effectue la traduction de protocole, par exemple MQTT à AMQP. Une passerelle de champ peuvent exécuter analytique sur le bord, prendre des décisions urgentes pour réduire la latence, fournissent des services de gestion de périphérique, appliquer les contraintes de confidentialité et de sécurité et également effectuer la traduction de protocole. Les deux types de passerelle agissent comme intermédiaires entre vos périphériques et votre concentrateur IoT.

Une passerelle de champ diffère d’un périphérique de routage de trafic simple (par exemple, un pare-feu ou un périphérique de traduction d’adresses réseau) car elle effectue généralement un rôle actif dans la gestion des accès et des informations de flux dans votre solution.

Une solution peut comprendre les protocoles et les passerelles.

## <a name="how-does-iot-hub-work"></a>Comment fonctionne la IoT concentrateur ?

Concentrateur de IoT Azure implémente la [assistés par service de communication] [ lnk-service-assisted-pattern] modèle organisée servant d’intermédiaire les interactions entre vos périphériques et vos principaux de la solution. Assisté par le service de communication vise à établir digne de confiance, les chemins de communication bidirectionnelle entre un système de contrôle IoT concentrateur, des appareils à usage spécifique qui sont déployés dans non fiable d’espace physique. Le modèle établit les principes suivants :

- Sécurité prévaut sur toutes les autres fonctionnalités.
- Appareils n’acceptent pas les informations de réseau non sollicité. Un périphérique établit toutes les connexions et les itinéraires de manière sortant uniquement. Pour un périphérique de recevoir une commande à partir de l’extrémité arrière, le périphérique doit lancer régulièrement d’une connexion pour vérifier les commandes en attente de traitement.
- Périphériques doivent uniquement se connecter à ou établir des itinéraires pour les services connus, ils sont ressources, tels que IoT concentrateur.
- Le chemin de communication entre le périphérique et service ou entre le périphérique et de la passerelle est sécurisé au niveau de la couche de protocole d’application.
- Authentification et autorisation de niveau système sont basés sur les identités par périphérique. Ils constituent les autorisations et les informations d’identification d’accès presque instantanément révocable.
- Pour les périphériques qui se connectent par intermittence d’alimentation ou de connectivité concerne la communication bidirectionnelle est facilitée en maintenant les commandes et les notifications de périphérique jusqu'à ce qu’un périphérique se connecte pour les recevoir. IoT Hub gère des files d’attente de spécifique au périphérique pour les commandes qu’il envoie.
- Données de charge utile d’application sont sécurisées séparément pour le transit protégé via des passerelles à un service particulier.

Le secteur mobile a utilisé le modèle de service assisté de communication à grande échelle pour mettre en œuvre des services de notification de type Pousser par exemple [Windows Push Notification Services][lnk-wns], [Échanges de nuage Google][lnk-google-messaging]et le [Service de Notification de transmission Apple][lnk-apple-push].

IoT concentrateur est pris en charge sur le chemin d’homologation publique de ExpressRoute.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment Azure IoT Hub permet la gestion des périphériques basés sur les normes IoT vous permettant de gérer, de configurer et de mettre à jour de vos périphériques à distance, voir [Gestion des périphériques de vue d’ensemble d’Azure IoT concentrateur][lnk-device-management].

Pour mettre en œuvre des applications clientes sur un large éventail de plates-formes de périphériques matériels et les systèmes d’exploitation, vous pouvez utiliser le kits de développement logiciel de périphérique IoT. Le kits de développement logiciel de périphérique IoT incluent les bibliothèques qui facilitent l’envoi de télémétrie et un concentrateur IoT reçoivent les commandes de nuage vers le périphérique. Lorsque vous utilisez le SDK, vous pouvez choisir à partir de différents protocoles réseau pour communiquer avec IoT concentrateur. Pour plus d’informations, consultez les [informations sur les kits de développement logiciel de périphérique][lnk-device-sdks].

Pour commencer à écrire du code et l’exécution des exemples, consultez la [mise en route de concentrateur de IoT] [ lnk-get-started] didacticiel.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Communications Service assistée, billet de blog par Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
