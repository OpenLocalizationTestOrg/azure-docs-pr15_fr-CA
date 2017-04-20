<properties
   pageTitle="Créer EAI App de logique à l’aide de VETR dans les applications de logique dans le Service d’application Azure | Microsoft Azure"
   description="Valider, coder et transformer les fonctionnalités des services BizTalk XML"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Créer EAI App de logique à l’aide de VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

La plupart des scénarios d’intégration d’applications d’entreprise (EAI) organisée servant d’intermédiaire données entre une source et une destination. Ces scénarios ont souvent un ensemble commun d’exigences :

- Assurez-vous que les données de différents systèmes sont correctement formatées.
- Effectuer des « recherche » sur les données entrantes pour prendre des décisions.
- Convertir les données d’un format à un autre. Par exemple, convertir des données de format de données d’un système CRM au format de données d’un système ERP.
- Données d’itinéraire à l’application souhaitée ou système.

Cet article décrit un modèle d’intégration commun : « intermédiation message unidirectionnel » ou VETR (Validate, Enrich, transformation, itinéraire). Le modèle VETR sert d’intermédiaire pour les données entre une entité source et une destination. La source et la destination sont généralement des sources de données.

Pensez à un site Web qui accepte des commandes. Les utilisateurs publier des commandes dans le système à l’aide de HTTP. Dans les coulisses, le système valide les données entrantes d’exactitude, il normalise et l’enregistre dans une file d’attente de Bus de Service pour un traitement ultérieur. Le système prend les commandes de la file d’attente, il est attendu dans un format particulier. Par conséquent, le flux de bout en bout est :

**HTTP** → **Valider** → **transformer** → **Bus des services**

![Flux de base VETR][1]

Les applications d’API BizTalk suivants permettent de générer ce modèle :

* **Déclencheur de HTTP** : Source de l’événement de déclenchement du message
* **Valider** - vérifie l’exactitude des données entrantes
* **Transformation** - transformations des données d’entrée au format de format requise par le système en aval
* **Connecteur de Bus de Service** - entité de Destination où les données sont envoyées.


## <a name="constructing-the-basic-vetr-pattern"></a>Construire le modèle de base VETR
### <a name="the-basics"></a>Les notions de base

Dans le portail Azure, sélectionnez **+ Nouveau** **Web + Mobile**et sélectionnez ensuite **Logique d’application**. Choisissez un nom, emplacement, abonnement, groupe de ressources et un emplacement qui vous convient. Groupes de ressources agissent comme conteneurs pour vos applications ; toutes les ressources pour votre application atteindre le même groupe de ressources.

Ensuite, nous allons ajouter les déclencheurs et les actions.


## <a name="add-http-trigger"></a>Ajouter un déclencheur de HTTP
1. Dans les **Modèles d’application logique**, sélectionnez **créer de toutes pièces**.
1. Sélectionnez le **Port d’écoute HTTP** à partir de la galerie pour créer un nouveau port d’écoute. Appelez-le **HTTP1**.
2. Définir la **Envoyer automatiquement de réponse ?** à false. Configurer l’action du déclencheur en définissant la _Méthode HTTP_ _POST_ et de paramètre _URL Relative_ à _/OneWayPipeline_:  
    ![Déclencheur de HTTP][2]
3. Sélectionnez la coche verte pour exécuter le déclencheur.

## <a name="add-validate-action"></a>Ajouter Action de validation

Maintenant, nous allons entrer des actions qui s’exécutent lorsque le déclencheur est déclenché, c'est-à-dire chaque fois qu’un appel est reçu sur le point de terminaison HTTP.

1. Ajouter un **Valideur XML de BizTalk** à partir de la galerie et le nom _(Validate1)_ pour créer une instance.
2. Configurer un schéma XSD pour valider les messages XML entrants. Sélectionnez l’action _Valider_ et _triggers('httplistener').outputs. Contenu_ comme valeur pour le paramètre _inputXml_ .

Désormais, l’action valider est la première action après l’écouteur HTTP : 

![Valideur XML de BizTalk][3]

De même, ajoutez le reste des actions. 

## <a name="add-transform-action"></a>Ajouter une action de transformation
Nous allons configurer des transformations pour normaliser les données entrantes.

1. Ajouter un **Service de transformation de BizTalk** à partir de la galerie.
2. Pour configurer un fichier de transformation pour transformer les messages XML entrants, sélectionnez l’action de **transformer** l’action à exécuter lorsque cette API est appelée. Sélectionnez ```triggers(‘httplistener’).outputs.Content``` comme valeur de _inputXml_. *Map* est un paramètre optionnel dans la mesure où les données entrantes sont mis en correspondance avec toutes les transformations configurées, et celles qui correspondent au schéma sont appliquées.
3. Enfin, la transformation s’exécute uniquement si la validation réussit. Pour configurer cette condition, sélectionnez l’icône ENGRENAGE sur le coin supérieur droit et sélectionnez _Ajouter une condition à remplir_. Définissez la condition à ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![Transformations de BizTalk][4]


## <a name="add-service-bus-connector"></a>Ajouter le connecteur de Bus de Service
Ensuite, nous allons ajouter la destination — une file d’attente de Bus de Service — pour écrire les données.

1. Ajouter un **Connecteur de Bus de Service** à partir de la galerie. Le **nom de** la valeur _Servicebus1_, définissez la **Chaîne de connexion** à la chaîne de connexion à votre instance de bus de service, définir le **Nom de l’entité** à la _file d’attente_et passez le **nom de l’abonnement**.
2. Sélectionnez l’action **Envoyer un Message** et la valeur du champ de **contenu** pour l’action _actions('transformservice').outputs. OutputXml_.
3. La valeur du champ **Type de contenu** *application/xml*:  

![Bus des services][5]


## <a name="send-http-response"></a>Envoyer la réponse HTTP
Une fois le traitement du pipeline est terminé, renvoyer une réponse HTTP de réussite et d’échec avec les étapes suivantes :

1. Ajouter un **Port d’écoute HTTP** à partir de la galerie et sélectionnez l’action **Envoyer de réponse HTTP** .
2. Définir l' **ID de réponse** pour envoyer le *Message*.
2. Définir le **Contenu de réponse** au *traitement du Pipeline est terminé*.
3. **Code d’état de réponse** *200* indiquant HTTP 200 OK.
4. Sélectionnez la liste déroulante dans le coin supérieur droit et sélectionnez **Ajouter une condition à remplir**.  Définissez la condition à l’expression suivante :  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Répétez ces étapes pour envoyer une réponse HTTP ainsi que lors de l’échec. Modifier la **Condition** à l’expression suivante :  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Cliquez sur **OK** puis **créer**.



## <a name="completion"></a>Saisie semi-automatique
Chaque fois que quelqu'un envoie un message au point de terminaison HTTP, il déclenche l’application et exécute les actions que vous venez de créer. Pour gérer toutes ces applications logique vous créez, sélectionnez **Parcourir** dans le portail Azure et sélectionnez **Les applications logique**. Sélectionnez votre application de plus amples informations.

Certaines rubriques utiles :

[Gérer et surveiller vos applications d’API et des connecteurs](app-service-logic-monitor-your-connectors.md)  <br/>
[Surveillance de vos applications de logique](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG
