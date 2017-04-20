<properties
    pageTitle="Créez ou importez une procédure opérationnelle dans Azure Automation"
    description="Cet article décrit comment créer une nouvelle procédure d’opérationnelle dans Azure Automation ou en importer une à partir d’un fichier."
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

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Créez ou importez une procédure opérationnelle dans Azure Automation

Vous pouvez ajouter une procédure opérationnelle à l’Automation d’Azure soit en [créant un nouveau](#creating-a-new-runbook) ou en important une procédure opérationnelle existante à partir d’un fichier ou à partir de la [Galerie de procédure opérationnelle](automation-runbook-gallery.md). Cet article fournit des informations sur la création et l’importation des procédures opérationnelles à partir d’un fichier.  Vous pouvez obtenir des informations sur l’accès aux procédures opérationnelles de Communauté et de modules dans des [galeries de module et de procédure opérationnelle pour Azure Automation](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Création d’une nouvelle procédure d’opérationnelle

Vous pouvez créer une nouvelle procédure d’opérationnelle dans Automation Azure à l’aide d’un des portails Azure ou Windows PowerShell. Une fois la procédure opérationnelle a été créé, vous pouvez le modifier à l’aide des informations contenues dans le [Workflow de PowerShell de formation](automation-powershell-workflow.md) et de [création graphique dans Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Pour créer une nouvelle procédure de Azure automatisation opérationnelle avec le portail Azure classique

Vous pouvez uniquement travailler avec les [procédures opérationnelles de flux de travail de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) dans Azure portal.

1. Dans le portail Azure classique, cliquez sur, les **nouveaux** **Services d’App**, **procédure opérationnelle**, **l’automatisation** **Création rapide**.
2. Entrez les informations requises, puis cliquez sur **créer**. Le nom de procédure opérationnelle doit commencer par une lettre et peut comporter des lettres, des chiffres, des traits de soulignement et des tirets.
3. Si vous souhaitez modifier la procédure opérationnelle maintenant, puis cliquez sur **Modifier la procédure opérationnelle**. Dans le cas contraire, cliquez sur **OK**.
4. Votre procédure de nouveau opérationnelle s’affiche sous l’onglet **procédures opérationnelles** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Pour créer une nouvelle procédure de Azure automatisation opérationnelle avec le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur le carré de **procédures opérationnelles** pour ouvrir la liste des procédures opérationnelles.
3. Cliquez sur le bouton **Ajouter une procédure opérationnelle** , puis **créer une nouvelle procédure d’opérationnelle**.
2. Tapez le **nom** de la procédure opérationnelle et sélectionnez son [Type](automation-runbook-types.md). Le nom de procédure opérationnelle doit commencer par une lettre et peut comporter des lettres, des chiffres, des traits de soulignement et des tirets.
3. Cliquez sur **créer** pour créer la procédure opérationnelle et ouvrez l’éditeur.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Pour créer une nouvelle procédure de Azure automatisation opérationnelle avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) pour créer une [procédure opérationnelle de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)de vide. Vous pouvez spécifier le **nom** de paramètre pour créer une procédure opérationnelle vide que vous pouvez modifier ultérieurement, ou vous pouvez spécifier le paramètre de **chemin d’accès** pour importer un fichier de procédure opérationnelle. Le paramètre de **Type** doit également être inclus pour spécifier l’un des quatre types de procédure opérationnelle.

Les exemples de commandes suivants montrent comment créer une nouvelle procédure d’opérationnelle vide.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importation d’une procédure opérationnelle à partir d’un fichier dans Azure Automation

Vous pouvez créer une nouvelle procédure d’opérationnelle dans Azure Automation en important un script PowerShell ou PowerShell Workflow (extension .ps1) ou une procédure opérationnelle graphique exporté (.graphrunbook).  Vous devez spécifier le [type de procédure opérationnelle](automation-runbook-types.md) qui va être créé à partir de l’importation en prenant en compte les considérations suivantes.

- Un fichier .graphrunbook ne peut-être être importé dans une nouvelle [procédure opérationnelle de graphique](automation-runbook-types.md#graphical-runbooks), et les procédures opérationnelles de graphique ne peut être créé à partir d’un fichier .graphrunbook.
- Un fichier .ps1 contenant un flux de travail PowerShell peut uniquement être importé dans une [procédure opérationnelle de flux de travail de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Si le fichier contient plusieurs flux de travail de PowerShell, l’importation échouera. Vous devez enregistrer chaque flux de travail dans son propre fichier et importez chacune séparément.
- Un fichier .ps1 qui ne contient-elle pas un flux de travail peut être importé dans une [procédure opérationnelle de PowerShell](automation-runbook-types.md#powershell-runbooks) ou dans une [procédure opérationnelle de flux de travail de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Si elle est importée dans une procédure opérationnelle du flux de travail de PowerShell, puis il est converti en un flux de travail, et les commentaires seront inclus dans la procédure opérationnelle spécifiant les modifications qui ont été apportées.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Pour importer une procédure opérationnelle à partir d’un fichier avec le portail Azure classique
Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.  Notez que vous ne pouvez importer qu’un fichier .ps1 dans une procédure opérationnelle de PowerShell le flux de travail à l’aide de ce portail.  Vous devez utiliser le portail Azure pour d’autres types.

1. Dans le portail de gestion d’Azure, sélectionnez **Automation** , puis sélectionnez un compte d’Automation.
2. Cliquez sur **Importer**.
3. Cliquez sur **Parcourir pour le fichier** et recherchez le fichier de script à importer.
4. Si vous souhaitez modifier la procédure opérationnelle maintenant, puis cliquez sur **Modifier la procédure opérationnelle**. Dans le cas contraire, cliquez sur OK.
5. La procédure de nouveau opérationnelle s’affiche sous l’onglet **procédures opérationnelles** pour le compte de l’Automation.
6. Vous devez [publier la procédure opérationnelle](#publishing-a-runbook) avant de pouvoir l’exécuter.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Pour importer une procédure opérationnelle à partir d’un fichier avec le portail Azure
Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.  

>[AZURE.NOTE] Notez que vous ne pouvez importer qu’un fichier .ps1 dans une procédure opérationnelle de PowerShell le flux de travail via le portail.

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur le carré de **procédures opérationnelles** pour ouvrir la liste des procédures opérationnelles.
3. Cliquez sur le bouton **Ajouter une procédure opérationnelle** , puis les **Importer**.
4. Cliquez sur pour sélectionner le fichier à importer un **fichier de procédure opérationnelle**
2. Si le champ de **nom** est activé, vous avez ensuite la possibilité de le modifier.  Le nom de procédure opérationnelle doit commencer par une lettre et peut comporter des lettres, des chiffres, des traits de soulignement et des tirets.
3. Le [type de procédure opérationnelle](automation-runbook-types.md) sera sélectionnée automatiquement, mais vous pouvez modifier le type après avoir pris en compte aux restrictions applicables. 
3. La procédure de nouveau opérationnelle s’affiche dans la liste des procédures opérationnelles pour le compte de l’Automation.
4. Vous devez [publier la procédure opérationnelle](#publishing-a-runbook) avant de pouvoir l’exécuter.

>[AZURE.NOTE] Après avoir importé une procédure opérationnelle de graphique ou d’une procédure opérationnelle de flux de travail de PowerShell graphique, vous avez la possibilité de convertir l’autre type si vous souhaitez. Vous ne pouvez pas convertir textuelle.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Pour importer une procédure opérationnelle à partir d’un fichier de script avec Windows PowerShell

Vous pouvez utiliser la cmdlet [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) pour importer un fichier de script en tant que brouillon procédure opérationnelle de flux de travail de PowerShell. Si la procédure opérationnelle existe déjà, l’importation échouera, sauf si vous utilisez la *-Force* paramètre. 

Les exemples de commandes suivants montrent comment importer un fichier de script dans une procédure opérationnelle.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publication d’une procédure opérationnelle

Lorsque vous créez ou importez une procédure opérationnelle de nouveau, vous devez le publier avant de pouvoir l’exécuter.  Chaque procédure opérationnelle dans Automation a un projet et une version publié. Que la version publiée est disponible pour être exécuté, et que la version de brouillon peut être modifiée. La version publiée n’est pas affectée par toute modification apportée à la version de brouillon. Lorsque le brouillon doit être mis à la disposition, puis vous le publier qui remplace la version publiée avec la version de brouillon.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Pour publier une procédure opérationnelle via le portail Azure classique

1. Ouvrez la procédure opérationnelle dans le portail Azure classique.
1. En haut de l’écran, cliquez sur **auteur**.
1. En bas de l’écran, cliquez sur **Publier** , puis sur **Oui** dans le message de vérification.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Pour publier une procédure opérationnelle via le portail Azure

1. Ouvrez la procédure opérationnelle dans le portail Azure.
1. Cliquez sur le bouton **Modifier** .
1. Cliquez sur le bouton **Publier** puis sur **Oui** dans le message de vérification.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Pour publier une procédure opérationnelle à l’aide de Windows PowerShell

Vous pouvez utiliser l’applet de commande [AzureRmAutomationRunbook-publier](https://msdn.microsoft.com/library/mt603705.aspx) pour publier une procédure opérationnelle avec Windows PowerShell. Les exemples de commandes suivants montrent comment publier un exemple de procédure opérationnelle.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Étapes suivantes
- Pour savoir comment vous pouvez bénéficier de la procédure opérationnelle et une galerie du Module PowerShell, consultez [les galeries de module et de procédure opérationnelle pour Azure Automation](automation-runbook-gallery.md)
- Pour en savoir plus sur la modification des procédures opérationnelles de PowerShell et de flux de travail de PowerShell avec un éditeur de texte, consultez [modification des procédures textuelle opérationnelles dans Azure Automation](automation-edit-textual-runbook.md)
- Pour en savoir plus sur la création de procédure opérationnelle de graphiques, voir [Graphical authoring dans Azure Automation](automation-graphical-authoring-intro.md)
