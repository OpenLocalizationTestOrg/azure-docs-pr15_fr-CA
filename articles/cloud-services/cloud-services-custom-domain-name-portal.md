<properties
    pageTitle="Configurer un nom de domaine personnalisé dans les Services en nuage | Microsoft Azure"
    description="Découvrez comment exposer votre application Azure ou des données Internet sur un domaine personnalisé par la configuration des paramètres DNS.  Ces exemples utilisent le portail Azure."
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
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configuration d’un nom de domaine personnalisé pour un service cloud Azure

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-custom-domain-name-portal.md)
- [Azure portal classique](cloud-services-custom-domain-name.md)

Lorsque vous créez un Service en nuage, Azure assigne à un sous-domaine de **cloudapp.net**. Par exemple, si votre Service Cloud est nommé « contoso », les utilisateurs pourront accéder à votre application sur une URL telle que http://contoso.cloudapp.net. Azure assigne également une adresse IP virtuelle.

Toutefois, vous pouvez également exposer votre application sur votre propre nom de domaine, par exemple, **contoso.com**. Cet article explique comment réserver ou de configurer un nom de domaine personnalisé pour les rôles de web Service en nuage.

Avez-vous déjà undestand les enregistrements CNAME et A sont ? [Saut au-delà de l’explaination](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Les procédures décrites dans cette tâche s’applique aux Services de Cloud Azure. Pour les Services d’application, voir [ce](../app-service-web/web-sites-custom-domain-name.md). Pour les comptes de stockage, voir [ce](../storage/storage-custom-domain-name.md).

<p/>

> [AZURE.TIP]
> Commencer plus rapidement, utilisez le nouveau Azure [guidée par la procédure pas à pas](http://support.microsoft.com/kb/2990804)!  Il rend l’association d’un nom de domaine personnalisé et sécurisation des communications (SSL) avec les Services Cloud Azure Azure sites Web ou un composant logiciel enfichable.

## <a name="understand-cname-and-a-records"></a>Comprendre les enregistrements CNAME et A

CNAME (ou alias) et un enregistrements vous permettent d’associer un nom de domaine à un serveur spécifique (ou de service dans ce cas,) toutefois ils fonctionnent différemment. Il existe également quelques considérations spécifiques lors de l’utilisation des enregistrements de services Azure Cloud qui vous devez considérer avant de décider laquelle utiliser.

### <a name="cname-or-alias-record"></a>Enregistrement de nom canonique ou l’Alias

Un enregistrement CNAME associe un domaine *spécifique* , comme **contoso.com** ou **www.contoso.com**, à un nom de domaine complet. Dans ce cas, le nom de domaine canonique est le **[myapp] .cloudapp .net** application hébergée de nom de domaine de votre Azure. Une fois créé, l’enregistrement CNAME crée un alias pour le **[myapp] .cloudapp .net**. L’entrée CNAME résout l’adresse IP de votre **[myapp] .cloudapp .net** de service automatiquement, donc si l’adresse IP du service cloud change, vous n’avez pas à intervenir.

> [AZURE.NOTE]
> Certains bureaux de domaine uniquement vous permettent de mapper les sous-domaines lors de l’utilisation d’un enregistrement CNAME, par exemple www.contoso.com et non les noms de racine, par exemple, contoso.com. Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par le document [IETF de noms de domaine - implémentation et spécification](http://tools.ietf.org/html/rfc1035) , [l’entrée de Wikipedia sur l’enregistrement CNAME](http://en.wikipedia.org/wiki/CNAME_record)ou votre registraire.

### <a name="a-record"></a>Un enregistrement

Un enregistrement *A* mappe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un domaine générique* tel que ** \*. contoso.com**, à une adresse IP. Dans le cas d’un Service en nuage Azure, l’adresse IP virtuelle du service. L’avantage principal d’un enregistrement A sur un enregistrement CNAME est que vous pouvez avoir une entrée qui utilise un caractère générique, tel que \* **. contoso.com**, qui peut gérer des requêtes de plusieurs sous-domaines, par exemple **mail.contoso.com**, **login.contoso.com**ou **www.contso.com**.

> [AZURE.NOTE]
> Dans la mesure où un enregistrement A est mappé à une adresse IP statique, il ne peut pas automatiquement répercuter les modifications apportées à l’adresse IP de votre Service Cloud. L’adresse IP utilisée par votre Service Cloud est alloué à la première fois que vous déployez sur un emplacement vide (production ou intermédiaire.) Si vous supprimez le déploiement pour l’emplacement, l’adresse IP est libérée par Azure et les déploiements futurs de l’emplacement peuvent bénéficier d’une nouvelle adresse IP.
>
> Heureusement, l’adresse IP d’un emplacement de déploiement donné (production ou intermédiaire) est rendu persistant lors de l’échange entre l’intermédiaire et des déploiements de production ou une mise à niveau sur place d’un déploiement existant. Pour plus d’informations sur l’exécution de ces actions, voir [comment gérer les services en nuage](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Ajouter un enregistrement CNAME pour votre domaine personnalisé

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé en utilisant les outils fournis par votre registraire. Chaque serveur d’inscriptions a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement CNAME, mais les concepts sont identiques.

1. Utilisez une des méthodes suivantes pour rechercher la **. cloudapp.net** nom de domaine affecté à votre service cloud.

    * Connexion au [portail Azure], sélectionnez votre service cloud, examinez la section **Essentials** et recherchez l’entrée **d’URL de Site** .

        ![section coup de œil rapide montrant l’URL du site][csurl]
            
        **OU**
  
    * Installer et configurer [Azure Powershell](../powershell-install-configure.md)et puis utiliser la commande suivante :

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Enregistrer le nom de domaine utilisé dans l’URL retournée par une de ces méthodes, car vous en aurez besoin lors de la création d’un enregistrement CNAME.

1.  Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Recherchez des liens ou des zones du site étiqueté comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

2.  Maintenant rechercher dans laquelle vous pouvez sélectionner ou entrer de CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés. Vous devez rechercher les mots **CNAME**, **Alias**ou **sous-domaines**.

3.  Vous devez également fournir l’alias de domaine ou un sous-domaine pour l’enregistrement CNAME, tel que **www** , si vous souhaitez créer un alias pour **www.customdomain.com**. Si vous souhaitez créer un alias pour le domaine racine, il peut être répertorié comme le '**@**' symbole dans les outils de votre registraire DNS.

4. Ensuite, vous devez fournir un nom d’hôte complet, ce qui correspond à votre domaine d’application **cloudapp.net** dans ce cas.

Par exemple, l’enregistrement CNAME suivant transmet tout le trafic à partir de **www.contoso.com** à **contoso.cloudapp.net**, le nom de domaine personnalisé de votre application déployée :

| Nom d’alias / d’hôte/sous-domaine | Domaine canonique     |
| ------------------------- | -------------------- |
| www                       | Contoso.cloudapp.NET |

> [AZURE.NOTE]
Un visiteur de **www.contoso.com** ne verront jamais l’hôte true (contoso.cloudapp.net), afin que le processus de transfert est invisible pour l’utilisateur final.

> L’exemple ci-dessus ne s’applique qu’au trafic sur le sous-domaine **www** . Dans la mesure où vous ne pouvez pas utiliser des caractères génériques avec des enregistrements CNAME, vous devez créer un CNAME pour chaque domaine/sous-domaine. Si vous souhaitez diriger le trafic à partir de sous-domaines, tel que *. contoso.com, à l’adresse cloudapp.net, vous pouvez configurer un * *Redirection d’URL* * ou * *URL avant** entrée dans vos paramètres DNS, ou créez un enregistrement A.


## <a name="add-an-a-record-for-your-custom-domain"></a>Ajouter un enregistrement A pour votre domaine personnalisé

Pour créer un enregistrement A, vous devez tout d’abord rechercher l’adresse IP virtuelle de votre service cloud. Puis ajoutez une nouvelle entrée dans la table DNS pour votre domaine personnalisé en utilisant les outils fournis par votre registraire. Chaque serveur d’inscriptions a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement de, mais les concepts sont identiques.

1. Utilisez une des méthodes suivantes pour obtenir l’adresse IP de votre service cloud.

    * Connexion au [portail Azure], sélectionnez votre service cloud, examinez la section **Essentials** et puis recherchez l’entrée **d’adresses IP publiques** .

        ![section coup de œil rapide montrant l’adresse VIP][vip]

        **OU**

    * Installer et configurer [Azure Powershell](../powershell-install-configure.md)et puis utiliser la commande suivante :

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Enregistrer l’adresse IP, car vous en aurez besoin lors de la création d’un enregistrement de.

1.  Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Recherchez des liens ou des zones du site étiqueté comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

2.  Maintenant rechercher dans laquelle vous pouvez sélectionner ou saisir d’un enregistrement. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés.

3. Sélectionnez ou entrez le domaine ou le sous-domaine qui utilisera cet enregistrement A. Par exemple, sélectionnez **www** si vous souhaitez créer un alias pour **www.customdomain.com**. Si vous souhaitez créer une entrée de caractère générique pour tous les sous-domaines, entrez «__*__». Il couvre tous les sous-domaines, par exemple **mail.customdomain.com**, **login.customdomain.com**et **www.customdomain.com**.

    Si vous souhaitez créer un enregistrement A pour le domaine racine, il peut être répertorié comme le '**@**' symbole dans les outils de votre registraire DNS.

4. Entrez l’adresse IP de votre service cloud dans le champ fourni. Cela associe l’entrée de domaine utilisée dans l’enregistrement A avec l’adresse IP du déploiement de votre service cloud.

Par exemple, le suivant Qu'un enregistrement transmet tout le trafic provenant de **contoso.com** à **137.135.70.239**, l’adresse IP de l’application déployée :

| Nom d’hôte/sous-domaine | Adresse IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |


Cet exemple illustre la création d’un enregistrement A pour le domaine racine. Si vous souhaitez créer une entrée de caractère générique pour couvrir tous les sous-domaines, vous devez entrer «__*__» que le sous-domaine.

>[AZURE.WARNING]
>Les adresses IP dans Azure sont dynamiques par défaut. Vous souhaiterez probablement utiliser une [adresse IP réservée](../virtual-network/virtual-networks-reserved-public-ip.md) pour vous assurer que votre adresse IP ne change pas.

## <a name="next-steps"></a>Étapes suivantes

* [La gestion des Services en nuage](cloud-services-how-to-manage.md)
* [Comment mapper le contenu CDN pour un domaine personnalisé](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuration générale de votre service cloud](cloud-services-how-to-configure-portal.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 