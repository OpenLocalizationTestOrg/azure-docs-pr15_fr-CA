<properties 
   pageTitle="Workflow de PowerShell d’apprentissage"
   description="Cet article est destiné à une leçon rapide auteurs familiarisés avec PowerShell pour comprendre les différences spécifiques entre PowerShell et les flux de travail de PowerShell."
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

# <a name="learning-windows-powershell-workflow"></a>Flux de travail de formation Windows PowerShell

Procédures opérationnelles dans Azure Automation sont implémentées en tant que flux de travail de Windows PowerShell.  Un flux de travail de Windows PowerShell est similaire à un script Windows PowerShell, mais présente certaines différences importantes qui peuvent être source de confusion pour un nouvel utilisateur.  Cet article est conçu pour les utilisateurs déjà familiarisés avec PowerShell et décrit brièvement les concepts dont vous avez besoin si vous convertissez un script PowerShell PowerShell d’un flux de travail pour une utilisation dans une procédure opérationnelle.  

Un workflow est une séquence d’étapes programmées, connectés qui effectuent des tâches de longue ou qui nécessitent la coordination de plusieurs étapes sur plusieurs périphériques ou nœuds gérés. Les avantages d’un workflow dans un script normal incluent la possibilité d’exécuter simultanément plusieurs périphériques et la possibilité de récupérer automatiquement des défaillances. Un flux de travail de Windows PowerShell est un script Windows PowerShell qui tire parti de Windows Workflow Foundation. Pendant que le flux de travail est écrite avec la syntaxe de Windows PowerShell et lancée par Windows PowerShell, elle est traitée par Windows Workflow Foundation.

