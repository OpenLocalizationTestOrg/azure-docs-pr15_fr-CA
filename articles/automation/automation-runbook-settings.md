<properties 
   pageTitle="Paramètres de procédure opérationnelle"
   description="Décrit les paramètres de configuration pour une procédure opérationnelle dans Azure Automation et comment les modifier à l’aide du portail de gestion Azure et de Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Paramètres de procédure opérationnelle

Chaque procédure opérationnelle dans Azure Automation a plusieurs paramètres pour l’aider à identifier et à modifier son comportement de journalisation. Chacun de ces paramètres est décrit ci-dessous suivie de procédures sur la façon de les modifier.

## <a name="settings"></a>Paramètres

### <a name="name-and-description"></a>Nom et description

Vous ne pouvez pas modifier le nom d’une procédure opérationnelle après que qu’il a été créé. La Description est facultative et peut comporter jusqu'à 512 caractères.

### <a name="tags"></a>Balises

Les balises vous permettent d’affecter différents mots et phrases pour aider à identifier une procédure opérationnelle. Par exemple, lorsque vous envoyez une procédure opérationnelle à la [Galerie de procédure opérationnelle](https://msdn.microsoft.com/library/dn781422.aspx), vous spécifiez certaines balises pour identifier les catégories de la procédure opérationnelle doit être répertorié dans. Vous pouvez spécifier plusieurs balises pour une procédure opérationnelle en les séparant par des virgules.

### <a name="logging"></a>Enregistrement dans le journal

Par défaut, les commentaires et la progression des enregistrements ne sont pas écrites à l’historique des travaux. Vous pouvez modifier les paramètres d’une procédure opérationnelle particulière pour ces enregistrements de journal. Pour plus d’informations sur ces enregistrements, consultez [Messages et sortie de procédure opérationnelle](https://msdn.microsoft.com/library/dn879148.aspx).

## <a name="changing-runbook-settings"></a>Modification des paramètres de procédure opérationnelle

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Modification des paramètres de procédure opérationnelle avec le portail de gestion Azure

Vous pouvez modifier les paramètres d’une procédure opérationnelle dans le portail de gestion Azure à partir de la page **configurer** la procédure opérationnelle.

1. Dans le portail de gestion Azure, sélectionnez **Automation** et puis cliquez sur le nom d’un compte d’automation.
1. Sélectionnez l’onglet **procédures opérationnelles** .
1. Cliquez sur le nom d’une procédure opérationnelle.
1. Sélectionnez l’onglet **configurer** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Modification des paramètres de procédure opérationnelle avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) pour modifier les paramètres d’une procédure opérationnelle. Si vous souhaitez spécifier plusieurs balises, vous pouvez fournir un tableau ou une chaîne unique avec les valeurs par des virgules pour le paramètre de balises. Vous pouvez obtenir les balises actives avec [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Les exemples de commandes suivants montrent comment définir les propriétés d’une procédure opérationnelle. Cet exemple ajoute trois balises pour les balises existantes et spécifie que les enregistrements détaillés doivent être enregistrés.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Articles connexes
- [Sortie de procédure opérationnelle et des Messages](../automation-runbook-output-and-messages) 
- [Créez ou importez une procédure opérationnelle](https://msdn.microsoft.com/library/dn643637.aspx) 