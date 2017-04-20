<properties
    pageTitle="Déployez le fournisseur de ressources MySQL sur Azure pile | Microsoft Azure"
    description="Étapes détaillées pour déployer le fournisseur de ressources MySQL pour pile d’Azure."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>Déployez le fournisseur de ressources MySQL sur pile Azure à utiliser avec les applications Web

> [AZURE.NOTE] Les informations suivantes ne s’applique qu’aux déploiements de TP1 de pile Azure.

Utilisation de cet article afin de suivre les étapes détaillées de configuration de fournisseur de ressources MySQL sur la pile d’Azure la preuve de concept (POC) de sorte que vous pouvez démarrer [à l’aide de bases de données MySQL](azure-stack-mysql-rp-deploy-short.md) sur Azure pile, y compris à l’aide de MySQL en tant que le serveur principal pour les sites WordPress créé avec [Azure Web Apps](azure-stack-webapps-deploy.md).

## <a name="set-up-steps-before-you-deploy"></a>Paramétrer des étapes avant de déployer

Avant de déployer le fournisseur de ressources, vous devez :

- Une image de Windows Server par défaut avec .NET 3.5
- Désactiver la sécurité renforcée d’Internet Explorer (IE)
- Installez la dernière version de PowerShell d’Azure

### <a name="create-an-image-of-windows-server-including-net-35"></a>Créer une image de Windows Server, y compris .NET 3.5

Vous pouvez ignorer cette étape si vous avez téléchargé les bits de la pile d’Azure après 2/23/2016, car l’image par défaut de base Windows Server 2012 R2 inclut le .NET framework 3.5 dans ce téléchargement et versions ultérieures.

Si vous avez téléchargé avant 2/23/2016, vous devez créer un disque dur virtuel de Windows Server 2012 R2 Datacenter avec .NET 3.5 image et jeu est par défaut dans le référentiel Image de plate-forme.

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>Désactiver Internet Explorer enhanced security et activer les cookies

Pour déployer un fournisseur de ressources, vous exécutez l’environnement de script intégré PowerShell (ISE) en tant qu’administrateur, vous devez autoriser les cookies et JavaScript dans le profil d’Internet Explorer que vous permet de vous connecter à Active Directory de Azure pour connexions administrateur et d’utilisateur.

**Pour désactiver Internet Explorer enhanced security :**

1. Connectez-vous en Azure pile preuve de concept (PoC) tant qu’un AzureStack/administrateur et puis ouvrez le Gestionnaire de serveur.

2. Désactiver **La Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et les utilisateurs.

3. Se connecter à la machine virtuelle de **ClientVM.AzureStack.local** en tant qu’administrateur, puis ouvrez le Gestionnaire de serveur.

4. Désactiver **La Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et les utilisateurs.

**Pour activer les cookies :**

1. Sur l’écran de démarrage de Windows, cliquez sur **toutes les applications**et cliquez sur **Accessoires de Windows**, cliquez sur **Internet Explorer**, pointez sur **autres**, puis sélectionnez **Exécuter en tant qu’administrateur**.

2. Si vous y êtes invité, vérifiez **l’utilisation recommandée de sécurité**, puis cliquez sur **OK**.

3. Dans Internet Explorer, cliquez sur l' **icône Outils (engrenages)** &gt; **options Internet** &gt; onglet **confidentialité** .

4. Cliquez sur **Options avancées**vous assurer que les deux boutons **Accepter** sont sélectionnés et cliquez sur **OK**, puis cliquez à nouveau sur **OK** .

5. Fermez Internet Explorer et redémarrez PowerShell ISE en tant qu’administrateur.

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>Installer une version compatible de la pile d’Azure de PowerShell d’Azure

1. Désinstallez tout PowerShell Azure existante à partir de votre ordinateur virtuel du Client.

2. Connectez-vous à l’ordinateur de démonstration de pile Azure en tant qu’AzureStack/administrateur.

3. À l’aide du Bureau à distance, connectez-vous à l’ordinateur virtuel de **ClientVM.AzureStack.local** en tant qu’administrateur.

4. Ouvrez le panneau de configuration, cliquez sur **désinstaller un programme** &gt; sur **Azure PowerShell** &gt; cliquez sur **désinstaller**.

