<properties
   pageTitle="Gestion d’erreur automation Azure | Microsoft Azure"
   description="Cet article fournit des erreurs de base des étapes de gestion pour résoudre et corriger les erreurs d’automatisation d’Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="Erreur d’automatisation, de gestion des erreurs"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Conseils pour les erreurs courantes Azure Automation de gestion d’erreur

Cet article explique certaines des erreurs courantes Azure Automation vous pouvez rencontrer et suggère possible de gestion d’erreur suit.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Résoudre les erreurs d’authentification lorsque vous travaillez avec des procédures opérationnelles d’Azure Automation  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Scénario : Se connecter à un compte Azure a échoué

**Erreur :** Vous recevez l’erreur « Unknown_user_type : Type inconnu d’utilisateur » lorsque vous travaillez avec les applets de commande Add-AzureAccount ou AzureRmAccount de la connexion.

**Raison de l’erreur :** Cette erreur se produit si le nom de la ressource d’informations d’identification n’est pas valide ou si le nom d’utilisateur et le mot de passe que vous avez utilisé pour l’installation de la ressource d’informations d’identification de Automation ne sont pas valides.

**Des conseils de dépannage :** Pour déterminer quel est le problème, procédez comme suit :  

1. Assurez-vous que vous n’avez pas tous les caractères spéciaux, y compris la **@** caractères dans le nom de la ressource d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.  

