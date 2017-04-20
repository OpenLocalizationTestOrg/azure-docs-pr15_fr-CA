<properties
    pageTitle="Azure CDN HTTP rapports avancés | Microsoft Azure"
    description="États HTTP avancés dans Microsoft Azure CDN. Ces rapports fournissent des informations détaillées sur l’activité CDN."
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

# <a name="advanced-http-reports-in-microsoft-azure-cdn"></a>États HTTP avancés dans Microsoft Azure CDN

## <a name="overview"></a>Vue d’ensemble

Ce document explique HTTP génération de rapports avancée dans Microsoft Azure CDN. Ces rapports fournissent des informations détaillées sur l’activité CDN.

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>L’accès aux rapports HTTP avancée

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Survolez l’onglet **Analytique** , puis pointez sur le Lanceur de **Rapports avancés de HTTP** .  Cliquez sur la **plate-forme de grande taille de HTTP**.

    ![Portail de gestion CDN - menu de rapports avancés](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)

    Options de rapport sont affichées.

## <a name="geography-reports-map-based"></a>Rapports de géographie (basées sur le plan)

Il existe cinq rapports qui tirent parti d’un plan pour indiquer les régions à partir de laquelle le contenu est demandé. Ces rapports sont les World Map, carte des États-Unis, Canada Map, carte d’Europe et Asie Pacifique Map.

Chaque rapport carte rangs géographique entités (par exemple, pays, États et provinces) en fonction du pourcentage d’accès provenant de cette région. En outre, une carte est fournie pour vous aider à visualiser les emplacements à partir de laquelle le contenu est demandé. Il est en mesure de le faire par un codage par couleur à chaque région en fonction de la quantité de la demande, l’expérience de cette région. Plus claire les zones ombrées indiquent réduire la demande de votre contenu, alors que les régions foncées indiquent les niveaux supérieurs de la demande pour votre contenu.

Informations détaillées sur le trafic et la bande passante pour chaque région sont fournies directement en dessous de la carte. Ainsi, vous permet d’afficher le nombre total de visites, le pourcentage de succès, le volume total de données transférées (en Go) et le pourcentage de données transférés pour chaque région. Permet d’afficher une description de chacun de ces mesures. Enfin, lorsque vous placez le curseur sur une région (par exemple, pays, état ou province), le nom et le pourcentage de visites qui ont eu lieu dans la région s’afficheront sous la forme d’une info-bulle.

Pour chaque type de rapport de géographie de cartes, une brève description est fournie ci-dessous.

Nom de l’état | Description
------------|------------
Carte du monde | Ce rapport vous permet d’afficher la demande dans le monde entier pour votre contenu CDN. Chaque pays est un code de couleur sur la carte du monde pour indiquer le pourcentage d’accès provenant de cette région.
Carte des États-Unis | Ce rapport vous permet d’afficher la demande de votre contenu CDN aux États-Unis. Chaque état est codé en couleur sur cette carte pour indiquer le pourcentage d’accès provenant de cette région.
Mappage du Canada | Ce rapport vous permet d’afficher la demande de votre contenu CDN au Canada. Chaque province est codé en couleur sur cette carte pour indiquer le pourcentage d’accès provenant de cette région.
Carte d’Europe | Ce rapport vous permet d’afficher la demande de votre contenu CDN en Europe. Chaque pays sont codé en couleur sur cette carte pour indiquer le pourcentage d’accès provenant de cette région.
Mappage d’Asie Pacifique | Ce rapport vous permet d’afficher la demande de votre contenu CDN en Asie. Chaque pays sont codé en couleur sur cette carte pour indiquer le pourcentage d’accès provenant de cette région.

## <a name="geography-reports-bar-charts"></a>Rapports de géographie (graphiques à barres)

