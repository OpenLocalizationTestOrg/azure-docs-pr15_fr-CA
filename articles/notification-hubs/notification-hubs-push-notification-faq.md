<properties
    pageTitle="Concentrateurs de Notification Azure - Forum aux Questions (FAQ)"
    description="Forum aux questions sur la conception/mise en œuvre de solutions sur les concentrateurs de Notification"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="notification de transmission, les notifications de transmission, les notifications de type Pousser iOS, notifications de type Pousser android, push d’ios, android push"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Pousser des Notifications avec des concentrateurs de Notification Azure - Forum aux Questions

##<a name="general"></a>Général
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. qu’est le modèle de prix pour des moyeux de Notification ?
Concentrateurs de notification est proposé en trois versions :

* **Libre** - obtenir pousse jusqu'à 1 million par abonnement un mois.
* **Base** - push de 10 millions de get par abonnement mois sous la forme d’une ligne de base, avec des options de croissance de quota.
* **Standard** - obtenir pousse 10 millions par abonnement mois sous la forme d’une ligne de base, avec quota augmenter les options, plus oublier de télémétrie riche.

Vous trouverez les informations les plus récentes sur la page [Tarification des concentrateurs de Notification] . Le prix est établi au niveau de l’abonnement et est basé sur le nombre de déclenchements de notification de transmission peu importe combien concentrateurs d’espaces de noms ou de notification que vous avez créés dans votre abonnement Azure.

Couche de **libre** est proposé à des fins de développement sans garantie de SLA. Alors que ce niveau est un bon point de départ pour ceux qui à Explorer les fonctionnalités de notifications de type Pousser par l’intermédiaire de concentrateurs de Notification d’Azure, il est peut-être pas le meilleur choix pour les moyennes et les applications à grande échelle.

**Base** & niveaux**Standard** est proposées, de l’utilisation de production avec les fonctionnalités clés suivantes activée *uniquement pour la norme de couche*:

- *TELEMETRIE de RTF* - offre de concentrateurs de Notification un certain nombre de fonctionnalités pour exporter vos données de télémétrie ainsi que de distribuer les informations de l’enregistrement de notification pour consultation hors connexion et l’analyse.
- *L’architecture mutualisée* - idéal si vous créez une application mobile à l’aide de concentrateurs de Notification pour prendre en charge des différents utilisateurs. Cela vous permet de définir des informations d’identification de Push Notification Services (solution) au niveau de l’espace de noms de concentrateur de Notification pour l’application, puis vous pouvez séparer les locataires mettant à leur disposition des concentrateurs dans cet espace de noms commun. Cela permet de faciliter la maintenance tout en gardant les clés de SAS pour envoyer et recevoir des notifications de type Pousser les concentrateurs de notification séparées pour chaque client assurant le chevauchement de cross-clients non.
- *Planifiée de transmission* - permet de planifier les notifications de transmission, qui seront par la suite la file d’attente et envoyées.
- *Importer en bloc* - vous permet d’importer les enregistrements en bloc.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. Quelle est la SLA de concentrateurs de Notification ?
Pour les niveaux de **base** et des concentrateurs de Notification **Standard** , nous garantit qu’au moins 99,9 % du temps, applications correctement configurées sera en mesure d’envoyer des notifications de type Pousser ou effectuer des opérations de gestion de l’inscription par rapport à un concentrateur de Notification déployées au sein d’un niveau de prise en charge. Pour en savoir plus sur notre SLA, visitez la page de [Notification concentrateurs SLA] .

> [AZURE.NOTE] Ne sont aucuns garanties SLA pour les pieds entre le Service de Notification de plate-forme et le périphérique car les concentrateurs de Notification dépendent des fournisseurs de plate-forme externe pour fournir la notification de transmission sur le périphérique.

###<a name="3---which-customers-are-using-notification-hubs"></a>3. les clients qui sont à l’aide de concentrateurs de Notification ?
Nous disposons d’un grand nombre de clients à l’aide de concentrateurs de Notification avec quelques notables ceux mentionnés ci-dessous :

