<properties 
    pageTitle="Configurer des applications web dans le Service d’application Azure" 
    description="Comment faire pour configurer une application web dans les Services d’application Azure" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Configurer des applications web dans le Service d’application Azure #

Cette rubrique explique comment configurer une application web à l’aide du [Portail Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Paramètres de l’application

1. Dans le [Portail Azure], ouvrez la lame pour l’application web.
2. Cliquez sur **tous les paramètres**.
3. Cliquez sur **paramètres de l’Application**.

![Paramètres de l’application][configure01]

La lame de **paramètres d’Application** a des paramètres regroupés sous plusieurs catégories.

### <a name="general-settings"></a>Paramètres généraux

**Les versions de framework**. Définissez ces options si votre application utilise une de ces infrastructures : 

- **.NET Framework**: définir la version du .NET framework. 
- **PHP**: définir la version PHP ou **OFF** pour désactiver PHP. 
- **Java**: sélectionnez la version de Java ou **OFF** pour désactiver Java. Utilisez l’option **Web conteneur** pour choisir entre les versions de Tomcat et jetée.
- **Python**: sélectionnez les Python version ou **OFF** pour désactiver les Python.

Pour des raisons techniques, l’activation de Java pour votre application désactive les options .NET, PHP et Python.

<a name="platform"></a>
**Plate-forme**. Permet d’indiquer si votre application web s’exécute dans un environnement 32 bits ou 64 bits. L’environnement 64 bits requiert le mode de base ou Standard. Libérez et modes de Shared sont toujours exécutent dans un environnement 32 bits.

**Web Sockets**. La valeur **ON** pour activer le protocole WebSocket. par exemple, si votre application web utilise [ASP.NET SignalR] ou [socket.io].

<a name="alwayson"></a>
**Toujours actif**. Par défaut, les applications web sont déchargées, s’ils sont inactifs pendant une certaine période de temps. Cela permet au système d’économiser les ressources. En mode base ou Standard, vous pouvez activer **Toujours** conserver l’application chargé tout le temps. Si votre application exécute des tâches de bande continue, vous devez activer **Toujours sur**ou les projets web peuvent ne pas fonctionner de manière fiable.

**Gérés Version du Pipeline**. Définit le [mode de pipeline]de IIS. Laisser ce jeu intégré (par défaut), sauf si vous avez une application héritée qui nécessite une version plus ancienne d’IIS.

**Remplacez l’automatique**. Si vous activez le Swap d’automatique pour un emplacement de déploiement, application Service inversera automatiquement l’application web en production lorsque vous appuyez sur une mise à jour de cet emplacement. Pour plus d’informations, voir [déploiement mis en œuvre des emplacements pour les applications web dans le Service d’application Azure] (web-sites-intermédiaire-publishing.md).

### <a name="debugging"></a>Débogage

Le **débogage distant**. Active le débogage distant. Lorsqu’il est activé, vous pouvez utiliser le débogueur distant dans Visual Studio pour vous connecter directement à votre application web. Débogage distant restera activé pendant 48 heures. 

### <a name="app-settings"></a>Paramètres de l’application

Cette section contient des paires nom/valeur qui vous web application chargera sur Démarrer. 

- Pour les applications .NET, ces paramètres sont injectées dans votre configuration .NET `AppSettings` lors de l’exécution, substitution de paramètres existants. 

- Les applications de Python, PHP, Java et de nœud peuvent accéder à ces paramètres en tant que variables d’environnement lors de l’exécution. Pour chaque paramètre d’application, les deux variables d’environnement sont créés ; l’une avec le nom spécifié par l’entrée de paramètre d’application et l’autre avec le préfixe APPSETTING_. Les deux contiennent la même valeur.

### <a name="connection-strings"></a>Chaînes de connexion

Chaînes de connexion pour les ressources liées. 

Pour les applications .NET, ces chaînes de connexion sont injectées dans votre configuration .NET `connectionStrings` paramètres lors de l’exécution, en remplaçant les entrées existantes dont la clé est le nom de la base de données liée. 

Pour les applications de Python, PHP, Java et de nœud, ces paramètres seront disponibles en tant que variables d’environnement lors de l’exécution, préfixé avec le type de connexion. Les préfixes de variables d’environnement sont les suivantes : 

- De SQL Server :`SQLCONNSTR_`
- MySQL :`MYSQLCONNSTR_`
- Base de données SQL :`SQLAZURECONNSTR_`
- Personnalisé :`CUSTOMCONNSTR_`

Par exemple, si une chaîne de connexion MySql ont été nommée `connectionstring1`, il est accessible via la variable d’environnement `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documents par défaut

Le document par défaut est la page web qui s’affiche à l’URL de la racine d’un site Web.  Le premier fichier correspondant dans la liste est utilisé. 

Les applications Web peuvent utiliser des modules itinéraire en fonction de l’URL plutôt que fournit du contenu statique, auquel cas il n’existe aucun document par défaut en tant que tel.    

### <a name="handler-mappings"></a>Mappages de gestionnaires

Utilisez cette zone pour ajouter des processeurs de script personnalisé pour traiter les demandes pour les extensions de fichier spécifiques. 

- **Extension**. L’extension de fichier à traiter, par exemple *.PHP ou handler.fcgi. 
- **Chemin d’accès du processeur de script**. Le chemin d’accès absolu au processeur de script. Les demandes de fichiers qui correspondent à l’extension de fichier seront traités par le processeur de script. Utilisez le chemin d’accès `D:\home\site\wwwroot` pour faire référence au répertoire racine de votre application.
- **Des Arguments supplémentaires**. Arguments de ligne de commande facultatifs pour le processeur de script 


### <a name="virtual-applications-and-directories"></a>Annuaires et les applications virtuelles 
 
Pour configurer des répertoires et des applications virtuelles, spécifier chaque répertoire virtuel et son chemin d’accès physique correspondant par rapport à la racine du site Web. Vous pouvez également sélectionner la case à cocher de **l’Application** pour marquer un répertoire virtuel en tant qu’application.


## <a name="enabling-diagnostic-logs"></a>Activation des journaux de diagnostic

Pour activer les journaux de diagnostic :

1. Dans la lame pour votre application web, cliquez sur **tous les paramètres**.
2. Cliquez sur **journaux de Diagnostic**. 

Options d’écriture des journaux de diagnostic à partir d’une application web qui prend en charge la journalisation : 

- **Journalisation de l’application**. Écrit des journaux d’application dans le système de fichiers. La journalisation s’étend sur une période de 12 heures. 

**Niveau**. Lorsque la journalisation de l’application est activée, cette option spécifie la quantité d’informations qui seront enregistrées (erreur, avertissement, Information ou Verbose).

**Journal de serveur web**. Les journaux sont enregistrés dans le format de fichier journal étendu W3C. 

**Messages d’erreur détaillés**. Des messages d’erreur détaillé d’enregistre les fichiers .htm. Les fichiers sont enregistrés sous /LogFiles/DetailedErrors. 

**Le traçage des demandes a échoué**. Journaux de demandes vers des fichiers XML qui ont échoué. Les fichiers sont enregistrés sous /LogFiles/W3SVC*xxx*, où xxx est un identificateur unique. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Veillez à télécharger le fichier XSL, car il fournit des fonctionnalités de mise en forme et le contenu des fichiers XML de filtrage.

Pour afficher les fichiers journaux, vous devez créer des informations d’identification FTP, comme suit :

1. Dans la lame pour votre application web, cliquez sur **tous les paramètres**.
2. Cliquez sur **informations d’identification de déploiement**.
3. Entrez un nom d’utilisateur et le mot de passe.
4. Cliquez sur **Enregistrer**.

![Informations d’identification du déploiement de jeu][configure03]

Le nom d’utilisateur FTP complet est « app\username » où *app* est le nom de votre application web. Le nom d’utilisateur est répertorié dans la blade d’application web, sous **Essentials**.  

![Informations d’identification du déploiement FTP][configure02]

## <a name="other-configuration-tasks"></a>Autres tâches de configuration

### <a name="ssl"></a>SSL 

En mode base ou Standard, vous pouvez télécharger les certificats SSL pour un domaine personnalisé. Pour plus d’informations, reportez-vous à la section [Activer HTTPS pour une application web]. 

Pour afficher vos certificats téléchargés, cliquez sur **Tous les paramètres** > **Custom domains et SSL**.

### <a name="domain-names"></a>Noms de domaine

Ajouter des noms de domaine personnalisé pour votre application web. Pour plus d’informations, reportez-vous à la section [configurer un nom de domaine personnalisé pour une application web dans le Service d’application Azure].

Pour afficher vos noms de domaine, cliquez sur **Tous les paramètres** > **Custom domains et SSL**.

### <a name="deployments"></a>Déploiements

- Configurer le déploiement en continu. Reportez-vous [à l’aide de Git pour déployer des applications Web dans le Service d’application Azure](./web-sites-deploy.md).
- Emplacements de déploiement. Consultez [déploiement dans des environnements de test pour les applications Web dans le Service d’application Azure].


Pour afficher les emplacements de votre déploiement, cliquez sur **Tous les paramètres** > **les emplacements de déploiement**.

### <a name="monitoring"></a>Surveillance

En mode base ou Standard, vous pouvez tester la disponibilité des points de terminaison HTTP ou HTTPS, de jusqu'à trois emplacements distribués géographiquement. Un test de surveillance échoue si le code de réponse HTTP est une erreur (4xx ou 5xx) ou de la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible si les surveillance tests réussissent dans tous les emplacements spécifiés. 

Pour plus d’informations, consultez [Comment : surveiller l’état du point de terminaison web].

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application], où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un nom de domaine personnalisé dans le Service d’application Azure]
- [Activer HTTPS pour une application dans Azure Application Service]
- [Mettre à l’échelle une application web dans le Service d’application Azure]
- [Surveillance des notions de base pour les applications Web dans le Service d’application Azure]

<!-- URL List -->

[SignalR d’ASP.NET]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Configurer un nom de domaine personnalisé dans le Service d’application Azure]: ./web-sites-custom-domain-name.md
[Déploiement dans les environnements de reclassement pour les applications Web dans le Service d’application Azure]: ./web-sites-staged-publishing.md
[Activer HTTPS pour une application dans Azure Application Service]: ./web-sites-configure-ssl-certificate.md
[Comment : surveiller l’état du point de terminaison web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Surveillance des notions de base pour les applications Web dans le Service d’application Azure]: ./web-sites-monitor.md
[mode pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Mettre à l’échelle une application web dans le Service d’application Azure]: ./web-sites-scale.md
[Socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Essayez le Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
