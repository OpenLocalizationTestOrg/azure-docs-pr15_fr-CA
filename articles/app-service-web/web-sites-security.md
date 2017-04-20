<properties
    pageTitle="Sécuriser une application de Service d’application Azure"
    description="Apprenez à sécuriser une application web, back-end de l’application mobile ou API app dans le Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Sécuriser une application de Service d’application Azure

Cet article vous aide à démarrer sur la sécurisation de votre application web, back-end de l’application mobile ou API app dans le Service d’application Azure. 

Sécurité dans le Service d’application Azure possède deux niveaux : 

- **Plate-forme et infrastructure de sécurité** - vous faire confiance Azure pour que les services que vous avez besoin d’exécuter en toute sécurité dans le nuage.
- **Sécurité des applications** - vous devez concevoir l’application elle-même en toute sécurité. Cela inclut comment vous intégrez à Active Directory de Azure, comment gérer des certificats et comment vous assurer que vous pouvez parler en toute sécurité à des services différents. 

#### <a name="infrastructure-and-platform-security"></a>Plate-forme et infrastructure de sécurité
Parce que le Service de l’application gère les ordinateurs virtuels Azure, stockage, connexions réseau, infrastructures web, gestion et fonctions d’intégration et bien plus encore, il est activement sécurisée et renforcée et passe par le respect de la réglementation énergiquement et vérifie en permanence pour s’assurer que :

- Vos applications de Service de l’application sont isolées à partir d’Internet à la fois et à partir de ressources Azure d’autres clients.
- La communication de secrets (par exemple, les chaînes de connexion) entre votre application de Service de l’application et d’autres ressources Azure (par exemple, base de données SQL) dans un groupe de ressources reste dans Azure et ne franchir les limites du réseau. Les secrets sont toujours cryptés.
- Toutes les communications entre votre application de Service de l’application et les ressources externes, comme la gestion de PowerShell, interface de ligne de commande, Azure SDK, les API reste et les connexions hybride, sont cryptées correctement.
- les menaces 24 heures gestion protège les ressources de Service de l’application contre les logiciels malveillants, distribuée par déni de service distribué (DDoS), man-in-the-middle (l’homme) et autres menaces. 

