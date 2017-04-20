<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les besoins d’identité | Microsoft Azure"
    description="Identifier les besoins de la société qui vous guideront pour définir les conditions requises pour la conception d’identité hybride."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins d’identité pour votre solution d’identité hybride
La première étape dans la conception d’une solution d’identification hybride est de déterminer les exigences de l’organisation de l’entreprise qui va exploiter cette solution.  Identité de hybride démarre en tant qu’un rôle auxiliaire (il prend en charge toutes les autres solutions de nuage grâce à l’authentification) et passe à fournir des fonctionnalités nouvelles et intéressantes que la déverrouiller les nouvelles charges de travail pour les utilisateurs.  Ces charges de travail ou les services que vous souhaitez adopter pour vos utilisateurs détermine les conditions requises pour la conception d’identité hybride.  Ces charges de travail et les services doivent exploiter les identités hybride à la fois sur site et dans le cloud.  

Vous devez passer en revue les aspects clés de l’entreprise de comprendre qu’il est désormais obligatoire et que la société prévoit pour l’avenir. Si vous n’avez pas la visibilité de la stratégie à long terme pour la conception d’identité hybride, sans doute que votre solution ne sera pas évolutive en fonction des besoins de l’entreprise se développent et évoluent.   T le diagramme ci-dessous présente un exemple d’une architecture d’identité hybride et les charges de travail qui sont en cours disponibles pour les utilisateurs. Il s’agit juste d’un exemple de toutes les nouvelles possibilités qui peuvent être déverrouillés et livré avec une stratégie d’identité hybride solide. 
 
