<properties
    pageTitle="Se connecter à locale SQL Server à partir d’une application web dans le Service d’application Azure à l’aide de connexions de hybride"
    description="Créer une application web sur Microsoft Azure et le connecter à une base de données de SQL Server sur site"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="cephalin"/>

# <a name="connect-to-on-premises-sql-server-from-a-web-app-in-azure-app-service-using-hybrid-connections"></a>Se connecter à locale SQL Server à partir d’une application web dans le Service d’application Azure à l’aide de connexions de hybride

Les connexions hybride peuvent se connecter [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps aux ressources sur site qui utilisent un port TCP statique. Les ressources prises en charge incluent Microsoft SQL Server, MySQL, API de Web HTTP, Service de l’application et la plupart des Services Web personnalisés.

Dans ce didacticiel, vous allez apprendre à créer une application web de Service de l’application dans [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715), l’application web de se connecter à votre base de données de SQL Server sur site local à l’aide de la nouvelle fonctionnalité connexion d’hybrides, créer une application ASP.NET simple qui utilise la connexion hybride et déployer l’application pour l’application web de Service de l’application. L’application de site web terminé sur Azure stocke les informations d’identification de l’utilisateur dans une base de données d’abonnement locale. Aucune expérience préalable à l’aide d’Azure ou ASP.NET dans ce didacticiel.

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.
>
>La partie des applications Web de la fonctionnalité connexions de hybride est disponible uniquement dans le [Portail Azure](https://portal.azure.com). Pour créer une connexion dans les Services BizTalk, consultez [Connexions d’hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## <a name="prerequisites"></a>Conditions préalables ##

Pour terminer ce didacticiel, vous allez disposer des produits suivants. Tous sont disponibles dans les versions gratuites, vous pouvez commencer à développer pour Azure entièrement pour libre.

- **Abonnement azure** - pour un abonnement gratuit, consultez [Azure la version d’évaluation gratuite](/pricing/free-trial/).

- **Visual Studio 2013** - pour télécharger une version d’évaluation gratuite de Visual Studio 2013, reportez-vous à la section [Téléchargements de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). L’installer avant de continuer.

- **Microsoft.NET Framework 3.5 Service Pack 1** - si votre système d’exploitation est Windows 8.1, R2 de Windows Server 2012, Windows 8, Windows Server 2012, Windows 7 ou Windows Server 2008 R2, vous pouvez activer cette option dans le panneau de configuration > Programmes et fonctionnalités > activer des fonctionnalités Windows on ou off. Dans le cas contraire, vous pouvez le télécharger à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **2014 SQL Server Express avec des outils** - télécharger Microsoft SQL Server Express gratuitement à la [page de base de données de Microsoft Web Platform](http://www.microsoft.com/web/platform/database.aspx). Choisissez la version **Express** (pas LocalDB). La version **Express avec des outils** inclut SQL Server Management Studio, que vous utiliserez dans ce didacticiel.

- **Express de SQL Server Management Studio** - ce dernier est fourni avec l’édition SQL Server 2014 Express avec téléchargement des outils mentionné ci-dessus, mais si vous avez besoin de l’installer séparément, vous pouvez le télécharger et l’installer à partir de la [page de téléchargement de SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

Ce didacticiel suppose que vous disposez d’un abonnement Azure, que vous avez installé Visual Studio 2013 et que vous avez installé ou activé le.NET Framework 3.5. Ce didacticiel vous montre comment installer SQL Server 2014 Express dans une configuration qui fonctionne correctement avec la fonctionnalité connexions de hybride Azure (une instance par défaut avec un port TCP statique). Avant de commencer le didacticiel, téléchargez 2014 SQL Server Express avec des outils à partir de l’emplacement mentionné ci-dessus, si vous ne disposez pas de SQL Server installée.

### <a name="notes"></a>Notes ###
Pour utiliser une base de données SQL Server ou de SQL Server Express local avec une connexion hybride, TCP/IP doit être activé sur un port statique. Instances par défaut de SQL Server utilisent static port 1433, les instances nommées ne le font pas.

L’ordinateur sur lequel vous installez l’agent hybride Connection Manager sur site :

- Connectivité sortante vers Azure nécessaires sur :

Port|Pourquoi
---|---
80|**Requis** pour le port HTTP pour la validation de certificat et éventuellement pour la connectivité des données.
443|**Facultatif** pour la connectivité des données. Si la connectivité sortante à 443 n’est pas disponible, le port TCP 80 est utilisé.
5671 et 9352|**Recommandé** mais facultatif pour la connectivité des données. Notez que ce mode donne généralement un débit plus élevé. Si la connectivité sortante à ces ports n’est pas disponible, c’est le port TCP 443 qui est utilisé.
- Doit être en mesure d’atteindre le *nom d’hôte*:*numéro de port* de votre ressource locale.

Les étapes décrites dans cet article supposent que vous utilisez le navigateur de l’ordinateur qui hébergera l’agent de connexion hybride sur site.

Si vous avez déjà SQL Server installés dans une configuration et un environnement qui répond aux conditions décrites ci-dessus, vous pouvez passer et commencer par [créer une base de données SQL Server locale](#CreateSQLDB).

<a name="InstallSQL"></a>
## <a name="a-install-sql-server-express-enable-tcpip-and-create-a-sql-server-database-on-premises"></a>A. Installation de SQL Server Express, activer le protocole TCP/IP et créer une base de données SQL Server sur site ##

Cette section vous indique comment installer de SQL Server Express, activer le protocole TCP/IP et créer une base de données afin que votre application web fonctionne avec le portail Azure.

### <a name="install-sql-server-express"></a>Installer SQL Server Express ###

1. Pour installer de SQL Server Express, exécutez le fichier **SQLEXPRWT_x64_ENU.exe** ou **SQLEXPR_x86_ENU.exe** que vous avez téléchargé. L’Assistant de centre d’Installation SQL Server s’affiche.

    ![Installation de SQL Server][SQLServerInstall]

2. Choisissez **installation autonome du nouveau SQL Server ou ajout de fonctionnalités à une installation existante**. Suivez les instructions, acceptez les options par défaut et les paramètres, jusqu'à ce que vous arrivez à la page de **Configuration de l’Instance** .

3. Dans la page **Configuration de l’Instance** , sélectionnez **instance par défaut**.

    ![Sélectionnez une Instance par défaut][ChooseDefaultInstance]

    Par défaut, l’instance par défaut de SQL Server écoute les requêtes des clients SQL Server sur le port 1433, statique qui est ce que nécessite la fonctionnalité connexions de hybride. Instances nommées utilisent des ports dynamiques et UDP, qui ne sont pas pris en charge par les connexions hybride.

4. Acceptez les valeurs par défaut sur la page de **Configuration du serveur** .

5. Sur la page de **Configuration du moteur de base de données** , sous **Mode d’authentification**, choisissez **Mode mixte (SQL Server et l’authentification Windows)**et fournir un mot de passe.

    ![Sélectionnez le Mode mixte][ChooseMixedMode]

    Dans ce didacticiel, vous utilisez l’authentification SQL Server. Veillez à noter le mot de passe que vous fournissez, car vous en aurez besoin ultérieurement.

6. Passez en revue le reste de l’Assistant pour terminer l’installation.

### <a name="enable-tcpip"></a>Activer le protocole TCP/IP ###
Pour activer le protocole TCP/IP, vous allez utiliser le Gestionnaire de Configuration SQL Server, qui a été installée lors de l’installation de SQL Server Express. Suivez les étapes décrites dans [Activer le protocole de réseau TCP/IP pour SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) avant de continuer.

<a name="CreateSQLDB"></a>
### <a name="create-a-sql-server-database-on-premises"></a>Créer une base de données SQL Server sur site ###

Votre application web de Visual Studio requiert une base de données d’appartenance qui est accessible par Azure. Cette opération nécessite une base de données SQL Server ou de SQL Server Express (pas la base LocalDB le modèle MVC utilise par défaut), afin que vous allez ensuite créer la base de données d’appartenance.

1. Dans SQL Server Management Studio, connectez-vous à la SQL Server que vous venez d’installer. (Si la **connexion au serveur de** boîte de dialogue ne s’affiche pas automatiquement, naviguez dans **l’Explorateur d’objets** dans le volet gauche, cliquez sur **se connecter**, puis cliquez sur **Le moteur de base de données**.)  ![De se connecter au serveur][SSMSConnectToServer]

    Pour le **type de serveur**, choisissez le **Moteur de base de données**. Pour le **nom du serveur**, vous pouvez utiliser **localhost** ou le nom de l’ordinateur que vous utilisez. Choisissez **l’authentification SQL Server**, puis ouvrez une session avec le nom d’utilisateur et le mot de passe que vous avez créé précédemment.

2. Pour créer une nouvelle base de données en utilisant SQL Server Management Studio, cliquez sur **bases de données** dans l’Explorateur d’objets, puis cliquez sur **Nouvelle base de données**.

    ![Créer la nouvelle base de données][SSMScreateNewDB]

3. Dans la boîte de dialogue **Nouvelle base de données** , entrez MembershipDB pour le nom de la base de données, puis cliquez sur **OK**.

    ![Fournir le nom de la base de données][SSMSprovideDBname]

    Notez que vous ne pas toutes les modifications apportées à la base de données à ce stade. Les informations d’appartenance ajoutera automatiquement ultérieurement par votre application web lorsque vous l’exécutez.

4. Dans l’Explorateur d’objets, si vous développez des **bases de données**, vous verrez que la base de données d’appartenance a été créé.

    ![MembershipDB créé][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## <a name="b-create-a-web-app-in-the-azure-portal"></a>B. Créer une application web dans le portail Azure ##

> [AZURE.NOTE] Si vous avez déjà créé une application web dans le portail Azure que vous souhaitez utiliser pour ce didacticiel, vous pouvez passer directement à [créer une connexion hybride et un BizTalk Service](#CreateHC) et continuer à partir de là.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web + Mobile** > **Web app**.

    ![Bouton Nouveau][New]

2. Configurez votre application web, puis cliquez sur **créer**.

    ![Nom du site Web][WebsiteCreationBlade]

3. Après quelques instants, l’application web est créée et sa pale d’application web s’affiche. La lame est un tableau de bord vertical avec défilement qui vous permet de gérer votre application web.

    ![Site Web en cours d’exécution][WebSiteRunningBlade]

    Pour vérifier que l’application web est en ligne, vous pouvez cliquer sur l’icône **Parcourir** pour afficher la page par défaut.

Ensuite, vous créerez une connexion hybride et un service BizTalk pour l’application web.

<a name="CreateHC"></a>
## <a name="c-create-a-hybrid-connection-and-a-biztalk-service"></a>C. Créer une connexion hybride et un Service BizTalk ##

1. Dans le portail, cliquez sur paramètres et cliquez sur **réseau** > **configurer vos points de terminaison de connexion hybride**.

    ![Connexions hybride][CreateHCHCIcon]

2. Sur la lame de connexions hybride, cliquez sur **Ajouter** > **nouvelle connexion hybride**.

3. Sur la lame de **créer une connexion hybride** :
    - Pour **nom**, fournissez un nom pour la connexion.
    - Pour le **nom d’hôte**, entrez le nom de l’ordinateur hôte de SQL Server.
    - **Port**, tapez 1433 (le port par défaut pour SQL Server).
    - Cliquez sur **Le BizTalk Service** > **Nouveau BizTalk Service** et entrez un nom pour le service BizTalk.

    ![Créer une connexion hybride][TwinCreateHCBlades]

5. Cliquez deux fois sur **OK** .

    Lorsque le processus se termine, la volonté de zone **Notifications** flash vert **succès** et la volonté de lame de **connexion de hybride** afficher la nouvelle connexion hybride avec l’état **non**connecté.

    ![Connexion d’un hybride créée][CreateHCOneConnectionCreated]

À ce stade, vous avez terminé une partie importante de l’infrastructure de connexion cloud hybride. Ensuite, vous allez créer une partie sur site correspondant.

<a name="InstallHCM"></a>
## <a name="d-install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>D. Installer le Gestionnaire de connexion hybride sur site pour effectuer la connexion ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Maintenant que l’infrastructure de connexion hybride est terminée, vous allez créer une application web qui l’utilise.

<a name="CreateASPNET"></a>
## <a name="e-create-a-basic-aspnet-web-project-edit-the-database-connection-string-and-run-the-project-locally"></a>E. Créez un projet web de base ASP.NET, modifier la chaîne de connexion de base de données et exécuter le projet localement ##

### <a name="create-a-basic-aspnet-project"></a>Créez un projet ASP.NET de base ###
1. Dans Visual Studio, dans le menu **fichier** , créez un nouveau projet :

    ![Nouveau projet de Visual Studio][HCVSNewProject]

2. Dans la section **modèles** de la boîte de dialogue **Nouveau projet** , sélectionnez **Web** et choisissez **ASP.NET Web Application**, puis cliquez sur **OK**.

    ![Choisissez l’Application Web d’ASP.NET][HCVSChooseASPNET]

3. Dans la boîte de dialogue **Nouveau projet ASP.NET** **MVC**de choisir et puis cliquez sur **OK**.

    ![Choisir MVC][HCVSChooseMVC]

4. Lorsque le projet a été créé, la page d’application readme s’affiche. N’exécutez pas encore le projet web.

    ![Page du fichier Lisezmoi][HCVSReadmePage]

### <a name="edit-the-database-connection-string-for-the-application"></a>Modifier la chaîne de connexion de base de données de l’application ###

Dans cette étape, vous modifiez la chaîne de connexion qui indique à votre application permettant de trouver votre base de données de SQL Server Express locale. La chaîne de connexion se trouve dans le fichier Web.config de l’application, qui contient les informations de configuration de l’application.

> [AZURE.NOTE] Pour vous assurer que votre application utilise la base de données que vous avez créé dans de SQL Server Express et non à celui de la valeur par défaut de Visual Studio LocalDB, il est important d’effectuer cette étape avant d’exécuter votre projet.

1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Web.config.

    ![Web.config][HCVSChooseWebConfig]

2. Modifiez la section **connectionStrings** pour pointer vers la base de données SQL Server sur votre ordinateur local, en respectant la syntaxe dans l’exemple suivant :

    ![Chaîne de connexion][HCVSConnectionString]

    Lorsque vous composez la chaîne de connexion, gardez à l’esprit les éléments suivants :

    - Si vous vous connectez à une instance nommée, au lieu d’une instance par défaut (par exemple, YourServer\SQLEXPRESS), vous devez configurer votre SQL Server pour utiliser les ports statiques. Pour plus d’informations sur la configuration des ports statiques, consultez [comment configurer SQL Server pour écouter sur un port spécifique](http://support.microsoft.com/kb/823938). Par défaut, les instances nommées utilisent UDP et les ports dynamiques ne sont pas pris en charge par les connexions hybride.

    - Il est recommandé de spécifier le port (1433 par défaut, comme illustré dans l’exemple) sur la chaîne de connexion afin que vous pouvez être sûr que votre SQL Server local TCP activé et utilise le port correct.

    - N’oubliez pas d’utiliser l’authentification SQL Server pour se connecter, spécifier l’ID utilisateur et le mot de passe dans votre chaîne de connexion.

3. Cliquez sur **Enregistrer** dans Visual Studio pour enregistrer le fichier Web.config.

### <a name="run-the-project-locally-and-register-a-new-user"></a>Exécuter le projet localement et inscrire un nouvel utilisateur ###

1. Maintenant, exécutez localement votre nouveau projet web en cliquant sur le bouton Parcourir dans le débogage. Cet exemple utilise Internet Explorer.

    ![Exécuter le projet][HCVSRunProject]

2. Dans le coin supérieur droit de la page web par défaut, choisissez **Enregistrer** pour enregistrer un nouveau compte :

    ![Enregistrer un nouveau compte][HCVSRegisterLocally]

3. Entrez un nom d’utilisateur et le mot de passe :

    ![Entrez le nom d’utilisateur et le mot de passe][HCVSCreateNewAccount]

    Cette opération crée automatiquement une base de données sur le SQL Server local qui contient les informations d’appartenance pour votre application. Une des tables (**dbo. AspNetUsers**) contient web informations d’identification utilisateur de app comme celles que vous venez d’entrer. Vous verrez le tableau plus loin dans le didacticiel.

4. Fermez la fenêtre du navigateur de la page web par défaut. Cette commande arrête l’application dans Visual Studio.

Vous êtes maintenant prêt pour l’étape suivante, qui consiste à publier l’application sur Azure et testez-le.

<a name="PubNTest"></a>
## <a name="f-publish-the-web-application-to-azure-and-test-it"></a>F. Publier l’application web vers Azure et la tester ##

Maintenant, vous publiez votre application à votre application Service web app et puis effectuez un test pour voir comment la connexion hybride que vous avez configurée précédemment est utilisée pour la connexion de votre application web à la base de données sur votre ordinateur local.

### <a name="publish-the-web-application"></a>Publier l’application web ###

1. Vous pouvez télécharger votre profil de publication de l’application web de Service de l’application dans Azure Portal. Sur la lame pour votre application web, cliquez sur le **profil de la publication Get**et puis enregistrez le fichier sur votre ordinateur.

    ![Téléchargement publier le profil][PortalDownloadPublishProfile]

    Ensuite, vous allez importer ce fichier dans votre application web de Visual Studio.

2. Dans Visual Studio, cliquez sur le nom du projet dans l’Explorateur de solutions et sélectionnez **Publier**.

    ![Sélectionnez Publier][HCVSRightClickProjectSelectPublish]

3. Dans la boîte de dialogue **Publier le site Web** , sous l’onglet **profil** , choisissez **Importer**.

    ![Importation][HCVSPublishWebDialogImport]

4. Accédez à votre profil de publication téléchargé, sélectionnez-le et puis cliquez sur **OK**.

    ![Accédez au profil][HCVSBrowseToImportPubProfile]

5. Votre publication des informations sont importées et s’affiche sous l’onglet **connexion** de la boîte de dialogue.

    ![Cliquez sur Publier][HCVSClickPublish]

    Cliquez sur **Publier**.

    Une fois la publication terminée, votre navigateur lancera et afficher votre application ASP.NET maintenant familière, à ceci près qu’il est maintenant live dans le nuage Azure !

Ensuite, vous utiliserez votre application web en direct pour voir sa connexion hybride en action.

### <a name="test-the-completed-web-application-on-azure"></a>Test de l’application de site web terminé sur Azure ###

1. Dans la partie supérieure droite de votre page web sur Azure, cliquez sur **se connecter**.

    ![Journal des tests dans][HCTestLogIn]

2. Votre application de web Service de l’application est maintenant connectée à la base de données d’appartenance de votre application web sur votre ordinateur local. Pour vérifier cela, ouvrez une session avec les mêmes informations d’identification que vous avez entrées précédemment dans la base de données locale.

    ![Vœux de Hello][HCTestHelloContoso]

3. Pour effectuer d’autres tests de votre nouvelle connexion hybride, déconnectez-vous de votre application web Azure et enregistrer en tant qu’un autre utilisateur. Fournir un nouveau nom d’utilisateur et un mot de passe, puis cliquez sur **Enregistrer**.

    ![Test inscrire un autre utilisateur][HCTestRegisterRelecloud]

4. Pour vérifier que les nouvelles informations d’identification ont été stockées dans votre base de données locale par le biais de votre connexion hybride, ouvrez SQL Management Studio sur votre ordinateur local. Dans l’Explorateur d’objets, puis développez la base de données **MembershipDB** et **Tables**. Avec le bouton droit de la dbo de **. AspNetUsers** l’appartenance au tableau et choisissez **Sélectionner Top 1000 lignes** pour afficher les résultats.

    ![Afficher les résultats][HCTestSSMSTree]

5. La table d’abonnement local affiche maintenant les deux comptes - celui que vous avez créés localement et celui que vous avez créé dans le nuage Azure. Celui que vous avez créé dans le nuage a été enregistré dans votre base de données sur site via la fonctionnalité de connexion d’hybrides d’Azure.

    ![Utilisateurs enregistrés dans la base de données des locaux][HCTestShowMemberDb]

Vous avez maintenant créé et déployé une application web ASP.NET qui utilise une connexion hybride entre une application web dans le nuage Azure et une base de données de SQL Server sur site. Félicitations !

## <a name="see-also"></a>Voir aussi ##
[Vue d’ensemble des connexions hybride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybride (vidéo Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Vue d’ensemble des connexions hybride](/services/biztalk-services/)

[Les Services BizTalk : Onglets de tableau de bord, moniteur, échelle, configurer et hybride connexion](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Création d’un nuage hybride de réelles avec la portabilité des applications transparente (vidéo Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Accéder à des ressources sur site à l’aide de connexions de hybride dans le Service d’application Azure](../app-service-web/web-sites-hybrid-connection-get-started.md)

[Vue d’ensemble de l’identité d’ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
