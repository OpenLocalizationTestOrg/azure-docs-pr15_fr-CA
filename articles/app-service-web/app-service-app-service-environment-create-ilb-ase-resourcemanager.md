<properties 
    pageTitle="Comment créer un équipement de direction auxiliaire ILB à l’aide de modèles de gestionnaire de ressources Azure | Microsoft Azure" 
    description="Apprenez à créer un ASE équilibreur de charge interne à l’aide des modèles du Gestionnaire de ressources Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Comment créer un équipement de direction auxiliaire ILB à l’aide de modèles de gestionnaire de ressources Azure

## <a name="overview"></a>Vue d’ensemble ##
Environnements de Service d’application peuvent être créés avec une adresse de réseau virtuel interne au lieu d’une adresse IP virtuelle publique.  Cette adresse interne est fournie par un composant Azure appelé l’équilibreur de charge interne (ILB).  ASE ILB peuvent être créé via le portail Azure.  Il peut également être créé à l’aide d’automation par le Gestionnaire de ressources Azure modèles.  Cet article décrit les étapes et la syntaxe permettant de créer des ASE ILB avec des modèles d’Azure le Gestionnaire de ressources.

Il existe trois étapes impliquées dans l’automatisation de la création d’un équipement de direction auxiliaire ILB :
1. Tout d’abord l’ASE de base est créé dans un réseau virtuel à l’aide d’une adresse d’équilibreur de charge interne au lieu d’une adresse IP virtuelle publique.  Dans le cadre de cette étape, un nom de domaine racine est affecté à la ILB ASE.
2. Une fois la ILB ASE est créé, un certificat SSL est téléchargé.  
3. Le certificat SSL téléchargé est explicitement affecté à la ILB ASE comme son certificat SSL de « par défaut ».  Ce certificat SSL sera utilisé pour le trafic SSL à des applications sur la ILB ASE lorsque les applications sont traitées en utilisant le domaine racine commun attribué à l’équipement de direction auxiliaire (par exemple, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Création de la Base de ILB ASE ##
Un exemple de modèle de gestionnaire de ressources Azure et son fichier de paramètres associés, sont disponibles sur GitHub [ici][quickstartilbasecreate].

La plupart des paramètres dans le fichier *azuredeploy.parameters.json* est commune à la création de ILB ASEs, ainsi que ASEs liés à une adresse IP virtuelle publique.  La liste ci-dessous appelle les paramètres out de Remarque spéciale ou qui sont uniques, lors de la création d’un équipement de direction auxiliaire ILB :


- *interalLoadBalancingMode*: dans la plupart des cas ensemble pour 3, ce qui signifie des ports de canal à la fois le trafic HTTP/HTTPS sur les ports 80/443 et les contrôle et de données écouté par le service FTP sur l’équipement de direction auxiliaire, sera lié à un ILB affecté l’adresse interne du réseau virtuel.  Si cette propriété a la valeur 2 à la place, que le service FTP liés ports (canaux de contrôle et de données) seront liés à une adresse ILB, alors que le trafic HTTP/HTTPS reste sur l’adresse VIP public.
-  *dnsSuffix*: ce paramètre définit le domaine racine par défaut qui sera affecté à l’équipement de direction auxiliaire.  Dans la variante du Service d’application Azure publique, le domaine racine par défaut pour toutes les applications web est *azurewebsites.net*.  Cependant depuis la ILB ASE est interne au réseau virtuel d’un client, il ne rend pas judicieux d’utiliser le domaine racine de la valeur par défaut du service public.  Au lieu de cela, un équipement de direction auxiliaire ILB doit avoir un domaine racine par défaut qui est logique pour une utilisation dans un réseau virtuel interne de la société.  Par exemple, un hypothétique Contoso Corporation peut utiliser un domaine de la racine par défaut de *contoso.com interne* pour les applications qui sont destinées à être uniquement résolu et accessible dans le réseau virtuel de Contoso. 
-  *ipSslAddressCount*: ce paramètre est automatiquement pris par défaut à une valeur de 0 dans le fichier *azuredeploy.json* car ILB ASEs ont uniquement une seule adresse ILB.  Aucune adresse IP à SSL explicite pour ASE ILB et, par conséquent, le pool d’adresses IP à SSL pour ASE ILB doit être défini à zéro, sinon une erreur de configuration se produira. 

Une fois le fichier *azuredeploy.parameters.json* a été renseigné pour ASE ILB, la ILB ASE peut ensuite créé à l’aide de l’extrait de code Powershell suivant.  Modifier le fichier de chemins d’accès correspondent aux où se trouvent les fichiers de modèle Azure le Gestionnaire de ressources sur votre ordinateur.  Pensez également à fournir vos propres valeurs pour le nom de déploiement d’Azure le Gestionnaire de ressources et le nom du groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le Gestionnaire de ressources Azure modèle soumis que prendra quelques heures pour la ILB ASE à créer.  Une fois la création terminée, l’équipement de direction auxiliaire ILB s’affichera dans le portail UX dans la liste des environnements de Service d’application pour l’abonnement qui a déclenché le déploiement.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Téléchargement et la configuration du certificat SSL « Par défaut » ##

Une fois la ILB ASE est créé, un certificat SSL doit être associé à l’équipement de direction auxiliaire comme « par défaut » certificat SSL utilise pour établir des connexions SSL aux applications.  Pour continuer avec l’exemple Contoso Corporation hypothétique, si l’équipement de direction auxiliaire par défaut DNS suffixe est *contoso.com interne*, puis une connexion à *https://some-random-app.internal-contoso.com* nécessite un certificat SSL valide pour **.internal-contoso.com*. 

Il existe plusieurs façons d’obtenir un certificat SSL valide, y compris des autorités de certification internes et acheter un certificat auprès d’un émetteur externe à l’aide d’un certificat auto-signé.  Quelle que soit la source du certificat SSL, les attributs de certificat suivants doivent être configurés correctement :

- *Objet*: cet attribut doit être défini sur **votre-racine-domaine-here.com*
- *Autre nom du sujet*: cet attribut doit inclure les deux * *votre-racine-domaine-here.com*, et * *.scm.your-racine-domaine-here.com*.  Pour la deuxième entrée parce que les connexions SSL sur le site SCM/Kudu associé à chaque application sont effectuées à l’aide d’une adresse de la forme *your-app-name.scm.your-root-domain-here.com*.

Avec un certificat SSL valide dans main, les deux étapes de préparation supplémentaires sont nécessaires.  Le certificat SSL doit être converti/enregistré dans un fichier .pfx.  N’oubliez pas que le fichier .pfx doit inclure tous les intermédiaires et les certificats racine, doit également être sécurisé avec un mot de passe.

Le fichier .pfx qui en résulte doit ensuite être converti en une chaîne en base 64 car le certificat SSL est téléchargé à l’aide d’un modèle de gestionnaire de ressources Azure.  Dans la mesure où le Gestionnaire de ressources Azure modèles sont des fichiers texte, le fichier .pfx doit être converti en une chaîne encodée base64 afin de pouvoir être incluse en tant que paramètre du modèle.

L’extrait de code Powershell ci-dessous montre un exemple de génération d’un certificat auto-signé, exportez le certificat sous la forme d’un fichier .pfx, convertissant le fichier .pfx en base64 de la chaîne codée, puis en enregistrant la base64 encodée de chaîne dans un fichier séparé.  Le codage base64 code Powershell a été adapté à partir du [Blog de Scripts Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Une fois que le certificat SSL a été généré et converti en un codée en base64 de chaîne, le modèle de gestionnaire de ressources Azure exemple sur GitHub pour [configurer le certificat SSL par défaut] [ configuringDefaultSSLCertificate] peut être utilisé.

Les paramètres dans le fichier *azuredeploy.parameters.json* sont répertoriés ci-dessous :

- *appServiceEnvironmentName*: le nom de l’équipement de direction auxiliaire ILB en cours de configuration.
- *existingAseLocation*: chaîne de texte contenant la région Azure où la ILB ASE a été déployée.  Par exemple : « Sud Central nous ».
- *pfxBlobString*: le based64 codé représentation sous forme de chaîne du fichier .pfx.  À l’aide de l’extrait de code ci-dessus, vous copiez la chaîne contenue dans « exportedcert.pfx.b64 » et collez-le dans la valeur de l’attribut *pfxBlobString* .
- *mot de passe*: mot de passe utilisé pour sécuriser le fichier .pfx.
- *certificateThumbprint*: l’empreinte du certificat.  Si vous récupérez cette valeur à partir de Powershell (par exemple, *$certificate. Empreinte* à partir de l’extrait de code précédent), vous pouvez utiliser la valeur en tant que-est.  Toutefois si vous copiez la valeur à partir de la boîte de dialogue de certificat de Windows, n’oubliez pas de supprimer les espaces superflus.  Le *certificateThumbprint* doit ressembler à : AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: un identificateur de chaîne convivial de votre choix permet d’identifier le certificat.  Le nom est utilisé en tant que partie de l’identificateur du Gestionnaire de ressources Azure unique pour l’entité *Microsoft.Web/certificates* qui représente le certificat SSL.  Le nom **doit** se termine par le suffixe suivant : \_yourASENameHere_InternalLoadBalancingASE.  Ce suffixe est utilisé par le portail comme un indicateur que le certificat est utilisé pour sécuriser un ASE compatible ILB.


Voici un exemple abrégé de *azuredeploy.parameters.json* :


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Une fois le fichier *azuredeploy.parameters.json* a été renseigné, le certificat SSL par défaut peut être configuré à l’aide de l’extrait de code Powershell suivant.  Modifier le fichier de chemins d’accès correspondent aux où se trouvent les fichiers de modèle Azure le Gestionnaire de ressources sur votre ordinateur.  Pensez également à fournir vos propres valeurs pour le nom de déploiement d’Azure le Gestionnaire de ressources et le nom du groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le modèle de gestionnaire de ressources Azure envoyé que prendra environ quarante minutes minutes par ASE frontal pour appliquer la modification.  Par exemple, avec l’équipement de direction auxiliaire par défaut de la taille à l’aide de deux serveurs frontaux, le modèle prendra environ 1 heure et 20 minutes pour terminer.  L’équipement de direction auxiliaire ne sera pas capable de mise à l’échelle pendant que le modèle est en cours d’exécution.  

Une fois le modèle terminé, des applications sur la ILB ASE sont accessibles via le protocole HTTPS et les connexions sont sécurisées à l’aide du certificat SSL par défaut.  Le certificat SSL par défaut sera utilisé lorsque les applications sur la ILB ASE sont traitées à l’aide d’une combinaison du nom de l’application ainsi que le nom d’hôte par défaut.  Par exemple *https://mycustomapp.internal-contoso.com* utilisez le certificat SSL par défaut pour **.internal-contoso.com*.

Toutefois, à l’instar des applications en cours d’exécution sur le service public de plusieurs utilisateurs, les développeurs peuvent également configurer les noms d’hôte personnalisé pour des applications individuelles et puis configurer les liaisons de certificat SNI SSL uniques pour les applications individuelles.  


## <a name="getting-started"></a>Mise en route

Pour vous familiariser avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)

Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
