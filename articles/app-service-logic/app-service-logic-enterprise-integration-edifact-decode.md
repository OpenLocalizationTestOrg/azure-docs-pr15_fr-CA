<properties 
    pageTitle="En savoir plus sur Enterprise Integration Pack décoder connecteur de Message EDIFACT | Service d’application Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-decode-edifact-message"></a>Mise en route de décoder un Message EDIFACT

Valide EDI et des propriétés spécifiques au partenaire, génère le document XML pour chaque ensemble de transaction ainsi d’accusé de réception pour la transaction traitée.

## <a name="create-the-connection"></a>Créer la connexion

### <a name="prerequisites"></a>Conditions préalables

* Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)

* Un compte d’intégration est nécessaire pour utiliser le connecteur de message EDIFACT de décoder. Voir plus d’informations sur la création d’un [Compte d’intégration](./app-service-logic-enterprise-integration-create-integration-account.md), de [partenaires](./app-service-logic-enterprise-integration-partners.md) et de [contrat de EDIFACT](./app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Se connecter à décoder de Message EDIFACT en suivant ces étapes :

1. [Créer une application de logique](./app-service-logic-create-a-logic-app.md) fournit un exemple.

2. Ce connecteur n’a pas de déclencheurs. Autres déclencheurs permet de démarrer l’application logique, tel qu’un déclencheur de la demande.  Dans le Concepteur de l’application de la logique, ajouter un déclencheur et ajouter une action.  Sélectionnez Afficher Microsoft API gérées dans la liste déroulante liste et puis entrez « EDIFACT » dans la zone de recherche.  Sélectionnez décoder le Message EDIFACT

    ![recherche EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
    
3. Si vous n’avez pas encore créé toutes les connexions au compte de l’intégration, vous êtes invité à entrer les détails de connexion

    ![créer le compte de l’intégration](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)  

4. Permet d’entrer les détails du compte de l’intégration.  Les propriétés d’un astérisque sont obligatoires

  	| Propriété | Détails |
  	| -------- | ------- |
  	| Nom de connexion * | Entrez un nom pour votre connexion. |
  	| Intégration compte * | Entrez le nom du compte de l’intégration. Assurez-vous que votre compte d’intégration et d’une application de logique sont dans le même emplacement Azure |

    Une fois terminé, les détails de votre connexion ressemblent à ce qui suit

    ![compte d’intégration créé](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)  

5. Sélectionnez **créer**

6. Notez que la connexion a été créée.

    ![Détails de connexion de compte intégration](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

7. Sélectionnez le message de fichier plat EDIFACT pour décoder

    ![fournir des champs obligatoires](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>Décoder des EDIFACT est suivant

* Résoudre l’accord en faisant correspondre le qualificateur de l’expéditeur identificateur et de qualificateur de récepteur & identificateur
* Fractionne des échanges multiples dans un seul message en distinct.
* Valide l’enveloppe par rapport à la négociation du contrat de partenariat
* Désassemble l’échange.
* Valide l’EDI et les propriétés spécifiques aux partenaires inclut
    * Validation de la structure de l’enveloppe d’échange.
    * Validation de schéma de l’enveloppe par rapport au schéma du contrôle.
    * Validation de schéma des éléments de données de l’ensemble de la transaction par rapport au schéma de message.
    * Validation de l’EDI effectuée sur des éléments de données de l’ensemble de la transaction
* Vérifie que les numéros de contrôle de jeu échange, de groupe et de transaction ne sont pas doublons (si configuré) 
    * Vérifie le numéro de contrôle d’échange contre des échanges reçus précédemment. 
    * Vérifie le numéro de contrôle de groupe par rapport à d’autres numéros de contrôle de groupe dans l’échange. 
    * Vérifie la transaction numéro de contrôle par rapport à d’autres numéros de contrôle de jeu de transaction dans ce groupe.
* Génère un document XML pour chaque ensemble de transactions.
* Convertit l’entier échange XML 
    * Échange de fractionnement en tant que jeux de transactions - suspendre des ensembles de transaction sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct. Si un ou plusieurs ensembles de transaction dans l’échange échouent de la validation, puis EDIFACT décoder ne suspend que les jeux de la transaction. 
    * Échange de fractionnement en tant que jeux de transactions - suspendre l’échange sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct.  Si un ou plusieurs ensembles de transaction dans l’échange échouent de la validation, puis EDIFACT décoder suspend l’échange complet.
    * Conserver l’échange - suspendre des ensembles de transaction sur l’erreur : crée un document XML pour l’échange de lots entier. EDIFACT décoder interrompt uniquement ces jeux de transactions que l’échouent de la validation, tout en continuant à traiter tous les autres ensembles de transaction
    * Conserver l’échange - échange de suspension sur l’erreur : crée un document XML pour l’échange de lots entier. Si un ou plusieurs ensembles de transaction dans l’échange échouent de la validation, puis EDIFACT décoder suspend l’échange complet, 
* Génère un accusé de réception fonctionnel et/ou une technique (contrôle) (si configuré).
    * Un accusé de réception technique ou la CONTRL ACK signale les résultats d’un contrôle syntaxique de l’échange de réception complète.
    * Un accusé de réception fonctionnel reconnaît les accepter ou de refuser un échange reçu ou un groupe

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise") 