
<properties 
    pageTitle="Options de migration de RemoteApp d’Azure | Microsoft Azure" 
    description="Obtenir des informations sur les options de migration de RemoteApp d’Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Options de migration de Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Si vous avez cessé d’utiliser RemoteApp d’Azure en raison de l' [annonce de la retraite](https://go.microsoft.com/fwlink/?linkid=821148) ou parce que vous avez terminé votre évaluation, vous devez migrer Azure RemoteApp à un autre service de l’application. Il existe deux approches différentes pour la migration : un autogéré (souvent appelés Infrastructure en tant que Service [IaaS]) déploiement ou une entièrement gérée (souvent appelée plate-forme en tant que Service) ou des logiciels en tant que Service [PaaS et de solutions SaaS] offre. 

IaaS de libre-service est un déploiement personnalisables qui est managé, géré et dont vous êtes propriétaire, directement déployé sur les machines virtuelles (VM) ou des systèmes physiques. À l’autre extrémité, une entièrement géré PaaS/offre SaaS est ressemble davantage à Azure RemoteApp - un partenaire fournit une couche de service sur une solution d’accès distant qui gère les opérations et maintenance, pendant que vous, en tant que client, faire des opérations image et l’application de gestion.

Continuez la lecture pour plus d’informations, y compris des exemples de différentes options d’hébergement.    

## <a name="self-managed-iaas-solutions"></a>Solutions (IaaS) gérées automatiquement

### <a name="rds-on-iaas"></a>**RDS sur IaaS** 
Vous pouvez déployer un natif session des Services Bureau à distance (dans Windows Server) déploiement à l’aide de RemoteApp ou postes de travail sur site ou dans un environnement hébergé (comme sur Azure VM). RDS sur les déploiements IaaS sont meilleures pour les clients déjà familiers et qui possèdent des compétences techniques existant avec les déploiements de RDS. 

> [AZURE.NOTE]
> Vous avez besoin de licences avec Software Assurance (SA) pour les licences d’accès client RDS pour utiliser cette option de déploiement.

