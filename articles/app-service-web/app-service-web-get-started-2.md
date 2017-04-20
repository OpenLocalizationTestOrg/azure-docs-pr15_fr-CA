<properties
    pageTitle="Ajouter des fonctionnalités à votre première application web"
    description="Ajouter des fonctionnalités intéressantes pour votre première application web en quelques minutes."
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
    ms.date="05/12/2016"
    ms.author="cephalin"
/>

# <a name="add-functionality-to-your-first-web-app"></a>Ajouter des fonctionnalités à votre première application web

Dans le [déploiement de votre première application web vers Azure dans les cinq minutes](app-service-web-get-started.md), vous avez déployé un exemple d’application web à un [Service d’application Azure](../app-service/app-service-value-prop-what-is.md). Dans cet article, vous allez rapidement ajouter certaines des fonctionnalités à votre application web déployée. Dans quelques minutes, vous pourrez :

- appliquer l’authentification pour les utilisateurs
- mettre automatiquement à l’échelle votre application
- recevoir des alertes sur les performances de votre application

Indépendamment de quel exemple d’application vous avez déployé dans l’article précédent, vous pouvez suivre le long du didacticiel.

Les trois activités dans ce didacticiel ne sont que quelques exemples des nombreuses fonctionnalités utiles que vous obtenez lorsque vous mettez votre application web dans le Service d’application. La plupart des fonctionnalités sont disponibles dans la couche **libre** (c’est votre première application web qui s’exécute sur), et vous pouvez utiliser vos crédits d’essai pour tester des fonctionnalités qui nécessitent des niveaux de tarification est élevée. Être assuré que votre application web reste **libre** niveau, sauf si vous le modifie explicitement à un autre niveau de tarification.

