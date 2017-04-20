<properties
    pageTitle="Mise en route de test de production pour les applications Web"
    description="Obtenir des informations sur le Test de fonctionnalité de Production (TiP) dans Azure Application Service Web Apps."
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
    ms.date="01/13/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-test-in-production-for-web-apps"></a>Mise en route de test de production pour les applications Web

Les tests en production ou live-test de votre application web en utilisant le trafic client en direct, est une stratégie de test que les développeurs d’application s’intègrent de plus en plus dans leur méthodologie de [développement agile](https://en.wikipedia.org/wiki/Agile_software_development) . Il vous permet de tester la qualité de vos applications avec le trafic utilisateur live dans votre environnement de production, et non des données synthétisées dans un environnement de test. En exposant votre nouvelle appli avec des utilisateurs réels, être informé sur les problèmes réels qui que pèsent sur votre application après son déploiement. Vous pouvez vérifier les fonctionnalités, performances et valeur de vos mises à jour de l’application sur le volume, la vitesse et la diversité de trafic utilisateur réel, ce qui vous pouvez rapprocher jamais dans un environnement de test.

## <a name="traffic-routing-in-app-service-web-apps"></a>Le trafic de routage dans les applications de Service d’application Web

Avec la fonctionnalité de routage du trafic, dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez diriger une partie du trafic d’utilisateur direct pour un ou plusieurs [emplacements de déploiement](web-sites-staged-publishing.md)et analyser votre application avec les [Perspectives d’Application Azure](/services/application-insights/) [Azure HDInsight](/services/hdinsight/)ou un outil tiers comme [Nouveau Relic](/marketplace/partners/newrelic/newrelic/) à valider vos modifications. Par exemple, vous pouvez implémenter les scénarios suivants avec le Service de l’application :

- Détecter les bogues fonctionnels ou d’identifier les goulots d’étranglement de performances dans vos mises à jour avant le déploiement de l’ensemble du site
- Effectuer des « vols de test contrôlé » de vos modifications en mesurant les mesures usibility sur l’application de la version bêta
- Rampe progressivement à une nouvelle mise à jour et normalement en bas de la version actuelle si une erreur se produit. 
- Optimiser les résultats de votre application en exécutant [A / B tests](https://en.wikipedia.org/wiki/A/B_testing) ou [tests MULTIVARIABLES](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) dans plusieurs emplacements de déploiement

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Configuration requise pour utiliser le routage du trafic dans les applications Web

- Votre application web doit s’exécuter dans la couche **Standard** ou **Premium** , comme il est requis pour plusieurs emplacements de déploiement.
- Pour fonctionner correctement, le routage du trafic requiert des cookies soit activée dans le navigateur des utilisateurs. Routage de trafic utilise des cookies pour épingler un navigateur client, à un emplacement de déploiement pour la durée de vie de la session du client.
- Routage de trafic prend en charge les scénarii TiP avancées via les applets de commande PowerShell d’Azure.

## <a name="route-traffic-segment-to-a-deployment-slot"></a>Segment de trafic d’itinéraire vers un emplacement de déploiement

Au niveau de base dans chaque scénario d’info-bulle, vous acheminer un pourcentage prédéfini de votre trafic direct vers un emplacement de déploiement de production non. Pour ce faire, suivez les étapes ci-dessous :

>[AZURE.NOTE] Cette procédure suppose que vous avez déjà un [emplacement de déploiement de production non](web-sites-staged-publishing.md) et que le contenu d’application web de votre choix est déjà [déployée](web-sites-deploy.md) .

1. Journal du [portail Azure](https://portal.azure.com/).
2. Dans les lames de votre application web, cliquez sur **paramètres** > **Le routage du trafic**.
  ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Sélectionnez l’emplacement que vous souhaitez acheminer le trafic vers et le pourcentage du trafic total que vous le souhaitez, puis cliquez sur **Enregistrer**.

    ![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Passez à l’aube de l’emplacement déploiement. Vous devez maintenant voir du trafic acheminé vers elle.

    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Une fois le routage du trafic est configuré, le pourcentage spécifié de clients est acheminé au hasard à votre emplacement hors production. Toutefois, il est important de noter qu’une fois qu’un client est automatiquement acheminé vers un emplacement spécifique, il sera pour « afficher » à cet emplacement pour toute la durée de cette session du client. Cela fait à l’aide d’un cookie pour épingler la session de l’utilisateur. Si vous Inspectez les demandes HTTP, vous trouverez une `TipMix` cookie à chaque demande ultérieure.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>Forcer les demandes des clients vers un emplacement spécifique

Outre le routage du trafic automatique, Service de l’application est en mesure de router les demandes vers un emplacement spécifique. Cela est utile lorsque vous souhaitez que vos utilisateurs doit être en mesure de choisir en ou annulations de votre application de version bêta. Pour ce faire, vous utilisez la `x-ms-routing-name` paramètre de requête.

Pour rediriger les utilisateurs vers un emplacement spécifique à l’aide de `x-ms-routing-name`, vous devez vous assurer que le slot est déjà ajouté à la liste de routage du trafic. Étant donné que vous souhaitez acheminer explicitement à un emplacement, le véritable pourcentage de routage que vous définissez n’a aucune importance. Si vous le souhaitez, vous pouvez concevoir un « lien bêta » que les utilisateurs peuvent cliquer pour accéder à l’application de la version bêta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>Choisir les utilisateurs de l’application de la version bêta

Pour permettre aux utilisateurs de choisir de ne pas votre application bêta, par exemple, vous pouvez placer ce lien dans votre page web :

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

La chaîne `x-ms-routing-name=self` Spécifie l’emplacement de production. Une fois que le navigateur client accéder au lien, non seulement il est redirigé vers l’emplacement de production, mais toutes les requêtes suivantes contiennent les `x-ms-routing-name=self` cookie qui épingle de la session à l’emplacement de production.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>Choisir les utilisateurs à l’application de la version bêta

Pour permettre aux utilisateurs de participer à votre application de la version bêta, définir le même paramètre de requête pour le nom de l’emplacement hors production, par exemple :

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>Plus de ressources ##

-   [Configurer la mise en attente des environnements pour les applications web dans le Service d’application Azure](web-sites-staged-publishing.md)
-   [Déployer une application complexe prévisible dans Azure](app-service-deploy-complex-application-predictably.md)
-   [Développement Agile avec le Service d’application Azure](app-service-agile-software-development.md)
-   [Utiliser efficacement les environnements de DevOps pour vos applications web](app-service-web-staged-publishing-realworld-scenarios.md)