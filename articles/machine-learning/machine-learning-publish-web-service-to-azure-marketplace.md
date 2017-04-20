<properties 
    pageTitle="Publier l’apprentissage automatique service pour Azure Marketplace web | Microsoft Azure" 
    description="Comment publier votre Service Web de Azure Machine Learning vers Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Publier le Service Web de formation Azure Machine vers Azure Marketplace 

Le marché Azure offre la possibilité de publier des services web de formation de Machine Azure comme étant payées ou libérer des services destinés à la consommation par les clients externes. Cet article fournit une vue d’ensemble de ce processus avec des liens vers des instructions pour vous aider à démarrer. À l’aide de ce processus, vous pouvez rendre vos services web disponibles aux autres développeurs de consommer dans leurs applications.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Vue d’ensemble du processus de publication 

La procédure de publication d’un service web de formation de Machine Azure vers Azure Marketplace sont les suivantes :

1. Créer et publier un service de formation de Machine demande-réponse (RR)
2. Déployer le service à la production et d’obtenir les informations de point de terminaison OData et de clé de l’API.
3. Utilisez l’URL du service web publié à publier sur [Azure Marketplace (marché de données)](https://publish.windowsazure.com/workspace/) 
4. Une fois envoyé, votre offre est révisé et doit être approuvé avant vos clients pouvez démarrer acheter. Le processus de publication peut prendre quelques jours. 

## <a name="walk-through"></a>Passez en revue
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Étape 1 : Créer et publier un service de formation de Machine demande-réponse (RR)###
 Si vous n'avez pas encore fait, prenez un coup de œil à ce [Guide](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Étape 2 : Déployer le service à la production et d’obtenir la clé d’API et les informations de point de terminaison OData###
1. À partir du [Portail classique d’Azure](http://manage.windowsazure.com), sélectionnez l’option **d’Apprentissage automatique** à partir de la barre de navigation de gauche et sélectionnez votre espace de travail. 

2. Cliquez sur l’onglet **SERVICES WEB** et sélectionnez le service web que vous souhaitez publier sur le marketplace.

    ![Azure Marketplace][workspace]

3. Sélectionnez le point de terminaison que vous souhaitez avoir sur le marché à consommer. Si vous n’avez pas créé les points de terminaison supplémentaires, vous pouvez sélectionner le point de terminaison **par défaut** .

4. Une fois que vous avez cliqué sur le point de terminaison, vous ne pourrez pas voir la **Clé d’API**. Vous aurez besoin de cette information d’informations suite à l’étape 3, donc faire une copie de celui-ci.

    ![Azure Marketplace][apikey]

5. Cliquez sur la méthode de **Demande/réponse** , à ce stade, que nous ne gèrent pas la publication de services d’exécution par lots sur le marché. Ce lien vous amènera à la page d’aide de API pour la méthode de demande/réponse.

6. Copier l' **Adresse de point de terminaison OData**, vous aurez besoin de ces informations ultérieurement à l’étape 3.

    ![Azure Marketplace][odata]




déployez le service en production.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Étape 3 : Utiliser l’URL du service web publié à publier sur Azure Marketplace (DataMarket)###

1.  Naviguez vers [Azure Marketplace (marché de données)](http://datamarket.azure.com/home) 
2.  Cliquez sur le lien de la **publication** en haut de la page. Ceci vous dirigera vers le [Portail de publication Microsoft Azure](https://publish.windowsazure.com)
3.  Cliquez sur la section **éditeurs** pour enregistrer en tant qu’éditeur.
4.  Lors de la création d’une nouvelle offre, sélectionnez les **Services de données**, puis cliquez sur **créer un nouveau Service de données**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  Sous **programmes** fournissent des informations sur votre offre, y compris un plan de tarification. Décidez si vous propose un service gratuit ou payant. Pour obtenir le paiement, fournir des informations de paiement comme vos informations bancaires et de taxe.

6.  Sous **Marketing** fournissent des informations sur votre offre, comme le titre et la description de votre offre.

7.  Sous **tarification** , vous pouvez définir un prix pour vos plans de certains pays, ou laissez le système « autoprice » dans votre offre.

8. Sous l’onglet **Service de données** , cliquez sur **Service Web** comme **Source de données**.

    ![Azure Marketplace][image2]

9.  Obtenir la clé API et les URL du service web à partir du portail classique d’Azure, comme expliqué à l’étape 2 ci-dessus.

10. Dans la boîte de dialogue Service de données Marketplace, collez l’adresse du point de terminaison OData dans la zone de texte **URL de Service** .

11. Pour **l’authentification**, choisissez **en-tête** en tant que le **Schéma d’authentification**.

    - Entrez « Autorisation » pour le **nom de l’en-tête**.
    - Pour la **Valeur de l’en-tête**, entrez « Support » (sans les guillemets) et cliquez sur la barre **d’espace** puis collez la clé d’API.
    - Activez la case à cocher **ce Service est OData** .
    - Cliquez sur **Tester la connexion** pour tester la connexion.

12. Sous **catégories**, vérifiez le **Qu'apprentissage automatique** est sélectionnée.

13. Lorsque vous avez terminé la saisie de toutes les métadonnées concernant votre offre, cliquez sur **Publier**, puis les **pousser à mis en œuvre**. À ce stade, vous serez averti de tous les problèmes restants que vous souhaitez corriger.

14. Après avoir vérifié l’achèvement de tous les problèmes en suspens, cliquez sur **demander une approbation pour pousser vers la Production**. Le processus de publication peut prendre quelques jours. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