Pour plus d’informations sur les rubriques de cet article, reportez-vous à la section [Mise en route de Workflow de Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Types de procédure opérationnelle

Il existe trois types de procédure opérationnelle dans Azure Automation, *Flux de travail de PowerShell*, *PowerShell* et *graphique*.  Vous définissez le type de procédure opérationnelle lorsque vous créez la procédure opérationnelle, et que vous ne pouvez pas convertir une procédure opérationnelle à l’autre type une fois qu’il a été créé.

PowerShell, workflow procédures opérationnelles et procédures opérationnelles de PowerShell sont pour les utilisateurs qui préfèrent travailler directement avec le code PowerShell, soit à l’aide de l’éditeur de texte dans Azure Automation ou d’un éditeur en mode hors connexion, tels que PowerShell ISE. Vous devez comprendre les informations contenues dans cet article si vous créez une procédure opérationnelle du flux de travail de PowerShell. 

Procédures opérationnelles graphiques permettent de créer une procédure opérationnelle à l’aide des activités et des applets de commande, mais à l’aide d’une interface graphique qui masque la complexité du flux de travail PowerShell sous-jacente.  Concepts de cet article, tels que les points de contrôle et d’exécution en parallèle s’appliquent toujours aux procédures opérationnelles graphique, mais vous n’aurez pas à vous soucier de la syntaxe détaillée. 

## <a name="basic-structure-of-a-workflow"></a>Structure de base d’un flux de travail

La première étape de la conversion d’un script PowerShell pour un flux de travail de PowerShell est englobant avec le mot clé du **flux de travail** .  Un flux de travail démarre avec le mot clé de **Workflow** suivi par le corps du script entouré accolades. Le nom du flux de travail suit le mot clé du **flux de travail** , comme indiqué dans la syntaxe suivante. 

    Workflow Test-Workflow
    {
       <Commands>
    }

Le nom du flux de travail doit correspondre à celui de la procédure d’automatisation opérationnelle. Si la procédure opérationnelle est importé, le nom du fichier doit correspondre au nom de flux de travail et doit se terminer par .ps1.

Pour ajouter des paramètres pour le flux de travail, utilisez le mot clé **Param** comme vous le feriez pour un script. 

## <a name="code-changes"></a>Modifications du code

Code de flux de travail de PowerShell est quasiment identique au code de script PowerShell, à l’exception de quelques modifications importantes.  Les sections suivantes décrivent les modifications que vous devrez apporter à un script PowerShell pour elle de s’exécuter dans un flux de travail.

### <a name="activities"></a>Activités

Une activité est une tâche spécifique dans un flux de travail. Tout comme un script est composé d’une ou plusieurs commandes, un flux de travail se compose d’une ou plusieurs activités qui sont effectuées dans une séquence. Flux de travail Windows PowerShell convertit automatiquement la plupart des applets de commande Windows PowerShell activités lors de l’exécution d’un flux de travail. Lorsque vous spécifiez une de ces applets de commande dans votre procédure opérationnelle, l’activité correspondante est en fait exécutée par Windows Workflow Foundation. Pour les applets de commande sans l’activité correspondante, flux de travail Windows PowerShell s’exécute automatiquement dans une activité [InlineScript avec](#inlinescript) l’applet de commande. Il existe un jeu d’applets de commande qui sont exclus et ne peut pas être utilisé dans un flux de travail, à moins que vous ne les incluiez explicitement dans un bloc InlineScript. Pour plus d’informations sur ces concepts, reportez-vous [à l’aide des activités de flux de travail de Script](http://technet.microsoft.com/library/jj574194.aspx).

Activités de flux de travail partagent un ensemble de paramètres communs à configurer leur fonctionnement. Pour plus d’informations sur les paramètres communs de flux de travail, consultez [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Paramètres positionnels

Vous ne pouvez pas utiliser de paramètres positionnels avec les activités et les applets de commande dans un flux de travail.  Tout cela signifie est que vous devez utiliser des noms de paramètre.

Par exemple, considérez le code suivant qui obtient tous les services en cours d’exécution.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Si vous essayez d’exécuter le même code dans un flux de travail, vous obtiendrez un message comme « jeu de paramètres ne peut pas être résolu à l’aide de paramètres nommés spécifié ».  Pour résoudre ce problème, fournissez simplement le nom du paramètre comme indiqué ci-dessous.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objets désérialisés

Dans les workflows, les objets sont désérialisés.  Cela signifie que leurs propriétés sont toujours disponibles, mais pas leurs méthodes.  Par exemple, considérez le code PowerShell suivant qui arrête un service à l’aide de la méthode Stop de l’objet de Service.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Si vous essayez d’exécuter cet exemple dans un flux de travail, vous obtiendrez une erreur indiquant que « appel de méthode n’est pas pris en charge dans un flux de travail de Windows PowerShell ».  

Une option consiste à encapsuler ces deux lignes de code dans un bloc [InlineScript](#InlineScript) auquel cas $Service serait un objet de service au sein du bloc. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

Une autre option consiste à utiliser une autre applet de commande qui exécute la même fonctionnalité que la méthode, si elle est disponible.  Dans le cas de notre exemple, l’applet de commande Stop-Service fournit la même fonctionnalité que la méthode Stop, et vous pouvez utiliser les éléments suivants pour un flux de travail.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

L’activité **InlineScript** est utile lorsque vous devez exécuter une ou plusieurs commandes sous la forme d’un script PowerShell traditionnel au lieu du flux de travail de PowerShell.  Alors que les commandes dans un flux de travail sont envoyés à Windows Workflow Foundation pour le traitement, les commandes dans un bloc InlineScript sont traitées par Windows PowerShell. 

InlineScript utilise la syntaxe ci-dessous.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Vous pouvez retourner la sortie à partir d’un InlineScript en affectant le résultat à une variable. L’exemple suivant arrête un service et renvoie ensuite le nom de service.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Vous pouvez passer des valeurs à un bloc InlineScript, mais vous devez utiliser le modificateur de portée de **$Using** .  L’exemple suivant est identique à l’exemple précédent, sauf que le nom de service est fourni par une variable. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Les activités InlineScript peuvent être critiques dans certains flux de production, ils ne prennent pas en charge les constructions de flux de travail et doivent être utilisés uniquement lorsque cela est nécessaire pour les raisons suivantes :

- Vous ne pouvez pas utiliser des [points de contrôle](#Checkpoints) à l’intérieur d’un bloc InlineScript. Si une défaillance se produit dans le bloc, il doit être relancé depuis le début du bloc.
- Vous ne pouvez pas utiliser [l’exécution en parallèle](#parallel-execution) dans un InlineScriptBlock.
- InlineScript affecte l’évolutivité du flux de travail dans la mesure où il détient la session Windows PowerShell pour toute la longueur du bloc InlineScript.

Pour plus de détails sur l’utilisation de InlineScript, voir [Exécution de commandes Windows PowerShell dans un flux de travail](http://technet.microsoft.com/library/jj574197.aspx) et [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Traitement en parallèle

Un des avantages de flux de travail de Windows PowerShell sont la possibilité d’effectuer d’un ensemble de commandes en parallèle et non séquentiellement comme avec un script typique. 

Vous pouvez utiliser le mot clé **parallèle** pour créer un bloc de script de commandes multiples qui seront exécutée simultanément. Cette méthode utilise la syntaxe ci-dessous. Dans ce cas, Activity1 et Activity2 démarrera en même temps. Activity3 démarre seulement après exécution de Activity1 et Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Par exemple, considérez les commandes PowerShell suivantes que copier plusieurs fichiers vers une destination réseau.  Ces commandes sont exécutées séquentiellement afin qu’un fichier doit se terminer avant le début de la suivante de la copie.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Le workflow suivant exécute ces commandes en parallèle afin qu’elles commencent toutes en même temps la copie.  Qu’une fois qu’elles sont toutes entièrement copié le message de fin apparaît.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Vous pouvez utiliser la **ForEach-parallèle** construction de traiter des commandes pour chaque élément dans une collection de manière simultanée. Pendant l’exécution de commandes dans le bloc de script dans l’ordre, les éléments de la collection sont traitées en parallèle. Cette méthode utilise la syntaxe ci-dessous. Dans ce cas, Activity1 démarrera en même temps pour tous les éléments dans la collection. Pour chaque élément, Activity2 démarrera une fois Activity1 terminée. Activity3 démarre seulement après exécution de tous les éléments de Activity1 et Activity2.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

L’exemple suivant est similaire à l’exemple précédent, la copie des fichiers en parallèle.  Dans ce cas, un message s’affiche pour chaque fichier après que l’avoir copié.  Qu’une fois qu’ils sont tous entièrement copié est le message d’achèvement final affiché.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  Nous ne recommandons pas l’exécution des procédures opérationnelles des enfants en parallèle dans la mesure où il a été prouvé peut donner des résultats peu fiables.  La sortie de la procédure opérationnelle enfant parfois n’apparaîtra pas et les paramètres de procédure opérationnelle d’un enfant peuvent affecter les autres procédures opérationnelles parallèles enfant 


## <a name="checkpoints"></a>Points de contrôle

Un *point de contrôle* est un instantané de l’état actuel du flux de travail qui inclut la valeur actuelle pour les variables et toute sortie générée à ce point. Si un flux de travail se termine par erreur ou est interrompu, puis la prochaine fois qu’elle est exécutée il démarre à partir de son dernier point de contrôle au lieu de début de la worfklow.  Vous pouvez définir un point de contrôle dans un flux de travail avec l’activité de **Workflow-point de contrôle** .

Dans l’exemple de code suivant, une exception se produit après le Activity2 à l’origine de la fin du flux de travail. Lorsque le flux de travail s’exécute à nouveau, il commence par Activity2 en cours d’exécution dans la mesure où il s’agissait juste après que le dernier point de reprise est défini.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Vous devez définir des points de contrôle dans un flux de travail une fois que les activités qui peuvent être sujets à une exception et ne doivent pas être répété si le workflow est repris. Par exemple, votre flux de travail peut créer un ordinateur virtuel. Vous pouvez définir un point de contrôle à la fois avant et après les commandes pour créer la machine virtuelle. En cas d’échec de la création, les commandes doivent être répétés si le workflow est démarré à nouveau. Si le le worfklow échoue après la création réussit, l’ordinateur virtuel ne sera pas créé à nouveau lorsque le flux de travail est repris.

L’exemple suivant copie plusieurs fichiers vers un emplacement réseau et définit un point de contrôle après chaque fichier.  Si l’emplacement réseau est perdue, le flux de travail se termine dans l’erreur.  Lorsqu’il est lancé à nouveau, il reprend au dernier point de contrôle c'est-à-dire que seuls les fichiers qui ont déjà été copiés seront ignorés.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Dans la mesure où les informations d’identification de nom d’utilisateur ne sont pas conservées après l’appel à l’activité de [Workflow de la suspension](https://technet.microsoft.com/library/jj733586.aspx) ou après le dernier point de contrôle, vous devez définir les informations d’identification à la valeur null, puis les extraire à nouveau à partir du magasin de l’immobilisation après que **Suspension-flux de travail** ou point de contrôle est appelée.  Dans le cas contraire, le message d’erreur suivant peut s’afficher : *la tâche de flux de travail ne peut pas être repris, soit parce que les données de persistance est enregistrées dans sa totalité, ou enregistrées les données persistance a été endommagées. Vous devez redémarrer le flux de travail.*

Le même code suivant montre comment gérer cette situation dans les procédures opérationnelles de flux de travail de PowerShell.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Ce n’est pas nécessaire si vous authentifiez en utilisant un compte Exécuter en tant que configuré avec un service principal.  

Pour plus d’informations sur les points de contrôle, voir [Ajout de points de contrôle pour un flux de travail de Script](http://technet.microsoft.com/library/jj574114.aspx).


## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec les procédures opérationnelles de flux de travail de PowerShell, voir [Ma première procédure de flux de travail de PowerShell opérationnelle](automation-first-runbook-textual.md) 
