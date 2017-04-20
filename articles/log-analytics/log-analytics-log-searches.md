<properties
    pageTitle="Journal des recherches dans journal Analytique | Microsoft Azure"
    description="Recherches de journaux vous permettent de combiner et de corréler les données de n’importe quel ordinateur à partir de plusieurs sources dans votre environnement."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="log-searches-in-log-analytics"></a>Recherche de journal de journal Analytique

Au cœur de journal Analytique est la fonction de recherche de journal qui vous permet de combiner et de corréler les données de n’importe quel ordinateur à partir de plusieurs sources dans votre environnement. Les solutions sont également alimentées par journal de recherche afin de vous apporter les mesures de pivoter autour d’une zone de problème particulier.

Sur la page de recherche, vous pouvez créer une requête et ensuite lorsque vous effectuez une recherche, vous pouvez filtrer les résultats en utilisant des contrôles de la facette. Vous pouvez également créer des requêtes avancées pour la transformation, de filtre et de rapport sur les résultats.

Journal des recherches fréquemment utilisées apparaissent sur la plupart des pages de la solution. Tout au long de la console de l’OMS, vous pouvez cliquez sur mosaïque ou extraire à d’autres éléments pour afficher plus d’informations sur l’élément à l’aide de recherche des journaux.

Dans ce didacticiel, nous allons les exemples pour couvrir toutes les notions de base lorsque vous utilisez la recherche dans un journal.

Nous allons commencer avec des exemples simples et pratiques et puis leur développement afin que vous pouvez obtenir une compréhension pratique des cas d’usage sur la façon d’utiliser la syntaxe pour extraire les analyses que vous souhaitez à partir des données.

