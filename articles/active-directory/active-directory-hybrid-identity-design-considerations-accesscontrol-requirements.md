
<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les exigences de contrôle d’accès | Microsoft Azure"
    description="Couvre les piliers de l’identité et l’identification des besoins d’accès pour les ressources pour les utilisateurs dans un environnement hybride."
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

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Déterminer les exigences de contrôle d’accès pour votre solution d’identité hybride
Lorsqu’une organisation consiste à concevoir leur solution d’identité hybride ils peuvent également utiliser cette opportunité pour consulter les conditions d’accès pour les ressources qu’ils prévoient pour le rendre disponible pour les utilisateurs. L’accès aux données inter-domaines tous les quatre pierres angulaires de l’identité, qui sont :

- Administration
- Authentification
- Autorisation
- L’audit

Les sections qui suit couvrent d’authentification et autorisation dans plus de détails, d’administration et audit font partie du cycle de vie identité hybride. Lire les [tâches de gestion des identités déterminer hybride](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) pour plus d’informations sur ces fonctionnalités.

>[AZURE.NOTE]
Pour plus d’informations sur chacun d’eux de ces piliers, lisez [Les quatre piliers de l’identité - gestion des identités dans l’âge de l’informatique hybride](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) .

## <a name="authentication-and-authorization"></a>Authentification et autorisation
Il existe différents scénarios d’authentification et d’autorisation, ces scénarios aura des exigences spécifiques qui doivent être remplies par la solution d’identité hybride que la société va adopter. Scénarios de communication de (B2B) entreprise à entreprise peuvent ajouter un défi supplémentaire pour les administrateurs informatiques puisqu’il faut s’assurer que la méthode d’authentification et d’autorisation utilisée par l’organisation peut communiquer avec leurs partenaires commerciaux. Au cours du processus de conception pour les exigences d’authentification et d’autorisation, s’assurer que les questions suivantes sont traitées :

- Votre organisation s’authentifier et autoriser uniquement les utilisateurs situés à leur système de gestion d’identité ?
 - Existe-t-il des plans pour les scénarios B2B ?
 - Si Oui, déjà savoir quels protocoles (SAML, OAuth, Kerberos, les jetons ou les certificats) seront utilisés pour connecter les deux entreprises ?
- Est la solution hybride identité que vous allez adopter la prise en charge de ces protocoles ?

Un autre point important à prendre en compte est où se trouvera le référentiel d’authentification qui sera utilisé par les utilisateurs et les partenaires et à utiliser le modèle d’administration. Envisagez les options suivantes de la base de deux :
- Centralisée : dans ce modèle d’informations d’identification de l’utilisateur, les stratégies et l’administration peuvent être centralisée sur site ou dans le nuage.
- Hybride : dans ce modèle d’informations d’identification de l’utilisateur, les stratégies et l’administration sera centralisée sur site et un répliquées dans le nuage.

Le modèle de votre organisation adopte varient en fonction des besoins de leur entreprise, que vous souhaitez répondre aux questions suivantes pour identifier où réside le système de gestion d’identité et le mode d’administration à utiliser :

- Votre entreprise en possède une gestion des identités sur site ?
 - Si Oui, les planifier pour qu’elle reste ?
 - Existe-t-il des conditions réglementaires et de conformité que votre organisation doit suivre qui détermine où le système de gestion d’identité doit résider ?
- Votre organisation utilise-t-elle l’ouverture de session unique pour les applications que qui se trouve sur site ou dans le nuage ?
 - Si Oui, l’adoption d’un modèle d’identité hybride affecte-t-elle ce processus ?

## <a name="access-control"></a>Contrôle d’accès
Lors de l’authentification et l’autorisation sont des éléments fondamentaux pour permettre l’accès aux données d’entreprise par le biais de validation de l’utilisateur, il est également important de contrôler le niveau d’accès de ces utilisateurs auront et dotés du niveau des administrateurs de l’accès sur les ressources qu’ils gèrent. Votre solution d’identité hybride doit être en mesure de fournir un accès granulaire aux ressources, de délégation et de contrôle d’accès de base de rôle. S’assurer qu’en matière de contrôle d’accès sont répond à la question suivante :

- Votre entreprise a-t-elle à plus d’un utilisateur avec des privilèges élevés pour gérer votre système d’identité ?
 - Si Oui, chaque utilisateur doit-il le même niveau d’accès ?
- Votre société devez déléguer l’accès aux utilisateurs de gérer des ressources spécifiques ?
 - Si Oui, à quelle fréquence cela se passe-t-il ?
- Votre entreprise doit intégrer des fonctionnalités de contrôle d’accès entre les locaux et cloud ressources ?
- Votre société doit limiter l’accès aux ressources en fonction de certaines conditions ?
- Votre entreprise aurait à n’importe quelle application qui nécessite un accès à certaines ressources contrôle personnalisé ?
 - Si Oui, où ces applications se trouvent (sur site ou dans le nuage) ?
 - Si Oui, où sont ceux cibler des ressources (sur site ou dans le nuage) ?

>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et de comprendre le raisonnement derrière la réponse. [Définir une stratégie de Protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) risque de dépasser les options disponibles et les avantages et les inconvénients de chaque option.  En répondant à ces questions, vous allez sélectionner option que le mieux à vos besoins.

## <a name="next-steps"></a>Étapes suivantes

[Déterminer les exigences en matière de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
