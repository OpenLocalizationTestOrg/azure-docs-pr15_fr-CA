<properties 
    pageTitle="Utilisez ReportViewer dans un Site Web | Microsoft Azure"
    description="Cette rubrique décrit comment créer un site Web de Microsoft Azure avec le contrôle ReportViewer de Visual Studio qui affiche un rapport stocké sur une Machine virtuelle de Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Utilisez ReportViewer dans un Site Web hébergé dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Vous pouvez créer un site Web de Microsoft Azure avec le contrôle ReportViewer de Visual Studio qui affiche un rapport stocké sur une Machine virtuelle de Microsoft Azure. Le contrôle ReportViewer est dans une application Web que vous créez en utilisant le modèle d’application Web d’ASP.NET.

>[AZURE.IMPORTANT] Les modèles d’Application Web de ASP.NET MVC ne gèrent pas le contrôle ReportViewer.

Pour intégrer ReportViewer dans votre site Web de Microsoft Azure, vous devez effectuer les tâches suivantes.

- **Ajouter** Le Package de déploiement des assemblys

- **Configurer** Authentification et autorisation

- **Publier** l’application Web d’ASP.NET vers Azure

## <a name="prerequisites"></a>Conditions préalables

Consultez la section « recommandation générale et meilleures pratiques » dans [SQL Server Business Intelligence dans Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Les contrôles ReportViewer sont livrés avec Visual Studio, Édition Standard ou version ultérieure. Si vous utilisez le Web Developer Express Edition, vous devez installer le [RUNTIME de MICROSOFT État observateur 2012](https://www.microsoft.com/download/details.aspx?id=35747) pour utiliser les fonctionnalités d’exécution de ReportViewer.
>
>ReportViewer configuré en mode traitement local n’est pas pris en charge dans Microsoft Azure.

Consultez le livre blanc [contrôle Visionneuse de rapports Reporting Services et de la machine virtuelle de Microsoft Azure base de serveurs de rapports](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Ajout d’assemblys au Package de déploiement

Lorsque vous hébergez votre ASP.NET application sur site, les assemblys de ReportViewer sont généralement installés directement dans le global assembly cache (GAC) du serveur IIS pendant l’installation de Visual Studio et sont accessibles directement par l’application. Toutefois, lorsque vous hébergez votre application ASP.NET dans le cloud, Microsoft Azure ne permet pas à être installé dans le GAC, vous devez vous assurer que les assemblys de ReportViewer sont disponibles localement dans votre application. Vous pouvez pour ce faire, l’ajout de références à ceux-ci dans votre projet et les configurer pour être copié localement.

En mode de traitement distant, le contrôle ReportViewer utilise les assemblys suivants :

- **Microsoft.ReportViewer.WebForms.dll**: contient le code de ReportViewer, vous devez utiliser ReportViewer dans votre page. Une référence de cet assembly est ajoutée à votre projet lorsque vous faites glisser un contrôle ReportViewer sur une page ASP.NET dans votre projet.

- **Microsoft.ReportViewer.Common.dll**: contient des classes utilisées par le contrôle ReportViewer au moment de l’exécution. Il n’est pas automatiquement ajouté à votre projet.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Pour ajouter une référence à Microsoft.ReportViewer.Common

- Cliquez droit sur le nœud **références** de votre projet et sélectionnez **Ajouter une référence**, sélectionnez l’assembly dans l’onglet .NET et cliquez sur **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Pour rendre les assemblys localement accessible par votre application ASP.NET

1. Dans le dossier **références** , cliquez sur l’assembly Microsoft.ReportViewer.Common afin que ses propriétés s’affichent dans le volet Propriétés.

1. Dans le volet de propriétés, définissez la **Copie locale** sur True.

1. Répétez les étapes 1 et 2 pour Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Pour obtenir le Pack de langue ReportViewer

1. Installez le package redistribuable Microsoft État observateur 2012 Runtime approprié à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Sélectionnez la langue dans la liste déroulante et que la page est redirigée vers la page du centre de téléchargement correspondant.

1. Cliquez sur **Télécharger** pour démarrer le téléchargement de ReportViewerLP.exe.

1. Après avoir téléchargé ReportViewerLP.exe, cliquez sur **exécuter** pour installer immédiatement, ou cliquez sur **Enregistrer** pour l’enregistrer sur votre ordinateur. Si vous cliquez sur **Enregistrer**, n’oubliez pas le nom du dossier où vous enregistrez le fichier.

1. Recherchez le dossier où vous avez enregistré le fichier. Droit ReportViewerLP.exe et cliquez sur **Exécuter en tant qu’administrateur**, puis cliquez sur **Oui**.

1. Après l’exécution de ReportViewerLP.exe, vous verrez le c:\windows\assembly a la ressource des fichiers **Microsoft.ReportViewer.Webforms.Resources** et **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Pour configurer pour localiser le contrôle ReportViewer

1. Téléchargez et installez le package redistribuable du Runtime de Microsoft État observateur 2012 en suivant les instructions spécifiées ci-dessus.

1. Créer <language> dossier dans le projet et la copie de l’assembly de ressource associés fichiers il. Les fichiers d’assembly de ressources à copier sont : **Microsoft.ReportViewer.Webforms.Resources.dll** et **Microsoft.ReportViewer.Common.Resources.dll**. Sélectionner les fichiers d’assembly de ressources et dans le volet de propriétés, définissez le **Copier dans le répertoire de sortie** pour «**toujours**».

1. Définir la Culture et UICulture pour le projet web. Pour plus d’informations sur la définition de la Culture et la Culture d’interface utilisateur pour une page ASP.NET Web, reportez-vous à la section [Comment : définir la Culture et la Culture d’interface utilisateur pour la globalisation des Web Page ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configuration d’authentification et autorisation

ReportViewer doit utiliser les informations d’identification adéquates pour s’authentifier auprès du serveur de rapports, et les informations d’identification doivent être autorisées par le serveur de rapports pour accéder aux rapports que vous souhaitez. Pour plus d’informations sur l’authentification, consultez le livre blanc [contrôle Visionneuse de rapports Reporting Services et de la machine virtuelle de Microsoft Azure base de serveurs de rapports](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publier l’application Web d’ASP.NET vers Azure

Pour obtenir des instructions sur la publication d’une application Web d’ASP.NET sur Azure, consultez [Comment : migrer et publier une Application Web vers Azure à partir de Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) et [mise en route avec ASP.NET et les applications Web](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Si la commande Ajouter un projet de déploiement Azure ou ajouter un projet Azure Cloud Service n’apparaît pas dans le menu contextuel de l’Explorateur de solutions, vous devrez modifier le framework cible du projet sur.NET Framework 4.
>
>Les deux commandes fournissent essentiellement les mêmes fonctionnalités. Un ou l’autre commande apparaîtra dans le menu contextuel selon la version du Kit de développement Microsoft Azure, vous avez installé.

## <a name="resources"></a>Ressources

[Rapports Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence dans des Machines virtuelles Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Utilisation de PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en Mode natif](virtual-machines-windows-classic-ps-sql-report.md)

[Rapports de contrôle de visionneuse de rapports de Services et de Microsoft Azure machine virtuelle basée serveurs de rapports](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
