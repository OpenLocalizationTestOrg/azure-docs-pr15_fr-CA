<properties
    pageTitle="Ajouter la mise en cache pour améliorer les performances dans la gestion des API Azure | Microsoft Azure"
    description="Découvrez comment améliorer le temps de latence, la consommation de bande passante et le service web de charge pour les appels de service de gestion de l’API."
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

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Ajouter la mise en cache pour améliorer les performances dans la gestion des API Azure

Dans Gestion de l’API peuvent être configurées pour la mise en cache de la réponse. Mise en cache de la réponse peut considérablement réduire la latence de l’API, la consommation de bande passante et web charge de service pour les données qui ne changent pas fréquemment.

Ce guide vous montre comment ajouter la réponse mise en cache pour votre API et de configurer des stratégies pour les opérations API d’écho de l’exemple. Vous pouvez ensuite appeler l’opération à partir du portail développeur pour vérifier la mise en cache en action.

>[AZURE.NOTE] Pour plus d’informations sur la mise en cache des éléments par la clé à l’aide d’expressions de stratégie, consultez [Personnaliser la mise en cache dans Azure API de gestion](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de suivre les étapes décrites dans ce guide, vous devez disposer d’une instance du service de gestion de l’API avec une API et un produit configuré. Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

## <a name="configure-caching"> </a>Configurer une opération de mise en cache

Dans cette étape, vous allez examiner les paramètres de mise en cache de l’opération **Obtenir des ressources (en cache)** de l’API de l’écho de l’échantillon.

>[AZURE.NOTE] Chaque instance de service de gestion de l’API est préconfiguré avec une API d’écho qui peut être utilisé pour tester et en savoir plus sur la gestion de l’API. Pour plus d’informations, consultez [mise en route de la gestion des API Azure][].

Pour commencer, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de Publisher][api-management-management-console]

Cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, puis cliquez sur **API d’écho**.

![API d’écho][api-management-echo-api]

Cliquez sur l’onglet **opérations** , puis cliquez sur l’opération **Obtenir des ressources (en cache)** dans la liste des **opérations** .

![Opérations API d’écho][api-management-echo-api-operations]

Cliquez sur l’onglet **mise en cache** pour afficher les paramètres de mise en cache pour cette opération.

![Onglet mise en cache][api-management-caching-tab]

Pour activer la mise en cache pour une opération, activez la case à cocher **Activer** . Dans cet exemple, la mise en cache est activée.

Chaque réponse de l’opération est indexé, basée sur les valeurs dans les champs de **variation par paramètres de chaîne de requête** et de la **variation par les en-têtes** . Si vous souhaitez mettre en cache des réponses multiples, en fonction des paramètres de chaîne de requête ou les en-têtes, vous pouvez les configurer dans ces deux champs.

**Durée** Spécifie l’intervalle d’expiration des réponses mises en cache. Dans cet exemple, l’intervalle est **3 600** secondes, ce qui équivaut à une heure.

À l’aide de la configuration de mise en cache dans cet exemple, la première demande pour l’opération **Obtenir des ressources (en cache)** retourne une réponse à partir du service back-end. Cette réponse sera cachée, indexé par les paramètres de chaîne de requête et les en-têtes spécifiés. Les appels suivants à l’opération, avec les paramètres correspondants, auront la réponse mise en cache est retournée, jusqu'à l’expiration de l’intervalle de durée du cache.

## <a name="caching-policies"> </a>Passer en revue les stratégies de mise en cache

Dans cette étape, vous passez en revue les paramètres de mise en cache pour l’opération **Obtenir des ressources (en cache)** de l’API de l’écho de l’échantillon.

Lors de la configuration des paramètres de mise en cache pour une opération sous l’onglet **mise en cache** , les stratégies de mise en cache sont ajoutés pour l’opération. Ces stratégies peuvent être affichées et modifiées dans l’éditeur de stratégie.

Cliquez sur **stratégies de** dans le menu de **Gestion de l’API** sur la gauche, puis sélectionnez **Echo API / obtenir les ressources (en cache)** à partir de la liste déroulante des **opérations** .

![Opération de portée de stratégie][api-management-operation-dropdown]

Cela affiche les stratégies pour cette opération dans l’éditeur de stratégie.

![Éditeur de stratégie de gestion de l’API][api-management-policy-editor]

La définition de stratégie pour cette opération inclut les stratégies qui définissent la configuration de mise en cache qui ont été vérifiés à l’aide de l’onglet **mise en cache** dans l’étape précédente.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Les modifications apportées aux stratégies de mise en cache dans l’éditeur de stratégie apparaîtra sous l’onglet **mise en cache** d’une opération et vice versa.

## <a name="test-operation"> </a>Appelle une opération et test de la mise en cache

Pour voir la mise en cache en action, nous pouvons appeler l’opération à partir du portail de développement. Dans le menu supérieur de droit, cliquez sur **portail destiné aux développeurs** .

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur et sélectionnez **API d’écho**.

![API d’écho][api-management-apis-echo-api]

>Si vous avez une seule API configurée ou à visible à votre compte, puis en cliquant sur API prend directement aux opérations de cette API.

Sélectionnez l’opération **Obtenir des ressources (en cache)** , puis cliquez sur **Ouvrir la Console**.

![Ouvrir la console][api-management-open-console]

La console vous permet d’appeler des opérations directement à partir du portail de développement.

![Console][api-management-console]

Conservez les valeurs par défaut des **param1** **param2**.

Sélectionnez la clé voulue à partir de la liste déroulante **clé de l’abonnement** . Si votre compte n’a qu’un seul abonnement, il sera déjà sélectionné.

Entrez **sampleheader:value1** dans la zone **en-têtes de demande** .

Cliquez sur **HTTP Get** et prenez note des en-têtes de réponse.

Entrez **sampleheader:value2** dans la zone **en-têtes de requête** , puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** est toujours la **valeur1** dans la réponse. Essayez des différentes valeurs et notez que la réponse mise en cache dans le premier appel est retournée.

Entrez **25** dans le champ **param2** , puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** dans la réponse est désormais **value2**. Étant donné que les résultats de l’opération sont indexées par chaîne de requête, la réponse mise en cache précédente n’est pas retournée.

## <a name="next-steps"> </a>Étapes suivantes

-   Pour plus d’informations sur les stratégies de mise en cache, consultez [mise en cache des stratégies][] dans la [gestion de l’API de référence de la stratégie][].
-   Pour plus d’informations sur la mise en cache des éléments par la clé à l’aide d’expressions de stratégie, consultez [Personnaliser la mise en cache dans Azure API de gestion](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Mise en route de la gestion des API Azure]: api-management-get-started.md

[Référence de stratégie de gestion de l’API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[La mise en cache des stratégies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
