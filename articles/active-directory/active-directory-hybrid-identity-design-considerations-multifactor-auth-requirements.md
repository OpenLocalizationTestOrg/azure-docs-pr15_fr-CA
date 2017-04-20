<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les besoins d’authentification à plusieurs facteurs"
    description="Avec contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Lorsque ces conditions sont réunies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins d’authentification à plusieurs facteurs pour votre solution d’identité hybride

Dans ce monde de la mobilité, avec des utilisateurs qui accèdent à des données et des applications dans le nuage et à partir de n’importe quel périphérique, sécurisation de ces informations jouent un rôle essentiel.  Chaque jour est un nouveau titre sur une faille de sécurité.  Bien qu’il n’existe aucune garantie contre ces violations, une authentification multifacteur, fournit une couche supplémentaire de sécurité afin d’empêcher les violations.
Commencez par évaluer les besoins des entreprises pour une authentification à plusieurs facteurs. Autrement dit, que l’organisation tente de sécuriser.  Cette évaluation est importante de définir les exigences techniques pour installer et activer les utilisateurs des organisations pour l’authentification à plusieurs facteurs.

>[AZURE.NOTE]
Si vous n’êtes pas familiarisé avec AMF et ce qu’il fait, il est fortement recommandé de lire l’article [Nouveautés Azure une authentification multifactorielle ?](../multi-factor-authentication/multi-factor-authentication.md) avant de poursuivre la lecture de cette section.

Assurez-vous de répondre les éléments suivants :

- Votre société tente de sécuriser des applications de Microsoft ? 
- Comment ces applications sont publiées ?
- Votre société fournit-elle des accès à distance pour permettre aux employés d’accéder aux applications de locaux ?

Si Oui, de quel type d’accès à distance ? Vous devez également évaluer où se trouvera les utilisateurs qui accèdent à ces applications. Cette évaluation est une étape importante pour définir la stratégie d’authentification à plusieurs facteurs appropriés. Veillez à répondre aux questions suivantes :

- Où les utilisateurs vont trouver ?
- Ils se trouve ?
- Votre entreprise souhaite-t-elle établir des restrictions en fonction de l’emplacement de l’utilisateur ?

Une fois que vous comprenez ces exigences, il faut également évaluer les besoins de l’utilisateur pour l’authentification à plusieurs facteurs. Cette évaluation est importante car il définit les conditions requises pour le déploiement d’une authentification multifactorielle. Veillez à répondre aux questions suivantes :

- Sont les utilisateurs familiarisés avec une authentification multifactorielle ?
- Certaines utilisations seront requises pour fournir une authentification supplémentaire ?  
 - Si Oui, tout le temps, lorsque provenant des réseaux externes, ou l’accès à des applications spécifiques, ou dans d’autres conditions ?
- Les utilisateurs nécessiteront une formation sur la façon de configurer et implémenter une authentification multifactorielle ?
- Quels sont les scénarios clés que votre société souhaite activer l’authentification par plusieurs facteur pour leurs utilisateurs ?

Après avoir répondu à ces questions, vous serez en mesure de comprendre s’il y a plusieurs facteurs d’authentification déjà mis en œuvre sur site. Cette évaluation est importante de définir les exigences techniques pour installer et activer les utilisateurs des organisations pour l’authentification à plusieurs facteurs. Veillez à répondre aux questions suivantes :

- Votre entreprise doit-elle protéger des comptes privilégiés avec AMF ?
- Votre société a besoin pour activer AMF pour certaines application, pour des raisons de conformité ?
- Votre société a besoin pour activer AMF pour tous les utilisateurs de ces applications ou des administrateurs uniquement ?
- Peut-être avez-vous AMF toujours activé ou uniquement lorsque les utilisateurs ouvrent votre réseau d’entreprise ?


## <a name="next-steps"></a>Étapes suivantes
[Définir une stratégie d’adoption identité hybride](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
