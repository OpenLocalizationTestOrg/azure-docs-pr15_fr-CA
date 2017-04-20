<properties
   pageTitle="Publication d’un Service Cloud à l’aide d’outils Azure | Microsoft Azure"
   description="Découvrez comment publier des projets de service cloud Azure à l’aide de Visual Studio."
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

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publication d’un Service Cloud à l’aide d’outils Azure

En utilisant les outils Azure pour Visual Studio de Microsoft, vous pouvez publier votre application Azure directement à partir de Visual Studio. Visual Studio prend en charge la publication intégrée à la mise en place ou à l’environnement de Production d’un service cloud.

Avant de publier une application Azure, vous devez posséder un abonnement Azure. Vous devez également définir un compte de service et stockage cloud pour être utilisé par votre application. Vous pouvez les définir à l' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Lorsque vous publiez, vous pouvez sélectionner l’environnement de déploiement pour votre service cloud. Vous devez également sélectionner un compte de stockage qui est utilisé pour stocker le package d’application pour le déploiement. Après le déploiement, le package d’application est supprimé du compte de stockage.

Lorsque vous développez et testez une application Azure, vous pouvez utiliser le déploiement Web pour publier des modifications de façon incrémentielle pour vos rôles web. Une fois que vous publiez votre application dans un environnement de déploiement, déploiement Web vous permet de déployer des modifications directement à l’ordinateur virtuel qui exécute le rôle web. Vous ne disposez pas du package et la publication de votre application Azure entière chaque fois que vous souhaitez mettre à jour votre rôle web pour tester les modifications. Avec cette approche, vous pouvez avoir vos modifications de rôle web disponibles dans le nuage pour le test sans attendre votre application publiée dans un environnement de déploiement.

Utilisez les procédures suivantes pour publier votre application Azure et mettre à jour un rôle web à l’aide de déploiement Web :

- Publier ou empaqueter une application Azure à partir de Visual Studio

- Mise à jour d’un rôle web dans le cadre du cycle de test et développement

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publier ou empaqueter une Application Azure à partir de Visual Studio

Lorsque vous publiez votre application Azure, vous pouvez effectuer l’une des tâches suivantes :

