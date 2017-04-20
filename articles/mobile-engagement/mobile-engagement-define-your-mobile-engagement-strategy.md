<properties
    pageTitle="Définir votre stratégie Mobile Engagement | Microsoft Azure"
    description="Découvrez comment intégré à et optimiser votre Engagement Mobile avec analytique et transmission des notifications."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="define-your-mobile-engagement-strategy"></a>Définir votre stratégie d’Engagement de Mobile

*Vous avez écrit votre application, pour une raison : pour que vos utilisateurs à utiliser !*

Nous pensons que vous placez certainement beaucoup d’effort en tentant de rendre une application que les utilisateurs apprécieront. Vous également certainement investi d’une quantité notable de budget marketing pour acquérir des utilisateurs. Mais après le pic initial agréable d’utilisateurs, vous pouvez voir les lentement arrêter à l’aide de votre application. *Il s’agit tout savoir sur quel Engagement de Mobile Azure !*: incitant à respecter et vous permettent d’améliorer votre application par le biais de test incrémentielle et d’en savoir plus.

Notre approche pour améliorer la conservation et l’utilisation est basée sur l’engagement des utilisateurs d’application par le biais de notifications de transmission et les messages dans l’application, mais d’une manière très spéciale, des messages et de communication adaptée, chacun en fonction de leur comportement dans votre application. Notre objectif est de vous permettent de communiquer avec la bonne audience au bon moment et au bon endroit.

Mais pour ce faire, vous devez démarrer avec la *compréhension de vos utilisateurs*, puis créer des groupes en fonction de ce qu’ils le faisaient ou leurs caractéristiques (nous l’appelons segments) et puis créer les communications appropriées à chaque segment.

## <a name="mobile-engagement-serves-your-objectives"></a>Engagement de mobile sert de vos objectifs

*Nous avons sur la conservation et l’utilisation, mais de quoi ?*

L’élaboration de votre stratégie Mobile Engagement demande regardant d’abord à des objectifs et des indicateurs de performance clés (KPI) de votre application.

Commencez par définir des objectifs et des indicateurs de performance clés qui permettent de définir votre engagement-cas d’utilisation avec le prisme de droit.

Votre cas d’usage sont une liste simple des campagnes que vous souhaitez apporter à communiquer avec les utilisateurs, allant de la simple d’accueil, à la notification de l’utilitaire très avancé déclenchée par votre système informatique. Un cas d’usage bien construit doit inclure au moins le trio *simulation-qui-lorsque*:

1. Désignation très courte (par exemple, une « Bienvenue campagne »).
2. **Quel**: un exemple de message (par exemple, « heureux de vous avoir intégré ! N’oubliez pas d’ouvrir une session pour obtenir votre 1er mois gratuit ! »). Ce message n’est pas final, vous pourrez le modifier quand que vous le souhaitez, mais il permet généralement de commencer à réfléchir sur ce que nous voulons dire.
3. **Qui**: le segment qui recevra ce message (par exemple, « tous les utilisateurs qui a lancé l’application pour la première fois 3 jours, ont visité la page de connexion mais n'êtes pas connecté à »).
    - Oui, vous pouvez le faire très facilement avec Azure Mobile Engagement  :)
    - Là encore, cela n’a pas à être final que vous pouvez définir vos segments à tout moment, mais il est important de définir dès le début sur vos critères de segmentation pour vous garantir collecter les bonnes données.
4. **Quand**: le minutage de votre campagne. Il peut être à une date donnée ou après une action spécifique, basé sur un déclencheur. Engagement de Mobile propose un montant important de possibilités à juste titre temps votre communication.

Une fois les cas d’usage et de segment sont définis, il donne une indication pour définir les données qui doivent être collectées dans une application. C’est le rôle d’un *« plan de balise »*. Un plan de la balise permet de vous assurer que la collection de données est spécifiée pour les développeurs. Par conséquent, les développeurs sont en mesure d’incorporer l’Engagement de Mobile avec la configuration appropriée, vous pouvez utiliser vos campagnes avec les bonnes données. Il est également très important d’exécuter des tests pour s’assurer de l’intégration est correcte et qu’il collecte à ce dont vous avez besoin.

Basé sur l’intégration, une fois que les applications sont publiées, comme un sont pourront voir votre analytique en temps réel, segmenter votre audience et ensuite commencer à envoyer des smart ciblé de notification d’émission avec les utilisateurs finaux dans ou en dehors de l’application.

