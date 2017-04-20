<properties
    pageTitle="Échelle de calculer automatiquement des nœuds dans un pool d’Azure lot | Microsoft Azure"
    description="Activer la mise à l’échelle automatique sur un pool de nuage pour ajuster dynamiquement le nombre de nœuds de calcul dans le pool."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Redimensionner automatiquement des nœuds de calcul dans un pool d’Azure lot

Mise à l’échelle automatique, le lot d’Azure service peut dynamiquement ajouter ou supprimer des nœuds de calcul dans un pool en fonction des paramètres que vous définissez. Vous pouvez potentiellement économiser temps et argent en ajustant automatiquement la quantité de puissance de calcul utilisé par votre application : ajouter des nœuds sous forme d’augmentation de demandes de tâche de votre projet et les supprimer lorsqu’ils diminuent.

Activer l’échelle automatique sur une liste de nœuds de calcul en associant une *formule d’échelle* que vous définissez, avec la [PoolOperations.EnableAutoScale] [ net_enableautoscale] méthode dans la bibliothèque [.NET de traitement par lots](batch-dotnet-get-started.md) . Puis, le service de traitement par lots utilise cette formule pour déterminer le nombre de nœuds de calcul qui sont nécessaires pour exécuter votre charge de travail. Lot répond aux métriques de service, des exemples de données qui sont collectées à intervalles réguliers et ajuste le nombre de nœuds de calcul dans le pool après un intervalle configurable en fonction de votre formule.

Vous pouvez activer la mise à l’échelle automatique lors de la création d’un pool ou d’un pool existant. Vous pouvez également modifier une formule existante sur un pool « échelle » est activée. Lot offre la possibilité d’évaluer vos formules avant de les assigner à des pools, ainsi que de surveiller l’état des séries de mise à l’échelle automatiques.

## <a name="automatic-scaling-formulas"></a>Formules de mise à l’échelle automatiques

