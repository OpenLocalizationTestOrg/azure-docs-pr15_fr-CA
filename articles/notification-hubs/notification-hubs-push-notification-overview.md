<properties
    pageTitle="Concentrateurs de Notification Azure"
    description="Découvrez comment utiliser les notifications de type Pousser dans Azure. Exemples de code écrits en C# à l’aide de l’API .NET."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Concentrateurs de Notification Azure

##<a name="overview"></a>Vue d’ensemble

Les concentrateurs de Notification Azure offrent une infrastructure facile à utiliser, multi-plateformes, mise à l’échelle à la sortie de commande qui vous permet d’envoyer des notifications de type Pousser mobile à partir de n’importe quel serveur principal (dans le nuage ou le local) permet à n’importe quelle plate-forme mobile.

Grâce aux concentrateurs Notification, vous pouvez facilement envoyer les notifications de transmission multiplates-formes, personnalisé, abstraction les détails des systèmes de notification des plates-formes différentes (solution). Avec un seul appel d’API, vous pouvez cibler des utilisateurs individuels ou à des audiences entière contenant des millions d’utilisateurs, sur tous les périphériques de leurs.

Vous pouvez utiliser des concentrateurs de Notification pour les scénarios d’entreprise et le consommateur. Par exemple :

- Envoyer des notifications de news de rupture des millions avec une faible latence (applications Bing préinstallées sur tous les périphériques Windows et Windows Phone se met des concentrateurs de Notification).
- Envoyer fondée sur l’emplacement des coupons pour les segments d’utilisateurs.
- Envoyer des notifications d’événement à des utilisateurs ou des groupes pour les applications/finance/jeux de sport.
- Avertissez les utilisateurs des événements d’entreprise tels que les nouveaux messages/e-mails et les prospects.
- Envoyer un-mots de passe requis pour l’authentification multifactorielle.



##<a name="what-are-push-notifications"></a>Que sont les Notifications de Push ?

Les Smartphones et les tablettes peuvent « avertir » les utilisateurs lorsqu’un événement s’est produit. Ces notifications peuvent prendre de nombreuses formes.

