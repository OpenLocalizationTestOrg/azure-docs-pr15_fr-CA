<properties 
   pageTitle="Planification d’une procédure opérationnelle dans Azure automatisation | Microsoft Azure"
   description="Décrit comment créer un planning dans Azure Automation afin que vous pouvez démarrer automatiquement une procédure opérationnelle à un moment donné ou selon une planification périodique."
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
   ms.date="08/05/2016"
   ms.author="bwren" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Planification d’une procédure opérationnelle dans Azure Automation

Pour planifier une procédure opérationnelle dans Automation Azure commence à une heure spécifiée, liez-le à une ou plusieurs planifications. Un calendrier peut être configuré à exécution seule ou une pose à nouveau toutes les heures ou tous les jours de planification de procédures opérationnelles dans Azure portal classique et de procédures opérationnelles dans Azure portal, vous pouvez en outre les planifier pour toutes les semaines, tous les mois, certains jours de la semaine ou jours du mois ou un jour spécifique du mois.  Une procédure opérationnelle peut être lié à plusieurs planifications et une planification peut avoir plusieurs des procédures opérationnelles liées à celle-ci.


## <a name="creating-a-schedule"></a>Création d’une planification

Vous pouvez créer une planification pour les procédures opérationnelles dans Azure portal, dans le portail classique, ou avec Windows PowerShell. Vous avez également la possibilité de créer une nouvelle planification, lorsque vous liez une procédure opérationnelle à une planification de l’utilisation du portail classique ou Azure Azure.

>[AZURE.NOTE] Lorsque vous associez une planification avec une procédure opérationnelle, Automation stocke les versions actuelles des modules dans votre compte et les lie à cette planification.  Cela signifie que si vous aviez un module avec la version 1.0 de votre compte lorsque vous créé un tableau, puis mettre à jour le module avec la version 2.0, le programme continue à utiliser 1.0.  Pour utiliser la version du module de mise à jour, vous devez créer une nouvelle planification. 

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Pour créer une nouvelle planification dans Azure portal classique

1. Dans Azure portal classique, sélectionnez Automation et puis sélectionnez le nom d’un compte d’automation.
1. Sélectionnez l’onglet **ressources** .
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter la planification**.
1. Tapez un **nom** et éventuellement une **Description** pour le nouvel agenda de schedule.your s’exécute **Une seule fois**, **horaire**, **quotidienne**, **hebdomadaire**ou **mensuel**.
1. Spécifiez une **Heure de début** et autres options en fonction du type de calendrier que vous avez sélectionné.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Pour créer une nouvelle planification dans le portail Azure

1. Dans le portail d’Azure, à partir de votre compte d’automation, cliquez sur la mosaïque de **ressources** pour ouvrir la blade **d’actifs** .
2. Cliquez sur la mosaïque de **planifications** pour ouvrir la lame de **planifications** .
3. Cliquez sur **Ajouter une planification** dans la partie supérieure de la lame.
4. Sur la lame de **nouvelle planification** , tapez un **nom** et éventuellement une **Description** pour la nouvelle planification.
5. Permet de sélectionner si la planification s’exécute une seule fois ou selon un calendrier récurrents en sélectionnant **une seule fois** ou sur **périodicité**.  Si vous sélectionnez à **la fois** spécifier une **heure de début** , puis sur **créer**.  Si vous sélectionnez **récurrence**, spécifiez une **heure de début** et la fréquence de la fréquence à laquelle vous souhaitez que la procédure opérationnelle à répéter - par **heure**, **jour**, **semaine**ou par **mois**.  Si vous sélectionnez **semaine** ou **mois** dans la liste déroulante, l' **option de récurrence** apparaîtra dans la lame et lors de la sélection, la lame **d’option de récurrence** s’affiche et vous pouvez sélectionner le jour de la semaine si vous avez sélectionné la **semaine**.  Si vous avez sélectionné **mois**, vous pouvez choisir en **jours de la semaine** ou jours du mois dans le calendrier et effectuez Enfin, vous souhaitez exécuter le dernier jour du mois ou non et puis cliquez sur **OK**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Pour créer une nouvelle planification avec Windows PowerShell

Pour les procédures opérationnelles dans Azure portal, vous pouvez utiliser l’applet de commande [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) pour créer une nouvelle planification dans Azure Automation pour les procédures opérationnelles classique ou une applet de commande [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) . Vous devez spécifier l’heure de début de la planification et la fréquence d’exécution.

Les exemples de commandes suivants montrent comment créer une planification qui s’exécute chaque jour à 3 h 30 commençant le 20 janvier 2015 avec une applet de commande de gestion des services Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

Les commandes de l’exemple suivant montre comment créer un calendrier pour le 15 et le 30 du mois, à l’aide d’une applet de commande du Gestionnaire de ressources Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
    

## <a name="linking-a-schedule-to-a-runbook"></a>Liaison d’une planification pour une procédure opérationnelle

