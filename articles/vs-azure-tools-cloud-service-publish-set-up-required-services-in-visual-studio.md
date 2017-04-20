<properties
   pageTitle="Préparer la publier ou de déployer une application Azure depuis Visual Studio | Microsoft Azure"
   description="Découvrez les procédures pour configurer le compte de services cloud et de stockage et configurer votre application Azure."
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

# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Préparer la publier ou de déployer une Application Azure à partir de Visual Studio

## <a name="overview"></a>Vue d’ensemble

Avant de publier un projet de service cloud, vous devez configurer les services suivants :

- Un **service en nuage** pour exécuter des rôles dans l’environnement Azure

- Un **compte de stockage** qui fournit l’accès aux services de Blob et Table file d’attente.

Utilisez les procédures suivantes pour configurer ces services et configurer votre application


## <a name="create-a-cloud-service"></a>Créer un service cloud

Pour publier un service cloud sur Azure, vous devez d’abord créer un service en nuage, qui exécute vos rôles dans l’environnement Azure. Vous pouvez créer un service cloud dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885), comme décrit dans la section **pour créer un service cloud à l’aide du portail classique Azure**, plus loin dans cette rubrique. Vous pouvez également créer un service cloud dans Visual Studio à l’aide de l’Assistant Publication.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Pour créer un service en nuage à l’aide de Visual Studio

1. Ouvrir le menu contextuel du projet Azure et choisissez **Publier**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Si vous n’êtes pas connecté, connectez-vous avec votre nom d’utilisateur et le mot de passe pour le compte de Microsoft ou d’organisation qui est associé à votre abonnement Azure.

1. Cliquez sur le bouton **suivant** pour passer à la page **paramètres** .

    ![Paramètres communs de l’Assistant Publication](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. Dans la liste des **Services en nuage** , choisissez **Créer un nouveau**. La boîte de dialogue **Créer des Services Azure** s’affiche.

1. Entrez le nom de votre service cloud. Le nom fait partie de l’URL de votre service et par conséquent doit être globalement unique. Le nom ne respecte pas la casse.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Pour créer un service en nuage à l’aide du portail classique Azure

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkId=253103) sur le site Web de Microsoft.

1. (facultatif) Pour afficher une liste des services en nuage que vous avez déjà créé, cliquez sur le lien de Services en nuage sur le côté gauche de la page.

1. Choisissez le **+** l’icône dans l’angle inférieur gauche de coin et dans le menu qui s’affiche, choisissez le **Service Cloud** . Un autre écran avec deux options : **Création rapide** et **Personnalisée créer**, s’affiche. Si vous choisissez **Création rapide**, vous pouvez créer un service cloud en spécifiant son URL et la région où il sera physiquement hébergé. Si vous choisissez **Personnalisé créer**, vous pouvez publier immédiatement un service cloud en spécifiant un package (fichier .cspkg), un fichier de configuration (.cscfg) et un certificat. Création personnalisée n’est pas nécessaire si vous prévoyez de publier votre service cloud à l’aide de la commande **Publier** dans un projet Azure. La commande **Publier** est disponible dans le menu contextuel pour un projet Azure.

1. Choisissez la **Création rapide** de publier votre service cloud à l’aide de Visual Studio.

1. Spécifiez un nom pour votre service cloud. L’URL complète s’affiche en regard du nom.

1. Dans la liste, cliquez sur la zone où se trouvent la plupart de vos utilisateurs.

1. En bas de la fenêtre, cliquez sur le lien **Créer un Service Cloud** .

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Un compte de stockage fournit un accès aux services de Blob et Table file d’attente. Vous pouvez créer un compte de stockage à l’aide de Visual Studio ou l' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Pour créer un compte de stockage à l’aide de Visual Studio

1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le nœud de **stockage** et puis cliquez sur **Créer un compte de stockage**.

    ![Créer un nouveau compte de stockage Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Sélectionnez ou entrez les informations suivantes pour le nouveau compte de stockage dans la boîte de dialogue **Créer un compte de stockage** .
    - L’abonnement Azure auquel vous voulez ajouter le compte de stockage.
    - Le nom que vous souhaitez utiliser pour le nouveau compte de stockage.
    - La région ou le groupe d’affinité (par exemple, ouest des Etats-Unis ou d’Asie de l’est).
    - Le type de réplication que vous souhaitez utiliser pour le compte de stockage, comme Geo redondant.

1. Lorsque vous avez terminé, cliquez sur **créer**. Le nouveau compte de stockage apparaît dans la liste de **stockage** dans **l’Explorateur de serveurs**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Pour créer un compte de stockage à l’aide du portail classique Azure

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkId=253103) sur le site Web de Microsoft.

