<properties
    pageTitle="Analyser les modèles d’utilisation Azure CDN | Microsoft Azure"
    description="Vous pouvez afficher des modèles d’utilisation pour le Canada en utilisant les rapports suivants : bande passante, les données transférées, accès, statuts de Cache, le taux de présence Cache, IPV4/IPV6 données transférées."
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

# <a name="analyze-azure-cdn-usage-patterns"></a>Analyser les modèles d’utilisation Azure CDN

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Vous pouvez afficher des modèles d’utilisation pour le Canada en utilisant les rapports suivants :

- Bande passante
- Données transférées
- Accès
- Statuts de cache
- Taux de présence cache
- IPv4/IPV6 des données transférées

## <a name="accessing-advanced-http-reports"></a>L’accès aux rapports HTTP avancée

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-reports/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Pointez sur l’onglet **Analytique** , puis survolez le Lanceur de **Rapports de base** .  Cliquez sur le rapport de votre choix dans le menu.

    ![Portail de gestion CDN - menu rapports de base](./media/cdn-reports/cdn-core-reports.png)


## <a name="bandwidth"></a>Bande passante

L’état de la bande passante se compose d’une table de données et le graphique indiquant l’utilisation de la bande passante pour HTTP et HTTPS sur une période donnée. Vous pouvez afficher tous les POP de CDN ou un POP notamment l’utilisation de la bande passante. Cela vous permet d’afficher les pics de trafic et la distribution sur CDN POP en Mbits/s.

- Sélectionner tous les nœuds de bord pour voir le trafic à partir de tous les nœuds, ou choisissez un région/nœud spécifique dans la liste déroulante.
- Sélectionnez la plage de dates pour afficher les données pour ce jour/semaine/mois, etc. ou saisir des dates personnalisées, puis cliquez sur « OK » pour vous assurer que votre sélection est mise à jour.
- Vous pouvez exporter et télécharger les données en cliquant sur l’icône de feuille excel situé à côté de « go ».

Le rapport est mis à jour toutes les 5 minutes.

