<properties
   pageTitle="Comment les applications sont ajoutées à Active Directory de Azure."
   description="Cet article décrit comment les applications sont ajoutées à une instance d’Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Comment et pourquoi les applications sont ajoutées à Active Directory Azure

L’une des choses doivent initialement lors de l’affichage d’une liste d’applications de votre instance d’Azure Active Directory est de comprendre d'où proviennent les applications et pourquoi ils sont a-t-il.  Cet article fournit une vue d’ensemble détaillée comment les applications sont représentées dans l’annuaire et vous fournissent un contexte qui vous aideront à comprendre comment une application fournie dans votre répertoire.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Les services AD Azure fournit aux applications ?

Les applications sont ajoutées à AD Azure pour exploiter une ou plusieurs des services qu’il fournit.  Ces services comprennent :

* Autorisation et authentification de l’application
* Autorisation et authentification de l’utilisateur
* Session unique (SSO) à l’aide de fédération ou le mot de passe
* Mise en service de l’utilisateur et synchronisation
* Contrôle d’accès basé sur les rôles ; Utilisez le répertoire pour définir les rôles d’application pour effectuer des rôles en fonction des vérifications d’autorisation dans une application.
* services d’autorisation oAuth (utilisés par Office 365 et d’autres applications Microsoft, pour autoriser l’accès aux API/ressources).
* Publication d’application et proxy ; Publier une application à partir d’un réseau privé à internet

## <a name="how-are-applications-represented-in-the-directory"></a>Comment les applications sont représentées dans l’annuaire ?

Les applications sont représentées dans la publicité Azure à l’aide de 2 objets : un objet application et un objet d’entité de service.  Il existe un objet application, inscrit dans un « home » / répertoire « propriétaire » ou « publication » et un ou plusieurs objets principal qui représente l’application dans chaque répertoire dans lequel il s’agit de service.  

L’objet application décrit l’application Azure AD (service mutualisée) et peuvent inclure les éléments suivants : (*Remarque*: il ne s’agit pas d’une liste exhaustive.)

* Nom, Logo et Publisher
* Secrets (clés symétriques ou asymétriques utilisées pour authentifier l’application)
* Dépendances d’API (oAuth)
* API/ressources/étendues publiées (oAuth)
* Rôles de l’application (RBAC)
* Les métadonnées de l’authentification unique et de la configuration (SSO)
* Approvisionnement des métadonnées et la configuration de l’utilisateur
* Configuration et les métadonnées de proxy

L’entité du service est un enregistrement de l’application dans chaque répertoire, où l’application agit notamment son répertoire de base.  L’entité du service :

* Fait référence à un objet de l’application via la propriété d’id app
* Utilisateur local des enregistrements et des affectations de rôle d’application de groupe
* Enregistre les autorisations d’administration et de l’utilisateur locales accordé à l’application
    * Par exemple : autorisation de l’application pour accéder à un courrier d’utilisateurs spécifiques
* Stratégies locales enregistrements, y compris la stratégie d’accès conditionnel
* Paramètres locaux enregistrements locaux alternative pour une application
    * Règles de transformation des revendications
    * Mappages d’attributs (approvisionnement de l’utilisateur)
    * Client de rôles d’application spécifique (si l’application prend en charge les rôles personnalisés)
    * Nom ou Logo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Un diagramme d’objets d’application et d’entités de service au sein de répertoires

![Diagramme illustrant comment des objets application et service les entités existantes avec les instances de AD Azure.][apps_service_principals_directory]

Comme vous pouvez le voir dans le diagramme ci-dessus.  Microsoft gère deux répertoires qu’il utilise en interne (à gauche) pour publier des applications.

* Une pour Microsoft Apps (répertoire de services Microsoft)
* Une pour des applications de tiers 3e pré-intégrées (répertoire de bibliothèque de l’application)

Éditeurs/fournisseurs d’applications qui s’intègrent avec AD Azure sont nécessaires pour avoir un répertoire de publication.  (Certains répertoire SAAS).

Les applications que vous ajoutez vous-même :

* Applications développées (intégrées avec DAS)
* Applications vous connecté de single-sign-on
* Les applications que vous avez publié en utilisant le proxy d’application Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Quelques notes et exceptions

* Pas de toutes les entités de service pointent vers les objets application.  AH bon ? Lorsque AD Azure a été conçu à l’origine les services fournis aux applications étaient beaucoup plus limitées et l’entité du service était suffisante pour l’établissement d’une identité d’application.  Le service d’origine principal a été plus proche de la forme sur le compte de service Windows Server Active Directory.  Pour cette raison, il s’agit de toujours possible de créer des entités de service à l’aide de AD PowerShell Azure sans d’abord créer un objet application.  L’API de graphique nécessite un objet d’application avant la création d’un service principal.
* Toutes les informations décrites ci-dessus est actuellement exposée par programme.  Les éléments suivants sont uniquement disponibles dans l’interface utilisateur :
    * Règles de transformation des revendications
    * Mappages d’attributs (approvisionnement de l’utilisateur)
