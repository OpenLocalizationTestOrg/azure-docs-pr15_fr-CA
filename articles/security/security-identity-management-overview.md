<properties
   pageTitle="Vue d’ensemble de la sécurité Azure identity management | Microsoft Azure"
   description=" Microsoft des identités et des accès management solutions aider l’informatique à protéger l’accès aux applications et ressources sur le centre de données d’entreprise et dans le nuage, l’activation des niveaux supplémentaires de validation d’authentification à facteurs multiples et de règles d’accès conditionnel. Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure qui aident à la gestion des identités. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Vue d’ensemble de la sécurité Azure identity management

Microsoft des identités et des accès management solutions aider l’informatique à protéger l’accès aux applications et ressources sur le centre de données d’entreprise et dans le nuage, l’activation des niveaux supplémentaires de validation d’authentification à facteurs multiples et de règles d’accès conditionnel. Surveillance des activités suspectes par le biais de sécurité avancée, création de rapports, l’audit et alertes contribue à atténue les problèmes de sécurité potentiels. [Azure Active Directory prime](../active-directory/active-directory-editions.md) fournit une ouverture de session unique sur des milliers de nuage (SaaS) applications et accéder à des applications web à exécuter sur site.

Les avantages de sécurité d’Azure Active Directory (AD) incluent la possibilité de :

- Créer et gérer une identité unique pour chaque utilisateur au sein de votre entreprise hybride, synchronisation des utilisateurs, des groupes et des dispositifs
- Fournir un accès de session unique à vos applications, y compris des milliers d’applications de SaaS pré-intégrées
- Activer la sécurité des applications en appliquant basée sur les règles d’authentification à plusieurs facteurs pour à la fois sur site et applications en nuage
- Configurer l’accès distant sécurisé à des applications web en local par l’intermédiaire de Proxy d’Application Azure AD

L’objectif de cet article est de fournir une vue d’ensemble des principales fonctionnalités de sécurité Azure qui aident à la gestion des identités. Nous fournissons également des liens vers des articles qui vous donne les détails de chaque fonctionnalité et vous pouvez en savoir plus.  

L’article met l’accent sur les capacités de gestion d’identité d’Azure core suivantes :

- L’ouverture de session unique
- Proxy inversé
- Plusieurs facteurs d’authentification
- Surveillance de la sécurité, les alertes et les rapports d’apprentissage machine
- Gestion des identités et des accès de consommateurs
- Enregistrement de dispositif
- Gestion des identités privilégié
- Protection d’identité
- Gestion des identités hybride

## <a name="single-sign-on"></a>L’ouverture de session unique

Sign-on (SSO) moyen unique pour pouvoir accéder à toutes les applications et les ressources dont vous avez besoin pour travailler, en se connectant une seule fois à l’aide d’un seul compte d’utilisateur. Une fois connecté, vous pouvez accéder à toutes les applications que vous avez besoin sans devoir s’authentifier (par exemple, tapez un mot de passe) une seconde fois.

De nombreuses organisations font usage du logiciel comme une application de service (SaaS) telles que Office 365 et force de vente pour la productivité des utilisateurs. Historiquement, le personnel informatique pour individuellement de créer et de mettre à jour les comptes d’utilisateurs dans chaque application SaaS, et les utilisateurs devaient se souvenir d’un mot de passe pour chaque application SaaS.

AD Azure étend sur site Active Directory dans le nuage, permettant aux utilisateurs d’utiliser leur compte d’organisation principale pour non seulement se connecter à leurs périphériques associés à un domaine et des ressources de l’entreprise, mais également tous les web et les applications SaaS nécessaires à leur travail.

Non seulement les utilisateurs inutile de gérer plusieurs ensembles de noms d’utilisateurs et mots de passe, accès de l’application peut être automatiquement mis en service ou la mise en service en fonction de groupes de d’organisation et de leur statut en tant qu’employé. AD Azure présente la sécurité et des contrôles d’accès gouvernance qui vous permettent de gérer de manière centralisée des accès des utilisateurs sur les applications SaaS.

Pour en savoir plus :

