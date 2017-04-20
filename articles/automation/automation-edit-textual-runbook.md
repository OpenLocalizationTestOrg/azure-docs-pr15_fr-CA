<properties 
    pageTitle="Modification des procédures opérationnelles de textuelle dans Azure Automation"
    description="Cet article fournit différentes procédures pour l’utilisation de procédures opérationnelles de PowerShell et de flux de travail de PowerShell dans Azure Automation à l’aide de l’éditeur de texte."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Modification des procédures opérationnelles de textuelle dans Azure Automation

L’éditeur de texte dans Azure Automation vous permet de modifier les [procédures opérationnelles de PowerShell](automation-runbook-types.md#powershell-runbooks) et les [procédures opérationnelles de flux de travail de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Cela a les fonctionnalités standard d’autres éditeurs de code, telles qu’intellisense et le codage en couleurs avec des fonctionnalités spéciales supplémentaires pour vous aider à accéder aux ressources communes aux procédures opérationnelles.  Cet article fournit des étapes détaillées pour les différentes fonctions avec cet éditeur.

L’éditeur de texte inclut une fonctionnalité pour insérer du code pour les activités, les actifs et les procédures opérationnelles des enfants dans une procédure opérationnelle. Plutôt que de taper le code vous-même, vous pouvez sélectionner dans une liste de ressources disponibles et le code approprié est inséré dans la procédure opérationnelle.

Chaque procédure opérationnelle dans Azure Automation a deux versions, brouillon et publié. Modifier la version provisoire de la procédure opérationnelle et publiez-le afin de pouvoir être exécuté. La version publiée ne peut pas être modifiée. Pour plus d’informations, voir [publication d’une procédure opérationnelle](automation-creating-importing-runbook.md#publishing-a-runbook) .

Pour travailler avec les [Procédures opérationnelles de graphiques](automation-runbook-types.md#graphical-runbooks), voir [Graphical authoring dans Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Pour modifier une procédure opérationnelle avec le portail Azure

Utilisez la procédure suivante pour ouvrir une procédure opérationnelle pour le modifier dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez votre compte d’automation.
2. Cliquez sur la mosaïque de **procédures opérationnelles** pour ouvrir la liste des procédures opérationnelles.
3. Cliquez sur le nom de la procédure opérationnelle que vous voulez modifier, puis sur le bouton **Modifier** .
6. Effectuez la modification requise.
7. Lorsque vos modifications sont terminées, cliquez sur **Enregistrer** .
8. Si vous souhaitez que la dernière version de brouillon de la procédure opérationnelle à publier, cliquez sur **Publier** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Pour insérer une applet de commande dans une procédure opérationnelle

2. Dans la zone de travail de l’éditeur de texte, placez le curseur où vous souhaitez placer l’applet de commande.
3. Développez le nœud **d’applets de commande** dans le contrôle de la bibliothèque. 
3. Développez le module contenant l’applet de commande que vous souhaitez utiliser.
4. Cliquez avec le bouton droit de la souris sur l’applet de commande pour insérer et sélectionnez **Ajouter à la zone de dessin**.  Si plus d’un paramètre est définie pour l’applet de commande, puis le jeu par défaut sera ajouté.  Vous pouvez également développer l’applet de commande pour sélectionner un ensemble de paramètres différentes.
4. Le code de l’applet de commande est inséré avec sa liste entière de paramètres.
5. Fournir une valeur appropriée à la place le type de données entouré d’accolades les <> pour tous les paramètres requis.  Supprime les paramètres que vous n’avez pas besoin.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Pour insérer le code pour une procédure opérationnelle d’enfants dans une procédure opérationnelle

2. Dans la zone de travail de l’éditeur de texte, placez le curseur où vous souhaitez placer le code pour la [procédure opérationnelle des enfants](automation-child-runbooks.md).
3. Développez le nœud **procédures opérationnelles** dans le contrôle de la bibliothèque. 
3. Cliquez avec le bouton droit de la souris sur la procédure opérationnelle pour insérer et sélectionnez **Ajouter à la zone de dessin**.
4. Le code de la procédure opérationnelle enfant est inséré avec des espaces réservés pour les paramètres de procédure opérationnelle.
5. Remplacez les espaces réservés par les valeurs appropriées pour chaque paramètre.

### <a name="to-insert-an-asset-into-a-runbook"></a>Pour insérer un élément dans une procédure opérationnelle

2. Dans la zone de travail de l’éditeur de texte, placez le curseur où vous souhaitez placer le code pour la procédure opérationnelle enfant.
3. Développez le nœud **actif** dans le contrôle de la bibliothèque. 
4. Développez le nœud pour le type de ressource que vous souhaitez.
3. Cliquez avec le bouton droit de la souris sur l’actif pour insérer et sélectionnez **Ajouter à la zone de dessin**.  Pour les [actifs de variable](automation-variables.md), sélectionnez **Ajouter « obtenir la Variable » pour la toile** ou **Ajouter « la valeur de la Variable » pour la toile** selon que vous voulez obtenir ou définir la variable.
4. Le code de l’actif est inséré dans la procédure opérationnelle.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Pour modifier une procédure opérationnelle avec le portail Azure

Utilisez la procédure suivante pour ouvrir une procédure opérationnelle pour le modifier dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez **Automation** et puis cliquez sur le nom d’un compte d’automation.
2. Sélectionnez l’onglet **procédures opérationnelles** .
3. Cliquez sur le nom de la procédure opérationnelle que vous voulez modifier, puis sélectionnez l’onglet de **l’auteur** .
5. Cliquez sur le bouton **Modifier** au bas de l’écran.
6. Effectuez la modification requise.
7. Lorsque vos modifications sont terminées, cliquez sur **Enregistrer** .
8. Si vous souhaitez que la dernière version de brouillon de la procédure opérationnelle à publier, cliquez sur **Publier** .

### <a name="to-insert-an-activity-into-a-runbook"></a>Pour insérer une activité dans une procédure opérationnelle

1. Dans la zone de travail de l’éditeur de texte, placez le curseur où vous souhaitez placer l’activité.
1. En bas de l’écran, cliquez sur **Insérer** , puis sur **activités**.
1. Dans la colonne **Integration Module** , sélectionnez le module qui contient l’activité.
1. Dans le volet **d’activité** , sélectionnez une activité.
1. Dans la colonne **Description** , notez la description de l’activité. Si vous le souhaitez, vous pouvez cliquer sur Affichage détaillé d’aide au démarrage de l’aide pour l’activité dans le navigateur.
1. Cliquez sur la flèche vers la droite.  Si l’activité a des paramètres, ils seront afficheront pour vos informations.
1. Cliquez sur le bouton de vérification.  Exécution de l’activité de code sera inséré dans la procédure opérationnelle.
1. Si l’activité nécessite des paramètres, fournir une valeur appropriée à la place le type de données entouré d’accolades <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Pour insérer le code pour une procédure opérationnelle d’enfants dans une procédure opérationnelle

1. Dans la zone de travail de l’éditeur de texte, placez le curseur où vous souhaitez placer la [procédure opérationnelle des enfants](automation-child-runbooks.md).
2. En bas de l’écran, cliquez sur **Insérer** , puis sur **procédure opérationnelle**.
3. Sélectionnez la procédure opérationnelle à insérer à partir de la colonne du milieu, puis cliquez sur la flèche vers la droite.
4. Si la procédure opérationnelle possède des paramètres, ils seront afficheront pour vos informations.
5. Cliquez sur le bouton de vérification.  Code pour exécuter la procédure opérationnelle sélectionnée sera inséré dans la procédure opérationnelle actuelle.
7. Si la procédure opérationnelle nécessite des paramètres, fournir une valeur appropriée à la place le type de données entouré d’accolades <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Pour insérer un élément dans une procédure opérationnelle

1. Dans la zone de travail de l’éditeur de texte, placez le curseur où vous souhaitez placer l’activité pour récupérer la ressource.
1. En bas de l’écran, cliquez sur **Insérer** , puis sur **paramètre**.
1. Dans la colonne **Action de paramètre** , sélectionnez l’action que vous souhaitez.
1. Sélectionnez les éléments disponibles dans la colonne centrale.
1. Cliquez sur le bouton de vérification.  Code pour obtenir ou définir l’actif sera insérée dans la procédure opérationnelle.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Pour modifier une procédure opérationnelle de Azure Automation à l’aide de Windows PowerShell

Pour modifier une procédure opérationnelle avec Windows PowerShell, vous utilisez l’éditeur de votre choix et l’enregistrez dans un fichier .ps1. Vous pouvez utiliser l’applet de commande [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) pour récupérer le contenu de la procédure opérationnelle, puis l’applet de commande [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) pour remplacer la procédure opérationnelle de projet existant modifié.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Pour récupérer le contenu d’une procédure opérationnelle à l’aide de Windows PowerShell

Les exemples de commandes suivants montrent comment récupérer le script pour une procédure opérationnelle et l’enregistrer dans un fichier de script. Dans cet exemple, la version du projet est extrait. Il est également possible de récupérer la version publiée de la procédure opérationnelle, bien que cette version ne peut pas être modifiée.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Pour modifier le contenu d’une procédure opérationnelle à l’aide de Windows PowerShell

Les exemples de commandes suivants montrent comment remplacer le contenu existant d’une procédure opérationnelle avec le contenu d’un fichier de script. Notez qu’il s’agit de la même procédure d’exemple comme [pour importer une procédure opérationnelle à partir d’un fichier de script avec Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Articles connexes

- [Créez ou importez une procédure opérationnelle dans Azure Automation](automation-creating-importing-runbook.md)
- [Flux de travail de PowerShell d’apprentissage](automation-powershell-workflow.md)
- [Création graphique dans Azure Automation](automation-graphical-authoring-intro.md)
- [Certificats](automation-certificates.md)
- [Connexions](automation-connections.md)
- [Informations d’identification](automation-credentials.md)
- [Planifications](automation-schedules.md)
- [Variables](automation-variables.md)