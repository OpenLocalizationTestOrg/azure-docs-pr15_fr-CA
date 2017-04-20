<properties
    pageTitle="Groupes d’ordinateurs dans l’Analytique de journal journal recherche | Microsoft Azure"
    description="Groupes d’ordinateurs dans le journal Analytique permettent à la portée des recherches de journaux à un ensemble particulier d’ordinateurs.  Cet article décrit les différentes méthodes que vous pouvez utiliser pour créer des groupes d’ordinateurs et comment les utiliser dans une recherche."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="bwren"/>

# <a name="computer-groups-in-log-analytics-log-searches"></a>Groupes d’ordinateurs dans le journal Analytique journal des recherches
Groupes d’ordinateurs dans le journal Analytique vous permettent de portée [session de recherche](log-analytics-log-searches.md) à un ensemble particulier d’ordinateurs.  Chaque groupe est rempli avec des ordinateurs soit à l’aide d’une requête que vous définissez ou en important des groupes à partir de différentes sources.  Lorsque le groupe est inclus dans une recherche, les résultats sont limités aux enregistrements qui correspondent à des ordinateurs dans le groupe.

## <a name="creating-a-computer-group"></a>Création d’un groupe d’ordinateurs
Vous pouvez créer un groupe d’ordinateurs dans Analytique du journal à l’aide d’une des méthodes dans le tableau suivant.  Pour plus d’informations sur chaque méthode sont fournis dans les sections ci-dessous. 

| Méthode | Description |
|:---|:---|
| Recherche de journal       | Créer une recherche qui retourne une liste d’ordinateurs et d’enregistrer les résultats sous la forme d’un groupe d’ordinateurs. |
| API de recherche de journal   | Utiliser l’API de recherche de journal pour créer par programme un groupe d’ordinateurs basé sur les résultats d’une recherche de journal. |
| Active Directory | Analyse automatiquement l’appartenance de groupe de tous les ordinateurs agents qui sont membres d’un domaine Active Directory et créer un groupe dans le journal Analytique pour chaque groupe de sécurité.
| WSUS              | Analyse des clients ou des serveurs WSUS pour cibler des groupes et créer un groupe dans le journal Analytique pour chaque automatiquement. |


### <a name="log-search"></a>Recherche de journal

Groupes d’ordinateurs créés à partir d’une recherche de journal contiendra tous les ordinateurs d’une requête de recherche que vous définissez.  Cette requête est exécutée chaque fois que le groupe d’ordinateurs est utilisé afin que toutes les modifications depuis la création du groupe apparaîtra.

Utilisez la procédure suivante pour créer un groupe d’ordinateurs à partir d’une recherche dans un journal.

1. [Créer une recherche](log-analytics-log-searches.md) qui retourne une liste d’ordinateurs.  La recherche doit retourner un ensemble distinct d’ordinateurs à l’aide de quelque chose comme **Ordinateur Distinct** ou **count() mesure par ordinateur** dans la requête.  
2. Cliquez sur le bouton **Enregistrer** en haut de l’écran.
3. Sélectionnez **Oui** pour **Enregistrer cette requête sous la forme d’un groupe d’ordinateurs :**.
4. Tapez un **nom** et une **catégorie** pour le groupe.  Si une recherche avec les mêmes nom et catégorie existe déjà, vous êtes invité à le remplacer.  Vous pouvez avoir plusieurs recherches portant le même nom dans différentes catégories. 

Voici des recherches d’exemple que vous pouvez enregistrer sous la forme d’un groupe d’ordinateurs.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>API de recherche de journal

Groupes d’ordinateurs créés avec les API de recherche de journal sont les mêmes que les recherches créés à l’aide d’une recherche dans un journal.

