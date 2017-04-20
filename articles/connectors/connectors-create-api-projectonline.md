<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Permet de créer des applications de logique avec le service d’application d’Azure. Projet en ligne est une solution en ligne flexible pour la gestion du portefeuille de projets (PPM) et le travail quotidien de Microsoft. Remis via Office 365, Project Online permet aux entreprises de mettre en route rapidement avec les fonctionnalités de gestion de projet puissant pour planifier, de hiérarchiser et de gérer des projets et des investissements de portefeuille de projet, à partir de presque n’importe où sur pratiquement n’importe quel périphérique."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-projectonline-connector"></a>Mise en route avec le connecteur de ProjectOnline

Projet en ligne est une solution en ligne flexible pour la gestion du portefeuille de projets (PPM) et le travail quotidien de Microsoft. Remis via Office 365, Project Online permet aux entreprises de mettre en route rapidement avec les fonctionnalités de gestion de projet puissant pour planifier, de hiérarchiser et de gérer des projets et des investissements de portefeuille de projet, à partir de presque n’importe où sur pratiquement n’importe quel périphérique.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2015-08-01-aperçu. 

Vous pouvez commencer par la création d’une application logique maintenant, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur de ProjectOnline peut être utilisé en tant qu’action ; Il a l’ou les déclencheurs. Tous les connecteurs prennent en charge les données au format JSON et XML. 

 Le connecteur de ProjectOnline a l’ou des actions et/ou les déclencheurs disponibles suivants :

### <a name="projectonline-actions"></a>Actions de ProjectOnline
Vous pouvez effectuer ces actions :

|Action|Description|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Répertorie les projets de votre site en ligne de projet|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Crée un nouveau projet dans votre site en ligne de projet|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Crée une nouvelle tâche dans votre projet|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Crée une ressource d’entreprise dans votre site en ligne de projet|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Répertorie les tâches dans un projet publiés|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Extrait d’un projet dans votre site.|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Archiver et publier un projet de votre site existant|
### <a name="projectonline-triggers"></a>Déclencheurs de ProjectOnline
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lors de la création d’un nouveau projet|Déclenche un flux dès la création d’un nouveau projet|
|Lors de la création d’une nouvelle ressource|Déclenche un nouveau flux lors de la création d’une nouvelle ressource|
|Lors de la création d’une nouvelle tâche|Déclenche un flux lorsqu’une nouvelle tâche est créée.|


## <a name="create-a-connection-to-projectonline"></a>Créer une connexion à ProjectOnline
Pour créer des applications de logique avec ProjectOnline, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification de ProjectOnline|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications de logique.

## <a name="reference-for-projectonline"></a>Référence ProjectOnline
S’applique à la version : 1.0

## <a name="onnewproject"></a>OnNewProject
Lors de la création d’un nouveau projet : déclenche un flux dès la création d’un nouveau projet 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="onnewresource"></a>OnNewResource
Lors de la création d’une nouvelle ressource : déclenche un nouveau flux lors de la création d’une nouvelle ressource 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="onnewtask"></a>OnNewTask
Lors de la création d’une nouvelle tâche : déclenche un flux lorsqu’une nouvelle tâche est créée. 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="listprojects"></a>ListProjects
Liste des projets : répertorie les projets de votre site en ligne de projet 

