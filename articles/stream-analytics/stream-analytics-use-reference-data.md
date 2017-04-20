<properties
    pageTitle="Utiliser des tables de données et de recherche de référence dans le flux de données Analytique | Microsoft Azure"
    description="Utilisez les données de référence dans une requête Analytique de flux"
    keywords="table de choix, les données de référence"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>À l’aide de tables de données ou de recherche des références dans un flux d’entrée flux Analytique

Les données de référence (également appelé une table de recherche) sont un ensemble de données limité qui est statique ou ralentir la modification dans la nature, utilisé pour effectuer une recherche ou pour mettre en corrélation avec votre flux de données. Pour utiliser les données de référence dans votre projet Azure flux Analytique vous utilise généralement une [Jointure des données de référence](https://msdn.microsoft.com/library/azure/dn949258.aspx) dans votre requête. Analytique de flux de données utilise le stockage Blob de Azure comme la couche de stockage pour les données de référence et avec la référence d’Azure Data Factory des données peuvent être transformées et/ou copiées vers le stockage Azure Blob, à utiliser comme données de référence, à partir de [n’importe quel nombre de nuage basé et banques de données sur site](../data-factory/data-factory-data-movement-activities.md). Les données de référence sont modélisées sous la forme d’une séquence d’objets BLOB (défini dans la configuration d’entrée) dans l’ordre croissant de la date et l’heure spécifiée dans le nom de l’objet blob. Il prend **uniquement** en charge l’ajout à la fin de la séquence à l’aide d’une date/heure **supérieure** à celle spécifiée par la dernière blob dans la séquence.

## <a name="configuring-reference-data"></a>Configuration des données de référence

Pour configurer les données de référence, vous devez d’abord créer une entrée de type de **Données de référence**. Le tableau ci-dessous décrit chaque propriété que vous devez fournir lors de la création de l’entrée de données de référence avec sa description :

<table>
<tbody>
<tr>
<td>Nom de la propriété</td>
<td>Description</td>
</tr>
<tr>
<td>Alias d’entrée</td>
<td>Un nom convivial qui sera utilisé dans la requête de travail pour faire référence à cette entrée.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Le nom du compte de stockage où se trouvent vos fichiers blob. Si elle est dans le même abonnement comme votre flux de travail d’Analytique, vous pouvez le sélectionner dans la liste déroulante vers le bas.</td>
</tr>
<tr>
<td>Clé de compte de stockage</td>
<td>La clé secrète avec le compte de stockage. Il est automatiquement remplie si le compte de stockage se trouve dans le même abonnement comme votre tâche de flux de données Analytique.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique des objets BLOB stockées dans le service Microsoft Azure Blob. Lorsque vous téléchargez un objet blob dans le service d’objet Blob, vous devez spécifier un conteneur pour ce blob.</td>
</tr>
<tr>
<td>Chemin d’accès au modèle</td>
<td>Le chemin d’accès du fichier utilisé pour localiser vos objets BLOB dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances de 2 variables suivantes :<BR>{date}, {time}<BR>Exemple 1 : products/{date}/{time}/product-list.csv<BR>Exemple 2 : products/{date}/product-list.csv
</tr>
<tr>
<td>Format de date [facultatif]</td>
<td>Si vous avez utilisé le {date} dans le modèle de chemin d’accès que vous avez spécifié, vous pouvez sélectionner le format de date dans laquelle les fichiers sont organisés dans la liste déroulante des formats pris en charge. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [facultatif]</td>
<td>Si vous avez utilisé {time} dans le modèle de chemin d’accès que vous avez spécifié, vous pouvez sélectionner le format dans lequel les fichiers sont organisés dans la liste déroulante des formats pris en charge. Exemple : HH</td>
</tr>
<tr>
<td>Format de sérialisation d’événement</td>
<td>Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de connaître le format de sérialisation, vous utilisez des flux de données entrantes. Pour les données de référence, les formats pris en charge sont CSV et JSON.</td>
</tr>
<tr>
<td>Codage</td>
<td>UTF-8 est le seul format de codage pris en charge pour l’instant</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Génération de données de référence d’une planification

Si vos données de référence sont un ensemble de données variation lente, prise en charge pour l’actualisation des données sont activées en spécifiant un motif de tracé dans la configuration d’entrée utilisant la {date} de référence et {temps} les variables de substitution. Flux de données Analytique reprendra les définitions de données de référence mis à jour en fonction de ce modèle de chemin d’accès. Par exemple, un modèle de `sample/{date}/{time}/products.csv` avec un format de date de **« AAAA-MM-JJ »** et une fois le format **«** hh : mm » indique à Analytique de flux pour récupérer le blob mis à jour `sample/2015-04-16/17:30/products.csv` à 5 h 30 avril, 2015 16e UTC de fuseau horaire.

> [AZURE.NOTE] Actuellement les tâches de flux de données Analytique recherchez l’actualisation de l’objet blob uniquement lorsque l’ordinateur passe à l’heure codée dans le nom de l’objet blob. Par exemple le travail de recherche `sample/2015-04-16/17:30/products.csv` dès que possible, mais non antérieure à 5 h 30 avril, 2015 16e UTC fuseau horaire. Il sera *jamais* l’aspect d’un fichier avec une heure codée précédemment que la dernière qui est détecté.
> 
> Par exemple une fois la tâche recherche le blob `sample/2015-04-16/17:30/products.csv` il ignore tous les fichiers portant une date codée antérieure à 5 h 30 2015 de 16 avril si une arrivée tardive `sample/2015-04-16/17:25/products.csv` blob est créé dans le même conteneur la tâche ne l’utilise pas.
> 
> Même si `sample/2015-04-16/17:30/products.csv` est produit uniquement à 10 h 03 2015 de 16 avril, mais aucun objet blob avec une date antérieure n’est présent dans le conteneur, le travail utiliseront ce fichier à partir de 10 h 03 2015 de 16 avril et les données de référence précédente jusqu’alors.
> 
> Une exception à cette règle est lorsque le travail doit traiter de nouveau les données dans le temps ou lorsque la tâche est d’abord démarré. À l’heure de début de que la tâche de recherche la plus récent blob produit avant la tâche heure de début spécifiée. Pour ce faire pour vous assurer qu’il existe un jeu de données de référence **non vide** au démarrage de la tâche. Si un ne peut pas être trouvé, la tâche affiche les tests de diagnostic suivants : `Initializing input without a valid reference data blob for UTC time <start time>`.


[Usine de données Azure](https://azure.microsoft.com/documentation/services/data-factory/) permet d’orchestrer la tâche de création des mise à jour BLOB requis par flux Analytique pour mettre à jour les définitions de données de référence. Usine de données est un service d’intégration de données basé sur le cloud qui orchestre et automatise le déplacement et la transformation des données. Usine de données prend en charge la [connexion à un grand nombre de banques de données locales et de nuage basé](../data-factory/data-factory-data-movement-activities.md) et déplacement des données facilement à intervalles réguliers que vous spécifiez. Pour plus d’informations et des instructions étape par étape comment configurer un pipeline de données usine pour générer des données de référence pour l’Analytique de flux de données qui est actualisé selon une planification prédéfinie, consultez cet [exemple de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Conseils sur l’actualisation de vos données de référence ##

1. Remplacement des blobs de données de référence ne provoque pas Analytique de flux recharger le blob, et dans certains cas, il peut provoquer l’échec du travail. Il est recommandé de modifier les données de référence pour ajouter un nouvel blob en utilisant le même conteneur et modèle de chemin d’accès défini dans l’entrée de la tâche et utiliser une date/heure **supérieure** à celle spécifiée par la dernière blob dans la séquence.
2.  BLOB n’est pas classées par heure de « Dernière modification » de l’objet blob, mais uniquement par l’heure et la date spécifiée dans l’objet blob de données de référence de nom à l’aide de la {date} et {temps} les substitutions.
3.  Parfois quelques, qu'une tâche doit revenir en arrière dans le temps, par conséquent BLOB des données de référence ne doivent pas modifiés ou supprimés.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez été introduit pour l’Analytique des flux de données, un service managé pour la diffusion analytique des données à partir de l’Internet des objets. Pour en savoir plus sur ce service, voir :

- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
