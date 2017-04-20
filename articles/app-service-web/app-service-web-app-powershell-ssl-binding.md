<properties
    pageTitle="Liaison de certificats SSL à l’aide de PowerShell"
    description="Découvrez comment lier les certificats SSL à votre application web à l’aide de PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure Application Service SSL liaison de certificat à l’aide de PowerShell #

Avec le lancement de Microsoft Azure PowerShell version 1.1.0 une applet de commande Nouveau a été ajoutée qui devrait donner à l’utilisateur la possibilité de lier des certificats SSL nouveau ou existants à une application Web existante.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Pour en savoir plus sur l’utilisation de gestionnaire de ressources Azure basé Azure PowerShell, les applets de commande pour gérer vos applications Web vérifier [Azure le Gestionnaire de ressources en fonction des commandes PowerShell pour Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Chargement et la liaison d’un nouveau certificat SSL ##

Scénario : L’utilisateur souhaite lier un certificat SSL à l’une de ses applications web.

Connaître le nom du groupe de ressources qui contient l’application web, le nom de l’application web, le chemin du fichier .pfx certificat sur l’ordinateur de l’utilisateur, le mot de passe pour le certificat et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour créer cette liaison SSL :

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Notez qu’avant d’ajouter une liaison SSL pour une application web, vous devez disposer d’un nom d’hôte (domaine personnalisé) déjà configuré. Si le nom d’hôte n’est pas configuré, puis vous obtenez une erreur « hostname » n’existe pas lors de l’exécution de New-AzureRmWebAppSSLBinding. Vous pouvez ajouter un nom d’hôte directement à partir du portail ou à l’aide de PowerShell d’Azure. L’extrait suivant de PowerShell est possible pour configurer le nom d’hôte avant d’exécuter de nouveau-AzureRmWebAppSSLBinding.   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
Il est important de comprendre que l’applet de commande Set-AzureRmWebApp remplace les noms d’hôte de l’application web. L’extrait de code PowerShell ci-dessus est donc l’ajout à la liste existante des noms d’hôte de l’application web.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Chargement et la liaison d’un certificat SSL existant ##

Scénario : L’utilisateur souhaite lier un certificat SSL précédemment téléchargé à l’une de ses applications web.

Nous pouvons obtenir la liste des certificats déjà téléchargés à un groupe de ressources spécifique à l’aide de la commande suivante

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Notez que les certificats sont à un emplacement spécifique et un groupe de ressources, l’utilisateur doit télécharger à nouveau le certificat si l’application web configurée est dans un emplacement différent et le groupe de ressources autres que celle du certificat requis 

Connaître le nom du groupe de ressources contenant l’application web, le nom de l’application web, l’empreinte de certificat et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour créer cette liaison SSL :

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Suppression d’une liaison SSL existante  ##

Scénario : L’utilisateur souhaite supprimer liaison SSL existant.

Connaître le nom du groupe de ressources contenant l’application web, le nom de l’application web et le nom d’hôte personnalisé, nous pouvons utiliser la commande PowerShell suivante pour supprimer cette liaison SSL :

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Notez que si la liaison SSL supprimée était la dernière liaison avec ce certificat dans la mesure où l’emplacement, par défaut, le certificat sera supprimé, si l’utilisateur souhaite conserver le certificat, qu'il peut utiliser l’option DeleteCertificate à conserver le certificat

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Références ###
- [Le Gestionnaire de ressources Azure en fonction des commandes PowerShell pour Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)
- [À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)
