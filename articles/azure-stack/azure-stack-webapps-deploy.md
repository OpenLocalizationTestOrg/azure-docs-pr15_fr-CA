<properties
    pageTitle="Ajouter un fournisseur de ressources d’applications Web à pile Azure | Microsoft Azure"
    description="Instructions détaillées pour déployer des applications Web dans une pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
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

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Ajouter un fournisseur de ressources Web Apps pour pile d’Azure

> [AZURE.NOTE] Les informations suivantes ne s’applique qu’aux déploiements de TP1 de pile Azure.

Ajout d’un fournisseur de ressources d’applications Web pour Azure pile a sept étapes :

1.  Télécharger les composants requis.
2.  Créer des certificats à utiliser par les applications Web de pile Azure.
3.  Utilisez le programme d’installation pour télécharger, scène et installer des applications Web de pile Azure. 
4.  Valider l’Installation d’applications Web.
5.  Créer des enregistrements DNS pour le Front-End et les équilibreurs de charge de serveur d’administration.
6.  Enregistrer le fournisseur de ressources d’applications Web nouvellement déployé avec ARM.
7.  Testez le fournisseur de ressources d’applications Web.

## <a name="download-required-components"></a>Télécharger les composants requis

1.  Téléchargez [le programme d’installation de Service d’application Azure pile aperçu](http://aka.ms/azasinstaller). 
2.  Télécharger les [scripts de services d’application Azure pile déploiement d’assistance](http://aka.ms/azashelper). 
3.  Extraire les fichiers à partir du fichier zip de scripts d’assistance, il doit y avoir trois scripts :
    - Créer-AppServiceCerts.ps1
    - Créer-AppServiceDnsRecords.ps1
    - Registre-AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Créer des certificats à utiliser par les applications Web de pile Azure

Ce script premier fonctionne avec l’autorité de certification de pile d’Azure pour créer des certificats de 3 qui sont nécessaires aux applications Web. Exécutez le script sur le ClientVM assurer que vous exécutez PowerShell en tant qu’azurestack\administrator :
1.  Dans une session PowerShell est en cours d’exécution en tant que **azurestack\administrator**, exécutez le script de **Création-AppServiceCerts.ps1** .  Ceci crée trois certificats, dans le même dossier que le script, qui sont nécessaires aux applications Web.
2.  Entrez un mot de passe pour sécuriser les fichiers pfx et prenez note de ce que vous devez entrer dans le programme d’installation Web Apps pour Azure pile.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Le programme d’installation permet de télécharger et d’installer des applications Web de pile Azure

Le programme d’installation de appservice.exe est :
1.  Invite l’utilisateur à accepter les tiers CLUF Microsoft.
2.  Collecter des informations de déploiement de pile d’Azure.
3.  Créer un conteneur d’objet blob dans le compte de stockage Azure pile spécifié.
4.  Télécharger les fichiers nécessaires pour installer le fournisseur de ressources Azure pile Web App.
5.  Préparer l’installation pour déployer le fournisseur de ressources d’application Web dans l’environnement de la pile d’Azure.
6.  Téléchargez les fichiers dans le compte de Service de l’application de stockage.
7.  Présenter les informations nécessaires pour amorcer le modèle du Gestionnaire de ressources Azure.

La procédure suivante vous guide à travers les étapes de l’installation :

>[AZURE.NOTE] Vous devez utiliser un compte avec des privilèges élevés (administrateur local ou de domaine) pour exécuter le programme d’installation. Si vous vous connectez en tant qu’azurestack\azurestackuser, le système vous demandera des informations d’identification avec des privilèges élevés. 

1.  Exécutez appservice.exe en tant que **azurestack\administrator**. 
2.  Cliquez sur **déployer à l’aide de gestionnaire de ressources Azure**.

![Le programme d’installation de pile Azure Application Service Technical Preview 1][1]

3.  Passez en revue et accepter le contrat de licence Microsoft version préliminaire et puis cliquez sur **suivant**.
4.  Passer en revue et accepter les termes de partylicense tiers et puis cliquez sur **suivant**.
5.  Passez en revue les informations de configuration d’application Cloud Service et cliquez sur **suivant**.

![Configuration de pile Azure Application Service Technical Preview 1 application Service Cloud][2]

6. Cliquez sur **se connecter** (en regard de la zone abonnements de pile Azure).

![Écran de Configuration Cloud pour bêta 1 application Service pile Azure Application Service deux][3]

7.  Dans la fenêtre authentification de pile Azure fournir votre **compte Azure Active Directory Service Admin** et le **mot de passe**, puis cliquez sur **Se connecter**.
**Remarque :** C’est le compte Azure Active Directory que vous avez fournies lorsque vous avez déployé la pile d’Azure.
    - Cliquez sur la **Flèche bas** située à droite de la case en regard des **Abonnements de pile d’Azure** , puis sélectionnez votre abonnement.

![Sélection d’abonnement Azure pile App Service Technical Preview 1][5]

8.  Cliquez sur la **Flèche bas** située à droite de la case en regard **d’Emplacements de pile d’Azure**.
    - Sélectionnez **Local**.
9. Entrez le **nom** de votre administrateur.
10. Entrez un **mot de passe** de l’administrateur.
11. Examinez les **Détails de SQL Server** et apporter des modifications si nécessaire.
12. Examinez le **Compte de connexion SysAdmin** et apporter des modifications si nécessaire.
13. Entrez le **Mot de passe SysAdmin**.
14. Cliquez sur **suivant**.  Le programme d’installation va vérifier à ce stade les détails de connexion pour SQL Server fourni.

![Sélection d’abonnement Azure pile App Service Technical Preview 1][4]    

15. Cliquez sur **Parcourir** en regard du **Fichier de certificat SSL par défaut les applications Web** et naviguer dans les applications Web **. AzureStack.Local** de certificat [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Entrez le **mot de passe de certificat** que vous avez défini lors de la création de certificats.
17. Cliquez sur **Parcourir** en regard du **Fichier de certificat SSL ressources fournisseur** et accédez à la gestion de **. AzureStack.Local** de certificat [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Entrez le **mot de passe de certificat** que vous avez défini lors de la création de certificats.
19. Cliquez sur **Parcourir** en regard du **Fichier de certificat de racine de fournisseur de ressource** et accédez à la gestion de **. AzureStack.Local** de certificat [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Cliquez sur **suivant** le programme d’installation vérifie le mot de passe de certificat fourni.

![Détails du certificat pile Azure Application Service Technical Preview 1][6]

Le déploiement va prendre environ 45-60 minutes.

![Progression de l’Installation bêta 1 pile Azure Application Service][7]

21. Une fois le programme d’installation est terminée, cliquez sur **Quitter**.

## <a name="validate-web-apps-installation"></a>Valider l’Installation d’applications Web

1.  Sur votre **Machine hôte de pile Azure** ouvrir le **Gestionnaire Hyper-V**.
2.  Recherchez la **CN0-VM** et **se connecter** à la machine virtuelle.
![Gestionnaire Hyper-V de pile Azure Application Service Technical Preview 1][8]
3.  Sur le bureau de cet ordinateur virtuel, double-cliquez sur la **Console de gestion Web Cloud**.
![Console de gestion de pile Azure Application Service Technical Preview 1][9]
4.  Accédez à **serveurs gérés**.
5.  Lorsque tous les ordinateurs sont **prêts** passez à l’étape suivante. 
![État de pile Azure Application Service Aperçu technique 1 serveurs gérés][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Créer des enregistrements DNS pour le serveur d’administration et les équilibreurs de charge des Front-End
1.  Ouvrez une instance de PowerShell en tant que **azurestack\administrator**.
2.  Accédez à l’emplacement des scripts téléchargé et extrait dans l' [étape](#Download-Required-Components).
3.  Exécutez le script de **Création-AppServiceDnsRecords.ps1** , cela crée des entrées DNS pour activer les demandes d’application web et de portail être acheminés vers les serveurs frontaux.  Lors du déploiement des applications Web, les équilibreurs de charge deux logiciels (SLBs), ARM ont été créés dans le fournisseur de ressources réseau. Ils pointent vers les serveurs d’administration et les serveurs frontaux. Le portail et les demandes ARM en fonction du fournisseur de ressources de Service d’application Azure pile atteindre le serveur d’administration.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Enregistrer le fournisseur d’applications Web de pile Azure nouvellement déployé avec ARM
1.  Ouvrez une instance de PowerShell en tant que **azurestack\administrator**.
2.  Accédez à l’emplacement des scripts téléchargé et extrait dans l' [étape](#Download-Required-Components).
3.  Exécutez le script de **Registre-AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Tapez le nom d’utilisateur et le mot de passe **exactement (y compris les cas supérieure et inférieure)** comme il a été entré pour **l’Administrateur d’ordinateurs virtuels** et les champs de **mot de passe** de l’installation ou votre enregistrement de fournisseur de ressources échoue.

## <a name="test-drive-azure-stack-web-apps"></a>Test lecteur pile Azure Web Apps

Maintenant que vous avez déployés et inscrit le fournisseur de ressources d’applications Web, vous pouvez la tester pour vous assurer que les locataires peuvent déployer des applications web.

1.  Dans le portail de la pile d’Azure, cliquez sur Nouveau, cliquez sur Web + Mobile, cliquez sur application Web.
2.  De la lame d’application Web, tapez un nom dans la zone d’application Web.
3.  Sous le groupe de ressources, cliquez sur Nouveau, puis tapez un nom dans la zone de groupe de ressources. 
4.  Cliquez sur plan/emplacement du Service de l’application et cliquez sur Créer nouveau.
5.  Dans la lame de plan de Service de l’application, tapez un nom dans la zone de plan de Service de l’application.
6.  Cliquez sur couche de tarification et cliquez sur libre-Shared ou non partagé-partagé, cliquez sur Sélectionner, cliquez sur OK, puis cliquez sur Créer.
7.  En moins d’une minute, une mosaïque pour la nouvelle application web apparaîtra sur le tableau de bord. Cliquez sur la mosaïque.
8.  De la lame d’application web, cliquez sur Parcourir pour afficher le site Web par défaut pour cette application.


**Déployer un site Web WordPress, DNN ou Django (facultatif)**

1. Dans le portail de la pile d’Azure, cliquez sur « + », allez sur le marché d’Azure, déployer un site Web de Django et attendre la réussite. La plate-forme de web Django utilise une base de données de système de fichiers et ne nécessite pas les fournisseurs de ressources supplémentaires tels que SQL ou de MySQL.  

2. Si vous avez déployé également un fournisseur de ressources MySQL, vous pouvez déployer un site Web de WordPress depuis le site Marketplace. Lorsque vous êtes invité à taper des paramètres de base de données, entrez le nom d’utilisateur en tant que *User1@Server1* (avec le nom d’utilisateur et le nom du serveur de votre choix).

3. Si vous avez déployé également un fournisseur de ressources de SQL Server, vous pouvez déployer un site Web DNN à partir du marché. Lorsque vous êtes invité à taper des paramètres de base de données, sélectionnez une base de données dans l’ordinateur exécutant SQL Server qui est connecté à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également essayer autre [plate-forme en tant que service (PaaS) services](azure-stack-tools-paas-services.md), tels que le [fournisseur de ressources SQL Server](azure-stack-sql-rp-deploy-short.md) et le [fournisseur de ressources de MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
