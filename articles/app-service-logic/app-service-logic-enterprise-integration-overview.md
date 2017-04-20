<properties 
    pageTitle="Vue d’ensemble de l’intégration d’entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Utilisez les fonctionnalités d’intégration d’entreprise pour permettre des scénarios d’intégration et de processus métier à l’aide d’applications de logique" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>Vue d’ensemble de l’entreprise Integration Pack

## <a name="what-is-the-enterprise-integration-pack"></a>Quel est le module d’intégration entreprise ?
Le module d’intégration entreprise est la solution de nuage de Microsoft permettant en toute transparence des communications de (B2B) entreprise-entreprise. Le pack utilise des protocoles standard y compris [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md)et [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) pour échanger des messages entre des partenaires commerciaux. Les messages peuvent être éventuellement sécurisés à l’aide du cryptage et signatures numériques. 

Le pack permet aux organisations qui utilisent différents protocoles et formats pour échanger des messages électroniques en transformant les différents formats dans un format que les systèmes de ces deux organisations peuvent interpréter et exécuter des actions sur. 

Si vous êtes familiarisé avec BizTalk Server ou les Services de BizTalk de Microsoft Azure, vous le trouverez facile à utiliser que les fonctionnalités de l’intégration d’entreprise, car la plupart des concepts est similaire. Une différence majeure est qu’intégration d’entreprise utilise les comptes d’intégration pour simplifier le stockage et la gestion d’artefacts utilisés dans les communications de B2B. 

Du point de vue architectural, le module d’intégration entreprise repose sur les **comptes d’intégration** qui stockent tous les artefacts qui peuvent être utilisés pour concevoir, déployer et gérer vos applications B2B. Un compte d’intégration est en fait un conteneur en nuage dans lequel vous stockez les artefacts tels que des schémas, partenaires, des certificats, des cartes et des accords. Ces artefacts peuvent ensuite être utilisés dans les applications de la logique de créer des workflows de B2B. Avant de pouvoir utiliser les artefacts dans une logique d’application, vous devez lier votre compte de l’intégration à votre application de logique. Après les liant, votre application logique aura accès aux artefacts du compte de l’intégration.  

## <a name="why-should-you-use-enterprise-integration"></a>Pourquoi utiliser intégration d’entreprise ?
- Grâce à l’intégration de l’entreprise, vous êtes capable de stocker tous vos artefacts dans un seul endroit, ce qui correspond à votre compte d’intégration. 
- Vous pouvez exploiter le moteur d’applications de logique et de tous ses connecteurs pour créer des workflows de B2B et d’intégration avec les applications SaaS 3ème partie, les applications locales, ainsi que des applications personnalisées
- Vous pouvez également exploiter les fonctions Azure

## <a name="how-to-get-started-with-enterprise-integration"></a>Comment démarrer avec l’intégration de l’entreprise ?
Vous pouvez créer et gérer des applications B2B en utilisant le module d’intégration entreprise via le Concepteur d’applications logique sur le **portail Azure**.  

Vous pouvez également utiliser [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "logique apps rubriques de PowerShell") pour gérer vos applications logique. 

Voici une vue d’ensemble des étapes que vous devez prendre avant de créer des applications dans le portail Azure : ![image de la vue d’ensemble](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Quels sont les scénarios courants ?

Intégration d’entreprise prend en charge ces normes industrielles :   

- EDI - l’échange de données informatisé  
- EAI - intégration  

## <a name="heres-what-you-need-to-get-started"></a>Voici ce que vous devez mise en route
- Un abonnement Azure avec un compte d’intégration
- 2015 de Visual Studio pour créer des schémas et des mappages
- [Intégration d’applications entreprise de logique de Microsoft Azure Tools pour Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Essaie
[Essayez maintenant](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) déployer un exemple opérationnel AS2 Envoyer & recevoir application logique qui utilise les fonctionnalités B2B de logique d’applications.

## <a name="learn-more-about"></a>En savoir plus sur :
- [Accords de] (./app-service-logic-enterprise-integration-agreements.md "Obtenir des informations sur les accords d’intégration entreprise")
- [Scénarios interentreprises (B2B)] (./app-service-logic-enterprise-integration-b2b.md "Apprenez à créer des applications avec les fonctionnalités B2B logique")  
- [Certificats] (./app-service-logic-enterprise-integration-certificates.md "En savoir plus sur les certificats d’intégration d’entreprise")
- [Codage et décodage de fichier plat] (./app-service-logic-enterprise-integration-flatfile.md "Apprenez à coder et à décoder le contenu du fichier plat")  
- [Comptes d’intégration] (./app-service-logic-enterprise-integration-accounts.md "En savoir plus sur les comptes de l’intégration")
- [Cartes] (./app-service-logic-enterprise-integration-maps.md "En savoir plus sur l’intégration d’entreprise mappé")
- [Partenaires] (./app-service-logic-enterprise-integration-partners.md "En savoir plus sur les partenaires d’intégration entreprise")
- [Schémas] (./app-service-logic-enterprise-integration-schemas.md "Obtenir des informations sur les schémas d’intégration entreprise")
- [Validation de message XML] (./app-service-logic-enterprise-integration-xml.md "Découvrez comment valider les messages XML avec des applications de logique")
- [Transformer du XML] (./app-service-logic-enterprise-integration-transform.md "En savoir plus sur l’intégration d’entreprise mappé")
- [Connecteurs d’intégration entreprise] (../connectors/apis-list.md "Obtenir des informations sur les connecteurs de pack d’intégration entreprise")