Une formule de mise à l’échelle automatique est une valeur de chaîne que vous définissez qui contient une ou plusieurs instructions et est affectée d’un pool [autoScaleFormula] [ rest_autoscaleformula] élément (lot reste) ou [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propriété (lot .NET). Lorsque affectés à un pool, le service de traitement par lots utilise votre formule pour déterminer le nombre de cibles de nœuds de calcul dans le pool pour le prochain intervalle de traitement (plus sur des intervalles plus loin). La chaîne de la formule ne peut pas dépasser 8 Ko la taille, il peut inclure des instructions jusqu'à 100 qui sont séparées par des points-virgules et peuvent inclure des sauts de ligne et les commentaires.

Vous pouvez envisager des formules de mise à l’échelle automatiques à l’aide d’une échelle de lot « langue ». Formules instructions sont de forme libre qui peut inclure des variables définies par service (variables définies par le service de traitement par lots) et les variables définies par l’utilisateur (les variables que vous définissez). Ils peuvent effectuer différentes opérations sur ces valeurs à l’aide des fonctions, des opérateurs et des types intégrés. Par exemple, une instruction peut prendre la forme suivante :

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Des formules contiennent généralement plusieurs instructions qui effectuent des opérations sur des valeurs obtenues dans les instructions précédentes. Par exemple, tout d’abord nous obtenir une valeur pour `variable1`, puis la passer à une fonction pour remplir `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Avec ces instructions dans votre formule, votre objectif est d’arriver à un nombre de nœuds de calcul le pool doit être mise à l’échelle, le nombre de **cibles** de **nœuds dédiés**. Ce nombre peut être supérieur, inférieur, ou le même que le nombre actuel de nœuds dans le pool. Lot évalue la formule d’échelle automatique d’un pool à un intervalle défini ([intervalles de mise à l’échelle automatiques](#automatic-scaling-interval) sont présentés ci-dessous). Il ajuste ensuite le nombre de cibles de nœuds dans le pool pour le numéro de votre formule autoscale indique au moment de l’évaluation.

Un exemple rapide, cette formule de deux lignes autoscale indique que le nombre de nœuds doit être ajusté en fonction du nombre de tâches actives, avec un maximum de 10 nœuds de calcul :

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Les sections suivantes de cet article traitent des différentes entités qui composent vos formules autoscale, y compris les variables, les opérateurs, les opérations et les fonctions. Vous découvrirez comment obtenir des métriques de ressource et de tâche calcul différents dans le traitement par lots. Vous pouvez utiliser ces mesures à réorienter le nombre de nœuds de votre pool en fonction de l’état de tâche et de l’utilisation des ressources. Vous apprendrez ensuite à construire une formule et d’activation à l’échelle automatique sur un pool en utilisant les API .NET et reste du lot. Nous allons terminer avec quelques formules de l’exemple.

> [AZURE.IMPORTANT] Chaque lot d’Azure compte est limité à un nombre maximal de cœurs (et, par conséquent, les nœuds de calcul) qui peut être utilisé pour le traitement. Le service de traitement par lots crée uniquement jusqu'à cette limite du noyau. Par conséquent, il ne peut pas atteindre le nombre de nœuds de calcul cible qui est spécifié par une formule. Pour plus d’informations sur l’affichage et l’augmentation de votre compte les quotas, reportez-vous à la section [Quotas et limites pour le service de traitement par lots d’Azure](batch-quota-limit.md) .

## <a name="variables"></a>Variables

Vous pouvez utiliser des variables à la fois **défini par le service** et **défini par l’utilisateur** dans vos formules autoscale. Les variables définies par service sont intégrées dans le service de traitement par lots : certains sont en lecture-écriture et certaines sont en lecture seule. Variables définies par l’utilisateur sont des variables que *vous* définissez. Dans la formule de l’exemple de deux lignes ci-dessus, `$TargetDedicated` est défini par un service variable, lors de la `$averageActiveTaskCount` est une variable définie par l’utilisateur.

Les tableaux ci-dessous indiquent les variables à la fois en lecture-écriture et en lecture seule qui sont définis par le service de traitement par lots.

Vous pouvez **obtenir** et **définir** les valeurs de ces variables sont définies par le service pour gérer le nombre de nœuds de calcul dans un pool :

| Variables de service défini en lecture-écriture. | Description |
| --- | --- |
| $TargetDedicated | Le numéro de la **cible** de **dédié des nœuds de calcul** pour le pool. Ceci est le nombre de nœuds de calcul le pool doit être mise à l’échelle à. Il est un nombre « cible » dans la mesure où il est possible pour un pool ne pas atteindre le nombre de postes cibles. Cela peut se produire si le nombre de postes cibles est à nouveau modifié par une évaluation ultérieure autoscale avant le pool a atteint la cible initiale. Il peut également se produire si un quota de nœud ou sur un compte de traitement par lots est atteint avant le nombre de cibles de nœuds est atteint. |
| $NodeDeallocationOption | L’action qui se produit lorsque les nœuds de calcul sont supprimés à partir d’un pool. Les valeurs possibles sont :<ul><li>**remettre**--termine immédiatement les tâches et les place dans la file d’attente de travail afin qu’ils sont replanifiées.<li>**Terminer**: termine immédiatement les tâches et les supprime de la file d’attente de travail.<li>**taskcompletion**--attend actuellement en cours d’exécution pour terminer les tâches, puis supprime le nœud à partir du pool.<li>**retaineddata**--attend que toutes les données conservées de tâche locales sur le nœud à être nettoyées avant de supprimer le nœud à partir du pool.</ul> |

Vous pouvez **obtenir** la valeur de ces variables sont définies par le service pour effectuer des ajustements basés sur des mesures à partir du service de traitement par lots :

| Variables définies par le service en lecture seule | Description |
| --- | --- |
| $CPUPercent | Le pourcentage moyen d’utilisation du processeur. |
| $WallClockSeconds | Le nombre de secondes utilisé. |
| $MemoryBytes | Le nombre de mégaoctets utilisé. |
| $DiskBytes | Le nombre moyen de gigaoctets utilisé sur les disques locaux. |
| $DiskReadBytes | Le nombre d’octets lus. |
| $DiskWriteBytes | Le nombre d’octets écrits. |
| $DiskReadOps | Le nombre d’opérations de lecture de la disquette. |
| $DiskWriteOps | Le nombre d’opérations d’écriture effectuées. |
| $NetworkInBytes | Le nombre d’octets entrants. |
| $NetworkOutBytes | Le nombre d’octets de sortie. |
| $SampleNodeCount | Le nombre de nœuds de calcul. |
| $ActiveTasks | Le nombre de tâches dans un état actif. |
| $RunningTasks | Le nombre de tâches en cours d’exécution. |
| $PendingTasks | La somme de $ActiveTasks et de $RunningTasks. |
| $SucceededTasks | Le nombre de tâches qui s’est terminée correctement. |
| $FailedTasks | Le nombre de tâches qui ont échoué. |
| $CurrentDedicated | Le nombre actuel de dédié des nœuds de calcul. |

> [AZURE.TIP] Les variables en lecture seule, définie par le service qui figurent ci-dessus sont des *objets* qui fournissent différentes méthodes pour accéder aux données associées à chaque. Pour plus d’informations, reportez-vous à la section [données d’échantillon obtenir](#getsampledata) ci-dessous.

## <a name="types"></a>Types de

Ces **types** sont pris en charge dans une formule.

- Double
- doubleVec
- doubleVecList
- chaîne
- timestamp : horodatage est une structure composée qui contient les membres suivants :

    - année
    - mois (1 à 12)
    - jour (de 1 à 31)
    - jour de la semaine (dans le format de nombre, par exemple, 1 pour lundi)
    - heure (dans le format de nombre de 24 heures, par exemple, 13 signifie 1 PM)
    - minute (00-59)
    - seconde (00-59)
- TimeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Opérations

Ces **opérations** sont autorisées sur les types qui sont répertoriés ci-dessus.

| Opération                             | Opérateurs pris en charge   | Type de résultat   |
| ------------------------------------- | --------------------- | ------------- |
| Double *opérateur* double              | +, -, *, /            | Double            |
| Double *opérateur* timeinterval        | *                     | TimeInterval      |
| doubleVec *opérateur* double           | +, -, *, /            | doubleVec         |
| doubleVec *opérateur* doubleVec        | +, -, *, /            | doubleVec         |
| TimeInterval *opérateur* double        | *, /                  | TimeInterval      |
| TimeInterval *opérateur* timeinterval  | +, -                  | TimeInterval      |
| horodatage *d’opérateur* TimeInterval     | +                     | horodatage         |
| timeinterval *opérateur* de timestamp     | +                     | horodatage         |
| horodatage *opérateur* timestamp        | -                     | TimeInterval      |
| *opérateur*double                      | -, !                  | Double            |
| *opérateur de*timeinterval                | -                     | TimeInterval      |
| Double *opérateur* double              | <, < =, ==, > =, >, ! =  | Double            |
| *opérateur* de chaîne              | <, < =, ==, > =, >, ! =  | Double            |
| horodatage *opérateur* timestamp        | <, < =, ==, > =, >, ! =  | Double            |
| TimeInterval *opérateur* timeinterval  | <, < =, ==, > =, >, ! =  | Double            |
| Double *opérateur* double              | & &, & #124 ; & #124 ;      | Double            |

Lorsque vous testez un double avec un opérateur ternaire (`double ? statement1 : statement2`), autre que zéro est **true**et zéro a la **valeur false**.

## <a name="functions"></a>Fonctions

Ces **fonctions** de prédéfinies sont disponibles pour les utiliser dans la définition d’une formule de mise à l’échelle automatique.

| Fonction                          | Type de retour   | Description
| --------------------------------- | ------------- | --------- |
| AVG(doubleVecList)                | Double        | Retourne la valeur moyenne de toutes les valeurs dans le doubleVecList.
| Len(doubleVecList)                | Double        | Retourne la longueur du vecteur qui est créé à partir de la doubleVecList.
| LG(double)                        | Double        | Retourne le journal 2 base du double.
| LG(doubleVecList)                 | doubleVec     | Retourne le journal un base 2 de la doubleVecList. Un vec(double) doit explicitement être passé pour le paramètre. Dans le cas contraire, la version de lg(double) double est supposée.
| ln(double)                        | Double        | Renvoie le logarithme népérien de la valeur double.
| ln(doubleVecList)                 | doubleVec     | Retourne le journal un base 2 de la doubleVecList. Un vec(double) doit explicitement être passé pour le paramètre. Dans le cas contraire, la version de lg(double) double est supposée.
| log(double)                       | Double        | Retourne le journal du double en base 10.
| log(doubleVecList)                | doubleVec     | Retourne le journal un base 10 de le doubleVecList. Un vec(double) doit être passé explicitement pour le même paramètre de type double. Dans le cas contraire, la version de log(double) double est supposée.
| max(doubleVecList)                | Double        | Retourne la valeur maximale dans la doubleVecList.
| min(doubleVecList)                | Double        | Retourne la valeur minimale dans la doubleVecList.
| Norm(doubleVecList)               | Double        | Retourne la norme-deux du vecteur qui est créé à partir de la doubleVecList.
| centile (v doubleVec, double p) | Double        | Retourne l’élément centile du vecteur v.
| ALEA)                            | Double        | Renvoie une valeur aléatoire entre 0,0 et 1,0.
| Range(doubleVecList)              | Double        | Renvoie la différence entre les valeurs minimale et maximale dans la doubleVecList.
| STD(doubleVecList)                | Double        | Renvoie l’écart des valeurs dans le doubleVecList.
| Stop()                            |               | Arrête l’évaluation de l’expression autoscaling.
| SUM(doubleVecList)                | Double        | Renvoie la somme de tous les composants de la doubleVecList.
| heure (chaîne dateTime = "")          | horodatage     | Retourne l’horodatage de l’heure actuelle, si aucun paramètre n’est passé, ou que l’horodatage de la chaîne dateTime, si elle est passée. Les formats dateTime pris en charge sont DTF-W3C et RFC 1123.
| Val (v doubleVec, i double)        | Double        | Retourne la valeur de l’élément qui se trouve à l’emplacement i dans le vecteur v, avec un index de départ de zéro.

Certaines fonctions décrites dans le tableau ci-dessus peuvent accepter une liste en tant qu’argument. La liste séparée par des virgules est une combinaison de *doubles* et de *doubleVec*. Par exemple :

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

La valeur de *doubleVecList* est convertie en un seul *doubleVec* avant d’évaluation. Par exemple, si `v = [1,2,3]`, puis en appelant `avg(v)` est équivalent à l’appel `avg(1,2,3)`. L’appel de `avg(v, 7)` est équivalent à l’appel `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtenir des exemples de données

AutoScale formules agissent sur les données de métrique (échantillons) qui sont fournies par le service de traitement par lots. Une formule augmente ou réduit la taille du pool en fonction des valeurs qu’il obtient à partir du service. Les variables définies par service décrites ci-dessus sont des objets qui fournissent différentes méthodes pour accéder aux données qui est associées à cet objet. Par exemple, l’expression suivante illustre une demande pour obtenir les cinq dernières minutes de l’utilisation de l’UC :

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Méthode | Description |
| --- | --- |
| GetSample() | Le `GetSample()` méthode retourne un vecteur des exemples de données.<br/><br/>Un échantillon est de 30 secondes de données de la métrique. En d’autres termes, les échantillons proviennent toutes les 30 secondes. Mais, comme indiqué ci-dessous, il existe un délai entre lorsqu’un échantillon est collecté et lorsqu’il est disponible pour une formule. En tant que tel, pas tous les exemples pour une période de temps donnée peuvent être disponibles pour évaluation par une formule.<ul><li>`doubleVec GetSample(double count)`<br/>Spécifie le nombre d’échantillons pour obtenir les exemples les plus récents qui ont été collectés.<br/><br/>`GetSample(1)`Renvoie le dernier échantillon de disponible. Pour des mesures comme `$CPUPercent`, toutefois, cela ne doit pas être utilisé car il est impossible de savoir *lorsque* l’échantillon a été collecté. Il peut être récente, ou, en raison de problèmes de système, il peut être beaucoup plus ancien. Il est préférable dans ce cas d’utiliser un intervalle de temps, comme illustré ci-dessous.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Spécifie un intervalle de temps pour rassembler des exemples de données. Le cas échéant, il spécifie également le pourcentage d’échantillons qui doivent être disponibles pendant la période demandée.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`Renvoie 20 échantillons si tous les exemples dans les dix dernières minutes sont présentes dans l’historique de la CPUPercent. Si la dernière minute de l’historique n’est pas disponible, toutefois, seulement 18 échantillons seront retournés. Dans ce cas :<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`échoue car que 90 % des échantillons sont disponibles.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`devrait aboutir.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Spécifie un intervalle de temps pour la collecte de données, avec une heure de début et une heure de fin.<br/><br/>Comme mentionné ci-dessus, il existe un délai entre lorsqu’un échantillon est collecté et lorsqu’il est disponible pour une formule. Ceci doit être pris en compte lorsque vous utilisez la `GetSample` méthode. Voir `GetSamplePercent` ci-dessous.|
| GetSamplePeriod() | Retourne la période d’échantillons qui ont été prises dans un jeu de données d’exemple historiques. |
| Count() | Renvoie le nombre total d’échantillons dans l’historique des métriques. |
| HistoryBeginTime() | Retourne l’horodatage de l’échantillon de données disponibles plus ancien de la mesure. |
| GetSamplePercent() |Renvoie le pourcentage des échantillons qui sont disponibles pour un intervalle de temps donné. Par exemple :<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Étant donné que la `GetSample` méthode échoue si le pourcentage d’échantillons retourné est inférieure à la `samplePercent` spécifié, vous pouvez utiliser la `GetSamplePercent` méthode pour vérifier tout d’abord. Vous pouvez ensuite effectuer une autre action si les échantillons suffisants sont présents, sans arrêter l’évaluation de la mise à l’échelle automatique.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemples, pourcentage d’échantillonnage et la méthode *GetSample()*

L’opération de base d’une formule d’échelle automatique consiste à obtenir des données métriques de tâche et de ressource et de puis ajustez la taille du pool en fonction de ces données. En tant que tel, il est important de bien comprendre comment les formules autoscale interagissent avec les données de métrique ou « samples ».

**Exemples**

Périodiquement, le service de traitement par lots prend des *exemples* de mesures de tâche et de ressource et les rend disponibles pour vos formules autoscale. Ces exemples sont enregistrées toutes les 30 secondes par le service de traitement par lots. Toutefois, il est en général une latence qui provoque un retard entre lorsque les échantillons ont été enregistrés et lorsqu’ils sont mis à disposition (et peuvent être lus par) vos formules autoscale. En outre, en raison de divers facteurs, tels que le réseau ou d’autres problèmes de l’infrastructure, exemples pas ont pu être enregistrées pour un intervalle donné. Ainsi, dans les échantillons de « manquants ».

**Pourcentage d’échantillonnage**

Lors de la `samplePercent` est passé à la `GetSample()` méthode ou de la `GetSamplePercent()` est appelée, le « pour cent » fait référence à une comparaison entre le nombre total *possible* d’échantillons qui sont enregistrés par le service de traitement par lots et le nombre d’échantillons qui sont réellement *disponibles* pour votre formule autoscale.

Examinons un timespan 10 minutes par exemple. Étant donné que les échantillons sont enregistrées toutes les 30 secondes, au sein d’un timespan 10 minutes, le nombre maximal d’échantillons qui sont enregistrés par le traitement par lots serait 20 échantillons (2 par minute). Toutefois, en raison de la latence inhérente du mécanisme de création de rapports ou un autre problème au sein de l’infrastructure d’Azure, il peut être uniquement 15 échantillons qui sont disponibles pour votre formule d’échelle pour la lecture. Cela signifie que, pour cette période de 10 minutes, que **75 %** du nombre total d’échantillons enregistrés sont réellement disponibles pour votre formule.

**Plages de GetSample() et d’exemple**

Vos formules autoscale vont être la croissance et la réduction de vos pools : ajout de nœuds ou de supprimer des nœuds. Étant donné que les nœuds vous coûtent de l’argent, que vous souhaitez vous assurer que vos formules utilisent une méthode intelligente d’analyse basé sur des données suffisantes. Par conséquent, nous vous conseillons une analyse de tendances-type dans vos formules. Ce type s’agrandit et réduire vos pools basés sur une *plage* d’échantillons collectés.

Pour ce faire, utilisez `GetSample(interval look-back start, interval look-back end)` pour renvoyer un **vecteur** d’échantillons :

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Lorsque la ligne ci-dessus est évaluée par lot, il retournera une plage d’échantillons comme un vecteur de valeurs. Par exemple :

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Une fois que vous avez récupéré le vecteur d’échantillons, vous pouvez ensuite utiliser des fonctions telles que `min()`, `max()`, et `avg()` de dériver les valeurs significatives de la plage collecte.

Pour plus de sécurité, vous pouvez forcer une évaluation de formule à *échouer* si inférieur à un certain pourcentage de l’exemple est disponible pour une période donnée. Lorsque vous forcez une évaluation de formule à échouer, vous demandez le lot de cesser de davantage d’évaluation de la formule, si le pourcentage d’échantillons spécifié n’est pas disponible, et aucune modification de la taille du pool n’est effectuée. Pour spécifier un pourcentage obligatoire d’échantillons destinés à l’évaluation réussisse, le spécifier en tant que troisième paramètre à `GetSample()`. Ici, une exigence de 75 % des échantillons est spécifiée :

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Il est également important, en raison du délai mentionné précédemment dans la disponibilité de l’échantillon, toujours spécifier une plage horaire avec une heure de début de regarder l’arrière qui est antérieure à une minute. C’est parce qu’il prend environ une minute pour les échantillons de se propager par le système, donc les exemples dans la plage `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` souvent ne seront plus disponibles. À nouveau, vous pouvez utiliser le paramètre pourcentage de `GetSample()` pour forcer une exigence de pourcentage exemple particulier.

> [AZURE.IMPORTANT] Nous **recommandons fortement** que vous * *d’éviter de compter *uniquement* sur `GetSample(1)` dans vos formules autoscale **. C’est parce que `GetSample(1)` dit globalement au service de traitement par lots, « je veux le dernier échantillon ont, peu importe Combien ça. » Dans la mesure où il est uniquement un seul échantillon, et il peut être un exemple plus ancien, il est peut-être pas représentatif de l’image supérieure de tâches récentes ou état de la ressource. Si vous n’utilisez pas `GetSample(1)`, veillez à ce qu’il fait partie d’une plus grande instruction et pas le seul point de votre formule s’appuie sur.

## <a name="metrics"></a>Mesures

Vous pouvez utiliser les mesures de **ressource** et de **tâche** lorsque vous définissez une formule. Vous ajustez le nombre de cibles de nœuds dédiés dans le pool basé sur les données de mesures que vous pouvez obtenir et évaluer. Consultez la section [Variables](#variables) ci-dessus pour plus d’informations sur chaque mesure.

<table>
  <tr>
    <th>Métrique</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><b>Ressources</b></td>
    <td><p><b>Mesures de la ressource</b> sont basées sur l’utilisation du processeur, la bande passante et la mémoire des nœuds de calcul, ainsi que le nombre de nœuds.</p>
        <p> Ces variables définies par service sont utiles pour apporter des ajustements basés sur le nombre de nœuds :</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ces variables définies par service sont utiles pour effectuer des ajustements en fonction de l’utilisation des ressources de nœud :</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tâche</b></td>
    <td><p><b>Mesures de tâches</b> sont basées sur l’état des tâches, telles que les actifs, en attente et terminé. Les variables définies par service suivantes sont utiles pour effectuer les modifications de taille du pool en fonction des métriques de tâche :</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Écrire une formule échelle automatique

Vous créez une formule autoscale en formant des instructions qui utilisent des composants ci-dessus, puis combinez ces instructions dans une formule complète. Dans cette section, nous allons créer un exemple de formule autoscale qui peut exécuter des décisions de mise à l’échelle du monde réel.

Tout d’abord, nous allons définir les exigences de notre nouvelle formule autoscale. La formule doit :

1. **Augmenter** le nombre de cibles de nœuds de calcul dans un pool si l’utilisation du processeur est élevée.
2. **Diminuer** le nombre de cibles de nœuds de calcul dans un pool lors de l’utilisation du processeur est faible.
3. Toujours limiter le nombre **maximal** de nœuds à 400.

Pour *augmenter* le nombre de nœuds lors de l’utilisation élevée du processeur, nous définissons l’instruction qui remplit une variable définie par l’utilisateur (`$totalNodes`) avec une valeur de 110 pour cent du nombre cible actuelle de nœuds, mais uniquement si l’utilisation moyenne du processeur minimale au cours des 10 dernières minutes a été supérieure à 70 %. Dans le cas contraire, nous utilisons la valeur dédiée actuelle.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Pour *réduire* le nombre de nœuds lors de l’utilisation du processeur faible, l’instruction suivante dans notre formule définit le même `$totalNodes` variable à 90 pour cent du nombre de nœuds, si l’utilisation moyenne du processeur dans les dernières minutes de 60 a moins de 20 pour cent cible en cours. Dans le cas contraire, utilisez la valeur en cours de `$totalNodes` qui nous remplie dans l’instruction ci-dessus.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Maintenant, limiter le nombre de cibles de nœuds de calcul dédiée à un **maximum** de 400 :

```
$TargetDedicated = min(400, $totalNodes)
```

Voici la formule complète :

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Créer un pool d’échelle automatique activée

Pour créer un nouveau pool autoscaling activé, vous pouvez utiliser l’une des techniques suivantes :

**Lot .NET**

1. Créer le pool avec [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Définissez la propriété [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) sur `true`.
1. Définissez la propriété [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) avec votre formule autoscale.
1. (Facultatif) Définissez la propriété [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (valeur par défaut est 15 minutes).
1. Valider le pool avec [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) ou [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Lot API REST**

* [Ajouter un pool à un compte](https://msdn.microsoft.com/library/azure/dn820174.aspx): spécifiez la `enableAutoScale` et `autoScaleFormula` éléments dans votre demande d’API REST pour configurer l’échelle automatique d’un pool lors de sa création.

L’extrait de code suivant crée un pool prenant en charge l’échelle automatique en utilisant le [Traitement par lots .NET] [ net_api] bibliothèque. Formule d’échelle du pool définit le nombre cible de nœuds à 5 le lundi et 1 sur tous les autres jours de la semaine. L' [intervalle de mise à l’échelle automatique](#automatic-scaling-interval) est défini sur 30 minutes. Dans ce guide et les autres extraits C# dans cet article, « myBatchClient » est une instance initialisée correctement de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Outre les API REST de lot et le Kit de développement .NET, vous pouvez utiliser les autres [Kits de développement de lot](batch-technical-overview.md#batch-development-apis), les [applets de commande PowerShell de lot](batch-powershell-cmdlets-get-started.md)et les [Commandes CLI](batch-cli-get-started.md) pour travailler avec autoscaling.

> [AZURE.IMPORTANT] Lorsque vous créez un pool d’échelle automatique activée, vous devez **pas** spécifier le `targetDedicated` paramètre. En outre, si vous souhaitez manuellement redimensionner un pool prenant en charge l’échelle (par exemple, avec [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), puis vous devez premier **désactivation** automatique de mise à l’échelle sur le pool, puis de le redimensionner.

### <a name="automatic-scaling-interval"></a>Intervalle de mise à l’échelle automatique

Par défaut, le service de traitement par lots ajuste taille d’un pool conformément à sa formule autoscale toutes **les 15 minutes**. Cet intervalle est configurable, toutefois, en utilisant les propriétés de pool suivants :

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (traitement par lots de .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (API REST)

L’intervalle minimal est de cinq minutes, et la valeur maximale est de 168 heures. Si un intervalle en dehors de cette plage est spécifié, le service de traitement par lots renverra une erreur de requête incorrecte (400).

> [AZURE.NOTE] AutoScaling ne constitue pas actuellement pour répondre aux modifications en moins d’une minute, mais vise plutôt à ajuster la taille de votre pool progressivement pendant l’exécution d’une charge de travail.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Activer autoscaling sur un pool existant

Si vous avez déjà créé un pool avec un certain nombre de nœuds de calcul en utilisant le paramètre *targetDedicated* , vous pouvez tout de même activer autoscaling sur le pool. Chaque lot de SDK fournit une opération « enable autoscale », par exemple :

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (traitement par lots de .NET)
*  [Activer la mise à l’échelle automatique sur une grappe de] [ rest_enableautoscale] (API REST)

Lorsque vous activez autoscaling sur un pool existant, les règles suivantes s’appliquent :

* Si la mise à l’échelle automatique est actuellement **désactivée** sur le pool lorsque vous émettez la demande « enable autoscale », vous *devez* spécifier une formule valide autoscale lorsque vous exécutez la requête. Vous pouvez *éventuellement* spécifier un intervalle d’évaluation autoscale. Si vous ne spécifiez pas un intervalle, la valeur par défaut de 15 minutes est utilisée.

* Si l’échelle est actuellement **activé** sur le pool, vous pouvez spécifier une formule autoscale, un intervalle d’évaluation ou les deux. Vous ne pouvez pas omettre les deux propriétés.

  * Si vous spécifiez un nouvel intervalle d’évaluation autoscale, puis la planification d’évaluation existante est arrêtée et démarre une nouvelle planification. Heure de début de la nouvelle planification est l’heure à laquelle la demande « activer l’échelle automatique » a été délivrée.

  * Si vous omettez soit la formule autoscale ou intervalle d’évaluation, le service de traitement par lots continue d’utiliser la valeur actuelle de ce paramètre.

> [AZURE.NOTE] Si une valeur a été spécifiée pour le paramètre *targetDedicated* lorsque le pool a été créé, il est ignoré lors de l’évaluation de la formule de la mise à l’échelle automatique.

Cet extrait de code C# utilise le [Traitement par lots .NET] [ net_api] bibliothèque pour activer autoscaling sur un pool existant :

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Mise à jour d’une formule d’échelle automatique

Vous utilisez la même demande à *mettre à jour* la formule « activer l’échelle automatique » sur un pool prenant en charge l’échelle (par exemple, avec [EnableAutoScale] [ net_enableautoscale] dans le traitement par lots .NET). Il n’y a aucune opération spéciale « mise à jour d’échelle automatique ». Par exemple, si autoscaling est déjà activé sur « myexistingpool » lors de l’exécution du code suivant, sa formule autoscale est remplacé par le contenu de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Mise à jour de l’intervalle d’échelle automatique

Comme avec la mise à jour d’une formule autoscale, vous utilisez même [EnableAutoScale] [ net_enableautoscale] méthode pour modifier l’intervalle d’évaluation échelle automatique d’un pool existant activé à l’échelle automatique. Par exemple, pour définir l’intervalle d’échelle d’évaluation à 60 minutes pour un pool est déjà activé en échelle automatique :

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Évaluer une formule échelle automatique

Vous pouvez évaluer une formule avant de l’appliquer à un pool. De cette façon, vous pouvez effectuer un « test run » de la formule pour voir comment ses instructions évaluer avant de placer la formule dans la production.

Pour évaluer une formule échelle automatique, vous devez **Activer autoscaling** sur le pool avec une **formule valide**. Si vous souhaitez tester une formule dans un pool n’ayant pas encore autoscaling activé, vous pouvez utiliser la formule d’une ligne `$TargetDedicated = 0` lorsque vous activez pour la première fois autoscaling. Puis, utilisez une des opérations suivantes pour évaluer la formule que vous souhaitez tester :

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Ces méthodes de lot .NET requièrent l’ID d’un pool existant et une chaîne qui contient la formule autoscale à évaluer. Résultats de l’évaluation sont contenus dans l’instance retournée de [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) .

* [Évaluer une formule de mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    Dans cette requête API REST, spécifiez l’ID du pool dans l’URI et la formule de l’échelle dans l’élément *autoScaleFormula* du corps de la demande. La réponse de l’opération contient des informations sur l’erreur qui peuvent être liées à la formule.

Dans ce [Traitement par lots .NET] [ net_api] l’extrait de code, nous évaluons une formule avant de l’appliquer à la [CloudPool][net_cloudpool]. Si le regroupement ne dispose pas d’autoscaling activé, nous l’activation de tout d’abord.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Une évaluation réussie de la formule dans cet extrait de code entraîne une sortie similaire à ce qui suit :

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtenir des informations sur les séries de l’échelle automatique

Pour vous assurer que votre formule s’exécute comme prévu, nous vous conseillons régulièrement les résultats de l’autoscaling, « runs » lot effectue sur votre pool. Pour, obtenir (ou Actualiser) une référence pour le pool et examiner les propriétés de son échelle dernière exécution.

Dans .NET de lot, la propriété [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) possède plusieurs propriétés fournissant des informations sur la dernière échelle automatique exécuter sur le pool effectuée par le service de traitement par lots.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Dans l’API REST, la demande [d’obtenir des informations sur un pool](https://msdn.microsoft.com/library/dn820165.aspx) renvoie des informations sur le pool, ce qui inclut la dernière échelle automatique, exécutez informations dans [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

L’extrait de code C# suivant utilise la bibliothèque .NET de traitement par lots pour imprimer des informations sur le dernier autoscaling s’exécuter sur le pool « monpool » :

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemple de sortie de l’extrait de code précédent :

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Formules d’autoscale exemple

Jetons un œil à quelques formules qui montrent différentes façons d’ajuster la quantité de ressources informatiques dans un pool.

### <a name="example-1-time-based-adjustment"></a>Exemple 1 : Temps-REGLAGE

Vous souhaiterez peut-être ajuster la taille du pool en fonction du jour de la semaine et l’heure de la journée, pour augmenter ou diminuer le nombre de nœuds dans le pool en conséquence.

Cette formule obtient d’abord l’heure actuelle. S’il s’agit d’un jour de la semaine (1-5) et dans les heures de travail (8 h à 6 h), la taille du pool cible est définie à 20 nœuds. Dans le cas contraire, elle est définie à 10 noeuds.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemple 2 : Tâche-REGLAGE

Dans cet exemple, la taille du pool est ajustée en fonction du nombre de tâches dans la file d’attente. Notez que les commentaires et les sauts de ligne sont acceptables dans les chaînes de la formules.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemple 3 : Comptabilisation des tâches parallèles

Voici un autre exemple qui ajuste la taille du pool en fonction du nombre de tâches. Cette formule prend également en compte la [MaxTasksPerComputeNode] [ net_maxtasks] valeur qui a été définie pour le pool. Ceci est particulièrement utile dans les situations où [l’exécution de la tâche parallèle](batch-parallel-node-tasks.md) a été activée sur votre pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemple 4 : Définition d’une taille de pool initial

Cet exemple montre un extrait de code C# avec une formule autoscale qui définit la taille du pool à un certain nombre de nœuds pour une période initiale. Puis il ajuste la taille du pool en fonction du nombre d’exécution et active des tâches après la période initiale s’est écoulé.

La formule de l’extrait de code suivant :

- Définit la taille du pool initial à quatre nœuds.
- N’ajuste pas la taille du pool dans les 10 premières minutes de cycle de vie du pool.
- Après 10 minutes, obtient la valeur maximale du nombre de tâches en cours d’exécution et actives dans les 60 dernières minutes.
  - Si les deux valeurs sont 0 (indiquant qu’aucune tâche n’était en cours d’exécution ou active dans les 60 dernières minutes), la taille du pool est définie sur 0.
  - Si une valeur est supérieure à zéro, aucune modification n’est apportée.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Étapes suivantes

* [Optimiser le lot Azure compute de l’utilisation des ressources avec les tâches simultanées de nœud](batch-parallel-node-tasks.md) contient plus d’informations sur la façon dont vous pouvez effectuer simultanément plusieurs tâches sur les nœuds de calcul dans le pool. En plus des autoscaling, cette fonctionnalité peut aider à réduire la durée du travail pour certaines charges de travail, pour vous épargner.

* Une autre amélioration de l’efficacité, assurez-vous que votre application de traitement par lots interroge le service de lot de façon plus optimale. Dans [efficace d’interroger le service de traitement par lots d’Azure](batch-efficient-list-queries.md), vous allez apprendre à limiter la quantité de données qui transitent par le réseau lors de l’interrogation de l’état des milliers de nœuds de calcul ou des tâches.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
