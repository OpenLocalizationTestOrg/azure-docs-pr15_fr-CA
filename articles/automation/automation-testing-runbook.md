<properties 
    pageTitle="Tester une procédure opérationnelle dans Azure automatisation | Microsoft Azure"
    description="Avant de publier une procédure opérationnelle dans Azure Automation, vous pouvez le tester pour vous assurer que le fonctionne comme prévu.  Cet article décrit comment tester une procédure opérationnelle et consulter sa sortie."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="testing-a-runbook-in-azure-automation"></a>Tester une procédure opérationnelle dans Azure Automation
Lorsque vous testez une procédure opérationnelle, l’exécution de la [version brouillon](automation-creating-importing-runbook.md#publishing-a-runbook) et toutes les actions qu’il exécute sont terminées. Aucun historique de projet n’est créé, mais les flux de [sortie](automation-runbook-output-and-messages.md#output-stream) et [d’erreur et avertissement](automation-runbook-output-and-messages.md#message-streams) sont affichés dans le Test de volet de sortie. Le [flux détaillé](automation-runbook-output-and-messages.md#message-streams) des messages sont affichent dans le volet sortie uniquement si la [variable de $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) est définie sur Continue.

Bien que le brouillon est en cours d’exécution, la procédure opérationnelle exécute le flux de travail normalement toujours et effectue une action par rapport aux ressources de l’environnement. Pour cette raison, vous devez uniquement tester les procédures opérationnelles dans les ressources de production non.

La procédure de test de chaque [type de procédure opérationnelle](automation-runbook-types.md) est identique, et qu’il n’y a pas de différence dans le test entre l’éditeur de texte et de l’éditeur de graphique dans le portail Azure.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Pour tester une procédure opérationnelle dans le portail Azure

Vous pouvez travailler avec n’importe quel [type de procédure opérationnelle](automation-runbook-types.md) dans le portail Azure.

1. Ouvrez le brouillon de la procédure opérationnelle dans [l’éditeur de texte](automation-editing-a-runbook.md#Portal) ou [l’éditeur de graphique](automation-graphical-authoring-intro.md).
2. Cliquez sur le bouton **Test** pour ouvrir la lame de Test.
3. Si la procédure opérationnelle possède des paramètres, ils seront afficheront dans le volet gauche, où vous pouvez fournir des valeurs à utiliser pour le test.
4. Si vous souhaitez exécuter le test sur un [Travailleur de procédure opérationnelle hybride](automation-hybrid-runbook-worker.md), puis modifier les **Paramètres d’exécution** **Hybride** travailleur et sélectionnez le nom du groupe cible.  Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le nuage.
5. Cliquez sur le bouton **Démarrer** pour démarrer le test.
6. Si la procédure opérationnelle est [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou un [graphique](automation-runbook-types.md#graphical-runbooks), vous pouvez arrêter ou suspendre pendant qu’il est testé avec les boutons situé sous le volet de sortie. Lorsque vous interrompez la procédure opérationnelle, il termine l’activité en cours avant d’en cours de suspension. Une fois la procédure opérationnelle est interrompue, vous pouvez l’arrêter ou le redémarrer.
7. Examinez la sortie de la procédure opérationnelle dans le volet de sortie.


## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer ou importer une procédure opérationnelle, reportez-vous à la section [Création ou importation d’une procédure opérationnelle dans Azure Automation](automation-creating-importing-runbook.md)
- Pour en savoir plus sur la création de graphiques, voir [Graphical authoring dans Azure Automation](automation-graphical-authoring-intro.md)
- Pour vous familiariser avec les procédures opérationnelles de flux de travail de PowerShell, voir [Ma première procédure de flux de travail de PowerShell opérationnelle](automation-first-runbook-textual.md)
- Pour en savoir plus sur la configuration de runboks pour renvoyer des messages d’état et les erreurs, y compris les pratiques recommandées, voir la [sortie de procédure opérationnelle et messages dans Azure Automation](automation-runbook-output-and-messages.md)