Il existe deux autres rapports qui fournissent des informations statistiques en fonction de la géographie, villes du haut et haut pays. Ces rapports classement villes et pays, respectivement, le nombre de visites provenant de ces régions. Lors de la génération de ce type de rapport, un graphique à barres indique le top 10 villes ou pays qui a demandé le contenu sur une plate-forme spécifique. Ce graphique à barres vous permet d’évaluer rapidement les régions qui génèrent le plus grand nombre de demandes de votre contenu.

Le côté gauche de (l’axe y) du graphique indique le nombre d’accès s’est produite dans la région spécifiée. Directement sous le graphique (axe x), vous trouverez une étiquette pour chacune des 10 principales régions.

### <a name="using-the-bar-charts"></a>En utilisant les graphiques en barres

* Si vous placez le curseur sur une barre, le nom et le nombre total de visites qui ont eu lieu dans la région s’afficheront sous la forme d’une info-bulle.
* L’info-bulle pour le rapport des villes du haut identifie une ville par son nom, le département et l’abréviation du pays.
* Si la ville ou la région (par exemple, état/province) d'où provenance une demande n’a pas pu être déterminée, puis il indique qu’elles sont inconnues. Si le pays est inconnu, puis deux points d’interrogation (c'est-à-dire ??), s’affiche.
* Un rapport peut inclure des mesures pour « Europe » ou la « région Asie/Pacifique ». Ces éléments ne sont pas destinés à fournir des informations statistiques sur toutes les adresses IP de ces régions. Au lieu de cela, ils s’appliquent uniquement aux demandes qui proviennent d’adresses IP qui sont répartis sur l’Europe ou en Asie/Pacifique au lieu de sur une ville spécifique ou un pays.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez le nombre total de visites, le pourcentage de succès, la quantité de données transférées (en Go) et le pourcentage de données transférées pour les régions haut 250. Permet d’afficher une description de chacun de ces mesures.

Une brève description est fournie pour les deux types de rapports ci-dessous.

Nom de l’état | Description
------------|------------
Villes du haut | Ce rapport évalue les villes en fonction du nombre de visites provenant de cette région.
Principaux pays | Ce rapport évalue en fonction du nombre de visites provenant des pays de cette région.

## <a name="daily-summary"></a>Résumé quotidien

Le rapport résumé quotidien vous permet d’afficher le nombre total d’accès et les données transférées sur une plate-forme particulière sur une base quotidienne. Ces informations peuvent être utilisées pour déterminer rapidement les modèles d’activité CDN. Par exemple, ce rapport peut vous aider à détecter les jours expérimentés supérieur ou inférieurs au trafic attendus.

Lors de la génération de ce type de rapport, un graphique à barres fournira une indication visuelle sur la quantité de la demande de spécifique à la plate-forme expérimentée sur une base quotidienne pendant la période couverte par le rapport. Elle le fera en affichant une barre pour chaque jour dans le rapport. Par exemple, la sélection de la période de temps appelée « Semaine dernière » génère un graphique à barres avec des barres de sept. Chaque barre indique le nombre total d’accès expérimentés à ce jour.

Le côté gauche de (l’axe y) du graphique indique le nombre d’accès s’est produite à la date spécifiée. Directement sous le graphique (axe x), vous trouverez une étiquette qui indique la date (Format : AAAA-MM-JJ) pour chaque jour à inclure dans l’état.

> [AZURE.TIP] Si vous placez le curseur sur une barre, le nombre total de visites qui ont eu lieu à cette date s’affichera sous la forme d’une info-bulle.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez le nombre total de visites et de la quantité de données transférées (en Go) pour chaque jour couverte par le rapport.

## <a name="by-hour"></a>Par heure

Le rapport par heure vous permet d’afficher le nombre total d’accès et les données transférées sur une plate-forme particulière sur une base horaire. Ces informations peuvent être utilisées pour déterminer rapidement les modèles d’activité CDN. Par exemple, ce rapport peut vous aider à détecter les périodes de temps au cours de la journée qui rencontrent plus ou moins de trafic attendus.

