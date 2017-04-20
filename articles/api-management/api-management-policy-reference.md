<properties 
    pageTitle="Référence API Azure de la stratégie de gestion" 
    description="Obtenir des informations sur les stratégies disponibles pour configurer la gestion de l’API." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Référence API Azure de la stratégie de gestion

Cette section fournit un index pour les stratégies dans la [gestion de l’API de référence de la stratégie][]. Pour plus d’informations sur l’ajout et la configuration des stratégies, voir [stratégies de gestion de l’API][].

Les expressions de stratégie peuvent être utilisées en tant que valeurs d’attribut ou de valeurs de texte dans une des stratégies de gestion de l’API, sauf indication contraire de la stratégie. Certaines stratégies comme les stratégies de [flux de contrôle][] et de [définir de variable][] sont basées sur des expressions de stratégie. Pour plus d’informations, consultez [expressions de stratégie][] et les [Stratégies avancées][]

## <a name="policy-reference-index"></a>Index de référence de stratégie

-   [Stratégies de restriction d’accès][]
    -   [En-tête HTTP de vérification][] - applique l’existence et/ou la valeur d’un en-tête HTTP.
    -   [Taux d’appels de limite par abonnement][] - utilisation de l’API d’empêche de pics en limitant le taux d’appel, sur une base par abonnement.
    -   [Taux d’appels de limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - utilisation de l’API d’empêche de pics en limitant le taux d’appel, sur une base par clé.
    -   [Restreindre l’appelant IPs][] - filtres (permet/refuse) appels à partir des adresses IP spécifiques ou des plages d’adresses.
    -   [Définir le quota d’utilisation par abonnement][] - vous permet d’appliquer un renouvelable ou à durée de vie appel volume et/ou de la bande passante quota, sur une base par abonnement.
    -   [Définir le quota d’utilisation par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - vous permet d’appliquer un renouvelable ou à durée de vie appel volume et/ou de la bande passante quota, sur une base par clé.
    -   [Valider la JWT][] - applique l’existence et la validité d’un JWT extraite d’un en-tête HTTP spécifié ou un paramètre de requête spécifié.
-   [Stratégies avancées][]
    -   [Flux de contrôle][] - applique conditionnellement des instructions de stratégie basées sur les résultats de l’évaluation des [expressions][]type Boolean.
    -   [Demande de transfert][] - transfère la demande au service back-end.
    -   [Journal des événement concentrateur][] : envoie des messages dans le format spécifié à une cible du message définie par une entité de [journal](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
    -   [Réessayer](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - l’exécution de nouvelles tentatives des instructions de stratégie fermée, si et jusqu'à ce que la condition est remplie. L’exécution se répète à intervalles réguliers et à spécifié le nombre de tentatives.
    -   [Renvoyer la réponse](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - les abandons de l’exécution du pipeline et renvoie la réponse directement à l’appelant.
    -   [Envoyer la demande d’une des méthodes](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - envoie une demande vers l’URL spécifiée sans attendre de réponse.
    -   [Envoyer demande](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - d’envoie une demande vers l’URL spécifiée.
    -   [Méthode de demande set](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - vous permet de modifier la méthode HTTP pour une demande.
    -   [Définir le statut](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - de modifie le code d’état HTTP à la valeur spécifiée.
    -   [Définissez la variable][] - conserver une valeur dans une variable nommée de [contexte][] pour un accès ultérieur.
    -   [Trace](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - ajoute une chaîne dans la sortie de [l’Inspecteur de l’API](../api-management/api-management-howto-api-inspector.md) .
    -   [Attente](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - attend entre envoyer la demande, d’obtenir la valeur à partir du cache ou les stratégies de contrôle de flux pour terminer avant de poursuivre.
-   [Stratégies d’authentification][]
    -   [Authentification Basic][] - authentifier avec un service back-end à l’aide de l’authentification de base.
    -   [Authentification par certificat client][] - authentifier avec un service back-end à l’aide de certificats clients.
-   [La mise en cache des stratégies][] 
    -   [Obtenir à partir du cache][] - effectuer le cache de rechercher et de retourner une réponse mise en cache valide lorsqu’elle est disponible.
    -   [Magasin de cache][] - réponse de Caches en fonction de la configuration de contrôle de cache spécifiée.
    -   [Obtenir la valeur du cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - récupérer un élément mis en cache par clé.
    -   [Valeur de banque d’informations dans le cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - banque d’un élément dans le cache de clé.
    -   [Supprimer la valeur du cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - suppression d’un élément dans le cache de clé.
-   [Entre les stratégies de domaine][] 
    -   [Autoriser les appels interdomaines][] - rend l’API accessible à partir de clients basés sur navigateur Adobe Flash et Microsoft Silverlight.
    -   [CORS][] - ajoute la prise en charge pour une opération ou d’une API pour permettre les appels interdomaines à partir des clients basés sur navigateur (CORS) le partage des ressources entre-origine.
    -   [JSONP][] - ajoute le JSON padding (JSONP) la prise en charge d’une opération ou une API pour permettre les appels interdomaines à partir de clients de navigateur JavaScript.
-   [Stratégies de transformation][] 
    -   Le corps à partir de JSON [Convertir de JSON et XML][] - convertit demande ou la réponse au format XML.
    -   [Convertir le XML en JSON][] - convertit demande ou réponse du corps à partir de XML en JSON.
    -   [Rechercher et remplacer la chaîne dans le corps][] - recherche une sous-chaîne de demande ou de réponse et le remplace par une autre sous-chaîne.
    -   [Masque les URL dans le contenu][] - liens de nouveau écrit (masques) dans la réponse du corps afin qu’ils pointent vers le lien équivalent via la passerelle.
    -   [Définir le service back-end][] - modifie le service back-end pour une demande entrante.
    -   [Définissez le corps][] - définit le corps du message pour les requêtes entrantes et sortantes.
    -   [En-tête HTTP de la valeur][] - assigne une valeur à un en-tête de demande et/ou une réponse existante ou ajoute un en-tête de réponse ou la demande de nouveau.
    -   [Définissez le paramètre de chaîne de requête][] - ajoute, valeur remplace ou supprime demande de paramètre de chaîne de requête.
    -   [Réécrire les URL][] - convertit une URL de requête à partir de sa forme public au formulaire attendu par le service web.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les expressions de stratégie, voir la vidéo suivante.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Stratégies de restriction d’accès]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Vérifier l’en-tête HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Taux d’appels de limite par abonnement]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Limiter l’appelant IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Quota d’utilisation de jeu par abonnement]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Valider JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Stratégies avancées]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flux de contrôle]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Jeu variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexte]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Demande de transfert]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Journal à concentrateur d’événements]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Stratégies d’authentification]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authentifier avec Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authentification par certificat client]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[La mise en cache des stratégies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obtenir à partir du cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Stocker dans le cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Entre les stratégies de domaine]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Autoriser les appels interdomaines]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Stratégies de transformation]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convertir JSON en XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Conversion de XML en JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Rechercher et remplacer la chaîne dans le corps]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Masque les URL dans le contenu]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Service de back-end de jeu]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Corps de jeu]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[En-tête HTTP de jeu]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Paramètre de chaîne de requête de jeu]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Réécriture d’URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Stratégies de gestion de l’API]: api-management-howto-policies.md
[Référence de stratégie de gestion de l’API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expressions de stratégie]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
