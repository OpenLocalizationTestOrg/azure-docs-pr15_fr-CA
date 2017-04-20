<properties
pageTitle="Application Azure Active Directory et des objets d’entité de Service | Microsoft Azure"
description="Une description de la relation entre l’application et les objets principal du service dans Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Application et objets principal du service dans Azure Active Directory
Lorsque vous le lisez sur un Azure Active Directory (AD) « application », il n’est pas toujours claire exactement ce qui est fait référence par l’auteur. L’objectif de cet article est pour le rendre plus clair, en définissant les aspects conceptuelles et concrètes AD Azure d’intégration des applications, avec un exemple d’inscription et de consentement pour une [application de plusieurs utilisateurs](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Vue d’ensemble
Une application Azure AD est plus large que juste un élément du logiciel. Il s’agit d’un terme conceptuel, de référence non seulement à des logiciels d’application, mais également son inscription (alias : configuration de l’identité) avec AD Azure, qui lui permet de participer à l’authentification et l’autorisation « conversations » lors de l’exécution. Par définition, une application peut fonctionner dans un rôle de [client](active-directory-dev-glossary.md#client-application) (utilise une ressource), un rôle de [serveur de la ressource](active-directory-dev-glossary.md#resource-server) (API exposition aux clients) ou encore les deux. Le protocole de conversation est défini par un [flux de OAuth 2.0 d’octroi](active-directory-dev-glossary.md#authorization-grant), dans le but de permettre la ressource/client/protéger les accès données d’une ressource respectivement. Passons à un niveau plus profond et voir comment le modèle d’application AD Azure représente une application interne. 

## <a name="application-registration"></a>Inscription de l’application
Lorsque vous enregistrez une application dans [Azure portal classique][AZURE-Classic-Portal], deux objets sont créés dans votre client AD Azure : un objet application et un objet d’entité de service.

#### <a name="application-object"></a>Objet d’application
Une application Azure AD est *défini* par son un et seul application objet qui réside dans le locataire AD Azure où l’application a été enregistrée, appelés clients « home » de l’application. L’objet application fournit les informations d’identité pour une application et est le modèle à partir duquel ses objets principal service correspondantes sont *dérivés* pour une utilisation au moment de l’exécution. 

Vous pouvez considérer l’application comme une représentation *globale* de votre application (pour une utilisation sur tous les locataires) et l’entité du service comme représentation *local* (pour une utilisation dans un client spécifique). graphique de publicité Azure [entité d’Application] [ AAD-Graph-App-Entity] définit le schéma d’un objet application. Par conséquent, un objet d’application a une relation 1:1 avec le logiciel et 1 : relation*n* avec ses objets principal du service *n* correspondant.

#### <a name="service-principal-object"></a>Objet principal de service
L’objet principal du service définit la stratégie et les autorisations pour une application, qui fournit la base d’une entité de sécurité représenter l’application lors de l’accès aux ressources au moment de l’exécution. graphique de publicité Azure [entité de ServicePrincipal] [ AAD-Graph-Sp-Entity] définit le schéma d’un objet principal de service. 

Un objet principal de service est requis dans chaque client pour lequel une instance de l’utilisation de l’application doit être représentée, ce qui permet de sécuriser l’accès aux ressources appartenant à des comptes d’utilisateurs à partir de ce client. Une application mono-utilisateur aura une seule entité de service (dans sa base clients). Une [application Web](active-directory-dev-glossary.md#web-client) partagée aura également un principal de service de chaque client, où un administrateur ou un utilisateur (s) à partir de ces clients ont consentement, ce qui lui permet d’accéder à leurs ressources. À la suite de l’accord, l’objet principal de service est consulté pour les demandes d’autorisation future. 

> [AZURE.NOTE] Les modifications apportées à l’objet application, sont également répercutées dans son objet principal du service dans domestique locataire l’application uniquement (le locataire dans laquelle il a été enregistré). Pour les applications de plusieurs utilisateurs, les modifications apportées à l’objet application ne sont pas répercutées dans les objets principal du service des locataires de n’importe quel client, jusqu'à ce que le locataire consommateur supprime l’accès et accorde l’accès.

## <a name="example"></a>Exemple
Le diagramme suivant illustre la relation entre l’objet application et correspondant d’une application de service des objets principal, dans le contexte d’un exemple d’application partagée appelée **application HR**. Il existe trois locataires de publicité Azure dans ce scénario : 

- **Adatum** - le locataire utilisé par la société qui a développé l' **application HR**
- **Contoso** - le locataire utilisé par l’entreprise Contoso, qui est un consommateur de l' **application HR**
- **Fabrikam** - le locataire utilisé par l’organisation de Fabrikam, qui consomme l' **application HR**

![Relation entre un objet d’application et un objet d’entité de service](./media/active-directory-application-objects/application-objects-relationship.png)

Dans le diagramme précédent, étape 1 est le processus de création de l’application et les objets principal du service dans les clients de base de l’application.

À l’étape 2, issue de l’accord, les administrateurs de Contoso et Fabrikam un objet principal de service est créé dans clients AD Azure de leur société et affecté les autorisations accordée par l’administrateur. Notez également que l’application de ressources humaines peut être configuré/conçue pour permettre le consentement par des utilisateurs individuels.

À l’étape 3, les locataires de consommateur de l’application HR (Contoso et Fabrikam) chaque ont leur propre objet principal de service. Chaque représente leur utilisation d’une instance de l’application lors de l’exécution, régie par les autorisations consentie par l’administrateur respectif.

## <a name="next-steps"></a>Étapes suivantes
Objet d’application d’une application est accessible via l’API Azure AD graphique, tel que représenté par ses OData [entité d’Application][AAD-Graph-App-Entity]

Objet principal de service d’une application est accessible via l’API Azure AD graphique, tel que représenté par ses OData [entité de ServicePrincipal][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com