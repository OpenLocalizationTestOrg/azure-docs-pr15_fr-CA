<properties
    pageTitle="Analyser les performances de bord dans Azure CDN | Microsoft Azure"
    description="Analyser les performances de nœud de bord dans Microsoft Azure CDN. Analytique des performances de bord fournit les informations granulaires le trafic et la bande passante utilisé pour le CDN."
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

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analyser les performances de nœud de bord dans Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Vue d’ensemble
Analytique des performances de bord fournit des informations précises le trafic et la bande passante utilisé pour le CDN. Ces informations peuvent ensuite être utilisées à générer des statistiques des tendances, ce qui vous permet d’obtenir un aperçu sur la manière dont vos ressources sont en cours mis en cache et remis à vos clients. À son tour, cela vous permet de former une stratégie à optimiser la diffusion de vos contenus et pour déterminer quels problèmes doivent être traités pour mieux tirer parti du CDN. Par conséquent, non seulement vous pourrez améliorer les performances de livraison de données, mais vous serez également en mesure de réduire vos coûts CDN.

> [AZURE.NOTE] Tous les rapports d’utilisent la notation de l’UTC/GMT lorsque vous spécifiez une date/heure.

## <a name="reports-and-log-collection"></a>Collection de journaux et de rapports

Paie Canada-Fichier activité les données sont collectées par le module de bord performances Analytique avant elle peut générer des rapports dessus. Ce processus se produit une fois par jour et il traite l’activité qui a eu lieu au cours de la veille. Cela signifie que les statistiques d’un état représentent un échantillon des statistiques de la journée au moment il a été traité et pas nécessairement contenir l’ensemble des données pour le jour actuel. La fonction principale de ces rapports est pour évaluer les performances. Ils ne doivent pas servir à des fins de facturation ou des statistiques numériques exacts.

> [AZURE.NOTE] Les données brutes générées à partir de laquelle les rapports bord performances analytiques sont disponibles pendant au moins 90 jours.

## <a name="dashboard"></a>Tableau de bord

Le tableau de bord de bord performances Analytique suit actuel et historique CDN empruntant un graphique et les statistiques. Ce tableau de bord permet de détecter les tendances récentes et à long terme sur les performances du trafic CDN pour votre compte.

Ce tableau de bord se compose de :

* Un graphique interactif qui permet la visualisation des statistiques clés et des tendances.
* Une chronologie qui offre une idée des modèles de long terme les tendances et les mesures clés.
* Mesures principales statistiques et des informations sur la façon de notre réseau CDN améliore le trafic en termes de performances, d’utilisation et l’efficacité globale.

### <a name="accessing-the-edge-performance-dashboard"></a>Le tableau de bord de performance de bord de l’accès à

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Survolez l’onglet **Analytique** , puis pointez sur le Lanceur de **Bord performance Analytique** .  Cliquez sur le **tableau de bord**.

    Le tableau de bord de bord nœud analytique s’affiche.

### <a name="chart"></a>Graphique

Le tableau de bord contient un graphique qui suit une mesure sur la période sélectionnée dans la chronologie qui apparaît juste en dessous.  Une barre de planning que graphiques jusqu'à la dernière deux ans d’activité CDN s’affiche directement sous le graphique.

#### <a name="using-the-chart"></a>Utilisation du graphique

* Par défaut, le taux de rendement du cache pour les 30 derniers jours doit être représentée.
* Ce graphique est généré à partir des données rassemblées sur une base quotidienne.
* Survol d’un jour sur le graphique de la ligne indique une date et la valeur de la mesure à cette date.
* Cliquez sur Mettre en surbrillance les week-ends pour activer ou désactiver une superposition des barres verticales grises lumière qui représentent les week-ends dans le graphique. Ce type d’incrustation est utile pour identifier les modèles de trafic sur les week-ends.
* Cliquez sur Afficher une année auparavant pour basculer d’une superposition de l’activité de l’année précédente sur la même période de temps dans le graphique. Ce type de comparaison fournit un aperçu des modèles d’utilisation à long terme CDN. Le coin supérieur droit du graphique contient une légende qui indique le code de couleur pour chaque courbe.

