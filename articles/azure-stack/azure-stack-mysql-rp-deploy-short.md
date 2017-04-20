<properties
    pageTitle="Utiliser les bases de données MySQL comme PaaS sur Azure pile | Microsoft Azure"
    description="Comprendre les étapes à suivre pour déployer le fournisseur de ressources de MySQL et de fournir de MySQL en tant que service sur Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Utiliser les bases de données MySQL comme PaaS sur Azure pile

> [AZURE.NOTE] Les informations suivantes ne s’applique qu’aux déploiements de TP1 de pile Azure.

Vous pouvez déployer un fournisseur de ressources MySQL sur Azure pile. Une fois que vous déployez le fournisseur de ressources, vous pouvez créer MySQL serveurs et bases de données par le biais de modèles de déploiement d’Azure le Gestionnaire de ressources et fournissent des bases de données MySQL en tant que service. Bases de données MySQL, qui sont courants sur les sites web, prise en charge de nombreuses plates-formes de site Web. Une fois que vous déployez le fournisseur de ressources, vous pouvez créer des sites Web de WordPress à partir de la plateforme Azure Web Apps sous la forme d’un module complémentaire de service (PaaS) pour Azure pile.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Étapes à suivre pour déployer le fournisseur de ressources
Suivez ces étapes si vous êtes déjà familiarisé avec la pile d’Azure. Si vous souhaitez plus d’informations, suivez les liens de chaque section ou cliquez directement sur [l’adaptateur de fournisseur de ressources de base de données MySQL sur Azure pile POC de déployer](azure-stack-mysql-rp-deploy-long.md).

1.  Assurez-vous que vous [effectuez toutes les étapes de paramétrage](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) avant de déployer le fournisseur de ressources :

    - .NET framework 3.5 est déjà défini dans l’image de base Windows Server. (Si vous avez téléchargé les bits de la pile d’Azure après février, 23, 2016, vous pouvez ignorer cette étape.)
    - [Une version de PowerShell Azure qui est compatible avec la pile d’Azure est installée](http://aka.ms/azStackPsh).
    - Dans paramètres de sécurité Internet Explorer sur le ClientVM, [Internet Explorer, sécurité renforcée est désactivée et les cookies sont activés](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Téléchargez le fichier de fichiers binaires de fournisseur de ressources MySQL](http://aka.ms/masmysqlrp) et extrayez-le sur le ClientVM dans votre pile Azure preuve du concept (PoC).

3. [Exécuter les bootstrap.cmd et les scripts](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Un ensemble de scripts qui est regroupé en deux onglets principaux s’ouvre dans l’environnement de script intégré PowerShell (ISE). Exécuter tous les scripts chargés dans l’ordre, de gauche à droite dans chaque onglet.

    1. Exécuter des scripts dans l’onglet de **préparation** à partir de la gauche vers la droite pour :

        - Créer un certificat générique pour sécuriser les communications entre le fournisseur de ressources et le Gestionnaire de ressources Azure.
        - Acceptez les termes du CLUF de MySQL et de télécharger les fichiers binaires de MySQL.
        - Télécharger les certificats et les autres artefacts à un compte de stockage Azure pile.
        - Publication des packages de la galerie afin que vous pouvez déployer les ressources de MySQL par le biais de la galerie.

        > [AZURE.IMPORTANT] Si un des scripts se bloque sans raison apparente après avoir soumis votre locataire Azure Active Directory, vos paramètres de sécurité empêche peut-être une DLL qui est requis pour le déploiement à exécuter. Pour résoudre ce problème, recherchez le Microsoft.AzureStack.Deployment.Telemetry.Dll dans votre dossier de fournisseur de ressources avec le bouton droit dessus et cliquez sur **Propriétés**, puis vérifiez les **Débloquer** dans l’onglet **Général** .

    2. Exécuter des scripts dans l’onglet de **déployer** à partir de la gauche vers la droite pour :

        - [Déployer une machine virtuelle (VM)](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) qui hébergera votre fournisseur de ressources, MySQL serveurs et bases de données que vous allez instancier. Ce script fait référence à un fichier de paramètres JSON, vous devez mettre à jour avec des valeurs avant d’exécuter le script.
        - [Enregistrer un enregistrement DNS local](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) correspondant à votre ordinateur virtuel de fournisseur de ressources.
        - [Inscription de votre fournisseur de ressources](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) avec le Gestionnaire de ressources local Azure.

        > [AZURE.IMPORTANT] Tous les scripts supposent que l’image du système d’exploitation remplit les conditions préalables (.NET 3.5 est installé, JavaScript et des cookies sur le ClientVM et la version la plus récente de PowerShell Azure installé). Si vous obtenez des erreurs lorsque vous exécutez les scripts, vérifiez que vous les conditions préalables remplies.

5. Pour [tester votre nouveau fournisseur de ressources MySQL](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment), déployer une base de données MySQL à partir du portail de la pile d’Azure. Cliquez sur **créer un** &gt; **Custom** &gt; **serveur MySQL et base de données**.

Il doit obtenir de votre fournisseur de ressources MySQL des et en cours d’exécution en 25 minutes environ.