### <a name="use-cases-to-get-started"></a>Cas d’usage pour commencer
1. Bienvenue dans la stratégie : créer plusieurs campagnes de notification de transmission basés sur le comportement de l’utilisateur final lors du lancement de l’application pour recontacter à D + 2/5/10/15 après la première session et augmentation de première exécution de rétention.
2. Promouvoir un nouveau contenu (fonction, article/vidéo ou produit) basé sur le comportement de l’utilisateur final à envoyer ces informations uniquement pour les utilisateurs finaux qui sont plus susceptibles de s’engager.
3. L’application de taux : cible de moins de 1 % de votre base d’utilisateurs plus susceptible d’évaluer les étoiles app 5 sur la banque.
4. Améliorer les abonnements : promouvoir le contenu précieux pour les utilisateurs finaux qui n’ont pas vu leur encore augmenter l’abonnement.
5. Didacticiel : Le didacticiel plus obligatoire pour tout le monde. Pourquoi ne pas construire super didacticiels dans l’application, puis dans l’application par le biais de messages uniquement si l’utilisateur semble ne pas utiliser l’application ou a des difficultés à utiliser une fonctionnalité de déclencheur ?

## <a name="why-do-you-need-analytics-to-engage"></a>Pourquoi devez-vous analytique de s’engager ?

Comme vous avez peut-être réalisé à ce stade, effectuer une notification de transmission diffusion uniquement ne suffit pas. Le concept de base de l’Engagement Mobile est d’aider les responsables et les développeurs s’engager avec l’utilisateur final de droite au bon moment et au bon endroit. Pour connaître ces trois concepts essentiels, il est essentiel à rassembler analytique à partir de votre application, puis l’utiliser pour segmenter votre audience. C’est également encore plus puissant lorsque les segments de comportement complète des données de votre base de données ou de la CRM ou à partir d’un canal entre. Engagement de Mobile permet la récupération des données de n’importe où et l’utilise pour cibler le bon public.

Pour être le plus contextuelles possible lorsque vous utilisez votre public, il est essentiel à la connaissance du comportement des utilisateurs finaux, de connaître leur état en temps réel. Collecte de données permet aux spécialistes du marketing vous consacrer véritablement à lire les scénarios d’utilisation et atteindre leurs objectifs de stratégie mobile d’engagement. Pour atteindre les objectifs jeu précédemment est également la raison pour laquelle la meilleure pratique est en fait ne pas pour rassembler tous les éléments et tout ce qui est dans l’analytique, mais uniquement ceux qui vous permettent de vous concentrer sur ce que vous voulez en savoir plus et vos scénarios d’utilisation. C’est le bon moyen pour démarrer, essayez, testez et apprenez à utiliser la solution et adresse de notification de transmission intelligente et augmenter la rétention d’une application pour l’amener à un niveau de réussite.

>[AZURE.NOTE] N’oubliez pas : Trop de données supprime des données !

### <a name="use-cases-and-best-practices"></a>Cas d’usage et les meilleures pratiques

Dans les sections suivantes, nous aborderons brièvement certaines clés-cas d’usage que nous avons accompli liste nos clients pour vous aider à démarrer.

#### <a name="media"></a>Médias

Collecter le type de contenu qui est consommée par l’utilisateur final et de segment puis l’audience en fonction de ce comportement à cible certains types de contenu qu’à un public qui est moins susceptible de consommer. Il permet d’éviter une base d’utilisateurs tout le spam et d’assurer une meilleure rétention.

#### <a name="m-commerce"></a>M-commerce

Collecter les sites les plus visités dans le public d’application et cible pour promouvoir une remise ou un nouveau produit dans cette catégorie que l’utilisateur final sera plus susceptible d’acheter des catégories de produits. Visent à augmenter le chiffre d’affaires. À nouveau, l’objectif est non au spam !

#### <a name="gaming"></a>Jeu

Collecter le niveau de jeu pour un utilisateur final et le temps passé dans une période donnée pour cibler le public qui pourrait être bloqué et risque de passer à un niveau supérieur avec une offre de bonus.

Communiquer les événements spécifiques à inciter les utilisateurs qui n’ont pas joué pendant un certain temps tenter de les encourager à retourner.

#### <a name="retail"></a>Vente au détail

Collecter les produits ou les marques une audience doit être plus susceptible de consommer en fonction des Favoris ou le comportement et le lecteur l’audience à votre banque pour augmenter les revenus de l’achat.

#### <a name="banking"></a>Opérations bancaires

Collecter des données à partir des utilisateurs finaux qui a créé un compte lors du premier lancement de l’application. Le but de déployer une stratégie de bienvenue avec une notification de transmission ciblée et d’augmenter le nombre d’abonnements de compte.

### <a name="how-to-create-a-great-tag-plan"></a>Comment faire pour créer un plan de balise excellent ?

