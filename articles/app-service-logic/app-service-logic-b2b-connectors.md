<properties 
    pageTitle="Connecteurs de Business-to-Business et les API des applications dans les applications de logique | Microsoft Azure" 
    description="Découvrez comment créer et configurer des connecteurs de module de plateforme sécurisée, EDIFACT, AS2 et EDI ; architecture de microservices" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Connecteurs de Business-to-Business et les applications de l’API

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Logique d’applications inclut de nombreuses applications API BizTalk qui sont essentielles pour les environnements d’intégration. Ces applications API sont basées sur les concepts et les outils utilisés au sein de BizTalk Server, mais sont désormais disponibles dans le cadre de la logique d’applications. 

Une catégorie de ces applications d’API sont les applications d’API Business-to-Business (B2B). À l’aide de ces applications d’API B2B, vous pouvez facilement ajouter des partenaires, création de contrats et faire tout ce que vous aurait sur site à l’aide de EDIFACT EDI et AS2.  

Ces applications d’API B2B offrent des fonctionnalités de « Déclenchement » et « Action ». Un déclencheur démarre une nouvelle instance basée sur un événement spécifique, comme l’arrivée d’une X12 message d’un partenaire. Une Action est le résultat, comme après une X12 de réception du message, puis envoyer le message à l’aide de AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>Quel est un Business-to-Business Connector ou les applications d’API
La fonctionnalité de Business-to-Business (B2B) inclut des applications API existantes et prédéfinis qui permettent de sociétés différentes, les divisions, les applications, et ainsi de suite pour communiquer à l’aide de EDIFACT, EDI et AS2. 

Les applications d’API B2B sont les suivantes : 

Connecteur ou applications d’API | Description
--- | ---
Gestion des partenaires commerciaux BizTalk | Une application API qui crée des relations d’entreprise-entreprise (B2B) à l’aide des partenaires et des accords. Ces relations utilisent le AS2, EDIFACT et X12 protocole.<br/><br/>L’application d’API de module de plateforme sécurisée est la base du connecteur AS2 et le X12 ou applications d’API EDIFACT. 
Connecteur AS2 | Un connecteur qui reçoit et envoie des messages à l’aide de transport AS2. Le connecteur transporte des données en toute sécurité et fiabilité sur Internet.
BizTalk EDIFACT | Une application API qui reçoit et envoie des messages à l’aide de EDIFACT. EDIFACT est aussi couramment appelé UN/EDIFACT (données d’organisation des Nations unies/électronique Interchange For Administration, Commerce and Transport) et est largement utilisé dans les secteurs d’activité.
BizTalk X12 | Une application API qui reçoit et envoie des messages à l’aide de la X12 protocole. X12 est aussi couramment appelé ASC X 12 (Accredited Standards Committee X12) et est largement utilisé dans les secteurs d’activité. 


À l’aide de ces applications d’API, vous pouvez effectuer des EDI de différente tâches de messagerie. Par exemple, à l’aide du connecteur AS2, vous pouvez en toute sécurité recevoir et envoyer différents types de messages (EDI, XML, FlatFile, et ainsi de suite) à un client, une division de votre entreprise comme les ressources humaines ou à toute personne qui utilise AS2. 

Vous pouvez créer des applications API autant que vous le souhaitez les créez facilement. Vous pouvez également réutiliser une seule API App dans plusieurs scénarios ou des flux de travail.

Vous pouvez le faire sans écrire de code. Allons-y. 


## <a name="requirements-to-get-started"></a>Configuration requise pour la mise en route
Lorsque vous créez des applications d’API B2B, il existe certaines ressources nécessaires. Ces éléments doivent être créés par vous, avant de pouvoir être utilisés dans d’autres applications d’API. Ces exigences sont les suivantes : 