* Sochi 2014 – 100 s de groupes d’intérêt, 3 millions de périphériques, une notification de 150 millions d’expédiés dans les 2 semaines. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Temps de Seattle - [CaseStudy - Seattle fois]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Relatif aux applications Bing – 10 millions d’unités, envoi de notifications de 3 millions par jour.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. comment mettre à niveau ou déclasser mon concentrateurs de Notification pour modifier mon niveau de service ?
Accédez au [Portail classique d’Azure]et cliquez sur Bus de Service, cliquez sur dans votre espace de noms puis votre concentrateur de notification. Sous l’onglet échelle, vous ne pourrez pas modifier votre niveau de service de concentrateurs de Notification.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Conception et développement
###<a name="1---which-server-side-platforms-do-you-support"></a>1. les plates-formes côté serveur vous prennent en charge ?
Nous fournissons kits de développement logiciel et [exemples complets] pour .NET, Java, PHP, Python, Node.js afin qu’un back-end de l’application peut être paramétré pour communiquer à des concentrateurs de Notification à l’aide d’une de ces plates-formes. API de concentrateurs de notification sont basés sur les interfaces du reste pour que vous puissiez choisir directement parler à ceux à la place si vous ne souhaitez pas ajouter une dépendance supplémentaire. Vous trouverez plus de détails sur la page [NH - API de reste] .

