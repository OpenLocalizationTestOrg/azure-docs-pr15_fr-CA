<properties
   pageTitle="Graphique d’Azure Active Directory API | Microsoft Azure"
   description="Un guide de présentation et de démarrage rapide pour l’API graphique qui permet d’accéder par programmation à Azure AD via les points de terminaison API REST."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Graphique d’Azure Active Directory API

> [AZURE.IMPORTANT] Azure AD graphique API fonctionnalité est également disponible par le biais de [Microsoft Graph](https://graph.microsoft.io/), une API unifiée qui inclut des API à partir d’autres services de Microsoft, comme Outlook, OneDrive, OneNote, planificateur et graphique Office, accessible via un point de terminaison unique et avec un jeton d’accès unique.

L’API de graphique Azure Active Directory fournit un accès par programmation à Azure AD via les points de terminaison API REST. Applications peuvent utiliser l’API de graphique pour effectuer créer, lire, mettre à jour et supprimer des opérations sur les objets et les données de l’annuaire. Par exemple, l’API de graphique prend en charge les opérations courantes suivantes pour un objet utilisateur :

- Créer un nouvel utilisateur dans un répertoire

- Obtenir les propriétés détaillées d’un utilisateur, par exemple leurs groupes

- Mettre à jour les propriétés d’un utilisateur, par exemple leur emplacement et leur numéro de téléphone, ou de modifier leur mot de passe

- Vérifier l’appartenance au groupe de l’utilisateur pour l’accès par rôle

- Désactiver un compte d’utilisateur ou de le supprimer entièrement

Outre les objets de l’utilisateur, vous pouvez effectuer des opérations similaires sur d’autres objets tels que des groupes et des applications. Pour appeler l’API graphique sur un répertoire, l’application doit être enregistrée avec la publicité Azure et être configurée pour autoriser l’accès à l’annuaire. Normalement cela via un flux de consentement d’utilisateur ou un administrateur.

Pour commencer à utiliser l’API de graphique Azure Active Directory, consultez le [Guide de démarrage rapide d’API graphique](active-directory-graph-api-quickstart.md)ou consulter la [documentation de référence API de graphique interactive](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Fonctionnalités

L’API graphique offre les fonctionnalités suivantes :

- **Les points de terminaison REST API**: l’API de graphique est un service RESTful constitué de points de terminaison qui sont accessibles à l’aide de demandes HTTP standard. L’API graphique prend en charge les types de contenu XML ou Notation JSON (Javascript Object) pour les demandes et les réponses. Pour plus d’informations, consultez [référence de l’API reste Azure AD graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Authentification avec AD Azure**: chaque demande à l’API de graphique doit être authentifié en ajoutant un JSON Web jeton (JWT) dans l’en-tête de la demande d’autorisation. Ce jeton est acquis par une demande au point de terminaison jeton Azure annonce et fournissant des informations d’identification valides. Vous pouvez utiliser le flux d’informations d’identification de client OAuth 2.0 ou le code d’autorisation des flux pour obtenir un jeton pour le graphique des appels. Pour plus d’informations, [2.0 OAuth dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **L’autorisation basée sur les rôles (RBAC)**: groupes de sécurité servent à effectuer des RBAC dans l’API de graphique. Par exemple, si vous souhaitez déterminer si un utilisateur a accès à une ressource spécifique, l’application peut appeler l’opération de [Vérifier l’appartenance au groupe (transitifs)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , qui renvoie true ou false.

- **Différentielle de requête**: Si vous souhaitez contrôler les modifications dans un répertoire entre deux périodes de temps sans avoir à effectuer des requêtes fréquentes à l’API de graphe, vous pouvez faire une demande de requête différentielle. Ce type de requête renvoie uniquement les modifications apportées entre la requête précédente différentielle et de la demande en cours. Pour plus d’informations, consultez [requête différentielle de Azure AD graphique API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Répertoire Extensions**: Si vous développez une application qui doit lire ou écrire des propriétés uniques pour des objets d’annuaire, vous pouvez enregistrer et utiliser des valeurs d’extension à l’aide de l’API du graphique. Par exemple, si votre application requiert une propriété Skype ID pour chaque utilisateur, vous pouvez enregistrer la nouvelle propriété dans le répertoire et elle sera disponible sur chaque objet utilisateur. Pour plus d’informations, consultez [Extensions de schéma du répertoire Azure AD graphique API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Sécurisé par autorisations**: API de graphique DAS expose des étendues d’autorisation qui permettent de sécuriser/consenti un accès aux données de DAS et prend en charge les différents types d’application client, y compris de :
 - ceux avec une interface utilisateur qui figurent délégué l’accès aux données par le biais d’autorisations de l’utilisateur connecté (délégué)
  - ceux qui utilisent application-définissent basée sur le rôle de contrôle d’accès tels que les clients du service/démon (rôles application)

    Les délégués et app rôle autorisations représentent un privilège exposé par l’API de graphique et peut être demandée par les applications clientes via des autorisations [fonctionnalités dans Azure portal classique](https://manage.windowsazure.com)des applications d’enregistrement. Clients peuvent vérifier les étendues autorisation accordé leur en inspectant la revendication de l’étendue (ou « CSP ») reçue dans le jeton d’accès pour les autorisations déléguées et les rôles (« rôles ») demander des autorisations de rôle d’application. Pour en savoir plus sur les [zones de graphique de publicité Azure API autorisation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Scénarios

L’API de graphique permet de nombreux scénarios d’application. Les scénarios suivants sont les plus courantes :

- **Applications professionnelles (mono-utilisateur)**: dans ce scénario, un développeur d’entreprise travaille pour une entreprise qui a un abonnement à Office 365. Le développeur construit une application web qui interagit avec AD Azure pour effectuer les tâches de cette attribution d’une licence à un utilisateur. Cette tâche requiert l’accès à l’API graphique, afin que les registres de développeur, le seul client application dans Azure AD et configure les autorisations Lire et écrire pour l’API de graphique. Ensuite, l’application est configurée pour utiliser ses propres informations d’identification ou celles de l’utilisateur d’authentification actuellement d’acquérir un jeton pour appeler l’API graphique.

- **Logiciel comme une Application de Service (partagée)**: dans ce scénario, un éditeur de logiciels indépendant (ISV) développe une application hébergée mutualisée web qui fournit des fonctionnalités de gestion utilisateur pour les autres entreprises qui utilisent Active Directory Azure. Ces fonctionnalités requièrent l’accès aux objets d’annuaire, et donc l’application doit appeler l’API graphique. Le développeur enregistre l’application dans Azure AD configure afin d’exiger en lecture et en écriture pour l’API graphique et permet un accès externe afin que les autres organisations peuvent consentir à utiliser l’application dans son propre répertoire. Lorsqu’un utilisateur dans une autre organisation s’authentifie à l’application pour la première fois, une boîte de dialogue de consentement avec les autorisations que requis par l’application sont affichées.  L’octroi d’autorisation appliquera l’application les autorisations demandées à l’API de graphique dans le répertoire de l’utilisateur. Pour plus d’informations sur le cadre de l’accord, consultez [vue d’ensemble de l’infrastructure de consentement](active-directory-integrating-applications.md).

## <a name="see-also"></a>Voir aussi

[Guide de démarrage rapide d’Azure AD graphique API](active-directory-graph-api-quickstart.md)

[Documentation de graphe reste AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guide du programmeur Active Directory Azure](active-directory-developers-guide.md)
