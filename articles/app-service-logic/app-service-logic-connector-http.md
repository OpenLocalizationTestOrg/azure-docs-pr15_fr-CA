<properties
   pageTitle="À l’aide de l’écouteur HTTP et un connecteur dans les applications de logique | Service d’application Microsoft Azure "
   description="Comment créer et configurer l’écouteur HTTP et l’application de connecteur ou API HTTP action et l’utiliser dans une logique d’application dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Mise en route de l’action de HTTP et le port d’écoute HTTP et l’ajouter à votre application logique

> [AZURE.NOTE]Nous sommes fin de prise en charge pour ce connecteur car sa fonctionnalité est maintenant incluse par défaut comme le **déclenchement manuel** lorsque vous créez de nouvelles applications de logique.  Nous vous conseillons de changer toutes vos applications de logique qui sont à l’aide de ce connecteur.  
> Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

Se connecter directement aux ressources HTTP pour écouter les demandes HTTP et de configurer des demandes web HTTP. Il existe certains scénarios où vous devez travailler avec des connexions HTTP directes, y compris :

1.  Pour développer une application qui prend en charge un web utilisateur mobile interactive front-end ou logique.
2.  Afin d’obtenir et de traiter les données à partir d’un service web qui ne possède pas une absence du connecteur de la zone.
3.  Pour effectuer des actions qui sont déjà exposé comme un service web, et non comme une application API.

Pour ces scénarios, deux options sont disponibles :

1. **Port d’écoute HTTP**: ce connecteur sert de déclencheur et écoute les demandes HTTP sur un point de terminaison configuré. Lors de la réception d’un appel sur le point de terminaison configuré, il déclenche une nouvelle instance du flux et transmet les données reçues dans le flux pour le traitement de la demande. Il peut également être configuré pour répondre automatiquement à la demande entrante lorsque le flux a démarré ou vous permettent de construire une réponse basée sur l’exécution de flux et envoyer une réponse à l’appelant.
2. **Action de HTTP**: cela vous permet de configurer une demande web à n’importe quel point de terminaison disponible sur internet, obtient en retour une réponse et le rend disponible pour les actions supplémentaires dans le flux à consommer.

Applications de logique peuvent déclencher basée sur une variété de sources de données et offre des connecteurs pour récupérer et traiter les données sous la forme d’une partie du flux. Vous pouvez ajouter le connecteur HTTP à vos données de workflow et des processus métier dans le cadre de ce flux de travail au sein d’une application de logique. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Création d’un port d’écoute HTTP pour votre application logique
Un connecteur peut être créé dans une application de logique ou être créé directement à partir de l’Azure Marketplace. Pour créer un lien à partir du marché :  

1. Dans la startboard d’Azure, sélectionnez **Marketplace**.
2. Rechercher « HTTP », sélectionnez le port d’écoute HTTP et sélectionnez **créer**.
3.  Configurez l’écouteur HTTP comme suit :  
![][1]

4.  Lorsque vous configurez les paramètres de package, vous verrez l’option suivante si l’écouteur doit répondre automatiquement ou exigent que vous envoyer une réponse explicite. Définir à **False** pour envoyer votre réponse :  
![][2]

5.  Cliquez sur **OK** pour le créer.
6.  Une fois que l’instance d’application API est créé, ouvrez les paramètres pour configurer la sécurité. L’écouteur HTTP prend actuellement en charge l’authentification de base. Vous pouvez le configurer à l’aide de l’option de sécurité lorsque vous ouvrez le port d’écoute HTTP :  
![][3]
  
    **Problème connu** Les paramètres de la sécurité de *affichent « None » comme valeur par défaut, mais il n’est pas défini. Vous devez modifier le paramètre pour Basic et à None avant de l’enregistrer pour vous assurer que le port d’écoute HTTP est correctement configuré.*  

7. Enfin, définissez les paramètres de sécurité de l’application de l’API public (anonyme) pour autoriser les clients externes à accéder au point de fin. Ce paramètre est disponible sous « tous les paramètres > Paramètres de l’Application » de l’application API du port d’écoute HTTP :![][10]

Une fois cette opération effectuée, vous pouvez désormais créer une application de logique pour utiliser l’écouteur HTTP.

## <a name="using-the-http-listener-in-your-logic-app"></a>Dans votre logique d’application à l’aide de l’écouteur HTTP
Une fois votre application API est créée, vous pouvez désormais utiliser l’écouteur HTTP en tant que déclencheur pour votre application logique. Pour ce faire, vous devez :

4.  Créez une nouvelle application logique.
5.  Ouvrez « Déclencheurs et Actions » pour ouvrir le Concepteur d’applications logique et configurer vos flux. Le port d’écoute HTTP est répertoriée dans la galerie. Sélectionnez-le.
6.  Vous pouvez maintenant définir la méthode HTTP et l’URL relative sur lequel vous avez besoin de l’écouteur déclencher le flux :  
![][4]  
![][5]

7.  Pour obtenir l’URI complet, double-cliquez sur le port d’écoute HTTP pour afficher ses paramètres de configuration et copiez l’URL de le « hôte » de votre application d’API :  
![][6]
8.  Vous pouvez maintenant utiliser les données reçues dans la requête HTTP dans les autres actions dans le flux comme suit :  
![][7]  
![][8]
9.  Enfin, pour envoyer une réponse, ajoutez un autre écouteur HTTP et sélectionnez l’action Envoyer de réponse HTTP. La valeur de l’ID de demande RequestID obtenu à partir de l’écouteur HTTP et remplir le corps de la réponse et l’état HTTP que vous voulez revenir en arrière :  
![][9]

## <a name="using-the-http-action"></a>À l’aide de l’action de HTTP
L’action HTTP est prise en charge par les applications de logique et ne nécessite pas une application API pour être créé pour pouvoir l’utiliser. Vous pouvez insérer une action HTTP à n’importe quel point dans votre logique d’application et choisissez les URI, les en-têtes et les corps de l’appel.
L’action HTTP prend en charge plusieurs options de sécurité côté client. Consultez les [options de sécurité côté client](../scheduler/scheduler-outbound-authentication.md).

La sortie de l’action HTTP est les en-têtes et corps, ce qui peut être utilisé plus en aval dans le flux, similaire à la sortie des autres actions et des connecteurs est consommée.

## <a name="do-more-with-your-connector"></a>Faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un workflow d’entreprise à l’aide d’une application de logique. Consultez [Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Permet d’afficher la référence de l’API de REST Swagger à des [connecteurs et des applications de référence sur l’API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les performances statistiques et contrôle de sécurité sur le connecteur. Reportez-vous à la section [gérer et surveiller les connecteurs et les applications d’API intégrées](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Si vous souhaitez démarrer avec les applications Azure logique avant l’ouverture d’un compte Azure, accédez à [Essayer une application de logique](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application de logique de démarrage de courte durée dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
