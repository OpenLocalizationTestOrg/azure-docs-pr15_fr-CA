<properties 
    pageTitle="Déploiement de votre première application web de Java vers Azure dans cinq minutes | Microsoft Azure" 
    description="Découvrez combien il est facile d’exécuter des applications web dans le Service d’application en déployant un exemple d’application. Commencent le développement réel rapidement et de voir immédiatement les résultats." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-java-web-app-to-azure-in-five-minutes"></a>Déploiement de votre première application web de Java vers Azure en cinq minutes

Ce didacticiel vous permet de déployer une application web de Java à l’adresse simple au [Service d’application Azure](../app-service/app-service-value-prop-what-is.md).
Vous pouvez utiliser le Service d’application pour créer des applications web [mobile application back-end](/documentation/learning-paths/appservice-mobileapps/)et [applications d’API](../app-service-api/app-service-api-apps-why-best-platform.md).

Vous allez : 

- Créer une application web dans le Service d’application Azure.
- Déployer un exemple d’application Java.
- Consultez votre code en cours d’exécution en production.

## <a name="prerequisites"></a>Conditions préalables

- Obtenir un client FTP/FTPS comme [FileZilla](https://filezilla-project.org/).
- Obtenir un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer vos avantages d’abonné de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Vous pouvez [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751) sans un compte Azure. Créer une application de démarrage et jouer avec lui pour jusqu'à une heure--aucune carte de crédit requise, aucun engagement.

<a name="create"></a>
## <a name="create-a-web-app"></a>Créer une application web

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

2. Dans le menu de gauche, cliquez sur **Nouveau** > **Web + Mobile** > **Web App**.

    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)

3. De la lame de création d’application, utilisez les paramètres suivants pour votre nouvelle application :

    - **Nom de l’application**: tapez un nom unique.
    - **Groupe de ressources**: sélectionnez **Créer nouveau** et nommez le groupe de ressources.
    - **Plan/emplacement du Service de l’application**: cliquez dessus pour configurer, puis cliquez sur **Créer un nouveau** pour définir le nom, un emplacement et un niveau de tarification du plan de Service de l’application. N’hésitez pas à utiliser le **libre** niveau de tarification.

    Lorsque vous avez terminé, la blade de création d’application doit se présenter comme suit :

    ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)

3. Cliquez sur **créer** en bas. Vous pouvez cliquer sur l’icône de **Notification** en haut pour voir la progression.

    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)

4. Lorsque le déploiement est terminé, vous devriez voir ce message de notification. Cliquez sur le message pour ouvrir les lames de votre déploiement.

    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)

5. La lame de **déploiement a réussi** , cliquez sur le lien de la **ressource** pour ouvrir les lames de votre nouvelle application web.

    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## <a name="deploy-a-java-app-to-your-web-app"></a>Déployer une application Java pour votre application web

Maintenant, nous allons déployer une application Java sur Azure à l’aide de FTPS.

5. De la lame d’application web, accédez à **paramètres de l’Application** ou le rechercher, puis cliquez sur. 

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

6. Dans la **version de Java**, sélectionnez **Java 8** , puis cliquez sur **Enregistrer**.

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

    Lorsque vous recevez la notification de **mise à jour réussie des paramètres de l’application web**, accédez à http://*&lt;appname >*. azurewebsites.net pour voir le servlet JSP par défaut en action.

7. Dans la blade d’application web, faites défiler **les informations d’identification de déploiement** ou le rechercher, puis cliquez sur.

8. La valeur de vos informations d’identification de déploiement, puis cliquez sur **Enregistrer**.

7. De retour dans la blade d’application web, cliquez sur **vue d’ensemble**. En regard de **nom d’utilisateur de déploiement/FTP** et **FTPS hostname**, cliquez sur le bouton **Copier** pour copier ces valeurs.

    ![](./media/app-service-web-get-started-languages/get-ftp-url.png)

    Vous êtes maintenant prêt à déployer votre application Java avec FTPS.

8. Dans votre client FTP/FTPS, connectez-vous au serveur FTP de votre application web Azure en utilisant les valeurs que vous avez copié dans la dernière étape. Utilisez le mot de passe de déploiement que vous avez créé précédemment.

    La capture d’écran suivante montre la connexion à l’aide de FileZilla.

    ![](./media/app-service-web-get-started-languages/filezilla-login.png)

    Vous pouvez voir les avertissements de sécurité pour le certificat SSL non reconnu dans Azure. Continuez et continuer.

9. Cliquez sur [ce lien](https://github.com/Azure-Samples/app-service-web-java-get-started/raw/master/webapps/ROOT.war) pour télécharger le fichier WAR sur votre ordinateur local.

9. Dans votre client FTP/FTPS, naviguez jusqu'à **/site/wwwroot/webapps** sur le site distant et faites glisser le fichier WAR téléchargé sur votre ordinateur local dans le répertoire distant.

    ![](./media/app-service-web-get-started-languages/transfer-war-file.png)

    Cliquez sur **OK** pour remplacer le fichier dans Azure.

    >[AZURE.NOTE] Selon un comportement de Tomcat par défaut, le nom de fichier **ROOT.war** dans /site/wwwroot/webapps vous donne l’application web racine (http://*&lt;appname >*. azurewebsites.net) et le nom de fichier ** * &lt;Nomquelconque >*.war** vous offre une application web nommée (http://*&lt;appname >*.azurewebsites.net/*&lt;Nomquelconque >*).

Voilà ! Votre application Java s’exécute dans Azure. Dans votre navigateur, accédez à http://*&lt;appname >*. azurewebsites.net pour le voir en action. 

## <a name="make-updates-to-your-app"></a>Mettre à jour votre application

Chaque fois que vous devez effectuer une mise à jour, vous devez simplement télécharger le nouveau fichier WAR dans le même répertoire distant avec votre client FTP/FTPS.

## <a name="next-steps"></a>Étapes suivantes

[Créer une application web de Java à partir d’un modèle sur le marché d’Azure](web-sites-java-get-started.md#marketplace). Vous pouvez obtenir votre propre conteneur Tomcat entièrement personnalisable et obtenir l’UI Manager familier. 

Déboguer votre application web d’Azure, directement dans [IntelliJ](app-service-web-debug-java-web-app-in-intellij.md) ou [Eclipse](app-service-web-debug-java-web-app-in-eclipse.md).

Ou bien, faire plus avec votre première application web. Par exemple :

- Essayez [d’autres méthodes de déploiement de votre code vers Azure](../app-service-web/web-sites-deploy.md). 
- Prendre votre application Azure au niveau suivant. Authentifier les utilisateurs. Évoluer en fonction de la demande. Permet de paramétrer des alertes de performances. En quelques clics. Reportez-vous à la section [Ajouter une fonctionnalité à votre première application web](app-service-web-get-started-2.md).

