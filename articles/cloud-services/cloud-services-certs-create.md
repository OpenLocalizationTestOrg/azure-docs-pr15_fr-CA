<properties 
    pageTitle="Les certificats de gestion et les Services en nuage | Microsoft Azure" 
    description="Découvrez comment créer et utiliser des certificats avec Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Vue d’ensemble des certificats pour les Services en nuage Azure
Les certificats sont utilisés dans Azure pour les services en nuage ([certificats de service](#what-are-service-certificates)) et pour l’authentification à l’aide de l’API ([certificats de gestion](#what-are-management-certificates) lorsque vous utilisez l’Azure portal classique et pas ARM) de gestion. Cette rubrique donne une vue d’ensemble les deux types de certificats, comment [créer](#create) et [déployer](#deploy) vers Azure.

Certificats utilisés dans Azure sont de type x.509 v3 certificats et peuvent être signés par un autre certificat de confiance, ou ils peuvent être autosignés. Un certificat auto-signé est signé par son propre créateur et de ce fait, ne sont pas approuvés par défaut. La plupart des navigateurs peuvent ignorer. Les certificats auto-signés doivent uniquement être utilisés par vous-même lors du développement et de test de vos services de cloud. 

Certificats utilisés par Azure peuvent contenir une privée ou une clé publique. Les certificats ont une empreinte numérique qui permet de les identifier de façon non ambiguë. Cette empreinte numérique permettant d’identifier le certificat à un service en nuage doit utiliser dans le [fichier de configuration](cloud-services-configure-ssl-certificate.md) d' Azure. 

## <a name="what-are-service-certificates"></a>Que sont les certificats de service ?
Certificats de service sont attachés à des services en nuage et de sécuriser la communication vers et à partir du service. Par exemple, si vous avez déployé un rôle web, vous pouvez fournir un certificat qui peut authentifier un point de terminaison HTTPS exposé. Les certificats de service, définies dans la définition de service, sont automatiquement déployés sur l’ordinateur virtuel qui exécute une instance de votre rôle. 

Vous pouvez télécharger les certificats de service pour Azure portal classique à l’aide du portail classique Azure, ou à l’aide de l’API de gestion de Service. Certificats de service sont associés à un service en nuage spécifique et affectés à un déploiement dans le fichier de définition de service.

Les certificats de service peuvent être gérées séparément de vos services et peuvent être gérés par des personnes différentes. Par exemple, un développeur peut télécharger un package de service qui fait référence à un certificat un directeur informatique a précédemment téléchargé dans Azure. Un directeur informatique peut gérer et renouveler ce certificat, la modification de la configuration du service sans avoir à télécharger un nouveau package de service. Ceci est possible parce que le nom logique pour le certificat et son nom de magasin et son emplacement sont spécifiés dans le fichier de définition de service, alors que l’empreinte de certificat est spécifié dans le fichier de configuration de service. Pour mettre à jour le certificat, il n’est plus nécessaire de télécharger un nouveau certificat et modifiez la valeur de l’empreinte numérique dans le fichier de configuration de service.

## <a name="what-are-management-certificates"></a>Que sont les certificats de gestion ?
Certificats de gestion permettent de s’authentifier avec l’API de gestion de Service fournie par Azure classique. De nombreux programmes et des outils (tels que Visual Studio ou le Kit de développement Azure) utilise ces certificats pour automatiser la configuration et le déploiement des divers services Azure. Ces ne sont pas vraiment liés aux services en nuage. 

>[AZURE.WARNING] Fais attention ! Ces types de certificats permettent à tout utilisateur qui s’authentifie avec eux pour gérer l’abonnement qu'auquel elles sont associées. 

### <a name="limitations"></a>Limitations
Il existe une limite de 100 certificats de gestion par abonnement. Il existe également une limite de 100 certificats de gestion pour tous les abonnements sous l’ID utilisateur de. l’administrateur d’un service spécifique Si l’ID utilisateur de l’administrateur du compte a déjà été utilisé pour ajouter les certificats de gestion de 100 et il existe un besoin de plus de certificats, vous pouvez ajouter un administrateur de collaboration pour ajouter les certificats supplémentaires. 

Avant d’ajouter plus de 100 certificats, voir si vous pouvez réutiliser un certificat existant. À l’aide des administrateurs de collaboration complique potentiellement inutiles à votre processus de gestion des certificats.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Créer un nouveau certificat auto-signé
Vous pouvez utiliser n’importe quel outil disponible pour créer un certificat auto-signé dans la mesure où ils adhèrent à ces paramètres :

* Un certificat X.509.
* Contient une clé privée.
* Créé pour l’échange de clés (fichier .pfx).
* Nom du sujet doit correspondre le domaine utilisé pour accéder au service de cloud. 
    > Vous ne pouvez pas acquérir un domaine de certificat pour le cloudapp.net (ou tout Azure liés) SSL ; nom du sujet du certificat doit correspondre au nom de domaine personnalisé qu’utilisé pour accéder à votre application. Par exemple, **contoso.net**, pas **contoso.cloudapp.net**.
* Minimum de cryptage 2048 bits.
* **Certificat de service uniquement**: certificat côté Client doit se trouver dans le magasin de certificats *personnel* .

Il existe deux méthodes pour créer un certificat dans Windows, avec la `makecert.exe` utilitaire ou IIS.

### <a name="makecertexe"></a>Makecert.exe

Cet utilitaire a été désapprouvé et n’est pas documenté ici. Consultez [cet article MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968) pour plus d’informations.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Si vous souhaitez utiliser le certificat avec une adresse IP au lieu d’un domaine, utilisez l’adresse IP dans le paramètre - NomDNS.


Si vous souhaitez utiliser ce [certificat avec le portail de gestion](../azure-api-management-certs.md), de l’exporter vers un fichier **.cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Il existe de nombreuses pages sur internet qui expliquent comment effectuer cette opération avec IIS. [Ici](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) est un excellent j’ai trouvé que je pense qu’il explique bien. 

### <a name="java"></a>Java
Vous pouvez utiliser Java pour [créer un certificat](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[Cet](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) article décrit comment créer des certificats avec SSH.

## <a name="next-steps"></a>Étapes suivantes

[Télécharger votre certificat de service sur le portail classique Azure](cloud-services-configure-ssl-certificate.md) (ou l' [Azure portal](cloud-services-configure-ssl-certificate-portal.md)).

Télécharger un [certificat de gestion de API](../azure-api-management-certs.md) sur le portail de classique Azure.

>[AZURE.NOTE] Le portail Azure n’utilise pas de certificats de gestion pour accéder à l’API, mais utilise à la place des comptes d’utilisateurs.
