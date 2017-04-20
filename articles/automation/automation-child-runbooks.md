<properties 
   pageTitle="Procédures opérationnelles des enfants dans Azure automatisation | Microsoft Azure"
   description="Décrit les différentes méthodes de démarrage d’une procédure opérationnelle dans Azure Automation à partir d’une autre procédure d’opérationnelle et de partager des informations entre eux."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Procédures opérationnelles des enfants dans Azure Automation

Il est conseillé dans Automation Azure pour écrire des procédures opérationnelles de réutilisables et modulaires avec une fonction séparée qui peut être utilisée par d’autres procédures opérationnelles. Une procédure opérationnelle du parent appelle souvent une ou plusieurs procédures opérationnelles des enfants pour exécuter la fonctionnalité requise. Il existe deux manières d’appeler une procédure opérationnelle d’enfants, et chacun a des grandes différences que vous devez comprendre afin que vous puissiez déterminer qui vous convient le mieux pour vos différents scénarios.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Appel d’une procédure opérationnelle d’enfant à l’aide de l’exécution inline

Pour appeler une procédure opérationnelle inline à partir d’une autre procédure d’opérationnelle, vous utilisez le nom de la procédure opérationnelle et fournissez des valeurs pour ses paramètres exactement comme vous utiliseriez une activité ou une applet de commande.  Procédures opérationnelles de tous dans le même compte d’Automation sont disponibles pour tous les autres pour être utilisé de cette manière. La procédure opérationnelle parent attend la procédure opérationnelle d’enfant à terminer avant de passer à la ligne suivante, et toute sortie est renvoyée directement au parent.

Lorsque vous appelez une procédure opérationnelle inline, il s’exécute dans le même travail que la procédure opérationnelle parent. Il n’y aura aucun indication de la procédure opérationnelle enfant qu’il le faisait dans l’historique des travaux. Toutes les exceptions et les flux de sortie à partir de la procédure opérationnelle enfant sera associés avec le parent. Cela entraîne moins de tâches et les rend plus facile à suivre et à résoudre les problèmes dans la mesure où les exceptions levées par la procédure opérationnelle enfant et ses flux de production sont associées avec la tâche parent.

Lorsqu’une procédure opérationnelle est publiée, les procédures opérationnelles enfant qu’il appelle déjà publié. Il s’agit, car Azure Automation crée une association avec les procédures opérationnelles d’enfant lors de la compilation d’une procédure opérationnelle. Si elles ne le sont pas, la procédure opérationnelle parent semble publier correctement, mais génère une exception lorsqu’il est démarré. Dans ce cas, vous pouvez republier la procédure opérationnelle parent pour référencer correctement les procédures opérationnelles enfant. Vous n’avez pas besoin de republier la procédure opérationnelle parent si les procédures opérationnelles enfant sont modifiés, car l’association aura déjà été créée.