Un plan de la balise doit être identique à une description de l’utilisateur-chemin d’accès ou un type de flux de travail de l’application, qui fournit toutes les balises nécessaires (données) qui doivent être collectés pour avoir suffisamment analytique pour comprendre le comportement de l’utilisateur et de segments correctement la base de l’utilisateur. Ce n’est pas un processus technique. Par conséquent, marketing est en mesure de spécifier les données à collecter en fonction de leur stratégie d’Engagement de Mobile.

Le minimum est de baliser au moins tous les écrans (appelés des *activités* dans Mobile Engagement) d’une application. Cela permet de déterminer le chemin d’accès utilisateur.

Une activité peut incorporer des *événements* qui collectent des informations telles que le clic sur un bouton d’action. Ainsi, la collection d’interactions au sein de l’application. Par conséquent, les spécialistes sont en mesure de savoir quel écran utilisateurs visitent et ce qu’ils font.

`Jobs`sont des actions dont la durée. Ceci est très utile de comprendre la durée d’un utilisateur de créer un compte ou connexion par exemple sont. Cela peut également être utile aux développeurs de contrôler le temps nécessaire pour appeler un service web.

`Errors`peut également être surveillée pour savoir si les utilisateurs rencontrent des problèmes dans votre application. Par exemple, l’obtention des problèmes de connexion fréquentes.

Tous de ce type de données peuvent être augmentés avec les paramètres (*informations supplémentaires* lors de l’Engagement Mobile) qui vous permet de regrouper des données dynamiques à partir de l’application. Ceci est important permettre la segmentation précise. Par exemple, marketing peut segmenter un utilisateur en fonction du type de contenu qu’ils ont consommé. Le type de contenu seront les informations dynamiques d’une activité ou un événement.

*Informations de l’application* sont des données qui vous permet de confirmer l’état de l’application ou de l’utilisateur en temps réel. Cela permet également de classer une base d’assistance et de cibler rapidement. Par exemple, il peut utiliser un état true/false si l’utilisateur se connecte ou non, ou la date d’expiration de son abonnement.

#### <a name="example-of-tags"></a>Exemple de balises

*Cas d’utilisation : Comportement d’audience à cibler le droit pour l’utilisateur final avec le contenu de la notification push droite de Segment*

1.  Envoyer une notification de transmission pour promouvoir une catégorie de produit : collecter des données de comportement à public du segment en fonction de la catégorie de produits qu’ils ont visités x fois dans une période donnée ou un élément spécifique qu’ils ont ajoutées dans un panier. Les données collectées permettent de segmenter et d’envoyer une notification de transmission au public approprié.
2.  L’application de taux : collecter des données en fonction du contenu partagé par le public sur un réseau social. Vise à segmenter l’audience en déterminant les *ambassadeurs* de votre application. À l’aide d’une push notification dans l’application, l’ambassadeurs sont le meilleur public de votre application à poser évaluer votre étoiles app 5 dans le magasin.

    ![][1]

*Cas d’utilisation : Données déclaratives*
1.  Segment d’alertes news : collecter des données déclaratives à public du segment en fonction de leurs préférences. Il autorise l’envoi de notification de transmission d’une rubrique spécifique qui intéresse vraiment une audience spécifique.
2.  Public du segment en fonction de l’état de la connexion. Collecter des données pour savoir si un utilisateur est connecté ou qu’il a créé un compte. Permet aux utilisateurs finaux de la cible qui ne sont pas encore connectés et envoie une notification d’émission à encourager les utilisateurs finaux à convertir.
    ![][2]

### <a name="next-steps"></a>Étapes suivantes

- Consultez les [Concepts d’Engagement Mobile] pour en savoir plus sur les concepts de base de Mobile Engagement.
- Visitez le site [créer un Engagement de Mobile App](mobile-engagement-create.md) pour créer une nouvelle Collection de App Engagement Mobile dans Azure et à commencer la gestion de vos applications avec le portail Mobile Engagement.
- Visitez les [meilleures pratiques](mobile-engagement-getting-started-best-practices.md) dans les détails.
- Reportez-vous au [scénario de l’application de jeu](mobile-engagement-gaming-scenario.md) pour en savoir plus sur l’implémentation de Mobile Engagement avec une application de jeu échantillon. 
- Reportez-vous au [scénario de l’application multimédia](mobile-engagement-media-scenario.md) pour en savoir plus sur l’implémentation de Mobile Engagement avec un exemple d’application support. 
- Consultez des [didacticiels] pour en savoir plus sur la mise en oeuvre.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Concepts d’Engagement mobile]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Didacticiels]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

