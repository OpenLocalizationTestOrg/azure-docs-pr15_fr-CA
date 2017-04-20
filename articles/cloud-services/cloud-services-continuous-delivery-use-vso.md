<properties
    pageTitle="Une livraison continue avec Visual Studio Team Services dans Azure | Microsoft Azure"
    description="Apprenez à configurer vos projets d’équipe Visual Studio Team Services pour générer et déployer la fonctionnalité d’application Web dans les services de nuage ou de Service d’application Azure automatiquement."
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Livraison continue vers Azure à l’aide de Visual Studio Team Services

Vous pouvez configurer vos projets d’équipe Visual Studio Team Services pour créer et déployer des applications web Azure ou services en nuage automatiquement.  (Pour plus d’informations sur la façon de configurer une build en continu et de déployer le système à l’aide d’un *local* de Team Foundation Server, consultez [Fourniture continue de Services en nuage dans Azure](cloud-services-dotnet-continuous-delivery.md).)

Ce didacticiel suppose que vous avez Visual Studio 2013 et installé le kit SDK Azure. Si vous ne disposez pas de Visual Studio 2013, le télécharger en cliquant sur le lien **commencer gratuitement** à [www.visualstudio.com](http://www.visualstudio.com). Installez le Kit de développement Azure à partir [d’ici](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Vous avez besoin d’un compte de Services d’équipe Visual Studio pour compléter ce didacticiel : vous pouvez [Ouvrir un compte de Visual Studio Team Services gratuitement](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Pour configurer un service en nuage à générer et à déployer vers Azure à l’aide de Visual Studio Team Services automatiquement, procédez comme suit.

## <a name="1-create-a-team-project"></a>1 : créer un projet d’équipe

Suivez les instructions fournies [ici](http://go.microsoft.com/fwlink/?LinkId=512980) pour créer votre projet d’équipe et le lier à Visual Studio. Cette procédure pas à pas suppose que vous utilisez Team Foundation Version contrôle (TFVC) en tant que votre solution de contrôle de code source. Si vous souhaitez utiliser Git pour le contrôle de version, consultez [la version Git de cette procédure pas à pas](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2 : archiver un projet au contrôle de code source

1. Dans Visual Studio, ouvrez la solution que vous souhaitez déployer ou créer un nouveau.
Vous pouvez déployer une application web ou un service en nuage (Application Azure) en suivant les étapes décrites dans cette procédure pas à pas.
Si vous souhaitez créer une nouvelle solution, créez un nouveau projet Azure Cloud Service, ou un nouveau projet ASP.NET MVC. Assurez-vous que le projet cible.NET Framework 4 ou 4.5 et si vous créez un projet de service cloud, ajouter un rôle de web ASP.NET MVC et un rôle worker et sélectionnez le rôle web application Internet. Lorsque vous y êtes invité, cliquez sur **Applications Internet**.
Si vous souhaitez créer une application web, choisissez le modèle de projet d’Application Web de ASP.NET, puis MVC. Voir [Création d’une application web ASP.NET dans le Service d’application Azure](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Visual Studio Team Services prennent uniquement en charge les déploiements CI des Applications de Web de Visual Studio pour l’instant. Les projets de Site Web sont hors de portée.

1. Ouvrir le menu contextuel de la solution et cliquez sur **Ajouter la Solution au contrôle de code Source**.

    ![][5]

1. Accepter ou modifier les valeurs par défaut et cliquez sur le bouton **OK** . Une fois le processus terminé, les icônes de contrôle de code source s’affichent dans **L’Explorateur de solutions**.

    ![][6]

1. Ouvrir le menu contextuel de la solution, puis choisissez **Archiver**.

    ![][7]

1. Dans la zone de **Modifications en attente** de **Team Explorer**, tapez un commentaire pour l’archivage et cliquez sur le bouton **Archiver** .

    ![][8]

    Notez les options à inclure ou à exclure lorsque vous archivez des modifications spécifiques. Si les modifications sont exclues, cliquez sur le lien **d’Inclure tous les** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3 : connectez le projet vers Azure

1. Maintenant que vous disposez d’un projet d’équipe VS Team Services avec du code source, vous êtes prêt à vous connecter votre projet d’équipe vers Azure.  Dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), sélectionnez votre application web ou de service de cloud, ou créez-en un nouveau en cliquant sur les **+** icône en bas à gauche et en choisissant **Service Cloud** ou **Web App** , puis **Création rapide**. Cliquez sur le lien **configurer la publication avec Visual Studio Team Services** .

    ![][10]

1. Dans l’Assistant, tapez le nom de votre compte de Visual Studio Team Services dans la zone de texte et cliquez sur le lien **Autoriser maintenant** . Vous pouvez être invité à vous connecter.

    ![][11]

1. Dans le menu contextuel de **La demande de connexion** , cliquez sur le bouton **Accepter** pour autoriser Azure pour configurer votre projet d’équipe dans VS Team Services.

    ![][12]

1. Lorsque l’autorisation réussit, vous voyez une liste déroulante contenant une liste de vos projets d’équipe Visual Studio Team Services. Choisissez le nom du projet d’équipe que vous avez créé dans les étapes précédentes, puis choisissez le bouton de l’Assistant.

    ![][13]

1. Une fois que votre projet est lié, vous verrez certaines instructions pour archiver les modifications apportées à votre projet d’équipe Visual Studio Team Services.  Sur votre prochain archivage, Visual Studio Team Services va générer et déployer votre projet dans Azure.  Essayez cela maintenant en cliquant sur le lien **Check In à partir de Visual Studio** et puis le lien de **Lancement de Visual Studio** (ou l’équivalent **Visual Studio** bouton au bas de l’écran du portail).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4 : déclencher une reconstruction et redéployer votre projet

1. Dans de Visual Studio **Team Explorer**, cliquez sur le lien de **l’Explorateur du contrôle de code Source** .

    ![][15]

1. Accédez au fichier solution et ouvrez-le.

    ![][16]

1. Dans l' **Explorateur de solutions**, ouvrez un fichier et le modifier. Par exemple, modifiez le fichier `_Layout.cshtml` dans les vues\\dossier partagé dans un rôle de web MVC.

    ![][17]

1. Modifier le logo pour le site et cliquez sur **Ctrl + S** pour enregistrer le fichier.

    ![][18]

1. Dans **Team Explorer**, cliquez sur le lien **Modifications en attente** .

    ![][19]

1. Permet d’entrer un commentaire et cliquez sur le bouton **Archiver** .

    ![][20]

1. Cliquez sur le bouton **accueil** pour retourner à la page d’accueil **De Team Explorer** .

    ![][21]

1. Cliquez sur le lien **versions** pour afficher les builds en cours.

    ![][22]

    **Team Explorer** montre qu’une génération a été déclenchée pour votre archivage.

    ![][23]

1. Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé que la génération progresse.

    ![][24]

1. Pendant une génération en cours, examinons la définition de build qui a été créée lorsque vous avez lié TFS pour Azure à l’aide de l’Assistant.  Ouvrir le menu contextuel pour la définition de build et choisissez **Modifier la définition de Build**.

    ![][25]

    Dans l’onglet **déclencheur** , vous verrez que la définition de build est configurée pour générer à chaque archivage par défaut.

    ![][26]

    Dans l’onglet **processus** , vous pouvez voir que l’environnement de déploiement est définie sur le nom de votre application web ou de service de cloud. Si vous travaillez avec des applications web, les propriétés que vous consultez seront différentes de ceux illustrés ci-après.

    ![][27]

1. Spécifier des valeurs pour les propriétés si vous souhaitez que les valeurs différentes de celles par défaut. Les propriétés de publication Azure se trouvent dans la section **déploiement** .

    Le tableau suivant affiche les propriétés disponibles dans la section **déploiement** :

  	|Propriété|Valeur par défaut|
  	|---|---|
  	|Autoriser les certificats non approuvés|Si la valeur est false, les certificats SSL doivent être signés par une autorité racine.|
  	|Autoriser la mise à niveau|Permet le déploiement de la mise à jour d’un déploiement existant au lieu de créer un nouveau. Conserve l’adresse IP.|
  	|Ne pas supprimer|Si la valeur est true, ne pas remplacer un déploiement sans relation existant (mise à jour est autorisée).|
  	|Chemin d’accès aux paramètres de déploiement|Le chemin d’accès à votre fichier de .pubxml pour une application web, par rapport au dossier racine de la mis en pension. Ignoré pour les services en nuage.|
  	|Environnement de déploiement SharePoint|Le même que le nom du service.|
  	|Environnement de déploiement d’Azure|Le nom du service web app ou nuage.|

1. Si vous utilisez plusieurs configurations de service (fichiers .cscfg), vous pouvez spécifier la configuration du service souhaité dans le paramètre de **génération, Avancé, arguments MSBuild** . Par exemple, pour utiliser ServiceConfiguration.Test.cscfg, définissez les arguments MSBuild option de ligne de `/p:TargetProfile=Test`.

    ![][38]

    À ce stade, votre build doit être terminée.

    ![][28]

1. Si vous double-cliquez sur le nom de build, Visual Studio affiche un **Résumé de la Build**, y compris les résultats des tests à partir de projets de test unitaire associés.

    ![][29]

1. Dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez afficher le déploiement associé sous l’onglet **déploiements** lorsque l’environnement intermédiaire est sélectionnée.

    ![][30]

1.  Accédez à l’URL de votre site. Pour une application web, cliquez sur le bouton **Parcourir** de la barre de commandes. Pour un service en nuage, cliquez sur l’URL dans la section **Aperçu rapide** de la page de **tableau de bord** qui affiche l’environnement mis en œuvre pour un service en nuage. Déploiements d’intégration continue des services en nuage sont publiés à l’environnement de test par défaut. Vous pouvez modifier cela en affectant à la propriété **d’Environnement de Service Cloud autre** **Production**. Cette capture d’écran indique où l’URL du site sur la page de tableau de bord du service nuage.

    ![][31]

    Un nouvel onglet de navigateur s’ouvre pour afficher votre site en cours d’exécution.

    ![][32]

    Pour les services en nuage, si vous apportez d’autres modifications à votre projet, vous déclencheur génère plus et vous accumulerez plusieurs déploiements. Tout derniers marquée comme Active.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5 : redéployer une version antérieure

Cette étape s’applique aux services de cloud et est facultative. Dans le portail Azure classique, choisissez un précédent déploiement, puis le bouton **redéployer** votre site à un archivage antérieure de rembobiner.  Notez que cela déclenche une nouvelle build dans TFS et créer une nouvelle entrée dans l’historique de votre déploiement.

![][34]

## <a name="6-change-the-production-deployment"></a>6 : modifier le déploiement en Production

Cette étape s’applique uniquement aux services en nuage, pas des applications web. Lorsque vous êtes prêt, vous pouvez promouvoir l’environnement mis en œuvre dans l’environnement de production en cliquant sur le bouton **Permuter** dans Azure portal classique. L’environnement de mise en œuvre récemment déployé est promu à la Production et l’environnement de Production précédente, le cas échéant, devient un environnement de test. Le déploiement Active peut être différent de la Production et de mise en attente des environnements, mais des builds récentes, l’historique de déploiement est le même, quel que soit l’environnement.

![][35]

## <a name="7-run-unit-tests"></a>7 : exécuter des tests unitaires

Cette étape s’applique uniquement aux applications web, services en nuage pas. Pour placer une porte de qualité dans votre déploiement, vous pouvez exécuter des tests unitaires et si elles échouent, vous pouvez arrêter le déploiement.

1.  Dans Visual Studio, ajoutez un projet de test unitaire.

    ![][39]

1.  Ajouter des références de projet pour le projet que vous souhaitez tester.

    ![][40]

1.  Ajouter quelques tests unitaires. Pour commencer, essayez un essai à blanc qui passera toujours.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Modifier la définition de build, cliquez sur l’onglet **processus** et développez le nœud de **Test** .

1.  Le **faire échouer la build en cas d’échec du test** de la valeur True. Cela signifie que le déploiement n’aura pas lieu, sauf si les tests ont réussi.

    ![][41]

1.  File d’attente une nouvelle build.

    ![][42]

    ![][43]

1. Bien que la génération se poursuit, vérifiez sa progression.

    ![][44]

    ![][45]

1. Lorsque la génération est terminée, vérifiez les résultats des tests.

    ![][46]

    ![][47]

1.  Essayez de créer un test qui échoue. Ajouter un nouveau test en copiant le premier d'entre eux, renommez-le et commentez la ligne de code que NotImplementedException est une exception attendue.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Archiver la modification à la file d’attente une nouvelle build.

    ![][48]

1. Permet d’afficher les résultats du test pour plus de détails sur l’échec.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le test unitaire dans Visual Studio Team Services, consultez [exécution de tests unitaires dans votre build](http://go.microsoft.com/fwlink/p/?LinkId=510474). Si vous utilisez Git, consultez [partage de votre code dans le Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) et [déploiement continue à un Service d’application Azure](../app-service-web/app-service-continuous-deployment.md).  Pour plus d’informations sur Visual Studio Team Services, consultez [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