Pour plus d’informations sur la sécurité de l’infrastructure et de plateforme dans Azure, consultez le [Centre de confiance Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Sécurité des applications

Azure est responsable de la sécurisation de l’infrastructure et la plate-forme de votre application s’exécute sur, il vous incombe de sécuriser votre application lui-même. En d’autres termes, vous avez besoin développer, déployer et gérer votre code d’application et le contenu de manière sécurisée. Sans cela, votre code d’application ou d’un contenu peut encore être vulnérable aux menaces telles que :

- SQL Injection
- Détournement de session
- Cross-site scripting
- HOMME au niveau de l’application
- Au niveau de l’application DDoS

Une description détaillée des considérations de sécurité pour les applications basées sur le web est au-delà de la portée de ce document. Comme point de départ pour plus d’informations sur la sécurisation de votre application, consultez [Cours Web Application Security Project (OWASP avoir)](https://www.owasp.org/index.php/Main_Page), spécifiquement les [10 premiers projet.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), qui répertorie les failles de sécurité application actuelle de top 10 web critiques, tel que déterminé par les membres d’OWASP avoir.

## <a name="perform-penetration-testing-on-your-app"></a>Effectuer le test de votre application de pénétration

Un des moyens plus simples à débuté avec des tests pour les vulnérabilités de votre application de Service de l’application est d’utiliser l' [intégration avec la sécurité de Tinfoil](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) pour effectuer la vulnérabilité d’un seul clic d’analyse sur votre application. Vous pouvez afficher les résultats dans un rapport facile à comprendre et savoir comment résoudre chaque problème de sécurité avec des instructions étape par étape.

Si vous préférez effectuer vos propres tests de pénétration ou que vous souhaitez utiliser une autre suite de scanneur ou d’un autre fournisseur, vous devez suivre le [processus d’approbation test de pénétration Azure](https://security-forms.azure.com/penetration-testing/terms) et obtenir une approbation préalable pour effectuer les tests de pénétration de votre choix.

##<a name="https"></a>Sécuriser la communication avec les clients

Si vous utilisez la ** \*. azurewebsites.net** nom de domaine créé pour votre application de Service de l’application, vous pouvez immédiatement utiliser HTTPS, un certificat SSL est fourni pour toutes les ** \*. azurewebsites.net** les noms de domaine. Si votre site utilise un [nom de domaine personnalisé](web-sites-custom-domain-name.md), vous pouvez télécharger un certificat SSL pour [Activer HTTPS](web-sites-configure-ssl-certificate.md) pour le domaine personnalisé.

L’activation du [protocole HTTPS](https://en.wikipedia.org/wiki/HTTPS) peut aider à protéger contre les attaques de l’homme sur la communication entre votre application et ses utilisateurs.

## <a name="secure-data-tier"></a>Couche de données sécurisées

Service d’application intègre hautement SQL de base de données, telles que les chaînes de connexion sont cryptées sur la carte et sont décryptées uniquement sur l’ordinateur virtuel que l’application s’exécute sur *et* uniquement lorsque l’application s’exécute. En outre, la base de données de SQL Azure inclut de nombreuses fonctionnalités de sécurité pour vous aider à sécuriser vos données d’application à partir de menaces informatiques, y compris le [cryptage au repos](https://msdn.microsoft.com/library/dn948096.aspx), [Toujours crypté](https://msdn.microsoft.com/library/mt163865.aspx), [Le masquage de données dynamiques](../sql-database/sql-database-dynamic-data-masking-get-started.md)et [Une détection des menaces](../sql-database/sql-database-threat-detection-get-started.md). Si vous avez des données sensibles ou respect de la réglementation, consultez [sécurisation de votre base de données SQL](../sql-database/sql-database-security.md) pour plus d’informations sur la sécurisation de vos données.

Si vous utilisez un fournisseur de base de données tierce, comme ClearDB, vous devez consulter la documentation du fournisseur directement sur les meilleures pratiques de sécurité.  

##<a name="develop"></a>Sécuriser le développement et le déploiement

### <a name="publishing-profiles-and-publish-settings"></a>Les profils de publication et les paramètres de publication

Lors du développement d’applications, effectuer des tâches de gestion, ou automatiser des tâches à l’aide d’utilitaires tels que **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou l' **Interface de ligne de commande de Azure (CLI Azure)**, vous pouvez utiliser un fichier de *paramètres de publication* ou un *profil de publication*. Les deux types de fichiers vous authentifieront avec Azure et doivent être sécurisées pour empêcher tout accès non autorisé.

* Un fichier de **paramètres de publication** contient

    * Votre ID d’abonnement Azure

    * Un certificat de gestion qui vous permet d’effectuer des tâches de gestion pour votre abonnement *sans avoir à fournir un nom de compte ou le mot de passe*.

* Un fichier de **profil de publication** contient

    * Informations pour la publication de votre application

Si vous utilisez un utilitaire qui utilise un fichier de paramètres de publication ou d’un fichier de profil de publication, importez le fichier contenant les paramètres de publication ou le profil dans l’utilitaire, puis **Supprimer** le fichier. Si vous devez conserver le fichier à partager avec d’autres personnes travaillant sur le projet, par exemple, le stocker dans un endroit sécurisé comme un répertoire *crypté* avec des autorisations restreintes.

En outre, il se peut que vous devez vous assurer que les informations d’identification importées sont sécurisées. Par exemple, **Azure PowerShell** et l' **Interface de ligne de commande de Azure (Azure CLI)** stockent les informations importées dans votre **répertoire de base** (*~* sur les systèmes Linux ou OS X et */users/yourusername* sur les systèmes Windows.) Pour plus de sécurité, vous souhaiterez peut-être **crypter** ces emplacements à l’aide d’outils de cryptage disponibles pour votre système d’exploitation.

### <a name="configuration-settings-and-connection-strings"></a>Paramètres de configuration et les chaînes de connexion
Il est de pratique courante pour stocker des chaînes de connexion, les informations d’authentification et autres informations sensibles dans les fichiers de configuration. Malheureusement, ces fichiers risquent d’être exposées sur votre site Web ou consignation dans un référentiel public, exposer ces informations. Par exemple, une recherche simple sur [GitHub](https://github.com), peut révéler des innombrables des fichiers de configuration avec des secrets exposés dans les référentiels publics.

La meilleure pratique consiste à conserver ces informations sur les fichiers de configuration de votre application. Service d’application vous permet de stocker des informations de configuration dans le cadre de l’environnement d’exécution en tant que **paramètres de l’application** et des **chaînes de connexion**. Les valeurs sont exposés à votre application lors de l’exécution via des *variables d’environnement* pour la plupart des langages de programmation. Pour les applications .NET, ces valeurs sont injectées dans votre configuration .NET lors de l’exécution. En dehors de ces cas, ces paramètres de configuration reste cryptés, sauf si vous affichez ou que vous configurez à l’aide du [Portail Azure](https://portal.azure.com) ou utilitaires tels que PowerShell ou l’interface CLI d’Azure. 

Stockage des informations de configuration dans le Service d’application rend possible pour l’administrateur de l’application verrouiller des informations sensibles pour les applications de production. Les développeurs peuvent utiliser un ensemble de paramètres de configuration distinct pour le développement de l’application et les paramètres peuvent être automatiquement remplacées par les paramètres configurés dans le Service d’application. Pas même les développeurs doivent connaître les secrets configurés pour l’application de production. Pour plus d’informations sur la configuration des paramètres d’application et des chaînes de connexion dans le Service d’application, consultez [configuration web applications](web-sites-configure.md).

### <a name="ftps"></a>FTPS

Service d’application Azure fournit un accès FTP sécurisé au système de fichiers pour votre application par le biais de **FTPS**. Cela vous permet d’accéder de manière sécurisée le code de l’application sur le web app, ainsi que les tests de diagnostic journaux. Il est recommandé de toujours utiliser FTPS place de FTP. 

Vous trouverez le lien FTPS pour votre application avec les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sélectionnez **tous les parcourir**.
3. À partir de la blade **Parcourir** , sélectionnez **Les Services d’application**.
4. À partir de la lame de **Services d’application** , sélectionnez l’application souhaitée.
5. À partir de lames de l’application, sélectionnez **tous les paramètres**.
6. À partir de la blade de **paramètres** , sélectionnez **Propriétés**.
7. Les liens FTP et FTPS sont fournies sur la lame de **paramètres** . 

Pour plus d’informations sur FTPS, voir [Protocole de transfert de fichier](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurité de la plateforme Azure, informations sur le signalement d’un **incident de sécurité ou d’abus**ou d’informer Microsoft que vous exécutez **des tests de pénétration** de votre site, consultez la section sécurité du [Centre de confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Pour plus d’informations sur les fichiers **web.config** ou **applicationhost.config** dans les applications de Service d’application, consultez [les options de Configuration déverrouillé dans Azure Application Service web apps](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Pour plus d’informations sur les informations de journalisation pour les applications de Service d’application, qui peut être utile pour détecter les attaques, consultez [Activer l’enregistrement des diagnostics](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="whats-changed"></a>Ce qui a changé

* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