#### <a name="updating-the-chart"></a>Mise à jour du graphique

* Plage de temps : Effectuer une des opérations suivantes :
    * Dans le panneau Montage, sélectionnez la région souhaitée. Le graphique est mis à jour avec les données correspondant à la période sélectionnée.
    * Double-cliquez sur le graphique pour afficher toutes les données historiques disponibles jusqu'à un maximum de deux ans.
* Métrique : Cliquez sur l’icône de graphique qui apparaît en regard de la mesure de votre choix. Le graphique et la chronologie seront actualisés avec les données de la mesure correspondante.


### <a name="key-metrics-and-statistics"></a>Statistiques et mesures clés

#### <a name="efficiency-metrics"></a>Mesures de l’efficacité

L’objectif de ces mesures est pour voir si les performances du cache peuvent être améliorées. Les principaux avantages que dérivées de l’efficacité du cache sont :

* Charge réduite sur le serveur d’origine qui peut conduire à :
    * Meilleures performances du serveur web.
    * Réduction des coûts opérationnels.
* Amélioré l’accélération de remise de données depuis plus de requêtes seront transmis directement à partir du CDN.

Champ | Description
------|------------
Efficacité du cache | Indique le pourcentage de données transférées et qui a été fournies à partir du cache. Cette métrique mesures lorsqu’une version mise en cache du contenu demandé a été pris en charge par le CDN (serveurs edge) aux émetteurs de demande (par exemple, le navigateur web)
Taux de réussite | Indique le pourcentage de requêtes qui ont été servies à partir du cache. Cette métrique mesures lorsqu’une version mise en cache du contenu demandé a été pris en charge par le CDN (serveurs edge) aux émetteurs de demande (par exemple, le navigateur web).
% d’octets à distance - aucune configuration de Cache | Indique le pourcentage du trafic qui a été fournie à partir de serveurs d’origine du CDN (serveurs edge) qui ne sont pas mis en cache à la suite de la fonctionnalité Cache de contournement (moteur de règles HTTP).
% d’octets à distance - expiration du Cache | Indique le pourcentage du trafic qui a été fournie à partir de serveurs d’origine du CDN (serveurs edge) à la suite de revalidation de contenus obsolète.

#### <a name="usage-metrics"></a>Mesures de l’utilisation

L’objectif de ces mesures est à fournir un aperçu des réduction des coûts des mesures suivantes :

* Réduction des coûts d’exploitation par le biais du CDN.
* Réduction des dépenses CDN par le biais de l’efficacité du cache et la compression.

> [AZURE.NOTE] Numéros des volumes de trafic représentent le trafic qui a été utilisé dans les calculs des taux et des pourcentages et peut afficher uniquement une partie du trafic total pour les clients de gros volumes.

Champ | Description
------|------------
Moyenne des octets sortants | Indique le nombre moyen d’octets transférés pour chaque demande pris en charge à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web).
Aucun taux d’octets de la configuration du Cache | Indique le pourcentage du trafic pris en charge à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web) qui n’est pas mis en cache en raison de la fonctionnalité Cache de contournement.
Taux d’octets compressés | Indique le pourcentage du trafic envoyé à partir du CDN (serveurs edge) aux émetteurs de demande (par exemple, le navigateur web) dans un format compressé.
Octets sortants | Indique la quantité de données, en octets, qui ont été remis à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web).  
Octets dans | Indique la quantité de données, en octets, envoyées à partir de (par exemple, le navigateur web) que les demandeurs du CDN (serveurs edge).
Distance d’octets | Indique la quantité de données, en octets, envoyés à partir de serveurs d’origine CDN et client du CDN (serveurs edge).

#### <a name="performance-metrics"></a>Mesures de performances

L’objectif de ces mesures est de suivre les performances globales du CDN pour le trafic.