1. (Facultatif) Pour afficher vos comptes de stockage, cliquez sur le lien de **stockage** dans le panneau sur le côté gauche de la page.

1. Dans le coin inférieur gauche de la page, cliquez sur le **+** icône.

1. Dans le menu qui s’affiche, cliquez sur **stockage**et puis choisissez **Création rapide**.

1. Nommez le compte de stockage qui se traduira par une url unique.

1. Nommez votre service cloud. L’URL complète s’affiche en regard du nom.

1. Dans la liste des régions, choisissez une région où se trouvent la plupart de vos utilisateurs.

1. Spécifiez si vous souhaitez activer la réplication de la zone géographique. Si vous activez la réplication géographique, les vos données seront enregistrées dans plusieurs emplacements physiques afin de réduire les risques de perte. Cette fonction permet de stockage plus coûteux, mais vous pouvez réduire le coût en activant la géolocalisation lorsque vous créez le compte de stockage au lieu d’ajouter la fonctionnalité ultérieurement. Pour plus d’informations, consultez [réplication géographique](http://go.microsoft.com/fwlink/?LinkId=253108).

1. En bas de la fenêtre, cliquez sur le lien **Créer un compte de stockage** .

Après avoir créé votre compte de stockage, vous verrez les URL que vous pouvez utiliser pour accéder aux ressources de chacun des services de stockage Azure, ainsi que les touches d’accès principal et secondaire de votre compte. Ces clés vous permet d’authentifier des demandes faites avec les services de stockage.

>[AZURE.NOTE] La clé d’accès secondaires fournit le même accès à votre compte de stockage comme touche d’accès principal et est générée comme une sauvegarde doit compromettre votre clé d’accès principal. En outre, il est recommandé de régénérer vos touches d’accès rapide sur une base régulière. Vous pouvez modifier un paramètre de chaîne de connexion pour utiliser la clé secondaire lors de la régénération de la clé primaire, vous pouvez le modifier pour utiliser la clé primaire régénérée lorsque vous régénérez la clé secondaire.

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configurer votre application pour utiliser les services fournis par le compte de stockage

Vous devez configurer n’importe quel rôle qui accède à des services de stockage pour utiliser les services de stockage Azure que vous avez créé. Pour ce faire, vous pouvez utiliser plusieurs configurations de service pour votre projet d’Azure. Par défaut, deux sont créés dans votre projet Azure. À l’aide de plusieurs configurations de service, vous pouvez utiliser la même chaîne de connexion dans votre code, mais ont une valeur différente pour une chaîne de connexion dans la configuration de chaque service. Par exemple, vous pouvez utiliser une configuration de service pour exécuter et déboguer votre application localement à l’aide de l’émulateur de stockage Azure et une configuration de service différent pour publier votre application sur Azure. Pour plus d’informations sur les configurations de service, consultez [Configuration de votre Azure projet à l’aide de plusieurs Configurations de Service](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Pour configurer votre application pour utiliser les services fournis par le compte de stockage

1. Dans Visual Studio, ouvrez votre solution Azure. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour chaque rôle dans votre projet Azure qui accède à des services de stockage et choisissez **Propriétés**. Une page avec le nom du rôle s’affiche dans l’éditeur de Visual Studio. La page affiche les champs de l’onglet **Configuration** .

1. Dans les pages de propriétés pour le rôle, cliquez sur **paramètres**.

1. Dans la liste de la **Configuration du Service** , cliquez sur le nom de la configuration du service que vous souhaitez modifier. Si vous souhaitez apporter des modifications à toutes les configurations de service pour ce rôle, vous pouvez choisir **Toutes les Configurations**.  Pour plus d’informations sur la façon de mettre à jour les configurations de service, consultez la section **Gérer les chaînes de connexion pour les comptes de stockage** dans la rubrique [configurer les rôles pour un Service de Cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Pour modifier les paramètres de chaîne de connexion, cliquez sur le bouton **...** bouton en regard du paramètre. La boîte de dialogue **Chaîne de connexion de stockage de créer** s’affiche.

1. Sous **se connecter en utilisant**, activez l’option de **votre abonnement** .

1. Dans la liste **d’abonnements** , choisissez votre abonnement. Si la liste des abonnements n’inclut pas celle que vous souhaitez, cliquez sur le lien de **Téléchargement des paramètres de publication** .

1. Dans la liste **nom du compte** , cliquez sur le nom de votre compte de stockage. Outils Azure obtient automatiquement des informations d’identification du compte de stockage en utilisant le fichier .publishsettings. Pour spécifier vos informations d’identification du compte de stockage manuellement, choisissez l’option **Saisie manuelle des informations d’identification** , puis poursuivez cette procédure. Vous pouvez obtenir votre nom de compte de stockage et d’une clé primaire à partir du [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885). Si vous ne souhaitez pas spécifier l’espace de stockage des paramètres de compte manuellement, cliquez sur le bouton **OK** pour fermer la boîte de dialogue.

1. Cliquez sur le lien d’informations d’identification de **compte de stockage d’entrée** .

1. Dans la zone **nom du compte** , entrez le nom de votre compte de stockage.

    >[AZURE.NOTE] Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885)et puis cliquez sur le bouton de **stockage** . Le portail affiche une liste des comptes de stockage. Si vous choisissez un compte, une page pour qu’elle s’ouvre. Vous pouvez copier le nom du compte de stockage à partir de cette page. Si vous utilisez une version antérieure du portail classique, le nom de votre compte de stockage apparaît dans l’affichage des **Comptes de stockage** . Pour copier ce nom, mettez-la en surbrillance dans la fenêtre **Propriétés** de cette vue, puis cliquez sur les touches Ctrl-C. Pour coller le nom de Visual Studio, cliquez sur la zone de texte **nom du compte** , puis cliquez sur les touches Ctrl + V.

1. Dans la zone **clé de compte** , entrez votre clé primaire, ou copier et coller à partir du [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).
    Pour copier cette clé :

    1. En bas de la page pour le compte de stockage approprié, cliquez sur le bouton de **Gestion des clés** .

    1. Dans la page **Gérer les accès clés** , sélectionnez le texte de la clé d’accès principal et cliquez ensuite sur les touches Ctrl + C.

    1. Dans Outils d’Azure, collez la clé dans la zone **clé de compte** .

    1. Vous devez sélectionner une des options suivantes pour déterminer comment le service accédera du compte de stockage :
        - **Utiliser HTTP**. Il s’agit de l’option standard. Par exemple, `http://<account name>.blob.core.windows.net`.
        - **Utiliser HTTPS** pour une connexion sécurisée. Par exemple, `https://<accountname>.blob.core.windows.net`.
        - **Spécifier les points de terminaison personnalisés** pour chacun des trois services. Vous pouvez ensuite taper ces points de terminaison dans le champ pour le service spécifique.

        >[AZURE.NOTE] Si vous créez des points de terminaison personnalisés, vous pouvez créer une chaîne de connexion plus complexe. Lorsque vous utilisez le format de cette chaîne, vous pouvez spécifier des points de terminaison storage services incluant un nom de domaine personnalisé que vous avez enregistrés pour votre compte de stockage avec le service d’objet Blob. Également, vous pouvez accorder l’accès uniquement aux ressources de blob dans un conteneur unique grâce à une signature de l’accès partagé. Pour plus d’informations sur la création de points de terminaison personnalisés, consultez [Configurer les chaînes de connexion stockage Azure](storage-configure-connection-string.md).

1. Pour enregistrer les modifications de chaîne de connexion, cliquez sur le bouton **OK** et cliquez sur le bouton **Enregistrer** dans la barre d’outils. Après avoir enregistré ces modifications, vous pouvez obtenir la valeur de cette chaîne de connexion dans votre code à l’aide de [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Lorsque vous publiez votre application Azure, choisissez la configuration du service qui contient le compte de stockage Azure pour la chaîne de connexion. Après la publication de votre application, vérifiez que l’application fonctionne comme prévu sur les services de stockage Azure

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la publication d’applications vers Azure à partir de Visual Studio, consultez la [publication d’un Service Cloud à l’aide d’outils Azure](vs-azure-tools-publishing-a-cloud-service.md).
