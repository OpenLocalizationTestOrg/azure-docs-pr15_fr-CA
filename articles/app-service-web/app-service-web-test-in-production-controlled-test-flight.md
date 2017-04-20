<properties
    pageTitle="Déploiement flighting (test bêta) dans le Service d’application Azure"
    description="Découvrez comment les nouvelles fonctionnalités de votre application de vol ou de vos mises à jour dans ce didacticiel de bout en bout pour le test bêta. Il réunit les fonctionnalités du Service de l’application telles que la publication continue, connecteurs, le routage du trafic et l’intégration de perspectives de l’Application."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cephalin"/>
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Déploiement flighting (test bêta) dans le Service d’application Azure

Ce didacticiel vous apprend à effectuer des *déploiements flighting* en intégrant les différentes fonctionnalités de [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714) et les [Perspectives d’Application Azure](/services/application-insights/). 

*Flighting* est un processus de déploiement qui valide une nouvelle fonctionnalité ou la modification d’un nombre limité de clients réels, et teste majeur dans un scénario de production. Il est similaire à la version bêta de test et est parfois appelée « vol de test contrôlé ». De nombreuses entreprises de grande taille avec une présence web utilisent cette approche pour obtenir une validation rapide sur leurs mises à jour de l’application dans leurs pratiques de [développement agile](https://en.wikipedia.org/wiki/Agile_software_development). Azure Application Service vous permet d’intégrer les tests en production continue la publication et les perspectives d’Application pour implémenter le scénario DevOps même. Avantages de cette approche :

- **Commentaires réels _avant_ sont mises à jour pour la production de gain** - le mieux plus feedback dès que vous relâchez gagne commentaires avant de libérer. Vous pouvez tester les mises à jour avec le trafic des utilisateurs réels et les comportements dès que vous le souhaitez dans le cycle de vie du produit.
- **Amélioration [continu développement piloté par test (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development) ** - grâce à l’intégration des tests en production avec intégration continue et l’instrumentation avec aperçus de l’Application, validation de l’utilisateur se produit rapidement et automatiquement dans votre cycle de vie du produit. Cela permet de réduire les investissements de temps dans l’exécution du test manuel.
- **Optimisation du flux de travail de test** - grâce à l’automatisation des tests en production avec les instruments de surveillance en continu, vous pouvez potentiellement les objectifs de différents types de tests dans un processus unique, telles que [l’intégration](https://en.wikipedia.org/wiki/Integration_testing), [régression](https://en.wikipedia.org/wiki/Regression_testing), [facilité d’utilisation](https://en.wikipedia.org/wiki/Usability_testing), d’accessibilité, localisation, [performance](https://en.wikipedia.org/wiki/Software_performance_testing), [sécurité](https://en.wikipedia.org/wiki/Security_testing)et [acceptation](https://en.wikipedia.org/wiki/Acceptance_testing).

Un déploiement flighting est quasiment pas de routage du trafic. Dans un tel déploiement que vous souhaitez mieux aussi rapidement que possible, si elle est un bogue inattendu, une dégradation des performances, problèmes d’expérience utilisateur. N’oubliez pas, vous êtes confronté à des clients. Par conséquent, faire droit, vous devez vous assurer que vous avez configuré votre déploiement flighting pour collecter toutes les données dont vous avez besoin pour prendre une décision éclairée pour l’étape suivante. Ce didacticiel vous montre comment collecter des données avec les informations d’Application, mais vous pouvez utiliser Relic nouvelle ou autres technologies qui convient à votre scénario. 

## <a name="what-you-will-do"></a>Ce que vous ferez

Dans ce didacticiel, vous allez apprendre à mettre les scénarios suivants pour tester votre application de Service de l’application en production :

- [Routage du trafic de production](app-service-web-test-in-production-get-start.md) à votre application de version bêta
- [Instrument de votre application](../application-insights/app-insights-web-track-usage.md) pour obtenir des mesures utiles
- Déployer votre application bêta et suivre les mesures d’application live en continu
- Comparer des mesures entre l’application de production et de l’application de la version bêta pour voir comment les modifications de code traduisent des résultats

## <a name="what-you-will-need"></a>Ce que vous devez

-   Un compte Azure
-   Un compte [GitHub](https://github.com/)
- Visual Studio 2015 - vous pouvez télécharger l' [Édition Communauté](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).
-   GIT Shell (installé avec [GitHub pour Windows](https://windows.github.com/)) - Cela vous permet d’exécuter des commandes à la fois le Git et PowerShell dans la même session
-   Derniers bits de [PowerShell d’Azure](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)
-   Connaissances de base des éléments suivants :
    -   Déploiement de modèle de [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) (voir [déploiement d’une application complexe prévisible dans Azure](app-service-deploy-complex-application-predictably.md))
    -   [GIT](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Vous avez besoin d’un compte Azure pour compléter ce didacticiel :
> + Vous pouvez [Ouvrir un compte Azure gratuitement](/pricing/free-trial/) , vous obtenez des crédits que vous pouvez utiliser pour tester des services Azure payés et même après leur utilisation vous pouvez conserver le compte et utilisation libre des services Azure, tels que les applications Web.
> + Vous pouvez [activer des avantages pour les abonnés de Visual Studio](/pricing/member-offers/msdn-benefits-details/) , de Visual Studio votre abonnement fournit les crédits que vous pouvez utiliser pour des services Azure payés chaque mois.
>
> Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="set-up-your-production-web-app"></a>Configurer votre application web de production

>[AZURE.NOTE] Le script utilisé dans ce didacticiel pour configurer automatiquement la publication continue à partir de votre référentiel de GitHub. Cette opération nécessite que vos informations d’identification de GitHub sont déjà stockées dans Azure, dans le cas contraire, les scripts de déploiement échoue lorsque vous essayez de configurer les paramètres de contrôle de code source pour les applications web.
>
>Pour stocker vos informations d’identification GitHub dans Azure, créez une application web dans le [Portail Azure](https://portal.azure.com/) et [configurer le déploiement de GitHub](app-service-continuous-deployment.md#Step7). Vous devez uniquement effectuer cette opération qu’une seule fois.

Dans un scénario classique de DevOps, vous avez une application qui s’exécute dans Azure et vous souhaitez apporter des modifications à la publication continue. Dans ce scénario, vous allez déployer en production un modèle que vous avez développé et testé.

1.  Créer votre propre branche du référentiel [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Pour plus d’informations sur la création de votre branche, consultez [branche un mis en pension](https://help.github.com/articles/fork-a-repo/). Une fois votre branche créée, vous pouvez le voir dans votre navigateur.

    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Ouvrez une session Git Shell. Si vous n’avez pas encore Git Shell, installer [GitHub pour Windows](https://windows.github.com/) maintenant.
3.  Créer un clone local de votre branche en exécutant la commande suivante :

        git clone https://github.com/<your_fork>/ToDoApp.git

4.  Une fois que vous avez votre clone local, accédez à * &lt;repository_root >*\ARMTemplates et exécuter la deploy.ps1 de script avec un suffixe unique, comme indiqué ci-dessous :

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.  Lorsque vous y êtes invité, tapez dans le nom d’utilisateur souhaité et le mot de passe pour l’accès de la base de données. N’oubliez pas vos informations d’identification de base de données car vous devrez les spécifier lors de la mise à jour le groupe de ressources.

    Vous devriez voir la progression de mise en service de diverses ressources Azure. Lorsque le déploiement est terminé, le script lance l’application dans le navigateur et vous donner un bip convivial.
    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.  Retourner dans votre session Git Shell, exécutez :

        .\swap –Name ToDoApp<your_suffix>

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.  Lorsque la fin du script, revenez en arrière pour naviguer jusqu'à l’adresse de la partie frontale (http://ToDoApp*&lt;your_suffix >*.azurewebsites.net/) pour voir l’application en cours d’exécution dans la production.
5.  Connectez-vous au [Portail Azure](https://portal.azure.com/) et observez ce qui est créé.

    Vous devez être en mesure de voir les deux applications web dans le même groupe de ressources, un avec le `Api` le suffixe dans le nom. Si vous examinez l’affichage des ressources de groupe, vous verrez également la base de données SQL server le plan de Service de l’application et que, les emplacements de zone de transit pour les applications web. Naviguer parmi les différentes ressources et de les comparer avec * &lt;repository_root >*\ARMTemplates\ProdAndStage.json pour voir comment ils sont configurés dans le modèle.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Vous avez configuré l’application de production.  À présent, imaginons que vous recevez les commentaires que facilité d’utilisation est faible pour l’application. Vous décidez donc de rechercher. Vous allez pour instrumenter votre application afin de vous donner des commentaires.

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>Rechercher : Instrumenter votre application cliente pour les mesures de surveillance /

5. Ouvrir * &lt;repository_root >*\src\MultiChannelToDo.sln dans Visual Studio.
6. Restauration de tous les packages Nuget en cliquant droit sur la solution > **Manage NuGet Packages de Solution** > **restauration**.
6. Droit **MultiChannelToDo.Web** > **Ajouter de télémétrie de perspectives Application** > **Configurer les paramètres** > modifier le groupe de ressource pour ToDoApp*&lt;your_suffix >* > **Perspectives d’Application ajouter au projet**.
7. Dans le portail d’Azure, ouvrez la lame pour la ressource d’Application Insight **MultiChannelToDo.Web** . Dans la partie de **l’état de l’Application** , puis **Apprenez à collecter les données de charge de page de navigateur** > Copier le code.
7. Ajoutez le code d’instrumentation JS copié à * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, juste avant la fermeture `<heading>` balises. Il doit contenir la clé unique d’instrumentation de vos ressources d’Application Insight.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Envoyer des événements personnalisés aux analyses de l’Application pour la souris clics en ajoutant le code suivant à la fin du corps :

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Cet extrait de code JavaScript envoie un événement personnalisé aux analyses d’Application chaque fois qu’un utilisateur clique n’importe où dans l’application web.

12. Dans le Git Shell, valider et envoyer vos modifications à votre branche dans GitHub. Puis, attendez que les clients d’actualiser le navigateur.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.  Permuter les modifications de l’application déployée en production :

        .\swap –Name ToDoApp<your_suffix>

13. Accédez à la ressource de perspectives de l’Application que vous avez configurés. Cliquez sur les événements personnalisés.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Si vous ne voyez pas les métriques pour les événements personnalisés, attendez quelques minutes et cliquez sur **Actualiser**.

Supposons que vous consultez un graphique comme ci-dessous :

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

Et la grille d’événement ci-dessous :

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

En fonction de votre code d’application ToDoApp, l’événement du **bouton** correspond au bouton submit, et l’événement **d’entrée** correspond à la zone de texte. Jusqu’ici, éléments de sens. Cependant, il semble y avoir une grande quantité de clics et très peu de clics sur les éléments d’action (les événements **LI** ).

En fonction de cela, le formulaire vous votre hypothèse que certains utilisateurs sont confondus quelle partie de l’interface utilisateur est interactif, et c’est parce que le curseur est un style pour la sélection de texte lorsqu’il se trouve sur les éléments de liste et leurs icônes.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Il peut s’agir d’un exemple artificiel. Néanmoins, vous allez apporter une amélioration de votre application et ensuite effectuer un déploiement flighting pour obtenir des commentaires sur la facilité d’utilisation à partir de clients live.

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>Instrumenter votre application serveur de surveillance/mesures
Il s’agit d’une tangente étant donné que le scénario présenté dans ce didacticiel ne traite qu’avec l’application cliente. Toutefois, par souci d’exhaustivité, vous allez définir l’application côté serveur.

6. Droit **MultiChannelToDo** > **Ajouter de télémétrie de perspectives Application** > **Configurer les paramètres** > modifier le groupe de ressource pour ToDoApp*&lt;your_suffix >* > **Perspectives d’Application ajouter au projet**.
12. Dans le Git Shell, valider et envoyer vos modifications à votre branche dans GitHub. Puis, attendez que les clients d’actualiser le navigateur.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.  Permuter les modifications de l’application déployée en production :

        .\swap –Name ToDoApp<your_suffix>

Voilà !

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>Examiner : Ajouter des balises d’emplacement spécifiques pour vos mesures d’application client

Dans cette section, vous allez configurer les emplacements de déploiement différentes pour envoyer de télémétrie d’emplacement spécifiques à la même ressource de perspectives de l’Application. De cette façon, vous pouvez comparer les données de télémétrie entre le trafic à partir de différents emplacements (environnements de déploiement) pour facilement visualiser l’effet de vos modifications de l’application. Dans le même temps, vous pouvez séparer le trafic de production du reste pour vous permettre de surveiller votre application de production si nécessaire.

Dans la mesure où vous êtes collecte de données sur le comportement du client, vous allez [Ajouter un initialiseur de télémétrie pour votre code JavaScript](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) dans index.cshtml. Si vous souhaitez tester les performances côté serveur, par exemple, vous pouvez également effectuer de la même façon dans votre code serveur (voir les [Conseils d’Application API pour les mesures et les événements personnalisés](../application-insights/app-insights-api-custom-events-metrics.md).

1. Tout d’abord, ajoutez l’entre du code les deux `//` bloquent les commentaires ci-dessous dans le code JavaScript que vous avez ajoutés à la `<heading>` plus haut de la balise.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Ce code d’initialiseur provoque la `appInsights` objet à ajouter l’une propriété personnalisée appelée `Environment` à chaque élément de télémétrie qu’il envoie.

2. Ensuite, ajoutez cette propriété personnalisée en tant que [paramètre d’emplacement](web-sites-staged-publishing.md#AboutConfiguration) pour votre application web dans Azure. Pour ce faire, exécutez les commandes suivantes dans votre session Git Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    Le fichier Web.config dans votre projet définit déjà le `environment` paramètre d’application. Avec ce paramètre, lorsque vous testez l’application localement, vos mesures sera identifié par `VS Debugger`. Toutefois, lorsque vous appuyez sur vos modifications vers Azure, Azure va trouver et utiliser le `environment` application paramètre dans configuration de l’application web et vos mesures sera identifié par `Production`.

3. Valider et envoyer vos modifications du code pour votre branche sur GitHub puis attendre à vos utilisateurs d’utiliser la nouvelle application (nécessaire pour actualiser le navigateur). Il faut environ 15 minutes pour la nouvelle propriété à afficher dans vos idées d’Application `MultiChannelToDo.Web` ressource.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. Maintenant, accédez à nouveau à la blade **d’événements personnalisés** et filtrer les métriques sur `Environment=Production`. Vous devez désormais être en mesure de voir tous les événements nouveaux personnalisées dans l’emplacement de production avec ce filtre.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Cliquez sur le bouton **Favoris** pour enregistrer les paramètres actuels de mesures Explorer à quelque chose comme **les événements personnalisés : Production**. Vous pouvez facilement basculer entre cet affichage et une vue emplacement de déploiement plus loin.

    > [AZURE.TIP] Pour encore plus puissant analytique, intégrez [votre ressource d’idées d’Application avec BI d’alimentation](../application-insights/app-insights-export-power-bi.md).

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>Ajouter des balises d’emplacement spécifiques pour vos mesures d’application de serveur
Là encore, par souci d’exhaustivité, vous allez définir l’application côté serveur. Contrairement à l’application cliente qui est instrumentée dans JavaScript, les balises spécifiques à l’emplacement de l’application serveur est instrumenté avec le code .NET.

1. Ouvrir * &lt;repository_root >*\src\MultiChannelToDo\Global.asax.cs. Ajoutez le bloc de code ci-dessous, juste avant la fermeture accolade de l’espace de noms.

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }

2. Corrigez les erreurs de résolution de nom en ajoutant le `using` instructions ci-dessous au début du fichier :

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;

3. Ajoutez le code ci-dessous au début de la `Application_Start()` méthode :

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Valider et envoyer vos modifications du code pour votre branche sur GitHub puis attendre à vos utilisateurs d’utiliser la nouvelle application (nécessaire pour actualiser le navigateur). Il faut environ 15 minutes pour la nouvelle propriété à afficher dans vos idées d’Application `MultiChannelToDo` ressource.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>Mise à jour : Configurer votre branche de la version bêta

2. Ouvrir * &lt;repository_root >*\ARMTemplates\ProdAndStagetest.json et rechercher le `appsettings` ressources (recherchez `"name": "appsettings"`). Il existe 4 d'entre eux, une pour chaque emplacement. 

2. Pour chaque `appsettings` ressource, ajouter une `"environment": "[parameters('slotName')]"` paramètre d’application à la fin de la `properties` tableau. N’oubliez pas de mettre fin à la ligne précédente avec une virgule.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Vous venez d’ajouter le `environment` paramètre d’application à tous les emplacements dans le modèle.
    
2. Dans le même fichier, recherchez le `slotconfignames` ressources (recherchez `"name": "slotconfignames"`). Il existe 2 d'entre eux, un pour chaque application.

2. Pour chaque `slotconfignames` ressource, ajoutez `"environment"` à la fin de la `appSettingNames` tableau. N’oubliez pas de mettre fin à la ligne précédente avec une virgule.

    Vous venez de créer la `environment` app définition stick à son emplacement de déploiement respectif pour les deux applications.  

3. Dans votre session Git Shell, exécutez les commandes suivantes avec le même suffixe de groupe de ressources que vous avez utilisé avant.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Lorsque vous y êtes invité, spécifiez le même SQL de base de données d’informations d’identification comme avant. Ensuite, lorsque vous êtes invité à mettre à jour le groupe de ressources, tapez `Y`, puis `ENTER`.

    Une fois le script terminé, toutes vos ressources dans le groupe de ressources d’origine sont conservées, mais un nouvel emplacement nommé « bêta » est créé avec la même configuration que l’emplacement de « Stockage temporaire » a été créé au début qu’il contient.

    >[AZURE.NOTE] Cette méthode de création de différents environnements de déploiement est différente de la méthode de [développement de logiciel Agile avec Service d’application Azure](app-service-agile-software-development.md). Ici, vous créez des environnements de déploiement avec les emplacements de déploiement, où que vous créez des environnements de déploiement avec les groupes de ressources. Gestion des environnements de déploiement avec les groupes de ressources vous permet de conserver l’environnement de production hors des limites pour les développeurs, mais il n’est pas facile de tests en production, et vous pouvez effectuer facilement avec connecteurs.

Si vous le souhaitez, vous pouvez également créer une application alpha en exécutant

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Pour ce didacticiel, vous allez continuer à utiliser votre application bêta.

## <a name="update-push-your-updates-to-the-beta-app"></a>Mise à jour : Distribuer les mises à jour pour l’application de la version bêta

Dans votre application que vous souhaitez améliorer.

1. Vérifiez que vous êtes maintenant dans la branche de votre version bêta

        git checkout beta

2. Dans * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, trouver la `<li>` de balise et d’ajouter le `style="cursor:pointer"` d’attribut, comme illustré ci-dessous.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. validation et envoi vers Azure.

4. Vérifiez que la modification est désormais répercute dans l’emplacement de la version bêta en accédant à http://todoapp*&lt;your_suffix >*-beta.azurewebsites.net/. Si vous ne voyez pas la modification encore, actualisez votre navigateur pour obtenir le nouveau code javascript.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Maintenant que vous avez votre modification en cours d’exécution dans l’emplacement de la version bêta, vous êtes prêt à effectuer un déploiement flighting.

## <a name="validate-route-traffic-to-the-beta-app"></a>Valider : Acheminer le trafic vers l’application de la version bêta

Dans cette section, vous achemine le trafic vers l’application de la version bêta. Par souci de clarté de la démonstration, vous allez acheminer une partie importante du trafic utilisateur vers ce. En réalité, la quantité de trafic que vous souhaitez router dépend de votre situation particulière. Par exemple, si votre site est à l’échelle de microsoft.com, vous devrez peut-être inférieure à 1 % du trafic total afin d’obtenir des données utiles.

1. Dans votre session Git Shell, exécutez les commandes suivantes pour acheminer la moitié du trafic de production vers l’emplacement de la version bêta :

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  Le `ReroutePercentage=50` propriété spécifie que 50 % du trafic de production seront acheminées vers l’URL de l’application de la version bêta (spécifié par la `ActionHostName` propriété).

2. Accédez maintenant à http://ToDoApp*&lt;your_suffix >*. azurewebsites.net. 50 % du trafic doit maintenant être redirigé vers l’emplacement de la version bêta.

3. Dans votre ressource Application Insights, filtrer les mesures en environnement = « bêta ».

    > [AZURE.NOTE] Si vous enregistrez cette vue filtrée comme un autre favori, vous pouvez inverser facilement les vues explorer métrique entre la production et les vues de la version bêta.

Supposons que dans les perspectives d’Application voir quelque chose de similaire à ce qui suit :

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Non seulement cela affiche qu’il n’y a plus de nombreux clics sur la `<li>` balises, mais il semble une surtension de clics sur `<li>` balises. Vous pouvez ensuite en conclure que les personnes ont découvert le nouveau `<li>` balises sont interactifs et effacez maintenant toutes leurs tâches précédemment effectuées dans l’application.

Basé sur les données de votre déploiement de flighting, vous décidez que votre nouvelle interface utilisateur est prêt pour la production.

## <a name="go-live-move-your-new-code-into-production"></a>Mise en ligne : déplacer votre nouveau code en production

Vous êtes maintenant prêt à déplacer votre mise à jour à la production. Ce qui est génial, c’est que maintenant, vous savez que votre mise à jour a déjà été validé _avant_ , elle est poussée vers la production. Maintenant vous pouvez en toute confiance la déployer. Dans la mesure où vous avez une mise à jour de l’application client de AngularJS, vous validé uniquement le code côté client. Si vous deviez apporter des modifications à l’application Web API de back-end, vous pourriez valider vos modifications de la même façon et facilement.

1. Dans le Git Shell, supprimez la règle de routage du trafic en exécutant la commande suivante :

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Exécutez les commandes Git :

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Attendez quelques minutes pour le nouveau code à déployer sur l’emplacement intermédiaire, puis lancer http://ToDoApp*&lt;your_suffix >*-staging.azurewebsites.net pour vérifier que la nouvelle mise à jour est échauffé dans l’emplacement intermédiaire. N’oubliez pas que la branche de maître de votre branche est lié à l’emplacement intermédiaire de votre application.

3. Maintenant, remplacez l’emplacement intermédiaire dans la production

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>Résumé ##

Service d’application Azure facilite pour les entreprises de petites à moyennes tester leurs applications côté client dans la production, de quelque chose qui a été fait traditionnellement dans les grandes entreprises. Nous espérons que ce didacticiel vous a donné les connaissances nécessaires rassembler de Service de l’application et perspectives d’Application flighting possibles et même d’autres scénarios de test de production, dans votre monde DevOps. 

## <a name="more-resources"></a>Plus de ressources ##

-   [Développement Agile avec le Service d’application Azure](app-service-agile-software-development.md)
-   [Configurer la mise en attente des environnements pour les applications web dans le Service d’application Azure](web-sites-staged-publishing.md)
-   [Déployer une application complexe prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
-   [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md)
-   [JSONLint - le validateur JSON](http://jsonlint.com/)
-   [Branchement de GIT – base de branchement et de fusion](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [PowerShell Azure](../powershell-install-configure.md)
-   [Wiki de Kudu de projet](https://github.com/projectkudu/kudu/wiki)