Pour plus d’informations sur la création d’un groupe d’ordinateurs à l’aide de l’API de recherche de journal voir [groupes d’ordinateurs dans l’Analytique de journal journal reste API de recherche](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory

Lorsque vous configurez Analytique du journal pour importer les appartenances aux groupes Active Directory, il analysera l’appartenance de groupe de tout ordinateur joint au domaine avec l’agent de l’OMS.  Un groupe d’ordinateurs est créé pour chaque groupe de sécurité dans Active Directory en journal Analytique, et chaque ordinateur est ajouté aux groupes d’ordinateurs correspondant aux groupes de sécurité qu’ils sont membres de.  Cet abonnement est mis continuellement à jour toutes les 4 heures.  

Vous configurez Analytique du journal pour importer des groupes de sécurité Active Directory dans le menu journal Analytique **paramètres de** **Groupes d’ordinateurs** .  Cliquez sur **automatisation** , puis **importation Active Directory appartenances aux groupes d’ordinateurs**.  Il n’y a aucune configuration supplémentaire requise.

![Groupes d’ordinateurs à partir d’Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Lorsque les groupes ont été importées, le menu répertorie le nombre d’ordinateurs avec l’appartenance au groupe a détecté et le nombre de groupes importés.  Vous pouvez cliquer sur un de ces liens pour retourner les enregistrements **ComputerGroup tant** avec ces informations.

### <a name="windows-server-update-service"></a>Service de mise à jour de Windows Server

Lorsque vous configurez Analytique du journal pour importer les appartenances aux groupes WSUS, il analysera l’appartenance de groupe de ciblage de tous les ordinateurs avec l’agent de l’OMS.  Si vous utilisez côté client cible, tout ordinateur qui est connecté à l’OMS et fait partie d’un WSUS ciblant des groupes aura son appartenance importé vers le journal Analytique. Si vous utilisez un serveur cible, l’OMS agent doit être installé sur le serveur WSUS afin que les informations d’appartenance de groupe être importées sur OMS.  Cet abonnement est mis continuellement à jour toutes les 4 heures. 

Vous configurez Analytique du journal pour importer des groupes de sécurité Active Directory dans le menu journal Analytique **paramètres de** **Groupes d’ordinateurs** .  Sélectionnez **Active Directory** , puis **importation Active Directory appartenances aux groupes d’ordinateurs**.  Il n’y a aucune configuration supplémentaire requise.

![Groupes d’ordinateurs à partir d’Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Lorsque les groupes ont été importées, le menu répertorie le nombre d’ordinateurs avec l’appartenance au groupe a détecté et le nombre de groupes importés.  Vous pouvez cliquer sur un de ces liens pour retourner les enregistrements **ComputerGroup tant** avec ces informations.

## <a name="managing-computer-groups"></a>Gestion des groupes d’ordinateurs

Vous pouvez afficher les groupes d’ordinateurs qui ont été créés à partir d’une recherche de journal ou de l’API de recherche de journal dans le menu journal Analytique **paramètres de** **Groupes d’ordinateurs** .  Cliquez sur le **x** dans la colonne **Supprimer** pour supprimer le groupe d’ordinateurs.  Cliquez sur l’icône **Afficher les membres** d’un groupe exécuter la recherche des journaux du groupe qui renvoie ses membres. 

![Groupes d’ordinateurs enregistrés](media/log-analytics-computer-groups/configure-saved.png)

Pour modifier le groupe, créez un nouveau groupe avec la même **catégorie** et le **nom** pour remplacer le groupe d’origine.

## <a name="using-a-computer-group-in-a-log-search"></a>Dans une recherche à l’aide d’un groupe d’ordinateurs
Vous utilisez la syntaxe suivante pour faire référence à un groupe d’ordinateurs dans une recherche.  Qui spécifie que la **catégorie** est facultative et uniquement requise si vous avez des groupes d’ordinateurs avec le même nom dans différentes catégories. 

    $ComputerGroups[Category: Name]

Lors de l’exécution d’une recherche, les membres des groupes d’ordinateurs inclus dans la recherche sont tout d’abord résolus.  Si le groupe est basé sur une recherche dans un journal, alors que la recherche est exécutée pour renvoyer les membres du groupe avant d’effectuer la recherche de journal de niveau supérieur.

Groupes d’ordinateurs sont généralement utilisés avec la clause **IN** dans les journaux comme dans l’exemple suivant.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Regrouper les enregistrements

Un enregistrement est créé dans le référentiel de l’OMS pour chaque groupe d’ordinateur créé à partir d’Active Directory ou WSUS.  Ces enregistrements ont un type de **ComputerGroup tant** et ont les propriétés dans le tableau suivant.  Les enregistrements ne sont pas créés pour des groupes d’ordinateurs basés sur des recherches de journaux.

| Propriété | Description |
|:--|:--|
| Type de                | *ComputerGroup tant* |
| SourceSystem        | *SourceSystem*  |
| Ordinateur            | Nom de l’ordinateur membre. |
| Groupe               | Nom du groupe. |
| GroupFullName       | Chemin d’accès complet au groupe, y compris la source et le nom de la source.
| GroupSource         | Ce groupe de la source a été collecté à partir de. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName     | Nom de la source qui a été prélevé selon les groupes.  Pour Active Directory, il s’agit du nom de domaine. |
| ManagementGroupName | Nom du groupe de gestion pour les agents SCOM.  Pour les autres agents, il s’agit d’AOI -\<ID de l’espace de travail\> |
| TimeGenerated       | Date et heure de création ou mise à jour du groupe d’ordinateurs. |



## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.  