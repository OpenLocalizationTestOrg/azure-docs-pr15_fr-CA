<properties
    pageTitle="Configurer la mise en attente des environnements pour les applications web dans le Service d’application Azure"
    description="Apprenez à utiliser la publication intermédiaire pour les applications web dans le Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>Configurer la mise en attente des environnements pour les applications web dans le Service d’application Azure
<a name="Overview"></a>

Lorsque vous déployez votre application web au [Service de l’application](http://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez déployer sur un emplacement de déploiement séparé au lieu de l’emplacement de production par défaut lors de l’exécution en mode plan **Standard** ou **Premium** application Service. Les emplacements de déploiement sont des applications web résident avec leurs propres noms d’hôtes. Éléments de contenu et des configurations Web app peuvent être transférées entre deux emplacements de déploiement, y compris l’emplacement de production. Déploiement de votre application à un emplacement de déploiement présente les avantages suivants :

- Vous pouvez valider les modifications d’application web dans un emplacement de déploiement intermédiaire avant qu’il échange avec l’emplacement de production.

- Déploiement d’une application web vers un emplacement de premier et il échange en production garantissent que toutes les instances de l’emplacement sont échauffés avant permutés en production. Cela élimine les interruptions de service lors du déploiement de votre application web. La redirection du trafic est transparente et aucune demande n’est supprimés à la suite des opérations de swap. Ce flux de travail entière peut être automatisé en configurant [Automatiquement remplacer](#configure-auto-swap-for-your-web-app) lors de la validation de swap avant n’est pas nécessaire.

- Après un remplacement, l’emplacement avec une application web précédemment nouvellement créé a maintenant l’application web de production précédente. Si les modifications échangées dans l’emplacement de production sont pas comme vous le souhaitiez, vous pouvez effectuer la même swap immédiatement pour obtenir votre « dernier site connu » précédent.

Chaque mode plan de Service d’application prend en charge un nombre différent d’emplacements de déploiement. Pour connaître le nombre d’emplacements de prise en charge du mode de votre application web, consultez le [Tarif de Service de l’application](/pricing/details/app-service/).

- Lorsque votre application web a plusieurs emplacements, vous ne pouvez pas modifier le mode.

- Mise à l’échelle n’est pas disponible pour les emplacements hors production.

- Gestion des ressources liées n’est pas pris en charge pour les emplacements hors production. Dans le [Portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) uniquement, vous pouvez éviter cet impact potentiel sur un emplacement de production en déplaçant temporairement l’emplacement hors production à un autre mode de plan de Service de l’application. Notez que l’emplacement hors production doit encore une fois partagent le même mode avec l’emplacement de production avant de vous pouvez permuter les deux emplacements.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>Ajouter un emplacement de déploiement d’une application web ##

L’application web doit être en cours d’exécution en mode **Standard** ou **Premium** dans pour que vous puissiez activer plusieurs emplacements de déploiement.

1. Dans le [Portail Azure](https://portal.azure.com/), ouvrez les lames de votre application web.
2. Cliquez sur **paramètres**, puis cliquez sur **les emplacements de déploiement**. Puis, dans la blade **d’emplacements de déploiement** , cliquez sur **Ajouter un emplacement**.

    ![Ajouter un nouvel emplacement de déploiement][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > Si l’application web n’est pas déjà en mode **Standard** ou **Premium** , vous recevrez un message indiquant les modes pris en charge pour activer la publication par étapes. À ce stade, vous avez la possibilité de sélectionner la **mise à niveau** et accédez à l’onglet de **l’échelle** de votre application web avant de continuer.

2. Dans la lame **Ajouter un emplacement** , donnez un nom à l’emplacement et indiquez si vous souhaitez dupliquer la configuration d’application web à partir d’un autre emplacement de déploiement existant. Cliquez sur la case à cocher pour continuer.

    ![Source de configuration][ConfigurationSource1]

    La première fois que vous ajoutez un emplacement, vous pourrez uniquement deux choix : configuration du clone à partir de l’emplacement par défaut dans la production ou pas du tout.

    Une fois que vous avez créé plusieurs connecteurs, vous serez en mesure de cloner la configuration à partir d’un emplacement différent de celui de la production :

    ![Sources de configuration][MultipleConfigurationSources]

5. De la lame **d’emplacements de déploiement** , cliquez sur l’emplacement de déploiement pour ouvrir une lame pour l’emplacement, avec un ensemble de mesures et de configuration tout comme n’importe quelle autre application web. **Your-Web-App-Name-Deployment-slot-Name** s’affiche en haut de la lame pour vous rappeler que vous affichez l’emplacement de déploiement.

    ![Titre d’emplacement de déploiement][StagingTitle]

5. Cliquez sur l’URL de l’application dans les lames de l’emplacement. Notez l’emplacement de déploiement a son propre nom d’hôte et est également une application live. Pour limiter l’accès du public à l’emplacement de déploiement, reportez-vous à la section [Application Service Web App – bloquer l’accès aux emplacements de déploiement de test web](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Il n’y a aucun contenu après la création d’emplacement de déploiement. Vous pouvez déployer à l’emplacement à partir d’une branche de référentiel différent ou un référentiel complètement différent. Vous pouvez également modifier la configuration de l’emplacement. Utiliser les références de publication profil ou de déploiement associés à l’emplacement de déploiement des mises à jour de contenu.  Par exemple, vous pouvez [publier à cet emplacement avec git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>Configuration des emplacements de déploiement ##
Lorsque vous clonez configuration à partir d’un autre emplacement de déploiement, la configuration clonée est modifiable. En outre, certains éléments de configuration suit le contenu au sein d’un échange (non spécifique à l’emplacement) tandis que les autres éléments de configuration restent dans le même emplacement après un échange (emplacement spécifique). Les listes suivantes indiquent la configuration qui change lorsque vous changez les emplacements.

**Les paramètres qui sont échangées**:

- Paramètres généraux - telles que Web sockets de framework version 32/64 bits,
- Paramètres de l’application (peut être configuré pour respecter un emplacement)
- Chaînes de connexion (peuvent être configurés pour respecter un emplacement)
- Mappages de gestionnaires
- Paramètres de surveillance et de Diagnostics
- Contenu de WebJobs

**Les paramètres qui ne sont pas échangées**:

- Publication des points de terminaison
- Noms de domaine personnalisé
- Les liaisons et les certificats SSL
- Paramètres d’échelle
- Planificateurs de WebJobs

Pour configurer une chaîne de connexion ou le paramètre application conformément à un emplacement (ne pas échangé), accédez à la lame de **Paramètres d’Application** pour un emplacement spécifique, puis activez la case du **Paramètre d’emplacement** pour les éléments de configuration qui est préférable de conserver l’emplacement. Notez qu’un élément de configuration de marquage comme spécifique à l’emplacement a pour effet d’établir cet élément comme non remplaçables à travers tous les emplacements de déploiement associées à l’application web.

![Paramètres d’emplacement][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>Pour permuter les emplacements de déploiement ##

>[AZURE.IMPORTANT] Avant que vous remplacez une application web à partir d’un emplacement de déploiement en production, assurez-vous que tous les paramètres spécifiques non slots sont configurés exactement comme vous souhaitez qu’il dans la cible de l’échange.

1. Pour échanger les emplacements de déploiement, cliquez sur le bouton **ECHANGER** dans la barre de commandes de l’application web ou dans la barre de commande d’un emplacement de déploiement. Assurez-vous que la source de l’échange et la cible de swap sont correctement définies. En règle générale, la cible de swap est l’emplacement de production.  

    ![Bouton de permutation][SwapButtonBar]

3. Cliquez sur **OK** pour terminer l’opération. Lorsque l’opération se termine, les emplacements de déploiement ont été échangées.

## <a name="configure-auto-swap-for-your-web-app"></a>Configurer l’échange d’automatique pour votre application web ##

Échange d’automatique rationalise DevOps les scénarios où vous voulez déployer en continu de votre application web avec zéro démarrage à froid et aucune interruption de service pour les clients finaux de l’application web. Lorsqu’un emplacement de déploiement est configuré pour l’échange d’automatique en production, chaque fois que vous poussez votre mise à jour du code à cet emplacement, Service d’application bascule automatiquement l’application web en production après que qu’il a déjà échauffé dans l’emplacement.

>[AZURE.IMPORTANT] Lorsque vous activez l’option Auto Swap pour un emplacement, assurez-vous que la configuration des emplacements est exactement la configuration destinée à l’emplacement cible (généralement l’emplacement de production).

Il est facile de configuration automatique Swap pour un emplacement. Suivez les étapes ci-dessous :

1. Dans la blade **d’Emplacements de déploiement** , sélectionnez un emplacement hors production, **Tous les paramètres** pour les lames de cet emplacement.  

    ![][Autoswap1]

2. Cliquez sur **paramètres de l’Application**. Sélectionnez **activé** pour **l’Échange d’automatique**, sélectionnez l’emplacement cible de votre choix dans **l’Emplacement d’échange automatique**et cliquez sur **Enregistrer** dans la barre de commandes. Assurez-vous que la configuration de l’emplacement est exactement la configuration destinée à l’emplacement cible.

    L’onglet **Notifications** clignote en vert **succès** une fois l’opération terminée.

    ![][Autoswap2]

    >[AZURE.NOTE] Pour tester la permutation d’automatique pour votre application web, vous sélectionnez d’abord un emplacement cible de non production dans un **Emplacement de remplacement automatique** pour vous familiariser avec la fonctionnalité.  

3. Exécute un push du code à cet emplacement de déploiement. Échange d’automatique se produit après un bref moment et de la mise à jour est reflétée dans les URL d’emplacement de votre cible.

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>Échange de plusieurs phase d’utiliser pour votre application web ##

Échange de plusieurs phase est disponible pour simplifier la validation dans le contexte d’éléments de configuration conçu conformément à un emplacement de chaînes de connexion. Dans ces cas, il peut être utile d’appliquer ces éléments de configuration à partir de la cible de la permutation à la source de remplacement et les valider avant l’échange entre effectivement en vigueur. Une fois que l’échange des éléments de configuration cible sont appliquées à la source de remplacement, les actions disponibles sont soit à la fin de l’échange ou le retour à la configuration d’origine de la source de remplacement qui a également pour effet d’annuler le remplacement. Exemples pour les applets de commande PowerShell de Azure disponible pour la permutation de plusieurs phase sont inclus dans les applets de commande PowerShell de Azure pour la section emplacements de déploiement.

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>Pour restaurer une application de production après remplacement ##

Si des erreurs sont identifiés dans la production après un échange de logement, restaurer les emplacements de leurs États de swap avant en remplaçant les deux fentes mêmes immédiatement.

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>Préchauffage personnalisé avant la permutation ##

Certaines applications peuvent nécessiter des actions de préparation personnalisé. L’élément de configuration applicationInitialization dans le fichier web.config vous permet de spécifier des actions d’initialisation personnalisées à exécuter avant la réception d’une demande. L’opération d’échange attend cette préparation personnalisée terminer. Voici un exemple de fragment de web.config.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>Pour supprimer un emplacement de déploiement##

De la lame pour un emplacement de déploiement, cliquez sur **Supprimer** dans la barre de commandes.  

![Supprimer un emplacement de déploiement][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Applets de commande PowerShell Azure pour des emplacements de déploiement

PowerShell Azure est un module qui fournit des applets de commande pour gérer les Azure par le biais de Windows PowerShell, notamment la prise en charge pour la gestion des emplacements de déploiement d’application web dans le Service d’application Azure.

- Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell et authentification Azure PowerShell avec votre abonnement Azure, voir [Comment faire pour installer et configurer Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### <a name="create-web-app"></a>Créer l’application web

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>Créer un emplacement de déploiement d’une application web

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>Initier l’échange de plusieurs phase et appliquer la configuration d’emplacement cible à l’emplacement de la source

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>Rétablir la première phase de la permutation de plusieurs phase et de restaurer la configuration d’emplacement source

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>Échangez les emplacements de déploiement

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>Supprimer l’emplacement de déploiement

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Commandes de l’Interface de ligne de commande (CLI Azure) Azure pour des emplacements de déploiement

La CLI Azure fournit les commandes multiplates-formes pour travailler avec Azure, y compris la prise en charge de la gestion des emplacements de déploiement Web App.

- Pour obtenir des instructions sur l’installation et la configuration de l’interface CLI Azure, y compris des informations sur la connexion CLI d’Azure à votre abonnement Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md).

-  Pour répertorier les commandes disponibles pour le Service d’application Azure dans la CLI d’Azure, appelez `azure site -h`.

----------
### <a name="azure-site-list"></a>liste des sites d’Azure
Pour plus d’informations sur les applications web dans l’abonnement en cours, appelez la **liste site azure**, comme dans l’exemple suivant.

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>Création de site Azure
Pour créer un emplacement de déploiement, appelez **azure site créer** et spécifier le nom d’une application web existante et le nom de l’emplacement à créer, comme dans l’exemple suivant.

`azure site create webappslotstest --slot staging`

Pour activer le contrôle de code source pour le nouvel emplacement, utilisez l’option **--git** , comme dans l’exemple suivant.

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>échange de site Azure
Pour rendre le déploiement de mises à jour emplacement de l’application de production, de la commande **swap de site azure** permet d’effectuer une opération de permutation, comme dans l’exemple suivant. L’application de production ne rencontreront aucun temps d’arrêt, ni subiront un démarrage à froid.

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>supprimer le site Azure
Pour supprimer un emplacement de déploiement qui n’est plus nécessaire, utilisez la commande **Supprimer du site azure** , comme dans l’exemple suivant.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="next-steps"></a>Étapes suivantes ##
[Azure Application Service Web App – bloquer l’accès web aux emplacements de déploiement non-production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Version d’évaluation gratuite de Microsoft Azure](/pricing/free-trial/)

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
