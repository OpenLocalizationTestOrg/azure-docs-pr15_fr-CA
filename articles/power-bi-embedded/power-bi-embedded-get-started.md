<properties
   pageTitle="Mise en route de Microsoft d’alimentation BI incorporé"
   description="Puissance incorporées de BI, ajouter des rapports interactifs de puissance BI dans votre application décisionnelle"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Mise en route de Microsoft d’alimentation BI incorporé

**Alimentation BI incorporé** est un service Azure permet aux développeurs d’applications d’ajouter des rapports interactifs de puissance BI dans leurs propres applications. **Incorporées de BI d’alimentation** fonctionne avec les applications existantes sans nécessiter la redéfinition ou la modification des manière dont les utilisateurs se connecter.

Ressources pour **Microsoft d’alimentation BI incorporé** sont mis en service par le biais de l' [API de ARM Azure](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource que vous configurez est une **Collection de puissance BI espace de travail**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Créer une collection de l’espace de travail
Une **Collection de l’espace de travail** est la ressource Azure de niveau supérieur et un conteneur pour le contenu qui est intégré dans votre application. Une **Collection de l’espace de travail** peut être créé de deux façons :

   -    Manuellement à l’aide du portail Azure
   -    Programmation à l’aide de l’API de Manager(ARM) de ressources Azure

Nous allons étudier les étapes pour créer une **Collection de l’espace de travail** via le portail d’Azure.

   1.   Ouvrez et connectez-vous à **Azure Portal**: [http://portal.azure.com](http://portal.azure.com).

   2.   Cliquez sur **+ de nouveau** sur le panneau supérieur.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Sous **données + Analytique** cliquez sur **Alimentation BI incorporé**.
   4.   Sur la **Lame de création**, entrez les informations requises. Pour la **tarification**, consultez [tarification incorporées de BI d’alimentation](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Cliquez sur **créer**.

La **Collection de l’espace de travail** prendra un certain temps à disposition. Une fois terminé, vous serez dirigé à la **Lame de Collection d’espace de travail**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

La **Lame de création** contient les informations nécessaires appeler les API pour créer des espaces de travail et déploiement le contenu vers les.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Touches d’affichage accès à l’API BI d’alimentation

Un des éléments d’informations nécessaires pour appeler les API de reste de BI d’alimentation plus importants sont les **Touches d’accès rapide**. Ceux-ci sont utilisés pour générer les **jetons d’application** qui sont utilisés pour authentifier les requêtes de votre API. Pour afficher les **Touches d’accès rapide**, cliquez sur les **Touches d’accès rapide** sur la **Lame de paramètres**. Pour plus d’informations sur les **jetons de l’application**, reportez-vous à la section [authentification et autorisation avec alimentation BI incorporé](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Vous allez ' Notez que vous disposez de deux clés.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copiez ces clés et de les stocker en toute sécurité dans votre application. Il est très important de traiter ces clés comme vous le feriez avec un mot de passe, car ils vous permettent d’accéder à tout le contenu de votre **Collection de l’espace de travail**.

Alors que les deux clés sont répertoriés, qu’une seule clé est nécessaire à un moment donné. La deuxième clé est fournie afin que vous pouvez régénérer périodiquement les clés sans interrompre l’accès au service.

Maintenant que vous disposez d’une instance de puissance BI pour votre application et les **Touches d’accès rapide**, vous pouvez importer un rapport dans votre application. Avant de voir comment importer un état, la section suivante décrit la création de groupes de données BI de puissance et de rapports à incorporer dans une application.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Créer des groupes de données BI de puissance et de rapports à incorporer dans une application

Maintenant que vous avez créé une instance de puissance BI pour votre application et que vous avez des **Touches d’accès rapide**, vous devez créer les groupes de données BI d’alimentation et les rapports que vous souhaitez incorporer. Des groupes de données et les rapports peuvent être créés à l’aide de **Puissance BI bureau**. Vous pouvez télécharger le [Bureau BI de puissance pour libérer](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Ou bien, pour commencer rapidement, vous pouvez télécharger le [Retail analyse exemple PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Pour en savoir plus sur l’utilisation **D’énergie BI bureau**, reportez-vous à la section [Mise en route avec le bureau de BI d’alimentation](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Avec le **Bureau de BI d’alimentation**, vous connectez à votre source de données en important une copie des données dans le **Bureau de BI d’alimentation** ou en se connectant directement à la source de données à l’aide de **DirectQuery**.

Voici les différences entre l’utilisation **d’importation** et de **DirectQuery**.

|Importation | DirectQuery
|---|---
|Tables, colonnes *et les données* sont importées ou copiées dans un **Bureau de BI d’alimentation**. Lorsque vous travaillez avec des visualisations, **Alimentation BI bureau** interroge une copie des données. Pour visualiser les modifications apportées aux données sous-jacentes, vous devez actualiser ou importer, un dataset terminé, en cours de nouveau.|Uniquement les *tables et les colonnes* sont importées ou copiées dans un **Bureau de BI d’alimentation**. Lorsque vous travaillez avec des visualisations, **Alimentation BI bureau** interroge la source de données sous-jacente, ce qui signifie que vous visualisez toujours les données actuelles.

Pour plus d’informations sur la connexion à une source de données, voir [se connecter à une source de données](power-bi-embedded-connect-datasource.md).

Une fois que vous enregistrez votre travail dans le **Bureau d’une alimentation**, un fichier PBIX est créé. Ce fichier contient votre rapport. En outre, si vous importez des données de la PBIX contient le groupe de données complet, ou si vous utilisez **DirectQuery**, le PBIX contient uniquement le schéma d’un dataset. Par programme, vous déployez le PBIX dans votre espace de travail à l’aide de l' [API d’importation BI d’alimentation](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Alimentation BI incorporé** a API supplémentaires pour modifier le serveur et la base de données qui pointe vers votre groupe de données et de définir une information d’identification du compte de service que le groupe de données utilisera pour se connecter à votre base de données. Voir [SetAllConnections de publication](https://msdn.microsoft.com/library/mt711505.aspx) et de la [source de données de correctif passerelle](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Étapes suivantes
Dans les étapes précédentes, vous avez créé une collection de l’espace de travail, votre premier rapport et dataset. Il est maintenant temps d’apprendre à écrire du code pour **Alimentation BI incorporé**. Pour vous aider à démarrer, nous avons créé un exemple d’application web : [mise en route de l’échantillon](power-bi-embedded-get-started-sample.md). L’exemple vous montre comment à :

  - Contenu de la disposition
      - Créer un espace de travail
      - Importation d’un fichier PBIX
      - Mettre à jour les chaînes de connexion et définir les informations d’identification pour vos groupes de données.

  - En toute sécurité incorporer un rapport

## <a name="see-also"></a>Voir aussi
- [Mise en route de l’échantillon](power-bi-embedded-get-started-sample.md)
- [Authentification et autorisation avec alimentation BI incorporé](power-bi-embedded-app-token-flow.md)
- [Bureau de BI de puissance](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