Lors de la génération de ce type de rapport, un graphique à barres fournira une indication visuelle sur la quantité de demande spécifique à la plate-forme rencontrée sur une base horaire pendant la période couverte par le rapport. Elle le fera en affichant une barre pour chaque heure couverte par le rapport. Par exemple, sélectionner un 24 heures période génère un graphique à barres avec des barres de vingt quatre. Chaque barre indique le nombre total d’accès rencontrés au cours de cette heure.

Le côté gauche de (l’axe y) du graphique indique le nombre d’accès s’est produite à l’heure spécifiée. Directement sous le graphique (axe x), vous trouverez une étiquette qui indique la date et l’heure (Format : AAAA-MM-JJ HH : mm) pour toutes les heures incluses dans le rapport. Le temps est déclaré à l’aide du format de 24 heures, et il est spécifié en utilisant le fuseau horaire UTC/GMT.

> [AZURE.TIP] Si vous placez le curseur sur une barre, le nombre total d’accès qui s’est produite au cours de cette heure s’affichera sous la forme d’une info-bulle.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez le nombre total de visites et de la quantité de données transférées (en Go) pour chaque heure couverte par le rapport.

## <a name="by-file"></a>Par fichier

Le rapport par fichier vous permet de vous permet d’afficher le montant de la demande et le trafic effectuées sur une plate-forme particulière pour les immobilisations plus demandées. Lors de la génération de ce type de rapport, un graphique à barres est généré sur les actifs les plus demandées 10 supérieure pendant la période spécifiée.

> [AZURE.NOTE] Pour les besoins de ce rapport, bord CNAME URL sont converties en leurs URL CDN équivalente. Permet d’obtenir un décompte précis du nombre total d’accès associé à une immobilisation, quel que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche de (l’axe y) du graphique indique le nombre de demandes pour chaque immobilisation pendant la période spécifiée. Directement sous le graphique (axe x), vous trouverez une étiquette qui indique le nom du fichier pour chacun des 10 actifs demandés supérieur.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez les informations suivantes pour chacune des principales ressources demandées 250 : chemin d’accès relatif, le nombre total de visites, le pourcentage de succès, la quantité de données transférées (en Go) et le pourcentage de données transférées.

## <a name="by-file-detail"></a>Par détails de fichier

Le rapport en détail de fichier vous permet de vous permet d’afficher le montant de la demande et le trafic effectuées sur une plate-forme particulière d’un actif spécifique. Tout en haut de ce rapport est l’option Détails de fichier de. Cette option fournit une liste de vos ressources les plus demandées sur la plate-forme sélectionnée. Pour générer un rapport en détail de fichier, vous devez sélectionnez l’immobilisation de votre choix à partir de l’option Détails de fichier de. Après quoi, un graphique à barres indique la quantité de demande quotidienne qui il généré pendant la période spécifiée.

Le côté gauche de (l’axe y) du graphique indique le nombre total de demandes qu’un actif a rencontré un jour donné. Directement sous le graphique (axe x), vous trouverez une étiquette qui indique la date (Format : AAAA-MM-JJ) pour le Canada à la demande pour l’immobilisation a été signalée.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez le nombre total de visites et de la quantité de données transférées (en Go) pour chaque jour couverte par le rapport.

## <a name="by-file-type"></a>Par Type de fichier

Le rapport par Type de fichier vous permet d’afficher le montant de la demande et le trafic supportés par type de fichier. Lors de la génération de ce type de rapport, un graphique indique le pourcentage de visites générées par les types de fichiers de 10 supérieure.

> [AZURE.TIP] Si vous pointez sur une tranche dans le graphique, type de média Internet de que type de fichier s’affiche sous la forme d’une info-bulle.

Les données qui a été utilisées pour générer le graphique peuvent être affichées en dessous. Vous y trouverez le type de média Internet / l’extension de fichier nom, le nombre total de visites, d’accès, le pourcentage de la quantité de données transférées (en Go) et le pourcentage de données transférées pour chacun des types de fichiers haut 250.

## <a name="by-directory"></a>Par répertoire

