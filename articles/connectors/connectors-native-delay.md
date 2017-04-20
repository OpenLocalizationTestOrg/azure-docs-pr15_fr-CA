<properties
    pageTitle="Ajouter un délai dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble du retard et retard-jusqu'à ce que les actions et comment les utiliser avec une application Azure logique."
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
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Mise en route avec le délai et le délai-jusqu'à ce que les actions

En utilisant le délai et « délai-jusqu'à ce que « actions, vous pouvez exécuter des scénarios de flux de travail.

Par exemple, vous pouvez :

- Attendez un jour ouvrable pour envoyer une mise à jour d’état par courrier électronique.
- Retarder le workflow jusqu'à ce qu’un appel HTTP ait le temps de finir avant la reprise et en récupérant le résultat.

Pour démarrer à l’aide de l’action de délai dans une logique d’application, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Utilisez les actions de retard

Une action est une opération qui est réalisée par le flux de travail est défini dans une logique d’application. [En savoir plus sur les actions](connectors-overview.md).

Voici un exemple de séquence de l’utilisation d’une étape de retard dans une logique d’application :

1. Après l’ajout d’un déclencheur, cliquez sur **Nouvelle étape** pour ajouter une action.
2. Recherche de **retard** afficher les actions de retard. Dans cet exemple, nous allons sélectionner le **délai**.

    ![Actions de retard](./media/connectors-native-delay/using-action-1.png)

3. Effectuer l’une des propriétés d’action pour configurer le délai.

    ![Configuration du délai](./media/connectors-native-delay/using-action-2.png)

4. Cliquez sur **Enregistrer** pour publier et activer l’application de la logique.


## <a name="action-details"></a>Détails de l’action

Le déclencheur de périodicité a les propriétés suivantes qui peuvent être configurées.

### <a name="delay-action"></a>Action de délai

Cette action diffère de l’exécution pour un intervalle de temps donné.
A * signifie qu’il est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Nombre *|nombre|Le nombre d’unités de temps de retard|
|Unité *|unité|L’unité de temps : `Second`, `Minute`, `Hour`, ou`Day`|
<br>

### <a name="delay-until-action"></a>Délai-jusqu'à ce que l’action

Cette action diffère de l’exécution jusqu'à une heure spécifiée.
A * signifie qu’il est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Année *|horodatage|L’année de différer jusqu'à (GMT)|
|Mois *|horodatage|Mois de retard jusqu'à (GMT)|
|Jour *|horodatage|Le premier jour de retard jusqu'à (GMT)|
<br>


## <a name="next-steps"></a>Étapes suivantes

Maintenant, essayez la plate-forme et de [créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications de la logique en consultant notre [liste d’API](apis-list.md).
