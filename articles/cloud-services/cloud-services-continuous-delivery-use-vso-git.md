<properties
    pageTitle="Une livraison continue avec Git et Visual Studio Team Services dans Azure | Microsoft Azure"
    description="Apprenez à configurer vos projets d’équipe Visual Studio Team Services pour Git permet de générer et de déployer à la fonctionnalité de l’application Web dans les services de nuage ou de Service d’application Azure automatiquement."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Livraison continue vers Azure à l’aide de Visual Studio Team Services et Git

Vous pouvez utiliser des projets d’équipe Visual Studio Team Services pour héberger un référentiel Git pour votre code source et automatiquement générer et déployer des applications web Azure ou services en nuage à chaque fois que vous poussez un commit dans le référentiel.

Vous aurez besoin de Visual Studio 2013 et installé le kit SDK Azure. Si vous ne disposez pas de Visual Studio 2013, le télécharger en cliquant sur le lien **commencer gratuitement** à [www.visualstudio.com](http://www.visualstudio.com). Installez le Kit de développement Azure à partir [d’ici](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Vous avez besoin d’un compte de Services d’équipe Visual Studio pour compléter ce didacticiel : vous pouvez [Ouvrir un compte de Visual Studio Team Services gratuitement](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Pour configurer un service en nuage à générer et à déployer vers Azure à l’aide de Visual Studio Team Services automatiquement, procédez comme suit.

## <a name="1-create-a-git-repository"></a>1 : créer un référentiel Git

1. Si vous ne disposez pas d’un compte de Visual Studio Team Services, vous pouvez en obtenir un [ici](http://go.microsoft.com/fwlink/?LinkId=397665). Lorsque vous créez votre projet d’équipe, choisissez Git comme votre système de contrôle de code source. Suivez les instructions pour connecter Visual Studio à votre projet d’équipe.

2. Dans **Team Explorer**, cliquez sur le lien de **cloner ce référentiel** .

    ![][3]

3. Spécifier l’emplacement de la copie locale, puis choisissez le bouton **Dupliquer** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2 : créer un projet et à valider dans le référentiel

1. Dans **Team Explorer**, dans la section **Solutions** , sélectionnez le lien de **Nouveau** pour créer un nouveau projet dans le référentiel local.

    ![][4]

2. Vous pouvez déployer une application web ou un service en nuage (Application Azure) en suivant les étapes décrites dans cette procédure pas à pas. Créer un nouveau projet Azure Cloud Service, ou un nouveau projet ASP.NET MVC. Assurez-vous que le projet cible.NET Framework 4 ou ultérieure. Si vous créez un projet de service cloud, ajouter un rôle de web ASP.NET MVC et un rôle worker.
Si vous souhaitez créer une application web, choisissez le modèle de projet **d’Application Web de ASP.NET** , puis **MVC**. Pour plus d’informations, voir [Création d’une application web ASP.NET dans le Service d’application Azure](../app-service-web/web-sites-dotnet-get-started.md) .

3. Ouvrir le menu contextuel de la solution et cliquez sur **Valider**.

    ![][7]

4. Si c’est la première fois que vous avez utilisé le Git dans Visual Studio Team Services, vous devrez fournir des informations pour vous identifier dans le Git. Dans la zone de **Modifications en attente** de **Team Explorer**, entrez votre nom d’utilisateur et votre adresse électronique. Entrez un commentaire pour la validation, puis choisissez le bouton de **validation** .

    ![][8]

5. Notez les options à inclure ou à exclure lorsque vous archivez des modifications spécifiques. Si les modifications que vous souhaitez sont exclues, cliquez sur **Inclure tous les**.

6. Vous avez maintenant soumis les modifications dans votre copie locale du référentiel. Ensuite, synchroniser ces modifications avec le serveur en cliquant sur le lien de **synchronisation** .

## <a name="3-connect-the-project-to-azure"></a>3 : connectez le projet vers Azure

1. Maintenant que vous disposez d’un référentiel Git dans Visual Studio Team Services avec du code source qu’il contient, vous êtes prêt à vous connecter votre référentiel git sur Azure.  Dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), sélectionnez votre application web ou de service de cloud, ou créez-en un nouveau en cliquant sur l’icône en bas à gauche et en choisissant **Service Cloud** ou **Web App** , puis **Création rapide**+.

    ![][9]

3. Pour les services en nuage, cliquez sur le lien **configurer la publication avec Visual Studio Team Services** . Pour les applications web, cliquez sur le lien **configuration de déploiement à partir du contrôle de code source** .

    ![][10]

2. Dans l’Assistant, tapez le nom de votre compte de Visual Studio Team Services dans la zone de texte et cliquez sur le lien **Autoriser maintenant** . Vous pouvez être invité à vous connecter.

    ![][11]

3. Dans le menu contextuel de **La demande de connexion** , cliquez sur **Accepter** pour autoriser Azure pour configurer votre projet d’équipe dans Visual Studio Team Services.

    ![][12]

4. Après que l’autorisation a réussi, vous voyez une liste déroulante qui contient vos projets d’équipe Visual Studio Team Services.  Sélectionnez le nom du projet d’équipe que vous avez créé dans les étapes précédentes et choisissez le bouton de l’Assistant.

    ![][13]

    La prochaine fois que vous poussez un commit pour votre référentiel, Visual Studio Team Services générer et déployer votre projet dans Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4 : déclencher une reconstruction et redéployer votre projet

1. Dans Visual Studio, ouvrez un fichier et le modifier. Par exemple, modifiez le fichier `_Layout.cshtml` dans les vues\\dossier partagé dans un rôle de web MVC.

    ![][17]

2. Modifier le texte de pied de page pour le site et enregistrez le fichier.

    ![][18]

3. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le nœud de solution, le nœud de projet ou le fichier que vous avez modifié et puis cliquez sur **Valider**.

4. Tapez un commentaire et cliquez sur **Valider**.

    ![][20]

5. Cliquez sur le lien de **synchronisation** .

    ![][38]

6. Cliquez sur le lien **envoyé** à pousser votre validation au référentiel dans Visual Studio Team Services. (Vous pouvez également utiliser le bouton de **synchronisation** pour copier vos engagements dans le référentiel. La différence est que **la synchronisation** extrait également les dernières modifications du référentiel).

    ![][39]

7. Cliquez sur le bouton **accueil** pour retourner à la page d’accueil **De Team Explorer** .

    ![][21]

8. Choisissez **les Builds** pour consulter les builds en cours.

    ![][22]

    **Team Explorer** montre qu’une génération a été déclenchée pour votre archivage.

    ![][23]

9. Pour afficher un journal détaillé que la génération progresse, double-cliquez sur le nom de la build en cours.

10. Pendant une génération en cours, examinons la définition de build qui a été créée lorsque vous avez utilisé l’Assistant pour créer un lien vers Azure.  Ouvrir le menu contextuel pour la définition de build et choisissez **Modifier la définition de Build**.

    ![][25]

11. Dans l’onglet **déclencheur** , vous verrez que la définition de build est configurée pour générer à chaque archivage, par défaut. (Pour un service en nuage, Visual Studio Team Services génère et déploie la branche principale à l’environnement intermédiaire automatiquement. Vous devrez effectuer une étape manuelle à déployer sur le site réel. Pour une application web qui n’a pas environnement intermédiaire, elle déploie la branche principale directement sur le site réel.

    ![][26]

1. Dans l’onglet **processus** , vous pouvez voir que l’environnement de déploiement est définie sur le nom de votre application web ou de service de cloud.

    ![][27]

1. Spécifier des valeurs pour les propriétés si vous souhaitez que les valeurs différentes de celles par défaut. Les propriétés de publication Azure se trouvent dans la section **déploiement** et vous devrez peut-être également définir des paramètres de MSBuild. Par exemple, dans un nuage service projet, pour spécifier une configuration de service « Nuage », définissez les paramètres de MSbuild `/p:TargetProfile=[YourProfile]` où *[YourProfile]* correspond à un fichier de configuration de service avec un nom tel que ServiceConfiguration. .Cscfg de *YourProfile*.

    Le tableau suivant affiche les propriétés disponibles dans la section **déploiement** :

  	|Propriété|Valeur par défaut|
  	|---|---|
  	|Autoriser les certificats non approuvés|Si la valeur est false, les certificats SSL doivent être signés par une autorité racine.|
  	|Autoriser la mise à niveau|Permet le déploiement de la mise à jour d’un déploiement existant au lieu de créer un nouveau. Conserve l’adresse IP.|
  	|Ne pas supprimer|Si la valeur est true, ne pas remplacer un déploiement sans relation existant (mise à jour est autorisée).|
  	|Chemin d’accès aux paramètres de déploiement|Le chemin d’accès à votre fichier de .pubxml pour une application web, par rapport au dossier racine de la mis en pension. Ignoré pour les services en nuage.|
  	|Environnement de déploiement SharePoint|Le même que le nom du service.|
  	|Environnement de déploiement d’Azure|Le nom du service web app ou nuage.|

1. À ce stade, votre build doit être terminée.

    ![][28]

1. Si vous double-cliquez sur le nom de build, Visual Studio affiche un **Résumé de la Build**, y compris les résultats des tests à partir de projets de test unitaire associés.

    ![][29]

1. Dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez afficher le déploiement associé sous l’onglet **déploiements** lorsque l’environnement intermédiaire est sélectionnée.

    ![][30]

1.  Accédez à l’URL de votre site. Pour une application web, vous devez simplement sur le bouton **Parcourir** dans le portail. Pour un service en nuage, cliquez sur l’URL dans la section **Aperçu rapide** de la page de **tableau de bord** qui affiche l’environnement intermédiaire.

    Déploiements d’intégration continue des services en nuage sont publiés à l’environnement de test par défaut. Vous pouvez modifier cela en affectant à la propriété **d’Environnement de Service Cloud autre** **Production**. Voici où l’URL du site est sur la page de tableau de bord du service nuage.

    ![][31]

    Un nouvel onglet de navigateur s’ouvre pour afficher votre site en cours d’exécution.

    ![][32]

1.  Si vous apportez d’autres modifications à votre projet, vous déclencheur génère plus et vous accumulerez plusieurs déploiements. Tout derniers est marquée comme Active.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5 : redéployer une version antérieure

Cette étape est facultative. Dans Azure portal classique, choisissez un précédent déploiement et choisissez le **redéployer** pour le retour rapide de votre site à un archivage antérieure. Notez que cela déclenche une nouvelle build dans TFS et créer une nouvelle entrée dans l’historique de votre déploiement.

![][34]

## <a name="6-change-the-production-deployment"></a>6 : modifier le déploiement en Production

Lorsque vous êtes prêt, vous pouvez promouvoir l’environnement mis en œuvre dans l’environnement de Production en sélectionnant **Permuter** dans Azure portal classique. L’environnement de mise en œuvre récemment déployé est promu à la Production et l’environnement de Production précédente, le cas échéant, devient un environnement de test. Le déploiement Active peut être différent de la Production et de mise en attente des environnements, mais des builds récentes, l’historique de déploiement est le même, quel que soit l’environnement.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7 : déployer à partir d’une branche de travail.

Lorsque vous utilisez Git, vous généralement apportez des modifications d’une branche de travail et s’intégrer dans la branche principale lorsque votre développement atteint un état de produits finis. Au cours de la phase de développement d’un projet, vous souhaiterez générer et déployer la branche de travail pour Azure.

1. Dans **Team Explorer**, cliquez sur le bouton **accueil** et cliquez sur le bouton de **Branches** .

    ![][40]

2. Cliquez sur le lien de la **Nouvelle branche** .

    ![][41]

3. Entrez le nom de la succursale, telles que le « travail » et choisissez **Créer une branche**. Cela crée une nouvelle branche locale.

    ![][42]

4. Publier la branche. Cliquez sur le nom de la branche dans **des branches non publiés**et choisissez **Publier**.

    ![][44]

6. Par défaut, seules les modifications à la branche principale déclenchent une build en continu. Pour configurer une build en continu pour une branche de travail, choisissez la page de **Builds** dans **Team Explorer**et choisissez **Modifier la définition de Build**.

7. Ouvrez l’onglet **Paramètres de la Source** . Sous **surveillés crée des branches pour l’intégration continue et de build**, cliquez sur **Cliquez ici pour ajouter une nouvelle ligne**.

    ![][47]

8. Spécifiez la branche que vous avez créé, par exemple Réf/têtes /.

    ![][48]

9. Apportez une modification dans le code, ouvrez le menu contextuel pour le fichier modifié et puis cliquez sur **Valider**.

    ![][43]

10. Sélectionnez le lien **Non synchronisées des validations** et cliquez sur le bouton de **synchronisation** ou le lien **Push** pour copier les modifications à la copie de la branche de travail dans Visual Studio Team Services.

    ![][45]

11. Accédez à la vue **génère** et trouver la génération juste l’a été déclenchée pour la branche de travail.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus de conseils sur l’utilisation de Git avec Visual Studio Team Services, consultez [développer et partager votre code dans le Git à l’aide de Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) et pour plus d’informations sur l’utilisation d’un référentiel Git qui n’est pas géré par Visual Studio Team Services pour publier sur Azure, consultez [Déploiement continue à un Service d’application Azure](../app-service-web/app-service-continuous-deployment.md). Pour plus d’informations sur Visual Studio Team Services, consultez [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
