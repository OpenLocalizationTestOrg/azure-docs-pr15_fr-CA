<properties 
    pageTitle="Vue d’ensemble de la validation de XML dans le Pack d’intégration entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Apprenez comment fonctionne la validation dans les applications Enterprise Integration Pack et logique" 
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

# <a name="enterprise-integration-with-xml-validation"></a>Intégration avec la validation de XML

## <a name="overview"></a>Vue d’ensemble
Souvent, dans les scénarios B2B, les partenaires à un accord doivent valider que les messages qu’ils échangent entre eux sont valides avant de débuter le traitement des données. Dans le module d’intégration entreprise, vous pouvez utiliser le connecteur de Validation XML pour valider des documents par rapport à un schéma prédéfini.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Comment valider un document avec le connecteur de la Validation XML
1. Créer une application de logique et la [lier à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md "apprendre à lier un compte de l’intégration à une application de logique") qui contient le schéma que vous utiliserez pour valider les données XML.
2. Ajouter un déclencheur **- requête HTTP d’un lors de la demande** à votre application logique  
![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. Ajoutez l’action de **Validation XML** par la première sélection **Ajouter une action**  
4. Entrez le *xml* dans la zone de recherche pour filtrer toutes les actions à celui que vous souhaitez utiliser 
5. Sélectionnez **Validation XML**     
![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. Sélectionnez la zone de texte **contenu**  
![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Sélectionnez la balise body comme le contenu qui sera validé.   
![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. Sélectionnez la zone de liste **Nom de schéma** et sélectionnez le schéma que vous souhaitez utiliser pour valider la saisie du *contenu* ci-dessus     
![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. Enregistrez votre travail.  
![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

À ce stade, vous avez terminé de configurer votre connecteur de validation. Dans une application réelle, vous souhaiterez stocker les données validées dans une application métier, tels que la force de vente. Vous pouvez facilement ajouter une action pour envoyer le résultat de la validation de la force de vente. 

Vous pouvez maintenant tester votre action de validation en effectuant une demande au point de terminaison HTTP.  

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise")   