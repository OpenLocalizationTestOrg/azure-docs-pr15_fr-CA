<properties 
   pageTitle="Journaux personnalisés dans le journal Analytique | Microsoft Azure"
   description="Analytique de journal peut collecter des événements à partir des fichiers texte sur des ordinateurs Windows et Linux.  Cet article décrit comment définir un nouveau journal personnalisé et les détails des enregistrements qu’ils créent dans le référentiel de l’OMS."
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

# <a name="custom-logs-in-log-analytics"></a>Journaux personnalisés dans le journal Analytique

La source de données dans le journal Analytique des personnalisé vous permet de collecter des événements à partir des fichiers texte sur des ordinateurs Windows et Linux. De nombreuses applications consigne les informations dans des fichiers texte au lieu des services de journalisation standard, tels que le journal des événements Windows ou le journal système.  Une fois collectées, vous pouvez analyser chaque enregistrement dans le journal en champs individuels à l’aide de la fonctionnalité de [Champs personnalisés](log-analytics-custom-fields.md) de journal Analytique.

![Collection de journal personnalisé](media/log-analytics-data-sources-custom-logs/overview.png)

Les fichiers journaux doivent être collectés respecter les critères suivants.

- Le journal doit avoir une seule entrée par ligne ou utiliser un horodatage correspondant à un des formats suivants au début de chaque entrée.

    AAAA-MM-JJ HH : MM : <br>
  J/AAAA HH : MM : SS AM/PM <br>
  Mois jj, aaaa hh : mm :
    
- Le fichier journal ne doit pas autoriser les mises à jour circulaires où le fichier est remplacé par de nouvelles entrées. 

## <a name="defining-a-custom-log"></a>Définition d’un journal personnalisé

Utilisez la procédure suivante pour définir un fichier journal personnalisé.  Faites défiler jusqu'à la fin de cet article pour une procédure pas à pas d’un exemple d’ajout d’un journal personnalisé.

### <a name="step-1-open-the-custom-log-wizard"></a>Étape 1. Ouvrir l’Assistant de journal personnalisé

L’Assistant journal personnalisé s’exécute dans le portail de l’OMS et vous permet de définir un nouveau journal personnalisé pour collecter.

1.  Dans le portail de l’OMS, cliquez sur **paramètres**.
2.  Cliquez sur **données** , puis **les journaux personnalisés**.
3.  Par défaut, toutes les modifications de configuration sont automatiquement envoyées à tous les agents.  Pour les agents de Linux, un fichier de configuration est envoyé pour le collecteur de données Fluentd.  Si vous souhaitez modifier ce fichier manuellement sur chaque agent de Linux, puis désactivez la case *appliquer sous configuration de mes machines Linux*.
4.  Cliquez sur **Ajouter +** pour ouvrir l’Assistant de journal personnalisé.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Étape 2. Charger et analyser un exemple de journal

Pour commencer, téléchargez un exemple du journal personnalisé.  L’Assistant va analyser et afficher les entrées dans ce fichier pour valider.  Analytique de journal utilisera le délimiteur que vous spécifiez pour chaque enregistrement.

**Nouvelle ligne** est le délimiteur par défaut et est utilisé pour les fichiers journaux qui ont une seule entrée par ligne.  Si la ligne commence par une date et une heure dans un des formats disponibles, vous pouvez spécifier un séparateur **d’horodatage** qui prend en charge les entrées qui couvrent plus d’une ligne. 

Si un séparateur de timestamp est utilisé, puis la propriété TimeGenerated de chaque enregistrement stocké dans OMS prendra par la date et l’heure spécifiée pour cette entrée dans le fichier journal.  Si un nouveau séparateur de ligne est utilisé, TimeGenerated est rempli avec la date et l’heure qu’Analytique du journal collectées à l’entrée. 

>[AZURE.NOTE]Analytique de journal traite actuellement l’heure collectée à partir d’un journal à l’aide d’un délimiteur de l’horodatage en heure UTC.  Cela deviendra plus rapidement pour utiliser le fuseau horaire de l’agent. 
 
1.  Cliquez sur **Parcourir** et accédez à un fichier d’exemple.  Notez que cela peut bouton peut-être être étiqueté **Choisir un fichier** dans certains navigateurs.
2.  Cliquez sur **suivant**. 
3.  L’Assistant de journal personnalisé télécharger le fichier et répertorier les enregistrements qu’il identifie.
4.  Changer le délimiteur qui est utilisé pour identifier un nouvel enregistrement et sélectionnez le délimiteur qui mieux identifie les enregistrements dans votre fichier journal.
5.  Cliquez sur **suivant**.