Champ | Description
------|------------
Taux de transfert | Indique le taux moyen auquel le contenu a été transféré à partir du CDN à un demandeur.
Durée | Indique la durée moyenne, en millisecondes, nécessaire à la remise d’un actif à un demandeur (par exemple, le navigateur web).
Taux de demandes compressé | Indique le pourcentage d’accès qui ont été remis à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web) dans un format compressé.
Taux d’erreur 4xx | Indique le pourcentage d’accès qui a généré un code d’état 4xx.
Taux d’erreur 5xx | Indique le pourcentage d’accès qui a généré un code d’état 5xx.
Accès | Indique le nombre de requêtes pour le contenu CDN.

#### <a name="secure-traffic-metrics"></a>Mesures de sécurisation du trafic

L’objectif de ces mesures est à effectuer le suivi des performances du CDN pour le trafic HTTPS.

Champ | Description
------|------------
Efficacité du Cache sécurisé | Indique le pourcentage de données transférées pour les requêtes HTTPS qui ont été servies à partir du cache. Cette métrique mesures lorsqu’une version mise en cache du contenu demandé a été pris en charge par le CDN (serveurs edge) aux émetteurs de demande (par exemple, le navigateur web) via HTTPS.
Taux de transfert sécurisé | Indique le taux moyen auquel le contenu a été transféré à partir du CDN (serveurs edge) aux émetteurs de demande (par exemple, les serveurs web) via HTTPS.
Durée moyenne de sécurisé | Indique la durée moyenne, en millisecondes, nécessaire à la remise d’un actif à un demandeur (par exemple, le navigateur web) via HTTPS.
Accès sécurisés | Indique le nombre de requêtes HTTPS pour le contenu CDN.
Sécuriser des octets sortants | Indique la quantité de trafic HTTPS, en octets, qui ont été remis à partir du CDN (serveurs edge) au demandeur (par exemple, le navigateur web).

## <a name="reports"></a>Rapports

Chaque rapport dans ce module contient un graphique et les statistiques sur l’utilisation de la bande passante et du trafic pour différents types de mesures (par exemple, codes d’état HTTP, codes de statut de cache, demandent des URL, etc..). Ces informations peuvent servir à explorent le contenu est servie à vos clients et pour ajuster le comportement CDN pour améliorer les performances de livraison de données.

### <a name="accessing-the-edge-performance-reports"></a>Accès aux rapports de performances de bord

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Survolez l’onglet **Analytique** , puis pointez sur le Lanceur de **Bord performance Analytique** .  Cliquez sur **l’objet HTTP**.

    L’écran de rapports de bord nœud analytique s’affiche.

