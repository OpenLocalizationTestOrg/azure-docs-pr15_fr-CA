<properties
    pageTitle="Quel est le Kit de développement .NET Azure"
    description="Découvrez ce qui est inclus dans le Kit de développement .NET Azure."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>Quel est le SDK Azure pour .NET ?

## <a name="overview"></a>Vue d’ensemble

Le Kit de développement Azure pour .NET est un ensemble d’outils de Visual Studio, des outils de ligne de commande, fichiers binaires du runtime et les bibliothèques de client qui vous permettent de développement, de tester et de déploiement des applications qui s’exécutent dans Azure. Cet article décrit en détail ce que vous obtenez lorsque vous installez le Kit de développement logiciel. Vous pouvez télécharger le SDK à partir de la [page de téléchargements d’Azure](https://azure.microsoft.com/downloads/).

Le Kit de développement Azure pour .NET comprend également des [bibliothèques clientes pour la consommation de services Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Ces bibliothèques sont installés séparément à l’aide de NuGet.

##<a id="included"></a>Ce qui est inclus dans le Kit de développement Azure pour .NET

Le Kit de développement Azure pour .NET installe les produits suivants :

- [Visual Studio Community Edition 2015](#vwd)
- [Émulateur de stockage Microsoft Azure](#stgemulator)
- [Outils de stockage Microsoft Azure](#stgtools)
- [Outils de création de Microsoft Azure](#auth)
- [Émulateur Microsoft Azure](#emulator)
- [Pilote ODBC de la ruche HDInsight les outils de Visual Studio et de Microsoft](#hdinsight)
- [Bibliothèques Microsoft Azure pour .NET](#libraries)
- [Microsoft Azure Mobile application SDK](#mobile)
- [Microsoft Azure PowerShell](#ps)
- [Outils Microsoft Azure pour Microsoft Visual Studio](#tools)
- [Microsoft ASP.NET et des outils Web pour Visual Studio](#wte)
- [Outils Microsoft Azure données LAC pour Visual Studio](#datalake)

###<a id="vwd"></a>Visual Studio Community Edition 2015

Si vous ne disposez pas de Visual Studio sur votre ordinateur, le Kit de développement installe [2015 édition de communauté Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Émulateur de stockage Microsoft Azure

L' [Émulateur de stockage Azure](http://msdn.microsoft.com/library/hh403989.aspx) utilise une instance de SQL Server et le système de fichiers local pour simuler le stockage Azure (files d’attente, les tableaux, objets BLOB), afin que vous puissiez tester localement.

###<a id="stgtools"></a>Outils de stockage Microsoft Azure

Cette procédure installe [AzCopy](http://aka.ms/AzCopy), un outil de ligne de commande que vous pouvez utiliser pour transférer des données vers et à partir d’un compte de stockage Azure.

###<a id="auth"></a>Outils de création de Microsoft Azure

Cela inclut les éléments suivants :

* L' [outil de ligne de commande CSPack](http://msdn.microsoft.com/library/gg432988.aspx) pour la création de packages de déploiement.
* l' [outil de ligne de commande CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) pour le cryptage des mots de passe utilisés pour accéder aux instances de rôle de service de cloud via une connexion Bureau à distance.
* Fichiers binaires du runtime que les projets de service en nuage requièrent pour communiquer avec leur environnement d’exécution et pour les tests de diagnostic. Ces fichiers binaires ne sont pas disponibles dans les packages NuGet.

###<a id="emulator"></a>Émulateur Microsoft Azure

L' [Émulateur Azure](http://msdn.microsoft.com/library/dn339018.aspx) simule l’environnement de service cloud afin que vous pouvez tester des projets de service cloud localement sur votre ordinateur avant de les déployer vers Azure.

###<a id="hdinsight"></a>Outils d’HDInsight pour Visual Studio et le pilote ODBC de Microsoft ruche

Outils d’HDInsight dans l’Explorateur de serveurs vous permettent de vous permet de naviguer dans les bases de données de ruche et comptes de stockage pour les clusters de HDInsight, de créer des tables et de créer et de soumettre des requêtes de la ruche. Pour plus d’informations, consultez [mise en route à l’aide d’outils d’Hadoop HDInsight pour Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="libraries"></a>Bibliothèques Microsoft Azure pour .NET

Cela inclut les éléments suivants :

* Les packages NuGet pour stockage Azure, un Bus de Service et la mise en cache qui sont stockés sur votre ordinateur afin que Visual Studio puisse créer nouveau nuage lors des projets de service en mode hors connexion.
* Un Visual Studio plug-in qui permet aux projets [Dans le rôle Cache](http://msdn.microsoft.com/library/dn386103.aspx) exécuter localement dans Visual Studio.

###<a id="mobile"></a>Microsoft Azure Mobile application SDK

Outils pour l’utilisation avec [Le Service Azure App Apps Mobile](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>Microsoft Azure PowerShell

PowerShell Azure permet [d’automatiser le déploiement et la création de l’environnement Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Outils Microsoft Azure pour Microsoft Visual Studio

Cela vous permet de travailler avec des ressources Azure, principalement les Services en nuage et les Machines virtuelles :

* [Créer, ouvrir et de publier des projets de service cloud](cloud-services/cloud-services-dotnet-get-started.md).
* [Créer des packages de déploiement pour les projets de service cloud](http://msdn.microsoft.com/library/ff683672.aspx).
* [Créer des ordinateurs virtuels Azure lors de la création de projets web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Scripts PowerShell de créer lors de la création de nouvelles machines virtuelles](http://msdn.microsoft.com/library/dn642480.aspx).
* [Afficher et gérer les paramètres de projet de service cloud dans les fenêtres de propriétés de projet Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Permet d’afficher et de gérer les [services en nuage](http://msdn.microsoft.com/library/ff683675.aspx), les [ordinateurs virtuels](http://msdn.microsoft.com/library/jj131259.aspx)et les [Bus de Service](http://msdn.microsoft.com/library/jj149828.aspx) dans l’Explorateur de serveurs.
* [Exécuter en mode de débogage à distance pour les services en nuage et les machines virtuelles](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatiser le provisionnement des ressources à l’aide de projets de déploiement de groupe de ressources Azure](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Outils de Service d’application Microsoft pour Visual Studio

Cela vous permet de travailler avec des sites Web Azure :

* [Projets web de publication de sites Web d’Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Publier des projets d’application console pour Azure WebJobs](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Ressources de site Web Azure de création et de la base de données SQL lors de la création d’un nouveau projet web ou lors de la publication d’un projet web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [PowerShell de créer des scripts de déploiement lors de la création de nouveaux sites Web](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gérer et dépanner les sites Web d’Azure dans l’Explorateur de serveurs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Exécuter en mode de débogage à distance pour les sites Web et WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] Vous n’êtes pas obligé d’installer le SDK Azure pour .NET à utiliser ces fonctionnalités ; ils sont également inclus dans les mises à jour de Visual Studio.

##<a id="datalake"></a>Outils Microsoft Azure données LAC pour Visual Studio

Pour plus d’informations, consultez [didacticiel : développement de scripts U-SQL à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>Ce qui n’a pas inclus lorsque vous installez le Kit de développement Azure pour .NET

Il y a quelques choses que vous souhaiterez pour le développement d’Azure qui ne sont pas inclus lorsque vous installez le Kit de développement. Les plus importants sont les suivants :

* [Les bibliothèques clientes](http://go.microsoft.com/fwlink/?LinkId=510472).

    L’Azure SDK comprend les bibliothèques client pour l’utilisation des services Azure, mais pas tous sont installés lorsque vous installez le Kit de développement. Si votre application requiert une bibliothèque cliente qui le SDK n’est pas installé, vous pouvez l’obtenir à partir de [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Si votre application utilise une bibliothèque cliente qui s’installe le Kit de développement, il est conseillé de mettre à jour avec la version actuelle sur NuGet.org.

    **Copies locales des bibliothèques clientes.** Le Kit de développement Azure pour .NET copie sur votre ordinateur les packages NuGet pour certaines bibliothèques client Azure, tels que le Bus des services, de stockage et de mise en cache. Ces bibliothèques de client sont automatiquement inclus dans les nouveaux projets de service cloud, afin que les packages NuGet locales activer Visual Studio créer des projets même si vous n’êtes pas connecté à Internet. Les bibliothèques clientes sont généralement mis à jour plus fréquemment que de nouvelles versions du Kit de développement logiciel sont publiées, afin que les bibliothèques clientes sur NuGet.org sont souvent plus récentes que celles que vous obtenez avec le Kit de développement logiciel.

    **Modèles de projet incluent les bibliothèques clientes.** Seuls les modèles de projet [Azure Cloud Service](cloud-services/cloud-services-dotnet-get-started.md) et Service d’application Azure [Applications Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) incluent automatiquement certaines bibliothèques client. Pour les autres bibliothèques ou d’autres modèles, installez les [packages NuGet de bibliothèque client](http://go.microsoft.com/fwlink/?LinkId=510472) dont vous avez besoin.

* [Modèles de projet des applications Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Modèles d’applications Mobile sont uniquement disponibles dans Visual Studio 2013 mise à jour 2 et versions ultérieures. Ils ne sont pas disponibles dans Visual Studio 2012 ou des versions antérieures et non dans Visual Studio 2013 mise à jour 1 ou une version antérieure, même si vous installez le Kit de développement Azure pour .NET.

##<a id="faq"></a>Forum aux Questions

- [De nombreuses fonctionnalités Azure sont déjà dans Visual Studio. Dois-je installer le SDK Azure pour .NET ?](#azinvs)
- [Je veux une bibliothèque cliente. Dois-je installer le SDK Azure pour .NET pour l’obtenir ?](#clientlib)
- [Où puis-je trouver des versions plus anciennes du SDK Azure pour .NET ?](#olderversions)
- [Quelle est la stratégie de cycle de vie pour les versions du Kit de développement Azure pour .NET ?](#lifecycle)
- [Les versions de système d’exploitation invité est le SDK Azure pour .NET compatible avec ?](#guestos)
- [Comment désinstaller le Kit de développement Azure pour .NET ?](#uninstall)

###<a id="azinvs"></a>De nombreuses fonctionnalités Azure sont déjà dans Visual Studio. Dois-je installer le SDK Azure pour .NET ?

Il est recommandé d’installer le Kit de développement si vous souhaitez développer pour Azure à l’aide des outils les plus récents. Si vous préférez installer pas le Kit de développement, vous pouvez le faire si les conditions suivantes sont remplies :

* Vous avez installé dernière [Mise à jour de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Vous développez uniquement pour les sites Web d’Azure ou Services mobiles, pas pour les services en nuage ou de Machines virtuelles.
* Votre application n’utilise pas de stockage, ou qu’il utilise le stockage, mais vous ne devez pas l’émulateur de stockage ou de l’outil AzCopy.

###<a id="clientlib"></a>Je veux une bibliothèque cliente. Dois-je installer le SDK Azure pour .NET pour l’obtenir ?

Le Kit de développement installe des bibliothèques clientes uniquement afin de pouvoir créer nuage des projets de service même si vous n’êtes pas connecté à Internet. Les bibliothèques de client en cours sont disponibles dans NuGet, sur [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Pour plus d’informations, consultez [ce qui n’a pas inclus lorsque vous installez le Kit de développement Azure pour .NET](#notincluded) plus haut dans ce document.

###<a id="olderversions"></a>Où puis-je trouver des versions plus anciennes du SDK Azure pour .NET ?

Pour les versions plus anciennes, consultez que page de téléchargement du [Kit de développement logiciel Azure pour .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

###<a id="lifecycle"></a>Quelle est la stratégie de cycle de vie pour les versions du Kit de développement Azure pour .NET ?

Reportez-vous à la section [Services de Cloud Microsoft Azure prend en charge la stratégie de cycle de vie](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Les versions de système d’exploitation invité est le SDK Azure pour .NET compatible avec ?

Voir [les versions du système d’exploitation invité Azure et matrice de compatibilité SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Comment désinstaller le Kit de développement Azure pour .NET ?

Chaque article repris dans cet article dans [ce qui est inclus dans le Kit de développement Azure pour .NET](#included) est un programme dans la liste des programmes installés dans le panneau **programmes et fonctionnalités**.  Il n’y a aucun moyen de les désinstaller en tant que groupe ; Vous devez désinstaller individuellement de chaque programme.

Lorsque vous avez le Kit de développement Azure pour .NET est déjà installé et que vous installez une nouvelle version, il n’est généralement aucun nécessaire de désinstaller l’ancienne. Dans la plupart des cas, l’installation du Kit de développement logiciel met à jour un programme existant plutôt qu’ajouter un nouveau laissant le.

Toutefois, si vous souhaitez supprimer non plus à vos besoins les restes d’une version antérieure, désinstallez uniquement les programmes qui spécifient le numéro de version et les désinstaller uniquement si le même programme avec une version plus récente est présent. Par exemple, après la mise à jour à partir de 2.5 de vers 2.6, vous pouvez voir les versions 2.5 et 2.6 de « Outils Microsoft Azure pour Microsoft Visual Studio 2013 », et vous pouvez désinstaller la version 2.5. Mais vous pouvez voir uniquement de la version 2.5 de « Outils de création de Microsoft Azure », et il ne serait pas possible sans désinstallation.

Notez que les numéros de version dans les titres du programme indiqués dans **programmes et fonctionnalités** peuvent être trompeuses.  Par exemple, version 2.6 comprend « Microsoft Azure Mobile application SDK V1.0 » si vous installez le Kit de développement pour Visual Studio 2013 et « Microsoft Azure Mobile application SDK V2.0 » pour Visual Studio 2015 ; Dans ce cas, la version est un indicateur dont Visual Studio version le programme s’applique, mais la version SDK.

Cet article ne répertorie pas tous les programmes que chaque version antérieure du SDK Azure incluse ; Il existe des autres programmes, que vous pouvez désinstaller des versions antérieures de SDK, car les versions antérieures du Kit de développement logiciel incluaient parfois des programmes qui ont été omis dans les versions ultérieures. Par exemple, version 2.5 installe « Azure Resource Manager outils pour Visual Studio », mais qui n’est pas dans la liste de cet article, car il ne s’affiche plus comme un programme distinct dans **programmes et fonctionnalités**.  Cet article répertorie uniquement les programmes qui sont inclus dans le Kit de développement Azure pour .NET version 2.6.

> **Remarque :** Certains des programmes contenant le Kit de développement peuvent également être installés séparément dans d’autres contextes et peuvent être nécessaire même si vous n’avez pas besoin du Kit de développement complet. Peuvent être de même des programmes qui ont été installés par les versions antérieures du Kit de développement logiciel, mais qui ont été omis à partir de la version du Kit de développement logiciel. Lorsque vous désinstallez des programmes, soyez prudent afin d’éviter la suppression d’un élément qui est toujours nécessaire sur votre ordinateur.



##<a id="versions"></a>Versions

Pour voir quelle version est en cours ou télécharger des versions antérieures, reportez-vous à la page [d’Azure SDK pour l’historique des versions de .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Ressources

Pour télécharger le Kit de développement logiciel actuel Azure pour .NET ou d’une bibliothèque cliente, reportez-vous à la [page de téléchargements d’Azure](https://azure.microsoft.com/downloads/).

Pour le Kit de développement Azure pour le code source de .NET, y compris les bibliothèques clientes, consultez [GitHub.com/Azure](https://github.com/azure/).

Pour la documentation de référence de bibliothèque client d’Azure, consultez [Référence de .NET Azure](https://azure.microsoft.com/documentation/api/).