### <a name="step-3-add-log-collection-paths"></a>Étape 3. Ajouter des chemins de collection de journaux

Vous devez définir un ou plusieurs tracés sur l’agent qui permet de localiser le journal personnalisé.  Vous pouvez fournir un nom pour le fichier journal et un chemin d’accès spécifique, ou vous pouvez spécifier un chemin d’accès avec un caractère générique pour le nom.  Il prend en charge les applications qui créent un nouveau fichier chaque jour ou lorsqu’un fichier atteint une certaine taille.  Vous pouvez également fournir plusieurs chemins d’accès pour un seul fichier journal.

Par exemple, une application peut créer un fichier journal chaque jour avec la date incluse dans le nom comme dans log20100316.txt. Un modèle d’un journal de ce type peut être *journal\*.txt* qui s’appliquent à n’importe quel fichier journal après l’application du schéma de dénomination.

Le tableau suivant fournit des exemples de séquences valides pour spécifier des fichiers journaux différents. 

| Description | Chemin d’accès |
|:--|:--|
| Tous les fichiers de *C:\Logs* , avec l’extension .txt sur l’agent Windows | C:\Logs\\\*.txt |
| Tous les fichiers stockés dans *C:\Logs* avec un nom commençant par les journaux et une extension .txt sur l’agent Windows | C:\Logs\log\*.txt |
| Tous les fichiers */var/log/audit* avec une extension .txt sur l’agent de Linux | /var/log/audit/*.txt |
| Tous les fichiers de */var/log/audit* avec un nom commençant par une extension .txt sur l’agent de Linux et de journal | /var/log/audit/log\*.txt |
  

1.  Sélectionnez Windows ou Linux pour spécifier le format du chemin d’accès que vous ajoutez.
2.  Tapez le chemin d’accès et cliquez sur le **+** bouton.
3.  Répétez le processus pour les chemins d’accès supplémentaires.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Étape 4. Fournir un nom et une description pour le journal

Le nom que vous spécifiez servira pour le type de journal comme décrit ci-dessus.  Il se termine toujours par _CL pour le distinguer comme un journal personnalisé.

1.  Tapez un nom pour le journal.  La ** \_CL** suffixe est fournie automatiquement.
2.  Ajoutez éventuellement une **Description**.
3.  Cliquez sur **suivant** pour enregistrer la définition de journal personnalisé.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Étape 5. Valider que les journaux personnalisés sont collectés
Il peut prendre une heure pour que les données initiales d’un nouveau journal personnalisé apparaissent dans le journal Analytique.  Il démarrera la collecte des entrées des journaux dans le chemin d’accès que vous avez spécifié à partir du point que vous avez défini le journal personnalisé.  Elle ne conservera pas les entrées que vous avez téléchargé lors de la création du journal personnalisé, mais il collecte des entrées existantes déjà dans les fichiers journaux qui localise.

Une fois le journal Analytique commence à collecter dans le journal personnalisé, ses enregistrements sera disponibles avec une recherche dans un journal.  Utiliser le nom que vous avez donné le journal personnalisé comme **Type** de votre requête.

>[AZURE.NOTE] Si la propriété RawData est manquante dans la recherche, vous devrez peut-être fermer et rouvrir votre navigateur.

### <a name="step-6-parse-the-custom-log-entries"></a>Étape 6. Analyser les entrées du journal personnalisé

L’entrée de la totalité du journal est stockée dans une propriété unique appelée **RawData**.  Vous souhaiterez probablement séparer les différents éléments d’information dans chaque entrée en propriétés individuelles stockées dans l’enregistrement.  Vous le faire en utilisant la fonction de [Champs personnalisés](log-analytics-custom-fields.md) de journal Analytique.

Les étapes détaillées pour l’analyse de l’entrée de journal personnalisé ne sont pas fournies ici.  Reportez-vous à la documentation des [Champs personnalisés](log-analytics-custom-fields.md) pour ces informations.

## <a name="disabling-a-custom-log"></a>La désactivation d’un journal personnalisé

Vous ne pouvez pas supprimer une définition de journal personnalisé une fois qu’il a été créé, mais vous pouvez la désactiver en supprimant tous ses chemins d’accès de la collection.

1.  Dans le portail de l’OMS, cliquez sur **paramètres**.
2.  Cliquez sur **données** , puis **les journaux personnalisés**.
3.  En regard de la définition de journal personnalisé pour la désactiver, cliquez sur **Détails** .
4.  Supprimez tous les chemins d’accès de collection pour la définition de journal personnalisé.


## <a name="data-collection"></a>Collecte de données

