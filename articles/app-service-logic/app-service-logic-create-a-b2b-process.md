<properties 
   pageTitle="Création d’un processus B2B dans le Service d’application Azure | Microsoft Azure" 
   description="Vue d’ensemble de la création d’un processus d’entreprise-entreprise" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Création d’un processus B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Scénario d’entreprise 
Contoso et les Comptoirs sont deux partenaires commerciaux. Contoso (le détaillant) envoie des commandes d’achat à Northwind (le fournisseur) sur une industrie au niveau de transport tels que AS2. Northwind stocke toutes les commandes entrantes dans son emplacement de stockage Cloud. Les ordres d’achat sont des messages XML entre ces deux partenaires. Une fois que le message est stocké dans le stockage en nuage de Northwind les processus internes de Northwind traiter la commande à partir de ce point sur.
 
L’objectif de ce didacticiel est d’établir comment Northwind peut établir un processus métier par lequel il peut recevoir des messages (bons de commande dans le fichier XML) à partir de son partenaire de Contoso sur AS2 et puis de le conserver dans son emplacement de stockage Cloud.


## <a name="capabilities-demonstrated"></a>Capacités montrées 
Ce didacticiel vous permet de présenter les capacités suivantes : 

- **Transport du message**: le détaillant et le fournisseur peuvent être sur des plates-formes différentes, mais ils peuvent toujours atteindre la communication entre les deux. Dans ce didacticiel qu’ils communiquent via AS2 (Applicability Statement 2). AS2 est un moyen populaire pour transporter des données entre les partenaires commerciaux dans des communications de l’entreprise-entreprise.
- **Persistance des données**: une fois que le message a été reçu sur AS2 puis Northwind souhaite conserver avant le traitement. Il peut utiliser un connecteur pour conserver les messages dans son emplacement de stockage Cloud. Dans ce didacticiel, les BLOB Azure est étant exploité comme le stockage en nuage pour Northwind.
- **Création d’un processus d’entreprise**: dans un flux, plusieurs applications d’API peuvent être assemblées pour obtenir des résultats commerciaux comme illustré ici.


## <a name="before-you-begin"></a>Avant de commencer
Ce didacticiel suppose que vous avez une connaissance de base des Services d’application Azure, savoir comment créer des applications d’API et combiner un flux.


## <a name="steps-to-achieve-the-business-scenario"></a>Étapes à suivre pour réaliser le scénario d’entreprise
**Créer et configurer les applications API requises**

1. Créez une instance du **Connecteur de Blob de stockage Azure**. Cette opération nécessite les informations d’identification pour un compte de stockage Azure. Assurez-vous qu’il est prêt avant de commencer cette création.
2. Créez une instance de la **Gestion des partenaires commerciaux BizTalk**. Cette opération nécessite une base de données SQL vide pour la fonction. Assurez-vous qu’il est prêt avant de commencer cette création.
3. Créez une instance du **Connecteur AS2**. Cela nécessite également une base de données SQL vide pour la fonction. Assurez-vous qu’il est prêt avant de commencer cette création. En outre, si vous souhaitez archiver les messages dans le cadre de AS2 de traitement, vous pouvez fournir d’informations d’identification pour un Blob Azure lors de sa création.
4. Configurer le service de module de plateforme sécurisée (gestion des partenaires commerciaux) qui est créé :  
    1. Accédez à l’instance du service de module de plateforme sécurisée créé dans le cadre de la procédure ci-dessus.
    2. Utilisez l’option de **partenaires** sous *composants* pour **Ajouter** un nouveau partenaire appelé **Contoso** et de son profil ajouter l’identité AS2 requise.
    3. Utilisez l’option de **partenaires** sous *composants* pour **Ajouter** un nouveau partenaire nommé **Northwind** et dans son profil ajouter l’identité AS2 requise.
    4. Utilisez l’option **accords** sous *composants* pour **Ajouter** un nouveau AS2 convenues, Northwind et Contoso. Northwind est le partenaire hébergé ici et Contoso est le partenaire de l’invité. Le cas échéant configurer la signature, le cryptage, la compression et les accusés de réception lors de la création de cet accord. Dans le cas où les certificats doivent être utilisés, ils peuvent être téléchargés via l’option de **certificats** lorsque vous naviguez sur le service de module de plateforme sécurisée est créé.


## <a name="create-a-flow--business-process"></a>Créer un flux / de processus métier
1. Créer un nouveau flux dont la première étape est AS2. Faites glisser et déposez le **Connecteur AS2** et choisissez l’instance déjà créée. Comme la fonctionnalité, sélectionnez le déclencheur :  
    ![][1]  
2. Ensuite glisser et déplacer le **Connecteur de Blob de stockage Azure** et choisissez l’instance déjà créée. Choisissez l’action que la fonctionnalité et dans laquelle, sélectionnez **Charger le Blob** en tant que la fonctionnalité souhaitée. Configurez comme il convient.
3. Maintenant créer/déployer le flux.


## <a name="message-processing--troubleshooting"></a>Traitement des messages et la résolution des problèmes
1. Il est temps de tester le flux que nous avons déployé. Envoyer que les messages XML encapsulé dans AS2 (selon l’accord AS2 créé ci-dessus) au point de terminaison AS2 surfacée par l’instance de AS2Connector que vous avez créé. Vous devrez peut-être configurer l’authentification pour le point de terminaison afin qu’il soit accessible au public.
2. Informations sur le flux d’exécution sont surfacées par le flux de navigation et puis pas à pas détaillé dans l’instance de flux qui a été exécuté
3. De traitement d’informations AS2, naviguez jusqu'à l’instance AS2Connector impliqué et suivez par pas à pas détaillé dans la partie du suivi. Vous pouvez utiliser des filtres de limiter l’affichage aux informations que vous souhaitez.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
