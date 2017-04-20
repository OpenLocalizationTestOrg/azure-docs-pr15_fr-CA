<properties 
   pageTitle="Types de procédure opérationnelle Automation Azure"
   description="Décrit les différents types de procédures opérationnelles que vous pouvez utiliser dans Azure Automation et les considérations à prendre en compte pour déterminer le type à utiliser. "
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
   ms.author="bwren" />

# <a name="azure-automation-runbook-types"></a>Types de procédure opérationnelle Azure Automation

Azure Automation prend en charge quatre types de procédures opérationnelles sont brièvement décrites dans le tableau suivant.  Les sections suivantes fournissent plus d’informations sur chaque type, y compris des considérations sur l’utilisation de chacun.


| Type de |  Description |
|:---|:---|
| [Graphique](#graphical-runbooks) | Basé sur Windows PowerShell et un éditeur graphique créé et modifié complètement dans Azure Portal. | 
| [Flux de travail graphique PowerShell](#graphical-runbooks) | Basé sur le flux de travail de Windows PowerShell et créés et modifiés complètement dans l’éditeur de graphique dans Azure portal. 
| [PowerShell](#powershell-runbooks) | Texte procédure opérationnelle basée sur un script de Windows PowerShell.
| [Flux de travail de PowerShell](#powershell-workflow-runbooks) | Texte procédure opérationnelle basée sur les flux de travail de Windows PowerShell. |


## <a name="graphical-runbooks"></a>Procédures opérationnelles graphique

[Graphique](automation-runbook-types.md#graphical-runbooks) et graphique du flux de travail PowerShell de procédures opérationnelles sont créés et modifiés avec l’éditeur de graphique dans le portail Azure.  Vous pouvez les exporter vers un fichier et ensuite les importer dans un autre compte d’automatisation, mais vous ne pouvez pas créer ou en modifier un autre outil.  Procédures opérationnelles graphiques génèrent un code PowerShell, mais vous ne pouvez pas directement permet d’afficher ou de modifier le code. Procédures opérationnelles graphique ne peut pas être converti en un des [formats de texte](automation-runbook-types.md), ni une procédure opérationnelle du texte de données peut être convertie au format graphique. Procédures opérationnelles graphique peut être converti de procédures opérationnelles de flux de travail PowerShell graphique lors de l’importation et vice versa.

### <a name="advantages"></a>Avantages

- Créer des procédures opérationnelles avec une connaissance minimale de [PowerShell](automation-powershell-workflow.md).
- Représenter visuellement le processus de gestion.
- Inclure d’autres procédures opérationnelles en tant que procédures opérationnelles d’enfant pour créer des workflows de niveau élevés.


### <a name="limitations"></a>Limitations

- Impossible de modifier la procédure opérationnelle en dehors d’Azure portal.
- Peut nécessiter une activité de Code contenant le code PowerShell pour exécuter une logique complexe.
- Impossible d’afficher ou de modifier directement le code PowerShell qui est créé par le flux de travail graphique. Notez que vous pouvez afficher le code que vous créez dans des activités de Code.


## <a name="powershell-runbooks"></a>Procédures opérationnelles de PowerShell

Procédures opérationnelles de PowerShell sont basés sur Windows PowerShell.  Vous modifiez directement le code de la procédure opérationnelle dans Azure portal à l’aide de l’éditeur de texte.  Vous pouvez également utiliser n’importe quel éditeur de texte en mode hors connexion et [Importer la procédure opérationnelle](http://msdn.microsoft.com/library/azure/dn643637.aspx) dans Azure Automation.

### <a name="advantages"></a>Avantages

- Implémenter toute la logique complexe avec code PowerShell sans les complexités supplémentaires de flux de travail de PowerShell. 
- Procédure opérationnelle démarre plus rapidement que les procédures opérationnelles de graphique ou de flux de travail de PowerShell dans la mesure où il n’a pas besoin d’être compilés avant exécution.

### <a name="limitations"></a>Limitations

- Doit être familiarisé avec l’écriture de scripts PowerShell.
- Impossible d’utiliser [un traitement parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
- Impossible d’utiliser des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre la procédure opérationnelle en cas d’erreur.
- Procédures opérationnelles de PowerShell le flux de travail et les procédures opérationnelles de graphique ne peut être inclus en tant que procédures opérationnelles des enfants à l’aide de l’applet de commande Start-AzureAutomationRunbook qui crée une nouvelle tâche.

### <a name="known-issues"></a>Problèmes connus
Voici les problèmes connus en cours avec les procédures opérationnelles de PowerShell.

- Impossible de procédures opérationnelles de PowerShell ne peut pas récupérer un non chiffré [actif variable](automation-variables.md) avec une valeur null.
- Procédures opérationnelles de PowerShell ne peut pas récupérer d’une [immobilisation variable](automation-variables.md) *~* dans le nom.
- Get-Process dans une boucle dans un PowerShell procédure opérationnelle peut se bloquer après environ 80 itérations. 
- Une procédure opérationnelle de PowerShell peut échouer si elle tente d’écrire une très grande quantité de données dans le flux de sortie à la fois.   Vous pouvez habituellement contourner ce problème en sortie uniquement les informations que vous avez besoin lorsque vous travaillez avec des objets de grande taille.  Par exemple, plutôt que de sortir quelque chose comme *Get-Process*, vous pouvez exporter uniquement les champs requis avec *Get-Process | Sélectionnez ProcessName, processeur*.

## <a name="powershell-workflow-runbooks"></a>Procédures opérationnelles de flux de travail de PowerShell

Procédures opérationnelles de PowerShell le flux de travail sont des procédures opérationnelles de texte basé sur le [Flux de travail de Windows PowerShell](automation-powershell-workflow.md).  Vous modifiez directement le code de la procédure opérationnelle dans Azure portal à l’aide de l’éditeur de texte.  Vous pouvez également utiliser n’importe quel éditeur de texte en mode hors connexion et [Importer la procédure opérationnelle](http://msdn.microsoft.com/library/azure/dn643637.aspx) dans Azure Automation.

### <a name="advantages"></a>Avantages

- Implémenter toute la logique complexe avec code de flux de travail de PowerShell.
- Utilisez des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre la procédure opérationnelle en cas d’erreur.
- Utilisation du [traitement parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
- Peut inclure des autres procédures opérationnelles de graphique et les procédures opérationnelles de flux de travail de PowerShell en tant que procédures opérationnelles d’enfant pour créer des workflows de niveau élevés.


### <a name="limitations"></a>Limitations

- Auteur doit être familiarisé avec les flux de travail de PowerShell.
- Procédure opérationnelle doit faire face à la complexité supplémentaire du flux de travail de PowerShell, tels que la [désérialisation des objets](automation-powershell-workflow.md#code-changes).
- Procédure opérationnelle prend plus de temps à démarrer que les procédures opérationnelles de PowerShell dans la mesure où il doit être compilé avant l’exécution.
- Procédures opérationnelles de PowerShell ne peut être inclus en tant que procédures opérationnelles des enfants à l’aide de l’applet de commande Start-AzureAutomationRunbook qui crée une nouvelle tâche.


## <a name="considerations"></a>Considérations relatives à la

Vous devez prendre en compte les considérations supplémentaires suivantes pour déterminer le type à utiliser pour une procédure opérationnelle particulière.

- Vous ne peut pas convertir les procédures opérationnelles à partir de graphiques de type texte ou vice versa.
- Il existe des limitations au moyen de procédures opérationnelles de différents types comme une procédure opérationnelle d’enfants.  Pour plus d’informations, reportez-vous à la section [procédures opérationnelles des enfants dans Azure Automation](automation-child-runbooks.md) .

  
## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de procédure opérationnelle de graphiques, voir [Graphical authoring dans Azure Automation](automation-graphical-authoring-intro.md)
- Pour comprendre les différences entre PowerShell et PowerShell des workflows pour les procédures opérationnelles, consultez [Processus d’apprentissage Windows PowerShell](automation-powershell-workflow.md)
- Pour plus d’informations sur la façon de créer ou importer une procédure opérationnelle, reportez-vous à la section [Création ou importation d’une procédure opérationnelle](automation-creating-importing-runbook.md)