- [Vue d’ensemble de l’ouverture de session unique](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Intégrer Azure Active Directory SSO avec des applications de SaaS](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy inversé

Le Proxy d’Application AD Azure vous permet de publier des applications sur site, comme les sites [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)et [IIS](http://www.iis.net/)-en fonction des applications à l’intérieur de votre réseau privé et offre un accès sécurisé aux utilisateurs extérieurs à votre réseau. Proxy d’application fournit un accès à distance et authentification unique (SSO) pour de nombreux types d’applications web de locaux avec des milliers d’applications SaaS AD Azure prend en charge. Employés peuvent s’enregistrer dans vos applications à partir d’accueil sur leurs propres périphériques et s’authentifier par le biais de ce proxy de nuage.

Pour en savoir plus :

- [L’activation du Proxy d’Application Azure AD](../active-directory/active-directory-application-proxy-enable.md)
- [Publier des applications à l’aide du Proxy d’Application Azure AD](../active-directory/active-directory-application-proxy-publish.md)
- [Single-sign-on avec le Proxy de l’Application](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Utilisation de l’accès conditionnel](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Plusieurs facteurs d’authentification
Authentification à plusieurs facteurs Azure (AMF) est une méthode d’authentification qui requiert l’utilisation de plus d’une méthode de vérification et ajoute une couche de sécurité critique pour les connexions utilisateur et des transactions. AMF contribue à garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une authentification forte via une gamme d’options de vérification, appel téléphonique, message texte ou application mobile notification ou la vérification de code et 3e partie OAuth jetons.

Pour en savoir plus :

- [Plusieurs facteurs d’authentification](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md)
- [Fonctionnement de l’authentification à plusieurs facteurs Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Surveillance de la sécurité, les alertes et les rapports d’apprentissage machine

Surveillance de la sécurité et les alertes et les rapports apprentissage machine permettant d’identifier des modèles d’accès incohérent peuvent vous aider à protéger votre entreprise. Visibilité sur l’intégrité et la sécurité de l’annuaire de votre organisation, vous pouvez utiliser l’accès Azure d’Active Directory et les rapports d’utilisation. Avec ces informations, un administrateur de l’annuaire peut mieux déterminer où peut se trouver des risques de sécurité afin qu’ils puissent correctement à atténuer ces risques.

Dans le portail Azure classique, les rapports sont classés dans l’une des manières suivantes :

- Les rapports d’anomalies – contiennent des événements que nous avons trouvé d’être anormale de connexion. Notre objectif est de vous informer de cette activité et vous permettent d’être en mesure de déterminer si un événement est suspect.
- Les rapports Application intégrées – fournissent des informations sur l’utilisation des applications en nuage dans votre organisation. Azure Active Directory offre une parfaite intégration avec des milliers d’applications en nuage.
- Les rapports d’erreurs – indiquent des erreurs pouvant se produire lors de la création de comptes à des applications externes.
- Les rapports spécifiques à l’utilisateur – afficher périphérique/dans les données de l’activité d’un utilisateur spécifique.
- Journaux d’activité – contiennent un enregistrement de tous les événements audités dans les dernières 24 heures 7 derniers jours, ou 30 derniers jours, ainsi que les changements d’activité groupe et activité d’enregistrement et de réinitialisation de mot de passe.

Pour en savoir plus :

- [Afficher vos rapports d’accès et d’utilisation](../active-directory/active-directory-view-access-usage-reports.md)
- [Mise en route avec Azure Active Directory Reporting](../active-directory/active-directory-reporting-getting-started.md)
- [Guide des rapports Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gestion des identités et des accès de consommateurs

Azure B2C annuaire Active est un service de gestion d’identité globale, hautement disponible pour les applications pour les consommateurs qui s’adapte à des centaines de millions d’identités. Il peut être intégré via mobile et web des plates-formes. Vos consommateurs peuvent ouvrir une session pour toutes vos applications grâce à des expériences personnalisables à l’aide de leurs comptes sociaux existants ou en créant de nouvelles informations d’identification.

Dans le passé, les développeurs d’applications qui souhaitaient d’inscription et de consommateurs dans leurs applications écrirait leur propre code. Et qu’ils seraient ont utilisé les systèmes ou les bases de données sur site pour stocker les noms d’utilisateur et mots de passe. Azure B2C répertoire actif offre à votre entreprise un meilleur moyen d’intégrer la gestion des identités dans les applications à l’aide d’une plate-forme sécurisée et basée sur des normes et un ensemble volumineux de stratégies extensibles.

Lorsque vous utilisez Azure Active Directory B2C, vos clients peuvent vous inscrire pour vos applications à l’aide de leurs comptes sociaux existants (Facebook, Google, Amazon, LinkedIn) ou en créant de nouvelles informations d’identification (adresse de messagerie et mot de passe ou nom d’utilisateur et mot de passe).

Pour en savoir plus :

- [Nouveautés d’Azure Active Directory B2C ?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Aperçu de Active Directory B2C Azure : signer vers le haut et se connecter aux consommateurs dans vos applications](../active-directory-b2c/active-directory-b2c-overview.md)
- [Aperçu de B2C Azure Active Directory : Les Types d’Applications](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Enregistrement de dispositif

Enregistrement de dispositif AD Azure constitue la base pour les scénarios basés sur le périphérique d' [accès conditionnel](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Lorsqu’un périphérique est inscrit, inscription de périphérique Azure Active Directory fournit le périphérique avec une identité qui sert à authentifier le périphérique lorsque l’utilisateur se connecte. Le périphérique authentifié et les attributs du périphérique, puis utilisable pour appliquer des stratégies d’accès conditionnel pour les applications qui sont hébergées dans le cloud et sur site.

Lorsqu’elle est combinée avec une solution de gestion (MDM) de dispositif mobile comme Intune, les attributs du périphérique dans Azure Active Directory sont mis à jour avec des informations supplémentaires sur le périphérique. Cela vous permet de créer des règles d’accès conditionnel qui permettent d’accéder à partir de périphériques pour répondre à vos normes de sécurité et de conformité.

Pour en savoir plus :

- [Mise en route de l’enregistrement de dispositif Azure Active Directory](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [Configuration d’un accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Enregistrement de dispositif automatique avec les périphériques à un domaine d’Azure Active Directory pour Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Gestion des identités privilégié
Gestion des identités privilégié Azure Active Directory (AD) vous permet de gérer, de contrôler et de surveiller vos identités privilégiées et de l’accès aux ressources dans Active Directory Azure ainsi que les autres services en ligne de Microsoft Office 365 ou Microsoft Intune.

Parfois, les utilisateurs ont besoin effectuer des opérations privilégiées dans ressources Azure ou Office 365, ou autres applications SaaS. Cela signifie souvent organisations disposent pour leur donner un accès privilégié permanent dans Azure AD. Il s’agit d’un risque croissant pour les ressources de hébergé sur le nuage, car les entreprises ne peuvent pas surveiller suffisamment ce que font les utilisateurs avec les privilèges d’administrateur. En outre, si un compte d’utilisateur avec accès privilégié est compromis, qu’une violation peut affecter leur sécurité globale du nuage. La gestion des identités AD privilégié Azure vous aide à résoudre ce risque.

La gestion des identités AD privilégié Azure vous permet de :

- Voir quels utilisateurs sont des administrateurs d’Azure AD
- Activer à la demande, « juste à temps » d’un accès administratif à de Microsoft Online Services comme Office 365 et Intune
- Obtenir des rapports sur l’historique de l’accès administrateur et les modifications dans les affectations de l’administrateur
- Recevoir des alertes sur l’accès à un rôle de privilège

Pour en savoir plus :

- [Gestion des identités Azure AD privilégié](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Rôles dans Azure AD privilégié de gestion des identités](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure AD privilégié la gestion des identités : Comment ajouter ou supprimer un rôle d’utilisateur](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Protection d’identité
La Protection d’identité de publicité Azure est un service de sécurité qui offre une vue consolidée des événements de risques et des vulnérabilités potentielles affectant les identités de votre entreprise. Protection d’identité tire parti des fonctionnalités de détection des anomalies existant Azure d’Active Directory (disponibles par l’intermédiaire de rapports d’activité anormale d’Azure AD) et introduit de nouveaux types d’événement de risque qui peuvent détecter les anomalies en temps réel.

Pour en savoir plus :

- [Protection d’identité Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Channel 9 : Les annonces Azure et afficher d’identité : aperçu de Protection d’identité](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gestion des identités hybride

L’approche de Microsoft d’identité s’étend sur local et le nuage, création d’une identité d’utilisateur unique pour l’authentification et l’autorisation à toutes les ressources, indépendamment de l’emplacement.

Pour en savoir plus :

- [Livre blanc identité hybride](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Blog de l’équipe Active Directory](https://blogs.technet.microsoft.com/ad/)
