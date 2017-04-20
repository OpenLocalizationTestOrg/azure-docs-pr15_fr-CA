<properties
   pageTitle="Logique applications exemples et scénarios | Microsoft Azure"
   description="Voir des exemples d’applications logique communes et apprenez à implémenter des scénarios courants"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-examples-and-common-scenarios"></a>Exemples d’applications de logique et les scénarios courants

Cette scénarios détails du document et des exemples pour vous aider à comprendre comment vous peuvent utiliser logique apps pour automatiser des processus d’entreprise. 

## <a name="custom-triggers-and-actions"></a>Actions et déclencheurs personnalisés

Il existe plusieurs méthodes que vous pouvez déclencher une application logique à partir d’une autre application. Voici quelques exemples courants :

- [Création d’un déclencheur personnalisé ou une action](app-service-logic-create-api-app.md)
- [Actions de longue](app-service-logic-create-api-app.md)
- [Déclencheur de demande HTTP (POST)](app-service-logic-http-endpoint.md)
- [Actions et déclencheurs de Webhook](app-service-logic-create-api-app.md)
- [Déclencheurs d’interrogation](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Scénarios

- [Demander une réponse synchrone](app-service-logic-http-endpoint.md)
- [Demande de réponse avec SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Enregistrement et gestion des erreurs

- [Exceptions et gestion des erreurs](app-service-logic-exception-handling.md)
- [Configurer les alertes Azure et diagnostics](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Scénarios

- [Cas d’utilisation : Erreurs et gestion des exceptions](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Déploiement et gestion

- [Créer un déploiement automatisé](app-service-logic-create-deploy-template.md)
- [Générer et déployer des applications de logique à partir de Visual Studio](app-service-logic-deploy-from-vs.md)
- [Applications de logique de moniteur](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Types de contenu, les conversions et les transformations

Le [langage de définition de flux de travail](http://aka.ms/logicappsdocs) de logique applications contient de nombreuses fonctions pour vous permettent de convertir et de travailler avec différents types de contenu.  En outre le moteur fera il peut pour conserver les types de contenu en tant que flux de données sur le flux de travail.

- [Gestion des types de contenu](app-service-logic-content-type.md) application/json, application/xml et texte brut
- [Création de définitions de workflow](app-service-logic-author-definitions.md)
- [Référence de langage de définition de workflow](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Les lots et les boucles

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Jusqu’au](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Intégration avec les fonctions Azure

- [Intégration de fonctions Azure](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Scénarios

- [Fonction Azure comme un déclencheur de Bus des services](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP et SOAP reste

 - [L’appel SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Nous vous ajoutez des exemples et scénarios à ce document. Utilisez la section commentaires ci-dessous pour nous dire quels exemples ou les scénarios vous souhaiteriez ici.