<properties 
    pageTitle="Mise en œuvre de Azure Mobile Engagement pour l’application de jeu"
    description="Scénario d’application pour implémenter Azure Mobile Engagement de jeu" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

#<a name="implement-mobile-engagement-with-gaming-app"></a>Implémenter l’Engagement Mobile avec application de jeu

## <a name="overview"></a>Vue d’ensemble

Un démarrage du jeu a lancé une nouvelle application de jeu de role-play/stratégie en fonction de pêche. Le jeu a été en cours d’exécution depuis 6 mois. Ce jeu est un énorme succès et il a des millions de téléchargements et la rétention est très élevée par rapport à d’autres applications de jeu de démarrage. Lors de la réunion de révision trimestrielles, les parties prenantes conviennent qu’ils doivent augmenter le revenu moyen par utilisateur (ARPU). Dans le jeu Premium sont disponibles sous la forme d’offres spéciales. Ces packs de jeu permettent aux utilisateurs de mettre à niveau l’apparence et les performances de leurs lignes de pêche et les appâts ou les s’attaque dans le jeu. Toutefois, les ventes de package sont très faibles. Ils décident tout d’abord analyser la satisfaction client grâce à un outil analytique, et puis pour développer un engagement de programme pour augmenter les ventes à l’aide des avancées segmentation.

En fonction de l' [Engagement de Mobile Azure - Guide de démarrage rapide avec les meilleures pratiques](mobile-engagement-getting-started-best-practices.md) qu’ils créent une stratégie d’engagement.

##<a name="objectives-and-kpis"></a>Objectifs et des indicateurs de performance clés

Les principales parties prenantes pour le jeu satisfaire. Tous s’accordent sur l’un des objectifs principaux - pour augmenter les ventes de package prime de 15 %. Ils créent l’entreprise indicateurs de Performance clés (KPI) pour mesurer et amener cet objectif

* Sur quel niveau de la partie ces packages sont achetés ?
* Quel est le chiffre d’affaires par utilisateur, par session, par semaine et par mois ?
* Quels sont les types de fournisseur préféré ?

La partie 1 du [Guide de démarrage](mobile-engagement-getting-started-best-practices.md) explique comment définir les objectifs et les indicateurs de performance clés. 

Avec les indicateurs de performance clés Business maintenant définis, le chef de produit Mobile crée Engagement indicateurs de performance clés pour déterminer la rétention et les nouvelles tendances de l’utilisateur.

* Surveiller la rétention et l’utiliser dans les intervalles suivants : tous les jours, tous les 2 jours, hebdomadaire, mensuelle et tous les 3 mois
* Nombre d’utilisateurs actifs
* L’évaluation de l’application dans le magasin

Fondée sur les recommandations de l’équipe, les indicateurs de performance clés techniques suivantes ont été ajoutées pour répondre aux questions suivantes :

* Quel est mon chemin d’accès de l’utilisateur (qui est consultée, l’utilisateurs consacrent dessus)
* Nombre d’incidents ou bogues rencontrés par session
* Quelles sont les versions du système d’exploitation exécutant mes utilisateurs ?
* Quelle est la taille moyenne d’un écran pour mes utilisateurs ?
* Quel type de connectivité internet ont mes utilisateurs ?

Pour chaque indicateur de performance clé le chef de produit Mobile spécifie les données dont elle a besoin et où il se trouve dans son manuel.

## <a name="engagement-program-and-integration"></a>Intégration et programme d’engagement

Avant de créer un programme d’engagement avancées, le directeur de projet Mobile responsable du projet doit avoir une bonne connaissance de la procédure et, lorsque les produits sont consommés par les utilisateurs.

Après 3 mois, le directeur de projet Mobile a collecté suffisamment de données pour améliorer ses ventes de notification dans l’application push. Il apprend que :

* Le premier achat s’effectue généralement au niveau du 14. 90 % des cas, l’achat est nouvelles armes légendaires pour 3 $.
* De 80 % des cas, les utilisateurs ayant effectué un achat, continuez avec le produit et le rendre plus achats.
* Les utilisateurs qui ont dépassé le niveau 20, démarrer à dépenser plus de $10/ semaine.
* Les utilisateurs ont tendance à acheter des progiciels de prime au niveau 16, 24 et 32.

Grâce à cette analyse, le directeur de projet Mobile décide de créer des séquences de notification à l’augmentation des ventes de l’application d’émetteurs spécifiques. Il crée trois séquences d’émission qu’il appelle : Bienvenue dans le programme, programme de vente et programme inactif. Pour plus d’informations, consultez les [règles](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