5. [Téléchargez la dernière PowerShell Azure qui prend en charge la pile d’Azure](http://aka.ms/azstackpsh) et l’installer.

    Après l’installation de PowerShell, vous pouvez exécuter cette vérification script PowerShell pour vous assurer que vous pouvez vous connecter à votre instance Azure pile (une page web de connexion doit apparaître).

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>Le déploiement de fournisseur de ressources PowerShell des données d’amorçage

1. Connexion Bureau à distance Azure pile VT à clientVm.AzureStack.Local et connectez-vous en tant qu’azurestack\\azurestackuser.

2. [Télécharger les fichiers binaires de MySQL RP](http://aka.ms/masmysqlrp) de fichier et l’extraire vers D:\\MySQLRP.

3. Exécutez le lecteur D:\\MySQLRP\\fichier de Bootstrap.cmd en tant qu’administrateur (azurestack\administrator).

    Le fichier Bootstrap.ps1 s’ouvre dans PowerShell ISE.

4. Lorsque windows PowerShell ISE termine le chargement, cliquez sur le bouton « lecture » ou appuyez sur F5.

    Deux onglets principaux se charge, chacun contenant tous les scripts et les fichiers nécessaires au déploiement de votre fournisseur de ressources de MySQL.

## <a name="prepare-prerequisites"></a>Préparer les conditions préalables

Cliquez sur l’onglet de **Préparer les conditions préalables** pour :

- Créer des certificats requis
- Télécharger les fichiers binaires de MySQL à votre pile Azure
- Télécharger des artefacts à un compte de stockage sur la pile d’Azure
- Publier les éléments de la galerie

### <a name="create-the-required-certificates"></a>Créer les certificats requis
Ce script de **Nouveau-SslCert.ps1** ajoute la \_. Certificat SSL de AzureStack.local.pfx sur le lecteur D:\\MySQLRP\\conditions préalables\\BlobStorage\\dossier conteneur. Le certificat permet de sécuriser la communication entre le fournisseur de ressources et de l’instance locale du Gestionnaire de ressources Azure.

1. Dans l’onglet principal de **Préparer les conditions préalables** , cliquez sur l’onglet **Nouveau-SslCert.ps1** et exécutez-le.

2. Dans l’invite qui s’affiche, tapez un mot de passe PFX qui protège la clé privée et **Prenez note de ce mot de passe**. Vous en aurez besoin ultérieurement.

### <a name="download-mysql-binaries-to-your-azure-stack"></a>Télécharger les fichiers binaires de MySQL à votre pile Azure

1. Sélectionnez l’onglet **Téléchargement-MySqlServer.ps1** et exécutez-le.
2. Lorsque vous y êtes invité, cliquez sur **Oui** dans la boîte de dialogue de confirmation pour accepter le CLUF.

    Cette commande ajoute deux fichiers zip dans le dossier D:\MySql\Prerequisites\BlobStorage\Container.

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>Télécharger tous les artefacts sur un compte de stockage dans la pile d’Azure

1. Cliquez sur l’onglet **Téléchargement-Microsoft.MySql-RP.ps1** et exécutez-le.

2. Dans la boîte de dialogue demande de d’informations d’identification Windows PowerShell, tapez les informations d’identification administrateur de pile d’Azure service.

3. Lorsque vous y êtes invité pour l’ID de client Azure Active Directory, tapez votre nom de domaine complet du locataire Azure Active Directory : par exemple, microsoftazurestack.onmicrosoft.com.

    Une fenêtre contextuelle vous demande des informations d’identification.

    > [AZURE.TIP] Si la fenêtre contextuelle ne s’affiche pas, vous que soit n’ont pas désactivé IE amélioré la sécurité afin d’activer JavaScript sur cet ordinateur et d’utilisateur, ou vous n’avez pas accepté les cookies dans Internet Explorer. Reportez-vous à la section [configurer les étapes avant de déployer](#set-up-steps-before-you-deploy).

4. Tapez vos informations d’identification d’administrateur de Service Azure pile et puis cliquez sur **Connexion**.

### <a name="publish-gallery-items-for-later-resource-creation"></a>Publier les éléments de la galerie pour la création ultérieure de la ressource

Sélectionnez l’onglet **Publier-GalleryPackages.ps1** et exécutez-le. Ce script ajoute deux éléments de marché pour le marché du portail POC de pile Azure que vous pouvez utiliser pour déployer des ressources de base de données en tant qu’éléments du marché.

## <a name="deploy-the-mysql-resource-provider-vm"></a>Déployez le fournisseur de ressources de MySQL VM

Maintenant que vous avez préparé le PoC de pile Azure avec les certificats nécessaires et les éléments de la marketplace, vous pouvez déployer un fournisseur de ressources de SQL Server. Cliquez sur l’onglet **fournisseur de déployer de MySQL** pour :

   - Fournir des valeurs dans un fichier JSON qui fait référence à la procédure de déploiement
   - Déployez le fournisseur de ressources
   - Mise à jour du DNS local
   - Enregistrer la carte de fournisseur de ressources de SQL Server

### <a name="provide-values-in-the-json-file"></a>Fournir des valeurs dans le fichier JSON

Cliquez sur **Microsoft.MySqlprovider.Parameters.JSON**. Ce fichier comprend les paramètres que le modèle de gestionnaire de ressources Azure doit déployer correctement à la pile d’Azure.

1. Renseignez les paramètres **vide** dans le fichier JSON :

    - Assurez-vous que vous spécifiez **adminusername** et **adminpassword** de la machine virtuelle de MySQL ressource fournisseur.

    - Vérifiez que vous fournissez le mot de passe pour le paramètre **SetupPfxPassword** que vous avez apportées à une note de dans l’étape de [composants requis de préparation](#prepare-prerequisites) .

    - Vérifiez que vous fournissez les paramètres **basicAuthUserName** et **basicAuthPassword** . **Prenez note de ces valeurs.** Vous aurez besoin ultérieurement pour enregistrer le fournisseur de ressources.

2. Cliquez sur **Enregistrer**.

### <a name="deploy-the-resource-provider"></a>Déployez le fournisseur de ressources

1. Cliquez sur l’onglet **déploiement-Microsoft.Mysql-provider.PS1** et exécutez le script.
2. Dans Azure Active Directory lorsque vous y êtes invité, tapez le nom de votre client.
3. Dans la fenêtre contextuelle, soumettre vos informations d’identification de pile d’Azure service admin.

Le déploiement complet peut prendre entre 15 et 45 minutes sur certains validation des conceptions pile Azure fortement sollicités.

### <a name="update-the-local-dns"></a>Mise à jour du DNS local

1. Cliquez sur l’onglet **historique-Microsoft.MySQL-fqdn.ps1** et exécutez le script.
2. Lorsque vous y êtes invité pour Azure des ID de clients Active Directory, entrez votre nom de domaine complet du locataire Azure Active Directory : par exemple, **microsoftazurestack.onmicrosoft.com**.

### <a name="register-the-sql-rp-resource-provider"></a>Enregistrer le fournisseur de ressources RP de SQL

1. Cliquez sur l’onglet **historique-Microsoft.My-provider.ps1** et exécutez le script.

2. À l’invite d’informations d’identification, utilisez ce que vous avez noté que les paramètres **basicAuthUserName** et **basicAuthPassword** .

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail de pile Azure

1. Se déconnecter de la ClientVM et reconnectez-vous en tant que **AzureStack\User**.

2. Sur le bureau, cliquez sur **Portail POC de pile Azure** et vous connecter au portail en tant que l’administrateur du service.

3. Vérifiez que le déploiement a réussi. Cliquez sur **Parcourir** &gt; **Groupes de ressources**, cliquez sur le groupe de ressources que vous avez utilisé (valeur par défaut est **MySQLRP**), puis assurez-vous que la partie de l’éducation de la lame (moitié supérieure) lit le **déploiement a réussi**.


4. Vérifiez que l’inscription a réussi. Cliquez sur **Parcourir** &gt; **les fournisseurs de ressources**, puis recherchez les **MySQL Local**.

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Créer votre première base de données MySQL pour tester votre déploiement

1. Se connecter au portail Azure pile POC en tant qu’administrateur du service.

2. Cliquez sur le **+** bouton &gt; **Custom** &gt; **serveur MySQL et bases de données**.

3. Renseignez le formulaire avec des détails de la base de données.

    **Prenez note de la vous entrez « server name ».** La chaîne de connexion pour votre base de données comprend le nom du serveur « » dans le cadre du nom d’utilisateur : par exemple, ** "user@ <ServerName>»**. Vous devez entrer un nom d’utilisateur dans ce format lorsque vous vous connectez à la base de données : par exemple, lorsque vous déployez un site web de MySQL à l’aide du fournisseur de ressources Azure Web Site


## <a name="next-steps"></a>Étapes suivantes

Essayez d’autres [services de PaaS](azure-stack-tools-paas-services.md) comme le [fournisseur de ressources SQL Server](azure-stack-sql-rp-deploy-short.md) et le [fournisseur de ressources d’applications Web](azure-stack-webapps-deploy.md).
