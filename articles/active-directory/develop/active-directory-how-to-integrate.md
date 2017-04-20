<properties
   pageTitle="L’intégration avec Azure Active Directory | Microsoft Azure"
   description="Un guide sur les avantages et les ressources pour l’intégration à Active Directory de Azure."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="integrating-with-azure-active-directory"></a>Intégration avec Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory offre aux entreprises la gestion des identités à l’échelle de l’entreprise pour les applications en nuage.  Intégration de publicités par Azure permet à vos utilisateurs une expérience utilisateur agréable et permet à votre application de se conformer à la stratégie de l’informatique.

## <a name="how-to-integrate"></a>Comment intégrer

Il existe plusieurs façons pour votre application d’intégrer AD Azure.  Tirer parti des nombreux ou peu de ces scénarios est approprié pour votre application.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Prise en charge d’AD Azure comme un moyen de se connecter à votre Application

**Réduire la friction de connexion et de réduire les coûts de prise en charge.** À l’aide d’Active Directory Azure pour vous connecter à votre application, vos utilisateurs n’auront un nom plus et mot de passe à retenir.  En tant que développeur, vous aurez moins mot de passe pour stocker et protéger.  Ne pas avoir à gérer des réinitialisations de mot de passe oublié peut-être un seul des économies significatives.  AD Azure alimente signe dans des applications de cloud les plus populaires au monde, y compris Office 365 et Microsoft Azure.  Avec des centaines de millions utilisateurs des millions d’entreprises, chances sont de votre utilisateur est déjà connecté à AD Azure.  Pour en savoir plus sur la [prise en charge l’ajout de connexion AD Azure](../active-directory-authentication-scenarios.md).

