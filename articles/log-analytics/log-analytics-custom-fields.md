<properties
   pageTitle="Champs personnalisés dans le journal Analytique | Microsoft Azure"
   description="La fonctionnalité de champs personnalisés de journal Analytique vous permet de vous permet de créer vos propres champs de recherche à partir de données OMS ajouter aux propriétés d’un enregistrement collecte.  Cet article décrit le processus pour créer un champ personnalisé et fournit une procédure pas à pas détaillé avec un exemple d’événement."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-fields-in-log-analytics"></a>Champs personnalisés dans le journal Analytique

La fonction de **Champs personnalisés** de journal Analytique vous permet d’étendre des enregistrements existants dans le référentiel de l’OMS en ajoutant vos propres champs de recherche.  Les champs personnalisés sont automatiquement remplies à partir des données extraites des autres propriétés dans le même enregistrement.

![Vue d’ensemble des champs personnalisé](media/log-analytics-custom-fields/overview.png)

Par exemple, l’exemple d’enregistrement ci-dessous a enfouies dans la description de l’événement de données utiles.  Extraction des données dans des propriétés séparées rend disponible pour des opérations telles que le tri et le filtrage.

![Bouton de recherche de journal](media/log-analytics-custom-fields/sample-extract.png)

>[AZURE.NOTE] Dans l’aperçu, vous êtes limité à 100 champs personnalisés dans votre espace de travail.  Cette limite est développée lorsque cette fonctionnalité atteint la disponibilité générale.

## <a name="creating-a-custom-field"></a>Création d’un champ personnalisé

Lorsque vous créez un champ personnalisé, journal Analytique doit comprendre les données à utiliser pour remplir sa valeur.  Il utilise une technologie de Microsoft Research appelé FlashExtract d’identifier rapidement ces données.  Au lieu de vous obliger à fournir des instructions explicites, journal Analytique apprend sur les données que vous souhaitez extraire des exemples que vous fournissez.

Les sections suivantes fournissent la procédure de création d’un champ personnalisé.  Au bas de cet article est une procédure pas à pas d’une extraction de l’échantillon.

> [!NOTE] Le champ personnalisé est rempli comme enregistrements correspondant aux critères spécifiés sont ajoutés à la banque de données OMS, donc il semblera seulement sur les enregistrements collectés après avoir créé le champ personnalisé.  Le champ personnalisé de ne pas être ajouté à des enregistrements qui sont déjà dans le magasin de données lors de sa création.

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Étape 1 : identifier les enregistrements dont le champ personnalisé
La première étape consiste à identifier les enregistrements qui obtient le champ personnalisé.  Vous démarrez avec une [recherche standard](log-analytics-log-searches.md) et que vous puis sélectionnez un enregistrement en tant que le modèle de journal Analytique apprendrez à partir de.  Lorsque vous spécifiez que vous souhaitez extraire les données dans un champ personnalisé, l' **Assistant d’Extraction de champ** est ouvert où valider et affiner les critères.

2. Accédez au **Journal recherche** et utiliser une [requête pour récupérer les enregistrements](log-analytics-log-searches.md) dont le champ personnalisé.
2. Sélectionnez un enregistrement de journal Analytique permet d’agir comme un modèle d’extraction de données pour remplir le champ personnalisé.  Vous allez identifier les données que vous souhaitez extraire à partir de cet enregistrement et Analytique de journal utilise ces informations pour déterminer la logique pour remplir le champ personnalisé pour tous les enregistrements similaires.
3. Cliquez sur le bouton à gauche de n’importe quelle propriété de texte de l’enregistrement et sélectionnez **extraire des champs à partir de**.
4. L' **Assistant d’Extraction de champ est ouvert**et l’enregistrement que vous avez sélectionné s’affiche dans la colonne **Exemple de Main** .  Le champ personnalisé est défini pour les enregistrements avec les mêmes valeurs dans les propriétés qui sont sélectionnées.  
5. Si la sélection n’est pas exactement ce que vous le souhaitez, sélectionnez des champs supplémentaires pour affiner les critères.  Pour modifier les valeurs de champ pour les critères, vous devez annuler et sélectionnez un autre enregistrement correspondant aux critères de que votre choix.

