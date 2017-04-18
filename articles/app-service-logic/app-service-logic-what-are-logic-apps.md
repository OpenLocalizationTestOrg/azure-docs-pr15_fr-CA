<properties 
    pageTitle="Quelles sont les applications logique ?" 
    description="En savoir plus sur App Apps de logique de Service" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>Quelles sont les applications logique ?

Applications de logique permettent de simplifier et de mettre en œuvre des intégrations évolutives et des flux de travail dans le cloud. Il fournit un concepteur visuel pour le modèle et d’automatiser vos processus sous la forme d’une série d’étapes connu sous la forme d’un flux de travail.  Il existe [plusieurs connecteurs](../connectors/apis-list.md) sur le nuage et les locaux d’intégrer rapidement entre les services et les protocoles.  Une application de logique commence par un trigger (comme « lorsqu’un compte est ajouté à Dynamics CRM ») et après déclenchement peut commencer de nombreuses actions de combinaisons, des conversions et logique de la condition.

Voici les avantages de l’utilisation d’applications de logique :  

- Gagner du temps en créant des processus complexes à l’aide de facile à comprendre les outils de conception
- Mise en œuvre de modèles et flux de travail en toute transparence, qui sinon seraient difficiles à implémenter dans le code
- Mise en route rapide à partir de modèles
- Personnalisation de votre application logique avec vos propres API, code et des actions personnalisées
- Se connecter et de synchroniser les systèmes disparates sur local et le nuage
- Exploiter de BizTalk server, gestion de l’API, fonctions d’Azure et Bus des services Azure avec prise en charge de l’intégration de première classe

Logique d’applications est un iPaaS entièrement géré (intégration de plate-forme en tant que Service) permettant aux développeurs de ne pas devoir de vous inquiéter à propos de la création d’hébergement, de l’évolutivité, de disponibilité et de gestion.  Logique d’applications est automatiquement évoluer pour répondre à la demande.

![Concepteur de flux de app](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Comme mentionné, avec la logique des applications, vous pouvez automatiser des processus métier. Voici quelques exemples :  
 
* Déplacer les fichiers téléchargement vers un serveur FTP dans le stockage Azure
* Commandes de processus et de la gamme entre les locaux et les systèmes en nuage
* Surveiller tous les tweet sur un certain sujet, analyser le sentiment et créez des alertes et des tâches pour des éléments devant être suivi.

Scénarios de ce type peuvent être configurés à partir du concepteur visuel et sans écrire une seule ligne de code. Obtenir la [Création de votre application logique maintenant][create].  Une fois écrite - une application logique peut être [déployé rapidement et de reconfigurer](app-service-logic-create-deploy-template.md) dans plusieurs environnements et régions.

## <a name="why-logic-apps"></a>Pourquoi les applications logique ?

Logique d’applications apporte la rapidité et l’évolutivité dans l’espace d’intégration entreprise.  La facilité d’utilisation du concepteur, plusieurs déclencheurs disponibles et les actions et les outils de gestion puissants que la centralisation de votre API plus simple que jamais.  Comme entreprises vous rapprocher de NUMERISATION, logique d’applications vous permet de connecter entre eux des systèmes hérités et des systèmes.

En outre, avec notre [Entreprise compte d’intégration] [ biztalk] vous pouvez mettre à l’échelle afin de faire évoluer les scénarios d’intégration avec la puissance d’une [messagerie XML][xml], [Gestion des partenaires commerciaux][tpm]et bien plus encore.

- **Outils de conception facile à utiliser** - applications de logique peut être conçue de bout en bout dans le navigateur ou avec les outils de Visual Studio. Démarrer avec un déclencheur, d’une grille simple à lorsqu’un problème GitHub est créé. Puis orchestrer des actions à l’aide de la galerie complète des connecteurs.

- **API connect facilement** -tâches même composition qui sont faciles à décrire sont difficiles à implémenter dans le code. Logique d’applications rend facile à connecter des systèmes disparates. Connecter votre cloud marketing solution dans vos locaux sur système de facturation ? Vous souhaitez centraliser la messagerie sur les API et les systèmes avec un Bus de Service d’entreprise ? Les applications de logique sont le moyen plus rapide et le plus fiable pour fournir des solutions à ces problèmes.

- **Commencer rapidement à partir de modèles** - pour vous aider à démarrer nous avons fourni une [Galerie de modèles de] [ templates] vous permettent de créer rapidement des solutions communes. À partir de solutions avancées de B2B pour une connectivité simple SaaS et même quelques qui lui sont « fun » : la galerie est le moyen le plus rapide pour commencer à utiliser la puissance de la logique d’applications.

- **Extensibilité cuit-dans** - ne voyez pas le connecteur dont vous avez besoin ? Logique d’applications est conçu pour fonctionner avec vos propres API et le code ; Vous pouvez facilement créer votre propre application d’API à utiliser comme un connecteur personnalisé, ou appeler une [Fonction d’Azure](https://functions.azure.com) pour exécuter des extraits de code à la demande. 

- **La puissance réelle intégration** - démarrez facile et évoluer à mesure que vous avez besoin. Logique d’applications peut facilement exploiter la puissance de BizTalk, Microsoft intégration solution marché pour permettre aux professionnels de l’intégration créer les solutions que dont ils ont besoin. Pour en savoir plus sur le [Pack d’intégration entreprise](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Concepts d’application logique

Voici quelques-uns des éléments clés qui composent l’expérience des applications de logique. 

- **Workflow** - logique d’applications fournit un moyen graphique de vos processus métier du modèle sous la forme d’une série d’étapes ou d’un flux de travail.
- **Gérer les connecteurs** - vos applications logique ont besoin d’accéder aux données et services. Connecteurs managés sont créés spécifiquement pour vous aider lorsque vous connectez et travailler avec vos données. Consultez la liste des connecteurs disponibles maintenant dans la [Gestion des connecteurs][managedapis].
- **Déclencheurs** - certains connecteurs de Managed peut également agir comme un déclencheur. Un déclencheur démarre une nouvelle instance d’un workflow basé sur un événement spécifique, comme l’arrivée d’un message électronique ou une modification de votre compte de stockage Azure.
-  **Actions** - chaque étape après avoir appelé une action du déclencheur dans un flux de travail. Chaque action correspond généralement à une opération sur votre connecteur managé ou les applications d’API personnalisées.
- **Pack d’intégration entreprise** - pour les scénarios d’intégration plus avancées, les applications de logique comprend les fonctionnalités de BizTalk. BizTalk est la plate-forme d’intégration de Microsoft secteur non significatif. Les connecteurs Enterprise Integration Pack permettent d’inclure facilement la validation, la transformation et plus dans vos flux de travail d’application de la logique.

## <a name="getting-started"></a>Mise en route  

- Pour vous familiariser avec les applications de logique, suivez la [Création d’une application logique] [ create] didacticiel.  
- [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
- [Vous pouvez automatiser des processus d’entreprise avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Apprenez à intégrer vos systèmes avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
