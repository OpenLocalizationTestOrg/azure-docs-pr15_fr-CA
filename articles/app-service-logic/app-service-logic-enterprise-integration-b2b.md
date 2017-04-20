<properties 
    pageTitle="Création de solutions de B2B avec Pack d’intégration entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="En savoir plus sur la réception de données à l’aide des fonctionnalités du Pack intégration entreprise B2B" 
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
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>En savoir plus sur la réception de données à l’aide des fonctionnalités du Pack intégration entreprise B2B#

## <a name="overview"></a>Vue d’ensemble ##

Ce document fait partie de la logique applications Enterprise Integration Pack. Consultez la présentation pour en savoir plus sur les [capacités d’intégration Pack Enterprise](./app-service-logic-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Conditions préalables ##

Pour utiliser le AS2 et X12 actions, vous aurez besoin d’un compte d’intégration entreprise

[Comment faire pour créer un compte d’intégration entreprise](./app-service-logic-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>Comment utiliser les connecteurs logique applications B2B ##

Une fois que vous avez créé un compte d’intégration et ajouté des partenaires et des accords à elle, vous êtes prêt à créer une application de logique qui implémente un flux de travail interentreprises (B2B).

Dans cet walkthru, vous verrez comment utiliser les AS2 et X12 actions pour créer une application de logique d’entreprise à entreprise qui reçoit des données à partir d’un partenaire commercial.

1. Créer une application de logique et [le lier à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md).  
2. Ajouter un déclencheur **- requête HTTP d’un lors de la demande** à votre application logique  
![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)  
3. Ajoutez l’action **AS2 de décoder** en premier la sélection **Ajouter une action**  
![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)  
4. Entrez mot **as2** dans la zone de recherche pour filtrer toutes les actions à celui que vous souhaitez utiliser  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)  
6. Sélectionnez l’action **AS2 - message de décoder les AS2**  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)  
7. Comme indiqué, ajouter l' **organisme** qui vous prendra comme entrée. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique. Vous pouvez également entrer une expression d’entrée les en-têtes dans le champ**d’en-têtes** :

    @triggerOutputs()['headers']

8. Ajoutez les **en-têtes** qui sont requis pour AS2. Il s’agit dans les en-têtes de demande HTTP. Dans cet exemple, sélectionnez les en-têtes de la demande HTTP qui a déclenché l’application logique.
9. L’action du message X12 de décodage à présent ajouter en sélectionnant à nouveau **Ajouter une action**  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)   
10. Entrez le mot **x12** dans la zone de recherche pour filtrer toutes les actions à celui que vous souhaitez utiliser  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)  
11. Sélectionnez le **X12-X12 de décodage message** action à ajouter à l’application de la logique  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)  
12. Vous devez maintenant spécifier l’entrée de cette action qui correspond à la sortie de l’action AS2 ci-dessus. Le contenu réel des messages est un objet JSON et est crypté en Base64. Vous devez par conséquent spécifier une expression comme l’entrée ainsi, entrez l’expression suivante dans le champ d’entrée **X12 plat fichier MESSAGE à décoder**  

    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  

13. Cette étape sera de décoder la X12 données reçues du partenaire commercial et génère un nombre d’éléments dans un objet JSON. Pour informer les partenaires de la réception des données vous pouvez renvoyer une réponse qui contient la Notification de destruction Message AS2 (MDN) dans une Action de réponse HTTP  
14. Ajoutez l’action de **réponse** en sélectionnant **Ajouter une action**   
![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)  
15. Entrez la **réponse** du mot dans la zone de recherche pour filtrer toutes les actions à celui que vous souhaitez utiliser  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)  
16. Sélectionnez l’action de **réponse** pour l’ajouter  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)  
17. La valeur du champ de réponse du **corps** à l’aide de l’expression suivante pour accéder à la MDN à partir de la sortie de l’action du **message de décodage X12**  

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)  
18. Enregistrez votre travail.  
![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)  

À ce stade, vous avez terminé de configurer votre application B2B logique. Dans une application réelle, vous souhaitez peut-être stocker le décodée X12 les données dans un magasin de données ou application cœur de métier. Vous pouvez facilement ajouter des actions supplémentaires pour effectuer cette opération ou d’écrire des API personnalisés pour se connecter à vos propres applications métier et d’utiliser ces API dans votre logique d’application.

## <a name="features-and-use-cases"></a>Fonctions et exemples d’utilisation ##

- X12 le AS2 décoder et encoder des actions vous permettent de recevoir des données et envoyer des données à l’aide de protocoles standard à l’aide d’applications de la logique des partenaires commerciaux  
- Vous pouvez utiliser AS2 et X12 avec ou sans eux pour échanger des données avec des partenaires commerciaux comme requis
- Les actions B2B facilitent la création des partenaires et des accords dans le compte de l’intégration et les consommer dans une logique d’application  
- En étendant votre application logique avec d’autres actions, vous pouvez envoyer et recevoir des données vers et à partir d’autres applications et les services tels que la force de vente  

## <a name="learn-more"></a>Pour en savoir plus ##

[En savoir plus sur le Pack d’intégration entreprise](./app-service-logic-enterprise-integration-overview.md)  