* Pour plus d’informations détaillées sur l’entité de service et les objets d’application, consultez la documentation de référence API REST de Azure AD graphique.  *Conseil*: documentation sur les API du graphique de publicité Azure est la chose la plus proche à une référence de schéma pour Azure AD qui est actuellement disponible.  
    * [Application](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Principaux de service](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Comment les applications sont ajoutées à mon instance AD Azure ?
Il existe plusieurs façons qu'une application peut être ajoutée à Active Directory Azure :

* Ajouter une application à partir de la [Galerie d’application Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Signe/dans un 3ème partie application intégrée avec Azure Active Directory (par exemple : [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Lors de l’inscription/utilisateurs sont posées pour donner l’autorisation de l’application pour accéder à leur profil et les autres autorisations.  La première personne à donner son consentement provoque un principal de service représentant l’application à ajouter à l’annuaire.
* Connexion haut/dans les services en ligne de Microsoft telles que [Office 365](http://products.office.com/)
    * Lorsque vous vous abonner à Office 365, ou commencez une version d’évaluation, une ou plusieurs entités de service sont créées dans le répertoire représentant les différents services qui sont utilisés pour offrir toutes les fonctionnalités associées à Office 365.
    * Certains services Office 365 comme SharePoint créer des entités de service sur une base permanente pour sécuriser les communications entre les composants, y compris les flux de travail.
* Ajouter une application que vous développez voir Azure Management Portal : https://msdn.microsoft.com/library/azure/dn132599.aspx
* Ajouter une application que vous développez à l’aide de Visual Studio Voir :
    * [Méthodes d’authentification ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Services connectés](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Ajouter une application à utiliser pour utiliser le [Proxy d’Application Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Se connecter à une application d’authentification unique à l’aide de SAML ou l’authentification du mot de passe
* Beaucoup d’autres, y compris les différentes expériences de développeur dans Azure et/dans explorer d’API expériences dans tous les centres de développement

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Qui possède l’autorisation pour ajouter des applications à mon instance AD Azure ?

Seuls les administrateurs globaux peuvent :

* Ajouter des applications de la galerie d’application Azure AD (applications pré-intégrées 3e partie)
* Publier une application utilisant le Proxy d’Application AD Azure

Tous les utilisateurs dans votre répertoire possèdent des droits pour ajouter des applications qu’ils développent et discrétion sur les applications qu’ils partage donnent accès à leurs données d’organisation.  *N’oubliez pas de connexion d’un utilisateur à distance/dans une application et l’octroi d’autorisations peut entraîner un principal de service en cours de création.*

Cette peut initialement son concernant, mais gardez à l’esprit ce qui suit :

* Les applications ont été possible d’exploiter Windows Server Active Directory pour l’authentification utilisateur depuis de nombreuses années sans nécessiter l’application d’être inscrit/enregistré dans le répertoire.  Désormais l’organisation sera ont amélioré de la visibilité à exactement combien d’applications sont l’utilisation de l’annuaire et pour quelles raisons.
* Aucune nécessité d’administration contrôlée par le processus de publication / d’enregistrement de app.  Avec les Services de fédération Active Directory, il était fort probable qu’un administrateur a dû ajouter une application sous la forme d’une partie de confiance pour les développeurs.  Désormais, les développeurs peuvent libre-service.
* Les utilisateurs de la signature/jusqu'à des applications à l’aide de leurs comptes d’entreprise à des fins commerciales sont une bonne chose.  Si par la suite, ils laissent l’organisation qu’ils perdront l’accès à son compte dans l’application qu’ils utilisaient.
* Disposer d’un enregistrement de données a été partagées avec lequel application est une bonne chose.  Les données sont transportables plus que jamais et il est utile de disposer d’un enregistrement clair de qui a partagé les données avec les applications.
* Les applications qui utilisent Active Directory Azure pour oAuth décident exactement quelles sont les autorisations dont les utilisateurs sont en mesure d’accorder aux applications et nécessitant des autorisations administrateur à accepter.  Il va sans dire que seuls les administrateurs pourront accorder d’autorisations plus importantes et les plus étendues.
* Utilisateurs, ajout et permettant d’accéder à leurs données, les applications sont les événements audités afin de pouvoir afficher les rapports d’Audit dans le portail de gestion d’Azure pour déterminer la façon dont une application a été ajoutée au répertoire.

**Remarque :** *Microsoft lui-même a été d’exploitation à l’aide de la configuration par défaut pour le nombre de mois maintenant.*

Avec tout cela dit qu’il est possible d’empêcher l’ajout d’applications des utilisateurs dans votre répertoire et d’exercer la discrétion quelles informations ils partagent avec des applications en modifiant la configuration de l’annuaire dans le portail de gestion d’Azure.  La configuration suivante est accessible au sein du portail de gestion d’Azure sur l’onglet « Configuration » de votre répertoire.

![Une capture d’écran de l’interface utilisateur pour configurer les paramètres de l’application intégrée][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des applications à AD Azure et comment configurer les services pour les applications.

* Développeurs : [comment intégrer une application avec DAS](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Développeurs : [réviser le code exemple pour des applications intégrées avec Azure Active Directory sur Github](https://github.com/AzureADSamples)
* Les développeurs et les professionnels de l’informatique : [révision de la documentation de l’API REST de l’API de graphique Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Professionnels de l’informatique : [Apprenez à utiliser les applications pré-intégrées Azure Active Directory à partir de la bibliothèque de l’application](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Professionnels de l’informatique : [Rechercher des didacticiels de configuration des applications pré-intégrées spécifiques](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Professionnels de l’informatique : [savoir comment publier une application utilisant le Proxy d’Application Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Voir aussi

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
