<properties 
    pageTitle="En savoir plus sur Enterprise Integration Pack décoder X12 Message Connctor | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Apprenez à utiliser les partenaires avec les applications Enterprise Integration Pack et logique" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-decode-x12-message"></a>Mise en route de décodage X12 Message

Valide EDI et des propriétés spécifiques au partenaire, génère le document XML pour chaque ensemble de transaction ainsi d’accusé de réception pour la transaction traitée.

## <a name="create-the-connection"></a>Créer la connexion

### <a name="prerequisites"></a>Conditions préalables

* Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)

* Un compte d’intégration est requis pour utiliser de connecteur de message X12 de décodage. Voir plus d’informations sur la façon de créer un [Compte d’intégration](./app-service-logic-enterprise-integration-create-integration-account.md), [les partenaires](./app-service-logic-enterprise-integration-partners.md) et [X12 contrat de](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>Se connecter à un Message de décodage X12 à l’aide des étapes suivantes :

1. [Créer une application de logique](./app-service-logic-create-a-logic-app.md) fournit un exemple

2. Ce connecteur n’a pas de déclencheurs. Autres déclencheurs permet de démarrer l’application logique, tel qu’un déclencheur de la demande.  Dans le Concepteur de l’application de la logique, ajouter un déclencheur et ajouter une action.  Dans la zone Rechercher, sélectionnez Afficher Microsoft API gérées dans la liste déroulante liste et entrez « x12 ».  Sélectionnez X12 – décoder X12 Message

    ![recherche x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)  

3. Si vous n’avez pas encore créé toutes les connexions au compte de l’intégration, vous êtes invité à entrer les détails de connexion

    ![connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)    

4. Permet d’entrer les détails du compte de l’intégration.  Les propriétés d’un astérisque sont obligatoires

  	| Propriété | Détails |
  	| -------- | ------- |
  	| Nom de connexion * | Entrez un nom pour votre connexion. |
  	| Intégration compte * | Entrez le nom du compte de l’intégration. Assurez-vous que votre compte d’intégration et d’une application de logique sont dans le même emplacement Azure |

    Une fois terminé, les détails de votre connexion ressemblent à ce qui suit
    
    ![connexion de compte intégration créée](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png) 

5. Sélectionnez **créer**
    
6. Notez que la connexion a été créée.

    ![Détails de connexion de compte intégration](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png) 

7. Sélectionnez X12 plat message de fichier de décodage

    ![fournir des champs obligatoires](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>X12 décoder fait suivant

* Valide l’enveloppe par rapport à la négociation du contrat de partenariat
* Génère un document XML pour chaque ensemble de transactions.
* Valide l’EDI et des propriétés spécifiques au partenaire
    * Validation structurelle de EDI et la validation du schéma étendu
    * Validation de la structure de l’enveloppe d’échange.
    * Validation de schéma de l’enveloppe par rapport au schéma du contrôle.
    * Validation de schéma des éléments de données de l’ensemble de la transaction par rapport au schéma de message.
    * Validation de l’EDI effectuée sur des éléments de données de l’ensemble de la transaction 
* Vérifie que les numéros de contrôle de jeu échange, de groupe et de transaction ne sont pas des doublons
    * Vérifie le numéro de contrôle d’échange contre des échanges reçus précédemment.
    * Vérifie le numéro de contrôle de groupe par rapport à d’autres numéros de contrôle de groupe dans l’échange.
    * Vérifie la transaction numéro de contrôle par rapport à d’autres numéros de contrôle de jeu de transaction dans ce groupe.
* Convertit l’entier échange XML 
    * Échange de fractionnement en tant que jeux de transactions - suspendre des ensembles de transaction sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct. Si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, X12 Decode ne suspend que les jeux de la transaction.
    * Échange de fractionnement en tant que jeux de transactions - suspendre l’échange sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct.  Si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, X12 Decode suspend l’échange complet.
    * Conserver l’échange - suspendre des ensembles de transaction sur l’erreur : crée un document XML pour l’échange de lots entier. X12 décoder interrompt uniquement ces jeux de transactions que la validation a échoué, tout en continuant à traiter toutes les autres transactions définit
    * Conserver l’échange - échange de suspension sur l’erreur : crée un document XML pour l’échange de lots entier. Si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, X12 Decode suspend l’échange complet, 
* Génère un accusé de réception technique et/ou fonctionnel (si configuré).
    * Il génère un accusé de réception technique par suite de la validation de l’en-tête. L’accusé de réception technique signale l’état du traitement d’un en-tête d’échange et de la remorque par le destinataire de l’adresse.
    * Un accusé de réception fonctionnel génère en raison de la validation de l’organisme. L’accusé de réception fonctionnel signale chaque erreur s’est produite lors du traitement du document reçu

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise") 