- Créer un package de service : vous pouvez utiliser ce package et le fichier de configuration du service pour publier votre application dans un environnement de déploiement à partir du [Portail classique d’Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publier votre projet Azure à partir de Visual Studio : pour publier votre application directement sur Azure, vous utilisez l’Assistant Publication. Pour plus d’informations, consultez [Assistant Publication d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Pour créer un package de service à partir de Visual Studio

1. Lorsque vous êtes prêt à publier votre application, ouvrez l’Explorateur de solutions, ouvrez le menu contextuel pour le projet qui contient vos rôles d’Azure et choisissez Publier.

1. Pour créer un package de service uniquement, procédez comme suit :  

  1. Dans le menu contextuel pour le projet d’Azure, choisissez le **Package**.

  1. Dans la boîte de dialogue **Package Azure Application** , choisissez la configuration du service pour lequel vous souhaitez créer un package et puis cliquez sur la configuration de build.

  1. (facultatif) Pour activer le Bureau à distance pour le service en nuage après que l’avoir publiée, activez la case à cocher **Activer le Bureau à distance pour tous les rôles** et sélectionnez **paramètres** pour configurer le Bureau à distance. Si vous souhaitez déboguer votre service cloud après que l’avoir publiée, activer le débogage distant en sélectionnant **Activer le débogueur distant pour tous les rôles**.

      Pour plus d’informations, consultez [L’aide de bureau à distance avec les rôles d’Azure](vs-azure-tools-remote-desktop-roles.md).

  1. Pour créer le package, cliquez sur **le lien** .

      Explorateur de fichiers indique l’emplacement du fichier du package nouvellement créé. Vous pouvez copier cet emplacement afin que vous puissiez l’utiliser du [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Pour publier ce package dans un environnement de déploiement, vous devez utiliser cet emplacement comme emplacement de Package lorsque vous créez un service en nuage et déployez ce package à un environnement avec [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facultatif) Pour annuler le processus de déploiement, dans le menu contextuel pour l’élément de ligne dans le journal d’activité, cliquez sur **Annuler et supprimer**. Cela arrête le processus de déploiement et le supprime de l’environnement de déploiement d’Azure.

    >[AZURE.NOTE] Pour supprimer cet environnement de déploiement après que qu’il a été déployé, vous devez utiliser le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facultatif) Une fois vos instances de rôles ont démarré, Visual Studio affiche automatiquement l’environnement de déploiement dans le nœud des **Services en nuage** dans l’Explorateur de serveurs. À partir de là, vous pouvez voir l’état des instances de chaque rôle. Consultez [Azure de gestion des ressources avec le Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). L’illustration suivante montre les instances de rôle, pendant qu’ils sont toujours à l’état de l’initialisation :

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Mise à jour d’un rôle Web dans le cadre du Cycle de test et développement

Si l’infrastructure de serveur principal de votre application est stable, mais les rôles web doivent souvent plus de mises à jour, vous pouvez utiliser le déploiement Web pour mettre à jour uniquement un rôle web dans votre projet. Cela est pratique lorsque vous ne souhaitez pas régénérer et redéployer les rôles de travail principal, ou si vous avez plusieurs rôles web et que vous souhaitez mettre à jour uniquement un des rôles web.

### <a name="requirements"></a>Configuration requise

Voici la configuration requise pour utiliser le déploiement Web mise à jour de votre rôle web :

- **De développement et de test à des fins uniquement :** Les modifications sont apportées directement à l’ordinateur virtuel où le rôle web est en cours d’exécution. Si cet ordinateur virtuel doit être recyclé, ces modifications sont perdues car le package d’origine que vous avez publiée est utilisé pour recréer l’ordinateur virtuel pour le rôle. Vous devez le republier votre application pour obtenir les dernières modifications apportées au rôle web.

- **Uniquement les rôles web peuvent être mis à jour :** Rôles de travail ne peut pas être mis à jour. En outre, vous ne peut pas mettre à jour le RoleEntryPoint de web role.cs.

- **Peut prendre uniquement en charge une seule instance d’un rôle web :** Impossible d’avoir plusieurs instances de n’importe quel rôle web dans votre environnement de déploiement. Toutefois, plusieurs rôles web chaque qu’une seule instance sont pris en charge.

- **Vous devez activer les connexions Bureau à distance :** Ceci est nécessaire pour que le déploiement Web peuvent utiliser l’utilisateur et le mot de passe pour vous connecter à l’ordinateur virtuel pour déployer les modifications sur le serveur qui exécute Internet Information Services (IIS). En outre, vous devrez peut-être vous connecter à l’ordinateur virtuel pour ajouter un certificat sécurisé à IIS sur cet ordinateur virtuel. (Ceci garantit que la connexion à distance pour IIS qui est utilisé par le déploiement Web est sécurisée).

La procédure suivante suppose que vous utilisez l’Assistant de **Publication des applications Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Pour activer le déploiement Web lorsque vous publiez votre Application.

1. Pour activer l' **Activer le déploiement Web** pour la case à cocher de toutes les web rôles, vous devez d’abord configurer les connexions Bureau à distance. Sélectionnez **Activer le Bureau à distance** pour tous les rôles et ensuite fournir les informations d’identification qui seront utilisées pour se connecter à distance dans la zone de **Configuration du Bureau à distance** s’affiche. Pour plus d’informations, consultez [L’aide de bureau à distance avec les rôles d’Azure](vs-azure-tools-remote-desktop-roles.md) .

1. Pour activer le déploiement Web pour tous les rôles dans votre application web, sélectionnez **Activer le déploiement Web pour tous les rôles de web**.

    Un triangle d’avertissement jaune s’affiche. Déploiement de Web utilise un certificat non approuvé et auto-signé par défaut, ce qui n’est pas recommandé pour le transfert de données sensibles. Si vous avez besoin sécuriser ce processus pour les données sensibles, vous pouvez ajouter un certificat SSL à utiliser pour les connexions de déploiement Web. Ce certificat doit être un certificat approuvé. Pour plus d’informations sur la procédure à suivre, consultez la section **Pour rendre Web déploiement sécurisé** plus loin dans cette rubrique.

1. Cliquez sur **suivant** pour afficher l’écran de **Résumé** et puis cliquez sur **Publier** pour déployer le service cloud.

    Le service en nuage est publié. L’ordinateur virtuel créé a des connexions à distance activées pour IIS, afin que le déploiement Web peut être utilisé pour mettre à jour vos rôles web sans les republier.

    >[AZURE.NOTE] Si vous avez plus d’une instance configurée pour un rôle web, un message d’avertissement s’affiche, indiquant que chaque rôle web seront limité à une seule instance uniquement dans le package est créé pour la publication de votre application. Cliquez sur **OK** pour continuer. Comme indiqué dans la section Configuration requise, vous pouvez avoir plus d’un rôle web mais qu’une seule instance de chaque rôle.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Pour mettre à jour votre rôle Web à l’aide de déploiement Web

1. Pour utiliser le déploiement Web, apportez les modifications du code au projet pour un de vos rôles web dans Visual Studio que vous souhaitez publier, puis cliquez sur ce nœud de projet dans votre solution et pointez sur **Publier**. La boîte de dialogue **Publier le site Web** s’affiche.

1. (Facultatif) Si vous avez ajouté un certificat SSL à utiliser pour les connexions à distance pour IIS, vous pouvez désactiver la case à cocher **Autoriser les certificats non approuvés** . Pour plus d’informations sur l’ajout d’un certificat pour sécuriser le déploiement Web, consultez la section **Pour rendre Web déploiement sécurisé** plus loin dans cette rubrique.

1. Pour utiliser le Web Deploy, le mécanisme de publication doit être le nom d’utilisateur et le mot de passe que vous définissez pour votre connexion Bureau à distance lorsque vous tout d’abord publié le package.

  1. Dans la zone **nom d’utilisateur**, entrez le nom d’utilisateur.

  1. Dans **mot de passe**, entrez le mot de passe.

  1. (Facultatif) Si vous souhaitez enregistrer ce mot de passe de ce profil, cliquez sur **Enregistrer le mot de passe**.

1. Pour publier les modifications apportées à votre rôle web, cliquez sur **Publier**.

    La ligne affiche le **démarrage de la publication**. Une fois la publication terminée, **publication réussie** s’affiche. Les modifications ont maintenant été déployées sur le rôle du web sur votre ordinateur virtuel. Vous pouvez maintenant commencer votre application Azure dans l’environnement Azure pour tester vos modifications.

### <a name="to-make-web-deploy-secure"></a>Pour faire du déploiement Web sécurisé

1. Déploiement de Web utilise un certificat non approuvé et auto-signé par défaut, ce qui n’est pas recommandé pour le transfert de données sensibles. Si vous avez besoin sécuriser ce processus pour les données sensibles, vous pouvez ajouter un certificat SSL à utiliser pour les connexions de déploiement Web. Ce certificat doit être un certificat approuvé, ce que vous obtenez d’une autorité de certification (CA).

    Pour sécuriser le déploiement Web pour chaque machine virtuelle pour chacun des rôles web, vous devez télécharger le certificat de confiance que vous souhaitez utiliser pour le web déployer sur [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). Cela permet de s’assurer que le certificat est ajouté à la machine virtuelle qui est créée pour le rôle web lorsque vous publiez votre application.

1. Pour ajouter un certificat SSL à utiliser pour les connexions à distance d’IIS, procédez comme suit :

  1. Pour vous connecter à l’ordinateur virtuel qui exécute le rôle web, sélectionnez l’instance du rôle web de **Cloud Explorer** ou **l’Explorateur de serveurs**et puis choisissez la commande **se connecter à l’aide du Bureau à distance** . Pour obtenir la procédure détaillée sur la façon de se connecter à la machine virtuelle, reportez-vous [à l’aide de bureau à distance avec les rôles d’Azure](vs-azure-tools-remote-desktop-roles.md).

      Votre navigateur vous invite à télécharger un. Fichier RDP.

  1. Pour ajouter un certificat SSL, ouvrez le service Gestion des services dans le Gestionnaire des services IIS. Dans le Gestionnaire des services IIS, activez SSL en ouvrant le lien de **liaisons** dans le volet **actions** . La boîte de dialogue **Ajouter une liaison de Site** s’affiche. Cliquez sur **Ajouter**, puis sélectionnez HTTPS dans la liste déroulante **Type** . Dans la liste du **certificat SSL** , cliquez sur le certificat SSL que vous aviez signé par une autorité de certification et que vous avez téléchargé au [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). Pour plus d’informations, consultez [Configurer les paramètres de connexion pour le Service de gestion](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Si vous ajoutez un certificat SSL fiable, le triangle d’avertissement jaune n’apparaît plus dans l' **Assistant Publication**.

## <a name="include-files-in-the-service-package"></a>Inclure des fichiers dans le Package de Service

Vous devrez peut-être inclure des fichiers spécifiques dans votre package de services afin qu’ils soient disponibles sur l’ordinateur virtuel qui est créé pour un rôle. Par exemple, vous pouvez souhaiter ajouter un .exe ou un fichier .msi qui est utilisé par un script de démarrage pour votre package de services. Ou bien, vous devrez peut-être ajouter un assembly qui nécessite d’un projet de rôle web rôle ou de travail. Pour inclure des fichiers, ils doivent être ajoutés à la solution pour votre application Azure.

### <a name="to-include-files-in-the-service-package"></a>Pour inclure des fichiers dans le package de service

1. Pour ajouter un assembly à un package de service, procédez comme suit :

  1. Dans **L’Explorateur de solutions** , ouvrez le nœud du projet pour le projet auquel il manque l’assembly référencé.

  1. Pour ajouter l’assembly au projet, ouvrez le menu contextuel pour le dossier **références** et puis cliquez sur **Ajouter une référence**. La boîte de dialogue Ajouter une référence s’affiche.

  1. Choisissez la référence que vous souhaitez ajouter et cliquez sur le bouton **OK** .

      La référence est ajoutée à la liste sous le dossier **références** .

  1. Ouvrir le menu contextuel pour l’assembly que vous avez ajouté et cliquez sur **Propriétés**. La fenêtre **Propriétés** s’affiche.

      Pour inclure cet assembly dans le package de service, dans la **liste de copie locale** choisissez **True**.

1. Dans **L’Explorateur de solutions** , ouvrez le nœud du projet pour le projet auquel il manque l’assembly référencé.

1. Pour ajouter l’assembly au projet, ouvrez le menu contextuel pour le dossier **références** et puis cliquez sur **Ajouter une référence**. La boîte de dialogue **Ajouter une référence** s’affiche.

1. Choisissez la référence que vous souhaitez ajouter et cliquez sur le bouton **OK** .

    La référence est ajoutée à la liste sous le dossier **références** .

1. Ouvrir le menu contextuel pour l’assembly que vous avez ajouté et cliquez sur **Propriétés**. La fenêtre Propriétés s’affiche.

1. Pour inclure cet assembly dans le package de service, dans la liste de **Copie locale** , choisissez **True**.

1. Pour inclure des fichiers dans le package de service qui ont été ajoutés à votre projet de rôle web, ouvrez le menu contextuel pour le fichier et puis cliquez sur **Propriétés**. Dans la fenêtre **Propriétés** , cliquez sur **contenu** dans la liste **Action de génération** .

1. Pour inclure des fichiers dans le package de service qui ont été ajoutés à votre projet de rôle de travail, ouvrez le menu contextuel pour le fichier et puis cliquez sur **Propriétés**. Dans la fenêtre **Propriétés** , choisissez **Copier si plus récent** dans la liste **Copier dans le répertoire de sortie** .

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la publication pour Azure à partir de Visual Studio, reportez-vous à la section [Publier Assistant Création d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).