Déploiement de RDS sur Azure VM est plus facile que jamais, lorsque vous utilisez le déploiement et la correction des modèles (lire une [vue d’ensemble](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) , puis [aller les chercher](https://aka.ms/rdautomation)). Vous pouvez obtenir les mêmes fonctionnalités de mise à l’échelle élastiques avec les ressources de modèle de déploiement d’Azure classique (pas les ressources de modèle de ressource d’Azure) dans Azure RemoteApp à l’aide de [mise à l’échelle le script](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), bien qu’il n’y a plus de personnalisations et de configurations. Lorsque vous déployez RDS sur Azure VM, est prise par l’intermédiaire de [La prise en charge de Azure](https://azure.microsoft.com/support/plans/), les techniciens du support technique même pris en charge vous avec Azure RemoteApp. Vous pouvez obtenir des estimations de coût en fonction de votre utilisation d’existante en contactant [Le Support Azure](https://azure.microsoft.com/support/plans/), ou vous pouvez effectuer des calculs vous-même à l’aide un prochain Calculateur des coûts.  En outre, avec les machines virtuelles de N-series (actuellement en aperçu privé), vous pouvez ajouter vGPU - entendre plus sur l’ajout de vGPU et sur la façon [d’exploiter les améliorations de RDS dans Windows Server 2016](https://myignite.microsoft.com/videos/2794) dans notre session de Ignite.   

Nous avons des guides de déploiement de l’étape par étape pour [Windows Server 2012 R2](http://aka.ms/rdsonazure) et [Windows Server 2016](http://aka.ms/rdsonazure2016) pour vous aider à votre déploiement. Consultez le [blog de bureau à distance](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) pour les dernières nouvelles.
 
### <a name="citrix-on-iaas"></a>**Citrix sur IaaS** 
Un Citrix natif déploiement de XenApp de basé sur session ou XenDesktop peut être déployé sur site ou dans un environnement hébergé (tels que sur Azure VM). 

Consultez le guide de déploiement étape par étape, [7.6 XA de Citrix sur Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), pour plus d’informations. Apprenez-en plus sur [Citrix sur Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), y compris une calculatrice de prix. Vous trouverez également un [contact de Citrix](http://citrix.com/English/contact/index.asp) pour discuter avec vos options de.

## <a name="fully-managed-paassaas-offerings"></a>Offres (PaaS et de solutions SaaS) entièrement gérés

### <a name="citrix-cloud"></a>**Nuage de Citrix** 
[Solution de cloud Citrix existante](https://www.citrix.com/products/citrix-cloud/)comme point de vue architectural Express de Citrix XenApp. Citrix offre une [promotion de remise de 50 %](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) pour les clients existants Azure RemoteApp. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (dans l’aperçu technique)**
[Inscrivez-vous à leur Aperçu technique](http://now.citrix.com/remoteapp)et observez sa [session de Ignite](https://myignite.microsoft.com/videos/2792) (commençant par minute 20:30). XenApp Express est point de vue architectural identique à nuage de Citrix, mais il inclut l’interface utilisateur de gestion simplifiée et d’autres fonctionnalités et capacités qui sont semblables aux Azure RemoteApp. 

Plus d’informations sur [Express de Citrix XenApp](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programme de fournisseur de services de Citrix** 
Le programme de fournisseur de Service de Citrix facilite pour offrir la simplicité du virtuel cloud computing pour PME/PMI, les fournisseurs de leur proposant les services de que leur choix dans un modèle simple et de paiement. Les fournisseurs de services de Citrix développer leur activité Microsoft SPLA et développer leurs investissements de plate-forme RDS avec n’importe quel périphérique, accès à distance, la prise en charge d’application plus large, expérience riche, sécurité accrue et une meilleure évolutivité. À son tour, les fournisseurs de services de Citrix attirer plus d’abonnés, augmenter la satisfaction de la clientèle et réduire leurs coûts d’exploitation. [En savoir plus](http://www.citrix.com/products/service-providers.html) ou [trouver un partenaire](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Fournisseur de Service hébergé de Microsoft** 
Partenaires d’hébergement offrent en général entièrement géré hébergé de bureau Windows et service d’application, qui peut inclure la gestion Azure ressources, systèmes d’exploitation, les applications et assistance à l’aide du partenaire du Gestionnaire de licences des accords avec Microsoft et d’autres fournisseurs de logiciels et en cours d’un contrat de licence de fournisseur de Service afin de permettre la revente de la licence d’accès abonné (SAL). Les informations suivantes fournissent des détails et des informations de contact pour certains les hébergeurs spécialisés dans l’assistance des clients avec leur migration Azure RemoteApp. Consultez [la liste des fournisseurs de services hébergés](http://aka.ms/rdsonazurecertified) qui ont terminé le RDS sur IaaS, chemin d’accès et de l’évaluation de la formation.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) est spécialisé dans les éditeurs de logiciels en cours de transition vers le nuage et ISV' cherchent à optimiser leurs paramétrages de nuage actuel. ASPEX offre une large gamme de services gérés, devops, services et de Conseil.  

Emplacement principal : Anvers, Belgique

Région de l’opération : Europe de l’ouest

Statuts de partenaire : [argent](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Fournisseur de services de Cloud de Microsoft : Oui

Proposer des solutions de RemoteApp et bureau basés sur session : Oui, les deux

Solutions de migration vers Azure RemoteApp : Oui, [pour en savoir plus](https://www.aspex.be/en/azure-remote-apps)

**Contact :**

- Téléphone : +3232202198
- Courrier électronique :[info@aspex.be](mailto:info@aspex.be)
- Web : [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nom de la plateforme : MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) est une plate-forme d’automatisation pour les sociétés informatiques simplifier, optimiser et mettre à l’échelle la migration et la livraison des bureaux à distance, les applications distantes et l’infrastructure de nuage Microsoft Azure. 

La plate-forme de MyCloudIT permet de réduire le temps de déploiement de 95 %, Azure, des coûts de 30 % et déplace de l’infrastructure informatique de leurs clients dans le nuage en termes de quelques séquences de touches. Partenaires peuvent désormais gérer les clients à partir d’un tableau de bord global, les utilisateurs finaux de service dans le monde entier comme jamais auparavant, augmenter le chiffre d’affaires sans ajouter une surcharge supplémentaire ou des formations Azure.  

Emplacement principal : Dallas, Texas, Etats-Unis

Région de l’opération : dans le monde entier

Statuts de partenaire : [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Fournisseur de services de Cloud de Microsoft : Oui

Proposer des solutions de RemoteApp et bureau basés sur session : Oui, les deux

Solutions de migration vers Azure RemoteApp : Oui, [pour en savoir plus](https://mycloudit.com/remote-app-microsoft/)

**Contact :**
- Brian Garoutte, vice-président du développement commercial

   Téléphone : 972-218-0741

   Messagerie :[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) est spécialisé dans la fourniture de solutions de bureau hébergées, fourniture de bureau et les applications ISV expériences conçus sur la technologie Microsoft à un client global à base d’Azure et leurs propres centres de données.

Emplacement principal : Londres, Royaume-Uni ; Singapour ; Houston, TX

Région de l’opération : dans le monde entier

Statuts de partenaire : Gold

Fournisseur de services de Cloud de Microsoft : Oui

Proposer des solutions de RemoteApp et bureau basés sur session : Oui, les deux

Solutions de migration vers Azure RemoteApp : Oui, [pour en savoir plus](http://www.acuutech.com/ara-migration/)

**Contact :**

- Royaume-Uni :

  Maison de York 5/6, Langston route,

  Loughton, Essex IG10 3TQ
  
  Téléphone : + 44 (0) 20 8502 2155
 
- Singapour :

  Rue de Cecil 100 #09-02, 
  
  L’Atlas mondial, Singapour, 069532
  
  Téléphone : + 65 6709 4933
 
- Amérique du Nord : 

  3601 S. Sandman St.
  
  Suite 200, Houston, TX 77098
  
  Téléphone : +1 713 691 0800

## <a name="need-more-help"></a>Besoin d’aide ?
Encore besoin d’aide choix ou avez d’autres questions ? Utilisez une des méthodes suivantes pour obtenir de l’aide. 

1.  Envoyez-nous un e-mail à [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Contactez [l’assistance technique de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Commencer par ouvrir une [Azure prend en charge la casse](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Nous contacter. [Trouver un numéro de client local](https://azure.microsoft.com/overview/sales-number/).