Le rapport par répertoire vous permet permet d’afficher le montant de la demande et le trafic effectuées sur une plate-forme particulière pour le contenu d’un répertoire spécifique. Lors de la génération de ce type de rapport, un graphique à barres indique le nombre total d’accès générés par contenu dans 10 répertoires principaux.

### <a name="using-the-bar-chart"></a>L’utilisation du graphique à barres

* Placez le curseur sur une barre pour afficher le chemin d’accès relatif au répertoire correspondant.
* Le contenu stocké dans un sous-dossier d’un répertoire ne compte pas lors du calcul de la demande par l’annuaire. Ce calcul se base uniquement sur le nombre de demandes générées pour le contenu stocké dans le répertoire réel.
* Pour les besoins de ce rapport, bord CNAME URL sont converties en leurs URL CDN équivalente. Permet d’obtenir un décompte précis pour toutes les statistiques associées à une immobilisation, quel que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche de (l’axe y) du graphique indique le nombre total de demandes pour le contenu stocké dans vos répertoires de top 10. Chaque barre du graphique représente un répertoire. Utiliser le schéma de codage de couleur pour faire correspondre une barre dans un répertoire listé dans la section répertoires de haut 250 complet.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez les informations suivantes pour chacun des répertoires haut 250 : chemin d’accès relatif, le nombre total de visites, le pourcentage de succès, la quantité de données transférées (en Go) et le pourcentage de données transférées.

## <a name="by-browser"></a>Par navigateur

Le rapport par navigateur vous permet d’afficher les navigateurs ont été utilisés pour demander du contenu. Lors de la génération de ce type de rapport, un graphique en secteurs indique le pourcentage de demandes traitées par les 10 navigateurs.

### <a name="using-the-pie-chart"></a>À l’aide du graphique à secteurs

* Pointez sur une tranche dans le graphique à secteurs pour afficher le nom et la version d’un navigateur.
* Pour les besoins de cet état, chaque combinaison unique/version du navigateur est considéré comme un autre navigateur.
* La tranche appelée « Autre » indique le pourcentage de demandes traitées par tous les autres navigateurs et versions.

Les données qui a été utilisées pour générer le graphique à secteurs peuvent être affichées au-dessous de lui. Vous y trouverez le numéro de version de type navigateur, le nombre total d’accès et le pourcentage de succès pour chacun des navigateurs haut 250.

## <a name="by-referrer"></a>Par point d’accès

