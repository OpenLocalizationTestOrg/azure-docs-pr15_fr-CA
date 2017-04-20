<properties
   pageTitle="Comment migrer et publier une Application Web à un Service Cloud Azure dans Visual Studio | Microsoft Azure"
   description="Apprenez à migrer et publier votre application web à un service cloud Azure à l’aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Comment : migrer et publier une Application Web à un Service Cloud Azure à partir de Visual Studio

Pour tirer parti des services d’hébergement et l’évolutivité d’Azure, vous pouvez souhaiter migrer et publier votre application web à un service cloud Azure. Vous pouvez exécuter une application web dans Azure avec peu de modifications à votre application existante.

>[AZURE.NOTE] Cette rubrique est sur le déploiement de services dans le nuage, pas aux sites web. Pour plus d’informations sur le déploiement de sites web, voir [déploiement d’une application web dans le Service d’application Azure](./app-service-web/web-sites-deploy.md).

Pour obtenir une liste de modèles spécifiques qui sont pris en charge pour Visual C# et Visual Basic, reportez-vous à la section **Prise en charge des modèles de projet** plus loin dans cette rubrique.

Vous devez d’abord activer votre application web pour Azure à partir de Visual Studio. L’illustration suivante montre les étapes clés pour publier votre application web existante en ajoutant un projet à utiliser pour le déploiement d’Azure. Cette procédure ajoute un projet Azure avec le rôle de site web à votre solution. Selon le type de projet web dont vous disposez, les propriétés du projet pour les assemblys sont également mis à jour si le package de service nécessite des assemblys supplémentaires pour le déploiement.

