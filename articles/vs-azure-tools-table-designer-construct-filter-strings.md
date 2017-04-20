<properties
   pageTitle="Construire des chaînes de filtrage pour le Concepteur de table | Microsoft Azure"
   description="Construire des chaînes de filtrage pour le Concepteur de table"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Construire des chaînes de filtrage pour le Concepteur de Table

## <a name="overview"></a>Vue d’ensemble

Pour filtrer des données dans une table Azure qui s’affiche dans le **Concepteur de tables**de Visual Studio, vous construisez une chaîne de filtrage et entrez dans le champ filtre. La syntaxe de chaîne de filtre est définie par les Services de données WCF et est similaire à une clause SQL WHERE, mais il est envoyée au service de la Table via une demande HTTP. Le **Concepteur de Table** gère le codage approprié à votre place, pour filtrer sur une valeur de propriété souhaitée, il suffit de taper le nom de la propriété, opérateur de comparaison, critère et éventuellement, valeur booléenne opérateur dans le champ filtre. Vous n’avez pas besoin d’inclure l’option de requête $filter comme vous le feriez si vous ont été construire une URL pour interroger la table via la [Référence des API d’autres Services de stockage](http://go.microsoft.com/fwlink/p/?LinkId=400447).

Les Services de données WCF sont basés sur l' [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Pour plus d’informations sur l’option de requête de filtre système (**$filter**), consultez la [spécification OData URI Conventions](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Opérateurs de comparaison

Les opérateurs logiques suivants sont pris en charge pour tous les types de propriété :

|Opérateur logique|Description|Exemple de chaîne de filtre|
|---|---|---|
|EQ|Égal à|City eq 'Redmond'|
|gt|Supérieur à|Prix gt 20|
|GE|Supérieur ou égal à|Prix ge 10|
|lt|Inférieur à|Prix lt 20|
|le|Inférieur ou égal|Le de prix 100|
|Nou|Pas égal à|Ville ne 'London'|
|et|Et|Le de prix 200 et prix gt 3.5|
|ou|Ou|Le de prix 3.5 ou prix gt 200|
|pas|Pas|pas d’isAvailable|

Lors de la construction d’une chaîne de filtrage, les règles suivantes sont importantes :

- Les opérateurs logiques permet de comparer une propriété à une valeur. Notez qu’il n’est pas possible de comparer une propriété à une valeur dynamique ; une partie de l’expression doit être une constante.

- Toutes les parties de la chaîne de filtre sont sensibles à la casse.

- La valeur de constante doit être du même type de données que la propriété afin que le filtre pour renvoyer des résultats valides. Pour plus d’informations sur les types de propriété pris en charge, consultez [compréhension du modèle de données de Service de Table](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Le filtrage sur les propriétés de type chaîne

Lorsque vous filtrez sur les propriétés de type chaîne, placez la constante de chaîne entre guillemets simples.

L’exemple ci-dessous filtre les propriétés **PartitionKey** et **RowKey** ; les propriétés ne correspondant pas à une clé supplémentaires peuvent également être ajoutées à la chaîne de filtrage :

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Vous pouvez mettre chaque expression de filtre entre parenthèses, même s’il n’est pas nécessaire :

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Notez que le service de la Table ne gère pas les requêtes génériques, et ils ne sont pas pris en charge dans le Concepteur de Table soit. Toutefois, vous pouvez effectuer le préfixe de correspondance sur le préfixe de votre choix à l’aide des opérateurs de comparaison. L’exemple suivant retourne les entités avec un début de propriété LastName par la lettre « A » :

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrage des propriétés numériques

Pour filtrer un entier ou un nombre à virgule flottante, spécifiez le nombre sans guillemets.

Cet exemple renvoie toutes les entités ayant une propriété Age dont la valeur est supérieure à 30 :

    Age gt 30

Cet exemple renvoie toutes les entités avec une propriété MontantDû dont la valeur est inférieure ou égale à 100.25 :

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrage de propriétés booléennes

Pour filtrer sur une valeur de type Boolean, spécifiez **true** ou **false** sans guillemets.

L’exemple suivant retourne tous les entités où la propriété IsActive est définie sur **true**:

    IsActive eq true

Vous pouvez également écrire cette expression de filtre sans l’opérateur logique. Dans l’exemple suivant, le service de la Table retournera également toutes les entités où IsActive a la **valeur true**:

    IsActive

Pour renvoyer toutes les entités où IsActive a la valeur false, vous pouvez utiliser non opérateur :

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Le filtrage sur les propriétés de DateTime

Pour filtrer sur une valeur DateTime, spécifiez le mot clé **datetime** , suivi par la constante de date/heure dans des guillemets simples. La constante de date/heure doit être au format d’heure UTC combinée, comme décrit dans la [Mise en forme des valeurs de propriété DateTime](http://go.microsoft.com/fwlink/p/?LinkId=400449).

L’exemple suivant retourne des entités où la propriété CustomerSince est égale à 10 juillet 2008 :

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