Certains composants qui font partie de l’architecture d’identité hybride![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Déterminer les besoins de l’entreprise
Chaque société a des exigences différentes, même si ces sociétés font partie du même secteur, l’entreprise réelle, configuration requise peut varier. Vous pouvez toujours tirer parti de meilleures pratiques de l’industrie, mais il est finalement des besoins de la société qui vous guideront pour définir les conditions requises pour la conception d’identité hybride. 

Veillez à répondre aux questions suivantes pour identifier les besoins de votre entreprise :

- Votre entreprise cherche à réduire les coûts d’exploitation informatique ?
- Votre entreprise cherche à sécuriser des ressources de cloud (les applications SaaS, infrastructure) ?
- Votre entreprise cherche à moderniser votre infrastructure informatique ?
  - Sont vos utilisateurs exigeants et les plus mobiles pour créer des exceptions dans votre zone DMZ pour autoriser différent type de trafic pour accéder aux différentes ressources informatiques ?
  - Votre société possède-t-elle des applications héritées qui doivent être publié pour ces utilisateurs modernes mais ne sont pas faciles à réécrire ?
  - Votre société a besoin pour accomplir toutes ces tâches et placez-le sous le contrôle en même temps ?
- Votre entreprise cherche à sécuriser l’identité des utilisateurs et réduire les risques en apportant de nouveaux outils qui tirent parti de l’expertise de la sécurité Azure/expertise sur site de Microsoft ?
- Votre société tente de se débarrasser des comptes « externes » redoutées de locaux et de les déplacer vers le nuage, où ils ne sont plus une menace latente dans votre environnement local ?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analyser l’infrastructure d’identité local
Maintenant que vous avez une idée en ce qui concerne les exigences commerciales de votre société, vous devez évaluer votre infrastructure d’identité sur site. Cette évaluation est importante pour la définition des exigences techniques pour intégrer votre solution d’identité en cours pour le système de gestion des identités de nuage. Veillez à répondre aux questions suivantes :

- Quelle solution d’authentification et d’autorisation est votre société utiliser sur site ? 
- Votre entreprise dispose actuellement des services de synchronisation sur site ?
- Est-ce que votre société utilise les fournisseurs d’identité tiers (IdP) ?

Vous devez également connaître les services en nuage que votre société possède peut-être. Il est très important d’effectuer une évaluation pour comprendre l’intégration en cours avec les modèles SaaS, IaaS ou PaaS dans votre environnement. Veillez à répondre aux questions suivantes lors de cette évaluation :
- Votre entreprise dispose-t-elle d’une intégration avec un fournisseur de services de cloud ?
- Si Oui, quels services sont utilisés ?
- Cette intégration n’est actuellement en production ou s’agit-il d’un projet pilote ?


>[AZURE.NOTE]
Si vous n’avez un mappage précis de toutes vos applications et services en nuage, vous pouvez utiliser l’outil de découverte d’application Cloud. Cet outil peut fournir le visibilité de toutes vos activités de l’entreprise et des applications de cloud consommateur de votre service informatique. Qui vous facilitera plus que jamais à découvrir ombre informatique de votre organisation, y compris les détails sur les modèles d’utilisation et de tous les utilisateurs accédant à vos applications en nuage. Pour accéder à cet outil accédez à [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Évaluer les besoins d’intégration identité
Ensuite, vous devez évaluer les exigences d’intégration de l’identité. Cette évaluation est importante de définir les exigences techniques pour comment authentifier des utilisateurs, aspect de présence de l’entreprise dans le nuage, comment l’entreprise permettra d’autorisation et que l’expérience utilisateur sera. Veillez à répondre aux questions suivantes :

- Votre organisation utilisera fédération, l’authentification standard ou les deux ?
- Fédération est-elle une exigence  En raison de ce qui suit :
 - Fonctionnalité SSO basée sur Kerberos
 - Votre société a une applications sur site (soit générée en interne ou 3e partie) qui utilise SAML ou les fonctionnalités de fédération similaires.
 - AMF via des cartes à puce. RSA SecurID, etc.
 - Règles d’accès client répondant aux questions ci-dessous :
     1. Puis-je bloquer tous les accès externe à Office 365, basé sur l’adresse IP du client ?
     1. Puis-je bloquer tous les accès externe à Office 365, à l’exception d’Exchange ActiveSync ?
     1. Puis-je bloquer tous les accès externe à Office 365, à l’exception des applications basées sur le navigateur (OWA, SPO)
     1. Puis-je bloquer tous les accès externe à Office 365 pour les membres des groupes d’annonces désignés
- L’audit de sécurité/les problèmes
- Investissements déjà réalisés dans authentification fédérée
- Le nom de notre organisation utilise pour notre domaine dans le nuage ?
- L’entreprise dispose-t-elle d’un domaine personnalisé ?
    1. Est ce domaine public et facilement vérifiable via DNS ?
    1. S’il n’est pas le cas, puis vous avez un domaine public qui peut être utilisé pour inscrire un autre UPN dans Active Directory ?
- Les identificateurs d’utilisateur ne sont cohérents pour la représentation sous forme de nuage ? 
- L’entreprise dispose-t-elle d’applications qui nécessitent une intégration avec les services cloud ?
- L’organisation possède plusieurs domaines, et ils utilisent l’authentification standard ou fédérée ?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Évaluer des applications qui s’exécutent dans votre environnement
Maintenant que vous avez une idée concernant vos locaux et cloud d’infrastructure, vous devez évaluer les applications qui s’exécutent dans ces environnements. Cette évaluation est importante de définir les exigences techniques pour l’intégration de ces applications pour le système de gestion des identités de nuage. Veillez à répondre aux questions suivantes :

- Où résidera nos applications ?
- Les utilisateurs accédant à applications sur site ?  Dans le nuage ? Ou les deux ?
- Des projets de prendre les charges de travail d’application existants et de les déplacer vers le nuage ?
- Existe-t-il des plans pour développer de nouvelles applications qui résident sur site ou dans le cloud qui utilisera le cloud l’authentification ?

## <a name="evaluate-user-requirements"></a>Évaluer les besoins des utilisateurs
Vous devez également évaluer les besoins des utilisateurs. Cette évaluation est importante de définir les étapes qui seront nécessaires pour l’intégration et l’assistance des utilisateurs qu’ils la transition vers le nuage. Veillez à répondre aux questions suivantes :

- Les utilisateurs accédant à applications sur site ?
- Les utilisateurs accéderont les applications dans le nuage ?
- Comment les utilisateurs généralement se connecter à leur environnement local ?
- Comment seront les utilisateurs ouvrez une session dans le nuage ?

>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et de comprendre le raisonnement derrière la réponse. [Exigences en matière de réponse aux incidents de déterminer](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) iront sur les options disponibles et le pour et le contre de chaque option.  En ayant répondu à ces questions que vous sélectionnez option les adaptée à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
[Déterminer les besoins de synchronisation d’annuaire](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