![Publier une application Web vers Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] **Convertir**, commande **convertir en projet de Service Cloud Azure** s’affiche uniquement pour le projet web dans votre solution. Par exemple, la commande n’est pas disponible pour un projet Silverlight dans votre solution.
Lorsque vous créez un package de service ou que vous publiez votre application Azure, des avertissements ou des erreurs peuvent se produire. Ces erreurs et les avertissements peuvent vous aider à résoudre les problèmes avant de déployer sur Azure. Par exemple, vous pouvez recevoir un message d’avertissement à propos d’un assembly manquant. Pour plus d’informations sur la façon de traiter tous les avertissements comme des erreurs, voir [configuration d’un projet de Service Cloud Azure avec Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Si vous générez votre application, exécutez localement à l’aide de l’émulateur de calcul ou la publiez sur Azure, vous pouvez voir le message d’erreur suivant dans la fenêtre **Liste d’erreurs** : **le chemin d’accès spécifié, du nom de fichier ou les deux sont trop longs**. Cette erreur se produit car la longueur du nom du projet d’Azure complet est trop longue. La longueur du nom du projet, y compris le chemin d’accès complet, ne peut pas être plus de 146 caractères. Par exemple, c’est le nom du projet complet, y compris le chemin d’accès de fichier pour un projet Azure qui est créé pour une application Silverlight : `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Vous devrez peut-être déplacer votre solution à un autre répertoire qui a un chemin plus court pour réduire la longueur du nom de projet complet.

Pour migrer et publier une application web vers Azure à partir de Visual Studio, procédez comme suit.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Activer une Application Web pour le déploiement vers Azure

### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Pour activer une application web pour le déploiement vers Azure

1. Pour permettre à votre application web pour un déploiement d’Azure, ouvrez le menu contextuel d’un projet web dans votre solution et choisissez Ajouter un projet de déploiement Azure.

    Les actions suivantes se produisent :

    - Un projet Azure appelé `<name of the web project>.Azure` est ajouté à la solution pour votre application.

    - Un rôle web pour le projet web est ajouté à ce projet Azure.

    - La propriété **Copie locale** a la valeur true pour tous les assemblys qui sont requis pour MVC de MVC 2, MVC 3, 4 et Applications professionnelles de Silverlight. Ajoute ces assemblys dans le package de service qui est utilisé pour le déploiement.

  >[AZURE.IMPORTANT] Si vous avez d’autres assemblys ou les fichiers qui sont requis pour cette application web, vous devez définir manuellement les propriétés de ces fichiers. Pour plus d’informations sur la façon de définir ces propriétés, consultez la section **Inclure les fichiers dans le Package de Service** plus loin dans cet article.

  >[AZURE.NOTE] S’il existe déjà un rôle web pour un projet web spécifique dans un projet Azure dans la solution, **convertir**, **convertir en projet de Service Cloud Azure** n’est pas affiché dans le menu contextuel pour ce projet web.

  Si vous avez plusieurs projets web dans votre application web et que vous souhaitez créer des rôles web pour chaque projet web, vous devez effectuer les étapes de cette procédure pour chaque projet web. Cela crée des projets Azure distincts pour chaque rôle de web. Chaque projet web peut être publié séparément. Vous pouvez également ajouter manuellement un autre rôle web à un projet Azure existant dans votre application web. Pour ce faire, ouvrez le menu contextuel pour le dossier **rôles** dans votre projet Azure, choisissez **Ajouter**, puis sur **Le projet de rôle Web dans la solution**, choisir le projet à ajouter comme un rôle web, puis cliquez sur le bouton **OK** .

## <a name="use-an-azure-sql-database-for-your-application"></a>Utiliser une base de données SQL Azure pour votre Application

Si vous avez une chaîne de connexion pour votre application web qui utilise une base de données SQL Server qui se trouve sur le site, vous devez modifier cette chaîne de connexion pour utiliser une instance de base de données de SQL qui héberge Azure à la place.

>[AZURE.IMPORTANT] Votre abonnement doit pouvoir utiliser SQL de base de données. Si vous accédez à votre abonnement à partir du [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez déterminer les services offerts par votre abonnement. Les instructions suivantes s’appliquent à la version [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). Si vous utilisez le [portail d’Azure](http://portal.microsoft.com), passez à la procédure suivante.

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Pour utiliser une instance de base de données SQL dans votre rôle de web pour votre chaîne de connexion

1. Pour créer une instance de base de données de SQL [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), suivez les étapes décrites dans l’article suivant : [créer un serveur de base de données SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Lorsque vous configurez les règles du pare-feu de votre instance de base de données SQL, vous devez sélectionner la case à cocher **Autoriser d’autres services Azure à accéder à ce serveur** .

1. Pour créer une instance de base de données SQL à utiliser pour la chaîne de connexion, suivez les étapes de la section suivante dans l’article suivant : [créer une base de données SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Pour copier la chaîne de connexion ADO.NET à utiliser pour la chaîne de connexion, procédez comme suit dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).  

  1. Cliquez sur le bouton de la **base de données** et ouvrez le nœud pour l’abonnement que vous avez utilisé pour créer votre instance de base de données de SQL.

  1. Pour afficher les instances disponibles de SQL de base de données, sélectionnez le nœud **Bases de données SQL** .

  1. Pour afficher les propriétés de la base de données, sélectionnez la base de données. La vue **Propriétés** s’affiche.

      >[AZURE.NOTE] Si la vue de **Propriétés** ne s’affiche pas, vous devrez ouvrir à l’aide de la ligne de séparation.

  1. Pour afficher les chaînes de connexion, cliquez sur le bouton de sélection (...) en regard de la vue.

    La boîte de dialogue **Chaînes de connexion** s’affiche.

  1. Pour copier la chaîne de connexion ADO.NET, mettez en surbrillance le texte et cliquez sur les touches Ctrl + C.

  1. Pour fermer la boîte de dialogue, cliquez sur le bouton **Fermer** .

1. Pour remplacer la chaîne de connexion dans le fichier web.config pour utiliser cette instance de base de données de SQL, ouvrez le fichier web.config, sélectionnez l’entrée de chaîne de connexion existante et cliquez sur les touches Ctrl + V. La chaîne de connexion ADO.NET pour l’instance de base de données de SQL remplace la chaîne de connexion existante.

1. Vous devez également ajouter le paramètre `MultipleActiveResultSets=True` à la chaîne de connexion. La chaîne de connexion doit avoir le format suivant :

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Facultatif) Une autre méthode de modification de la chaîne de connexion directement dans le fichier web.config est d’ajouter une section dans un des fichiers de transformation web.config, en fonction de la configuration de build que vous utilisez pour créer votre package de services. Ouvrez le fichier Web.Debug.Config ou le fichier Web.Release.Config. Dans ce fichier, ajoutez la section suivante :

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Enregistrez le fichier que vous avez modifié et republiez votre application.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Pour utiliser une instance de base de données de SQL à l’aide du portail classique Azure

1. Dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), choisissez le nœud bases de données SQL.

  - Si l’instance de base de données SQL que vous souhaitez utiliser s’affiche, choisissez Ouvrir.

  - Si vous n’avez pas créé de toutes les instances, cliquez sur le lien approprié et puis créer une instance.

1. Une fois que vous ouvrez ou créez une instance de base de données, cliquez sur le lien de **Chaînes de connexion** .

1. En bas de la page, cliquez sur le lien pour configurer les paramètres du pare-feu et acceptez les valeurs par défaut ou configurez les valeurs dont vous avez besoin.

1. Copier la chaîne de connexion ADO.NET, collez-le dans votre fichier web.config sur l’ancienne chaîne de connexion pour la base de données sur site et veillez à ajouter `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publier une Application Web vers Azure

### <a name="to-publish-a-web-application-to-azure"></a>Pour publier une application Web vers Azure

1. Pour tester l’application dans le développement local environnement à l’aide de l’Azure compute émulateur, ouvre le menu contextuel du projet Azure pour le rôle web et sélectionnez **définir comme projet de démarrage**. Puis choisissez **Déboguer**, **Démarrer le débogage** (clavier : **F5**).

    La boîte de dialogue **Démarrer l’environnement de débogage Azure** s’ouvre et le démarrage de l’application dans le navigateur. Pour plus d’informations sur le démarrage de chaque type d’application web dans l’émulateur de calcul spécifiques, consultez le tableau dans cette section.

1. Pour configurer les services de votre application publier sur Azure, vous devez disposer d’un compte Microsoft et un abonnement Azure. Suivez les étapes dans la rubrique suivante pour configurer vos services : [Préparation à publier ou à déployer une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Pour publier l’application web pour Azure, ouvrez le menu contextuel du projet web et choisissez **Publier vers Azure**.

    La boîte de dialogue **Publier les applications Azure** s’ouvre et Visual Studio démarre le processus de déploiement. Pour plus d’informations sur la façon de publier l’application, consultez la section **publier une Application Azure à partir de Visual Studio** dans la [publication d’un Service Cloud à l’aide d’outils Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] Vous pouvez également publier l’application web à partir du projet Azure. Pour ce faire, ouvrez le menu contextuel du projet Azure et choisissez **Publier**.

1. Pour voir la progression du déploiement, vous pouvez afficher la fenêtre du **Journal d’activité Azure** . Ce journal s’affiche automatiquement au démarrage du processus de déploiement. Vous pouvez étendre la ligne de facturation dans le journal d’activité pour afficher des informations détaillées, comme indiqué dans l’illustration suivante :

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Facultatif) Pour annuler le processus de déploiement, ouvrez le menu contextuel pour la ligne de facturation dans le journal d’activité et cliquez sur **Annuler et supprimer**. Cela arrête le processus de déploiement et le supprime de l’environnement de déploiement d’Azure.

    >[AZURE.NOTE] Pour supprimer cet environnement de déploiement après que qu’il a été déployé, vous devez utiliser le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facultatif) Une fois vos instances de rôles ont démarré, Visual Studio affiche automatiquement l’environnement de déploiement dans le nœud **De calcul Azure** **Cloud Explorer** ou **l’Explorateur de serveurs**. À partir de là, vous pouvez afficher l’état des instances de chaque rôle.

    L’illustration suivante montre les instances de rôle dans **l’Explorateur de serveurs** lorsqu’ils sont toujours dans l’état d’initialisation :

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Pour accéder à votre application après le déploiement, cliquez sur la flèche en regard de votre déploiement lorsque l’état **est terminé** s’affiche dans le **journal d’activité d’Azure**. Cela affiche l’URL de votre application web dans Azure. Reportez-vous au tableau suivant pour les détails sur le démarrage d’un type spécifique de l’application web à partir d’Azure.

    Le tableau suivant répertorie les détails sur la façon de démarrer des applications web spécifiques à partir d’Azure, ou d’exécuter ou de déboguer une application web localement à l’aide de l’émulateur de calcul Azure :

  	|Type d’Application Web|Exécuter/déboguer localement à l’aide de l’émulateur de calcul|En cours d’exécution dans Azure|
  	|---|---|---|
  	|Application Web d’ASP.NET|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Cliquez sur le lien de l’URL affiché dans l’onglet **déploiement** pour le **journal d’activité de Azure** charger la page de démarrage dans le navigateur.|
  	|Application Web ASP.NET MVC 2|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Cliquez sur le lien de l’URL affiché dans l’onglet **déploiement** pour le **journal d’activité de Azure** charger la page de démarrage dans le navigateur.|
  	|Application Web ASP.NET MVC 3|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Cliquez sur le lien de l’URL affiché dans l’onglet **déploiement** pour le **journal d’activité de Azure** charger la page de démarrage dans le navigateur.|
  	|Application Web ASP.NET MVC 4|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Cliquez sur le lien de l’URL affiché dans l’onglet **déploiement** pour le **journal d’activité de Azure** charger la page de démarrage dans le navigateur.|
  	|Application de Web ASP.NET vide|Vous devez ajouter une page .aspx qui vous définir comme page de démarrage pour votre projet web dans votre application. Puis sur la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Si vous avez une page de .aspx par défaut dans votre application, cliquez sur le lien de l’URL affiché dans l’onglet **déploiement** pour le **journal d’activité de Azure** et la page est chargée dans le navigateur. Si vous avez une page .aspx différents, vous devez accéder à cette page spécifique en utilisant le format suivant pour l’url de votre :`<url for deployment>/<name of page>.aspx`|
  	|Application Silverlight|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous avez besoin accéder à la page spécifique de votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of page>.aspx`|
  	|Application métier de Silverlight|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous avez besoin accéder à la page spécifique de votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of page>.aspx`|
  	|Application de Navigation Silverlight|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous avez besoin accéder à la page spécifique de votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of page>.aspx`|
  	|Application de Service WCF|Vous devez définir le fichier .svc comme page de démarrage pour votre projet de Service WCF. Puis sur la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous avez besoin naviguer vers le fichier svc pour votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of service file>.svc`|
  	|Application de Service de Workflow WCF|Vous devez définir le fichier .svc comme page de démarrage pour votre projet de Service WCF. Puis sur la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous avez besoin naviguer vers le fichier svc pour votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of service file>.svc`|
  	|Entités ASP.NET dynamique|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous devez mettre à jour la chaîne de connexion (voir section suivante). Vous devez également accéder à la page spécifique de votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of page>.aspx`|
  	|ASP.NET Dynamic Data Linq to SQL|Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** (clavier : cliquez sur la touche **F5** .).|Vous devez suivre les étapes de cette procédure : utiliser une base de données SQL Azure pour votre application (voir la section précédente dans cette rubrique). Vous devez également accéder à la page spécifique de votre application en utilisant le format suivant pour l’url :`<url for deployment>/<name of page>.aspx`|

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Mettre à jour une chaîne de connexion pour les entités ASP.NET dynamique

### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Pour mettre à jour une chaîne de connexion pour les entités ASP.NET dynamique

1. Pour créer une base de données SQL Azure qui peut être utilisé pour une application web de ASP.NET des entités dynamiques, suivez les étapes de la procédure **d’utilisation d’une base de données SQL Azure pour votre application** plus haut dans cette rubrique.

1. Ajoutez les tables et les champs dont vous avez besoin pour cette base de données à partir du [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

1. La chaîne de connexion pour ce type d’application a le format suivant dans le fichier web.config :  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Mettre à jour la valeur *connectionString* avec la chaîne de connexion ADO.NET pour votre base de données SQL Azure comme suit :

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

1. Pour enregistrer le fichier web.config avec les modifications que vous avez apportées à la chaîne de connexion, dans la barre de menu Choisissez **fichier**, **enregistrez le fichier web.config**.

## <a name="supported-project-templates"></a>Modèles de projet pris en charge

Pour publier une application web vers Azure, l’application doit utiliser un des modèles de projet pour C# ou Visual Basic qui est répertorié dans le tableau ci-dessous.

|Groupe de modèles de projet|Modèle de projet|
|---|---|
|Web|Application Web d’ASP.NET|
|Web|Application Web ASP.NET MVC 2|
|Web|Application Web ASP.NET MVC 3|
|Web|Application de Web ASP.NET MVC4|
|Web|Application de Web ASP.NET vide|
|Web|Application de site Web vide d’ASP.NET MVC 2|
|Web|Entités ASP.NET Dynamic Data Web Application|
|Web|ASP.NET Dynamic Data Linq to SQL Web Application|
|Silverlight|Application Silverlight|
|Silverlight|Application métier de Silverlight|
|Silverlight|Application de Navigation Silverlight|
|WCF|Application de Service WCF|
|WCF|Application de Service de Workflow WCF|
|Flux de travail|Application de Service de Workflow WCF|

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la publication, consultez [Préparation à publier ou à déployer une Application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Également extraire le [Paramètre de configuration nommé d’informations d’authentification](vs-azure-tools-setting-up-named-authentication-credentials.md).
