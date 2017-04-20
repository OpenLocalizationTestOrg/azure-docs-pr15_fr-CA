<properties 
    pageTitle="Déploiement de votre première application web vers Azure dans cinq minutes | Microsoft Azure" 
    description="Découvrez combien il est facile d’exécuter des applications web dans le Service d’application en déployant un exemple d’application. Commencent le développement réel rapidement et de voir immédiatement les résultats." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>Déploiement de votre première application web vers Azure en cinq minutes

Ce didacticiel vous permet de déployer une application web HTML + CSS simple au [Service d’application Azure](../app-service/app-service-value-prop-what-is.md).
Vous pouvez utiliser le Service d’application pour créer des applications web [mobile application back-end](/documentation/learning-paths/appservice-mobileapps/)et [applications d’API](../app-service-api/app-service-api-apps-why-best-platform.md).

Vous allez : 

- Créer une application web dans le Service d’application Azure.
- Déployer le HTML et CSS pour elle.
- Consultez vos pages en cours d’exécution en production.
- Jour du contenu de la même façon que les [push que GIT valide](https://git-scm.com/docs/git-push).

## <a name="prerequisites"></a>Conditions préalables

- [Git](http://www.git-scm.com/downloads).
- [CLI azure](../xplat-cli-install.md).
- Un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer vos avantages d’abonné de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Vous pouvez [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751) sans un compte Azure. Créer une application de démarrage et jouer avec lui pour jusqu'à une heure--aucune carte de crédit requise, aucun engagement.

## <a name="deploy-a-simple-html-site"></a>Déployer un site HTML simple

1. Ouvrez une nouvelle invite de commandes Windows, une fenêtre PowerShell, shell Linux ou OS X terminal. Exécutez `git --version` et `azure --version` pour vérifier que le Git et Azure CLI sont installés sur votre ordinateur.

    ![Tester l’installation d’outils CLI de votre première application web dans Azure](./media/app-service-web-get-started/1-test-tools.png)

    Si vous n’avez pas installé les outils, consultez [conditions préalables](#Prerequisites) pour les liens de téléchargement.

3. Ouvrez une session Azure comme suit :

        azure login

    Suivez le message d’aide pour continuer le processus de connexion.

    ![Ouvrez une session Azure pour créer votre première application web](./media/app-service-web-get-started/3-azure-login.png)

4. Modifier Azure CLI en mode d’ASM, puis définir l’utilisateur de déploiement pour l’application de Service. Vous allez déployer le code à l’aide des informations d’identification plus tard.

        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

1. Passer à un répertoire de travail (`CD`) et de la cloner l’exemple d’application comme suit :

        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

2. Modifier le référentiel de l’exemple d’application. 

        cd app-service-web-html-get-started

4. Créez la ressource de l’application de Service de l’application dans Azure avec un nom unique de l’application et l’utilisateur de déploiement que vous avez configurée précédemment. Lorsque vous y êtes invité, spécifiez le numéro de la région de votre choix.

        azure site create <app_name> --git --gitusername <username>

    ![Créez la ressource Azure pour votre première application web Azure](./media/app-service-web-get-started/4-create-site.png)

    Votre application est maintenant créée dans Azure. En outre, votre répertoire actif est Git initialisé et connecté à la nouvelle application de Service de l’application comme un Git à distance.
    Vous pouvez accéder à l’URL de l’application (http://&lt;nom_app >. azurewebsites.net) pour voir la page HTML par défaut beau, mais en réalité passons votre code maintenant.

4. Déployer l’exemple de code à votre application Azure comme vous pousse à n’importe quel code avec Git. Lorsque vous y êtes invité, utilisez le mot de passe que vous avez configurée précédemment.

        git push azure master

    ![Code de commande à votre première application web dans Azure](./media/app-service-web-get-started/5-push-code.png)

    Si vous avez utilisé une des structures de langage, vous verrez une sortie différente. C’est parce que `git push` non seulement injecte du code dans Azure, mais déclenche également les tâches de déploiement dans le moteur de déploiement. Si vous avez des package.json (Node.js) ou requirements.txt (Python) les fichiers dans la racine de votre projet (référentiel), ou si vous avez un fichier packages.config dans votre projet ASP.NET, le script de déploiement restaure les packages requis pour vous. Vous pouvez également [Activer l’extension de Composer](web-sites-php-mysql-deploy-use-git.md#composer) pour traiter automatiquement les fichiers composer.json dans votre application PHP.

Félicitations, vous avez déployé votre application au Service d’application Azure.

## <a name="see-your-app-running-live"></a>Consultez votre application en direct.

Pour visualiser votre application en cours d’exécution dans Azure, exécutez cette commande à partir de n’importe quel répertoire de votre référentiel :

    azure site browse

## <a name="make-updates-to-your-app"></a>Mettre à jour votre application

Vous pouvez désormais utiliser Git pour pousser à partir de la racine de votre projet (référentiel) à tout moment pour effectuer une mise à jour sur le site réel. Vous faire la même façon que lorsque vous avez déployé votre code la première fois. Par exemple, chaque fois que vous souhaitez distribuer une nouvelle modification que vous avez testé localement, exécutez les commandes suivantes à partir de la racine de votre projet (référentiel) :

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Étapes suivantes

Rechercher les opérations de développement et de déploiement par défaut pour votre structure de langage :

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [PHP](app-service-web-php-get-started.md)
- [Node.js](app-service-web-nodejs-get-started.md)
- [Python](web-sites-python-ptvs-django-mysql.md)
- [Java](web-sites-java-get-started.md)

Ou bien, faire plus avec votre première application web. Par exemple :

- Essayez [d’autres méthodes de déploiement de votre code vers Azure](../app-service-web/web-sites-deploy.md). Par exemple, pour déployer à partir d’un de vos référentiels GitHub, sélectionnez simplement **GitHub** au lieu de **Référentiel de Git Local** dans **les options de déploiement**.
- Prendre votre application Azure au niveau suivant. Authentifier les utilisateurs. Évoluer en fonction de la demande. Permet de paramétrer des alertes de performances. En quelques clics. Reportez-vous à la section [Ajouter une fonctionnalité à votre première application web](app-service-web-get-started-2.md).