>[AZURE.NOTE] L’application web que vous avez créé avec l’interface CLI d’Azure s’exécute dans la couche **libre** , qui autorise uniquement une seule instance de machine virtuelle partagée avec des quotas de ressource. Pour plus d’informations sur ce que vous obtenez avec couche de **libre** , consultez [limites de Service de l’application](../azure-subscription-service-limits.md#app-service-limits).

## <a name="authenticate-your-users"></a>Authentifier les utilisateurs

Maintenant, nous allons voir combien il est facile pour ajouter l’authentification pour votre application (plus d’informations au niveau de [l’Application de Service d’authentification/autorisation](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)).

1. Dans la portail lame pour votre application, que vous venez d’ouvrir, cliquez sur **paramètres** > **l’authentification / autorisation**.  
    ![Authentification - lame de paramètres](./media/app-service-web-get-started/aad-login-settings.png)

2. Cliquez **sur** activer l’authentification.  

4. **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.  
    ![Authentification - sélectionnez AD Azure](./media/app-service-web-get-started/aad-login-config.png)

5. De la lame **Azure les paramètres Active Directory** , cliquez sur **Express**, puis cliquez sur **OK**. Les paramètres par défaut créent une nouvelle application Azure AD dans votre répertoire par défaut.  
 ![Authentification - configuration express](./media/app-service-web-get-started/aad-login-express.png)

6. Cliquez sur **Enregistrer**.  
    ![Authentification - enregistrer la configuration](./media/app-service-web-get-started/aad-login-save.png)

    Une fois que la modification est réussie, vous verrez la cloche notification vert, ainsi qu’un message convivial.

7. Dans la portail lame de votre application, cliquez sur le lien **URL** (ou **Rechercher** dans la barre de menus). Le lien est une adresse HTTP.  
    ![Authentification - accédez à l’URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Mais une fois qu’il ouvre l’application dans un nouvel onglet, l’URL de zone redirections plusieurs fois et se termine sur votre application avec une adresse HTTPS. Ce que vous voyez est que vous êtes déjà connecté à votre abonnement Azure, et vous êtes automatiquement authentifié dans l’application.  
    ![Authentification - connecté](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Par conséquent, si vous ouvrez maintenant une session non authentifiée dans un autre navigateur, vous verrez un écran de connexion lorsque vous accédez à la même URL.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
   Si vous n’avez jamais fait quoi que ce soit avec Azure Active Directory, le répertoire par défaut peut-être pas tous les utilisateurs AD Azure. Dans ce cas, le seul compte de cet emplacement est probablement le compte Microsoft avec votre abonnement Azure. C’est pourquoi vous ont été automatiquement connecté à l’application dans le navigateur même plus haut.
   Vous pouvez utiliser ce même compte Microsoft pour ouvrir une session cette page de connexion.

Félicitations, vous authentifiez tout le trafic vers votre application web.

Vous avez peut-être remarqué dans le **l’authentification / autorisation** lame que vous pouvez faire beaucoup plus d’informations, telles que :

- Activer la connexion sociale
- Activer plusieurs options de connexion
- Modifier le comportement par défaut lorsque les utilisateurs d’abord accéder à votre application

Service de l’application fournit qu'une solution clé en main pour une partie de l’authentification commune doit donc vous n’avez pas besoin de fournir la logique d’authentification vous-même.
Pour plus d’informations, consultez [Application Service d’authentification/autorisation](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

## <a name="scale-your-app-automatically-based-on-demand"></a>Mettre à l’échelle votre application automatiquement en fonction de la demande

Ensuite, nous allons autoscale votre application afin qu’il ajuste automatiquement il capacité à répondre à la demande de l’utilisateur (plus d’informations à [l’échelle de votre application dans Azure](web-sites-scale.md) et le [nombre d’instances à l’échelle manuellement ou automatiquement](../monitoring-and-diagnostics/insights-how-to-scale.md)).

En bref, vous mettre à l’échelle votre application web de deux façons :

- [Évolution verticale](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenir plus du processeur, mémoire, espace disque et des fonctionnalités supplémentaires telles que les ordinateurs virtuels dédiés, les domaines personnalisés et les certificats, emplacements, autoscaling et plus la zone de transit. Évoluer en modifiant le niveau de tarification du plan de Service d’application qu'auquel appartient votre application.
- [Mise à l’échelle](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): augmentation du nombre de machines virtuelles les instances qui exécute votre application.
Vous pouvez faire évoluer jusqu'à 50 instances, en fonction de votre niveau de tarification.

Sans plus de cérémonie, nous allons définir autoscaling.

1. Tout d’abord, nous allons évoluer pour activer autoscaling. Dans la portail lame de votre application, cliquez sur **paramètres** > **Échelle des (Plan de Service de l’application)**.  
    ![Évoluer - lame de paramètres](./media/app-service-web-get-started/scale-up-settings.png)

2. Faites défiler et sélectionnez le niveau **%s1 Standard** , le niveau le plus bas qui prend en charge les autoscaling (encerclé dans la capture d’écran), puis cliquez sur **Sélectionner**.  
    ![Évoluer - choix d’un niveau](./media/app-service-web-get-started/scale-up-select.png)

    Vous avez terminé mise à l’échelle des.

    >[AZURE.IMPORTANT] Cette couche consacrés vos crédits d’évaluation gratuits. Si vous avez un compte de paie par utilisation, il entraîne des frais pour votre compte.

3. Ensuite, nous allons configurer autoscaling. Dans la portail lame de votre application, cliquez sur **paramètres** > **L’élargissement (Plan de Service de l’application)**.  
    ![Mise à l’échelle - lame de paramètres](./media/app-service-web-get-started/scale-out-settings.png)

4. Modifier **l’échelle par** pourcentage de **l’UC**. Les curseurs en dessous de la liste déroulante mise à jour en conséquence. Ensuite, définissez une plage comprise entre **1** et **2** et une **plage cible** entre **40** et **80** **d’Instances** . Faites-le en tapant dans les zones ou en déplaçant les curseurs.  
 ![Évoluer - configurer autoscaling](./media/app-service-web-get-started/scale-out-configure.png)

    Selon cette configuration, votre application automatiquement peut évoluer lors de l’utilisation du processeur est supérieure à 80 % et met à l’échelle dans lors de l’utilisation de l’UC est inférieure à 40 %.

5. Dans la barre de menus, cliquez sur **Enregistrer** .

Félicitations, votre application est autoscaling.

Vous avez peut-être remarqué dans la lame de **Paramètres d’échelle** que vous pouvez faire beaucoup plus d’informations, telles que :

- Échelle manuellement à un certain nombre d’instances
- Mettre à l’échelle par d’autres mesures de performances, tels que de la file d’attente du disque ou de pourcentage de mémoire
- Personnaliser le comportement de mise à l’échelle lors du déclenche d’une règle de performance
- Échelle automatique d’une planification
- Définir le comportement autoscaling pour un événement futur

Pour plus d’informations sur l’évolution verticale de votre application, consultez [évoluer votre application dans Azure](../app-service-web/web-sites-scale.md). Pour plus d’informations sur la mise à l’échelle, consultez le [nombre d’instances à l’échelle manuellement ou automatiquement](../monitoring-and-diagnostics/insights-how-to-scale.md).

## <a name="receive-alerts-for-your-app"></a>Recevoir des alertes pour votre application

Maintenant que votre application est autoscaling, que se passe-t-il lorsqu’il atteint le nombre maximal d’instance (2) et le processeur est supérieur à l’utilisation souhaitée (80 %) ?
Vous pouvez configurer une alerte (plus d’informations à [recevoir des notifications alertes](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)) pour vous informer de cette situation afin d’autres adapter à distance/hors de votre application, par exemple. Nous allons rapidement définir une alerte pour ce scénario.

1. Dans la portail lame de votre application, cliquez sur **Outils** > **alertes**.  
    ![Alertes - lame de paramètres](./media/app-service-web-get-started/alert-settings.png)

2. Cliquez sur **Ajouter une alerte**. Puis, dans la zone de la **ressource** , sélectionnez la ressource qui se termine par **(serverfarms)**. Qui est votre plan de Service de l’application.  
    ![Alertes - Ajout d’alerte pour le plan de Service d’application](./media/app-service-web-get-started/alert-add.png)

3. Spécifiez le **nom** comme `CPU Maxed`, la **mesure** en tant que **Pourcentage de l’UC**et le **seuil** en tant que `90`, puis sélectionnez **lecteurs, collaborateurs et les propriétaires de l’E-mail**, puis cliquez sur **OK**.   
 ![Configurer les alertes - alerte](./media/app-service-web-get-started/alert-configure.png)

    Après la création de l’alerte par Azure, vous le verrez dans la blade **d’alertes** .  
    ![Alertes - affichage terminé](./media/app-service-web-get-started/alert-done.png)

Félicitations, vous obtenez maintenant alertes.

Ce paramètre d’alerte vérifie l’utilisation de l’UC toutes les cinq minutes. Si ce nombre dépasse 90 %, vous recevrez un message d’alerte, ainsi que toute personne est autorisée. Pour afficher tous les utilisateurs est autorisé à recevoir des alertes, revenez à la blade de portail de votre application et cliquez sur le bouton **d’accès** .  
![Reportez-vous à la section qui reçoit les alertes](./media/app-service-web-get-started/alert-rbac.png)

Vous devez voir que les **administrateurs de l’abonnement** sont déjà le **propriétaire** de l’application. Ce groupe vous comprend si vous êtes l’administrateur du compte de votre abonnement Azure (par exemple, votre abonnement d’essai). Pour plus d’informations sur le contrôle d’accès basé sur rôle de Azure, consultez [Contrôle d’accès Azure Role-Based](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] Règles d’alerte est une fonction d’Azure. Pour plus d’informations, consultez [réception notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="next-steps"></a>Étapes suivantes

Sur la bonne voie pour configurer l’alerte, vous avez peut-être remarqué un ensemble rich d’outils de la lame **d’Outils** . Ici, vous pouvez résoudre les problèmes, analyse des performances, test des vulnérabilités, gérer les ressources, interagir avec la console de machine virtuelle et ajouter des extensions utiles. Nous vous invitons à cliquer sur chacun de ces outils pour découvrir les outils simples mais puissantes à vos conseils de doigt.

Découvrez comment en faire plus avec votre application déployée. Voici une liste partielle :

- [Acheter et configurer un nom de domaine personnalisé](custom-dns-web-site-buydomains-web-app.md) - acheter un domaine attrayant pour votre application web à la place de la *. azurewebsites.net domaine. Ou utilisez un domaine que vous avez déjà.
- [Configurer les environnements de reclassement](web-sites-staged-publishing.md) - déployer votre application vers une URL intermédiaire avant sa mise en production. Mise à jour de votre application web en ligne en toute confiance. Permet de paramétrer une solution DevOps complexe avec plusieurs emplacements de déploiement.
- [Configuration du déploiement continu](app-service-continuous-deployment.md) - intégrer le déploiement d’applications dans votre système de contrôle de code source. Déployer vers Azure avec chaque validation.
- [Accès aux ressources sur site](web-sites-hybrid-connection-get-started.md) - accès existant sur site de base de données ou le système CRM.
- [Sauvegarder votre application](web-sites-backup.md) - paramétrer des principaux et de restauration pour votre application web. Préparer à des défaillances inattendues et de les résoudre.
- [Activer les journaux de diagnostic](web-sites-enable-diagnostic-log.md) - lire les journaux IIS traces Azure ou de l’applications. Lire dans un flux de données, les télécharger ou les porter dans les [Perspectives d’Application](../application-insights/app-insights-overview.md) pour l’analyse de la clé en main.
- [Analyser votre application pour les vulnérabilités](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
analyser votre application web contre les menaces modernes à l’aide du service fourni par la [Sécurité de Tinfoil](https://www.tinfoilsecurity.com/).
- [Exécuter des travaux en arrière-plan](../azure-functions/functions-overview.md) - exécuter les tâches de traitement de données, reporting, etc..
- [Découvrez le fonctionnement du Service de l’application](../app-service/app-service-how-works-readme.md)