Les paramètres d’une procédure opérationnelle d’enfant appelé inline peuvent être de tout type de données, y compris les objets complexes, et il n’y a pas de [sérialisation JSON](automation-starting-a-runbook.md#runbook-parameters) étant lorsque vous démarrez la procédure opérationnelle via le portail de gestion Azure ou avec l’applet de commande Start-AzureRmAutomationRunbook.


### <a name="runbook-types"></a>Types de procédure opérationnelle

Les types peuvent s’appellent mutuellement :

- Une [procédure opérationnelle de PowerShell](automation-runbook-types.md#powershell-runbooks) et les [procédures opérationnelles graphique](automation-runbook-types.md#graphical-runbooks) peuvent appeler chaque en ligne (les deux sont en fonction de PowerShell).
- Une procédures de [procédure opérationnelle de flux de travail de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) et graphique du flux de travail PowerShell opérationnelles peut appeler chaque en ligne (les deux sont Workflow PowerShell basé)
- Les types de PowerShell et les types de flux de travail PowerShell ne peut pas appeler l’autre inline et doivent utiliser Start-AzureRmAutomationRunbook.
    
Lorsque ne publie pas question d’ordre :

- La commande Publier des procédures opérationnelles est important uniquement pour les procédures opérationnelles de PowerShell le flux de travail et flux de travail graphique PowerShell.


Lorsque vous appelez un graphique ou un Workflow de PowerShell enfant procédure opérationnelle à l’aide de l’exécution inline, vous utilisez simplement le nom de la procédure opérationnelle.  Lorsque vous appelez une procédure opérationnelle enfant de PowerShell, vous devez précéder son nom avec *.\\ * Pour spécifier que le script se trouve dans le répertoire local. 

### <a name="example"></a>Exemple

L’exemple suivant appelle une procédure opérationnelle enfant de test qui accepte trois paramètres, un objet complexe, un entier et une valeur booléenne. La sortie de la procédure opérationnelle enfant est assignée à une variable.  Dans ce cas, la procédure opérationnelle enfant est une procédure opérationnelle du flux de travail de PowerShell

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Voici le même exemple, à l’aide d’une procédure opérationnelle de PowerShell comme enfant.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Démarrage d’une procédure opérationnelle d’enfant à l’aide de la cmdlet

Vous pouvez utiliser l’applet de commande [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) pour démarrer une procédure opérationnelle, comme indiqué dans la zone [Démarrer une procédure opérationnelle avec Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Il existe deux modes d’utilisation de cette applet de commande.  Dans un mode, l’applet de commande renvoie l’id de la tâche dès que la tâche enfant est créée pour la procédure opérationnelle enfant.  Dans l’autre mode, vous activez en spécifiant le **-attente** paramètre, l’applet de commande attend jusqu'à ce que l’enfant de tâche se termine et retourne le résultat de la procédure opérationnelle enfant.

La tâche à partir d’une procédure opérationnelle d’enfant a démarré avec une applet de commande est exécutée dans une tâche distincte à partir de la procédure opérationnelle parent. Cela se traduit dans les tâches plus qu’appeler la procédure opérationnelle inline et les rend plus difficiles à suivre. Le parent peut démarrer plusieurs procédures opérationnelles d’enfant asynchrone sans attendre pour terminer. Pour ce même type d’exécution parallèle en appelant le procédures opérationnelles d’enfant en ligne, la procédure opérationnelle parent doit utiliser le [mot clé parallèle](automation-powershell-workflow.md#parallel-processing).

Les paramètres pour une procédure opérationnelle d’enfant a démarré avec une applet de commande sont fournis sous la forme d’une table de hachage comme indiqué dans les [Paramètres de procédure opérationnelle](automation-starting-a-runbook.md#runbook-parameters). Peuvent être utilisés uniquement les types de données simples. Si la procédure opérationnelle a un paramètre avec un type de données complexe, puis il doit être appelée en ligne.

### <a name="example"></a>Exemple

L’exemple suivant démarre une procédure opérationnelle d’enfants avec des paramètres, puis attend qu’elle se termine à l’aide de la AzureRmAutomationRunbook-début-paramètre d’attente. Une fois terminé, sa sortie est recueillie à partir de la procédure opérationnelle enfant.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparaison des méthodes d’appel d’une procédure opérationnelle d’enfant

Le tableau suivant résume les différences entre les deux méthodes pour appeler une procédure opérationnelle à partir d’une autre procédure d’opérationnelle.

| | En ligne| Applet de commande|
|:---|:---|:---|
|Travail|Procédures opérationnelles des enfants s’exécutent dans le même projet que le parent.|Un travail distinct est créé pour la procédure opérationnelle enfant.|
|Exécution de|Procédure opérationnelle du parent attend la procédure opérationnelle d’enfant à terminer avant de poursuivre.|Procédure opérationnelle du parent continue immédiatement après le démarrage de procédure opérationnelle d’enfant *ou de* procédure opérationnelle de parent attend la tâche enfant se termine.|
|Sortie|Procédure opérationnelle du parent peut obtenir directement la sortie de procédure opérationnelle des enfants.|Procédure opérationnelle du parent doit récupérer la sortie de procédure opérationnelle tâche enfant *ou* procédure opérationnelle du parent peut obtenir directement sortie de procédure opérationnelle des enfants.|
|Paramètres|Les valeurs pour les paramètres de procédure opérationnelle enfant sont spécifiés séparément et peuvent utiliser n’importe quel type de données.|Les valeurs pour les paramètres de procédure opérationnelle enfant doivent être combinés dans une seule table de hachage et peuvent inclure uniquement des simples, tableau, et que la sérialisation JSON exploiter les types de données de l’objet.|
|Compte d’Automation|Procédure opérationnelle du parent ne peut utiliser que des procédure opérationnelle d’enfants dans le même compte d’automation.|Procédure opérationnelle du parent permet de procédure opérationnelle des enfants à partir de n’importe quel compte d’automation à partir de la même abonnement Azure et même un autre abonnement si vous avez une connexion avec elle.|
|Publication|Procédure opérationnelle d’enfant doit être publié avant la publication de procédure opérationnelle du parent.|Procédure opérationnelle d’enfant doit être publié à tout moment avant le lancement de procédure opérationnelle du parent.|

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage d’une procédure opérationnelle dans Azure Automation](automation-starting-a-runbook.md)
- [Sortie de procédure opérationnelle et messages dans Azure Automation](automation-runbook-output-and-messages.md)