Une procédure opérationnelle peut être lié à plusieurs planifications et une planification peut avoir plusieurs des procédures opérationnelles liées à celle-ci. Si une procédure opérationnelle possède des paramètres, vous pouvez fournir des valeurs pour eux. Vous devez fournir des valeurs pour les paramètres obligatoires et pouvez fournir des valeurs pour les paramètres facultatifs.  Ces valeurs serviront à chaque fois que la procédure opérationnelle est démarré par ce calendrier.  Vous pouvez joindre la procédure opérationnelle même à une autre planification et spécifier des valeurs de paramètre différentes.


### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Pour lier un programme à une procédure opérationnelle avec le portail classique Azure

1. Dans le portail Azure classique, sélectionnez **Automation** et puis cliquez sur le nom d’un compte d’automation.
2. Sélectionnez l’onglet **procédures opérationnelles** .
3. Cliquez sur le nom de la procédure opérationnelle à planifier.
4. Cliquez sur l’onglet **planification** .
5. Si la procédure opérationnelle n’est pas lié à une planification, puis vous aurez l’option de **lien dans un nouvel échéancier** ou **à une planification existante**.  Si la procédure opérationnelle est actuellement lié à une planification, cliquez sur le **lien** en bas de la fenêtre pour accéder à ces options.
6. Si la procédure opérationnelle possède des paramètres, le système vous demandera de leurs valeurs.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Pour lier un programme à une procédure opérationnelle avec le portail Azure

1. Dans le portail d’Azure, à partir de votre compte d’automation, cliquez sur la mosaïque de **procédures opérationnelles** pour ouvrir la lame de **procédures opérationnelles** .
2. Cliquez sur le nom de la procédure opérationnelle à planifier.
3. Si la procédure opérationnelle n’est pas lié à une planification, puis vous aurez la possibilité de créer une nouvelle planification ou un lien vers une planification existante.  
4. Si la procédure opérationnelle possède des paramètres, vous pouvez sélectionner l’option **Modifier (par défaut : Azure) les paramètres d’exécution** et la lame de **paramètres** est présentée, dans laquelle vous pouvez saisir les informations en conséquence.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Pour lier un programme à une procédure opérationnelle avec Windows PowerShell

Vous pouvez utiliser [Registre-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) pour lier un programme à une procédure opérationnelle classique ou une applet de commande [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) de procédures opérationnelles dans le portail Azure.  Vous pouvez spécifier des valeurs pour les paramètres de la procédure opérationnelle avec le paramètre paramètres. Voir [une procédure opérationnelle dans Azure Automation](automation-starting-a-runbook.md) pour plus d’informations sur la spécification des valeurs de paramètre.

Les exemples de commandes suivants montrent comment lier une planification à l’aide d’une applet de commande de gestion des services Azure avec des paramètres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

Les exemples de commandes suivants montrent comment lier une planification pour une procédure opérationnelle à l’aide d’une applet de commande du Gestionnaire de ressources Azure avec des paramètres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## <a name="disabling-a-schedule"></a>La désactivation d’une planification

Lorsque vous désactivez une planification, les procédures opérationnelles liées ne fonctionnera plus sur cette planification. Vous pouvez manuellement désactiver une planification ou la valeur de délai d’expiration pour les tableaux avec une fréquence lors de leur création. Lorsque le délai d’expiration est atteinte, la planification sera désactivée.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Pour désactiver un programme à partir du portail classique Azure

Vous pouvez désactiver une planification dans le portail Azure classique à partir de la page de détails de la planification de la planification.

1. Dans le portail Azure classique, sélectionnez Automation et puis cliquez sur le nom d’un compte d’automation.
1. Sélectionnez l’onglet ressources.
1. Cliquez sur le nom d’une planification pour ouvrir sa page de détails.
2. Modifier **activé** sur **non**.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Pour désactiver un programme à partir du portail Azure

1. Dans le portail d’Azure, à partir de votre compte d’automation, cliquez sur la mosaïque de **ressources** pour ouvrir la blade **d’actifs** .
2. Cliquez sur la mosaïque de **planifications** pour ouvrir la lame de **planifications** .
2. Cliquez sur le nom d’une planification pour ouvrir la lame de détails.
3. Modifier **activé** sur **non**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Pour désactiver un programme avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) pour modifier les propriétés d’une planification existante pour une procédure opérationnelle classique ou l’applet de commande [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) pour les procédures opérationnelles dans le portail Azure. Pour désactiver la planification, spécifiez **false** pour le paramètre **IsEnabled** .

Les exemples de commandes suivants montrent comment désactiver une planification à l’aide de l’applet de commande de gestion des services Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

Les exemples de commandes suivants montrent comment désactiver une planification pour une procédure opérationnelle à l’aide d’une applet de commande du Gestionnaire de ressources Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’utilisation des tableaux, consultez [Planification des ressources dans Azure Automation](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- Pour vous familiariser avec les procédures opérationnelles dans Azure Automation, reportez-vous à la section [Démarrer une procédure opérationnelle dans Azure Automation](automation-starting-a-runbook.md) 