```GET: /_api/ProjectServer/Projects``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="createproject"></a>CreateProject
Crée le nouveau projet : crée un nouveau projet dans votre site en ligne de projet 

```POST: /_api/ProjectServer/Projects``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|proj| |Oui|corps|Aucun|Nouveau projet à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="createtask"></a>CreateTask
Crée une nouvelle tâche : crée une nouvelle tâche dans votre projet 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|ID unique du projet pour ajouter la tâche à|
|tâche| |Oui|corps|Aucun|Nouvelle tâche pour ajouter au projet|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="createresource"></a>CreateResource
Créer nouvelle ressource : crée les ressources d’une entreprise dans votre site en ligne de projet 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|ressources| |Oui|corps|Aucun|Nouvelle ressource d’entreprise à ajouter au projet|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="listtasks"></a>ListTasks
Répertorie les tâches : répertorie les tâches dans un projet publiés 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|ID unique du projet pour extraire des tâches|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="checkoutproject"></a>CheckoutProject
Extraire un projet : extrait d’un projet dans votre site. 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|ID unique du projet pour ajouter la tâche à|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="publishproject"></a>PublishProject
Consignation et publier le projet : archiver et publier et un projet de votre site existant 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine des url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|ID unique du projet pour archivage|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Non trouvé|
|500|Erreur de serveur interne. Une erreur inconnue s’est produite|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="triggerproject"></a>TriggerProject


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ProjectStartDate|chaîne|N° |
|ProjectFinishDate|chaîne|N° |
|ProjectCreatedDate|chaîne|N° |
|ProjectId|chaîne|N° |
|ProjectModifiedDate|chaîne|N° |
|ProjectType|nombre entier|N° |
|ProjectName|chaîne|N° |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="triggerresource"></a>TriggerResource


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ResourceId|chaîne|N° |
|ResourceBaseCalendar|chaîne|N° |
|ResourceBookingType|nombre entier|N° |
|ResourceCanLevel|valeur booléenne|N° |
|ResourceCostPerUse|numéro de|N° |
|ResourceCreatedDate|chaîne|N° |
|ResourceEarliestAvailableFrom|chaîne|N° |
|ResourceEmail|chaîne|N° |
|ResourceInitials|chaîne|N° |
|ResourceIsActive|valeur booléenne|N° |
|ResourceIsGeneric|valeur booléenne|N° |
|ResourceLatestAvailableTo|chaîne|N° |
|ResourceModifiedDate|chaîne|N° |
|Nom_ressource|chaîne|N° |
|ResourceStatsuName|chaîne|N° |
|Type de ressource|nombre entier|N° |
|Type de ChampDescription|chaîne|N° |
|TypeName|chaîne|N° |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="triggertask"></a>TriggerTask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ProjectId|chaîne|N° |
|Identificateur de tâche|chaîne|N° |
|ProjectName|chaîne|N° |
|Nom de la tâche|chaîne|N° |
|TaskCreatedDate|chaîne|N° |
|TaskModifieddate|chaîne|N° |
|TaskStartDate|chaîne|N° |
|TaskFinishDate|chaîne|N° |
|TaskPriority|nombre entier|N° |
|TaskIsActive|valeur booléenne|N° |



### <a name="newproject"></a>NewProject


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|Oui |
|Description|chaîne|N° |
|Début|chaîne|N° |



### <a name="newreource"></a>NewReource


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|Oui |
|IsBudget|valeur booléenne|N° |
|IsGeneric|valeur booléenne|N° |
|IsInactive|valeur booléenne|N° |



### <a name="project"></a>Projet


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ApprovedStart|chaîne|N° |
|ApprovedEnd|chaîne|N° |
|CheckedOutDate|chaîne|N° |
|CheckOutDescription|chaîne|N° |
|CheckOutId|chaîne|N° |
|CreatedDate|chaîne|N° |
|ID|chaîne|N° |
|IsCheckedOut|valeur booléenne|N° |
|LastPublishedDate|chaîne|N° |
|DateDernierEnreg|chaîne|N° |
|OptimizerDecision|nombre entier|N° |
|PlannerDecision|nombre entier|N° |
|ProjectType|nombre entier|N° |
|Nom|chaîne|N° |
|WinprojVersion|chaîne|N° |



### <a name="projectswrapper"></a>ProjectsWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="newtask"></a>NewTask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|paramètres|non défini|Oui |



### <a name="taskparameters"></a>TaskParameters


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|Oui |
|Notes|chaîne|N° |
|Début|chaîne|N° |
|Durée|chaîne|N° |



### <a name="enterpriseresource"></a>EnterpriseResource


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|CanLevel|valeur booléenne|N° |
|Code|chaîne|N° |
|CostAccrual|nombre entier|N° |
|Centre de coût|chaîne|N° |
|Créé|chaîne|N° |
|DefaultBookingType|nombre entier|N° |
|Messagerie|chaîne|N° |
|ExternalId|chaîne|N° |
|Groupe|chaîne|N° |
|Date embauche|chaîne|N° |
|ID|chaîne|N° |
|Initiales|chaîne|N° |
|IsActive|valeur booléenne|N° |
|IsBudget|valeur booléenne|N° |
|IsCheckedOut|valeur booléenne|N° |
|IsGeneric|valeur booléenne|N° |
|IsTeam|valeur booléenne|N° |
|MaterialLabel|chaîne|N° |
|Modifié|chaîne|N° |
|Nom|chaîne|N° |
|Phonétique|chaîne|N° |
|Type de ressource|nombre entier|N° |
|TerminationDate|chaîne|N° |



### <a name="taskswrapper"></a>TasksWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="task"></a>Tâche


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Créé|chaîne|N° |
|Modifié|chaîne|N° |
|Début|chaîne|N° |
|Terminer|chaîne|N° |
|Nom|chaîne|N° |
|ID|chaîne|N° |
|Priorité|nombre entier|N° |
|PercentComplete|nombre entier|N° |
|Notes|chaîne|N° |
|Contact|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)