### <a name="step-2---perform-initial-extract"></a>Étape 2 : effectuer l’extraction initiale.
Une fois que vous avez identifié les enregistrements dont le champ personnalisé, vous identifiez les données que vous souhaitez extraire.  Analytique de journal utilisera ces informations pour identifier des modèles similaires dans des enregistrements similaires.  Dans l’étape après cela, vous serez en mesure de valider les résultats et fournissent des autres détails pour l’Analytique de journal à utiliser dans son analyse.

1. Mettez en surbrillance le texte dans l’exemple d’enregistrement que vous souhaitez remplir le champ personnalisé.  Vous obtiendrez une boîte de dialogue pour fournir un nom pour le champ et effectuer l’extrait initial.  Les caractères ** \_CF** seront automatiquement ajoutées.
2. Cliquez sur **Extraire** pour effectuer une analyse d’enregistrements collectés.  
3. Les sections **Résumé** et les **Résultats de la recherche** affichent les résultats de l’extrait pour vous pouvez vérifier son exactitude.  **Résumé** affiche les critères utilisés pour identifier des enregistrements et un nombre pour chacune des valeurs de données identifiés.  **Résultats de la recherche** fournit une liste détaillée des enregistrements correspondant aux critères.


### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Étape 3 : vérifier l’exactitude de l’extrait et créer le champ personnalisé

Une fois que vous avez effectué l’extraction initiale, journal Analytique affiche ses résultats en fonction des données qui ont été collectées.  Si les résultats de recherche précis vous pouvez créer le champ personnalisé avec aucun travail supplémentaire.  Si ce n’est pas le cas, puis vous pouvez affiner les résultats afin de journal Analytique peut améliorer sa logique.

2.  Si toutes les valeurs dans l’extrait initial ne sont pas correctes, puis cliquez sur l’icône **Modifier** en regard d’un enregistrement inexact et sélectionnez **modifier cette mise en surbrillance** pour modifier la sélection.
3.  L’entrée est copiée dans la section **des exemples supplémentaires** en dessous de l' **Exemple de la Main**.  Vous pouvez régler la surbrillance ici pour aider la sélection, il y a lieu de journal Analytique.
4.  Cliquez sur **Extraire** pour ces nouvelles informations permettent d’évaluer tous les enregistrements existants.  Les résultats peuvent être modifiées pour les enregistrements que celui que vous venez de modifier selon cette nouvelle intelligence.
5.  Continuez à ajouter des corrections jusqu'à ce que tous les enregistrements dans l’extrait d’identifient correctement les données pour remplir le champ personnalisé.
6. Lorsque vous êtes satisfait du résultat, cliquez sur **Enregistrer l’extraire** .  Le champ personnalisé est défini, mais il ne sont pas encore être ajouté à tous les enregistrements.
7.  Attendre pour les nouveaux enregistrements correspondant aux critères spécifiés pour être collectées et puis exécutez à nouveau la recherche du journal. Nouveaux enregistrements doivent avoir le champ personnalisé.
8.  Utilisez le champ personnalisé comme toute autre propriété d’enregistrement.  Vous pouvez l’utiliser pour les données d’agrégation et de groupe et même l’utiliser pour générer des idées nouvelles.


## <a name="viewing-custom-fields"></a>Affichage des champs personnalisés
Vous pouvez afficher une liste de tous les champs personnalisés dans votre groupe d’administration à partir de la fenêtre **paramètres** du tableau de bord OMS.  Sélectionnez les **données** , puis sur **champs personnalisés** pour obtenir la liste de tous les champs personnalisés dans votre espace de travail.  

