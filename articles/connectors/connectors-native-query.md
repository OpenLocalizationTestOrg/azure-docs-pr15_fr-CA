<properties
    pageTitle="Ajoutez l’action de requête dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble de l’action de requête pour effectuer des actions comme tableau de filtre."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-query-action"></a>Mise en route de l’action de requête

À l’aide de l’action de requête, vous pouvez travailler avec les lots et les tableaux pour accomplir des flux de travail :

- Créer une tâche pour tous les enregistrements de haute priorité à partir d’une base de données.
- Enregistrer les pièces jointes PDF tous les messages e-mail dans un blob Azure.

Pour démarrer à l’aide de l’action de requête dans une logique d’application, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-query-action"></a>Utilisez l’action de requête

Une action est une opération qui est réalisée par le flux de travail est défini dans une logique d’application. [En savoir plus sur les actions](connectors-overview.md).  

L’action de requête a actuellement une seule opération, appelée le tableau filtre, ce qui est exposé dans le concepteur. Cela vous permet d’interroger un tableau et de retourner un jeu de résultats filtrés.

Voici comment vous pouvez l’ajouter dans une logique d’application :

1. Cliquez sur le bouton **Nouvelle étape** .
2. Cliquez sur **Ajouter une action**.
3. Dans la zone de recherche action, tapez le **filtre** pour l’action du **tableau de filtres** de la liste.

    ![Sélectionnez l’action de requête](./media/connectors-native-query/using-action-1.png)

4. Sélectionnez un tableau pour filtrer. (La capture d’écran suivante affiche le tableau des résultats de la recherche Twitter).
5. Créer une condition à évaluer pour chaque élément. (Filtres de la capture d’écran suivante tweet aux utilisateurs disposant de plus de 100 sont des suiveurs.)

    ![Effectuer l’action de requête](./media/connectors-native-query/using-action-2.png)

    L’action génère un nouveau tableau contenant uniquement les résultats répondant aux exigences du filtre.
6. Cliquez sur le coin supérieur gauche de la barre d’outils pour enregistrer et votre application logique est à la fois enregistrer et publier (activer).

## <a name="query-action"></a>Requête action

Voici les détails de l’action qui prend en charge de ce connecteur. Le connecteur possède une action possible.

|Action|Description|
|---|---|
|Tableau de filtres|Évalue une condition pour chaque élément dans un tableau et renvoie les résultats|

## <a name="action-details"></a>Détails de l’action

L’action de requête est fourni avec une action possible. Les tableaux suivants décrivent les champs d’entrée requis et facultatifs pour l’action et les détails de sortie correspondants qui sont associés à l’aide de l’action.

### <a name="filter-array"></a>Tableau de filtres
Les éléments suivants sont les champs d’entrée de l’action, ce qui rend les demandes HTTP sortantes.
A * signifie qu’il est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|À partir de *|De|Le tableau pour filtrer|
|Condition *|où|La condition à évaluer pour chaque élément.|
<br>

### <a name="output-details"></a>Détails de sortie

Vous trouverez ci-dessous des informations de sortie pour la réponse HTTP.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Tableau filtré|tableau|Un tableau qui contient un objet pour chaque résultat filtré|

## <a name="next-steps"></a>Étapes suivantes

Maintenant, essayez la plate-forme et de [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications de la logique en consultant notre [liste d’API](apis-list.md).
