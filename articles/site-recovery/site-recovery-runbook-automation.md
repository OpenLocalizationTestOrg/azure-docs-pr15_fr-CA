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


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Ajouter des procédures opérationnelles d’Azure automation pour les plans de récupération


Ce didacticiel explique comment restauration du Site Azure s’intègre avec Automation Azure pour fournir l’extensibilité à des plans de récupération. Plans de reprise peuvent gérer la récupération de vos machines virtuelles protégées à l’aide de la récupération de Site Azure pour cloud secondaire de la réplication et la réplication des scénarios Azure. Ils aident également à effectuer la restauration **précise de manière cohérente**, **reproductible**et **automatisée**. Si vous ne pouvez pas sur vos ordinateurs virtuels vers Azure, intégration avec Azure Automation étend les plans de récupération et vous donne la possibilité d’exécuter les procédures opérationnelles, permettant ainsi des tâches d’automatisation puissantes.

Si vous n’avez pas encore entendu parler d’Automation d’Azure, inscrivez-vous [ici](https://azure.microsoft.com/services/automation/) et télécharger les exemples de scripts [ici](https://azure.microsoft.com/documentation/scripts/). En savoir plus sur la [Récupération de Site Azure](https://azure.microsoft.com/services/site-recovery/) et comment orchestrer la récupération vers Azure à l’aide de plans de récupération [d’ici](https://azure.microsoft.com/blog/?p=166264).

Dans ce didacticiel, nous allons étudier comment vous pouvez intégrer des procédures opérationnelles d’Azure Automation dans les plans de reprise. Nous automatiser des tâches simples qui précédemment nécessaire une intervention manuelle et voir comment convertir une récupération en plusieurs étapes en une action de récupération d’un simple clic. Nous examinerons également comment vous pouvez résoudre un script simple si elle se produit.

## <a name="customize-the-recovery-plan"></a>Personnaliser le plan de récupération

1. Commençons par operning la lame de ressources du plan de récupération. Vous pouvez visualiser que le plan de récupération a deux machines virtuelles ajoutées pour la récupération. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Cliquez sur le bouton Personnaliser pour commencer à ajouter une procédure opérationnelle. Cette action ouvre le plan de récupération personnaliser la lame.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Cliquez avec le bouton droit sur le groupe de démarrage 1 et sélectionnez cette option pour ajouter une action de post' ajouter.

4. Sélectionnez cette option pour choisir un script dans la nouvelle blade.

5. Nom du script 'Hello World'.

6. Choisissez un nom de compte de l’Automation. C’est le compte Azure Automation. Notez que ce compte peut être dans n’importe quel emplacement géographique Azure mais doit se trouver dans le même abonnement comme le coffre-fort de la récupération de Site.

7. Sélectionnez une procédure opérationnelle dans le compte de l’Automation. C’est le script qui sera exécuté lors de l’exécution du plan de récupération après la récupération du premier groupe.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Sélectionnez OK pour enregistrer le script. Cela ajoutera le script pour le groupe d’action post du groupe 1 : Démarrer.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Points saillants de l’ajout d’un script

1. Vous pouvez cliquez avec le bouton droit sur le script et choisissez 'supprimer l’étape' ou « mise à jour de script ».

2. Un script peut exécuter sur la Azure lors du basculement sur site vers Azure et exécuter sur Azure sous la forme d’un script du côté primaire avant l’arrêt, au cours de la restauration à partir d’Azure sur site.

3. Lorsqu’un script s’exécute, il injectera un contexte du plan de récupération.

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
CloudServiceName (dans le modèle de déploiement du Gestionnaire de ressources) | Nom du groupe de ressources Azure sous lequel la machine virtuelle est créée.


## <a name="using-complex-variables-per-recovery-plan"></a>À l’aide de variables complexes par un plan de récupération

Parfois, une procédure opérationnelle nécessite davantage d’informations que seulement à la RecoveryPlanContext. Il n’y a pas de mécanisme pour transmettre un paramètre à une procédure opérationnelle de première classe. Toutefois, si vous souhaitez utiliser le même script via plusieurs plans de récupération vous pouvez utiliser la variable de contexte de Plan de reprise 'RecoveryPlanName' et utiliser le dessous technique expérimentale d’utiliser une variable complexe d’Azure Automation dans une procédure opérationnelle. L’exemple ci-dessous montre comment vous pouvez créer trois différents complexe variable actifs et leur utilisation dans la procédure opérationnelle en fonction du nom du plan de récupération.

Considérez que vous souhaitez utiliser des 3 paramètres supplémentaires dans une procédure opérationnelle. Nous les codez dans un formulaire JSON {« Var1 » : « testautomation », « Var2 » : « Non planifié », « Var3 » : « PrimaryToSecondary »}

[AA complexe variable](../automation/automation-variables.md#variable-types Complex variable) permet de créer une nouvelle immobilisation de l’Automation.
Nom de la variable en tant que <RecoveryPlanName>- params.
Vous pouvez utiliser la référence ici pour créer une [variable de complexe](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Pour les plans de récupération différent, nom de la variable en tant que

1. recoveryPlanName1 >-params
2. recoveryPlanName2 >-params
3. recoveryPlanName3 >-params

Maintenant, dans le script de faire les paramètres en tant que

1. Obtenez le nom RP dans la $rpname = $Recoveryplancontext variable
2. Obtenir des actifs de $paramValue = « $($rpname)-params »
3. Utilisez-le comme une variable complexe pour le plan de récupération en appelant Get-AzureAutomationVariable [-AutomationAccountName] <String> -nom de $paramValue.

Par exemple, pour obtenir le complexe variable ou le paramètre pour le plan de récupération SharepointApp, créez une variable de complexe de Azure Automation appelée 'SharepointApp-params'.

Utiliser dans le plan de récupération en extrayant de la variable de l’actif à l’aide de l’instruction Get-AzureAutomationVariable [-AutomationAccountName] <String> [-nom] $paramValue. [Cette référence pour plus de détails](https://msdn.microsoft.com/library/dn913772.aspx)

De cette manière, le même script peut être utilisée pour le plan de récupération différents en stockant la variable complexe spécifique de plan dans les actifs.

## <a name="sample-scripts"></a>Exemples de scripts

Un référentiel de scripts que vous pouvez importer directement dans votre compte d’automation, consultez [référentiel d’OMS de Kristian Nese pour les scripts](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

Le script ici est un modèle de gestionnaire de ressources Azure qui déploiera tous les le dessous des scripts

* NSG

La procédure opérationnelle NSG va affecter des adresses IP publiques de chaque machine virtuelle dans le Plan de récupération et joindre leurs cartes réseau virtuelles à un groupe de sécurité réseau qui permettront la communication par défaut

* PublicIP

La procédure opérationnelle IP publique attribue des adresses IP publiques de chaque machine virtuelle dans le Plan de récupération. Accès aux ordinateurs et aux applications varient selon les paramètres du pare-feu au sein de chaque invité


* CustomScript

La procédure opérationnelle CustomScript attribuera adresses IP publiques à chaque machine virtuelle dans le Plan de récupération et installer une extension de script personnalisé qui extrait le faire pendant le déploiement du modèle de script

* NSGwithCustomScript

La procédure opérationnelle affecte des adresses IP publiques de chaque machine virtuelle dans le Plan de récupération, installation personnalisée à l’aide d’extension de script et connecter les cartes réseau virtuelles à un NSG permettant de NSGwithCustomScript par défaut des communications entrantes et sortantes pour l’accès distant

## <a name="additional-resources"></a>Ressources supplémentaires

[Automation Azure Service exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)

[Vue d’ensemble de Automation Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Automation Azure")

[Exemples de Scripts Automation Azure] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Exemples de Scripts Automation Azure")