![État de la bande passante](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Données transférées

Ce rapport se compose d’une table de données et le graphique indiquant l’utilisation du trafic HTTP et HTTPS sur une période donnée. Vous pouvez afficher tous les POP de CDN ou un POP notamment l’utilisation du trafic. Vous pouvez ainsi afficher les pics de trafic et la distribution sur CDN POP dans Go.

- Sélectionner tous les nœuds de bord pour le trafic à partir de toutes les notes ou choisissez un région/nœud spécifique dans la liste déroulante.
- Sélectionnez la plage de dates pour afficher les données pour ce jour/semaine/mois, etc. ou saisir des dates personnalisées, puis cliquez sur « OK » pour vous assurer que votre sélection est mise à jour.
- Vous pouvez exporter et télécharger les données en cliquant sur l’icône de feuille excel situé à côté de « go ».

Le rapport est mis à jour toutes les 5 minutes.

![Rapport de transfert de données](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Accès (codes d’état)

Ce rapport décrit la distribution des codes de statut de demande de votre contenu. Chaque demande de contenu génère un code d’état HTTP. Le code d’état décrit comment POP de bord traité la demande. Par exemple, les codes d’état de 2xx indiquent que la demande a été correctement pris en charge pour un client, tandis qu’un code d’état 4xx indique une erreur s’est produite. Pour plus d’informations sur le code d’état HTTP, reportez-vous à la section [codes de statut](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

- Sélectionnez la plage de dates pour afficher les données pour ce jour/semaine/mois, etc. ou saisir des dates personnalisées, puis cliquez sur « OK » pour vous assurer que votre sélection est mise à jour.
- Vous pouvez exporter et télécharger les données en cliquant sur la feuille excel située à côté de « go ».

![Rapport d’accès](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Statuts de cache

Ce rapport décrit la distribution des correspondances dans le cache et absences dans le cache pour la requête du client. Dans la mesure où les meilleures performances proviennent de présences dans le cache, vous pouvez optimiser les vitesses de livraison de données en réduisant échecs dans le cache et de présences dans le cache a expiré. Absences dans le cache peuvent être réduites par la configuration de votre serveur d’origine pour éviter d’affecter les en-têtes de réponse de « no-cache », en évitant la chaîne de requête mise en cache, sauf lorsque cela est strictement nécessaire et en évitant les codes de réponse non mise en cache. Du cache expirés accès peuvent être évités en effectuant une immobilisation 's max-age aussi longtemps que possible afin de réduire le nombre de requêtes au serveur d’origine.

![État des statuts de cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Les statuts de cache principal sont les suivantes :

- TCP_HIT : Pris en charge à partir du bord. L’objet était dans le cache et n’avait pas dépassé son max-age.
- TCP_MISS : Pris en charge à partir de l’origine. L’objet n’est pas dans le cache et la réponse était à l’origine.
- TCP_EXPIRED _MISS : provenant d’origine après la revalidation avec l’origine. L’objet était dans le cache mais avait dépassé ses max-age. Une revalidation avec origine a entraîné l’objet cache remplacé par une nouvelle réponse d’origine.
- TCP_EXPIRED _HIT : provenant de bord après la revalidation avec l’origine. L’objet était dans le cache mais avait dépassé ses max-age. Une revalidation avec le serveur d’origine a entraîné l’objet de cache en cours non modifié.

- Sélectionnez la plage de dates pour afficher les données pour ce jour/semaine/mois, etc. ou saisir des dates personnalisées, puis cliquez sur « OK » pour vous assurer que votre sélection est mise à jour.
- Vous pouvez exporter et télécharger les données en cliquant sur l’icône de feuille excel situé à côté de « go ».

### <a name="full-list-of-cache-statuses"></a>Liste complète des statuts de cache

- TCP_HIT - ce statut est signalé lorsqu’une demande est pris en charge directement à partir de la fenêtre contextuelle sur le client. Une immobilisation est immédiatement pris en charge à partir d’un POP lorsqu’il est mis en cache sur le POP le plus proche du client et il a un time-to-live, durée de vie. Durée de vie est déterminée par les en-têtes de réponse suivants :

    - Cache-Control : s-maxage
    - Cache-Control : max-age
    - Expire

- TCP_MISS - cet état indique qu’une version mise en cache de la ressource demandée est introuvable sur le POP le plus proche du client. L’immobilisation sera demandée à partir d’un serveur d’origine, ou un serveur de protection d’origine. Si le serveur d’origine ou le serveur de protection d’origine retourne un actif, il sera pris en charge sur le client et mis en cache sur le client et le serveur de transport edge. Dans le cas contraire, un code d’état de non-200 (par exemple, 403 interdit, 404 non trouvé, etc.) seront retournés.

- _HIT de TCP_EXPIRED - ce statut est signalé lorsqu’une demande ciblant une immobilisation ayant une durée de vie a expiré, par exemple lorsque max-age l’actif a expiré, a été pris en charge directement à partir de la fenêtre contextuelle sur le client.

    Une demande a expiré entraîne généralement une demande de revalidation au serveur d’origine. Dans l’ordre pour un _HIT de TCP_EXPIRED se produire, le serveur d’origine doit indiquer qu’une version plus récente de la ressource n’existe pas. Ce type de situation met généralement à jour de cet actif Cache-Control et Expires en-têtes.

- _MISS de TCP_EXPIRED - ce statut est signalé lorsqu’une version plus récente d’un élément mis en cache a expiré est pris en charge à partir de la fenêtre contextuelle sur le client. Cela se produit lorsque la durée de vie pour un élément mis en cache a expiré (par exemple, expiré max-age) et le serveur d’origine retourne une version plus récente de cet actif. Cette nouvelle version de l’actif sera servie au client au lieu de la version mise en cache. En outre, il va être mis en cache sur le serveur de transport edge et le client.

- CONFIG_NOCACHE - cet état indique qu’une configuration spécifique au client sur notre bord POP a empêché l’actif sont mis en cache.

- Aucun - cet état indique qu’un contrôle de contenu de fraîcheur du cache n’a pas été effectué.

- _MISS de CLIENT_REFRESH de TCP_ - cet état est indiqué lorsqu’un client HTTP (par exemple, le navigateur) force une arête POP pour extraire une nouvelle version d’un actif obsolète à partir du serveur d’origine.

    Par défaut, nos serveurs empêchent un client HTTP de forcer nos serveurs de périphérie pour extraire une nouvelle version de l’immobilisation à partir du serveur d’origine.

- PARTIAL_HIT de TCP_ - ce statut est signalé lorsqu’une demande de plage d’octets entraîne un accès à une immobilisation partiellement mises en cache. La plage d’octets demandée est immédiatement pris en charge à partir de la fenêtre contextuelle sur le client.

- UNCACHEABLE - cet état est indiqué lorsque les en-têtes de Cache-Control et Expires d’un élément indiquent qu’il ne doit pas être mis en cache sur un POP ou par le client HTTP. Ces types de requêtes sont servis à partir du serveur d’origine

## <a name="cache-hit-ratio"></a>Taux de présence cache

Ce rapport indique le pourcentage de demandes mises en cache qui ont été pris en charge directement à partir du cache.

Le rapport fournit les informations suivantes :

- Le contenu demandé a été mis en cache sur le POP le plus proche au demandeur.
- La demande a été pris en charge par le bord de notre réseau.
- La demande ne nécessitaient pas de revalidation avec le serveur d’origine.

Le rapport ne comprend pas :

- Requêtes refusées en raison des options de filtrage des pays.
- Demandes de ressources dont les en-têtes indiquent qu’ils ne doivent pas être mis en cache. Par exemple, Cache-Control : private, Cache-Control : non-cache ou Pragma : en-têtes non-cache empêche une ressource mise en cache.
- Demandes de plage d’octets de contenu partiellement mis en cache.

La formule est : (TCP_ accès / (positionnement TCP_ + TCP_MISS)) * 100

- Sélectionnez la plage de dates pour afficher les données pour ce jour/semaine/mois, etc. ou saisir des dates personnalisées, puis cliquez sur « OK » pour vous assurer que votre sélection est mise à jour.
- Vous pouvez exporter et télécharger les données en cliquant sur l’icône de feuille excel situé à côté de « go ».


![Rapport des taux de réussite du cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Transfert de données IPv4/IPV6

Cet état affiche la répartition de l’utilisation du trafic IPV4 et IPV6.

![Transfert de données IPv4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

- Sélectionnez la plage de dates pour afficher les données pour ce jour/semaine/mois, etc. ou saisir des dates personnalisées.
- Puis, cliquez sur « OK » pour vous assurer que votre sélection est mise à jour.


## <a name="considerations"></a>Considérations relatives à la

Les rapports ne peuvent pas être générés au cours des 18 derniers mois.
