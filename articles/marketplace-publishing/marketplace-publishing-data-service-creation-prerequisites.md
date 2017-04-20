<properties
   pageTitle="Conditions préalables techniques pour la création d’un Service de données pour le marché | Microsoft Azure"
   description="Comprendre la configuration requise pour la création d’un Service de données pour déployer et vendre sur le marché d’Azure"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Création d’un Service de données techniques préalables offrent à Azure Marketplace

>[AZURE.IMPORTANT] **Pour l’instant nous ne sont plus arrivant des nouvelles publications de Service de données. Nouvelle dataservices ne sera pas obtenir approuvée pour la liste.** Si vous disposez d’une application d’entreprise SaaS vous souhaitez publier sur AppSource, vous pouvez trouver plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous possédez une applications IaaS ou service de développeur vous souhaitez publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Lire attentivement avant de commencer le processus d’et comprendre où et pourquoi chaque étape est effectuée. Autant que possible, vous devez préparer les informations sur votre société et autres données, téléchargez les outils nécessaires et/ou créer les composants techniques avant de commencer le processus de création d’offre.

Vous devez disposer des éléments suivants avant de commencer le processus :

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Prendre une décision sur quelle technologie sera utilisée pour publier votre offre de Service de données

Un éditeur peut choisir entre plusieurs technologies lors de la publication du Service de données dans Azure Marketplace. Les principales technologies qui sont pris en charge décrites ci-dessous. Quel que soit l’utilisateur final quelle technologie est utilisée pour publier le Service de données, utilise les données via le **flux d’OData** exposées par Azure Marketplace Service. Informations complètes sur le service OData que vous trouverez sur [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure une base de données

Jeu de données prêt dans SQL Azure est responsable de l’éditeur. Vous devez vous abonner à Azure, de fournir la taille appropriée de la base de données et de charger vos données dans la base de données SQL Azure. Publisher est également chargé de conserver ses données toujours à jour. Plus d’informations sur l’abonnement à des Services Azure que vous trouverez sur [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Lors du déplacement de données dans SQL Azure, le marché Azure peuvent exposer des tables et des vues. L’éditeur peut spécifier les tables/vues et les colonnes sont exposés à l’utilisateur final. Le fournisseur de contenu peut également spécifier les colonnes qui peuvent être interrogées par l’utilisateur final et ceux qui n’est renvoyées dans la charge utile. Ainsi, un niveau élevé de flexibilité sur les données dans la base de données doivent être exposées. Les colonnes qui peuvent être interrogés doivent être sauvegardées par un ou plusieurs index de base de données.

## <a name="rest-based-web-service"></a>Service web REST en fonction

Protocole pris en charge : **HTTPS uniquement**

Les services en fonction des autres existants peuvent être exposées via le Marketplace Azure. Étant donné que le groupe de données est toujours exposé à l’utilisateur final comme un flux OData, le service Azure Marketplace doit pouvoir mapper le service à un OData service basé sur. Pour ce faire le reste en fonction des points de terminaison doivent exposer tous les paramètres en tant que paramètres HTTP.

La charge utile doit être dans un format qui peut être mappé à une réponse ATOM. Donc la réponse de le services doit être au format XML et ne peut contenir un élément extensible qui contient les valeurs de charge utile (comme un jeu d’enregistrements). Le service Azure Marketplace mappera le nœud extensible pour le nœud d’entrée ATOM et les valeurs de charge utile dans les nœuds des propriétés dans le nœud d’entrée.

Les informations d’autorisation (par exemple, les API clés, l’authentification par jeton, etc.) doivent être fourni comme paramètre HTTP ou dans l’en-tête HTTP (paire clé / valeur) – authentification de base est également pris en charge. Une clé valide doit être fourni et toutes les requêtes via Azure Marketplace sont faites par l’intermédiaire de cette clé. Utilisateur de surveillance et de facturation se produit au niveau de la couche d’Azure Marketplace.

Les erreurs retournées par le service doivent être mappées dans les codes d’état HTTP. Dans le cas où le service retourne un fichier XML qui contient l’erreur ces vont mappé par le service Azure Marketplace aux codes d’état HTTP.

## <a name="soap-based-web-services"></a>Services web SOAP en fonction

Protocole : **HTTPS uniquement**

Les besoins sont les mêmes que dans le reste section service. La seule différence est que les paramètres peuvent également être fournis dans un corps XML validé au service de l’éditeur avec chaque demande faite via Azure Marketplace. Cela signifie que l’utilisateur fournit le composant frontal les paramètres HTTP sont traduits dans les éléments XML d’un document XML qui est en cours de comptabilisation avec la demande au service du web du fournisseur de contenu.

## <a name="odata-based-web-services"></a>Services web OData en fonction

Protocole : **HTTPS uniquement**

Données peuvent être exposées comme un service OData à Azure Marketplace. Le système va passer le service via et remplace la racine du service de la racine du service Azure Marketplace – pour garantir à tous les appels ultérieurs passent Azure Marketplace.

Les services OData ne suffit accéder à une base de données dans le système principal. OData prend en charge aucun type de logique métier ou de stockage pour le service.


## <a name="next-steps"></a>Étapes suivantes
Révisé les composants requis et de terminer les tâches nécessaires, vous pouvez le déplacer vers l’avant avec la création de votre offre de Service de données comme indiqué dans le [Guide de publication de Service de données](marketplace-publishing-data-service-creation.md).

Ou, si vous souhaitez passer en revue le processus global et les articles respectifs pour chacune des phases de publication, reportez-vous à l’article [mise en route : comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
