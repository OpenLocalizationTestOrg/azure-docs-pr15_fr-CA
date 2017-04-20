<properties 
    pageTitle="Mise en œuvre de Mobile Engagement Azure pour application multimédia"
    description="Scénario d’application Media pour implémenter l’Engagement de Mobile Azure" 
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

#<a name="implement-mobile-engagement-with-media-app"></a>Implémenter l’Engagement Mobile avec application multimédia

## <a name="overview"></a>Vue d’ensemble

John est un gestionnaire de projet mobile pour une société multimédia volumineux. Il a récemment lancé une nouvelle application qui a un nombre très élevé de téléchargement. Il a atteint ses objectifs pour le téléchargement, mais toujours son retour sur Investment(ROI) par l’utilisateur ne répond pas à ses besoins. 

John a déjà identifié pourquoi son retour sur investissement est trop faible. Les utilisateurs arrêter de fréquemment à l’aide de son application seulement 2 semaines et la plupart d'entre eux ne jamais revenir. Il souhaite augmenter la rétention de son application.

Après que la partie initiale du test, qu’il a apprises lorsqu’il engage ses utilisateurs avec des notifications de type Pousser, ils ont tendance à continuer à utiliser son application. Également les utilisateurs inactifs revient souvent à l’application en fonction des notifications, qu'il les envoie. John décide d’investir dans une sorte de programme d’Engagement pour son application qui utilise le ciblage avancées avec les notifications de transmission.

John a lu récemment l' [Engagement de Mobile Azure - Guide de démarrage rapide avec les meilleures pratiques](mobile-engagement-getting-started-best-practices.md) et a décidé d’implémenter les recommandations de ce guide.

##<a name="objectives-and-kpis"></a>Objectifs et des indicateurs de performance clés

Les principales parties prenantes pour application de Jean satisfaire. Business est généré à partir de publicités que son support consommée par les utilisateurs. En augmentant le contenu consommé par utilisateur, John augmente son chiffre d’affaires. Tous s’accordent sur l’un des objectifs principaux : À accroître les ventes à partir des annonces de 25 %. Ils créent l’entreprise indicateurs de Performance clés (KPI) pour mesurer et amener cet objectif

* Nombre de publicités que vous cliquez sur par utilisateur
* Le nombre de pages de l’article visité (par utilisateur / par session / par semaine / par mois...)
* Quelles sont les catégories favorites

En fonction de la réunion de John avec les principales parties prenantes qu’il a défini des KPI de son entreprise. Il suit la partie 1 de l' [Engagement de Mobile Azure - Guide de démarrage rapide avec les meilleures pratiques](mobile-engagement-getting-started-best-practices.md). 

Ensuite, il crée les KPI Engagement suivant pour vous assurer que les objectifs ont été atteints :

* Surveiller la rétention sur les intervalles suivants : quotidienne, hebdomadaire, bimensuelle et mensuelle.
* Nombre d’utilisateurs actifs
* L’évaluation de l’application dans l’application stocke

Fondée sur les recommandations de l’équipe, les indicateurs de performance clés techniques suivantes ont été ajoutées pour répondre aux questions suivantes :

* Quel est mon chemin d’accès de l’utilisateur (la page est visitée, combien d’utilisateurs temps consacré il)
* Nombre d’incidents ou bogues rencontrés par session ?
* Quelles sont les versions du système d’exploitation exécutant mes utilisateurs ?
* Quelle est la taille moyenne d’un écran pour mes utilisateurs ?
* Quel type de connexions internet ont mes utilisateurs ?

Pour chaque indicateur de performance clé, il classe les données requises et il enregistre dans l’emplacement approprié de son manuel.

## <a name="engagement-program-and-integration"></a>Intégration et programme d’engagement

Maintenant que John a terminé ses indicateurs de performance clés, il commence sa phase de stratégie Engagement en définissant des programmes d’engagement 4 et leurs objectifs :    ![][1]

Puis John va plus loin en détaillant les notifications de transmission pour chaque programme. Notification de transmission sont définis par les cinq éléments :

1. Objectif : quel est l’objectif de la notification
2. Comment l’objectif sera atteint
3. Cible : qui recevra la notification ?
4. Contenu : Quel est le libellé et le format de la notification (dans App/Out de App)
5. Quand : quel est le meilleur moment pour envoyer cette notification de transmission

    ![][2]

Pour plus d’informations consultez les [règles](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Conformément à la partie 2 du livre blanc Jean utilise la section cible pour définir quelles données il doit collecter et écrit son Plan de balise conjointement avec l’équipe informatique à mettre en œuvre la solution. 1 semaine de mise en œuvre et le test d’acceptation utilisateur, John peut lancer enfin de ses programmes.

##<a name="program-results"></a>Résultats du programme

4 mois plus tard, Jean passe en revue les performances des programmes. Le programme d’accueil et le programme hebdomadaires sont objectifs de son. Le nombre d’utilisateurs à une seule session diminue, plus les fonctionnalités de l’application sont utilisées et le nombre de connexions par semaine a doublé.

Le **Programme inactif** est d’aider John comprendre les tendances de l’utilisateur. Il apparaît que 15 % des utilisateurs inactifs revenir à l’application. Toutefois la plupart d'entre elles ne reste pas active plus de 1 mois. John prévoit une optimisation potentielle de cette séquence avec des notifications supplémentaires et en développant son choix de contenu.

Le **Programme de découverte** ne fonctionne pas correctement. Il augmente la vente mais pas suffisamment pour atteindre ses objectifs. John identifie qu’il n’a pas suffisamment de données pour les rendre pertinents de ciblage et de proposer du contenu approprié. Il arrête ce programme et se concentre sur l’envoi de « les notifications de transmission éditoriale » avec Azure Mobile Engagement. Son journalistes ont déjà une solution CMS pour envoyer des notifications de transmission, et ils ne souhaitent pas modifier.

John décide d’utiliser l’API d’atteindre une API reste HTTP qui permet la gestion des campagnes de portée sans avoir à utiliser l’interface Web de AZME. Avec cette approche, John peut collecter les données qu’il a besoin et permettre son writers continuer à utiliser la solution CMS.

Pour vous assurer que la fonctionnalité fonctionne correctement, John demande équipe informatique en permanence sur les points suivants :

1. **Systèmes d’exploitation** : elles ont toutes leurs propres règles pour administrer des notifications de type Pousser, John décide de la liste de tous les cas et vérifie si les API de la gérer.
Par exemple : Système de transmission Android permet de présentation qui n’est pas le cas avec les e/s.

2. **Délai**: Jean souhaite une API, définie le cadre temporel et définie une fin aux campagnes. Il souhaite conserver les utilisateurs à partir d’une notification d’interruption de l’activité bombing.

3. **Catégories**: équipe de Marketing prépare un modèle pour chaque type d’alerte. John demande l’équipe informatique pour définir les catégories à l’intérieur de l’API.

Après quelques essais, John est satisfait. Grâce à cette API, les journalistes peuvent toujours envoyer des notifications de type Pousser avec leurs CMS et l’Engagement de Mobile Azure collecte toutes les données de comportement pour les

Après ces 4 premier mois, résultats reflètent une bonne globale performances et donne la confiance pour Paul et sa carte, le ROI par utilisateur augmente par 15 % et mobiles représentent 17,5 % du total des ventes, une augmentation de 7,5 % en quatre mois uniquement.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