**Simplifier le signe pour votre application.**  Lors de l’inscription de votre application, AD Azure peut envoyer des informations essentielles sur un utilisateur afin que vous pouvez renseigner votre formulaire d’inscription ou éliminer complètement.  Les utilisateurs peuvent s’inscrire pour votre application à l’aide de leur compte Azure AD via une expérience familière de consentement similaire à celles d’applications mobiles et les médias.  Tout utilisateur peut s’inscrire et vous connecter à une application qui est intégrée à AD Azure sans nécessiter d’implication des services informatiques.  Pour en savoir plus sur la [signature à distance de votre application pour la connexion du compte Active Directory d’Azure](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Rechercher des utilisateurs, gérer l’approvisionnement de l’utilisateur et contrôler l’accès à votre Application

**Rechercher des utilisateurs dans l’annuaire.**  L’API de graphique permet d’aider les utilisateurs à rechercher et Parcourir par d’autres personnes dans leur organisation, lorsque vous invitez d’autres personnes ou des adresses de l’octroi d’accès, plutôt que de type courrier électronique.  Les utilisateurs peuvent naviguer à l’aide d’une interface de style livre adresse familière, y compris la consultation des détails de la hiérarchie d’organisation.  Pour en savoir plus sur l' [API du graphique](../active-directory-graph-api.md).

**Réutiliser des groupes Active Directory et votre client déjà gère les listes de distribution.**  Azure AD contient les groupes que votre client est déjà à l’aide de distribution de courrier électronique et gestion de l’accès.  À l’aide de l’API de graphe, réutiliser ces groupes au lieu de demander votre client créer et gérer un ensemble distinct de groupes dans votre application.  Groupe informations peuvent également être envoyées à votre application dans les jetons de connexion.  Pour en savoir plus sur l' [API du graphique](../active-directory-graph-api.md).

**Utilisez une publicité Azure pour contrôler qui a accès à votre application.**  Les administrateurs et les propriétaires d’applications dans Active Directory Azure peuvent affecter des accès à des applications à des utilisateurs et groupes.  À l’aide de l’API de graphe, vous pouvez lire cette liste et l’utiliser pour contrôler la mise en service et mise hors service des ressources et accéder au sein de votre application.

**Utilisez AD Azure pour les rôles en fonction de contrôle d’accès.**  Les administrateurs et les propriétaires d’applications peuvent affecter des utilisateurs et des groupes aux rôles que vous définissez lorsque vous enregistrez votre application dans Azure AD.  Informations sur les rôles sont envoyées à votre application dans les jetons de connexion et peuvent également être lus à l’aide de l’API du graphique.  En savoir plus sur [AD Azure pour l’autorisation](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Accéder au profil de l’utilisateur, calendrier, messagerie, Contacts, fichiers et bien plus encore

**Azure est le serveur d’autorisation pour Office 365 et d’autres services d’entreprise de Microsoft.**  Si vous disposez de publicité Azure pour se connecter à votre application ou de la prise en charge de la liaison de vos comptes d’utilisateur en cours à des comptes d’utilisateur AD Azure à l’aide de OAuth 2.0, vous pouvez demander l’accès en lecture et écriture pour le profil d’un utilisateur, calendrier, messagerie, contacts, fichiers et autres informations.  Vous pouvez facilement écrire des événements dans le calendrier de l’utilisateur et lire ou écrire des fichiers sur leur OneDrive.  Plus d’informations sur [l’accès à l’API d’Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promouvoir votre Application Azure et Office 365 Marketplaces

**Promouvoir votre application à des millions d’entreprises qui utilisent déjà Active Directory Azure.**  Utilisateurs pour rechercher et parcourir ces marchés sont déjà l’un ou plusieurs services en nuage, ce qui les rend qualifié des clients du service nuage.  Pour en savoir plus sur la promotion de votre application sur [Le marché d’Azure](https://azure.microsoft.com/marketplace/partner-program/).

**Lorsque les utilisateurs s’inscrire pour votre application, il s’affiche dans leur panneau d’accès AD Azure et le Lanceur d’applications Office 365.**  Les utilisateurs pourront facilement et rapidement revenir à votre application, par la suite, améliorer l’engagement de l’utilisateur.  Plus d’informations sur [publicité Azure d’accès](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Dispositif d’intervention et de Service-Service de Communication sécurisée

**L’utilisation d’AD Azure pour la gestion des identités des périphériques et des services permet de réduire le code que nécessaire pour écrire et permet au service informatique pour gérer l’accès.**  Services et périphériques peuvent obtenir des jetons d’Azure AD à l’aide de OAuth et utiliser ces jetons pour accéder aux API de web.  Utilise Active Directory Azure peut ne pas écrire un code d’authentification complexe.  Étant donné que les identités des services et des périphériques sont stockées dans AD Azure, informatique peut gérer des clés et la révocation au lieu d’avoir à faire séparément dans votre application.

## <a name="benefits-of-integration"></a>Avantages de l’intégration

Intégration avec Active Directory Azure est fourni avec des avantages qui ne nécessitent pas d’écrire du code supplémentaire.

### <a name="integration-with-enterprise-identity-management"></a>Intégration avec la gestion des identités

**À votre application sont conformes avec les stratégies informatiques.**  Entreprises intégrer leurs systèmes de gestion d’identité avec AD Azure, et lorsqu’une personne quitte une entreprise, ils seront automatiquement perdre l’accès à votre application sans informatique ayant besoin d’effectuer des étapes supplémentaires.  INFORMATIQUE peut gérer qui peut accéder à votre application et déterminer quelles sont les stratégies d’accès sont nécessaires - pour exemple une authentification multifacteur - réduction de votre besoin d’écrire du code pour se conformer à des règles d’entreprise complexes.  AD Azure fournit aux administrateurs d’un journal d’audit détaillé de qui connecté à votre application donc informatique peut effectuer le suivi de l’utilisation.

**Annonce Azure étend Active Directory vers le nuage afin que votre application peut s’intégrer à Active Directory.**  De nombreuses entreprises du monde utilisent Active Directory comme leur principal connexion et système de gestion des identités et nécessitent leurs applications fonctionnent avec Active Directory.  Intégration avec Active Directory Azure, votre application s’intègre avec Active Directory.

### <a name="advanced-security-features"></a>Fonctionnalités de sécurité avancées

**Authentification à plusieurs facteurs.**  Annonce Azure offre natif une authentification multifactorielle.  Les administrateurs informatiques peuvent nécessiter plusieurs facteurs d’authentification pour accéder à votre application, afin que vous ne disposez pas de cette prise en charge de code vous-même.  Pour en savoir plus sur [l’Authentification à plusieurs facteurs](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Signe anormal dans la détection.**  Annonce Azure traite plus d’un milliard de signe-ins jour, lors de l’utilisation d’algorithmes d’apprentissage machine pour détecter les activités suspectes et de notifier les administrateurs informatiques des éventuels problèmes.  Prend en charge la connexion à Active Directory Azure, votre application obtienne le bénéfice de cette protection. Plus d’informations sur [l’affichage Azure Active Directory d’accéder aux rapports](../active-directory-view-access-usage-reports.md).

**Accès conditionnel.**  En plus de l’authentification multifactorielle, les administrateurs peuvent exiger des conditions particulières être remplies avant que les utilisateurs peuvent signer à votre application.  Les conditions qui peuvent être définies incluent la plage d’adresses IP de périphériques clients, l’appartenance aux groupes spécifiés et l’état de l’appareil utilisé pour l’accès.  Pour en savoir plus sur [l’accès conditionnel Azure Active Directory](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Développement facile

**Protocoles standard.**  Microsoft s’engage à prendre en charge les normes de l’industrie.  Annonce Azure prend en charge les protocoles d’authentification SAML 2.0, 1.0 de connexion OpenID, OAuth 2.0 et 1.2 de WS-Federation.  L’API de graphique est conforme à OData 4.0.  Si votre application fait déjà prend en charge les protocoles SAML 2.0 ou 1.0 de connexion OpenID de connexion fédérée, prise en charge de l’ajout de publicité Azure peut être simple.  Pour en savoir plus sur [AD Azure prise en charge des protocoles d’authentification](../active-directory-authentication-protocols.md).

**Bibliothèques Open source.**  Microsoft fournit des bibliothèques d’ouvrir la source entièrement prise en charge pour les plates-formes pour accélérer le développement et les langages les plus courants.  Le code source est autorisé sous Apache 2.0 et vous êtes libre de branche et de contribuer aux projets.  Pour en savoir plus sur les [bibliothèques de l’authentification AD Azure](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Présence dans le monde entier et haute disponibilité

**Azure AD est déployé dans les centres de données dans le monde entier et est géré et surveillé en permanence.**  Annonce Azure est le système de gestion des identités pour Microsoft Azure et Office 365 et est déployé dans les centres de 28 données dans le monde entier.  Données de l’annuaire sont garanties pour être répliqués vers au moins trois centres de données.  Équilibreurs de charge global garantir aux utilisateurs l’accès à la copie la plus proche de publicité Azure contenant leurs données et retransmet automatiquement les demandes d’autres centres de données si un problème est détecté.

## <a name="next-steps"></a>Étapes suivantes

[Commencer à écrire du code](../active-directory-developers-guide.md#getting-started).

[Signe les utilisateurs en utilisant Active Directory Azure](../active-directory-authentication-scenarios.md)
