<properties 
    pageTitle="En savoir plus sur Enterprise Integration Pack décoder AS2 Message Connctor | Service d’application Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-decode-as2-message"></a>Mise en route de décoder le Message AS2

Se connecter à décoder le Message AS2 pour établir la sécurité et la fiabilité lors de la transmission des messages. Elle fournit la signature numérique, de décryptage et d’accusés de réception par Message Disposition Notifications (MDN).

## <a name="create-the-connection"></a>Créer la connexion

### <a name="prerequisites"></a>Conditions préalables

* Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)

* Un compte d’intégration est requis pour utiliser de connecteur de message AS2 de décoder. Voir plus d’informations sur la création d’un [Compte d’intégration](./app-service-logic-enterprise-integration-create-integration-account.md), de [partenaires](./app-service-logic-enterprise-integration-partners.md) et d’un [accord de AS2](./app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Se connecter à décoder AS2 Message à l’aide des étapes suivantes :

1. [Créer une application de logique](./app-service-logic-create-a-logic-app.md) fournit un exemple.

2. Ce connecteur n’a pas de déclencheurs. Autres déclencheurs permet de démarrer l’application logique, tel qu’un déclencheur de la demande.  Dans le Concepteur de l’application de la logique, ajouter un déclencheur et ajouter une action.  Sélectionnez Afficher Microsoft API gérées dans la liste déroulante liste et entrez « AS2 » dans la zone de recherche.  Sélectionnez AS2 – décoder AS2 Message

    ![Recherche AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Si vous n’avez pas encore créé toutes les connexions au compte de l’intégration, vous êtes invité à entrer les détails de connexion

    ![Créer la connexion de l’intégration](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. Permet d’entrer les détails du compte de l’intégration.  Les propriétés d’un astérisque sont obligatoires

  	| Propriété   | Détails |
  	| --------   | ------- |
  	| Nom de connexion *    | Entrez un nom pour votre connexion. |
  	| Intégration compte * | Entrez le nom du compte de l’intégration. Assurez-vous que votre compte d’intégration et d’une application de logique sont dans le même emplacement Azure |

    Une fois terminé, les détails de votre connexion ressemblent à ce qui suit

    ![connexion de l’intégration](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. Sélectionnez **créer**
    
6. Notez que la connexion a été créée.  Maintenant, effectuez les autres étapes dans votre logique d’application

    ![connexion d’intégration créée](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 

7. Sélectionnez les en-têtes et les corps à partir des sorties de la demande

    ![fournir des champs obligatoires](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>Le décodage AS2 effectue les opérations suivantes

* Traite les en-têtes AS2/HTTP
* Vérifie la signature (si configuré)
* Décrypte les messages (si configuré)
* Décompresse le message (si configuré)
* Rapproche un MDN reçu avec le message sortant d’origine
* Met à jour et met en corrélation les enregistrements dans la base de données non-répudiabilité.
* Écrit les enregistrements pour le rapport d’état AS2
* Le contenu de charge utile de sortie est codées en base64
* Détermine si un MDN est requis et si le MDN doit être synchrone ou asynchrone selon configuration dans AS2 accord
* Génère un MDN synchrone ou asynchrone (basé sur les configurations d’accord)
* Définit les propriétés et les jetons de corrélation sur la MDN

##<a name="try-it-for-yourself"></a>Essayez-le par vous-même

Pourquoi ne pas essayer. Cliquez sur [ici](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) pour déployer une application logique opérationnelle de votre propre en utilisant les fonctionnalités de logique applications AS2 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise") 