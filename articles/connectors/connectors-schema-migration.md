<properties
    pageTitle="Comment migrer des applications de logique vers le schéma version 2015-08-01-aperçu | Service d’application Microsoft Azure"
    description="Vous pouvez facilement migrer vos applications de logique à la dernière version de schéma. Il suffit de suivre ces étapes."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Comment migrer des applications de logique vers le schéma version 2015-08-01-aperçu

Pour déplacer vos applications logique existant vers le nouveau schéma, effectuez les opérations suivantes :  
1. Ouvrez votre application de logique dans le portail Azure  
2. Cliquez sur le schéma de la mise à jour :

 ![Icône de l’API][step1]   
La page mise à jour du schéma affiche et fournit un lien vers un document qui fournissent des détails sur les améliorations apportées dans le nouveau schéma : ![API icône][step2]

>[AZURE.NOTE] Lorsque vous sélectionnez la **Mise à jour de schéma**, nous automatiquement exécuter les étapes de migration et fournir la code de sortie pour vous. Vous pouvez l’utiliser pour mettre à jour de votre définition, cependant, assurez-vous que vous suivez des bonnes pratiques de programmation, telles que celles décrites dans la section **méthodes recommandées** ci-dessous.

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Meilleures pratiques lors de la migration de vos applications de logique à la dernière version de schéma :  

- Copiez le script migré vers une nouvelle application logique - ne pas remplacer l’ancienne un jusqu'à ce que vous avez terminé votre test et confirmé l’existence de l’application migrée fonctionne comme prévu.
- Tester votre logique app **avant** mise en production
- Une fois la migration terminée, démarrez la mise à jour de vos applications de logique pour utiliser les [API managées](./apis-list.md) lorsque cela est possible. Par exemple, vous pouvez démarrer à l’aide de la boîte de dépôt v2, où vous utilisez la boîte de dépôt v1.


## <a name="whats-next"></a>Quel est l’avenir
-  [Apprenez à migrer manuellement vos applications logique](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






