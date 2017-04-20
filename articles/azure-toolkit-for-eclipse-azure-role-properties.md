<properties
    pageTitle="Propriétés de rôle Azure"
    description="Découvrez comment le Shared Computer Toolkit Azure pour Eclipse permet de configurer les paramètres de rôle Azure."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# <a name="azure-role-properties"></a>Propriétés de rôle Azure #

Divers paramètres de configuration pour votre rôle Azure peuvent être définies dans le Shared Computer Toolkit Azure pour Eclipse.

## <a name="configuring-azure-role-properties"></a>Configuration des propriétés de rôle Azure ##

Configuration des propriétés de votre rôle Azure s’effectue via les boîtes de dialogue Propriétés de rôle de votre travail. Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et sélectionnez le sous-menu **Azure** . (Si vous ne voyez pas le rôle dans l’Explorateur de projets Eclipse, développez votre projet Azure dans l’Explorateur de projet.)

![][ic789599]

Les différentes propriétés qui peuvent être définies dans les boîtes de dialogue de **Propriétés** sont décrites dans cette rubrique. Notez que de nombreuses propriétés sont renseignées automatiquement lorsque vous créez un nouveau projet de déploiement d’Azure.

Les pages de propriétés suivantes sont disponibles pour les rôles d’Azure.

* [Propriétés de la machine virtuelle](#virtual_machine_properties)
* [La mise en cache des propriétés](#caching_properties)
* [Propriétés de certificats](#certificates_properties)
* [Propriétés des composants](#components_properties)
* [Propriétés de débogage](#debugging_properties)
* [Propriétés des points de terminaison](#endpoints_properties)
* [Propriétés de variables d’environnement](#environment_variables_properties)
* [Équilibrage de la charge / propriétés affinité (ou « sticky sessions ») de la session](#session_affinity_properties)
* [Propriétés de stockage local](#local_storage_properties)
* [Propriétés de configuration de serveur](#server_configuration_properties)
* [Propriétés de déchargement SSL](#ssl_offloading_properties)
    
<a name="virtual_machine_properties"></a>
### <a name="virtual-machine-properties"></a>Propriétés de la machine virtuelle ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse, cliquez sur **Azure**, puis cliquez sur **Propriétés**, et vous avez la possibilité de modifier la taille de la machine virtuelle et également modifier le nombre d’instances, comme le montre l’image suivante.

![][ic719499]

>[AZURE.NOTE] Windows uniquement : lorsque vous définissez le nombre d’instances d’une valeur supérieure à 1 et que vous configurez également un serveur d’applications, le toolkit permet uniquement de 1 instance de rôle à l’exécution dans l’émulateur, indépendamment de ce paramètre. C’est pour éviter des conflits de liaison de port entre les différentes instances de serveur (par exemple, tous les essayer de se lier au port 8080) lorsqu’ils s’exécutent sur le même ordinateur. Votre paramètre du nombre instance souhaitée est conservé, mais il prend effet uniquement lorsque vous déployez sur le nuage.

<a name="caching_properties"></a> 
### <a name="caching-properties"></a>La mise en cache des propriétés ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **mise en cache**. Dans cette boîte de dialogue, vous pouvez activer nommés caches compatible avec memcache, être situés, en vous permettant d’accélérer vos applications web.

![][ic719483]

Dans la page de propriétés de **mise en cache** , vous pouvez spécifier des paramètres globaux pour les éléments suivants :

* Si la mise en cache situés est activée.
* la taille du cache sous la forme d’un pourcentage de la mémoire.
* le nom de compte de stockage pour l’enregistrement de l’état du cache lorsque votre application s’exécute comme un service en nuage, ou none si vous ne souhaitez pas enregistrer l’état du cache. (Le nom du compte de stockage n’est pas utilisé lorsque vous exécutez votre application dans l’émulateur de calcul.) Si vous définissez le nom du compte de stockage **(auto)** (qui est la valeur par défaut), votre configuration de mise en cache utilise automatiquement le même compte de stockage que celui que vous sélectionnez dans la boîte de dialogue **publier sur Azure** .

>[AZURE.NOTE] Le paramètre de **(automatique)** a l’effet souhaité que si vous publiez votre déploiement à l’aide de la trousse à outils de Eclipse Assistant Publication. Si en revanche vous publiez le fichier .cspkg manuellement à l’aide d’un mécanisme externe, tel que le [Portail de gestion Azure][], le déploiement ne fonctionnera pas correctement.

La boîte de dialogue affiche les propriétés d’un cache.

![][ic719501]

* **Nom :** Le nom du cache situé.
* **Numéro de port :** Le numéro de port à utiliser pour le cache.
* **Procédure d’expiration :** Une des valeurs suivantes qui spécifie quand une clé dans le cache expire.
    * **Absolu :** La clé arrive à expiration lorsque le délai spécifié en **Minutes à live** est atteinte.
    * **NeverExpires :** La clé n’a pas de délai d’expiration.
    * **SlidingWindow :** La clé expire si elle n’ont pas été utilisé pendant la durée spécifiée par **Minutes en direct**; chaque fois qu’il est accessible, l’horloge d’expiration est réinitialisé.
* **Minutes live :** Nombre maximal de minutes pour une clé de memcached de vie, sous réserve de la stratégie d’expiration.
* **Haute disponibilité grâce à des sauvegardes répliquées sur les instances de rôles différents :** Si activée, aide à fournit la haute disponibilité utilisant répliquées des sauvegardes sur les instances de rôles différents. Notez qu’au moins deux instances de rôle doit être en vigueur pour le déploiement de cette fonctionnalité.

Pour ajouter un nouveau cache, cliquez sur le bouton **Ajouter** dans la page de propriétés de **mise en cache** et une boîte de dialogue **Configurer le Cache nommé** sera ouvert. Fournir des valeurs pour les propriétés qui sont décrites ci-dessus.

Pour modifier un cache nommé, activez le cache et cliquez sur le bouton **Modifier** dans la page de propriétés de **mise en cache** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés de cache. Appuyez sur **OK** pour enregistrer les valeurs de cache.

Pour supprimer un cache, sélectionnez le cache et cliquez sur le bouton **Supprimer** dans la page de propriétés de **mise en cache** , puis cliquez sur **Oui** pour confirmer la suppression.

Pour plus d’informations sur l’utilisation de la mise en cache, voir [comment utiliser la mise en cache Co-located][].

<a name="certificates_properties"></a> 
### <a name="certificates-properties"></a>Propriétés de certificats ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **certificats**.

![][ic710964]

Dans cette boîte de dialogue, vous pouvez ajouter ou supprimer des certificats référencés par votre projet Eclipse. Notez que les certificats répertoriés ici ne sont pas automatiquement stockés à l’intérieur d’un keystore de Java et par conséquent, ne sont pas automatiquement disponibles pour toute utilisation à partir d’une application Java. Ils sont uniquement enregistrés avec Azure afin que puissent être préchargés dans les fenêtres de certificat stocker sur les ordinateurs virtuels exécutant votre déploiement et est ensuite utilisé par d’autres logiciels Windows. Actuellement, la seule fonctionnalité de la boîte à outils qui utilise les certificats référencés de cette façon dans la boîte de dialogue **certificats** est le [Déchargement SSL][], en raison de sa dépendance vis-à-vis d’Internet Information Services (IIS) et l’Application demande routage (ARR), qui exigent le certificat approprié à la disposition de cette manière.

Lorsque vous déployez votre projet sur Azure à l’aide de l’Assistant Publication, vous devez pointer sur les fichiers d’échange dynamique de données (PFX, Personal Information Exchange) correspondant à ces certificats, ainsi que de leurs mots de passe, afin de les télécharger automatiquement au service Azure, mais uniquement si elles n'ont pas été téléchargées il précédemment.

<a name="components_properties"></a> 
### <a name="components-properties"></a>Propriétés des composants ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **composants**. Dans cette boîte de dialogue, vous avez la possibilité d’ajouter, modifier, ou supprimer les composants de votre rôle, mais aussi modifier l’ordre dans lequel ils sont traités.

![][ic719502]

La fonctionnalité des composants vous permet d’ajouter des dépendances à votre projet de déploiement d’Azure, tels que les projets d’application Java, fichiers spéciaux et les instructions d’exécutable de ligne de commande qui sont nécessaires à votre déploiement.

Pour chaque composant, vous pouvez spécifier :

* L’étape à prendre lors de l’importation du composant dans votre projet de déploiement d’Azure, lorsqu’il est créé.
* L’étape à prendre lors du déploiement de ce composant dans le nuage Azure.

>[AZURE.NOTE] Lors de la spécification des fichiers de composants ou de lignes de commande, gardez à l’esprit que votre déploiement sera publié à une machine virtuelle Windows, afin que vos étapes personnalisées doivent être valides pour un système d’exploitation Windows. 

Les composants ont les propriétés suivantes :

* **Importation :** Méthode qui indique comment le composant sera importé dans le projet lorsque le projet est généré. Cela peut être une des valeurs suivantes :
    * **copie :** Le composant est copié à partir du chemin d’accès local spécifié par la propriété **From** dans le répertoire **approot** du rôle.
    * **EAR :** Le composant est un archivage d’entreprise Java (EAR) importé à partir d’un projet sur le chemin d’accès local spécifié par la propriété **à partir de** Application d’entreprise. (Ceci est détecté automatiquement par le Kit d’outils en fonction de la nature du projet à cet emplacement).
    * **JAR :** Le composant est une archive Java (JAR) et est importé à partir d’un projet Java en le chemin d’accès local spécifié par la propriété **à partir de** . (Ceci est détecté automatiquement par le Kit d’outils en fonction de la nature du projet à cet emplacement).
    * **Aucun :** Aucune action n’est prise pour importer le composant. Cela s’applique lorsque le composant est supposé pour être déjà présents dans le répertoire **approot** du rôle, ou lorsque le composant est simplement une instruction exécutable de ligne de commande, tel que spécifié dans la propriété **en tant que** lorsque la méthode de **déploiement** est **exec**.
    * **WAR :** Le composant est une archive d’application web Java (WAR) et est importé à partir d’un projet Web dynamique dans le chemin d’accès local spécifié par la propriété **From** . (Ceci est détecté automatiquement par le Kit d’outils en fonction de la nature du projet à cet emplacement).
    * **zip :** Le composant est un fichier zip et est importé par compresser le répertoire ou le fichier spécifié par la propriété **From** .
* **à partir de :** Chemin d’accès de la source sur votre ordinateur local vers le dossier ou le fichier qui représente les éléments à importer dans votre déploiement. Variables d’environnement Windows peuvent être utilisées dans cette propriété. Tous les composants importables seront importés dans le répertoire **approot** du rôle lorsque le projet est généré.
    
    Notez que vous avez la possibilité de déployer un composant à partir d’un téléchargement lors du déploiement sur le cloud (pas l’émulateur de calcul). Consultez les informations connexes ci-dessous sur l’ajout d’un composant.    
    
* **As:** Nom de fichier sous lequel le composant sera importé dans le répertoire **approot** du rôle et finalement déployer dans le nuage Azure. Laissez cette propriété vide pour conserver le nom tel qu’il est sur l’ordinateur local. (Pour les composants exécutables, c'est-à-dire celles dont la méthode de **déploiement** est **exec**, cela peut être une instruction de ligne de commande Windows arbitraire).

    >[AZURE.IMPORTANT] Si vous utilisez des espaces pour cette valeur, ceux-ci sont gérés différemment en fonction de la méthode de déploiement. Si la méthode de déploiement est **exec**, espaces seront interprétés comme des séparateurs d’arguments de ligne de commande et non comme une partie du nom de fichier. Déploiement de pour toutes les autres méthodes, espaces seront interprétés comme faisant partie du nom de fichier.
    
* **Déployer :** Méthode qui indique l’action appliquée au composant lorsque le déploiement est démarré. Cela peut être une des valeurs suivantes :
    * **copie :** Le composant est copié dans le chemin d’accès de destination spécifié par la propriété **To** .
    * **exec :** Le composant est une instruction exécutable de ligne de commande Windows exécutée dans le contexte de chemin d’accès spécifié par la propriété **To** , au moment que du déploiement commence.
    * **Aucun :** Aucune action n’est appliquée au composant lorsque le déploiement commence.
    * **zip :** Le composant est décompressé dans le chemin d’accès de destination spécifié par la propriété **To** . Cette méthode est disponible uniquement lorsque la propriété **importation** **zip**.
* **To:** Chemin de destination sur l’ordinateur virtuel dans lequel le composant sera déployé. Variables d’environnement Windows peuvent être utilisées dans cette propriété et chemins d’accès sont relatifs **approot**.
    
Pour ajouter un nouveau composant, cliquez sur le bouton **Ajouter** dans la page de propriétés des **composants** et une boîte de dialogue **Composant du rôle Azure** sera ouvert. Fournir des valeurs pour les propriétés qui sont décrites ci-dessus. 

Voici un exemple d’ajout d’un nouveau composant de guerre.

![][ic719503]

Lors du déploiement sur le cloud (et pas l’émulateur de calcul), si vous souhaitez déployer le composant à partir d’un téléchargement, assurez-vous que l’option **en nuage, au lieu d’inclure dans le package, déployer à partir** d’est activée. Si vous souhaitez télécharger à partir de votre compte de stockage Azure, sélectionnez le compte de stockage à partir de la liste déroulante **compte de stockage** (vous pouvez cliquer sur le lien **comptes** pour modifier ce qui figure dans la liste), qui va remplir partiellement dans le champ **URL** , puis renseignez la partie restante de l’URL. Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **compte de stockage** et entrez l’URL de votre composant dans le champ **URL** . Spécifiez l’une des méthodes suivantes :

* **copie :** Le composant de téléchargement est copié dans le chemin d’accès de destination spécifié par le chemin d’accès **Au répertoire** .
* **même :** La méthode utilisée pour **déployer à partir de téléchargement** pour le **déploiement du package**.
* **zip :** Le composant de téléchargement est décompressé dans le chemin d’accès de destination spécifié par le chemin d’accès **Au répertoire** .

Pour modifier un composant, sélectionnez le composant et cliquez sur le bouton **Modifier** dans la page de propriétés **composants** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés du composant. Appuyez sur **OK** pour enregistrer les valeurs de composant.

Pour supprimer un composant, sélectionnez le composant et cliquez sur le bouton **Supprimer** dans la page de propriétés de **composants** , puis cliquez sur **Oui** pour confirmer la suppression.

Les composants sont traités dans l’ordre indiqué. Utilisez les boutons **Déplacer vers le haut** et **Déplacer vers le bas** pour modifier l’ordre.

>[AZURE.NOTE] La fonctionnalité de configuration du serveur s’appuie sur les composants. Ces composants ne peuvent pas être supprimées ou modifiées sans supprimer la configuration du serveur correspondant. Vous serez invité à ce sujet lorsque vous tentez d’apporter des modifications à ces composants.

<a name="debugging_properties"></a> 
### <a name="debugging-properties"></a>Propriétés de débogage ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **débogage**. Dans cette boîte de dialogue, vous avez la possibilité d’activer ou de désactiver le débogage à distance, ainsi que créez des configurations de débogage, comme indiqué dans l’image suivante.

![][ic719504]

Pour plus d’informations sur le débogage, consultez [Débogage d’Applications Azure dans Eclipse][].

<a name="endpoints_properties"></a> 
### <a name="endpoints-properties"></a>Propriétés des points de terminaison ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur les **points de terminaison**. Dans cette boîte de dialogue, vous avez la possibilité de créer un point de terminaison, ainsi que de modifier ou de supprimer un point de terminaison, comme illustré dans l’image suivante.

![][ic719505]

Pour ajouter un point de terminaison, cliquez sur le bouton **Ajouter** dans la page de propriétés des **points de terminaison** et ouvrira une boîte de dialogue **Ajouter un point de terminaison** .

![][ic710897]

Entrez un nom pour le point de terminaison, sélectionnez le type ( **entrée**, **interne**ou **InstanceInput**) et spécifier le port public et privé. Appuyez sur **OK** pour enregistrer les nouvelles valeurs de point de terminaison.

Selon le type de point de terminaison, vous pouvez utiliser les plages de ports comme suit :

* Pour un point de terminaison d’instance d’entrée, le port public peut être une plage de ports (par exemple le **2000-2010**) et le port privé est une valeur fixe.
* Pour un point de terminaison interne, le port public n’est pas utilisé, et le port privé peut être une plage vide ou un astérisque pour indiquer qu’il a la valeur est automatiquement définie par Azure.
* Pour les points de terminaison d’entrée, le port public a uniquement peut être une valeur fixe, et le port privé peut être une valeur fixe, ou vide ou définie sur un astérisque pour indiquer qu’elle est définie automatiquement par Azure.

Si vous souhaitez utiliser un numéro de port unique au lieu d’une plage, laissez la zone de texte à la fin de la plage à blanc.

Pour les ports qui sont la valeur automatique, si vous devront déterminer quel port est utilisé lors de l’exécution, votre application peut utiliser l’API du Runtime Service Azure, qui est documenté dans le [Résumé du package com.microsoft.windowsazure.serviceruntime][].

Pour voir comment les points de terminaison d’entrée instance peuvent être utilisés pour faciliter le débogage d’un déploiement de plusieurs instances, consultez [débogage d’une instance de rôle spécifique dans un déploiement de plusieurs instances][].

Pour modifier un point de terminaison, sélectionnez le point de terminaison et cliquez sur le bouton **Modifier** dans la page de propriétés des **points de terminaison** . Une boîte de dialogue s’ouvre et vous permet de modifier le nom de point de terminaison, type et les ports publics et privés. Appuyez sur **OK** pour enregistrer les valeurs modifiées du point de terminaison.

Pour supprimer un point de terminaison, sélectionnez le point de terminaison et cliquez sur le bouton **Supprimer** dans la page de propriétés des **points de terminaison** , puis cliquez sur **Oui** pour confirmer la suppression.

Pour configurer correctement certaines fonctionnalités (telles que la mise en cache, le débogage à distance, Session affinité ou SSL de déchargement) activées par l’utilisateur dans un rôle, la boîte à outils peut configurer automatiquement des points de terminaison spéciales qui apparaîtra ainsi que les points de terminaison définis par l’utilisateur. La trousse à outils empêche l’utilisateur de modifier, ou par exemple supprimer automatiquement généré des points de terminaison tant que la fonction associée est activée.

<a name="environment_variables_properties"></a> 
### <a name="environment-variables-properties"></a>Propriétés de variables d’environnement ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Variables d’environnement**. Dans cette boîte de dialogue, vous avez la possibilité de créer une variable d’environnement, ainsi que de modifier ou de supprimer une variable d’environnement, comme illustré dans l’image suivante.

![][ic719506]

Variables d’environnement sont disponibles à votre script de démarrage lorsque le rôle démarre.

>[AZURE.NOTE] Lorsque vous spécifiez des variables d’environnement, gardez à l’esprit que votre déploiement sera publié à une machine virtuelle Windows, afin que vos variables d’environnement doivent être valides pour un système d’exploitation Windows.

Un exemple d’une variable d’environnement qui est disponible lorsque le rôle démarre, créer une nouvelle variable d’environnement en cliquant sur le bouton **Ajouter** . L’exemple suivant montre une variable d’environnement nommée **MyRoleVersion** qui est créée et attribuée la valeur **1.0**.

![][ic659268]

Dans votre code jsp, vous pouvez afficher la valeur à l’aide de la `System.getenv` méthode :

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Ce qui entraîne cette sortie lors de l’exécution de votre application :

![][ic552233]

Pour modifier une variable d’environnement, sélectionnez la variable d’environnement, puis cliquez sur le bouton **Modifier** dans la page de propriétés de **Variables d’environnement** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés de variables d’environnement. Appuyez sur **OK** pour enregistrer des valeurs de variable de l’environnement.

Pour supprimer une variable d’environnement, sélectionnez la variable d’environnement et cliquez sur le bouton **Supprimer** dans la page de propriétés de **Variables d’environnement** , puis cliquez sur **Oui** pour confirmer la suppression.

Pour configurer correctement activé par l’utilisateur dans un rôle de certaines fonctionnalités (telles que la Configuration du serveur, le débogage distant ou Local de stockage), la boîte à outils peut configurer automatiquement les variables spéciales d’environnement qui apparaîtra ainsi que les variables d’environnement de défini par l’utilisateur. La trousse à outils empêche l’utilisateur de modifier, ou par exemple supprimer automatiquement généré des variables d’environnement tant que la fonction associée est activée.

<a name="session_affinity_properties"></a> 
### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Équilibrage de la charge / propriétés affinité (ou « sticky sessions ») de la session ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **L’équilibrage de charge**. Dans cette boîte de dialogue, vous avez la possibilité d’activer ou de désactiver l’affinité de session, comme indiqué dans l’image suivante.

![][ic719492]

Pour plus d’informations, consultez [l’Affinité de la Session][]. Notez également le comportement de cette fonction dans le contexte de déchargement SSL, comme décrit sur [Le déchargement SSL][].

<a name="local_storage_properties"></a> 
### <a name="local-storage-properties"></a>Propriétés de stockage local ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Stockage Local**. Dans cette boîte de dialogue, vous avez la possibilité de créer, de modifier ou de supprimer le stockage local temporaire pour l’ordinateur virtuel qui exécute votre application. Valeurs spécifiques peuvent être définies pour la taille du stockage local, ainsi que le contenu est conservé lorsque le rôle est recyclé, comme illustré dans l’image suivante.

![][ic719508]

Vous pouvez également spécifier une variable d’environnement qui correspond au stockage local.

Par défaut, tous les éléments que vous déployez dans Azure sont placé (et décompressé) dans le dossier **approot** de l’instance de rôle. Alors que les déploiements plus simples seront s’adapter à la même après l’avoir décompacté, l’espace alloué pour le répertoire **approot** est limitée et pas bien défini (moins de 1 Go est une empirique raisonnable). Par conséquent, pour garantir le Qu'azure alloue suffisamment d’espace disque pour des déploiements plus importants qui ne tient pas dans le dossier **approot** , vous devez configurer une ressource de stockage local à l’aide de la boîte de dialogue **Stockage Local** . Un moyen facile pour ce faire, reportez-vous à la section [Déploiement de grands déploiements][].

Vous pouvez facilement référencer la ressource de stockage à partir de scripts de démarrage (par exemple, votre **startup.cmd**) à l’aide de la variable d’environnement associée automatiquement par la trousse à outils Eclipse avec la ressource, comme indiqué dans la boîte de dialogue **Stockage Local** . Cette variable d’environnement contient le chemin d’accès complet de la ressource locale que vous avez configuré au moment de le qu'exécution de votre script de démarrage. 

Pour modifier une ressource de stockage local, sélectionnez la ressource de stockage local et cliquez sur le bouton **Modifier** dans la page de propriétés de **Stockage Local** . Une boîte de dialogue s’ouvre et vous permet de modifier les propriétés de ressource de stockage local. Appuyez sur **OK** pour enregistrer les valeurs de ressources de stockage local.

Pour supprimer une ressource de stockage local, sélectionnez la ressource de stockage local et cliquez sur le bouton **Supprimer** dans la page de propriétés de **Stockage Local** , puis cliquez sur **Oui** pour confirmer la suppression.

<a name="server_configuration_properties"></a> 
### <a name="server-configuration-properties"></a>Propriétés de configuration de serveur ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Configuration du serveur**. Dans cette boîte de dialogue, ont la possibilité d’ajouter, de supprimer et de modifier le serveur d’applications JDK et Java utilisé par votre déploiement, mais aussi ajouter ou supprimer les applications (par exemple, les fichiers JAR, de guerre ou de l’oreille) utilisées par votre déploiement.

### <a name="jdk-configuration"></a>Configuration du JDK ###

Cette boîte de dialogue vous permet de spécifier le package JDK à utiliser pour votre déploiement. Si vous utilisez Eclipse sous Windows, vous pouvez spécifier le package JDK à utiliser localement lors de l’exécution dans l’émulateur Azure et vous avez la possibilité de déployer cette installation locale vers Azure. Sur les systèmes d’exploitation autres que Windows, le paramètre de JDK émulateur n’est pas applicable, et vous ne pouvez pas déployer JDK installé localement dans la mesure où il n’est pas compatible avec Windows. Toutefois, quel que soit le système d’exploitation que vous utilisez, vous pouvez toujours choisir entre les packages JDK 3ème partie à déployer sur Azure, ou pointez sur votre propre package JDK de compatible Windows à partir d’un emplacement de téléchargement de remplacement.

Voici un exemple de la façon dont vous pouvez spécifier un JDK sur Windows :

![][ic780647]

Si vous utilisez Eclipse sous Windows, vous pouvez spécifier un JDK à utiliser avec l’émulateur de calcul ; Pour ce faire, vérifiez que **utilisation du JDK à partir de ce chemin d’accès de fichier pour tester localement** est activé dans la section **déploiement de l’émulateur** . Ensuite, spécifiez le chemin d’accès local de votre JDK ; Vous pouvez accéder à JDK différent si celui que vous souhaitez utiliser n’est pas activée automatiquement. Vous avez également la possibilité de déployer votre JDK à votre service cloud Azure ; Pour ce faire, sélectionnez l’option **déployer mon JDK local (téléchargement automatique pour le stockage en nuage)** dans la section **déploiement de Cloud** .

Remarque : Sur les systèmes d’exploitation autres que Windows, les paramètres de **déploiement de l’émulateur** et l’option **déployer mon JDK local** ne sont pas disponibles. L’exemple suivant illustre la spécification d’un JDK sur un Mac ou autre système d’exploitation non Windows pris en charge :

![][ic789643]

Quel que soit le système d’exploitation que vous utilisez, vous avez les deux options suivantes de **déploiement de nuage** pour la source et le type de votre package JDK :

* **Déployer un package JDK 3ème partie disponible sur Azure** 
* **Déployer à partir d’un téléchargement personnalisé** 

Si vous utilisez l’option **déployer un 3ème partie package JDK disponible dans Azure** :

1. Cochez la case nommée **déployer un 3ème partie package JDK disponible dans Azure**.
1. Dans la liste déroulante, sélectionnez le package JDK partie 3 disponible sur Azure.
1. L’onglet **JDK** ressemblera à ce qui suit sous Windows :  ![][ic780648] 
    et il ressemblera à ce qui suit sous Mac OS ou autre prise en charge des systèmes d’exploitation non-Windows : ![][ic789643]
1. Cliquez sur **OK** pour enregistrer vos modifications.
1. Lorsque vous êtes invité à accepter le contrat de licence du fournisseur de package 3ème partie JDK, passez en revue le contrat de licence. En supposant que vous acceptez les termes, cliquez sur **Oui** pour fermer la boîte de dialogue **contrat de licence d’accepter** .
    Notez que la logique sous-jacente pour lequel les éléments apparaissent dans la liste déroulante de l’option **déployer un 3ème partie package JDK disponible dans Azure** peut être personnalisée. Pour personnaliser les éléments, dans la boîte de dialogue **JDK** , cliquez sur le lien **Personnaliser** . Cela ferme la page de propriétés **JDK** et ouvrir le fichier **componentsets.xml** dans Eclipse, que vous pouvez ensuite modifier en fonction des besoins. Documentation de **componentsets.xml** est incluse dans le fichier **componentsets.xml** .

Si vous utilisez l’option **déployer un JDK à partir d’un téléchargement personnalisé** :

1. Créer un fichier ZIP de votre répertoire d’installation de JDK, veiller à ce que le nœud du répertoire lui-même est l’enfant de la structure ZIP et pas son contenu. Prenez note du nom du répertoire, comme vous en auriez besoin ultérieurement et gardez à l’esprit que cette installation JDK est déployée sur une machine virtuelle Windows.
1. Télécharger le ZIP sur votre compte de stockage Azure comme un objet blob. Vous pouvez effectuer ce en utilisant un outil externes disponible pour les objets BLOB de téléchargement vers le stockage Azure. Il est recommandé d’utiliser un blob privé. Prenez note de l’URL de blob du contenu ZIP.
1. Cochez la case nommée **déployer un JDK à partir d’un téléchargement personnalisé**.
    Si vous souhaitez télécharger à partir de votre compte de stockage Azure, sélectionnez le compte de stockage à partir de la liste déroulante **compte de stockage** (vous pouvez cliquer sur le lien **comptes** pour modifier ce qui figure dans la liste), qui va remplir partiellement dans le champ **URL** , puis renseignez la partie restante de l’URL. Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **compte de stockage** et entrez l’URL de téléchargement de votre JDK dans le champ **URL** . Si vous utilisez le stockage Azure, noms de blob dans l’URL doivent être en minuscules.
1. Assurez-vous que la zone de texte **JAVA_HOME** fait référence au nom de répertoire correct. Par défaut, il référence le même nom de répertoire JDK que la valeur que vous avez choisi pour votre usage local. Mais si le répertoire contenu dans le ZIP porte un nom différent (par exemple, en raison d’à l’aide d’une version différente), mettre à jour le nom du répertoire dans la zone de texte **JAVA_HOME** en conséquence, étant donné que ce paramètre sera utilisé dans le nuage (et non dans l’émulateur de calcul).
1. Cliquez sur **OK** pour enregistrer vos modifications.

Voilà. Maintenant, lorsque vous générez pour le nuage, vous remarquerez la taille du package seront beaucoup plus petite, le processus de génération a généralement doit prendre moins de temps et le déploiement lui-même lorsque vous publiez vers le nuage doit également prendre moins de temps. Notez que les options **Mon JDK local (téléchargement automatique pour le stockage en nuage) de déployer** ou de **déployer un JDK à partir d’un téléchargement personnalisé** sont en vigueur uniquement lorsque votre application est déployée dans le nuage. Ils n’ont aucun effet sur votre expérience d’émulateur de calcul ; la version locale des composants sera toujours utilisée lors du déploiement vers l’émulateur de calcul. 

### <a name="server-configuration"></a>Configuration du serveur ###

Voici un exemple de la façon dont vous pouvez spécifier un serveur d’applications.

![][ic796926]

Vérifiez que la case à cocher **déployer un serveur de ce type** est sélectionné, puis choisissez le type de serveur d’applications à utiliser.

Pour spécifier un serveur à utiliser pour le déploiement du cloud, vous pouvez tirer parti des options suivantes :

1. **Déployer un 3ème partie serveur disponible sur Azure** - Ceci s’applique en particulier dans les scénarios de développement/test où l’efficacité du déploiement et la simplicité est une priorité et que le serveur ne requiert pas une configuration personnalisée. Ou lorsque vous souhaitez utiliser un de ces serveurs comme point de départ, mais vous inclure les étapes de personnalisation du serveur approprié dans la logique de démarrage de votre déploiement.
1. **Déployer à partir d’un téléchargement personnalisé** - ce est applicable en particulier dans les scénarios de production lorsque vous avez un serveur spécialement préparé et configuré que vous souhaitez utiliser dans le nuage.
1. **Déployer l’installation de serveur local** - c’est particulièrement applicable dans si votre installation de serveur local est déjà configurés pour votre usage. Si vous choisissez cette option, vous devez également spécifier le chemin d’accès au local de votre serveur dans la zone de texte **chemin d’accès Local de serveur** ci-dessous.

Si vous utilisez l’option **déployer un 3ème partie serveur disponible sur Azure** :

1. Cochez la case nommée **déployer un 3ème partie serveur disponible sur Azure**.
1. Dans le menu déroulant, sélectionnez le logiciel de serveur de votre choix à utiliser avec votre déploiement dans le cloud. Notez que si vous avez déjà spécifié un type de serveur à utiliser précédemment, sera limitée à choisir un serveur de nuage qui est de la même famille que ce type de serveur. Mais si vous n’avez pas choisi un type de serveur, vous pouvez choisir parmi tous les serveurs qui sont actuellement disponibles sur Azure et le type de serveur sera sélectionné automatiquement pour vous.
1. Cliquez sur **OK** pour enregistrer vos modifications.

Si vous utilisez l’option de **déploiement à partir d’un téléchargement personnalisé** :

1. Assurez-vous que vous avez déjà sélectionné un type de serveur en fonction de la procédure précédente. Indique le plug-in comment déployer le serveur à partir de votre téléchargement personnalisé, il doit être de la même famille comme type de serveur sélectionné.
1. Cochez la case nommée **Deploy à partir d’un téléchargement personnalisé**.
    Si vous souhaitez télécharger à partir de votre compte de stockage Azure, sélectionnez le compte de stockage à partir de la liste déroulante **compte de stockage** (vous pouvez cliquer sur le lien **comptes** pour modifier ce qui figure dans la liste), qui va remplir partiellement dans le champ **URL** , puis renseignez la partie restante de l’URL de votre serveur de téléchargement ZIP (lors de l’utilisation du stockage Azure, noms d’objet blob dans l’URL doit être en minuscule). Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **compte de stockage** et entrez l’URL de votre téléchargement de server postal dans le champ **URL** . Le ZIP contient un dossier enfant qui représente votre répertoire d’installation d’application server. Par exemple, si vous utilisez un zip pour Apache Tomcat 7.0.35, dans le zip serait le dossier enfant qui représente le répertoire d’installation, tels que **apache-tomcat-7.0.35**. 
1. Permet de spécifier la valeur de la variable d’environnement de répertoire de base. Il prend par défaut la valeur utilisée pour votre serveur d’application locale, le cas échéant, mais vous pouvez spécifier une valeur différente si votre serveur d’application cloud est différent de votre serveur d’application local. Toutefois, vous devez vous assurer que votre serveur d’application cloud est de la même famille que le serveur de type sélectionné précédemment.
    Si vous mettez à jour votre zip de serveur d’application cloud à l’avenir, vous pouvez modifier manuellement le paramètre de répertoire de base, ou, pour le rendre à nouveau correspond à votre paramètre local (si vous avez modifié votre serveur d’application local trop).
1. Cliquez sur **OK** pour enregistrer vos modifications.

La logique sous-jacente pour lequel les éléments apparaissent dans l’onglet **serveur** de la page de propriétés de **Configuration du serveur** peut être personnalisée. Il s’agit d’une fonctionnalité avancée que vous devrez peut-être si vos besoins dépassent les valeurs par défaut ou si vous souhaitez ajouter d’autres serveurs. Pour personnaliser la logique, dans la boîte de dialogue **serveur** , cliquez sur le lien **Personnaliser** . Cela ferme la page de propriétés de **Configuration du serveur** et ouvrez le fichier **componentsets.xml** dans Eclipse, que vous pouvez ensuite modifier en fonction des besoins d’étendre le modèle de configuration de serveur. Documentation de **componentsets.xml** est incluse dans le fichier **componentsets.xml** .

Si vous utilisez l’option **déployer mon serveur local (téléchargement automatique pour le stockage en nuage)** :

1. Cochez la case nommée **déployer mon serveur local (téléchargement automatique pour le stockage en nuage)**.
1. À l’aide de la liste déroulante **compte de stockage** , sélectionnez **(auto)**. Si vous spécifiez ici **(auto)** , le Kit d’outils Eclipse utilisera le même compte de stockage pour votre serveur en tant que celui que vous sélectionnez pour votre déploiement, dans la boîte de dialogue **publier sur Azure** .
1. Cliquez sur **OK** pour enregistrer vos modifications.

Sélectionnez un chemin d’installation de serveur sur votre ordinateur dans la zone de texte **chemin d’accès du serveur Local** si une des conditions suivantes est remplie :

* Vous souhaitez tester votre déploiement dans l’émulateur (s’applique uniquement à Windows).
* Vous souhaitez déployer votre serveur installé en local dans le nuage.
* Vous souhaitez utiliser un téléchargement de serveur personnalisé de votre propre dans le nuage, dans lequel cas, vérifiez également que l’option **déployer mon serveur local (téléchargement automatique pour le stockage en nuage)** est sélectionnée ci-dessus.

Si aucune des options précédentes s’applique à votre situation, le paramètre du serveur local est facultatif.

### <a name="applications-configuration"></a>Configuration des applications ###

Voici un exemple de la façon dont vous pouvez spécifier une application.

![][ic719512]

Cliquez sur **Ajouter** pour ajouter une autre application ou sur **Supprimer** pour supprimer une application. Pour des raisons d’efficacité, si vous souhaitez utiliser un téléchargement de la source d’une application lors du déploiement sur le cloud, utilisez les [Propriétés de composants](#components_properties) pour spécifier une URL, le compte de stockage, etc.. 

À partir de la version d’avril 2014, vos applications sont automatiquement chargées dans le même compte de stockage (sous le conteneur **eclipsedeploy** ) que celui sélectionné pour votre déploiement. La logique de démarrage de votre déploiement contient une étape qui télécharge d’abord ces applications à partir de ce compte de stockage. Cela signifie que vous pouvez mettre à niveau vos applications dans votre déploiement sans avoir à régénérer et redéployer le package entier, en le téléchargeant manuellement les versions plus récentes de l’application directement dans ce compte de stockage (en utilisant par exemple le portail Azure), remplacer les fichiers WAR de télécharger à l’origine par la boîte à outils. Alors, venez lancer le recyclage de toutes les instances de rôle à l’aide du portail de gestion d’Azure, ou via les utilitaires de ligne de commande. (Rôle de recyclage directement à partir de la boîte à outils Eclipse de déclenchement n'est pas actuellement pris en charge.)

### <a name="notes-about-server-configuration"></a>Notes à propos de la configuration du serveur ###

Les modifications apportées par le biais de la page de propriétés de **configuration du serveur** sont répercutées dans les `<component>` les éléments du fichier package.xml.

Lorsque vous utilisez le **Télécharger automatiquement...** ou les options de **déploiement à partir de téléchargement...** pour JDK ou serveur d’applications et vous générez pour le nuage (pas l’émulateur de calcul) et que vous êtes connecté au réseau, vous pouvez remarquer la génération des messages tels que les éléments suivants dans la sortie de la Console, comme le générateur Ant vérifie la disponibilité du téléchargement :

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Si vous avez sélectionné l’option de **déploiement à partir de téléchargement...** , le message d’avertissement suivant peut s’afficher, mais la génération se poursuit :

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Cet avertissement est la seule indication que la disponibilité de téléchargement n’a pas été vérifiée. Par conséquent, si un déploiement échoue dans le nuage pour une raison quelconque, vérifiez si vous avez reçu cet avertissement.

Si vous voulez désactiver la vérification de téléchargement (par exemple, si vous pensez que cela ralentit inutilement de la build), de définir la `verifydownloads` l’attribut `false` dans les `<windowsazurepackage>` élément de package.xml : 

`<windowsazurepackage verifydownloads="false" ...>` 

Si vous avez sélectionné l’option **Télécharger automatiquement...** , puis dans la fenêtre de console vous verrez les messages de génération reporting sur la progression du téléchargement toutes les 5 secondes, chaque fois qu’un téléchargement est nécessaire.

<a name="ssl_offloading_properties"></a> 
### <a name="ssl-offloading-properties"></a>Propriétés de déchargement SSL ###

Ouvrir le menu contextuel pour le rôle dans le volet de l’Explorateur de projet de Eclipse et cliquez sur **Azure**, puis cliquez sur **Le déchargement SSL**. 

![][ic719481]

Dans cette boîte de dialogue, vous pouvez activer le déchargement SSL, ce qui vous permet facilement d’activer la prise en charge du protocole sécurisé HTTPS (Hypertext Transfer) dans votre déploiement de Java sur Azure, sans vous obliger à configurer SSL sur votre serveur d’application Java. Pour plus d’informations, consultez [Déchargement SSL][] et explique [comment utiliser le déchargement SSL][].

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][]

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

[Propriétés de projet Azure][]

[Liste des comptes de stockage Azure][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propriétés de projet Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Liste des comptes de stockage Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Résumé du package com.Microsoft.windowsazure.ServiceRuntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Débogage d’une instance de rôle spécifique dans un déploiement de plusieurs instances]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Débogage d’Applications Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Déploiement des déploiements à grandes échelle]: http://go.microsoft.com/fwlink/?LinkID=699536
[Comment faire pour utiliser la mise en cache situés]: http://go.microsoft.com/fwlink/?LinkID=699542
[Comment faire pour utiliser le déchargement SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Affinité de session]: http://go.microsoft.com/fwlink/?LinkID=699548
[Le déchargement SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png
