<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - vue d’ensemble | Microsoft Azure"
    description="Vue d’ensemble et plan de contenu de guide de considérations de conception hybride identité"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considérations de conception identité Azure Active Directory hybride

Les périphériques basés sur le consommateur sont multiplient de manière le monde d’entreprise et nuage-software as a service (SaaS) des applications sont faciles à adopter. Par conséquent, en gardant le contrôle d’accès des utilisateurs application sur plusieurs plates-formes de centres de données et de nuage internes est difficile.  Les solutions d’identité de Microsoft s’étendent sur les sites et les fonctionnalités basées sur le nuage, création d’une identité d’utilisateur unique pour l’authentification et l’autorisation à toutes les ressources, indépendamment de l’emplacement. Nous appelons cette identité hybride. Il existe différente conception et options de configuration pour l’identité hybride à l’aide de solutions de Microsoft et dans certains cas, qu'il peut être difficile de déterminer quelle combinaison mieux répondre aux besoins de votre organisation. Ce Guide de considérations de conception hybride identité vous aidera à comprendre comment concevoir une solution d’identification hybride qui correspond le mieux à l’entreprise et la technologie a besoin de votre organisation.  Ce guide décrit en détail une série d’étapes et les tâches que vous pouvez suivre pour vous aider à concevoir une solution d’identification hybride qui répond aux exigences uniques de votre organisation. Tout au long de la procédure et les tâches, le guide présente les technologies appropriées et les options des fonctions disponibles pour les organisations à la conférence fonctionnel et de qualité de service (par exemple, la disponibilité, l’évolutivité, performances, la facilité de gestion et de sécurité) niveau requis. Plus précisément, le guide objectifs de considérations relatives à la conception hybride identité sont à répondre aux questions suivantes : 

- Quelles sont les questions à poser et répondre à une conception hybride identité spécifique pour un domaine de problème ou de la technologie qui répond à mes exigences au lecteur dois-je ?
- Séquence d’activités dois-je effectuer pour concevoir une solution d’identité hybride pour le domaine de la technologie ou le problème ? 
- Quelles options de configuration et de la technologie d’identité hybride sont disponibles pour m’aider à répondre à mes besoins ? Quels sont les compromis entre ces options afin que je peux sélectionner la meilleure option pour mon entreprise ?


## <a name="who-is-this-guide-intended-for"></a>Qui est ce guide destiné ?
 Responsable de l’information, CITO, Chief identité architectes, architectes d’entreprise, architectes informatiques chargés de concevoir une solution d’identification hybride pour les entreprises moyennes ou grandes.

## <a name="how-can-this-guide-help-you"></a>Comment ce guide peut vous aider ? 
Vous pouvez utiliser ce guide pour apprendre à concevoir une solution d’identité hybride qui est en mesure d’intégrer un système de gestion des identités de nuage de points basée à votre solution d’identité local en cours. Le graphique suivant illustre un exemple une solution d’identification hybride permettant aux administrateurs informatiques à gérer pour intégrer leurs solutions Windows Server Active Directory en cours situé sur le site avec Microsoft Azure Active Directory pour permettre aux utilisateurs d’utiliser Single Sign-On (SSO) entre les applications dans le nuage et sur site.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


L’illustration ci-dessus est un exemple d’une solution d’identification hybride qui exploite les services en nuage pour s’intégrer avec des capacités sur site afin de fournir une expérience unique pour le processus d’authentification utilisateur final et de faciliter informatiques de gestion de ces ressources. Bien que cela puisse être un scénario très courant, conception d’identité de chaque organisation hybride est susceptible d’être différent de celui de l’exemple illustré dans la Figure 1 en raison des exigences différentes. Ce guide fournit une série d’étapes et les tâches que vous pouvez suivre pour concevoir une solution d’identité hybride qui répond aux exigences uniques de votre organisation. Dans les étapes suivantes et les tâches, le guide présente les technologies appropriées et les options de la fonctionnalité disponibles pour répondre aux fonctionnel et exigences au niveau de qualité de service pour votre organisation.

**Hypothèses**: avoir une certaine expérience avec Windows Server, Active Directory Domain Services et Azure Active Directory. Dans ce document, nous supposons que vous cherchez comment ces solutions peuvent répondre à vos besoins professionnels sur leurs propres ou dans une solution intégrée.

## <a name="design-considerations-overview"></a>Vue d’ensemble des considérations relatives à la conception
Ce document fournit un ensemble d’étapes et les tâches que vous pouvez suivre pour concevoir une solution d’identité hybride qui répond le mieux à vos besoins. Les étapes sont présentées dans un ordre précis. Vous découvrez dans des étapes ultérieures de conception pouvant vous obliger à modifier les décisions que vous prenez dans les étapes précédentes, toutefois, en raison des choix de conception en conflit. Chaque tentative est faite pour vous signaler les conflits potentiels de conception dans tout le document. 

Vous arrivera à la conception qui répond à vos besoins uniquement après l’itération à travers les étapes autant de fois que nécessaire pour intégrer toutes les considérations au sein du document. 

| Phase d’identité hybride                                             | Liste des rubriques                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Déterminer les besoins d’identité                                   | [Déterminer les besoins de l’entreprise](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Déterminer les besoins de synchronisation d’annuaire](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Déterminer les besoins d’authentification à plusieurs facteurs](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Définir une stratégie d’adoption identité hybride](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Plan d’amélioration de la sécurité des données grâce à la solution de forte identité | [Déterminer les exigences de protection de données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Déterminer les exigences en matière de gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Déterminer les exigences de contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Déterminer les exigences en matière de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Définir la stratégie de protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Plan du cycle de vie des identités hybride                                | [Déterminer les tâches de gestion d’identité hybride](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestion de la synchronisation](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Détermination de la stratégie d’adoption hybride identity management](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Téléchargez ce guide
Vous pouvez télécharger une version pdf du guide de considérations de conception hybride identité à partir de la [Galerie Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             