Le rapport par point d’accès vous permet d’afficher les principales références à du contenu sur la plate-forme sélectionnée. Un point d’accès indique le nom d’hôte à partir duquel une demande a été générée. Lors de la génération de ce type de rapport, un graphique à barres indique la quantité de la demande (c'est-à-dire accès) générée par les principaux référents de 10.

Le côté gauche de (l’axe y) du graphique indique le nombre total de demandes qu’un actif a rencontré pour chaque point d’accès. Chaque barre du graphique représente un point d’accès. Utiliser le schéma de codage de couleur pour faire correspondre une barre à un point d’accès répertorié dans la section de point d’accès haut 250.

Les données qui a été utilisées pour générer le graphique à barres peuvent être affichées au-dessous de lui. Vous y trouverez l’URL, le nombre total d’accès et le pourcentage de visites générées à partir de chacune des principaux référents de 250.

## <a name="by-download"></a>Par téléchargement

Le rapport à télécharger vous permet d’analyser des modèles de téléchargement pour votre contenu plus demandé. La partie supérieure du rapport contient un graphique à barres que compare a tenté de téléchargements avec les téléchargements terminés pour top 10 actifs demandées. Chaque barre est de couleur selon qu’elle est un tentative de téléchargement (bleu) ou un téléchargement terminé (vert).

> [AZURE.NOTE] Pour les besoins de ce rapport, bord CNAME URL sont converties en leurs URL CDN équivalente. Permet d’obtenir un décompte précis pour toutes les statistiques associées à une immobilisation, quel que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche de (l’axe y) du graphique indique le nom de fichier pour chacun des 10 actifs demandés supérieur. Directement sous le graphique (axe x), vous trouverez des étiquettes qui indiquent le nombre total de téléchargements de tentée/terminé.

Directement sous le graphique à barres, les informations suivantes apparaît pour les capitaux demandées 250 supérieur : chemin d’accès relatif (y compris le nom de fichier), le nombre de fois où il a été téléchargé à la fin, le nombre de fois où il a été demandé et le pourcentage de requêtes qui ont abouti à un téléchargement complet.

> [AZURE.TIP] Notre CDN n’est pas informé par un client HTTP (c'est-à-dire le navigateur) lorsqu’une immobilisation a été téléchargée. Par conséquent, nous devons calculer si une immobilisation a été complètement téléchargée en fonction des codes d’état et de la plage d’octets demandes. La première chose que nous serons pour ce calcul est que la demande entraîne un code d’état OK 200. Si tel est le cas, puis nous examinons les demandes de plage d’octets pour s’assurer qu’ils couvrent l’ensemble actif. Enfin, nous comparer le volume de données transférés à la taille de la ressource demandée. Si les données transférées sont égale ou supérieure à la taille du fichier et les demandes de plage d’octets sont appropriées pour cet actif, l’accès sera comptée comme un téléchargement complet.
>
>En raison de la nature a de ce rapport, vous gardez à l’esprit les points suivants qui peuvent modifier la cohérence et la précision de ce rapport.
>
>* Modèles de trafic ne peuvent pas être prédites avec précision lorsque les agents utilisateurs se comportent différemment. Cela peut produire des résultats de téléchargement terminé qui sont supérieures à 100 %.
>* Actifs qui tirent parti du téléchargement progressif de HTTP ne peuvent pas être représentés avec précision par ce rapport. Cela est dû aux utilisateurs cherchant à des positions différentes dans une vidéo.

## <a name="by-404-errors"></a>Par 404 erreurs

L’état par les erreurs 404 vous permet d’identifier le type de contenu qui génère le plus grand nombre de codes d’état 404 introuvable. La partie supérieure du rapport contient un graphique à barres pour les immobilisations de top 10 pour lequel un code d’état 404 introuvable a été retourné. Ce graphique à barres compare le nombre total de demandes de requêtes qui ont abouti à un code statut 404 introuvable pour ces actifs. Chaque barre est codé par couleur. Une barre jaune est utilisée pour indiquer que la demande a généré un code d’état 404 introuvable. Une barre rouge est utilisée pour indiquer le nombre total de demandes pour l’immobilisation.

> [AZURE.NOTE] Pour les besoins de ce rapport, notez les points suivants :
>
>* Un accès représente une demande pour un actif, quel que soit le code d’état.
>* Bord CNAME URL sont convertis en leurs URL CDN équivalente. Permet d’obtenir un décompte précis pour toutes les statistiques associées à une immobilisation, quel que soit le CDN ou le bord URL CNAME utilisé pour le demander.

Le côté gauche de (l’axe y) du graphique indique le nom de fichier pour chacune des ressources demandées 10 supérieure qui a généré un code d’état 404 introuvable. Directement sous le graphique (axe x), vous trouverez les étiquettes qui indiquent le nombre total de demandes et le nombre de demandes qui ont abouti à un code d’état 404 introuvable.

Directement sous le graphique à barres, les informations suivantes apparaît pour les capitaux demandées 250 supérieur : chemin d’accès relatif (y compris le nom de fichier), le nombre de demandes qui ont abouti à un code d’état 404 introuvable, le nombre total de fois où l’actif a été demandé et le pourcentage de requêtes qui ont abouti à un code d’état 404 introuvable.

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble d’Azure CDN](cdn-overview.md)
* [Statistiques en temps réel dans Microsoft Azure CDN](cdn-real-time-stats.md)
* [Le comportement par défaut HTTP à l’aide du moteur de règles](cdn-rules-engine.md)
* [Analyser les performances de bord](cdn-edge-performance.md)