Une fois que vous avez familiarisé avec les techniques de recherche, vous pouvez consulter le [journal Analytique consigner la référence de recherche](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Utiliser des filtres de base

La première chose à savoir est que la première partie d’une recherche de requête, avant tout « | » caractère barre verticale est toujours un *filtre*. Vous pouvez la considérer comme une clause WHERE dans TSQL--il détermine *le* sous-ensemble de données d’extraire du magasin de données OMS. Recherche dans le magasin de données est en grande partie sur la spécification des caractéristiques des données que vous souhaitez extraire, il est naturel qu’une requête devrait démarrer avec la clause WHERE.

Les filtres de base que vous pouvez utiliser sont les *mots clés*, tels que 'erreur' ou 'timeout' ou un nom d’ordinateur. Ces types de requêtes simples retournent généralement des diverses formes de données au sein du même jeu de résultats. C’est parce que le journal Analytique a différents *types* de données dans le système.


### <a name="to-conduct-a-simple-search"></a>Pour effectuer une recherche simple
1. Dans le portail de l’OMS, cliquez sur **Recherche**.  
    ![mosaïque de recherche](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. Dans le champ de requête, tapez `error` , puis cliquez sur **Rechercher**.  
    ![Erreur de recherche](./media/log-analytics-log-searches/oms-search-error.png)  
    Par exemple, la requête de `error` dans l’image suivante a renvoyé les enregistrements **d’événement** 100 000 (collectées par la gestion des journaux), 18 **ConfigurationAlert** enregistrements (générées par l’évaluation de la Configuration) et 12 **ConfigurationChange** (capturé par le suivi des modifications).   
    ![résultats de la recherche](./media/log-analytics-log-searches/oms-search-results01.png)  

Ces filtres ne sont pas vraiment des classes/types d’objet. *Le type* est simplement une balise, une propriété ou une chaîne/nom/catégorie, qui est associée à un élément de données. Certains documents dans le système sont marqués en tant que **Type : ConfigurationAlert** , et certains sont balisés en tant que **Type : Perf**ou **Type : événements**et ainsi de suite. Chaque résultat de recherche, un document, un enregistrement ou une entrée affiche toutes les propriétés brutes et leurs valeurs pour chacun de ces éléments de données, et vous pouvez utiliser ces noms de champ permet de spécifier dans le filtre lorsque vous souhaitez récupérer uniquement les enregistrements dont le champ a qui valeur donnée.

*Le type* est simplement un champ ayant tous les enregistrements, il n’est pas différent de tout autre champ. Il a été établi en fonction de la valeur du champ Type. Enregistrement qu’une autre forme ou. Incidemment, **Type = Perf**, ou **Type = événement** est également la syntaxe que vous avez besoin interroger les données de performance ou les événements.

Vous pouvez utiliser soit un signe des deux-points ( :) ou un signe égal (=) après le nom du champ et avant la valeur. **Type : événements** et **Type = événement** sont une signification équivalente, vous pouvez choisir le style que vous préférez.

Ainsi, si le Type = Perf enregistrements ont un champ intitulé « CounterName », puis vous pouvez écrire une requête qui ressemble à `Type=Perf CounterName="% Processor Time"`.

Vous obtiendrez uniquement les données de performance dans laquelle le nom de compteur de performance est « % temps processeur ».

### <a name="to-search-for-processor-time-performance-data"></a>Pour rechercher des données de performance de temps processeur
- Dans le champ de requête de recherche, tapez`Type=Perf CounterName="% Processor Time"`

Vous pouvez aussi être plus spécifique et utiliser **InstanceName = _ 'Total'** dans la requête, qui est un compteur de performance Windows. Vous pouvez également sélectionner une facette et une autre **: valeur du champ**. Le filtre est ajouté automatiquement à votre filtre dans la barre de la requête. Vous pouvez le voir dans l’image suivante. Il vous indique où cliquer pour ajouter **InstanceName : '_Total'** à la requête sans rien taper.

![facette de la recherche](./media/log-analytics-log-searches/oms-search-facet.png)

Votre requête est alors`Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

Dans cet exemple, vous n’êtes pas obligé de spécifier **Type = Perf** pour obtenir ce résultat. Étant donné que les champs CounterName et InstanceName existent uniquement pour les enregistrements de Type = Perf, la requête est suffisamment spécifique pour retourner les mêmes résultats que le plus long, précédent :
```
CounterName="% Processor Time" InstanceName="_Total"
```

C’est pourquoi tous les filtres dans la requête sont évalués comme étant dans *et* entre eux. Effectivement, plus de champs que vous ajoutez les critères, vous obtenez inférieure, des résultats plus spécifiques et raffinées.

Par exemple, la requête `Type=Event EventLog="Windows PowerShell"` est identique à `Type=Event AND EventLog="Windows PowerShell"`. Il retourne tous les événements qui ont été enregistrés dans et recueillies dans le journal des événements de Windows PowerShell. Si vous ajoutez un filtre plusieurs fois en sélectionnant plusieurs fois de la facette même, alors le problème est purement cosmétique--il peut encombrer la barre de recherche, mais elle renvoie toujours le même résultat, car l’opérateur AND implicite est toujours.

Vous pouvez facilement inverser l’opérateur et implicite à l’aide d’un opérateur n’est pas explicitement. Par exemple :

`Type:Event NOT(EventLog:"Windows PowerShell")`ou son équivalent `Type=Event EventLog!="Windows PowerShell"` renvoyer tous les événements de tous les autres journaux qui ne sont pas le journal de Windows PowerShell.

Vous pouvez également utiliser comme autre opérateur booléen 'OR'. La requête suivante renvoie les enregistrements pour lesquels le journal des événements sont un système ou d’Application.

```
EventLog=Application OR EventLog=System
```

À l’aide de la requête ci-dessus, vous obtiendrez les entrées pour les journaux dans le même jeu de résultats.

Cependant, si vous supprimez l’OR en laissant implicite et en place, puis la requête suivante ne produira aucun résultat, car il n’existe pas une entrée de journal des événements qui appartient à ces deux journaux. Chaque entrée de journal des événements a été écrit pour un seul des deux journaux.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Utilisation des filtres supplémentaires

La requête suivante retourne les entrées 2 des journaux d’événements pour tous les ordinateurs qui ont transmis les données.

```
EventLog=Application OR EventLog=System
```

![résultats de la recherche](./media/log-analytics-log-searches/oms-search-results03.png)

Sélection de l’un des champs ou des filtres pour rétrécir la requête à un ordinateur spécifique, à l’exclusion de tous les autres. La requête résultante aurait l’aspect suivant.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Qui est équivalente à la suivante, en raison de l’opérateur implicite AND.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Chaque requête est évaluée dans l’ordre explicite suivant. Notez la parenthèse.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Tout comme le champ journal des événements, vous pouvez récupérer des données uniquement pour un ensemble d’ordinateurs spécifiques en ajoutant ou. Par exemple :

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

De même, cela la requête suivante renvoie **% temps processeur** pour deux ordinateurs sélectionnés uniquement.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### <a name="boolean-operators"></a>Opérateurs booléens
Datetime et les champs numériques, vous pouvez rechercher les valeurs à l’aide de *supérieur*, *inférieur à*, et *inférieur ou égal*. Vous pouvez utiliser des opérateurs simples tels que >, <>, =, < =, ! =, dans la barre de recherche de requête.


Vous pouvez interroger un journal des événements spécifique pour une période spécifique. Par exemple, au cours des 24 heures est exprimée par l’expression suivante de mnémonique.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Pour effectuer une recherche à l’aide d’un opérateur booléen
- Dans le champ de requête de recherche, tapez`EventLog=System TimeGenerated>NOW-24HOURS"`  
    ![Rechercher avec une valeur booléenne](./media/log-analytics-log-searches/oms-search-boolean.png)

Bien que vous pouvez contrôler graphiquement l’intervalle de temps, et la plupart du temps vous pouvez pour ce faire, il existe des avantages, y compris un filtre de temps directement dans la requête. Par exemple, cela fonctionne très bien avec des tableaux de bord où vous pouvez substituer le temps pour chaque mosaïque, quel que soit le sélecteur de temps *global* sur la page de tableau de bord. Pour plus d’informations, consultez [Les questions de temps dans le tableau de bord](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Lors du filtrage par heure, gardez à l’esprit que vous obtiendrez des résultats à l' *intersection* des deux périodes : celle qui est spécifiée dans le portail de l’OMS (S1) et celle qui est spécifiée dans la requête (S2).

![intersection](./media/log-analytics-log-searches/oms-search-intersection.png)

Cela signifie que, si les périodes de temps ne se coupent pas, par exemple dans le portail de l’OMS où vous choisissez **cette semaine** et dans la requête, où vous pouvez définir **la semaine dernière**, il n’y a aucune intersection et vous ne recevez aucun résultat.

Opérateurs de comparaison utilisés pour le champ TimeGenerated sont également utiles dans d’autres situations. Par exemple, avec les champs de type numérique.

Par exemple, étant donné que les alertes de Configuration évaluation ont les valeurs de gravité suivants :

- 0 = les informations
- 1 = avertissement
- 2 = critique

Vous pouvez interroger des alertes d’avertissement et critiques et également exclure ceux d’information avec la requête suivante :

```
Type=ConfigurationAlert  Severity>=1
```


Vous pouvez également utiliser des requêtes de plage. Cela signifie que vous pouvez fournir la plage de début et la fin des valeurs d’une séquence. Par exemple, si vous souhaitez que les événements dans le journal des événements où l’ID d’événement est supérieur ou égal à 2100 mais inférieure ou égale à 2199 Operations Manager, puis la requête suivante renvoie les.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE] Vous devez utiliser la syntaxe de la plage est le séparateur de champ : valeur de deux-points ( :) et *pas* le signe égal (=). Placez l’extrémité inférieure et supérieure de la plage de crochets et séparés par deux points (.).

## <a name="manipulate-search-results"></a>Manipuler les résultats de la recherche

Lorsque vous recherchez des données, vous voudrez affiner votre requête de recherche et avoir un bon niveau de contrôle sur les résultats. Lorsque les résultats sont récupérés, vous pouvez appliquer des commandes permettant de les transformer.

Commandes de recherche de journal Analytique *doit* suivent après le caractère barre verticale (|). Un filtre doit toujours être la première partie d’une chaîne de requête. Il définit le jeu de données avec lequel vous travaillez et puis « pipes » des résultats dans une ligne de commande. Vous pouvez ensuite utiliser le canal pour ajouter des commandes supplémentaires. Cela ressemble vaguement au pipeline Windows PowerShell.

En général, la langue de recherche Analytique du journal tente de respecter les instructions pour le rendre similaire pour les professionnels de l’informatique et pour faciliter l’apprentissage et style de PowerShell.

Commandes portent des noms des verbes afin de savoir facilement ce qu’elles font.  

### <a name="sort"></a>Tri

La commande sort vous permet de définir l’ordre de tri par un ou plusieurs champs. Même si vous n’utilisez pas, par défaut, une fois l’ordre décroissant est appliquée. Les derniers résultats sont toujours en haut des résultats de recherche. Cela signifie que lorsque vous exécutez une recherche, avec `Type=Event EventID=1234` ce qui est réellement exécuté pour vous est :

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

C’est parce qu’il s’agit du type d’expérience que vous sont familiers dans les journaux. Par exemple, dans l’Observateur d’événements Windows.

Tri vous permet de modifier la façon dont les résultats sont renvoyés. Les exemples suivants montrent comment cela fonctionne.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


L’exemple simple ci-dessus montrent le fonctionnement des commandes--ils modifient la forme des résultats renvoyé par le filtre.

### <a name="limit-and-top"></a>Limite et haut
Une autre commande de moins connue est limité. Limite est un verbe de type PowerShell. Limite est fonctionnellement identique à la commande TOP. Les requêtes suivantes retournent les mêmes résultats.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Pour effectuer une recherche à l’aide de haut
- Dans le champ de requête de recherche, tapez`Type=Event EventID=600 | Top 1`   
    ![début de la recherche](./media/log-analytics-log-searches/oms-search-top.png)

Dans l’image ci-dessus, il existe des enregistrements de 358 mille avec EventID = 600. Les champs, les facettes et les filtres sur la gauche affichent toujours les informations sur les résultats retournés *par la partie du filtre* de la requête, qui est la partie avant de n’importe quel caractère. Le volet de **résultats** retourne uniquement le 1 résultat le plus récent, étant donné que l’exemple de commande en forme et transformer les résultats.

### <a name="select"></a>Sélectionnez

La commande SELECT se comporte comme Select-Object de PowerShell. Elle renvoie les résultats filtrés qui n’ont pas toutes leurs propriétés d’origine. Au lieu de cela, il sélectionne uniquement les propriétés que vous spécifiez.

#### <a name="to-run-a-search-using-the-select-command"></a>Pour exécuter une recherche à l’aide de la commande select

1. Dans Rechercher, tapez `Type=Event` , puis cliquez sur **Rechercher**.
2. Cliquez sur **+ Afficher plus** dans un des résultats pour afficher toutes les propriétés ayant les résultats.
3. Sélectionnez parmi ceux explicitement et la requête passe à `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![Sélectionnez de recherche](./media/log-analytics-log-searches/oms-search-select.png)

Il s’agit de commande particulièrement utile lorsque vous souhaitez contrôler le résultat de la recherche et choisir uniquement les parties des données qui importent vraiment pour votre exploration, qui n’est pas souvent de l’enregistrement complet. Cela est également utile lorsque les enregistrements de types différents ont *des* propriétés communes, mais pas *toutes* leurs propriétés sont communes. La barre d’outils, vous pouvez générer une sortie qui ressemble plus naturellement à une table, ou fonctionnent correctement lorsque exportés vers un fichier CSV et ensuite malaxés dans Excel.



## <a name="use-the-measure-command"></a>Utilisez la commande de mesure

MESURE est une des commandes dans les recherches de journal Analytique plus polyvalents. Il vous permet d’appliquer les *fonctions* de statistiques à vos données et les résultats de regroupement regroupés par un champ donné. Il existe plusieurs fonctions statistiques qui prend en charge de la mesure.

### <a name="measure-count"></a>Count() de mesure

La première fonction statistique permettent d’utiliser et de la plus simple à comprendre est la fonction *count()* .

Résultats à partir d’une requête de recherche, tels que `Type=Event`, afficher les filtres, également appelés des facettes sur le côté gauche des résultats de recherche. Les filtres de représenter une distribution des valeurs à un champ donné pour les résultats de la recherche effectuée.

![nombre de mesures de recherche](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Par exemple, dans l’image ci-dessus, vous verrez le champ **ordinateur** et elle montre que dans les événements presque 739 mille dans les résultats, il y a des valeurs uniques et distincts 68 pour le champ **ordinateur** dans ces enregistrements. La mosaïque affiche uniquement les 5 premiers, qui est les 5 valeurs courantes qui sont écrits dans les champs de **l’ordinateur** ), triés en fonction du nombre de documents qui contiennent la valeur de ce champ spécifique. Dans l’image, vous voyez que, parmi ces événements presque 369 mille – 90 mille proviennent de l’ordinateur OpsInsights04.contoso.com, des milliers de 83 à partir de l’ordinateur DB03.contoso.com et ainsi de suite.


Que se passe-t-il si vous souhaitez voir toutes les valeurs, étant donné que la mosaïque n'indique que seules les 5 premières ?

C’est ce que peut faire la commande de mesure avec la fonction count(). Cette fonction n’utilise pas tous les paramètres. Vous spécifiez simplement le champ par lequel vous voulez regrouper par – le champ **ordinateur** dans ce cas :

`Type=Event | Measure count() by Computer`

![nombre de mesures de recherche](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

Cependant, **l’ordinateur** est juste un champ utilisé *dans* chaque élément de données – il n’y a aucune base de données relationnelle concernés et aucun objet **ordinateur** distinct n’existe anywhere. Uniquement les valeurs *dans* les données peut décrire de quelle entité a généré les et un certain nombre de caractéristiques et aspects des données – d'où le terme *facette*. Cependant, vous pouvez également regrouper par d’autres champs. Étant donné que les résultats d’origine des événements presque 739 mille qui sont transmis à la commande de mesure disposent également d’un champ appelé **ID de l’événement**, vous pouvez appliquer la même technique pour regrouper par ce champ et d’obtenir un nombre d’événements par ID d’événement :

```
Type=Event | Measure count() by EventID
```

Si vous n’êtes pas intéressé par le nombre d’enregistrements réels qui contiennent une valeur spécifique, mais à la place si vous ne souhaitez qu’une liste des valeurs eux-mêmes, vous pouvez ajouter une commande *Select* à la fin de celle-ci et uniquement, sélectionnez la première colonne :

```
Type=Event | Measure count() by EventID | Select EventID
```

Vous pouvez obtenir plus complexes et les résultats de la requête avant le tri, ou vous pouvez simplement cliquer sur les colonnes dans la grille, trop.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>Pour effectuer une recherche à l’aide de la mesure nombre

- Dans le champ de requête de recherche, tapez`Type=Event | Measure count() by EventID`
- Ajouter `| Select EventID` à la fin de la requête.
- Enfin, ajoutez `| Sort EventID asc` à la fin de la requête.


Il existe quelques points importants à analyser et à mettre en évidence :

Tout d’abord, les résultats que vous voyez ne sont pas les résultats bruts d’origine plus. Au lieu de cela, ils sont des résultats agrégés – essentiellement les groupes de résultats. Ce n’est pas un problème, mais vous devez comprendre que vous interagissez avec une forme très différente des données qui est différent de la forme brute d’origine qui est créée à la volée lorsque la fonction d’agrégation et statistique.

En second lieu, **mesure nombre** actuellement renvoie uniquement les résultats distincts à 100 premiers. Cette limite ne s’applique pas aux autres fonctions de statistiques. Par conséquent, vous devez généralement d’utiliser d’abord un filtre plus précis pour rechercher des éléments spécifiques avant d’appliquer des mesures count().

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Utilisez les fonctions min et max avec la commande de mesure

Il existe plusieurs scénarios où la **Mesure Max()** et **Min() de mesure** sont utiles. Toutefois, étant donné que chaque fonction est opposée de l’autre, nous illustrerons Max() et vous pouvez expérimenter Min() vous-même.

Si vous recherchez des événements de sécurité, ils ont une propriété de **niveau** peut varier. Par exemple :

```
Type=SecurityEvent
```

![recherche mesure nombre Démarrer](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Si vous souhaitez afficher la valeur la plus élevée pour l’ensemble de la sécurité étant donnés un ordinateur commun, le champ Regrouper par, les événements que vous pouvez utiliser

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![Rechercher un ordinateur max mesure](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Il va afficher que pour les ordinateurs qui contenait des enregistrements de **niveau** , la plupart d'entre eux ont de niveau au moins 8, la plupart ont un niveau de 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Durée max. de mesure de recherche généré ordinateur](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Cette fonction fonctionne bien avec les numéros, mais elle fonctionne également avec les champs DateTime. Il est utile vérifier l’horodatage de dernière ou plus récente de tout type de données indexées pour chaque ordinateur. Par exemple : lorsque l’événement de sécurité la plus récente a été signalée pour chaque machine ?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Utilisez la fonction avg avec la commande de mesure

La fonction de statistiques de Avg() utilisée avec mesure vous permet de vous permet de calculer la valeur moyenne pour certains champs et regrouper des résultats par le champ du même ou d’autre. Cela est utile dans plusieurs cas, par exemple les données de performance.

Nous allons commencer avec des données de performances. Notez que OMS collecte actuellement les compteurs de performance pour les ordinateurs Windows et Linux.

Pour rechercher *toutes les* données de performances, la requête la plus basique est la suivante :

```
Type=Perf
```

![Recherche moyenne début](./media/log-analytics-log-searches/oms-search-avg01.png)

La première chose que vous remarquerez est que journal Analytique montre trois perspectives : liste, qui vous montre qui affiche les enregistrements réels derrière les graphiques ; Table, qui affiche une vue tabulaire des données de compteur de performance ; et mesures, qui affiche les graphiques pour les compteurs de performance.

Dans l’image ci-dessus, il existe deux jeux de champs marqués ce qui indiquent les éléments suivants :

- Le premier jeu identifie le nom de compteur de Performance Windows, nom de l’objet et nom de l’Instance dans le filtre de requête. Ce sont les champs que probablement plus couramment servira de facettes/filtres
- **Contre-valeur** est la valeur réelle du compteur. Dans cet exemple, la valeur est *de 75*.
- **TimeGenerated** est de 12:51, au format de 24 heures.

Voici une vue des mesures dans un graphique.

![Recherche moyenne début](./media/log-analytics-log-searches/oms-search-avg02.png)

Après la lecture sur la forme d’enregistrements de performances et avoir lu sur d’autres techniques de recherche, vous pouvez utiliser les mesure Avg() pour regrouper ce type de données numériques.

Voici un exemple simple :

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![Recherche moyenne (Avg) samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

Dans cet exemple, vous sélectionnez le temps Total du processeur compteur de performance et moyenne par ordinateur. Si vous souhaitez limiter les résultats à 6 dernières heures uniquement, vous pouvez utiliser le contrôle de filtre de temps ou spécifier dans votre requête comme suit :

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Pour effectuer une recherche à l’aide de la fonction avg avec la commande de mesure
- Dans la zone de requête de recherche, tapez `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.


Vous pouvez regrouper et mettre en corrélation des données *entre les* ordinateurs. Par exemple, imaginez que vous disposez d’un ensemble d’hôtes dans une sorte de batterie où chaque nœud est égal à l’autre une et ils simplement tous du même type de travail et doit être à peu près équilibrage de charge. Vous pouvez obtenir leurs compteurs en un seul rendez-vous par ce qui suit interroger ainsi qu’obtenir des moyennes pour la ferme entière. Vous pouvez démarrer en cliquant sur les ordinateurs avec l’exemple suivant :

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Maintenant que vous avez les ordinateurs, vous souhaitez également uniquement sélectionner deux indicateurs de performance clés (KPI) : utilisation de l’UC et % d’espace disque libre. Par conséquent, cette partie de la requête devient :

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Vous pouvez désormais ajouter des ordinateurs et des compteurs avec l’exemple suivant :

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Dans la mesure où vous disposez d’une sélection très spécifique, la commande de **mesure Avg()** peut renvoyer la moyenne pas par ordinateur, mais dans la batterie de serveurs, simplement en les regroupant par CounterName. Par exemple :

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Cela vous donne une vue compacte utile de quelques indicateurs de performance clés de votre environnement.

![regroupement d’avg recherche](./media/log-analytics-log-searches/oms-search-avg04.png)


Vous pouvez facilement utiliser la requête de recherche dans un tableau de bord. Par exemple, vous pourriez enregistrer la requête et créer un tableau de bord à partir de celui-ci nommé *KPI de batterie de serveurs Web*. Pour en savoir plus sur l’utilisation de tableaux de bord, voir [Création d’un tableau de bord personnalisé Analytique du journal](log-analytics-dashboards.md).

![tableau de bord de recherche moyenne](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Utilisez la fonction somme avec la commande de mesure

La fonction somme est similaire à d’autres fonctions de la commande de mesure. Vous pouvez voir un exemple sur la façon d’utiliser la fonction somme à la [Recherche de journaux IIS W3C dans des perspectives opérationnelles de Microsoft Azure](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Vous pouvez utiliser Max() et Min() avec les nombres, les dates et les chaînes de texte. Avec des chaînes de texte, elles sont triées par ordre alphabétique et vous obtenir des premier et dernier.

Toutefois, vous ne pouvez pas utiliser Sum() avec autre chose que des champs numériques. Cela s’applique également aux Avg().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Utilisez la fonction centile avec la commande de mesure

La fonction centile est similaire à la Avg() et Sum() dans la mesure où vous pouvez l’utiliser uniquement pour les champs numériques. Vous pouvez utiliser n’importe quel centile entre 1 et 99 dans un champ numérique. Vous pouvez également utiliser des commandes de **position** et de **pct** . Voici quelques exemples :  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Utilisez l’emplacement de commande

Le cas de commande fonctionne comme un filtre, mais elle peut être appliquée dans le pipeline pour filtrer davantage les résultats agrégés qui ont été produits par une commande de mesure – au lieu des résultats bruts qui sont filtrés au début d’une requête.

Par exemple :

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Vous pouvez ajouter un autre tuyau « | » caractère et la commande pour obtenir uniquement les ordinateurs dont les UC moyenne sont supérieure à 80 %, avec l’exemple suivant :

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Si vous êtes familiarisé avec Microsoft System Center - Operations Manager, vous pouvez considérer la commande where en termes de pack de gestion. S’il s’agissait d’une règle de l’exemple, la première partie de la requête est la source de données et la commande serait la détection de la condition.

Vous pouvez utiliser la requête sous forme de mosaïque dans **Mon tableau de bord**, en tant que moniteur de tris, à savoir quand les processeurs de l’ordinateur sont surexploitées. Pour plus d’informations sur les tableaux de bord, voir [Création d’un tableau de bord personnalisé Analytique du journal](log-analytics-dashboards.md). Vous pouvez également créer et utiliser des tableaux de bord à l’aide de l’application mobile. Pour plus d’informations, consultez [Application de Mobile OMS ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). Dans les mosaïques bas deux l’image suivante, vous pouvez voir le moniteur affiche une liste et sous la forme d’un nombre. Pour l’essentiel, vous souhaitez toujours le numéro zéro et la liste est vide. Sinon, il indique une condition d’alerte. Si nécessaire, vous pouvez l’utiliser pour jeter un coup de œil quels ordinateurs sont sous pression.

![tableau de bord mobile](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Utiliser l’opérateur in

L’opérateur *IN* avec *NOT IN* vous permet d’utiliser subsearches, qui sont des recherches qui incluent une autre recherche en tant qu’argument. Ils sont contenus dans les accolades {} dans une autre recherche *principale* ou *externe* . Le résultat d’une subsearch, souvent une liste de résultats distincts, est ensuite utilisé en tant qu’argument dans sa recherche de principal.

Vous pouvez utiliser subsearches pour correspondre à des sous-ensembles de vos données que vous ne puissiez décrire directement dans une expression de recherche, mais qui peut être généré à partir d’une recherche. Par exemple, si vous êtes intéressé par à l’aide d’une recherche pour trouver tous les événements à partir *d’ordinateurs mises à jour de sécurité manquantes*, vous devez concevoir un subsearch qui identifie tout d’abord que les *mises à jour de sécurité manquantes des ordinateurs* avant de trouver les événements appartenant à ces hôtes.

Ainsi, vous pourriez exprimer *ordinateurs manquants actuellement des mises à jour de sécurité requises* par la requête suivante :

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![DANS l’exemple de recherche](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Une fois que vous disposez de la liste, vous pouvez utiliser la recherche comme une recherche interne pour alimenter la liste des ordinateurs dans une recherche (principale) externe qui recherche les événements de ces ordinateurs. Pour cela, en mettant la recherche interne entre accolades et alimenter ses résultats en tant que valeurs possibles pour un champ de filtre/dans la recherche externe à l’aide de l’opérateur IN. La requête doit ressembler à :

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![DANS l’exemple de recherche](./media/log-analytics-log-searches/oms-search-in02-revised.png)


Notez également le filtre utilisé dans la recherche interne, car l’évaluation de la mise à jour système prend un instantané de tous les ordinateurs toutes les 24 heures. Vous pouvez crée une requête interne plus léger et plus précis en recherchant uniquement pour un jour. La recherche externe utilise à la place la sélection de l’heure dans l’interface utilisateur, en récupérant des événements au cours des 7 derniers jours. Pour plus d’informations sur les opérateurs de temps, reportez-vous à la section [opérateurs booléens](#boolean-operators) .

Dans la mesure où vous vraiment utiliser uniquement les résultats de la recherche interne en tant que filtre valeur externe, vous pouvez toujours appliquer les commandes dans la recherche externe. Par exemple, vous pouvez toujours grouper les événements ci-dessus avec une autre commande de mesure :

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![DANS l’exemple de recherche](./media/log-analytics-log-searches/oms-search-in03-revised.png)


En règle générale, vous souhaitez que la requête interne pour exécuter rapidement car Analytique de journal a des délais d’attente du côté service pour cette et également retourner une petite quantité de résultats. Si la requête interne ne retourne plus de résultats, la liste des résultats est tronquée, ce qui pourrait provoquer la recherche externe renvoyer des résultats incorrects.

Une autre règle est que la recherche interne doit fournir des résultats *agrégés* . En d’autres termes, il doit contenir une commande de *mesure* ; Vous ne peut pas actuellement d’alimentation résultats bruts dans une recherche externe.

Aussi, il peut y avoir qu’un seul opérateur dans et il doit être le dernier filtre dans la requête. Plusieurs opérateurs IN ne peut pas être ou serait – cela empêche essentiellement plusieurs subsearches en cours d’exécution : le point important est que seule la recherche sub/interne est possible pour chaque recherche externe.

Même avec ces limites, IN permet de nombreux types de recherches en corrélation et vous permet de définir de quelque chose de similaire à des groupes tels que les ordinateurs, les utilisateurs ou les fichiers, quel que soit les champs de vos données contiennent. Voici plusieurs exemples :

**Les mises à jour manquantes à partir d’ordinateurs où le paramètre de mise à jour automatique est désactivée**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Tous les événements d’erreur à partir d’ordinateurs exécutant SQL Server (= où l’évaluation de SQL a exécuter)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Tous les événements de sécurité à partir d’ordinateurs qui sont des contrôleurs de domaine (= où AD évaluation a exécuté)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Dont autres comptes ont ouvert une session les mêmes ordinateurs, lequel est connecté le compte BACONLAND\jochan ?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Utilisez la commande distincte

Comme son nom l’indique, cette commande fournit une liste de valeurs distinctes pour un champ. C’est étonnamment simple mais très utile. Le même peut être obtenu avec commande count() de mesure ainsi, comme illustré ci-dessous.

```
Type=Event | Measure count() by Computer
```

![Exemple de commande de recherche distinctes](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Toutefois, si vous êtes intéressés par est simplement une liste de valeurs distinctes et pas le nombre de documents qui ont des valeurs, puis DISTINCT permet plus propre et plus facile à lire la sortie et la syntaxe plus courte, comme indiqué ci-dessous.

```
Type=Event | Distinct Computer
```
![Exemple de commande de recherche distinctes](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Utilisez la fonction countdistinct avec la commande de mesure
La fonction countdistinct compte le nombre de valeurs distinctes au sein de chaque groupe. Par exemple, il peut servir à compter le nombre d’ordinateurs uniques pour chaque Type de création de rapports :

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Utilisez la commande d’intervalle de mesure
Avec près de collecte de données de performances en temps réel, vous pouvez collecter et visualiser n’importe quel compteur de performances dans le journal Analytique. Simplement saisir la requête **Que type : Perf** renverra des milliers de métriques graphiques en fonction du nombre de compteurs et les serveurs de votre environnement Analytique du journal. Avec l’agrégation de mesure à la demande, vous pouvez consulter les chiffres globaux dans votre environnement à un niveau élevé et approfondie de données plus précises que nécessaire.

Supposons que vous voulez connaître la moyenne du processeur sur tous vos ordinateurs. En examinant la moyenne du processeur pour tous les ordinateurs peut s’avérer utile car résultats peuvent obtenir lissés. Pour rechercher dans plus de détails, vous pouvez regrouper vos résultats une fois de plus petite segments de la fenêtre, puis ouvrez dans une série chronologique dans différentes dimensions. Par exemple, vous pouvez effectuer la moyenne horaire d’utilisation du processeur sur tous vos ordinateurs comme suit :

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![intervalle de mesure moyenne](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Par défaut, ces résultats seront affichera dans un graphique en courbes interactive de plusieurs séries.  Ce graphique prend en charge la série basculement (avec remise à l’échelle l’axe des y), zoom et en plaçant le curseur.  L’option d’affichage de la table est toujours disponible pour l’affichage des données brutes, si nécessaire.

Vous pouvez également regrouper par d’autres champs. Dans cet exemple, j’examine tous les compteurs % pour un ordinateur spécifique, et je souhaite connaître les centiles toutes les 70 heures de chaque compteur :

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Une chose à noter est que ces requêtes ne sont pas limitées aux compteurs de performance. Vous pouvez les appliquer à toute mesure. Dans cet exemple, j’examine les journaux W3C IIS. Je voudrais savoir quelle est la durée maximale que nécessaire sur un intervalle de 5 minutes pour le traitement de chaque requête :

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Utiliser plusieurs agrégats dans une même requête
Vous pouvez spécifier plusieurs clauses d’agrégation dans une commande de mesure.  Chacun peut être un alias indépendamment.  Si un alias n’est pas spécifié le nom du champ qui en résulte est la fonction d’agrégation qui a été utilisé (par exemple, « avg(CounterValue) » de avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Voici un autre exemple :
 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recherches de journaux, voir :

- [Champs personnalisés dans le journal Analytique](log-analytics-custom-fields.md) permet d’étendre les recherches de journaux.
- Passez en revue le [journal Analytique consigner la référence de recherche](log-analytics-search-reference.md) pour afficher tous les champs de recherche et les facettes disponibles dans journal Analytique.