Exigence | Description
--- | ---
Base de données SQL Azure | Stocke les éléments B2B, y compris des partenaires, des schémas, des certificats et des accords. Chacune des applications API B2B nécessite sa propre base de données de SQL Azure. <br/><br/>**Remarque** Copier la chaîne de connexion à cette base de données.<br/><br/>[Créer une base de données SQL Azure](../sql-database/sql-database-get-started.md)
Conteneur de stockage des objets Blob Azure | Stocke les propriétés de message lorsque vous AS2 l’archivage est activé. Si vous n’avez pas besoin d’archivage de messages AS2, un conteneur de stockage n’est pas nécessaire. <br/><br/>**Remarque** Si vous activez l’archivage, copiez la chaîne de connexion à ce stockage Blob.<br/><br/>[À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md)
Namespace de Bus de service et de ses valeurs de clé | Stocke les X12 et EDIFACT, le traitement par lots de données. Si vous ne devez pas le traitement par lots, le Bus de Service d’un espace de noms n’est pas nécessaire.<br/><br/>**Remarque** Si vous activez le traitement par lots, copiez ces valeurs.<br/><br/>[Créer un Namespace de Bus de Service](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instance du module de plateforme sécurisée | Une instance de gestion des partenaires commerciaux (GPC) BizTalk est requis pour créer un connecteur AS2 et X12 ou EDIFACT API App. Lorsque vous créez l’application API de module de plateforme sécurisée, vous créez l’Instance du module de plateforme sécurisée. <br/><br/>**Remarque** Connaître le nom de votre application d’API de module de plateforme sécurisée. 


## <a name="create-the-api-apps"></a>Créer des applications API
Applications d’API B2B peuvent être créées via le portail Azure, ou à l’aide des API de reste. 


### <a name="create-the-api-apps-using-rest-apis"></a>Créer des applications API à l’aide des API de repos
[Consultez la documentation sur l’utilisation de l’API de reste.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Créer des applications API B2B dans le portail Azure
Dans le portail Azure, vous pouvez créer des applications d’API B2B lors de la création d’applications de logique, les applications Web ou applications Mobile. Ou bien, vous pouvez en créer un à l’aide de son propre lame. Les deux méthodes sont faciles, donc il dépend de vos besoins ou les préférences. Certains utilisateurs préfèrent créer d’abord toutes les applications d’API B2B avec leurs propriétés spécifiques. Ensuite, créer des applications mobiles d’applications/applications/Web logique et ajouter les applications d’API B2B vous avez créé.  

Les étapes suivantes créent des applications B2B API à l’aide de la lame d’applications d’API.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Créer la négociation des applications API de gestion (TPM) partenaire de BizTalk

> [AZURE.NOTE] Une instance de gestion des partenaires commerciaux (GPC) BizTalk est requis pour créer un connecteur AS2 et X12 ou EDIFACT API App. Lorsque vous créez l’application API de module de plateforme sécurisée, vous créez l’Instance du module de plateforme sécurisée.

Les étapes suivantes créent l’instance du module de plateforme sécurisée :

1. Dans le portail Azure Startboard (la page d’accueil), sélectionnez **Marketplace**. **Applications d’API** répertorie toutes les applications d’API existantes et les connecteurs. Vous pouvez également **Rechercher** pour les applications d’API B2B spécifique.
2. Sélectionnez **Gestion des partenaires commerciaux de BizTalk**. Dans la nouvelle blade, sélectionnez **créer**. 
3. Entrez les propriétés : 

    Propriété | Description
--- | ---
Nom | Entrez un nom pour l’instance du module de plateforme sécurisée. Par exemple, vous pouvez nommez-le *AccountsPayableTPM*.
Paramètres de package | La base de données de SQL Azure créé, entrez la **Chaîne de connexion de base de données** de ADO.NET. <br/><br/>Lorsque vous copiez la chaîne de connexion, le mot de passe n’est pas ajouté à la chaîne de connexion. Veillez à entrer le mot de passe dans la chaîne de connexion.
Plan de Service d’application | Listes de votre plan de paiement. Vous pouvez le modifier si vous avez besoin plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui répertorie la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créer un nouveau ou utiliser un groupe existant. Toutes les applications d’API et des connecteurs pour votre logique d’applications, les applications Web et les applications de Mobile doivent être dans le même groupe de ressources. <br/><br/>[À l’aide de groupes de ressources](../azure-resource-manager/resource-group-overview.md) explique cette propriété. 
Abonnement | Propriété en lecture seule qui répertorie vos abonnements actuels.
Emplacement | L’emplacement géographique qui héberge votre service Azure. 
Ajouter au Startboard | Sélectionnez cette option pour ajouter l’application API B2B à votre tribord (la page d’accueil).

4. Sélectionnez **créer**. 

Après avoir créé l’application API de module de plateforme sécurisée (Instance du module de plateforme sécurisée), vous pouvez ensuite créer le connecteur AS2 ou le X12 ou applications d’API EDIFACT. 


#### <a name="create-the-as2-connector"></a>Créer le connecteur AS2

1. Dans le portail Azure Startboard (la page d’accueil), sélectionnez **Marketplace**. **Applications d’API** répertorie toutes les applications d’API existantes et les connecteurs. Vous pouvez également **Rechercher** pour les applications d’API B2B spécifique.
2. Sélectionnez le **connecteur AS2**. Dans la nouvelle blade, sélectionnez **créer**. 
3. Entrez les propriétés : 

    Propriété | Description
--- | ---
Nom | Entrez un nom pour le connecteur AS2. Par exemple, vous pouvez nommez-le *AS2Connector*.
Paramètres de package | Entrez les paramètres spécifiques à l’application de cette API, telles que le nom d’Instance du module de plateforme sécurisée. <br/><br/>Dans cette rubrique pour les propriétés spécifiques, reportez-vous à la section [Ajouter des paramètres de Package AS2](#AddAS2Conn) . 
Plan de Service d’application | Listes de votre plan de paiement. Vous pouvez le modifier si vous avez besoin plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui répertorie la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créer un nouveau ou utiliser un groupe existant. [À l’aide de groupes de ressources](../azure-resource-manager/resource-group-overview.md) explique cette propriété. 
Abonnement | Propriété en lecture seule qui répertorie vos abonnements actuels.
Emplacement | L’emplacement géographique qui héberge votre service Azure. 
Ajouter au Startboard | Sélectionnez cette option pour ajouter l’application API B2B à votre tribord (la page d’accueil).

    **<a name="AddAS2Conn"></a>Connecteur AS2 paramètres de Package**

    Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET pour la base de données de SQL Azure vous avez créé. Lorsque vous copiez la chaîne de connexion, le mot de passe n’est pas ajouté à la chaîne de connexion. Veillez à entrer le mot de passe dans la chaîne de connexion avant de coller.
Activer l’archivage des messages entrants | Facultatif. Activer cette propriété stocker les propriétés du message d’un message AS2 entrant envoyé par un partenaire. 
Chaîne de connexion du stockage Blob Azure  | Entrez la chaîne de connexion pour le conteneur de stockage des objets Blob Azure que vous avez créé. Lorsque l’archivage est activé, les messages encodés et décodés sont stockées dans ce conteneur de stockage.
Nom de l’Instance TPM | Entrez le nom de la **Gestion des partenaires commerciaux BizTalk** application API créé précédemment. Lorsque vous créez le connecteur AS2, ce connecteur s’exécute uniquement les accords AS2 dans cette instance spécifique de module de plateforme sécurisée.

4. Sélectionnez **créer**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Créer des applications API X12 ou EDIFACT

1. Dans le portail Azure Startboard (la page d’accueil), sélectionnez **Marketplace**. **Applications d’API** répertorie toutes les applications d’API existantes et les connecteurs. Vous pouvez également **Rechercher** pour les applications d’API B2B spécifique.
2. Sélectionnez **BizTalk X 12** ou **EDIFACT de BizTalk**. Dans la nouvelle blade, sélectionnez **créer**. 
3. Entrez les propriétés : 

    Propriété | Description
--- | ---
Nom | Entrez un nom pour l’application d’API B2B. Par exemple, vous pouvez nommez-le *EDI850APIApp*.
Paramètres de package | Entrez les paramètres spécifiques à l’application de cette API, telles que le nom d’Instance du module de plateforme sécurisée. <br/><br/>Dans cette rubrique pour les propriétés spécifiques, reportez-vous à la section [X12 ou des paramètres de Package EDIFACT](#AddX12) . 
Plan de Service d’application | Listes de votre plan de paiement. Vous pouvez le modifier si vous avez besoin plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui répertorie la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créer un nouveau ou utiliser un groupe existant. [À l’aide de groupes de ressources](../azure-resource-manager/resource-group-overview.md) explique cette propriété. 
Abonnement | Propriété en lecture seule qui répertorie vos abonnements actuels.
Emplacement | L’emplacement géographique qui héberge votre service Azure. 
Ajouter au Startboard | Sélectionnez cette option pour ajouter l’application API B2B à votre tribord (la page d’accueil).

    **<a name="AddX12"></a>Paramètres de Package des applications d’API X12 et EDIFACT**  

    Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET pour la base de données de SQL Azure vous avez créé. Lorsque vous copiez la chaîne de connexion, le mot de passe n’est pas ajouté à la chaîne de connexion. Veillez à entrer le mot de passe dans la chaîne de connexion avant de coller.
Namespace de Bus de service | Entrez l’espace de noms Bus de Service que vous avez créé. Requis uniquement lorsque le traitement par lots est activé. 
Nom de la clé d’accès partagés Service Bus Namespace | Entrez l’espace de noms du Service Bus touche d’accès rapide vous avez créé. Requis uniquement lorsque le traitement par lots est activé. 
Valeur de clé d’accès partagés Service Bus Namespace | Entrez l’espace de noms du Service Bus valeur de touche d’accès rapide que vous avez créé. Requis uniquement lorsque le traitement par lots est activé. 
Nom de l’Instance TPM | Entrez le nom de la **Gestion des partenaires commerciaux BizTalk** application API créé précédemment. Lorsque vous créez la X12 ou des applications d’API EDIFACT, cette application API exécute les accords de X12/EDFIACT dans cette instance spécifique de module de plateforme sécurisée.

4. Sélectionnez **créer**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Ajoutez vos partenaires, les accords, les certificats et les schémas 
Dans le portail Azure, ouvrez votre application d’API de module de plateforme sécurisée. Dans la section **composants** , ajoutez vos partenaires, les accords, les certificats et les schémas. 

Vous pouvez également ajouter des accords à vos liens AS2, X12 les applications API et applications d’API EDIFACT. 


## <a name="monitor-your-api-apps"></a>Surveillance de vos applications d’API
Dans le portail Azure, ouvrez votre application d’API de module de plateforme sécurisée. Dans la section **opérations** , vous pouvez afficher les opérations de gestion. Par exemple, vous pouvez :

- Événements d’erreur et d’information d’affichage
- Afficher le nombre de thread et de l’utilisation de mémoire du processus de travail (w3wp)
- Afficher les journaux de serveur web et d’Application

Plus à [vos applications de logique de moniteur](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Ajouter les applications API pour votre application 
Service d’application Microsoft Azure expose différents types d’applications qui peuvent utiliser ces applications d’API B2B. Vous pouvez créer un nouveau ou ajouter vos applications d’API B2B existantes pour une applications Web, applications Mobile ou logique d’applications. 

Dans votre application, simplement en sélectionnant les applications d’API B2B de la galerie automatiquement ajoute à votre application.  

> [AZURE.IMPORTANT] Pour ajouter des connecteurs et des applications d’API vous avez précédemment créé, créer la logique d’applications, applications Mobile ou les applications Web dans le même groupe de ressources. 

Les étapes suivantes ajoutent des applications B2B API pour les applications Web, applications Mobile ou logique d’applications : 

1. Dans le portail Azure Startboard (page d’accueil), accédez à la **Marketplace**et recherchez votre logique, mobiles ou pour les applications Web. 

    Si vous créez une nouvelle application, rechercher des applications Web, applications Mobile ou logique d’applications. Sélectionnez l’application et dans la nouvelle blade, sélectionnez **créer**. [Créer une application de logique](app-service-logic-create-a-logic-app.md) répertorie les étapes. 

2. Ouvrez votre application et sélectionnez les **déclencheurs et les Actions**. 

3. À partir de la **Galerie**, sélectionnez l’application API B2B, qui l’ajoute automatiquement à votre application. Vous pouvez également créer une nouvelle application d’API B2B.

    > [AZURE.IMPORTANT] Le connecteur AS2 et X12, EDIFACT API applications nécessitent une Instance du module de plateforme sécurisée. Si vous créez des nouvelles applications d’API B2B, créez d’abord l’application API de module de plateforme sécurisée et ensuite créer le connecteur AS2, X12 API App ou EDIFACT API App. 

4. Cliquez sur **OK** pour enregistrer vos modifications. 

>[AZURE.NOTE] Mise en route avec les applications Azure logique avant l’ouverture d’un compte Azure, [Essayez les applications logique](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application de logique de démarrage de courte durée. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="more-b2b-resources"></a>Plus de ressources B2B

[Création d’un processus B2B](app-service-logic-create-a-b2b-process.md)<br/>
[Création d’un contrat de partenaire commercial](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[Quels sont les connecteurs et les applications d’API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>En savoir plus sur les applications de la logique et les applications Web
[Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md)<br/>
[Sites Web et applications Web dans le Service d’application Azure](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Plus de connecteurs

[Connecteurs et liste d’applications API](app-service-logic-connectors-list.md)<br/><br/>
[Quels sont les connecteurs et les applications d’API BizTalk](app-service-logic-what-are-biztalk-api-apps.md) 