![Champs personnalisés](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Suppression d’un champ personnalisé
Il existe deux façons de supprimer un champ personnalisé.  Le premier est l’option **Supprimer** pour chaque champ, lorsque vous affichez la liste complète, comme décrit ci-dessus.  L’autre méthode consiste à les récupérer un enregistrement et cliquez sur le bouton à gauche du champ.  Le menu aura une option pour supprimer le champ personnalisé.

## <a name="sample-walkthrough"></a>Procédure d’exemple

La section suivante vous guide à travers un exemple complet de création d’un champ personnalisé.  Cet exemple extrait le nom de service dans des événements de Windows indiquant un changement d’état de service.  Il s’appuie sur les événements créés par le Gestionnaire de contrôle de Service dans le journal système sur les ordinateurs Windows.  Si vous souhaitez suivre cet exemple, vous devez être [collecte les événements d’Information du journal système](log-analytics-data-sources-windows-events.md).

Nous permet d’entrer la requête suivante pour renvoyer tous les événements à partir du Gestionnaire de contrôle de Service ayant des ID d’événement 7036 qui est l’événement qui indique le service démarre ou s’arrête.

![Requête](media/log-analytics-custom-fields/query.png)

Ensuite, nous allons sélectionner n’importe quel enregistrement avec ID 7036 d’événement.

![Enregistrement de la source](media/log-analytics-custom-fields/source-record.png)

Nous souhaitons que le nom du service qui apparaît dans la propriété **RenderedDescription** et sélectionnez le bouton en regard de cette propriété.

![Extraire des champs](media/log-analytics-custom-fields/extract-fields.png)

Pour ouvrir l' **Assistant d’Extraction de champ** et les champs de **journal des événements** et **l’ID d’événement** sont sélectionnés dans la colonne **Exemple de Main** .  Cela indique que le champ personnalisé est défini pour les événements du journal système avec un ID d’événement de 7036.  Cela est suffisant pour nous n’est pas nécessaire de sélectionner d’autres champs.

![Exemple principal](media/log-analytics-custom-fields/main-example.png)

Nous mettez en surbrillance le nom du service dans la propriété **RenderedDescription** et **Service** permet d’identifier le nom du service.  Le champ personnalisé sera appelé **Service_CF**.

![Titre du champ](media/log-analytics-custom-fields/field-title.png)

Nous constatons que le nom du service est correctement identifié pour certains enregistrements, mais pas pour d’autres.   Les **Résultats de la recherche** indiquent que la partie du nom de la **Carte de Performance WMI** n’a pas été sélectionné.  Le **Résumé** indique que quatre enregistrements avec **DPRMA** service incluses à tort un mot supplémentaire, et deux enregistrements identifié **Installateur de Modules** au lieu de **l’Installateur de Modules Windows**.  

![Résultats de la recherche](media/log-analytics-custom-fields/search-results-01.png)

Nous allons commencer avec l’enregistrement de la **Carte de Performance WMI** .  Nous allons cliquer sur son icône de modification, puis **modifier cette mise en surbrillance**.  

![Modifier la mise en surbrillance](media/log-analytics-custom-fields/modify-highlight.png)

Nous augmentons la sélection pour inclure le mot **WMI** et exécutez à nouveau l’extrait.  

![Exemple supplémentaire](media/log-analytics-custom-fields/additional-example-01.png)

Nous pouvons voir que les entrées de la **Carte de Performance WMI** ont été corrigées et journal Analytique utilisé également ces informations pour corriger les enregistrements pour **Module de Windows Installer**.  Bien que ce **DPMRA** est toujours pas identifié correctement, nous pouvons le voir dans la section **Résumé** .

![Résultats de la recherche](media/log-analytics-custom-fields/search-results-02.png)

Nous avons accédé à un enregistrement avec le service DPMRA et utilisent le même processus pour corriger cet enregistrement.

![Exemple supplémentaire](media/log-analytics-custom-fields/additional-example-02.png)

 Lorsque nous exécutons l’extraction, nous pouvons voir que tous les résultats sont désormais corrects.

![Résultats de la recherche](media/log-analytics-custom-fields/search-results-03.png)

Nous pouvons voir que **Service_CF** est créé mais n’est pas encore ajouté à tous les enregistrements.

![Compteur initial](media/log-analytics-custom-fields/initial-count.png)

Après que quelques temps écoulé new, les événements sont collectés, nous pouvons voir que que le champ **Service_CF** est maintenant ajouté à des enregistrements qui correspondent à nos critères.

![Résultat final](media/log-analytics-custom-fields/final-results.png)

Nous pouvons à présent utiliser le champ personnalisé comme toute autre propriété de l’enregistrement.  Pour illustrer cela, nous créons une requête qui regroupe par le nouveau champ de **Service_CF** pour inspecter les services qui sont les plus actifs.

![Regrouper par requête](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) à créer des requêtes à l’aide des champs personnalisés pour des critères.
- Analyser [les fichiers journaux personnalisés](log-analytics-data-sources-custom-logs.md) que vous analysez à l’aide de champs personnalisés.