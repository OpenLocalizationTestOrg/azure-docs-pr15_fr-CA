<properties
    pageTitle="Déploiement local Git au Service d’application Azure"
    description="Apprenez à activer le déploiement Git local pour le Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="local-git-deployment-to-azure-app-service"></a>Déploiement local Git au Service d’application Azure

Ce didacticiel vous montre comment déployer votre application au [Service d’application Azure] à partir d’un référentiel Git sur votre ordinateur local. Service d’application prend en charge cette approche avec l’option de déploiement **Local Git** dans [Azure Portal].  
La plupart des commandes Git décrits dans cet article sont exécutées automatiquement lors de la création d’une application de Service de l’application à l’aide de l' [Interface de ligne de commande de Azure] comme décrit [ici](app-service-web-get-started.md).

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez :

- GIT. Vous pouvez télécharger l’installation binaire [ici](http://www.git-scm.com/downloads).  
- Connaissances de base sur Git.
- Un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial) ou [activer vos avantages d’abonné de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.  

## <a name="Step1"></a>Étape 1 : Création d’un référentiel local

Effectuer les tâches suivantes pour créer un nouveau référentiel Git.

1. Démarrer un outil de ligne de commande, telles que **GitBash** (Windows) ou **Bash** (Shell Unix). Sur les systèmes OS X vous pouvez accéder à la ligne de commande par l’intermédiaire de l’application **Terminal** .

2. Accédez au répertoire où le contenu à déployer serait localisé.

3. Pour initialiser un nouveau référentiel Git, utilisez la commande suivante :

        git init

## <a name="Step2"></a>Étape 2 : Valider votre contenu

Service d’application prend en charge les applications créées dans une variété de langages de programmation. 

1. Si votre référentiel inclut déjà contenu ignorer ce point et déplacez au point 2 ci-dessous. Si votre référentiel ne possède pas contenu simplement remplir avec un fichier .html statique comme suit : 

    - À l’aide d’un éditeur de texte, créez un nouveau fichier nommé **index.html** à la racine du référentiel Git
    - Ajoutez le texte suivant comme le contenu de la index.html de fichier et l’enregistrer : *Git Hello !*
        
2. À partir de la ligne de commande, vérifiez que vous êtes sous la racine de votre référentiel Git. Puis utilisez la commande suivante pour ajouter des fichiers à votre référentiel :

        git add -A 

4. Ensuite, validez les modifications dans le référentiel à l’aide de la commande suivante :

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Étape 3 : Activez le référentiel d’application de Service d’application

Effectuez les étapes suivantes pour activer un référentiel Git pour votre application de Service de l’application.

1. Connectez-vous au [portail Azure].

2. Dans les lames de votre application de Service de l’application, cliquez sur **les paramètres > source de déploiement**. Cliquez sur **Choisir les sources**, puis cliquez sur **Un référentiel Git Local**puis cliquez sur **OK**.  

    ![Référentiel de Git local](./media/app-service-deploy-local-git/local_git_selection.png)

3. S’il s’agit de votre première configuration de temps un référentiel dans Azure, vous devez créer des informations d’identification pour celui-ci. Vous les utiliserez pour ouvrir une session dans les modifications Azure de référentiel et de diffusion à partir de votre référentiel Git local. À partir de lames de votre application, cliquez sur **les paramètres > informations d’identification de déploiement**, puis configurer votre mot de passe et le nom d’utilisateur du déploiement. Lorsque vous avez terminé, cliquez sur **Enregistrer**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Étape 4 : Déployer votre projet

Procédez comme suit pour publier votre application au Service d’application à l’aide de Git Local.

1. Dans les lames de votre application dans Azure Portal, cliquez sur **les paramètres > Propriétés** pour l' **URL de Git**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **URL de GIT** est la référence à distance pour déployer vers votre référentiel local. Vous utiliserez cette URL dans les étapes suivantes.

2. À l’aide de la ligne de commande, vérifiez que vous êtes à la racine de votre référentiel Git local.

3. Utilisez `git remote` pour ajouter la référence à distance répertoriées dans le **Git URL** à partir de l’étape 1. Votre commande sera semblable au suivant :

        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] La commande **à distance** ajoute une référence nommée vers un référentiel distant. Dans cet exemple, il crée une référence nommée « azure » pour les référentiels de votre application web.