Analytique de journal collecte des nouvelles entrées dans chaque journal personnalisé environ toutes les 5 minutes.  L’agent enregistre sa place dans chaque fichier journal qu’il collecte à partir de.  Si l’agent est mis hors connexion pendant une période de temps, puis journal Analytique collecte des entrées à partir de l’endroit où il s’était arrêté, même si ces entrées ont été créées alors que l’agent était hors ligne.

Tout le contenu de l’entrée du journal est écrits dans une propriété unique appelée **RawData**.  Vous pouvez analyser cette dans plusieurs propriétés qui peuvent être analysées et traitées séparément par définition de [Champs personnalisés](log-analytics-custom-fields.md) après avoir créé le journal personnalisé.


## <a name="custom-log-record-properties"></a>Propriétés d’enregistrement de journal personnalisé

Les enregistrements de journal personnalisé ont un type avec le nom du journal que vous fournissez et les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| TimeGenerated | Date et heure auxquelles l’enregistrement a été collecté par journal Analytique.  Si le journal utilise un délimiteur de l’heure il s’agit de l’heure collectée à partir de l’entrée. |
| SourceSystem  | Type de l’enregistrement ont été collecté à partir de l’agent. <br> OpsManager – l’agent Windows, soit directement connecter ou SCOM <br> Linux – tous les agents de Linux  |
| RawData             | Texte complet de l’entrée collecte. |
| ManagementGroupName | Nom du groupe de gestion pour les agents SCOM.  Pour les autres agents, il s’agit d’AOI -\<ID de l’espace de travail\> |


## <a name="log-searches-with-custom-log-records"></a>Recherche de journal avec les enregistrements de journal personnalisé

Les enregistrements de journaux personnalisés sont stockés dans le référentiel de l’OMS à l’instar des enregistrements à partir de n’importe quelle autre source de données.  Ils auront un type correspondant au nom que vous fournissez lorsque vous définissez le journal, vous pouvez utiliser la propriété Type dans votre recherche pour extraire les enregistrements collectés à partir d’un journal spécifique.

Le tableau suivant fournit des exemples de recherche de journal qui extraient des enregistrements de journaux personnalisés.

| Requête | Description |
|:--|:--|
| Type = MyApp_CL | Tous les événements à partir d’un fichier journal nommé MyApp_CL. |
| Type = MyApp_CL Severity_CF = erreur | Tous les événements dans un journal personnalisé nommé MyApp_CL avec une valeur d' *erreur* dans un champ personnalisé nommé *Severity_CF*. |




## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Procédure pas à pas un exemple d’ajout d’un journal personnalisé

La section suivante vous guide à travers un exemple de création d’un journal personnalisé.  L’exemple de journal collectée a une seule entrée sur chaque ligne commençant par une date et heure, puis virgule délimitée par des champs pour code, état et le message.  Plusieurs exemples d’entrées sont affichées ci-dessous.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Charger et analyser un exemple de journal

Nous fournir un des fichiers journaux et que vous pouvez voir les événements qu’il va collecter.  Dans ce cas, nouvelle ligne est un délimiteur suffisant.  Si une seule entrée dans le journal peut couvrir plusieurs lignes, puis un délimiteur d’horodatage devra être utilisé.

![Charger et analyser un exemple de journal](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Ajouter des chemins de collection de journaux

Les fichiers journaux se situeront dans *C:\MyApp\Logs*.  Chaque jour, avec un nom qui inclut la date dans modèle *appYYYYMMDD.log*, un nouveau fichier sera créé.  Un motif suffisant pour ce journal serait *C:\MyApp\Logs\\\*.log*.

![Le chemin d’accès de journal collection](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Fournir un nom et une description pour le journal

Nous utilisons un nom de *MyApp_CL* et de type dans une **Description**.

![Nom du journal](media/log-analytics-data-sources-custom-logs/log-name.png)


### <a name="validate-that-the-custom-logs-are-being-collected"></a>Valider que les journaux personnalisés sont collectés

Nous utilisons une requête de *Type = MyApp_CL* pour renvoyer tous les enregistrements du journal collectées.

![Requête de journal avec des champs personnalisés](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analyser les entrées du journal personnalisé

Champs personnalisés nous permet de définir le *EventTime*, le *Code*, le *statut*et les champs de *Message* et nous pouvons voir la différence dans les enregistrements qui sont renvoyés par la requête.

![Requête de journal avec des champs personnalisés](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Étapes suivantes

- Utiliser [Des champs personnalisés](log-analytics-custom-fields.md) pour analyser les entrées dans le journal personnalisé en champs individuels.
- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions. 