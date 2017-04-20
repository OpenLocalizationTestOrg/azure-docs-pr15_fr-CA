<properties
    pageTitle="Contrôle de prime à Azure CDN de Verizon comportement de demandes de chaînes de requête de cache | Microsoft Azure"
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

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Contrôle du comportement de mise en cache de requêtes CDN avec des chaînes de requête - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Prime d’Azure CDN de Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Vue d’ensemble

La mise en cache des contrôles, comment les fichiers doivent être mis en cache lorsqu’elles contiennent des chaînes de requête de chaîne de requête.

> [AZURE.IMPORTANT] Les produits Standard et Premium CDN fournissent la même chaîne de requête, la mise en cache, mais diffère de l’interface utilisateur.  Ce document décrit l’interface de **Prime à Azure CDN auprès de Verizon**.  Requête de chaîne mise en cache avec **Azure CDN Standard à partir d’Akamai** et **Azure CDN Standard de Verizon**, consultez [comportement de mise en cache de contrôle de demandes CDN avec des chaînes de requête](cdn-query-string.md).

Trois modes sont disponibles :

- **cache de standard**: il s’agit du mode par défaut.  Le nœud de bord CDN passe la chaîne de requête auprès du demandeur à l’origine de la première demande et un cache l’actif.  Toutes les demandes ultérieures de cette immobilisation sont servis à partir du nœud de bord ignore la chaîne de requête jusqu'à ce que l’élément mis en cache expire.
- **no-cache**: dans ce mode, les demandes de chaînes de requête ne sont pas mis en cache au niveau du nœud de bord CDN.  Le nœud de bord récupère l’immobilisation directement à partir de l’origine et le transmet au demandeur avec chaque demande.
- **cache unique**: ce mode traite chaque demande avec une chaîne de requête comme une ressource unique avec son propre cache.  Par exemple, la réponse à l’origine d’une demande de *foo.ashx?q=bar* est mis en cache au niveau du nœud de bord et retournée pour les caches suivants avec cette même chaîne de requête.  Une demande de *foo.ashx?q=somethingelse* devrait être mis en cache comme un actif distinct avec sa propre durée de vie.

##<a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modifier la chaîne de requête mise en cache des paramètres pour les profils CDN premium

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Survolez l’onglet **HTTP volumineux** , puis pointez sur la fenêtre de **Paramètres de Cache** mobile.  Cliquez sur **la mise en cache d’une chaîne de requête**.

    Options de mise en cache de chaîne de requête sont affichées.

    ![Chaîne de requête CDN options mise en cache](./media/cdn-query-string-premium/cdn-query-string.png)

3. Après avoir effectué votre sélection, cliquez sur le bouton de **mise à jour** .


> [AZURE.IMPORTANT] Les modifications apportées aux paramètres ne sont peut-être pas visibles immédiatement, comme le temps requis pour l’enregistrement de se propager par l’intermédiaire du CDN.  Pour les profils <b>Azure CDN de Verizon</b> , propagation termine généralement l’opération dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.