4. Votre contenu est transféré au Service de l’application à l’aide de la nouvelle **azure** distant que vous venez de créer.

        git push azure master

    Vous demandera le mot de passe que vous avez créé précédemment lors de la réinitialisation de vos informations d’identification de déploiement dans le portail Azure. Entrez le mot de passe (Notez que Gitbash n’affiche pas d’astérisques dans la console lorsque vous tapez votre mot de passe). 
       
5. Revenez à votre application dans Azure Portal. Une entrée de journal de votre plus récente campagne doit s’afficher dans la lame de **déploiements** . 

    ![](./media/app-service-deploy-local-git/deployment_history.png)

6. Cliquez sur le bouton **Parcourir** dans la partie supérieure de la lame de l’application pour vérifier que le contenu a été déployé. 
    
## <a name="Step5"></a>Résolution des problèmes

Voici les erreurs ou les problèmes couramment rencontrés lors de l’utilisation de Git pour publier dans une application de Service de l’application dans Azure :

****

**Problème**: Impossible d’accéder au « [siteURL] » : Impossible de se connecter à [scmAddress]

**Cause**: cette erreur peut se produire si l’application n’est pas en cours d’exécution.

**Résolution**: démarrer l’application dans Azure Portal. Déploiement de GIT ne fonctionnera pas à moins que l’application est en cours d’exécution. 


****

**Symptôme**: n’a pas pu résoudre l’hôte 'nom_hôte'

**Cause**: cette erreur peut se produire si les informations d’adresse entrées lors de la création du 'azure' distant étaient incorrectes.

**Résolution**: utilisez le `git remote -v` commande pour répertorier toutes les bases de données distantes, ainsi que l’URL associée. Vérifiez que l’URL de la 'azure' distant est correcte. Si nécessaire, supprimer et recréer ce à distance à l’aide de l’URL est correcte.

****

**Symptôme**: aucune référence en commun et aucun n’est spécifié ; ne rien faire. Par exemple, vous devez spécifier une branche de 'master'.

**Cause**: cette erreur peut se produire si vous ne spécifiez pas une branche lors de l’exécution un git push d’opération et n’avez pas défini la valeur de push.default utilisée par le Git.

**Résolution**: effectuer l’opération push, spécification de la branche principale. Par exemple :

    git push azure master

****

**Symptôme**: src refspec [branchname] ne correspond pas à un.

**Cause**: cette erreur peut se produire si vous essayez de vous pousser à une branche de maître sur le 'azure' à distance.

**Résolution**: effectuer l’opération push, spécification de la branche principale. Par exemple :

    git push azure master

****

**Symptôme**: erreur - modifications envoyées au référentiel distant, mais votre application web ne pas mis à jour.

**Cause**: cette erreur peut se produire si vous déployez une application Node.js contenant un fichier package.json qui spécifie des modules supplémentaires requis.

**Résolution**: des messages supplémentaires contenant « npm ERR ! » doivent être enregistré avant cette erreur et peut fournir le contexte supplémentaires sur l’échec. Les éléments suivants constituent les causes de cette erreur et le correspondant « npm ERR ! » Message :

* **Fichier de package.json incorrecte**: npm ERR ! Impossible de lire les dépendances.

* **Module natif, qui ne dispose pas d’une distribution binaire pour Windows**:

    * npm ERR ! \`cmd « / c » « reconstruction nœud-gyp »\` a échoué avec 1

        OU

    * npm ERR ! [modulename@version]préinstaller : \`rendre || gmake\`


## <a name="additional-resources"></a>Ressources supplémentaires

* [Documentation de GIT](http://git-scm.com/documentation)
* [Documentation du projet Kudu](https://github.com/projectkudu/kudu/wiki)
* [Déploiement continue à un Service d’application Azure](app-service-continuous-deployment.md)
* [L’utilisation de PowerShell pour Azure](../powershell-install-configure.md)
* [Comment faire pour utiliser l’Interface de ligne de commande de Azure](../xplat-cli-install.md)

[Service d’application Azure]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Interface de ligne de commande Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