2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et le mot de passe sont stockés dans les informations d’identification d’Azure Automation dans votre éditeur de PowerShell ISE local. Pour cela, vous pouvez exécuter les applets de commande suivantes dans PowerShell ISE :  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Si l’authentification échoue localement, cela signifie que vous n’avez pas défini vos informations d’identification Active Directory de Azure correctement. Consultez le billet de blog [authentification vers Azure à l’aide d’Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour obtenir le compte Azure Active Directory configuré correctement.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure

**Erreur :** Vous recevez l’erreur « l’abonnement nommé ``<subscription name>`` ne peut pas être trouvé » lorsque vous travaillez avec les applets de commande Select-AzureSubscription ou sélectionnez-AzureRmSubscription.

**Raison de l’erreur :** Cette erreur se produit si le nom d’abonnement n’est pas valide ou si l’utilisateur Active Directory de Azure qui essaie d’obtenir des informations sur l’abonnement n’est pas configuré en tant qu’administrateur de l’abonnement.

**Des conseils de dépannage :** Pour déterminer si vous avez correctement authentifié vers Azure et avez accès à l’abonnement que vous voulez sélectionner, procédez comme suit :  

1. Assurez-vous que vous exécutez l' **Add-AzureAccount** avant d’exécuter l’applet de commande **Select-AzureSubscription** .  

2. Si vous voyez toujours ce message d’erreur, modifiez votre code en ajoutant l’applet de commande **Get-AzureSubscription** suivant l’applet de commande **Add-AzureAccount** , puis exécutez le code.  Maintenant, vérifiez si la sortie de Get-AzureSubscription contient les détails de votre abonnement.  
    * Si vous ne voyez pas les détails de l’abonnement dans la sortie, cela signifie que l’abonnement n’est pas encore initialisé.  
    * Si vous ne voyez pas les détails de l’abonnement dans la sortie, vérifiez que vous utilisez le nom d’abonnement correct ou l’ID avec l’applet de commande **Select-AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scénario : Azure l’authentification a échoué car l’authentification selon plusieurs facteur est activée

**Erreur :** Vous recevez l’erreur « Add-AzureAccount : AADSTS50079 : inscription de l’authentification renforcée (preuve-up) est requise » lors de l’authentification pour Azure avec votre nom d’utilisateur Azure et d’un mot de passe.

**Raison de l’erreur :** Si vous avez plusieurs facteurs d’authentification de votre compte Azure, vous ne pouvez pas utiliser un utilisateur Azure Active Directory pour authentifier vers Azure.  Au lieu de cela, vous devez utiliser un certificat ou un service principal pour s’authentifier sur Azure.

**Des conseils de dépannage :** Pour utiliser un certificat avec les applets de commande de gestion des services Azure, reportez-vous à [Création et ajout d’un certificat pour gérer les services Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser une entité de service avec le Gestionnaire de ressources Azure applets de commande, reportez-vous à [Création d’entité de service à l’aide d’Azure portal](./resource-group-create-service-principal-portal.md) et [l’authentification d’un principal de service avec le Gestionnaire de ressources Azure.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Résoudre les erreurs courantes lors de l’utilisation des procédures opérationnelles

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scénario : Procédure opérationnelle échoue en raison de l’objet désérialisé

**Erreur :** Votre procédure opérationnelle échoue avec l’erreur « Impossible de lier le paramètre ``<ParameterName>``. Impossible de convertir le ``<ParameterType>`` valeur de type Deserialized ``<ParameterType>`` à taper ``<ParameterType>``».

**Raison de l’erreur :** Si votre procédure opérationnelle est un flux de travail de PowerShell, il stocke des objets complexes dans un format désérialisé pour faire persister l’état de votre procédure opérationnelle si le workflow est suspendu.  

**Des conseils de dépannage :**  
Une des trois solutions suivantes pour corriger ce problème :

1. Si le module de tuyauterie vous sont des objets complexes d’un cmdlet à un autre, encapsuler ces applets de commande dans un InlineScript.  
2. Transmettez le nom ou la valeur dont vous avez besoin à partir de l’objet complexe au lieu de passer l’objet entier.  

3. Au lieu d’une procédure opérationnelle du flux de travail de PowerShell, utiliser une procédure opérationnelle de PowerShell.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scénario : Tâche de procédure opérationnelle a échoué car le quota alloué dépassé

**Erreur :** Votre travail de procédure opérationnelle échoue avec l’erreur « le quota pour le travail total mensuel d’exécution a été atteint pour cet abonnement ».

**Raison de l’erreur :** Cette erreur se produit lors de l’exécution de la tâche dépasse le quota de libre de 500-minute de votre compte. Ce quota s’applique à tous les types de tâches d’exécution tels que les tests d’une tâche, démarrage d’une tâche à partir du portail, l’exécution d’un travail à l’aide de webhooks et de planification d’un travail à exécuter à l’aide du portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification pour Automation voir [Automation de tarification](https://azure.microsoft.com/pricing/details/automation/).

**Des conseils de dépannage :** Si vous souhaitez utiliser plus de 500 minutes de traitement par mois, vous devrez modifier votre abonnement à partir de la couche à la couche de base. Vous pouvez mettre à niveau à la couche de base en effectuant les opérations suivantes :  

1. Connectez-vous à votre abonnement Azure  
2. Sélectionnez le compte d’Automation que vous souhaitez mettre à jour  
3. Cliquez sur **paramètres** > **niveau de tarification et d’utilisation** > **niveau de tarification**  
4. Sur la lame de **Choisir votre niveau de tarification** , sélectionnez de **base**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scénario : Applet de commande non reconnu lors de l’exécution d’une procédure opérationnelle

**Erreur :** Votre travail de procédure opérationnelle échoue avec l’erreur «``<cmdlet name>``: le terme ``<cmdlet name>`` n’est pas reconnu comme le nom de l’applet de commande, fonction, fichier de script ou un programme exécutable. »

**Raison de l’erreur :** Cette erreur est provoquée lorsque le moteur PowerShell ne peut pas trouver l’applet de commande que vous utilisez dans votre procédure opérationnelle.  Cela peut être parce que le module contenant l’applet de commande ne figure pas dans le compte, il existe un conflit de nom avec un nom de procédure opérationnelle, ou l’applet de commande existe également dans un autre module et Automation ne peut pas résoudre le nom.

**Des conseils de dépannage :** Une des solutions suivantes corrigera le problème :  

- Vérifiez que vous avez entré correctement le nom de l’applet de commande.  

- Vérifiez que l’applet de commande existe dans votre compte d’Automation et qu’il n’y a pas de conflit. Pour vérifier si l’applet de commande est présent, ouvrir une procédure opérationnelle en mode édition et recherche de l’applet de commande que vous souhaitez rechercher dans la bibliothèque ou l’exécution **la commande Get ``<CommandName>`` **.  Une fois que vous avez validé que l’applet de commande n’est disponible pour le compte et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou les procédures opérationnelles, ajouter à la zone de dessin et vous assurer que vous utilisez un paramètre valide défini dans votre procédure opérationnelle.  

- Si vous n’avez pas besoin d’un conflit de nom et de l’applet de commande n’est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Par exemple, vous pouvez utiliser **ModuleName\CmdletName**.  

- Si vous exécutez une procédure opérationnelle sur site dans un groupe de travailleurs hybride, assurez-vous que le module/applet de commande est installé sur l’ordinateur qui héberge le travailleur hybride.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scénario : Une procédure opérationnelle longue ne parvient pas à l’exception : « la tâche ne peut pas continuer car il a été supprimé à plusieurs reprises le même point de contrôle ».

**Raison de l’erreur :** Il s’agit par le comportement de conception en raison de la surveillance des processus d’automatisation d’Azure, qui suspend automatiquement une procédure opérationnelle si elle s’exécute plus de 3 heures « Équitable ». Toutefois, le message d’erreur ne fournit pas de « étapes suivantes » options. Une procédure opérationnelle peut être interrompu pour un certain nombre de raisons. Suspend se produire principalement en raison d’erreurs. Par exemple, une exception non interceptée dans une procédure opérationnelle, une défaillance du réseau ou de panne du travailleur de procédure opérationnelle qui exécute la procédure opérationnelle, tous entraîne la procédure opérationnelle à être suspendu et démarrer à partir de son dernier point de contrôle lors de la reprise.

**Des conseils de dépannage :** La solution pour éviter ce problème consiste à utiliser des points de contrôle dans un flux de travail.  Pour en savoir plus reportez-vous aux [Workflows de PowerShell d’apprentissage](automation-powershell-workflow.md#Checkpoints).  Vous trouverez une explication plus approfondie de « Équitable » et le point de contrôle dans cet article de blog [à l’aide de points de contrôle dans les procédures opérationnelles](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Résoudre les erreurs courantes lors de l’importation des modules

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scénario : Module ne parvient pas à importer ou applets de commande ne peut pas être exécutée après l’importation

**Erreur :** Un module ne parvient pas à importer ou importe correctement, mais sans applets de commande sont extraits.

**Raison de l’erreur :** Certaines raisons fréquentes pour lesquelles un module ne peut ne pas importe correctement à Azure Automation sont les suivants :  

- La structure ne correspond pas à la structure Automation doit pouvoir être dans.  

- Le module dépend d’un autre module n’a pas été déployé pour votre compte d’Automation.  

- Le module n’a pas ses dépendances dans le dossier.  

- L’applet de commande **New-AzureRmAutomationModule** est utilisé pour charger le module, et vous ne dispose que le chemin d’accès complet de stockage ou n’avez pas chargé le module en utilisant une URL accessible publiquement.  

**Des conseils de dépannage :**  
Une des solutions suivantes corrigera le problème :  

- Assurez-vous que le module suit le format suivant :  
ModuleName.Zip **->** ModuleName ou le numéro de Version **->** (ModuleName.psm1, ModuleName.psd1)

- Ouvrez le fichier .psd1 et si le module a des dépendances.  Si c’est le cas, télécharger ces modules sur le compte de l’Automation.  

- Assurez-vous que les fichiers .dll référencés sont présents dans le dossier du module.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Résoudre les erreurs courantes lors de l’utilisation avec Configuration état souhaité (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scénario : Nœud est en état d’échec avec une erreur « Introuvable »

**Erreur :** Le nœud a un rapport avec l’état **Échec** et contenant l’erreur « la tentative d’obtention de l’action à partir du serveur https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction a échoué, car une configuration valide ``<guid>`` ne peut pas être trouvé. »

**Raison de l’erreur :** Cette erreur se produit généralement lorsque le nœud est assigné à un nom de configuration (par exemple ABC) au lieu d’un nom de configuration de nœud (par exemple ABC. Serveur Web).  

**Des conseils de dépannage :**  

- Assurez-vous que vous affectez le nœud avec le « nom du nœud configuration » et pas le « nom de configuration ».  

- Vous pouvez affecter une configuration nœud à un nœud à l’aide d’Azure portal, ou avec une applet de commande PowerShell.
    - Afin d’attribuer une configuration nœud à un nœud à l’aide d’Azure portal, ouvrir la lame **DSC nœuds** , puis sélectionnez un nœud et cliquez sur le bouton **assigner la configuration du nœud** .  
    - Pour assigner une configuration nœud à un nœud à l’aide d’applet de commande PowerShell, utilisez l’applet de commande **Set-AzureRmAutomationDscNode**


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scénario : Aucune configuration de nœud (fichiers MOF) ne produites lors de la compilation d’une configuration

**Erreur :** Votre travail de compilation de DSC suspend avec l’erreur : « Compilation s’est terminée correctement, mais sans .mofs de configuration du nœud ont été générés ».

**Raison de l’erreur :** Lorsque l’expression après que le mot clé de **nœud** dans la configuration de DSC évalue $null puis aucune configuration nœud sera produit.    

**Des conseils de dépannage :**  
Une des solutions suivantes corrigera le problème :  

- Assurez-vous que l’expression en regard du mot-clé de **nœud** dans la définition de la configuration n’est pas évaluer sur $null.  
- Si vous transmettez ConfigurationData lors de la compilation de la configuration, assurez-vous que vous transmettez les valeurs attendues nécessitant la configuration à partir de [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scénario : L’état du nœud DSC se bloque l’état « en cours »

**Erreur :** Agent de DSC ne renvoie « Aucune instance trouvée avec valeurs de propriétés fournies. »

**Raison de l’erreur :** Vous mis à niveau votre version WMF et endommagé WMI.  

**Des conseils de dépannage :** Suivez les instructions dans le billet de blog [DSC des limitations et problèmes connus](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) pour résoudre le problème.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Scénario : Impossible d’utiliser les informations d’identification dans une configuration de DSC

**Erreur :** Votre travail de compilation de DSC a été interrompue avec l’erreur : « erreur System.InvalidOperationException 'Références' du type de propriété de traitement ``<some resource name>``: conversion et stockage de mot de passe crypté en texte en clair est autorisée uniquement si PSDscAllowPlainTextPassword a la valeur True ».

**Raison de l’erreur :** Vous utiliser une information d’identification dans une configuration mais n’a pas fourni de bon **ConfigurationData** pour **PSDscAllowPlainTextPassword** la valeur true pour la configuration de chaque nœud.  

**Des conseils de dépannage :**  
- Veillez à passer dans le bon **ConfigurationData** à **PSDscAllowPlainTextPassword** la valeur true pour chaque configuration de nœud mentionnée dans la configuration. Pour plus d’informations, consultez [ressources dans Azure Automation DSC](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Étapes suivantes

Si vous avez suivi les étapes de dépannage ci-dessus et que vous avez besoin d’aide supplémentaire à tout moment dans cet article, vous pouvez :

- Obtenir de l’aide d’experts Azure. Soumettre votre problème à le [des forums MSDN Azure ou débordement de pile.](https://azure.microsoft.com/support/forums/)

- Un incident de support Azure du fichier. Aller sur le [site de Support d’Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **obtenir de l’aide** sous **support technique et la facturation**.

- Valider une demande de Script sur le [Centre de scripts](https://azure.microsoft.com/documentation/scripts/) si vous recherchez une solution de procédure opérationnelle Azure Automation ou d’un module d’intégration.

- Valider la fonctionnalité de commentaires ou des demandes Automation Azure sur la [Voix de l’utilisateur](https://feedback.azure.com/forums/34192--general-feedback).
