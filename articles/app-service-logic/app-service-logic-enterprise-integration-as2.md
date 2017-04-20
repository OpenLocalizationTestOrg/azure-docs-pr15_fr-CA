<properties 
    pageTitle="Apprenez à créer un accord AS2 pour le Pack d’intégration entreprise" 
    description="Apprenez à créer un accord AS2 pour le Pack d’intégration entreprise | Service d’application Microsoft Azure" 
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
    ms.date="06/29/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-as2"></a>Intégration avec AS2

## <a name="create-an-as2-agreement"></a>Créer un accord AS2
Pour utiliser les fonctionnalités d’entreprise dans les applications de logique, vous devez d’abord créer les accords. 

### <a name="heres-what-you-need-before-you-get-started"></a>Voici ce dont vous avez besoin avant de commencer
- Un [compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) définies dans votre abonnement Azure  
- Au moins deux [partenaires](./app-service-logic-enterprise-integration-partners.md) déjà défini dans votre compte d’intégration  

>[AZURE.NOTE]Lors de la création d’un accord, le contenu du fichier de contrat doit correspondre le type d’accord.    


Une fois que vous avez [créé un compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) et [ajouté des partenaires](./app-service-logic-enterprise-integration-partners.md), vous pouvez créer un accord en suivant ces étapes :  

### <a name="from-the-azure-portal-home-page"></a>À partir de la page d’accueil de portail Azure

Après la connexion au [portail Azure](http://portal.azure.com "Azure portal"):  
1. Dans le menu de gauche, sélectionnez **Parcourir** .  

>[AZURE.TIP]Si vous ne voyez pas le lien **Parcourir** , vous devrez tout d’abord, développez le menu. Pour ce faire, cliquez sur le lien **Afficher le menu** qui est situé en haut à gauche du menu réduit.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Type *d’intégration* dans la zone de filtre de recherche, puis sélectionnez les **Comptes de l’intégration** à partir de la liste des résultats.       
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Dans la lame de **Comptes d’intégration** qui s’ouvre, sélectionnez le compte de l’intégration dans lequel vous allez créer l’accord. Si vous ne voyez pas une intégration des listes, comptes [créer un premier](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4.  Sélectionnez la **accords de** mosaïque. Si vous ne voyez pas les accords de mosaïque, ajouter en premier.   
![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
5. Sélectionnez le bouton **Ajouter** dans la blade d’accords qui s’ouvre.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Entrez un **nom** pour votre accord, puis sélectionnez le **Partenaire d’accueil** **Identité de l’hôte**, **Partenaire de l’invité**, **Identité des invités**, de la lame d’accords qui s’ouvre.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

Voici quelques détails que peut s’avérer utile lorsque vous configurez les paramètres de votre contrat : 
  
|Propriété|Description|
|----|----|
|Partenaire d’accueil|Un accord doit être partenaire d’un hôte et invité. Le partenaire d’accueil représente l’organisation qui configure l’accord.|
|Identité de l’hôte|Un identificateur pour le partenaire d’accueil. |
|Partenaire de l’invité|Un accord doit être partenaire d’un hôte et invité. Le partenaire invité représente l’organisation fait des affaires avec le partenaire d’accueil.|
|Identité de l’invité|Un identificateur pour le partenaire de l’invité.|
|Recevoir des paramètres|Ces propriétés s’appliquent à tous les messages reçus par un accord|
|Les paramètres d’envoi|Ces propriétés s’appliquent à tous les messages envoyés par un accord|  
Nous allons continuer :  
7. Sélectionnez **Recevoir des paramètres** pour configurer la manière dont les messages reçus via le présent accord doivent être traités.  
 
 - Si vous le souhaitez, vous pouvez substituer les propriétés dans le message entrant. Pour ce faire, activez la case à cocher **Remplacer les propriétés de message** .
  - Sélectionnez la case à cocher **Message devrait être signé** si vous souhaitez exiger de tous les messages entrants doivent être signées. Si vous sélectionnez cette option, vous devez également sélectionner le **certificat** qui sera utilisé pour valider la signature sur les messages.
  - Si vous le souhaitez, vous pouvez demander des messages soient cryptés également. Pour ce faire, activez la case à cocher **Message devrait être crypté** . Vous devez alors sélectionner le **certificat** qui sera utilisé pour décoder les messages entrants.
  - Vous pouvez également exiger des messages doit être compressée. Pour ce faire, activez la case à cocher **Message doit être compressé** .  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

Reportez-vous au tableau ci-dessous si vous souhaitez en savoir plus sur ce que la réception activent les paramètres.  

|Propriété|Description|
|----|----|
|Substituer des propriétés de message|Sélectionnez cette option pour indiquer que les propriétés dans les messages reçus peuvent être substituées |
|Message doit être signé.|Activez cette option pour demander à signer numériquement les messages|
|Message doit être chiffré.|Activez cette option pour demander le cryptage des messages. Les messages non chiffrés seront rejetées.|
|Message doit être compressé.|Activez cette option pour demander les messages à compresser. Non compressé les messages seront rejetés.|
|Texte MDN|Il s’agit d’une valeur par défaut MDN à envoyer à l’expéditeur du message|
|Envoyer MDN|Activez cette option pour autoriser le MDNs à envoyer.|
|Envoyer MDN signé|Activez cette option pour demander le MDNs à signer.|
|Algorithme de MIC||
|Envoyer MDN asynchrone|Activez cette option pour demander les messages à être envoyé de façon asynchrone.|
|URL|Il s’agit de l’URL à laquelle les messages seront envoyés.|
Maintenant, nous allons continuer :  
8. Sélectionnez **Envoyer des paramètres** pour configurer la manière dont les messages envoyés via le présent accord doivent être traités.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

Reportez-vous au tableau ci-dessous si vous souhaitez en savoir plus sur l’envoyer activent les paramètres.  

|Propriété|Description|
|----|----|
|Activer la signature du message|Activez cette case à cocher pour activer tous les messages envoyés à partir du contrat à signer.|
|Algorithme de MIC|Sélectionnez l’algorithme à utiliser lors de la signature du message|
|Certificat|Sélectionnez le certificat à utiliser lors de la signature du message|
|Activer le cryptage des messages|Cochez cette case pour crypter tous les messages envoyés à partir de cet accord.|
|Algorithme de cryptage|Sélectionnez l’algorithme de chiffrement à utiliser pour le cryptage des messages|
|Déroulement des en-têtes HTTP|Activez cette case à cocher pour le déroulement de l’en-tête content-type HTTP sur une seule ligne.|
|Demande MDN|Activer cette case à cocher Demander un MDN pour tous les messages envoyés à partir de cet accord.|
|Demande signée MDN|Activer pour demander que toutes les MDNs envoyés au présent accord sont signés.|
|Demande asynchrone MDN|Activer la demande asynchrone MDN à envoyer au présent accord|
|URL|L’URL à laquelle MDNs seront envoyés|
|Activer NRR|Cochez cette case pour activer la Non répudiation de réception|
Nous avons presque terminé !  
9. Sélectionnez la mosaïque **d’accords** sur la lame de compte de l’intégration, et vous verrez l’accord nouvellement ajouté répertorié.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

