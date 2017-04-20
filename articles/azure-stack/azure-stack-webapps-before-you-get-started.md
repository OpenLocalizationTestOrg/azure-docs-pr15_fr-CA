<properties
    pageTitle="Aperçu technique du Service d’application Azure pile 1 avant de commencer | Microsoft Azure"
    description="Étapes à effectuer avant de déployer des applications Web sur la pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>Avant de commencer avec Azure pile Web Apps

> [AZURE.NOTE] Les informations suivantes ne s’applique qu’aux déploiements de TP1 de pile Azure.

Vous aurez besoin de quelques éléments à installer des applications Web de pile Azure :

- Un déploiement [d’Azure pile Technical Preview 1](azure-stack-run-powershell-script.md)
- Suffisamment d’espace dans un petit déploiement d’Azure pile Web Apps sur votre système pile d’Azure.  L’espace requis est d’environ 20 Go d’espace disque.
- [Un serveur qui exécute SQL Server](#SQL-Server).

>[AZURE.NOTE] Les étapes suivantes que prendre place sur l’ordinateur virtuel du Client.

## <a name="before-you-deploy-azure-stack-web-apps"></a>Avant de déployer des applications Web de pile Azure

Pour déployer un fournisseur de ressources, vous devez exécuter la Environment(ISE) PowerShell Integrated Scripting en tant qu’administrateur. Pour cette raison, vous devez autoriser les cookies et JavaScript dans le profil d’Internet Explorer que vous utilisez pour vous connecter à Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Désactiver la sécurité renforcée de Internet Explorer

1.  Connectez-vous en pile d’Azure preuve du concept (POC) tant **qu’AzureStack/à l’administrateur**et puis ouvrez le **Gestionnaire de serveur**.
2.  Désactiver **La Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et les utilisateurs.
3.  Se connecter à la machine virtuelle de ClientVM.AzureStack.local en tant qu’administrateur, puis ouvrez le **Gestionnaire de serveur**.
4.  Désactiver **La Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et les utilisateurs.

## <a name="enable-cookies"></a>Activer les cookies

1.  Sélectionnez **Démarrer**, > **toutes les applications**, > **Accessoires de Windows**. Cliquez sur **Internet Explorer** > **plus** > **Exécuter en tant qu’administrateur**.
2.  Si vous y êtes invité, sélectionnez **l’utilisation recommandée de sécurité**et sélectionnez **OK**.
3.  Dans Internet Explorer, sélectionnez **Outils** (l’icône engrenage) > **Internet Options** > **Privacy** > **Avancé**.
4.  Sélectionnez **Avancé**. Assurez-vous que les deux cases d’option **Accepter** sont sélectionnés, puis sélectionnez **OK** et **OK** .
5.  Fermez Internet Explorer et redémarrez PowerShell ISE en tant qu’administrateur.

## <a name="install-the-latest-version-of-azure-powershell"></a>Installez la dernière version de PowerShell d’Azure

1.  Connectez-vous à l’ordinateur de démonstration de pile Azure en tant **qu’AzureStack/administrateur**.
2.  Utiliser la connexion Bureau à distance pour vous connecter à la machine virtuelle de ClientVM.AzureStack.local en tant qu’administrateur.
3.  Ouvrez **Le panneau de configuration** et sélectionnez **désinstaller un programme**. 
4.  Avec le bouton droit sur **Microsoft Azure PowerShell - novembre 2015** et sélectionnez **désinstaller**.
5.  Une fois la désinstallation terminée, redémarrez l’ordinateur virtuel ClientVM.AzureStack.local
6.  Téléchargez et installez la dernière [Azure PowerShell](http://aka.ms/azstackpsh).


## <a name="sql-server"></a>SQL Server

Par défaut, le programme d’installation d’applications Web de pile Azure est défini pour utiliser le SQL Server qui est installée avec le fournisseur de ressources de SQL Azure pile. Lorsque vous installez le fournisseur de ressources de SQL Server (SQL RP) Assurez-vous de que noter le nom d’utilisateur administrateur de base de données et le mot de passe. Vous devez à la fois lorsque vous installez des applications Web de pile Azure.
Remarque : Vous devez également la possibilité de déployer et d’utiliser un autre serveur à l’exécution de SQL Server. Vous pouvez choisir de l’instance de SQL Server à utiliser lorsque vous terminez les options dans le programme d’installation d’applications Web de pile Azure.
