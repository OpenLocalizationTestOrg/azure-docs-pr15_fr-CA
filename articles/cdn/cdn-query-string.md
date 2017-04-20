<properties
    pageTitle="Contrôle Azure CDN comportement de demandes de chaînes de requête de cache | Microsoft Azure"
    description="Chaîne de requête Azure CDN la mise en cache des contrôles, comment les fichiers doivent être mis en cache lorsqu’elles contiennent des chaînes de requête."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings"></a>Contrôle du comportement de mise en cache de requêtes CDN avec des chaînes de requête

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Prime d’Azure CDN de Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Vue d’ensemble

La mise en cache des contrôles, comment les fichiers doivent être mis en cache lorsqu’elles contiennent des chaînes de requête de chaîne de requête.

> [AZURE.IMPORTANT] Les produits Standard et Premium CDN fournissent la même chaîne de requête, la mise en cache, mais diffère de l’interface utilisateur.  Ce document décrit l’interface pour **Azure CDN Standard à partir d’Akamai** et **Azure CDN Standard de Verizon**.  Requête de chaîne mise en cache avec **Prime à Azure CDN de Verizon**, consultez [comportement de mise en cache de contrôle de demandes CDN avec des chaînes de requête - prime](cdn-query-string-premium.md).

Trois modes sont disponibles :

- **Ignorer les chaînes de requête**: c’est le mode par défaut.  Le nœud de bord CDN passe la chaîne de requête auprès du demandeur à l’origine de la première demande et un cache l’actif.  Toutes les demandes ultérieures de cette immobilisation sont servis à partir du nœud de bord ignore la chaîne de requête jusqu'à ce que l’élément mis en cache expire.
- **Ignorer la mise en cache pour les URL avec des chaînes de requête**: dans ce mode, les demandes de chaînes de requête ne sont pas mis en cache au niveau du nœud de bord CDN.  Le nœud de bord récupère l’immobilisation directement à partir de l’origine et le transmet au demandeur avec chaque demande.
- **Mettre en cache chaque URL unique**: ce mode traite chaque demande avec une chaîne de requête comme une ressource unique avec son propre cache.  Par exemple, la réponse à l’origine d’une demande de *foo.ashx?q=bar* est mis en cache au niveau du nœud de bord et retournée pour les caches suivants avec cette même chaîne de requête.  Une demande de *foo.ashx?q=somethingelse* devrait être mis en cache comme un actif distinct avec sa propre durée de vie.

##<a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modification des paramètres des profils CDN standard de mise en cache de chaîne de requête

1. À partir de la blade de profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.

    ![Points de terminaison CDN profil lame](./media/cdn-query-string/cdn-endpoints.png)

    La lame de point de terminaison CDN s’ouvre.

2. Cliquez sur le bouton **configurer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-query-string/cdn-config-btn.png)

    La lame CDN Configuration s’ouvre.

3. Sélectionnez un paramètre dans la liste déroulante du **comportement de cache de chaîne de requête** .

    ![Chaîne de requête CDN options mise en cache](./media/cdn-query-string/cdn-query-string.png)

4. Après avoir effectué votre sélection, cliquez sur le bouton **Enregistrer** .

> [AZURE.IMPORTANT] Les modifications apportées aux paramètres ne sont peut-être pas visibles immédiatement, comme le temps requis pour l’enregistrement de se propager par l’intermédiaire du CDN.  Pour les profils <b>Azure CDN à partir d’Akamai</b> , propagation se terminera en une minute.  Pour les profils <b>Azure CDN de Verizon</b> , propagation termine généralement l’opération dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.
