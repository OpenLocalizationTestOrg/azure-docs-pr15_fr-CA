<properties
    pageTitle="Engagement de Mobile Azure comparaison avec d’autres services Azure similaires"
    description="Engagement de Mobile Azure comparaison avec d’autres services Azure similaires - HockeyApp, AppInsights, concentrateurs de Notification"
    services="mobile-engagement"
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Engagement de Mobile Azure comparaison avec d’autres services Azure similaires

La liste des services offerts par Microsoft Azure jamais en expansion et parfois vous vous demandez peut-être comment diffère Azure Mobile Engagement cet autre service que vous venez de lire ou entendrez sur. Cet article tente de supprimer la confusion et vous dirige à choisir Azure Mobile Engagement lorsqu’il est plus approprié pour votre utilisation. 
 
Engagement de Mobile Azure est un service ciblé spécifiquement **pour marketing/CMOs numériques** , mais pourrait être utilisé par n’importe quel **propriétaire d’application mobile ou publisher** qui souhaite augmenter l’utilisation, la rétention et la monétisation de leurs applications mobiles. 

*Il s’agit d’une plate-forme de contrat utilisateur software as a service (SaaS) qui offre un aperçu utilisation app, segmentation de l’utilisateur en temps réel, contrôlée par les données et permet des notifications de type Pousser en contexte prenant en charge et dans l’application de messagerie.* 

Décomposer cette définition, nous avons les caractéristiques clés suivantes qui met également en évidence sa proposition de valeur unique :

1.  **Notifications de type Pousser en contexte prenant en charge et dans l’application de messagerie**
        
    Il s’agit principalement du produit - effectuer des notifications de transmission ciblés et personnalisés. Et, pour ce faire, nous collecter des données de riches analytique comportementale. 

2.  **Contrôlée par les données de perspectives dans l’utilisation de l’application**

    Nous fournissons des plates-formes et kits de développement logiciel pour collecter l’analytique comportementale sur les utilisateurs de l’application. Notez l’analytique comportementale du terme (aux analytique des performances), car nous intéresser à la manière dont les utilisateurs de l’application sont à l’aide de l’application. Nous recueillons les données d’analytique de performances de base sur les erreurs, les blocages, etc. mais c’est pas principalement du produit. 

3.  **Segmentation des utilisateurs en temps réel**

    Une fois que vous avez collecté les données d’analytique comportementale app utilisateurs, nous vous permettent de segment votre public en fonction de divers paramètres et des données recueillies à vous permettent de lancer des campagnes de push ciblée. 

4.  **Logiciels-as-a-service (SaaS) :**

    Nous avons un portail séparé à partir du portail de gestion Azure qui est optimisé pour interagir et afficher enrichi analytique comportementale sur les utilisateurs de l’application et exécuter le push campagnes marketing. Le produit est conçu pour vous lancer sans délai !   
 
Avec cet ensemble de différenciation dans main, voici comment nous comparer par rapport à d’autres offres Azure apparemment semblables - Remarque que l’article ne fait pas une comparaison de niveau fonctionnalité, mais prend un scénario plus en fonction de l’approche pour définir quel produit fonctionne mieux :
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) est la solution de Microsoft mobile DevOps. Avec, vous pouvez distribuer des builds bêta-testeurs, collecter les données de panne et obtenir les commentaires des utilisateurs. Il s’intègre également avec Visual Studio Team Services permettant des déploiements de génération simple et intégration des éléments de travail. 
    
    Ici le focus est sur DevOps et collecte des données performance analytique sur les applications mobiles. Vous risquez avec intégration à la fois HockeyApps et Engagement Mobile dans votre application et qui ne sera pas inhabituel, même s’il existe un certain chevauchement dans les données collectées, principalement des produits est différente et ils vous aident à atteindre des objectifs différents pour vous.  

2.  [Aperçu de l’application](../application-insights/app-insights-overview.md) Si votre application mobile a un côté serveur, puis vous utiliserez des perspectives d’Application pour surveiller le côté du serveur web de votre application, mais pour les applications mobiles côté client, vous devez utiliser HockeyApp. 

3.  [Concentrateurs de notification](https://azure.microsoft.com/services/notification-hubs/) fournit un service léger en ce sens que vous n’avez pas besoin d’un kit de développement logiciel intégré dans l’application mobile et vous pouvez avoir un contrôle total de votre application mobile, et il autorise l’envoi de notifications de type Pousser avec des fonctionnalités de balisage de base. C’est formidable pour n’importe quel propriétaire app attentive de moins sur le ciblage et plus informations envoi/communiquer instantanément à leurs utilisateurs de l’application (diffusion à une population importante). 

    Notez ici le focus lors de l’envoi d’obtenir éblouissantes notifications rapides avec la fonctionnalité de segmentation de base. 

Examinons certains scénarios :

1.  Tim fait partie de l’équipe marketing numérique au magasin Adventure Works qui publie des applications mobiles pour les utilisateurs. Les objectifs de TIM sont pour vous assurer que les utilisateurs qui téléchargent leurs applications mobiles continuent à l’utiliser et fréquemment. Ce augmente pas uniquement une marque joindre avec les utilisateurs de l’application mais aussi monétisation d’augmente lorsque les utilisateurs de l’application effectuent des achats à l’aide de l’application mobile. Pour ce Tim devez envoyer des notifications ciblées pour les utilisateurs de l’application, qui ils utile, de les inciter à ouvrir l’application et y revenir souvent. Il s’agit d’où Tim sera utile Mobile Engagement. 

2.  JoAnn fait partie de l’équipe de développement des applications mobiles d’Adventure Works et est à la recherche d’un produit pour plus d’informations sur les blocages, des exceptions, ouvrir une session et obtenir de télémétrie de performances à partir d’une application. Il s’agit d’où Joann utiles HockeyApp. JoAnn pourrait intégrer les HockeyApp pour ses scénarios types de développeur ciblé et Engagement de Mobile Azure pour le Tim dans la même application de tirer le meilleur des deux. 

3.  Robin fait partie de l’équipe de développement des applications mobiles au réseau de Contoso News et tous qu’elle veut consiste à envoyer des alertes nouvelles la rupture pour tous les utilisateurs sans quantité segmentation dès le déclenchement de l’alerte de news. Il s’agit d’où Robin utiles concentrateurs de Notification. Dans ce scénario, il est possible cependant que comme l’application mobile évolue, il est nécessaire que pour faire beaucoup plus riche segmentation et obtenir des détails sur le comportement de l’utilisateur de l’application. À ce stade, Robin devront évaluer Azure Mobile Engagement. 
 
Pour résumer, l’objectif de la mission de Mobile n’est pas juste pour collecter analytique - il n’est pas « encore un autre produit d’Analytique à partir de Microsoft ». C’est sur l’envoi de notifications de type Pousser ciblée et pour le ciblage, nous recueillons les données d’analytique comportementale, mais le focus reste sur l’envoi de notifications de type Pousser qui rendent la plus pertinente pour les utilisateurs de l’application afin qu’il n’est pas fourni comme étant du spam. 

Pour plus de détails - Examinez cette [vidéo rapide](mobile-engagement-overview.md) sur Mobile Engagement en bref. 

