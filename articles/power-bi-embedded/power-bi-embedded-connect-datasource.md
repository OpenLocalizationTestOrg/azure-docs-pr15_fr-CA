<properties
   pageTitle="Embedded Microsoft Power BI - connexion à une source de données"
   description="Puissance incorporées de BI, de se connecter aux sources de données"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Se connecter à une source de données

Avec **Alimentation BI incorporé**, vous pouvez incorporer des rapports dans votre application. Lorsque vous incorporez un état d’alimentation BI dans votre application, le rapport connecte aux données sous-jacentes en **important** une copie des données ou en **se connectant directement** à la source de données à l’aide de **DirectQuery**.

Voici les différences entre l’utilisation **d’importation** et de **DirectQuery**.

|Importation | DirectQuery
|---|---
|Tables, colonnes *et les données* sont importées ou copiées dans un groupe de données de l’état. Pour visualiser les modifications apportées aux données sous-jacentes, vous devez actualiser ou importer, un dataset terminé, en cours de nouveau.|Seules les *tables et les colonnes* sont importées ou copiées dans un groupe de données de l’état. Vous toujours Affichez les données les plus récentes.
Avec alimentation BI incorporé, vous pouvez utiliser DirectQuery avec les sources de données de nuage mais pas sur site les sources de données pour l’instant.

## <a name="benefits-of-using-directquery"></a>Avantages de l’utilisation de DirectQuery

Il existe deux avantages principaux lors de l’utilisation de **DirectQuery**:

   -    **DirectQuery** vous permet de créer des visualisations sur très grands ensembles de données, où il sinon serait première importation fonctionner toutes les données.
   -    Sous-jacent des modifications de données peut nécessiter une actualisation des données, et pour certains rapports, la nécessité d’afficher des données en cours peut nécessiter des transferts importants de données, ce qui fonctionner la ré-importation des données. En revanche, **DirectQuery** signale toujours les données en cours d’utilisation.

## <a name="limitations-of-directquery"></a>Limitations de DirectQuery

   Il existe quelques limitations à l’aide de **DirectQuery**:

   -    Toutes les tables doivent provenir d’une seule base de données.
   -    Si la requête est trop complexe, une erreur se produit. Pour remédier à l’erreur, vous devez refactoriser la requête afin qu’il soit moins complexe. Si le quuery doit être complexe, vous devrez importer les données au lieu d’utiliser **DirectQuery**.
   -    Filtrage de la relation est limité à une seule direction, plutôt que dans les deux directions.
   -    Vous ne pouvez pas modifier le type de données d’une colonne.
   -    Par défaut, les limites sont imposées à expressions DAX autorisées dans les mesures. Consultez [DirectQuery et mesures](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>DirectQuery et mesures

Pour garantir que les requêtes envoyées à la source de données sous-jacente ont des performances acceptables, les limitations sont imposées sur les mesures. Lorsque à l’aide de la **Puissance BI bureau**, utilisateurs expérimentés vous pouvez choisir de contourner cette limitation en choisissant **fichier > Options et paramètres > Options**. Dans la boîte de dialogue **Options** , cliquez sur **DirectQuery**et sélectionnez l’option **Autoriser les mesures non restreints en mode de DirectQuery**. Lorsque cette option est sélectionnée, toute expression de DAX qui est valide pour une mesure peut être utilisée. Les utilisateurs doivent être informés ; Toutefois, que certaines expressions qui effectuent très bien lorsque les données sont importées peut entraîner des requêtes très lents à la source back-end en mode de **DirectQuery** . 

## <a name="see-also"></a>Voir aussi
- [Mise en route de Microsoft d’alimentation BI incorporé](power-bi-embedded-get-started.md)
- [Bureau de BI de puissance](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
