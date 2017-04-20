<properties 
    pageTitle="En savoir plus sur Enterprise Integration Pack Encoder X12 Message Connctor | Service d’application Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-encode-x12-message"></a>Mise en route de coder X12 Message

Valide l’EDI et des propriétés spécifiques au partenaire, convertit les messages codés sur le XML en jeux de transactions EDI dans l’échange et demande un accusé de réception technique et/ou fonctionnel

## <a name="create-the-connection"></a>Créer la connexion

### <a name="prerequisites"></a>Conditions préalables

* Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)

* Un compte d’intégration est requis pour utiliser de connecteur de message Encoder x12. Voir plus d’informations sur la façon de créer un [Compte d’intégration](./app-service-logic-enterprise-integration-create-integration-account.md), [les partenaires](./app-service-logic-enterprise-integration-partners.md) et [X12 contrat de](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>Se connecter à un Message X12 d’encoder à l’aide des étapes suivantes :

1. [Créer une application de logique](./app-service-logic-create-a-logic-app.md) fournit un exemple

2. Ce connecteur n’a pas de déclencheurs. Autres déclencheurs permet de démarrer l’application logique, tel qu’un déclencheur de la demande.  Dans le Concepteur de l’application de la logique, ajouter un déclencheur et ajouter une action.  Dans la zone Rechercher, sélectionnez Afficher Microsoft API gérées dans la liste déroulante liste et entrez « x12 ».  Sélectionnez soit X12-Encoder X12 Message par un nom de contrat ou la X12-message X 12 de coder par des identités.  

    ![recherche x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 

3. Si vous n’avez pas encore créé toutes les connexions au compte de l’intégration, vous êtes invité à entrer les détails de connexion

    ![connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 


4. Permet d’entrer les détails du compte de l’intégration.  Les propriétés d’un astérisque sont obligatoires

  	| Propriété | Détails |
  	| -------- | ------- |
  	| Nom de connexion * | Entrez un nom pour votre connexion. |
  	| Intégration compte * | Entrez le nom du compte de l’intégration. Assurez-vous que votre compte d’intégration et d’une application de logique sont dans le même emplacement Azure |

    Une fois terminé, les détails de votre connexion ressemblent à ce qui suit

    ![connexion de compte intégration créée](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 


5. Sélectionnez **créer**

6. Notez que la connexion a été créée.

    ![Détails de connexion de compte intégration](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12-Encoder X12 Message par le nom de l’accord

7. Sélectionnez X12 accord à partir du message vers le bas et xml pour coder.

    ![fournir des champs obligatoires](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12-Encoder X12 Message par des identités

7.  Fournissent l’identificateur d’expéditeur, expéditeur qualificateur, identificateur de récepteur et récepteur qualificateur tel que configuré dans le X12 accord.  Sélectionnez le message xml pour coder

    ![fournir des champs obligatoires](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>X12 Encoder fait suivant :

* Résolution du contrat en faisant correspondre les propriétés de contexte de l’expéditeur et le destinataire.
* Sérialise l’échange EDI, convertir les messages codés sur XML jeux de transactions EDI dans l’échange.
* Applique des segments en-tête et le code du jeu de transactions
* Génère un numéro de contrôle d’échange, un numéro de contrôle de groupe et un numéro de contrôle du jeu de transactions pour chaque échange sortant
* Remplace les séparateurs dans les données de charge utile
* Valide l’EDI et des propriétés spécifiques au partenaire
    * Validation de schéma du jeu de transactions d’éléments de données contre le schéma du message
    * Validation de l’EDI effectuée sur des éléments de données de l’ensemble de la transaction.
    * Validation étendue effectuées sur les éléments de données de l’ensemble de la transaction
* Demande un accusé de réception technique et/ou fonctionnel (si configuré).
    * Il génère un accusé de réception technique par suite de la validation de l’en-tête. L’accusé de réception technique signale l’état du traitement d’un en-tête d’échange et de la remorque par le destinataire de l’adresse
    * Un accusé de réception fonctionnel génère en raison de la validation de l’organisme. L’accusé de réception fonctionnel signale chaque erreur s’est produite lors du traitement du document reçu

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise") 