###<a name="2---which-client-platforms-do-you-support"></a>2. les plates-formes client prenez-vous en charge ?
Nous prenons en charge d’envoi de notifications de type pousser à [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows universel](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Chine Android (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([e/s](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), les plates-formes [Des applications de Chrome](notification-hubs-chrome-push-notifications-get-started.md) et [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) . Pour obtenir une liste complète de l’obtention des didacticiels attaquer l’envoi de notifications de type Pousser sur ces plates-formes, visitez notre page de [NH - mise en route les didacticiels de démarrage] .

###<a name="3---do-you-support-smsemailweb-notifications"></a>3., vous prenez en charge les notifications d’E-mail/SMS/web ?
Concentrateurs de notification est principalement conçu pour envoyer des notifications à des applications mobiles en utilisant les plates-formes répertoriées ci-dessus. Que nous ne fournissons pas encore la possibilité d’envoyer des e-mails ou des alertes SMS ; Toutefois les plates-formes tierces qui fournissent ces fonctionnalités peuvent être intégrés avec des concentrateurs de Notification pour envoyer des notifications de type Pousser natif à l’aide [d’Applications de Mobile Azure].

Également, les concentrateurs de notification ne fournissent pas un navigateur push notification livraison service-de-l’emploi. Les clients peuvent choisir cette implémentation à l’aide de SignalR sur les plates-formes prises en charge côté serveur. Si vous souhaitez pour envoyer des notifications à des applications de navigateur dans le sandbox de Chrome, consultez le [didacticiel d’applications de Chrome].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. Quelle est la relation entre les applications de Mobile Azure et d’Azure Notification concentrateurs et quand les utiliser ?
Si vous avez un back-end application mobile existant et que vous souhaitez ajouter la possibilité d’envoyer des notifications de transmission uniquement vous pouvez utiliser des concentrateurs de Notification Azure. Si vous souhaitez configurer votre back-end de l’application mobile à partir de zéro puis vous devez envisager d’utiliser des applications de Mobile Azure. Une application de Mobile Azure met automatiquement un concentrateur de Notification pour vous permettre d’envoyer des notifications de type Pousser facilement à partir du serveur principal chargé de l’application mobile. Tarification pour les applications mobiles Azure inclut les frais des base d’un concentrateur de Notification et vous ne payez que lorsque vous passez au-delà de la pousse inclus. Plus de détails sur les coûts sont disponibles sur la page [Application de tarification Service] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. combien de périphériques puis-je prendre en charge si j’envoie des notifications de type Pousser via des concentrateurs de Notification ?
Reportez-vous à la page [Tarification des concentrateurs de Notification] pour plus d’informations sur le nombre de périphériques pris en charge.

Pour certains scénarios, si vous avez besoin d’une prise en charge de plus de 10 000 000 périphériques inscrits, veuillez directement de [nous contacter](https://azure.microsoft.com/overview/contact-us/) et nous vous permettra de faire évoluer votre solution.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. le nombre de notifications de type Pousser peuvent envoyer ?
Selon le niveau choisi, Azure sera automatiquement évoluer en fonction du nombre de notifications transitant par le système.

>[AZURE.NOTE] Le coût de l’utilisation globale peut remonter en fonction du nombre de notifications de type Pousser est pris en charge. Assurez-vous que vous êtes conscient des limites de couche existante décrites sur la page [Tarification des concentrateurs de Notification] .

Nos clients existants utilisent des concentrateurs de Notification pour envoyer tous les jours des millions de notifications de type Pousser. Vous n’avez pas à effectuer d’action particulière à l’échelle de votre émission notifications atteint dans la mesure où l’utilisation de concentrateurs de Notification Azure.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. combien de temps faut-il pour pousser des notifications pour atteindre mon périphérique ?
Azure concentrateurs de Notification est en mesure de traiter au moins **1 million de notification d’émission envoie une minute** normal utiliser scénario où la charge entrante est relativement constante et n’est pas spikey par nature. Ce taux peut varier selon le nombre de balises, de nature d’envois entrants et d’autres facteurs externes.

Pendant le délai de livraison estimé, le service est en mesure de calculer les cibles par la plate-forme et l’itinéraire des messages aux services de livraison respectifs push notification basées sur les expressions de balises/balise enregistrée. À partir de là, il est de la responsabilité des services Push des Notifications (solution) pour envoyer la notification à l’appareil.

Une solution ne garantit pas les SLA pour livrer des notifications ; Toutefois, en général une grande majorité des notifications de type Pousser sont remis aux équipements cibles dans quelques minutes (généralement dans les limites de 10 minutes) à partir du moment qu’ils sont envoyés à notre plate-forme. Il peut y avoir quelques solitaires qui peuvent prendre plus de temps.

>[AZURE.NOTE] Les concentrateurs de Notification Azure dispose d’une stratégie permettant de supprimer les notifications de type Pousser qui ne sont pas en mesure d’être remis à la solution en 30 minutes. Ce délai peut se produire pour plusieurs raisons, plus fréquemment, car la solution est la limitation de votre application.

###<a name="8---is-there-any-latency-guarantee"></a>8. existe-t-il aucune garantie de latence ?
La nature des notifications de type Pousser (ils sont remis par un Service de Notification de transmission externe, spécifique à la plate-forme), il n’existe aucune garantie de latence. En général, la majorité des notifications de type Pousser remis au destinataire dans quelques minutes.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. Quelles sont les considérations que j’ai besoin de prendre en compte lors de la conception d’une solution avec les espaces de noms et des concentrateurs de Notification ?

####<a name="mobile-appenvironment"></a>Application/environnement mobile

* Il doit exister un concentrateur de Notification par une application mobile, par l’environnement.
* Dans un scénario mutualisée, chaque client doit avoir un concentrateur distinct.
* Vous ne devez jamais partager au même concentrateur de Notification entre les environnements de test et de production que cela peut entraîner des problèmes de la ligne vers le bas lors de l’envoi de notifications. par exemple, Apple offre Sandbox et Production pousser les points de terminaison avec chacun possédant des informations d’identification séparées.
* Par défaut, vous pouvez envoyer des notifications de test pour vos périphériques inscrits via le portail Azure ou le composant intégré Azure dans Visual Studio. Le seuil est défini sur 10 périphériques qui sont sélectionnées de manière aléatoire à partir du pool de l’enregistrement.

>[AZURE.NOTE] Si le concentrateur a été configuré à l’origine avec un certificat de sandbox Apple et puis reconfiguré pour utiliser un certificat de production d’Apple, les jetons de périphérique ancien seraient ne sont plus valides avec le nouveau certificat et provoquer le pousse à échouer. Il est préférable de séparer votre production et environnements de test, utilisez différents concentrateurs pour les différents environnements.

####<a name="pns-credentials"></a>Informations d’identification de la solution

Lorsqu’une application mobile est enregistrée avec le portail de développement de la plate-forme (par exemple Apple ou Google etc.) puis vous obtenez une application jetons d’identificateur et de la sécurité un back-end de l’application doit fournir des services de Notification de transmission de la plate-forme pour être en mesure d’envoyer des notifications de type Pousser aux périphériques. Ces jetons de sécurité qui peuvent être sous la forme de certificats (par exemple Apple iOS ou Windows Phone) ou les clés de sécurité (Google Android, Windows etc.) doivent être configurés dans les concentrateurs de la Notification. Cela s’effectue généralement au niveau du concentrateur de notification, mais peut également être effectuée au niveau de l’espace de noms dans un scénario de plusieurs utilisateurs.

####<a name="namespaces"></a>Espaces de noms

Espaces de noms peut être utilisé pour le regroupement de déploiement.  Il peut également être utilisé pour représenter tous les concentrateurs de Notification pour tous les locataires de la même application dans le cas de plusieurs utilisateurs.

####<a name="geo-distribution"></a>Geo-distribution

Distribution géographique n’est pas toujours critique dans les scénarios de notification de transmission. Il est à noter que différentes Push Notification Services (par exemple, APNS, etc. GCM), qui a finalement livrer les notifications de type Pousser aux périphériques, ne sont pas équitablement répartis soit.

Si vous avez une application qui est utilisée dans le monde entier, vous pouvez créer plusieurs concentrateurs dans les espaces de noms différents et tirer parti de la disponibilité du service de Notification concentrateurs dans différentes régions d’Azure dans le monde entier.

>[AZURE.NOTE] Cela augmente le coût de gestion - en particulier autour des enregistrements, afin que ce n’est pas vraiment recommandé et ne doit être utilisé que s’il existe une nécessité.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10. dois-je faire des enregistrements à partir du serveur principal chargé de l’application ou directement via le client de périphériques ?
Les enregistrements à partir du serveur principal chargé de l’application sont utiles lorsque vous devez effectuer l’authentification du client avant de créer l’enregistrement ou lorsque vous avez des balises qui doivent être créés ou modifiés par le back-end de l’application selon une logique d’application. Pour plus d’informations, vous pouvez en savoir plus dans les pages de [conseils de l’inscription du back-end] et [back-end inscription Guide - 2] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. quel est le modèle de sécurité de livraison de notification envoyé ?
Les concentrateurs de Notification Azure utiliser une [Signature de l’accès partagé (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md)-en fonction du modèle de sécurité. Vous pouvez utiliser les jetons SAS au niveau de l’espace de noms racine ou au niveau granulaire des concentrateurs de Notification. Ces jetons SAS peut être défini avec des règles d’autorisation différents, par exemple envoyer message autorisations, écoutez des autorisations de notification etc.. Plus de détails sont disponibles dans le document de [modèle de sécurité de NH] .

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. Comment dois-je traiter des charge sensible dans les notifications de type Pousser ?
Toutes les notifications sont remises aux équipements cibles par la plate-forme Push Notification Services (solution). Lorsqu’un expéditeur envoie une notification à des concentrateurs de Notification Azure nous traiter et transmettre la notification à la solution respectifs.

Toutes les connexions de l’expéditeur dans les concentrateurs de Notifications d’Azure, puis dans la solution utilisent le protocole HTTPS.

>[AZURE.NOTE] Concentrateurs de Notifications Azure n’enregistre pas la charge utile du message en aucune façon.

Pour l’envoi des charges sensibles mais nous vous recommandons un modèle Push sécurisé où l’expéditeur propose une notification « ping » avec un identificateur de message au périphérique sans la charge utile sensible et lorsque l’application sur le périphérique reçoit cette charge utile, il est en mesure d’appeler une API sécurisée directement pour extraire les détails du message. Un guide sur la manière d’implémenter le modèle décrit ci-dessus est disponible sur la page [NH - didacticiel de Push de Secure] .

##<a name="operations"></a>Opérations
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. qu’est l’histoire de la récupération après sinistre (DR) ?
Nous fournir une couverture de reprise après sinistre de métadonnées sur notre fin (nom de concentrateur de Notification, chaîne de connexion et autres informations critiques). Lors du déclenche d’un scénario de reprise après sinistre, les données des enregistrements sont le **segment uniquement** de l’infrastructure de concentrateurs de Notification qui est perdue. Vous devrez implémenter une solution pour remplir de nouveau ces données dans votre nouvelle après la reprise concentrateur.

- *Étape 1* - créer un concentrateur secondaire de Notification dans un autre centre de données. Vous pouvez le créer à la volée au moment de l’événement de reprise après sinistre, ou vous pouvez en créer un à partir du se passer. Il ne rend beaucoup de différence option que vous choisissez, car la mise en service de concentrateur de Notification est un processus relativement rapide dans l’ordre de quelques secondes. Ayant une depuis le début pour protéger à partir de l’événement de reprise après sinistre impact sur vos capacités de gestion, afin qu’il soit l’option fortement recommandé.

- *Étape 2* - Hydrate le concentrateur de Notification secondaire avec les enregistrements à partir de la Notification de concentrateur principal. Il n’est pas recommandé pour essayer de conserver les enregistrements sur les deux concentrateurs et essayez de les maintenir synchronisées à la volée lorsque des enregistrements arrivent dans - en général qui n’a pas travaillé bien en raison de la tendance inhérente des enregistrements d’expiration sur le côté de la solution. Concentrateurs de notification les nettoyer que nous recevrons de commentaires solution sur les enregistrements expirés ou invalides.  

Il est recommandé d’utiliser un back-end de l’application qui :

- Conserve un ensemble d’enregistrements à sa fin, afin qu’il peut faire une insertion en bloc dans le concentrateur secondaire de notification en cas de reprise après sinistre

**OU**

- Obtient une expression régulière de vidage des enregistrements à partir du concentrateur principal de sauvegarde et puis est une insertion en bloc dans le NH secondaire.

>[AZURE.NOTE] Fonctionnalité d’exportation/importation des enregistrements disponible dans la couche Standard est décrite dans le document [d’Importation/exportation des enregistrements] .

Si vous n’avez un back-end, puis au démarrage de l’application sur un des équipements cibles, ils exécuteront une nouvelle inscription dans le concentrateur de Notification secondaire, et finalement secondaire Notification Hub aura tous les périphériques actifs enregistrés.

L’inconvénient est qu’il y aura une période de temps lorsque les périphériques où les applications n’ont pas ouvert ne recevra pas de notifications.

###<a name="2---is-there-any-audit-log-capability"></a>2. existe-t-il aucune fonction de journal d’audit ?
Toutes les opérations de gestion de concentrateurs de Notification atteindre des journaux qui sont exposées dans le [Portail classique d’Azure].

##<a name="monitoring--troubleshooting"></a>Surveillance et dépannage
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. Quelles capacités de dépannage sont disponibles ?
Concentrateurs de Notification Azure fournissent plusieurs fonctionnalités pour effectuer les procédures de dépannage courantes, en particulier dans le scénario le plus courant autour des notifications ignorées. Consultez les détails dans notre [NH - dépannage] livre blanc.

###<a name="2---what-telemetry-features-are-available"></a>2. les fonctionnalités de télémétrie sont disponibles ?
Azure permet de concentrateurs de Notification affichant les données de télémétrie dans le [Portail classique d’Azure]. Détails des mesures disponibles sont disponibles sur la page [NH - mesures] .

>[AZURE.NOTE] Notifications de réussies, cela signifie simplement que les notifications de type Pousser ont été remises pour le Service de Notification de commande externe (par exemple, APNS pour Apple, GCM pour Google etc.). C’est la solution pour livrer la notification aux équipements cibles. En règle générale, la solution n’expose pas les mesures de remise pour les fournisseurs tiers.  

Nous proposons également la possibilité d’exporter les données de télémétrie par programme (dans la couche **Standard** ). Consultez le [NH - exemple de métrique] pour plus de détails.

[Azure Portal classique]: https://manage.windowsazure.com
[Concentrateurs de notification de tarification]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Concentrateurs de notification SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - heures de Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - autres API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - mise en route des didacticiels]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Didacticiel d’applications de chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Conseils de l’inscription du serveur principal]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Guide de l’inscription du serveur principal - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modèle de sécurité de NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - didacticiel de Push sécurisé]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - dépannage]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - mesures]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - exemple de métrique]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Importation/exportation des enregistrements]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[exemples complets]: https://github.com/Azure/azure-notificationhubs-samples
[Applications mobiles Azure]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[Service d’application de tarification]: https://azure.microsoft.com/en-us/pricing/details/app-service/