Rapport | Description
-------|------------
Résumé quotidien | Vous permet d’afficher les tendances de trafic quotidien sur une période de temps spécifiée. Chaque barre sur ce graphique représente une date particulière. La taille de la barre indique le nombre total de visites qui ont eu lieu à cette date.
Résumé de toutes les heures | Vous permet d’afficher toutes les heures les tendances du trafic pendant une période spécifiée. Chaque barre sur ce graphique représente une seule heure à une date donnée. La taille de la barre indique le nombre total d’accès qui s’est produite au cours de cette heure.
Protocoles | Affiche la répartition du trafic entre les protocoles HTTP et HTTPS. Un graphique indique le pourcentage d’accès qui s’est produite pour chaque type de protocole.
Méthodes HTTP | Vous permet de découvrir rapidement les HTTP méthodes sont utilisées pour demander vos données. En général, les méthodes de demande HTTP les plus courantes sont GET, HEAD et POST. Un graphique indique le pourcentage d’accès qui s’est produite pour chaque type de méthode de demande HTTP.
URL | Contient un graphique qui affiche les URL demandées 10 supérieure. Une barre s’affiche pour chaque URL. La hauteur de la barre indique le nombre d’accès URL particulière a généré sur la période couverte par le rapport. Statistiques pour les 100 premiers demandé que les URL s’affichent directement en dessous de ce graphique.
Enregistrements CNAME | Contient un graphique qui affiche les 10 enregistrements de CNAME permet de demander des ressources en fonction du temps qui s’étendent d’un rapport de haut. Statistiques pour les 100 premiers demandé les qu'enregistrements CNAME s’affichent directement en dessous de ce graphique.
Origines | Contient un graphique qui affiche le CDN 10 principaux ou les serveurs d’origine de client à partir de laquelle les actifs ont été demandées sur une période donnée de temps. Statistiques pour les 100 premiers demandé CDN ou client serveurs d’origine sont affichent directement en dessous de ce graphique. Serveurs d’origine client sont identifiés par le nom défini dans l’option de nom de répertoire.
Geo POP | Affiche la quantité du trafic est routé via une particulier point de présence (POP). L’abréviation à trois lettres représente un POP dans notre réseau CDN.
Clients | Contient un graphique qui affiche les 10 clients qui a demandé les ressources sur une période spécifiée. Aux fins de ce rapport, toutes les demandes qui proviennent de la même adresse IP sont considérés comme provenant du même client. Statistiques pour les 100 clients s’affichent directement en dessous de ce graphique. Ce rapport est utile pour déterminer les modèles d’activité de téléchargement pour les clients de haut.
Statuts de cache | Donne une description détaillée du comportement de cache, qui peut révéler des approches permettant d’améliorer l’expérience globale de l’utilisateur final. Dans la mesure où les meilleures performances proviennent de présences dans le cache, vous pouvez optimiser les vitesses de livraison de données en réduisant échecs dans le cache et de présences dans le cache a expiré.
Aucun détail | Contient un graphique qui affiche les 10 URL pour les immobilisations pour lesquelles fraîcheur de contenu du cache a été désactivée sur une période donnée de temps supérieure. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails de la CONFIG_NOCACHE | Contient un graphique qui affiche les 10 principales URL pour les immobilisations qui n’ont pas mis en cache en raison de la configuration du client CDN. Ces types d’actifs ont été pris en charge par le serveur d’origine. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails UNCACHEABLE | Contient un graphique qui affiche les 10 URL principaux pour les immobilisations qui n’a pas mis en cache car des données d’en-tête de demande. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails de la TCP_HIT | Contient un graphique qui affiche les 10 URL supérieur pour les actifs qui sont pris en charge immédiatement à partir du cache. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails de la TCP_MISS | Contient un graphique qui affiche les 10 URL supérieur pour les actifs qui ont un statut de cache de TCP_MISS. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails de la TCP_EXPIRED_HIT | Contient un graphique qui affiche les 10 URL supérieur pour les immobilisations obsolètes ont été pris en charge directement à partir de la fenêtre contextuelle. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails de la TCP_EXPIRED_MISS | Contient un graphique qui affiche les 10 URL supérieur pour les immobilisations obsolètes pour lequel une nouvelle version devait être récupérés à partir du serveur d’origine. Statistiques pour les URL top 100 pour ces types d’actifs s’affichent directement en dessous de ce graphique.
Détails de la TCP_CLIENT_REFRESH_MISS | Affiche un graphique à barres qui affiche les 10 principales URL pour les actifs ont été extraites à partir d’un serveur d’origine suite à une demande non-cache à partir du client. Statistiques pour les URL top 100 pour ces types de demandes s’affichent directement en dessous de ce graphique.
Types de demande client | Indique le type de requêtes qui ont été effectuées par les clients HTTP (par exemple, les navigateurs). Ce rapport comprend un graphique qui fournit une idée sur la façon dont les demandes sont traitées. La bande passante et le trafic des informations pour chaque type de requête sont affichées sous le graphique.
Agent utilisateur | Affiche un graphique à barres affichant les agents 10 utilisateur supérieure pour demander votre contenu par le biais de notre CDN. En général, un agent utilisateur est un navigateur de téléphone portable, lecteur multimédia ou un navigateur web. Statistiques pour les agents 100 utilisateur supérieur s’affichent directement en dessous de ce graphique.
Points d’accès | Affiche un graphique à barres affichant les principaux 10 référents au contenu accédé par le biais de notre CDN. En général, un point d’accès est l’URL de la page web ou d’une ressource qui est lié à votre contenu. Des informations détaillées sont fournies sous le graphique pour les références de 100 premiers.
Types de compression | Contient un graphique qui répartit les ressources demandées par si elles ont été compressées par nos serveurs edge. Le pourcentage des actifs compressés est ventilé d’après le type de compression utilisé. Des informations détaillées sont fournies sous le graphique pour chaque type de compression et l’état.
Types de fichiers | Affiche un graphique à barres qui affiche les types de 10 fichier principaux qui ont été demandées par le biais de notre CDN pour votre compte. Pour les besoins de cet état, un type de fichier est défini par l’extension de nom de fichier de l’actif et le type de support Internet (.html, par exemple, \[texte/html\], .htm \[texte/html\], .aspx \[texte/html\], etc..). Des informations détaillées sont fournies sous le graphique des types de fichiers de 100 supérieur.
Fichiers uniques | Affiche un graphique représentant le nombre total de ressources uniques qui ont été demandées à un jour donné sur une période donnée de temps.
Résumé de l’authentification des jetons | Contient un graphique en secteurs qui fournit une vue d’ensemble rapide sur non demandées étaient protégées par l’authentification basée sur le jeton. Actifs protégés sont affichés dans le graphique en fonction des résultats de leur authentification a été lancée.
Jeton Auth refuser les détails | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont été refusées en raison de l’authentification basée sur un jeton.
Codes de réponse HTTP | Fournit une liste des codes d’état HTTP (par exemple, 200 OK, 403 interdit, 404 non trouvé, etc.) qui ont été livrés à vos clients HTTP par nos serveurs edge. Un graphique en secteurs vous permet de rapidement évaluer comment vos ressources ont été pris en charge. Les données statistiques détaillées sont fournies pour chaque code de réponse sous le graphique.
404 erreurs | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont abouti à un code de réponse 404 introuvable.
403 erreurs | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont abouti à un code de réponse 403 Interdit. Un code de réponse 403 Interdit se produit lorsqu’une demande est refusée par le serveur d’origine un client ou un serveur de transport edge sur notre POP.
Erreurs de 4xx | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont abouti à un code de réponse dans la plage de 400. Exclus de ce rapport sont 403 introuvable et codes de réponse 404 interdit. En général, un code de réponse 4xx se produit lorsqu’une demande est refusée suite à une erreur de client.
Erreurs de 504 | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont abouti à un code de réponse 504 délai de la passerelle. Un code de réponse 504 délai de la passerelle se produit lorsqu’un délai d’attente se produit lorsqu’un proxy HTTP tente de communiquer avec un autre serveur. Dans le cas de notre CDN, un code de réponse 504 délai de la passerelle se produit généralement lorsqu’un serveur de transport edge ne peut pas établir de communication avec un serveur client d’origine.
502 erreurs | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont abouti à un code de réponse 502 Passerelle incorrecte. Un code de réponse 502 Passerelle incorrecte se produit en cas d’échec du protocole HTTP entre un serveur et un proxy HTTP. Dans le cas de notre CDN, un code de réponse 502 Passerelle incorrecte se produit généralement lorsque le serveur d’origine un client renvoie une réponse non valide à un serveur de transport edge. Une réponse n’est pas valide si elle ne peut pas être analysée ou si elle est incomplète.
Erreurs de 5xx | Affiche un graphique à barres qui permet d’afficher les top 10 requêtes qui ont abouti à un code de réponse dans la plage 500.  Exclus de ce rapport sont 502 Passerelle incorrecte et codes de réponse 504 délai de la passerelle.

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble d’Azure CDN](cdn-overview.md)
* [Statistiques en temps réel dans Microsoft Azure CDN](cdn-real-time-stats.md)
* [Le comportement par défaut HTTP à l’aide du moteur de règles](cdn-rules-engine.md)
* [Rapports HTTP avancée](cdn-advanced-http-reports.md)
