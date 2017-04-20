<properties 
    pageTitle="Guide du développeur de gouvernement Azure" 
    description="Cela fournit une comparaison des fonctionnalités et des conseils sur le développement d’applications pour Azure gouvernement" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Guide du développeur Microsoft Azure gouvernement 

<p> Microsoft Azure gouvernement un physiquement et instance isolé de réseau de Microsoft Azure.  Ce guide du développeur fournira plus d’informations sur les différences que les développeurs d’applications et aux administrateurs d’interagir et de travailler avec ces régions séparées d’Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>Dans cette rubrique.


+ [Vue d’ensemble](#Overview)
+ [Conseils pour les développeurs](#Guidance)
+ [Fonctionnalités disponibles dans Microsoft Azure gouvernement](#Features)
+ [Mappage de point de terminaison](#Endpoint)
+ [Étapes suivantes](#next)


## <a name="Overview"></a>Vue d’ensemble

Microsoft Azure gouvernement est une instance distincte du service Microsoft Azure satisfaire les besoins de sécurité et de conformité des administrations locales et les agences fédérales américaines et leurs fournisseurs de solutions. Gouvernement Azure offre physique et réseau d’isolement à partir des déploiements non américaine et filtré le personnel aux États-Unis. 

Microsoft fournit un certain nombre d’outils pour créer et déployer des applications de cloud service Microsoft Azure global (« Global Service ») et les services Microsoft Azure publics de Microsoft.

Lors de la création et le déploiement d’applications sur les Services de gouvernement Azure, et non le Service Global, les développeurs doivent connaître les principales différences entre les deux services.  Plus particulièrement autour de définition et la configuration de leur environnement de programmation, configuration des points de terminaison, écrire des applications et de les déployer en tant que services au gouvernement d’Azure.

Les informations contenues dans ce document récapitule ces différences et complètent les informations disponibles sur le site du(http://www.azure.com/gov "Gouvernement de Azure") [Azure gouvernement]et la [Bibliothèque technique de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") sur MSDN. Informations officielles peuvent également être disponibles dans tous les autres emplacements tels que la [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center" /), [Centre de Documentation Azure](https://azure.microsoft.com/documentation/) et [Blogs Azure] (https://azure.microsoft.com/blog/ « Blogs Azure » /). 

Ce contenu est destiné aux partenaires et de développeurs qui déploient Microsoft Azure gouvernement.



## <a name="Guidance"></a>Conseils pour les développeurs
La plupart du contenu technique qui est disponible actuellement suppose que les applications sont en cours de développement pour le Service Global plutôt que pour les administrations publiques de Microsoft Azure, il est important pour vous assurer que les développeurs connaissent les différences clés pour les applications développées pour être hébergée dans Azure gouvernement.

- Tout d’abord, il existe des différences de fonctionnalités et de services, cela signifie que certaines fonctionnalités qui se trouvent dans des zones spécifiques du Service Global ne soient pas disponibles dans Azure.

- Ensuite, pour les fonctionnalités que l'on retrouve dans Azure gouvernement, il existe des différences de configuration à partir du Service Global.  Par conséquent, vous devez examiner votre exemple de code, les configurations et les étapes à suivre pour vous assurer que vous générez et en cours d’exécution dans l’environnement de Services de Cloud publics Azure.


## <a name="Features"></a>Fonctionnalités disponibles dans Microsoft Azure gouvernement
Gouvernement Azure possède actuellement les services suivants disponibles dans les régions à la fois nous GOV IOWA et nous GOV VIRGINIE :

- Ordinateurs virtuels
- Services en nuage
- Stockage
- Active Directory
- Planificateur
- Réseau virtuel
- Base de données SQL
- Fichiers Azure
- Services de support
- Traffic Manager
- Bus des services
- StorSimple
- Redis de Cache
- Sauvegarde Azure
- Automation
- ExpressRoute
- etc.

Autres services sont disponibles, et les autres services seront ajoutés sur une base continue.  Pour obtenir la liste actualisée des services, veuillez consulter la [page des régions](https://azure.microsoft.com/regions/#services) qui met en surbrillance chaque région disponibles et leurs services.  

Actuellement, Iowa GOV de nous et nous Virginie de GOV sont les centres de données prenant en charge le gouvernement d’Azure.  Reportez-vous à la page des régions ci-dessus pour les centres de données en cours et les services disponibles.

## <a name="Endpoint"></a>Mappage de point de terminaison

Utilisez le tableau suivant pour vous guider lors du mappage des points de terminaison publics Microsoft Azure et de base de données SQL aux points de terminaison spécifiques de gouvernement d’Azure.


Type de service|Public Azure|Gouvernement Azure
---|---|---
Portail de gestion|Manage.windowsazure.com|Manage.windowsazure.us
Général|*. windows.net|*. usgovcloudapi.net
Principaux|*. core.windows.net|*. core.usgovcloudapi.net
Calculer|*. cloudapp.net|*. usgovcloudapp.net
Stockage des objets BLOB|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Stockage de la file d’attente|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Stockage de table|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Gestion des services|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
Base de données SQL|*. database.windows.net|*. database.usgovcloudapi.net
Équilibrage des charges ARM de point de terminaison|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* Pour l’authentification de ARM via AD Azure, veuillez vous reporter à [Authentifier les demandes d’Azure le Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Étapes suivantes

Si vous souhaitez en savoir plus et sur Azure gouvernement exploiter certains des liens ci-dessous.

- **[Inscrivez-vous pour une version d’évaluation](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Acquisition et accéder au gouvernement d’Azure](http://azure.com/gov)**

- **[Vue d’ensemble du gouvernement Azure](/azure-government-overview)**

- **[Blog du gouvernement Azure](http://blogs.msdn.com/b/azuregov/)**

- **[Conformité Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
