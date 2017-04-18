<properties
    pageTitle="Protéger votre API avec une API Azure Management | Microsoft Azure"
    description="Découvrez comment protéger votre API avec des quotas et des limitations (limitation de vitesse) les stratégies."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Protéger votre API avec limites de taux à l’aide de la gestion des API Azure

Ce guide vous montre combien il est facile pour ajouter une protection pour la principale API en configurant des stratégies de limite de quota et le taux avec gestion des API Azure.

Dans ce didacticiel, vous allez créer un produit « Version d’évaluation gratuite « API qui permet aux développeurs d’effectuer jusqu'à 10 appels par minute et un maximum de 200 appels par semaine à votre API en utilisant le [taux d’appel limite par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et les stratégies de [quota de l’utilisation de jeu par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) . Puis vous l’API de publication et tester la stratégie de limite de vitesse.

Pour des scénarios plus avancés d’accélération en utilisant les stratégies de [taux limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [par clé de quota](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , consultez [limitation avec Azure API de gestion de demande avancée](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Pour créer un produit

Dans cette étape, vous allez créer un produit de la version d’évaluation gratuite qui ne requiert pas l’approbation de l’abonnement.

>[AZURE.NOTE] Si vous avez déjà disposez d’un produit configuré et que vous souhaitez utiliser pour ce didacticiel, vous pouvez passer directement à [configurer appeler taux limite et de quota][] et suivre le didacticiel à partir de là, l’utilisation du produit à la place du produit de la version d’évaluation gratuite.

Pour commencer, cliquez sur **Gérer** dans Azure classique pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel de [Gérer votre première API dans Azure API de gestion][] .

Dans le menu de **Gestion de l’API** à gauche pour afficher la page **produits** , cliquez sur **produits** .

![Ajouter un produit][api-management-add-product]

Cliquez sur **Ajouter produit** pour afficher la boîte de dialogue **Ajouter un nouveau produit** .

![Ajouter un nouveau produit][api-management-new-product-window]

Dans la zone **titre** , tapez **Version d’évaluation gratuite**.

Dans la zone **Description** , tapez le texte suivant :  **les abonnés pourront exécuter 10 appels minutes jusqu'à un maximum de 200 appels par semaine après lequel l’accès est refusé.**

Les produits de gestion de l’API peuvent être protégés ou ouvrir. Produits protégés doivent être souscrite avant de pouvoir être utilisés. Produits ouverts peuvent être utilisés sans abonnement. Vérifiez que **l’abonnement** est sélectionné pour créer un produit protégé qui requiert un abonnement. Il s’agit du paramètre par défaut.

Si vous souhaitez un administrateur pour réviser et accepter ou refuser des tentatives de l’abonnement à ce produit, sélectionnez **Exiger l’approbation de l’abonnement**. Si la case à cocher n’est pas activée, les tentatives d’abonnement seront acceptés automatiquement. Dans cet exemple, les abonnements sont automatiquement approuvées, n’activez ne pas la case.

Pour permettre à des comptes de développeur s’abonner plusieurs fois pour le nouveau produit, sélectionnez la case à cocher **Autoriser plusieurs abonnements simultanées** . Ce didacticiel ne pas utiliser plusieurs abonnements simultanées, ainsi, laissez-la désactivée.

Une fois toutes les valeurs sont entrées, cliquez sur **Enregistrer** pour créer le produit.

![Produits ajoutés][api-management-product-added]

Par défaut, les nouveaux produits sont visibles par les utilisateurs du groupe **administrateurs** . Nous allons ajouter le groupe de **développeurs** . Cliquez sur la **Version d’évaluation gratuite**, puis cliquez sur l’onglet **visibilité** .

>Dans Gestion de l’API, les groupes sont utilisés pour gérer la visibilité des produits pour les développeurs. Produits accordent la visibilité aux groupes, et les développeurs peuvent afficher et s’abonner aux produits qui sont visibles pour les groupes dont ils font partie. Pour plus d’informations, consultez [comment créer et utiliser des groupes dans la gestion des API Azure][].

![Ajouter un groupe de développeurs][api-management-add-developers-group]

Activez la case à cocher **les développeurs** et puis cliquez sur **Enregistrer**.

## <a name="add-api"> </a>Pour ajouter une API pour le produit

Dans cette étape de ce didacticiel, nous allons ajouter l’API écho pour le nouveau produit de la version d’évaluation gratuite.

>Chaque instance de service de gestion de l’API est préconfigurée avec une API d’écho qui peut être utilisé pour tester et en savoir plus sur la gestion de l’API. Pour plus d’informations, consultez [Gérer votre première API dans Azure API de gestion][].

Cliquez sur les **produits** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Version d’évaluation gratuite** pour configurer le produit.

![Configurer le produit][api-management-configure-product]

Cliquez sur **Ajouter une API pour le produit**.

![Ajouter des API pour le produit][api-management-add-api]

Sélectionnez des **API d’écho**, puis cliquez sur **Enregistrer**.

![Ajouter des API d’écho][api-management-add-echo-api]

## <a name="policies"> </a>Pour configurer les stratégies de quota et de limite de taux appel

Limites de débit et de quotas sont configurés dans l’éditeur de stratégie. Dans le menu de **Gestion de l’API** sur la gauche, cliquez sur **stratégies** . Dans la liste des **produits** , cliquez sur **Essai gratuit**.

![Stratégie de produit][api-management-product-policy]

Cliquez sur **Ajouter une stratégie** pour importer le modèle de stratégie et de commencer à créer des stratégies de quota et de limite le taux.

![Ajouter une stratégie][api-management-add-policy]

Pour insérer des stratégies, placez le curseur dans une section soit **entrant** ou **sortant** du modèle de stratégie. Politique de limite de quota et le taux de stratégies entrants, par conséquent, placez le curseur dans l’élément entrant.

![Éditeur de stratégie][api-management-policy-editor-inbound]

Les deux stratégies que nous ajoutons à ce didacticiel sont les stratégies de [taux d’appel limite par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et la [définition du quota d’utilisation par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Instructions de stratégie][api-management-limit-policies]

Une fois que le curseur est positionné dans l’élément **entrant** de la stratégie, cliquez sur la flèche en regard des **taux d’appel limite par abonnement** pour insérer son modèle de stratégie.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Taux d’appels de limite par abonnement** peut être utilisée au niveau du produit et peuvent également être utilisées par les API et les niveaux de nom de chaque opération. Dans ce didacticiel, seules les stratégies au niveau du produit sont utilisés, donc de supprimer les éléments **d’api** et le **fonctionnement** de l’élément **la limite de taux** , ainsi que le reste d’élément externe **- la limite de taux** , comme illustré dans l’exemple suivant.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Dans le produit de la version d’évaluation gratuite, le taux d’appel autorisée maximale est de 10 appels par minute, tapez **10** , ainsi que la valeur de l’attribut **d’appels** , **60** pour l’attribut **période de renouvellement** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Pour configurer la stratégie **définir le quota d’utilisation par abonnement** , placez votre curseur juste sous l’élément récemment ajouté **à la limite de taux** au sein de l’élément **entrant** et puis cliquez sur la flèche située à gauche de la **définition du quota d’utilisation par abonnement**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Étant donné que cette stratégie doit également être au niveau du produit, supprimer les éléments nom **d’api** et **d’opération** , comme illustré dans l’exemple suivant.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Quotas peuvent être basés sur le nombre d’appels par intervalle, bande passante ou les deux. Dans ce didacticiel, nous ne sommes pas la limitation de la bande passante en fonction, donc supprimer l’attribut de **la bande passante** .

    <quota calls="number" renewal-period="seconds">
    </quota>

Dans le produit de la version d’évaluation gratuite, le quota est 200 appels par semaine. Spécifier les **200** comme valeur pour l’attribut **d’appels** et de configurer **604800** comme valeur pour l’attribut **période de renouvellement** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalles de stratégie sont spécifiées en secondes. Pour calculer l’intervalle d’une semaine, vous pouvez multiplier le nombre de jours (7) par le nombre d’heures d’une journée (24) par le nombre de minutes dans une heure (60) par le nombre de secondes dans une minute (60) : 7 *24* 60 * 60 = 604800.

Lorsque vous avez terminé la configuration de la stratégie, elle doit correspondre à l’exemple suivant.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Après avoir configuré les stratégies souhaitées, cliquez sur **Enregistrer**.

![Enregistrer la stratégie][api-management-policy-save]

## <a name="publish-product"></a> Pour publier le produit

Maintenant que l’API sont ajoutés et les stratégies sont configurées, le produit doit être publié afin qu’il peut être utilisé par les développeurs. Cliquez sur les **produits** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Version d’évaluation gratuite** pour configurer le produit.

![Configurer le produit][api-management-configure-product]

Cliquez sur **Publier**, puis cliquez sur **Oui, publiez-le** à confirmer.

![Publier le produit][api-management-publish-product]

## <a name="subscribe-account"> </a>Pour vous abonner à un compte de développeur pour le produit

Maintenant que le produit est publié, il est disponible pour être abonné et utilisée par les développeurs.

>Les administrateurs d’une instance de gestion de l’API sont automatiquement inscrit pour chaque produit. Dans ce didacticiel étape, nous vous inscrire un des comptes d’administrateur non développeur pour le produit de la version d’évaluation gratuite. Si votre compte fait partie du rôle Administrateurs, puis vous pouvez suivre cette étape, même si vous êtes déjà inscrit.

Cliquez sur **utilisateurs** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur le nom de votre compte de développeur. Dans cet exemple, nous utilisons le compte de développeur **Clayton Gragg** .

![Configurer le développeur][api-management-configure-developer]

Cliquez sur **Ajouter un abonnement**.

![Ajouter l’abonnement][api-management-add-subscription-menu]

Sélectionnez la **Version d’évaluation gratuite**, puis cliquez sur **s’abonner**.

![Ajouter l’abonnement][api-management-add-subscription]

>[AZURE.NOTE] Dans ce didacticiel, plusieurs abonnements simultanées ne sont pas activées pour le produit de la version d’évaluation gratuite. S’il s’agissait, vous êtes invité à nom de l’abonnement, comme indiqué dans l’exemple suivant.

![Ajouter l’abonnement][api-management-add-subscription-multiple]

Après avoir cliqué sur **s’abonner**, le produit s’affiche dans la liste **d’abonnement** pour l’utilisateur.

![Abonnement ajouté][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Pour appeler une opération et test de la limite de taux

Maintenant que le produit de la version d’évaluation gratuite est configuré et publié, nous pouvons appeler certaines opérations et tester la stratégie de limite de vitesse.
Basculez vers le portail de développement en cliquant sur **portail destiné aux développeurs** dans le menu supérieur droit.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis cliquez sur **API d’écho**.

![Portail des développeurs][api-management-developer-portal-api-menu]

Cliquez sur **Obtenir la ressource**, puis cliquez sur **essayer**.

![Ouvrir la console][api-management-open-console]

Conserver les valeurs de paramètre par défaut et sélectionnez votre clé d’inscription du produit de la version d’évaluation gratuite.

![Clé de l’abonnement][api-management-select-key]

>[AZURE.NOTE] Si vous disposez de plusieurs abonnements, veillez à sélectionner la clé de la **Version d’évaluation gratuite**, sinon les stratégies qui ont été configurés dans les étapes précédentes ne seront pas appliqué.

Cliquez sur **Envoyer**, puis afficher la réponse. Notez l' **état de la réponse** de **200 OK**.

![Résultats de l’opération][api-management-http-get-results]

Cliquez sur **Envoyer** à une vitesse supérieure à la stratégie de limite de vitesse de 10 appels par minute. Une fois la stratégie de limite de taux est dépassée, un état de la réponse de **429 trop grand nombre de demandes** est retourné.

![Résultats de l’opération][api-management-http-get-429]

Le **contenu de la réponse** indique le temps restant avant de tentatives sera réussies.

Lorsque la stratégie de limite de vitesse de 10 appels par minute est activée, les appels suivants échoueront jusqu'à 60 secondes se sont écoulées depuis la première des 10 appels réussies pour le produit avant le dépassée de la limite de taux. Dans cet exemple, le temps restant est de 54 secondes.

## <a name="next-steps"> </a>Étapes suivantes

-   Regarder une démonstration de la définition de quotas et limites de taux dans la vidéo suivante.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Gérer votre première API dans la gestion des API Azure]: api-management-get-started.md
[Comment créer et utiliser des groupes dans la gestion des API Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurer les stratégies de quota et de limite de taux appel]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