Dans les applications Windows Store et Windows Phone, la notification peut être sous la forme d’un _toast_: une fenêtre non modale s’affiche, avec un son, pour signaler une nouvelle notification. Autres types de notification qui sont pris en charge incluent les notifications de _Mosaïque_, _raw_et _badge_ . Pour plus d’informations sur les types de notifications pris en charge sur les périphériques Windows, consultez les [mosaïques, les Badges et les Notifications](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Sur les périphériques Apple iOS, le push avertit de la même façon l’utilisateur avec une boîte de dialogue qui demande à l’utilisateur d’afficher ou de fermer le message. Cliquez sur **affichage** pour ouvrir l’application qui reçoit le message. Pour plus d’informations sur les Notifications d’iOS, consultez [Notifications d’iOS](http://go.microsoft.com/fwlink/?LinkId=615245).

Notifications de type Pousser permettent l’affichage d’informations fraîches et tout en restant à l’efficacité énergétique des périphériques mobiles. Notifications peuvent être envoyées par les systèmes back-end sur des appareils mobiles, même lorsque les applications correspondantes sur un périphérique ne sont pas actives. Les notifications de transmission sont un composant essentiel pour les applications consommateur, où ils sont utilisés pour améliorer l’utilisation et l’engagement de l’application. Les notifications sont également utiles pour les entreprises, lorsque des informations à jour augmentent la réactivité des employés à des événements de l’entreprise.

Quelques exemples spécifiques de scénarios d’engagement mobile sont :

1.  Mise à jour d’une mosaïque dans Windows 8 ou Windows Phone avec des informations financières en cours.
2.  Alertes d’un utilisateur avec un toast un élément de travail a été affecté à l’utilisateur, dans une application d’entreprise basée sur le flux de travail.
3.  Affichage d’un badge avec le nombre de ventes en cours des prospects dans une application CRM (par exemple de Microsoft Dynamics CRM).

##<a name="how-push-notifications-work"></a>Comment placer le travail de Notifications

Les notifications de transmission sont fournis par des infrastructures spécifiques à la plateforme appelés _Systèmes de Notification de plate-forme_ (solution). Une solution offre des fonctions de carcasses (autrement dit, aucune prise en charge pour la diffusion, personnalisation) et n’ont aucune interface commune. Par exemple, pour envoyer une notification à une application du Windows Store, un développeur doit contacter le WNS (Service de Notification de Windows). Pour envoyer une notification à un périphérique d’e/s, même développeur doit contacter APNS (Apple Push Notification Service) et envoie le message une deuxième fois. Les concentrateurs de Notification Azure aident en fournissant une interface commune, ainsi que d’autres fonctionnalités pour prendre en charge les notifications de type Pousser sur chaque plate-forme.

Cependant, à un niveau élevé, tous les systèmes de notification de plate-forme suivent le même modèle :

1.  L’application cliente contacte la solution pour récupérer son _Gérer_. Le type de handle dépend du système. WNS, il est un URI ou « canal de notification ». Pour APNS, il s’agit d’un jeton.
2.  L’application client stocke ce handle dans l' application _back-end_ pour une utilisation ultérieure. Pour WNS, back-end est généralement un service en nuage. Pour les ordinateurs Apple, le système est appelé un _fournisseur_.
3.  Pour envoyer une notification de transmission, application back-end contacte la solution à l’aide de la poignée pour cibler une instance d’application client spécifique.
4.  La solution transmet la notification au périphérique spécifié par le handle.

![][0]

##<a name="the-challenges-of-push-notifications"></a>Les défis de Notifications de type Pousser

Alors que ces systèmes sont très puissants, ils laissent toujours beaucoup de travail pour le développeur de l’application afin d’implémenter le même émetteur notification courantes, telles que la diffusion ou l’envoi de notifications de type Pousser aux utilisateurs segmentés.

Les notifications de transmission sont une des fonctionnalités plus demandées dans les services de cloud pour les applications mobiles. La raison à cela est que l’infrastructure nécessaire pour qu’ils fonctionnent est assez complexe et généralement non liées à la logique de l’activité principale de l’application. Certains des défis dans la conception d’une infrastructure de transmission de la demande sont les suivantes :

- **Dépendance de plate-forme.** Pour envoyer des notifications aux périphériques sur différentes plates-formes, plusieurs interfaces doivent être codées dans le back-end. Non seulement sont les détails de bas niveau différents, mais la présentation de la notification (mosaïque, toast ou badge) est également dépendant de la plateforme. Ces différences peuvent entraîner un code principal complexe et difficile à gérer.

- **Échelle.** Mise à l’échelle de cette infrastructure présente deux aspects :
    + Par les instructions de la solution, les jetons de périphérique doivent être actualisées chaque fois que l’application est lancée. Il en résulte une grande quantité de trafic (et accède à la base de données qui en résulte) uniquement pour les jetons de périphérique à jour. Lorsque le nombre de périphériques de grandir (et éventuellement à des millions), le coût de la création et la gestion de cette infrastructure est nonnegligible.

    + La plupart des PNSs ne supportent pas diffusion sur plusieurs périphériques. Il s’ensuit qu’une diffusion à des millions de périphériques se traduit par des millions d’appels des PNSs. Capable de s’adapter ces requêtes est importante, car les développeurs d’application recommandé de réduire la latence totale. Par exemple, le dernier périphérique à recevoir le message ne doit pas recevoir la notification 30 minutes après l’envoi de notifications, en ce qui concerne la plupart des cas il serait en opposition avec l’objectif pour les notifications de transmission.
- **Le routage.** PNSs fournissent un moyen d’envoyer un message à un périphérique. Toutefois, dans la plupart des applications notifications visant les utilisateurs et/ou les groupes d’intérêt (par exemple, tous les employés affectés à un compte client). En tant que tel, afin d’acheminer les notifications sur les périphériques appropriés, application back-end doit conserver un Registre qui associe des groupes d’intérêt à jetons de périphérique. Cette surcharge ajoute à la durée totale de coûts de marché et de la maintenance d’une application.

##<a name="why-use-notification-hubs"></a>Pourquoi utiliser des concentrateurs de Notification ?

Notification de concentrateurs éliminent la complexité : vous n’êtes pas obligé de relever les défis de notifications de type Pousser. Au lieu de cela, vous pouvez utiliser un concentrateur de la Notification. Concentrateurs de notification utilisent une infrastructure de notification de transmission complète multi-plateformes, à grande échelle et réduisent considérablement le code spécifique à la commande qui s’exécute dans le back-end de l’application. Concentrateurs de notification implémentent toutes les fonctionnalités d’une infrastructure de transmission. Les périphériques ne sont chargés de l’enregistrement des poignées de la solution, et le serveur principal est responsable de l’envoi de messages d’indépendant de la plate-forme à des utilisateurs ou des groupes d’intérêt, comme illustré dans la figure suivante :

![][1]


Concentrateurs de notification fournissent une infrastructure de notification d’émission de prêts à l’emploi avec les avantages suivants :

- **Plusieurs plates-formes.**
    +  Prise en charge pour toutes les principales plates-formes mobiles. Concentrateurs de notification peuvent envoyer des notifications de type Pousser aux applications Windows Store, iOS, Android et Windows Phone.

    +  Concentrateurs de notification fournissent une interface commune pour envoyer des notifications pour toutes les plates-formes prises en charge. Les protocoles spécifiques à la plate-forme ne sont pas nécessaires. Application back-end peut envoyer des notifications dans des formats spécifiques à la plate-forme ou indépendante de la plate-forme. L’application communique uniquement avec les concentrateurs de la Notification.

    +  Poignée de gestion des périphériques. Notification de concentrateurs conserve le handle de Registre et les commentaires à partir de PNSs.

- **Fonctionne avec n’importe quel back-end**: nuage ou sur site, .NET, PHP, Java, nœud, etc..

- **Échelle.** Échelle de concentrateurs de notification à des millions de périphériques sans devoir remanier ou partagé.


- **Ensemble complet de modèles de livraison**:

    - *Diffusion*: permet la diffusion quasi simultanées à des millions de périphériques avec un seul appel d’API.

    - *Monodiffusion/multidiffusion*: distribuer aux balises représentant des utilisateurs individuels, y compris tous les dispositifs ; ou d’un groupe plus large ; par exemple, différents facteurs de forme (tablet et téléphone).

    - *Segmentation*: pousser vers le segment complexe défini par les expressions de balise (par exemple, les périphériques à New York suite à supporter les Yankees).

    Chaque périphérique, lors de l’envoi de sa poignée à un concentrateur de la notification, peut spécifier une ou plusieurs _balises_. Pour plus d’informations sur les [balises]. Balises n’ont pas à être pré-configurés ou supprimé. Balises fournissent un moyen simple pour envoyer des notifications à des utilisateurs ou des groupes d’intérêt. Étant donné que les balises peuvent contenir n’importe quel identificateur spécifique à l’application (comme les ID utilisateur ou un groupe), leur utilisation libère application back-end de se décharger de devoir de stocker et de gérer les handles de périphérique.

- **Personnalisation**: chaque périphérique peut avoir un ou plusieurs modèles, pour atteindre la localisation de chaque équipement et personnalisation sans affecter le code de back-end.

- **Sécurité**: accès Secret (SAS) ou l’authentification fédérée partagés.

- **TELEMETRIE de riches**: disponible dans le portail et par programme.


##<a name="integration-with-app-service-mobile-apps"></a>Intégration avec le Service d’application des applications mobiles

Pour faciliter une expérience transparente et unification entre des services Azure, [Applications de Mobile Application Service] prend en charge les notifications de transmission à l’aide de concentrateurs de Notification. [Applications de Mobile Application Service] offre une plate-forme de développement des applications mobiles évolutives, globalement disponibles pour les développeurs d’entreprise et aux intégrateurs système qui offre un ensemble rich de fonctionnalités pour les développeurs mobiles.

Les développeurs d’applications Mobile peuvent utiliser des concentrateurs de Notification avec le flux de travail suivant :

1. Récupérer le handle de solution de périphérique
2. Enregistrer le périphérique et les [modèles] centraux de Notification via l’API de Registre SDK de Client applications Mobile pratique avec
    + Notez que les applications Mobile supprime toutes les balises dans des enregistrements pour des raisons de sécurité. Travailler avec des concentrateurs de Notification à partir de votre serveur principal directement à associer des balises à des périphériques.
3. Envoyer des notifications à partir de votre back-end de l’application avec les concentrateurs de Notification

Voici certaines commodités mises aux développeurs avec cette intégration :

- **Kits de développement logiciel Client d’applications mobiles.** Ces kits de développement logiciel multi-plateformes fournissent des API simples pour l’inscription et communiquer avec le concentrateur de notification lié automatiquement avec l’application mobile. Les développeurs n’avez pas besoin plonger dans les informations d’identification de concentrateurs de Notification et de travailler avec un service supplémentaire.
    + Kits de développement logiciel balise automatiquement le périphérique donné avec un ID d’utilisateur authentifié applications Mobile pour activer l’extraction vers le scénario de l’utilisateur.
    + Les kits de développement utilisent automatiquement l’ID d’Installation Apps Mobile en tant que GUID pour enregistrer avec Notification concentrateurs, évite aux développeurs la peine de maintenir plusieurs GUID du service.
    
- **Modèle d’installation.** Applications Mobile fonctionne avec le dernier modèle d’émission des concentrateurs de Notification pour représenter toutes les propriétés d’un envoi associées avec un périphérique dans une Installation de JSON qui s’aligne avec Push Notification Services et est facile à utiliser.

- **Flexibilité.** Les développeurs peuvent toujours choisir de travailler avec des concentrateurs de Notification directement même avec l’intégration en place.

- **Expérience intégrée dans [Azure portal].** Pousser une capacité est représentée visuellement dans les applications mobiles et les développeurs peuvent travailler facilement avec le hub de notification associé à travers les applications mobiles.



##<a name="next-steps"></a>Étapes suivantes

Vous trouverez plus d’informations sur les concentrateurs de Notification dans ces rubriques :

+ **[Comment les clients utilisent les concentrateurs de Notification]**

+ **[Notification concentrateurs didacticiels et guides]**

+ **Didacticiels de notification concentrateurs mise en route** ([iOS], [Android], [Windows universel], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Références à l’API géré par le .NET pertinentes pour les notifications de type Pousser se trouvent ici :

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Comment les clients utilisent les concentrateurs de Notification]: http://azure.microsoft.com/services/notification-hubs
  [Notification concentrateurs didacticiels et guides]: http://azure.microsoft.com/documentation/services/notification-hubs
  [e/s]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Universel de Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Service d’application des applications mobiles]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [modèles]: notification-hubs-templates-cross-platform-push-messages.md
  [Azure portal]: https://portal.azure.com
  [balises]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
