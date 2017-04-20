<properties
   pageTitle="Ajouter des procédures opérationnelles d’Azure automation pour les plans de récupération | Microsoft Azure"
   description="Cet article décrit comment récupération de Site Azure vous permet désormais d’étendre des plans de récupération à l’aide de Automation d’Azure pour effectuer des tâches complexes lors de la restauration vers Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Ajouter des procédures opérationnelles d’Azure automation pour les plans de récupération - classique


Ce didacticiel explique comment restauration du Site Azure s’intègre avec Automation Azure pour fournir l’extensibilité à des plans de récupération. Plans de reprise peuvent gérer la récupération de vos machines virtuelles protégées à l’aide de la récupération de Site Azure pour cloud secondaire de la réplication et la réplication des scénarios Azure. Ils aident également à effectuer la restauration **précise de manière cohérente**, **reproductible**et **automatisée**. Si vous ne pouvez pas sur vos ordinateurs virtuels vers Azure, intégration avec Azure Automation étend les plans de récupération et vous donne la possibilité d’exécuter les procédures opérationnelles, permettant ainsi des tâches d’automatisation puissantes.

Si vous n’avez pas encore entendu parler d’Automation d’Azure, inscrivez-vous [ici](https://azure.microsoft.com/services/automation/) et télécharger les exemples de scripts [ici](https://azure.microsoft.com/documentation/scripts/). En savoir plus sur la [Récupération de Site Azure](https://azure.microsoft.com/services/site-recovery/) et comment orchestrer la récupération vers Azure à l’aide de plans de récupération [d’ici](https://azure.microsoft.com/blog/?p=166264).

Dans ce court didacticiel, nous allons étudier comment vous pouvez intégrer des procédures opérationnelles d’Azure Automation dans les plans de reprise. Nous automatiser des tâches simples qui précédemment nécessaire une intervention manuelle et voir comment convertir une restauration en une étape plusieurs fois une action de récupération d’un simple clic. Nous examinerons également comment vous pouvez résoudre un script simple si elle se produit.

## <a name="protect-the-application-to-azure"></a>Protéger l’application Azure

Commençons par une application simple qui se compose de deux machines virtuelles. Ici, nous avons une application Web de Fabrikam. Fabrikam-Web-frontend et Fabrikam-Web-back-end sont à deux ordinateurs virtuels protégés vers Azure à l’aide de la récupération de Site Azure. Pour protéger les ordinateurs virtuels à l’aide de la récupération de Site d’Azure, suivez les étapes ci-dessous.

1.  Activer la protection de vos ordinateurs virtuels.

2.  Assurez-vous que les ordinateurs virtuels ont terminé la réplication initiale et sont répliqués.

3.  Attendez la réplication initiale est terminée et l’état de réplication indique que Protected.

![](media/site-recovery-runbook-automation/01.png)
---------------------

Dans ce didacticiel, nous allons créer un plan de récupération pour l’application de Fabrikam HRweb pour basculer l’application Azure. Puis nous l’intégrera avec une procédure opérationnelle qui crée un point de terminaison sur l’échec sur Azure machine virtuelle pour servir des pages web sur le port 80.

Tout d’abord, nous allons créer un plan de récupération pour notre application.

## <a name="create-the-recovery-plan"></a>Créer le plan de récupération

Pour récupérer l’application Azure, vous devez créer un plan de récupération.
À l’aide d’un plan de récupération que vous pouvez spécifier l’ordre de la récupération des ordinateurs virtuels. L’ordinateur virtuel placé dans le groupe 1 sera récupérer et commencer en premier, et suivez la machine virtuelle dans le groupe 2.

Créer un Plan de récupération qui se présente comme ci-dessous.

![](media/site-recovery-runbook-automation/12.png)

Pour en savoir plus sur les plans de récupération, consultez la documentation [ici](https://msdn.microsoft.com/library/azure/dn788799.aspx "ici").

Ensuite, nous allons créer les artefacts nécessaires dans Azure Automation.

## <a name="create-the-automation-account-and-its-assets"></a>Créez le compte de l’automation et de ses actifs

Vous avez besoin d’un compte Azure Automation pour créer des procédures opérationnelles. Si vous ne disposez pas déjà d’un compte, accédez à onglet Azure Automation désigné par ![](media/site-recovery-runbook-automation/02.png)et créer un nouveau compte.

1.  Accordez au compte un nom permettant d’identifier avec.

2.  Spécifier une région géographique où vous souhaitez placer le compte.

Il est recommandé de placer le compte dans la même région, comme le coffre-fort de la récupération automatique du système.

![](media/site-recovery-runbook-automation/03.png)

Ensuite, créez les ressources suivantes sur le compte.

### <a name="add-a-subscription-name-as-asset"></a>Ajouter un nom d’abonnement comme actif

1.  Ajouter un nouveau paramètre ![](media/site-recovery-runbook-automation/04.png) les sélectionner pour Azure Automation actifs![](media/site-recovery-runbook-automation/05.png)

2.  Sélectionnez le type de variable en tant que **chaîne**

3.  Spécifiez le nom de la variable en tant que **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Spécifiez le nom réel de votre abonnement Azure comme la valeur de la variable.

    ![](media/site-recovery-runbook-automation/07_1.png)

Vous pouvez identifier le nom de votre abonnement à partir de la page des paramètres de votre compte sur le portail Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Ajouter des informations d’identification de connexion Azure en tant qu’actif

Automation Azure Azure PowerShell pour se connecter à l’abonnement et fonctionne sur les artefacts il. Pour ce faire, vous devez authentifier à l’aide de votre compte Microsoft ou un travail ou l’école.
Vous pouvez stocker les informations d’identification du compte de l’actif à être utilisé en toute sécurité par la procédure opérationnelle.

1.  Ajouter un nouveau paramètre ![](media/site-recovery-runbook-automation/04.png) dans les actifs d’Automation Azure et sélectionnez![](media/site-recovery-runbook-automation/09.png)

2.  Sélectionnez le type d’informations d’identification comme **Informations d’identification de Windows PowerShell**

3.  Spécifiez le nom comme **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Spécifiez le nom d’utilisateur et le mot de passe pour vous connecter à.

Ces deux paramètres sont désormais disponibles dans vos ressources.

![](media/site-recovery-runbook-automation/11.png)

Plus d’informations sur la façon de se connecter à votre abonnement via PowerShell sont données [ici](../powershell-install-configure.md).

Ensuite, vous créez une procédure opérationnelle dans Azure Automation qui peuvent d’ajouter un point de terminaison pour l’ordinateur virtuel frontal après le basculement.

## <a name="azure-automation-context"></a>Contexte d’automatisation Azure

Récupération automatique du système passe une variable de contexte à la procédure opérationnelle pour vous aider à écrire des scripts déterministes. Disent que les noms du Service Cloud et de la Machine virtuelle sont prévisibles, mais il arrive qu’il n’est pas toujours le cas en raison de certains scénarios tels que celui dans lequel le nom de l’ordinateur virtuel peut avoir modifié en raison de caractères non pris en charge dans Azure. Par conséquent, ces informations sont transmises au plan de récupération ASR dans le cadre du *contexte*.

Vous trouverez ci-dessous un exemple de l’aspect de la variable de contexte.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Le tableau ci-dessous contient le nom et la description de chaque variable dans le contexte.

**Nom de la variable** | **Description**
---|---
RecoveryPlanName | Nom de plan en cours d’exécution. Vous aide à prendre des mesures sur le nom en utilisant le même script
FailoverType | Spécifie si le basculement est test, planifié ou non.
FailoverDirection | Spécifier si la récupération est principal ou secondaire
GroupID | Identifier le numéro de groupe dans le plan de récupération lorsque le plan est en cours d’exécution.
VmMap | Tableau de tous les ordinateurs virtuels dans le groupe
Clé de VMMap | Clé unique (GUID) de chaque ordinateur virtuel. Il est identique à l’ID de VMM de la machine virtuelle, le cas échéant.
RoleName | Nom de la machine virtuelle Azure qui est en cours de restauration
CloudServiceName | Nom de Service Cloud Azure sous lequel la machine virtuelle est créée.


Pour identifier la VmMap Key dans le contexte, vous pouvez également accéder à la page de propriétés de machine virtuelle dans l’ASR et examinez la propriété VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Créer une procédure d’automatisation opérationnelle

Créer la procédure opérationnelle pour ouvrir le port 80 sur la machine virtuelle front-end.

1.  Créer une nouvelle procédure d’opérationnelle dans le compte Azure Automation avec le nom **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Accédez à l’affichage de l’auteur de la procédure opérationnelle et entrez dans le mode brouillon.

3.  Tout d’abord spécifier la variable à utiliser comme le contexte du plan de récupération

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Ensuite se connecter à l’abonnement avec le nom d’informations d’identification et d’abonnement

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Notez que vous utilisez les actifs Azure – **AzureCredential** et **AzureSubscriptionName** ici.

5.  Spécifier les détails du point de terminaison et le GUID de l’ordinateur virtuel pour lequel vous souhaitez exposer le point de terminaison. Dans ce cas, la machine virtuelle front-end.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Spécifie le protocole de point de terminaison Azure, port local sur l’ordinateur virtuel et son port public mappé. Ces variables sont les paramètres requis par les commandes Azure ajouter des points de terminaison aux machines virtuelles. La VMGUID conserve le GUID de l’ordinateur virtuel pour fonctionner sur.

6.  Le script va maintenant extraire le contexte du GUID de VM donné et créer un point de terminaison sur l’ordinateur virtuel qu’il référencé.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Une fois cette opération terminée, cliquez sur Publier ![](media/site-recovery-runbook-automation/20.png) pour permettre à votre script doivent être disponibles pour l’exécution.

Le script complet est donné ci-dessous pour votre référence.

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Ajouter le script au plan de récupération

Une fois que le script est prêt, vous pouvez l’ajouter au plan de restauration que vous avez créé précédemment.

1.  Dans le plan de récupération que vous avez créé, cliquez sur Ajouter un script après le groupe 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Spécifiez un nom de script. Il s’agit d’un nom convivial pour ce script pour l’affichage dans le plan de récupération.

3.  Dans le basculement vers le script Azure – permet de sélectionner le nom du compte d’Automation Azure.

4.  Dans les procédures opérationnelles Azure, sélectionnez la procédure opérationnelle que vous avez créés.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Scripts du côté primaire

Lorsque vous exécutez un basculement sur incident sur Azure, vous pouvez également choisir d’exécuter des scripts du côté primaire. Ces scripts s’exécuteront sur le serveur VMM lors du basculement.
Scripts du côté primaire ne sont disponibles que pour la préparation à la fermeture et de valider les étapes de l’arrêt. C’est parce que nous pensons que le site principal d’être généralement disponible lors d’un sinistre.
Au cours d’un basculement non planifié, uniquement si vous choisissez cette option pour les opérations du site principal, il tentera d’exécuter les scripts du côté primaire. Si elles ne sont pas accessibles ou délai d’attente, le basculement sur incident continue à récupérer les ordinateurs virtuels.
Scripts du côté primaire sont non disponibles pour les Sites de VMware/physique/Hyper-v sans VMM protégé vers Azure - lors du basculement vers Azure.
Toutefois, lorsque vous la restauration automatique d’Azure pour les locaux, les scripts côté primaire (procédures opérationnelles) peut être utilisée pour toutes les destinations à l’exception de VMware.

## <a name="test-the-recovery-plan"></a>Tester le plan de récupération

Une fois que vous avez ajouté la procédure opérationnelle dans le plan, vous pouvez lancer un basculement sur incident test et voir en action. Il est toujours recommandé d’exécuter un basculement de test pour tester votre application et le plan de récupération pour vous assurer qu’il n’y a pas d’erreurs.

1.  Sélectionnez le plan de récupération et d’entamer un basculement de test.

2.  Lors de l’exécution du plan, vous pouvez voir si l’exécution de la procédure opérationnelle ou non par l’intermédiaire de son état.

    ![](media/site-recovery-runbook-automation/17.png)

3.  Vous pouvez également voir l’état d’exécution de procédure opérationnelle détaillée sur la page de travaux Azure Automation pour la procédure opérationnelle.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Une fois le basculement terminé, mis à part le résultat d’exécution de procédure opérationnelle, vous pouvez voir si l’exécution a réussi ou non en visitant la page Azure VM et en examinant les points de terminaison.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Exemples de scripts

Alors que nous avons passé en revue une automatisation couramment utilisé la tâche d’ajout d’un point de terminaison à une machine virtuelle Azure dans ce didacticiel, vous pourriez faire un certain nombre d’autres tâches d’automatisation puissantes à l’aide d’automation Azure. Microsoft et la Communauté Azure Automation fournissent des procédures opérationnelles d’exemple qui peuvent vous aider à commencer à créer vos propres solutions et les procédures opérationnelles d’utilitaire, que vous pouvez utiliser comme blocs de construction de plus grandes tâches d’automatisation. Commencer à les utiliser à partir de la galerie et créer des plans de récupération d’un seul clic puissante pour vos applications à l’aide de la récupération de Site Azure.

## <a name="additional-resources"></a>Ressources supplémentaires

[Vue d’ensemble de Automation Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Automation Azure")

[Exemples de Scripts Automation Azure] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Exemples de Scripts Automation Azure")
