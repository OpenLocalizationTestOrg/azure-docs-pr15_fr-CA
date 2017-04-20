<properties
    pageTitle="Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse"
    description="Découvrez les dernières fonctionnalités de la Shared Computer Toolkit Azure pour Eclipse."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse

## <a name="azure-toolkit-for-eclipse-releases"></a>Azure Shared Computer Toolkit pour les versions Éclipse

Cet article contient des informations sur les différentes versions et les dernières mises à jour pour le Shared Computer Toolkit Azure pour Eclipse.

> [AZURE.NOTE] Il existe également un Shared Computer Toolkit Azure pour IntelliJ IDE. Pour plus d’informations, reportez-vous à la section [Shared Computer Toolkit Azure pour IntelliJ].

### <a name="august-26-2016"></a>26 août 2016

Le Shared Computer Toolkit Azure pour Eclipse - août 2016 version inclut les améliorations suivantes :

* **Les Distributions de JDK personnalisé**. Le Shared Computer Toolkit Azure pour Eclipse prend désormais en charge la spécification et le déploiement d’une version JDK arbitraire à votre conteneur Azure WebApp :
  - Outre les kits JDK fourni par Azure, vous pouvez choisir à partir d’un large choix de versions Zoulou OpenJDK mis à disposition sur Azure par des systèmes de Azul.
  - Vous pouvez également spécifier votre propre distribution JDK si vous téléchargez un fichier ZIP sur votre compte de stockage.
* **Améliorations apportées à la vue Explorateur d’Azure**:
  - Prise en charge de la gestion des machines virtuelles à l’aide du nouveau modèle de gestionnaire de ressources du Azure : vous pouvez répertorier, créer et supprimer des machines virtuelles resource manager sans quitter l’IDE.
  - Prise en charge pour la gestion des comptes de stockage blob à l’aide du Gestionnaire de ressources d’Azure, qui complète les fonctionnalités de gestion des comptes de stockage « classique ».
* **Le pilote JDBC de Microsoft 6.0 pour SQL Server**. Cette mise à jour inclut le dernier pilote JDBC de Microsoft SQL Server (version 6.0), qui est maintenant inclus dans une bibliothèque que vous pouvez facilement ajouter à vos projets Java, ainsi remplacer l’ancienne version.

### <a name="june-29-2016"></a>29 juin 2016

Le Shared Computer Toolkit Azure pour Eclipse - juin 2016 version inclut les améliorations suivantes :

* **Exigence de Java 8**. Le Shared Computer Toolkit Azure pour Eclipse requiert désormais Java 8, bien que cette exigence n'est que pour la boîte à outils - vos applications peuvent continuer à utiliser toutes les versions de Java sont prises en charge par Azure.
* **Prise en charge pour les kits de JDK Java plus récente**. Les dernières versions des kits JDK Java sont désormais pris en charge par le Shared Computer Toolkit Azure pour Eclipse.
* **Prise en charge pour Azure SDK v2.9.1**. La dernière version du SDK Azure est désormais la minimum requise pour le Shared Computer Toolkit Azure pour Eclipse.
* **Exemples intégrés**. Le Shared Computer Toolkit Azure pour Eclipse propose maintenant plusieurs exemples d’applications pour aider les développeurs à commencer.
* **Intégration d’outils de HDInsight**. Outils d’HDInsight d’Azure sont désormais fournis avec le Shared Computer Toolkit Azure pour Eclipse. Pour plus d’informations, consultez le [Plug-in d’HDInsight outils Eclipse].
* **Débogage d’applications Web de Java à distance**. Le Shared Computer Toolkit Azure pour Eclipse prend désormais en charge le débogage distant des applications web de Java sur le Service d’application Azure.
* **Prise en charge pour la version Eclipse Luna.** La nouvelle version minimale requise Eclipse IDE est Luna.

### <a name="april-12-2016"></a>12 avril 2016

Le Shared Computer Toolkit Azure pour Eclipse - avril 2016 version inclut les améliorations suivantes :

* **Prise en charge pour Azure SDK v2.9.0**. La dernière version du SDK Azure est désormais la minimum requise pour le Shared Computer Toolkit Azure pour Eclipse.
* **Utilisation de divers, amélioration de la réactivité et les performances liées à la prise en charge de l’application Web de Azure**. Un certain nombre d’optimisations des performances dans comment le Shared Computer Toolkit communique avec résultat Azure dans une interface utilisateur plus réactive.
* **Possibilité de supprimer un conteneur d’Application Web existant dans Azure à partir d’Eclipse**. Le Shared Computer Toolkit Azure pour Eclipse permet désormais de supprimer un conteneur Azure Web existant sans quitter Eclipse.

### <a name="march-7-2016"></a>7 mars 2016

Le Shared Computer Toolkit Azure pour Eclipse - mars 2016 version inclut les améliorations suivantes :

* **Prise en charge pour le déploiement rapide des applications Java légères**. Le Shared Computer Toolkit Azure pour Eclipse prend désormais en charge le déploiement rapide des applications de Java légères dans Azure conteneurs d’applications Web, afin de déployer les applications Java maintenant prend quelques secondes seulement.
* **Prise en charge de la gestion d’application Web à l’aide de la vue Explorateur d’Azure**. La vue Explorateur d’Azure dans la boîte à outils prend désormais en charge de la liste, de démarrage et d’arrêt des applications Web de Azure.
* **Mise à jour de Tomcat, jetée et Zoulou OpenJDK les distributions**. Le Shared Computer Toolkit Azure pour Eclipse prend en charge les versions mises à jour de Tomcat, jetée et OpenJDK du Zoulou pour les déploiements de Java dans les services en nuage Azure.

### <a name="january-4-2016"></a>4 janvier 2016

Le Shared Computer Toolkit Azure pour Eclipse - janvier 2016 version inclut les améliorations suivantes :

* **Prise en charge pour les mises à jour Zoulou OpenJDK**. Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Mise à jour de Tomcat et les distributions de la jetée**. Les distributions de la jetée et Tomcat qui sont disponibles sur Microsoft Azure pour une utilisation avec le Shared Computer Toolkit Azure pour Eclipse ont été mis à jour.
* **Parité des fonctionnalités entre Eclipse et boîtes à outils IntelliJ pour Azure**. Le Shared Computer Toolkit Azure pour Eclipse et [Shared Computer Toolkit Azure pour IntelliJ] prennent désormais en charge le même jeu de fonctionnalités.

### <a name="september-1-2015"></a>1er septembre 2015

Le Shared Computer Toolkit Azure pour Eclipse - septembre 2015 version inclut les améliorations suivantes :

* **Prise en charge pour les mises à jour Zoulou OpenJDK**. Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Mise à jour de Tomcat et les distributions de la jetée**. Les distributions de la jetée et Tomcat qui sont disponibles sur Microsoft Azure pour une utilisation avec le Shared Computer Toolkit Azure pour Eclipse ont été mis à jour. (Ces distributions permet aux développeurs de créer de développement rapide et tester des projets avec la Shared Computer Toolkit Azure pour Eclipse.
* **Prise en charge des références de Tomcat et jetée automatiquement mis à jour**. Outre les versions spécifiques de Tomcat et jetée disponibles sur Azure, les développeurs peuvent référencer maintenant une distribution dénommée la « dernière (mise à jour automatique) », qui met automatiquement à jour à la dernière distribution de chaque version majeure de la jetée ou Tomcat la prochaine fois que vos instances de rôles sont recyclés. (Le recyclage se produit automatiquement, mais les développeurs peuvent déclencher manuellement un recyclage via le portail Azure). Cette nouvelle fonctionnalité signifie que les développeurs n’ont pas de redéployer leur application à être en mesure de leur logiciel de serveur mis à jour. (
*  Cette fonctionnalité est actuellement destinée uniquement à des fins de développement et de test ou les applications non critiques et n’est pas recommandée pour la production.)
* **Affichage de l’Explorateur d’azure pour les objets BLOB, les files d’attente et les tables dans le stockage Azure**. Cela permet aux développeurs d’effectuer un ensemble de tâches courantes avec leurs artefacts de stockage directement à partir de l’IDE Eclipse. Par exemple : suppression, de transférer ou de télécharger des objets BLOB.

### <a name="august-1-2015"></a>1er août 2015

Le Shared Computer Toolkit Azure pour Eclipse - août 2015 version inclut les améliorations suivantes :

* **Gestion des clés application perspectives sur Instrumentation**. Cette mise à jour vous permet d’acquérir, de créer et de gérer vos clés d’instrumentation de perspectives de l’Application directement à partir de l’IDE Eclipse.
* **Le pilote JDBC de Microsoft 4.1 pour SQL Server**. Cette mise à jour prend en charge le tout dernier pilote JDBC de Microsoft SQL Server.
* **Version 2.7 du SDK Azure**. Cette mise à jour la plus récente du Kit de développement SDK Azure est un nouveau préalable pour le Shared Computer Toolkit lors de l’installation de Windows. (Notez que cela n’est pas nécessaire sur les systèmes d’exploitation autres que Windows).
* **Mise à jour de la prise en charge du Zoulou OpenJDK v7**. Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].

### <a name="may-1-2015"></a>1er mai 2015

Le Shared Computer Toolkit Azure pour Eclipse - mai 2015 version inclut les améliorations suivantes :

* **Améliorer l’interface utilisateur de sélection de serveur**. Cette version simplifie l’utilisation du Kit de ressources sur les systèmes d’exploitation autres que Windows.
* **Prise en charge pour les projets Maven**. Cette version prend en charge les projets Maven comme les applications, la boîte à outils peut déployer vers Azure et configurer les informations d’Application.
* **La version 2.6 du SDK Azure**. Cette mise à jour la plus récente du Kit de développement SDK Azure est un nouveau préalable pour le Shared Computer Toolkit lors de l’installation de Windows. (Notez que cela n’est pas nécessaire sur les systèmes d’exploitation autres que Windows).
* **Mise à niveau de déploiement au lieu de republier**. Si vous republiez un projet de déploiement lors de la version précédente est déjà en ligne, la boîte à outils utilise désormais des fonctionnalités de mise à niveau de déploiement d’Azure au lieu d’arrêter le déploiement précédent et le republier à partir de zéro, comme dans le passé. Cela permet à votre service cloud à s’exécuter sans interruption, autant que possible, en aidant à atteindre une haute disponibilité même au cours d’une mise à jour et accélère le processus de publication de nouveau.
* **Prise en charge de mettre à jour les plus récentes v8 Zoulou OpenJDK - 40**. Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].

### <a name="march-9-2015"></a>9 mars 2015

Le Shared Computer Toolkit Azure pour Eclipse - mars 2015 version inclut les améliorations suivantes :

* **Prise en charge de Mac, Ubuntu et autres distributions de Linux**. Cette version de la Shared Computer Toolkit Azure pour Eclipse ajoute la prise en charge de Mac OS et plusieurs plates-formes Unix, afin que les développeurs puissent installer la trousse à outils pour créer, configurer et publier des projets de Java pour les Services en nuage Azure (PaaS) à partir d’Eclipse en cours d’exécution sur les systèmes d’exploitation autres que Windows.

>[AZURE.NOTE] Cette fonctionnalité est en mode Aperçu, et il n’est pas recommandé pour une utilisation dans des environnements de production. Il n’y a aucune prise en charge client accord de niveau de Service (SLA), mais tous les commentaires sont apprécié et encouragés.

* **Plug-in de nouvelles idées d’Application**. Les développeurs sont désormais en mesure de configurer de TELEMETRIE automatique du serveur à l’aide d’idées d’Application dans Azure.
* **Automatisation du déploiement ant-ligne de commande**. Cette fonctionnalité permet aux développeurs d’automatiser la publication des versions plus récentes de leurs déploiements à l’aide de Ant en dehors d’Eclipse. Un script préalable généré est automatiquement configuré pour un projet après la première fois qu’il est déployé à partir d’Eclipse et les déploiements suivants peuvent utiliser le script pour automatiser entièrement les déploiements via la ligne de commande uniquement.
* **Disponibilité de tomcat et jetée sur Azure pour un déploiement plus simple et plus rapide**. Les développeurs peuvent maintenant référencer des différentes versions de Tomcat et jetée qui sont disponibles sur Azure directement à la place de devoir télécharger un serveur Java à leurs comptes (ou via le Shared Computer Toolkit), donc n’est pas nécessaire de télécharger un serveur Java pour les scénarios de mise en route rapides.
* **La méthode de raccourci pour les applications web Java publication de services cloud Azure**. Pour réduire la courbe d’apprentissage pour les scénarios de développement et de test simples, les développeurs peuvent publier maintenant les applications Java d’Azure. Au lieu d’avoir à passer par le processus de création et de configuration d’un projet de déploiement d’Azure, applications seront déployées avec une instance par défaut de Tomcat v8 et Zoulou JVM (OpenJDK).

### <a name="january-30-2015"></a>30 janvier 2015.

Le Shared Computer Toolkit Azure pour Eclipse - janvier 2015 version inclut les améliorations suivantes :

* **Prise en charge des IBM® WebSphere® Application Server Core de liberté**. Cette version ajoute le cœur de Liberty IBM WebSphere Application Server à la liste des serveurs d’application pris en charge à partir de laquelle la boîte à outils est en mesure de déployer vers Azure. Ce dernier ajout développe la liste actuelle des serveurs d’application qui sont prises en charge &quot;-de-l’emploi&quot; par le Shared Computer Toolkit, qui inclus déjà les différentes versions de Tomcat, jetée, JBoss et GlassFish.
* **Inclusion d’idées d’Application SDK**. Cette bibliothèque récemment publié le client API (v0.9.0) fait désormais partie du Package pour les bibliothèques d’Azure pour Java.
* **Mise à jour de Package pour les bibliothèques Azure pour Java**. Cette mise à jour inclut des bibliothèques d’Azure pour Java v0.7.0 et stockage Client API v2.0.0, ainsi que la v0.9.0 Application Insights SDK qui vient d’être publié.

### <a name="november-12-2014"></a>12 novembre 2014.

Le Shared Computer Toolkit Azure pour Eclipse - novembre 2014 version inclut les améliorations suivantes :

* **Prise en charge pour Azure SDK 2.5**. Cette mise à jour la plus récente du Kit de développement SDK Azure est un nouveau préalable pour le Shared Computer Toolkit.
* **Prise en charge pour la version mise à jour des v1.6, 1.7 et 1.8 de Zoulou OpenJDK packages**. Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Prise en charge pour les nouvelles tailles Standard D pour les services en nuage**, qui offrent augmente les performances et les ressources de la mémoire supplémentaire. Pour plus d’informations, voir les [tailles de Service de Cloud pour Azure et de la Machine virtuelle].

### <a name="october-17-2014"></a>17 octobre 2014.

Le Shared Computer Toolkit Azure pour Eclipse - octobre 2014 version inclut les améliorations suivantes :

* **Améliorations des performances dans la publier sur des scénarios de nuage**. Chargement des informations d’abonnement est beaucoup plus rapide lorsque les utilisateurs ont plusieurs comptes de stockage et les abonnements.
* **Prise en charge de la version mise à jour du package 1.8 Zoulou OpenJDK**. Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Prise en charge de la dépréciation d’anciennes versions de kits JDK de partie 3**. Les packages JDK désapprouvées n’affichent plus dans le menu déroulant pour les nouveaux projets de déploiement. Références packages JDK désapprouvées continuera à être en mesure de le faire pour l’heure en cours, mais il est recommandé de mettre à niveau ces projets pour s’appuient sur la dernière des projets existants.
* **Version de mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque d’API Java client**. Pour plus d’informations, consultez l' [API de Client Microsoft Azure].
* **Correctifs de bogues.** Cette version contient un certain nombre de divers correctifs de bogues basés sur les rapports sur les utilisateurs et les tests.

### <a name="august-5-2014"></a>5 août 2014.

Le Shared Computer Toolkit Azure pour Eclipse - août 2014 version inclut les améliorations suivantes

* **Prise en charge pour Azure SDK 2.4.** Les versions antérieures de la Shared Computer Toolkit Eclipse ne fonctionnera pas avec ce kit qui vient d’être lancé.
* **Mise à jour des versions de la version 1.6 Zoulou OpenJDK, packages 1.7 et 1.8.** Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Version mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque d’API Java client.** Pour plus d’informations, consultez l' [API de Client Microsoft Azure].
* **Prise en charge des plus tard publier le Format du fichier de paramètres.** Prise en charge a été ajoutée pour la version 2.0 du format de fichier de paramètres de publication.
* **Modifications de l’architecture derrière la publier sur la fonctionnalité de nuage.** Le Shared Computer Toolkit utilise désormais l’API de Client Microsoft Azure qui vient d’être lancé pour Java pour sa prise en charge de la publication-nuage.
* **Correctifs de bogues.** Cette version contient un certain nombre de résolutions de bogues demandées par l’utilisateur.

### <a name="june-12-2014"></a>12 juin 2014.

Le Shared Computer Toolkit Azure pour Eclipse - version de juin 2014 est une maintenance mise à jour mineure qui apporte les améliorations suivantes :

* **Prise en charge pour le 1.8 de package Zoulou OpenJDK.** Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Versions mises à jour de la version 1.6 Zoulou OpenJDK et 1,7 packages.** Pour plus d’informations, consultez la [page web de systèmes de Azul pour la OpenJDK Zoulou].
* **Version mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque d’API Java client.** Pour plus d’informations, consultez l' [API de Client Microsoft Azure].
* **Correctifs de bogues.** Cette version contient un certain nombre de résolutions de bogues demandées par l’utilisateur.

### <a name="april-4-2014"></a>4 avril 2014.

Le plug-in Eclipse - avril 2014 version Azure a publié. Il s’agit d’une mise à jour qui accompagnent la version 2.3 Azure SDK, qui est une condition préalable et seront téléchargés automatiquement lorsque vous installez le plug-in. Cette mise à jour comprend les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation dans la mesure où le février 2014 aperçu :

* **Prise en charge pour la version 2.3 du SDK Azure.** Le plug-in Eclipse - avril 2014 version Azure requiert Azure SDK 2.3. Lorsque vous utilisez le plug-in de nouveau, si vous n’avez pas encore Azure SDK 2.3, vous devrez autoriser son installation. N’utilisez pas d’Azure SDK 2.3 avec les versions antérieures du plug-in.
* **Mise à niveau d’applications sans le déploiement du package terminée.** Lorsque vous déployez des applications Java qui font partie de votre projet, le plug-in maintenant télécharge automatiquement les à votre compte de stockage sélectionné afin que vous pouvez mettre à jour et recycler les instances de rôle pour déployer les derniers bits d’application sans avoir à régénérer et redéployer le package entier.
* **Tomcat 8 est maintenant un serveur d’applications reconnues.** Si vous sélectionnez un répertoire d’installation de Tomcat 8 sur votre ordinateur dans l’onglet **serveur** de la boîte de dialogue du **Projet de déploiement d’Azure** , le plug-in sera désormais détecte automatiquement et être en mesure de déployer Tomcat 8 de manière automatique, similaire aux anciennes versions de Tomcat déjà dans la liste.
* **Mises à jour du package azul Zoulou OpenJDK : 47 de mise à jour de la mise à jour de version 1.7 51 et de la version 1.6.** Efficace avec cette version, mise à jour du système de Azul JDK ouvert du Zoulou v7 package 51 est disponible. En outre, les packages JDK ouvert du Zoulou v6 commençaient à être disponible, à partir de la mise à jour 47. Ces mises à jour sont en plus du package v7 de JDK ouvert du Zoulou précédemment disponible les 45 de mise à jour, mise à jour de 40 et mettre à jour les 25.
* **Prise en charge de taille A8 et A9 Microsoft Azure Virtual Machine.** Vous pouvez maintenant déployer un service en nuage à la mémoire haute A8 et tailles de A9 Virtual Machine. Pour plus d’informations sur ces formats de machine virtuelle, voir [l’ordinateur virtuel et des tailles de Service Cloud pour Azure].
* **Redirection automatique à partir de HTTP vers HTTPS pour les rôles de SSL activé.** Lorsque votre service cloud contient les rôles HTTPS uniquement, si la demande de l’utilisateur spécifie HTTP, il redirige automatiquement vers HTTPS. Il est inutile de créer un rôle distinct pour gérer les demandes HTTP.
* **Express émulateur utilisé pour émulation locale.** L’émulateur de Express Azure est désormais utilisé comme l’émulateur lors du débogage de vos applications localement.
* **Azure a été rebaptisé comme Microsoft Azure.** Les écrans de l’interface utilisateur est désormais reflètent que Azure a été rebaptisé et n’est plus appelé Azure.

### <a name="february-6-2014"></a>6 février 2014.

Le plug-in Azure pour Eclipse - février 2014 Aperçu a publié. Cette mise à jour inclut les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation dans la mesure où l’octobre 2013 aperçu :

* **Prise en charge pour le déchargement SSL.** Déchargement de Secure Sockets Layer (SSL) a été ajouté en tant que fonctionnalité, ce qui vous permet facilement d’activer la prise en charge du protocole sécurisé HTTPS (Hypertext Transfer) dans votre déploiement de Java sur Azure, sans vous obliger à configurer SSL sur votre serveur d’application Java. Ceci est particulièrement utile dans la Session affinité et/ou authentifiés de scénarios de communication. Par exemple, lorsque vous utilisez le filtre de Service de contrôle d’accès (ACS), qui est déjà pris en charge par la boîte à outils. Pour plus d’informations, consultez [Déchargement SSL] et explique [comment utiliser le déchargement SSL].
* **GlassFish 4 est maintenant un serveur d’applications reconnues.** Si vous sélectionnez un répertoire d’installation de GlassFish 4 sur votre ordinateur dans l’onglet **serveur** de la boîte de dialogue du **Projet de déploiement d’Azure** , le plug-in sera désormais détecte automatiquement et être en mesure de déployer GlassFish OSE 4 de manière automatique, semblable à la version 3 de OSE GlassFish déjà dans la liste.
* **Mise à jour de package azul Zoulou OpenJDK 45.** Efficace avec cette version, mise à jour du Zoulou (package v7 pour ouvrir JDK) du système de Azul 45 est désormais disponible ; C’est en plus de la mise à jour précédemment disponible 40 et mise à jour de 25.
* **Prise en charge des ports de point de terminaison de « auto » pour privé.** Vous pouvez définir un port privé sur automatique pour les points de terminaison d’entrée et des points de terminaison internes Azure permettent d’affecter un port à ce point de terminaison, automatiquement. Précédemment vous pouviez uniquement affecter un numéro de port spécifique.
* **Prise en charge pour personnaliser le nom du certificat (CN) dans l’interface utilisateur de création de certificat auto-signé.** Auparavant, il a été utilisé le même nom codé en dur pour tous les nouveaux certificats ; maintenant, vous pouvez spécifier votre propre nom de certificat afin de faire la distinction entre plusieurs certificats dans le portail Azure utilisé à des fins différentes.
* **Une barre d’outils azure :** La barre d’outils Azure a une mise à jour avec les modifications suivantes : 
    * ![][ic710876]Cette icône a été ajoutée pour le **Nouveau projet de déploiement d’Azure**.
    * ![][ic710877]Cette icône a été ajoutée sous la forme d’un raccourci à la boîte de dialogue de création de certificat auto-signé.
* **Prise en charge pour la taille de la Machine virtuelle de Azure A5.** Vous pouvez maintenant déployer un service en nuage à la taille de la Machine virtuelle de A5 élevée de la mémoire. Pour plus d’informations sur la taille de cette mémoire virtuelle, voir [l’ordinateur virtuel et des tailles de Service Cloud pour Azure].
* **Prise en charge pour Microsoft Windows Server 2012 R2.** Vous pouvez désormais sélectionner Windows Server 2012 R2 en tant que le système d’exploitation en nuage.

### <a name="october-22-2013"></a>22 octobre 2013

Le plug-in Azure pour Eclipse - octobre 2013 Preview a publié. Cette mise à jour inclut les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation dans la mesure où le 2013 septembre aperçu :

* **Prise en charge pour la version 2.2 du SDK Azure.** Le plug-in Eclipse - Azure octobre 2013 aperçu prend en charge Azure SDK 2.2. Le plug-in continuera de fonctionner avec Azure SDK 2.1 et installera automatiquement Azure SDK 2.2 si vous n’avez pas encore au moins Azure SDK 2.1 est installé.
* **Mise à jour de package azul Zoulou OpenJDK 40.** Comme annoncé pour le 2013 septembre aperçu, la permet maintenant de plug-in à l’aide d’un JDK fourni par le tiers directement sur Azure, sans avoir à télécharger vos propres JDK. Dans la version d’octobre 2013, mise à jour du Zoulou (package v7 pour ouvrir JDK) du système de Azul 40 est désormais disponible ; C’est en plus de la mise à jour initialement publié 25.
* **Lien de déploiement de cloud dans le journal d’activité.** Dans le journal d’activité Azure, votre déploiement a le statut **publié**, vous pouvez cliquer sur **publié** dans la mesure où il est désormais un lien vers votre déploiement ; votre déploiement sera ensuite ouvert dans votre navigateur. (L’état **publié** a été appelée précédemment **en cours d’exécution**.)
* **Sélection de système d’exploitation cible disponible à publier à temps.** La boîte de dialogue **publier sur Azure** contient un nouveau champ, **Système d’exploitation cible**, qui offre un moyen plus détectable vous permet de configurer votre système d’exploitation cible.
* **Auto-remplacer un précédent déploiement.** La boîte de dialogue **publier sur Azure** contient une nouvelle case à cocher **Remplacer un précédent déploiement**. Si cette option est activée, lorsque votre déploiement nouveau est publié il remplacera automatiquement le déploiement précédent ; Vous ne connaîtriez pas &quot;409 conflit&quot; problèmes lors de la publication vers le même emplacement sans la première annulation de la publication du déploiement précédent.
* **Jetée 9 est maintenant un serveur d’applications reconnues.** Si vous sélectionnez un répertoire d’installation jetée 9 sur votre ordinateur dans l’onglet **serveur** de la boîte de dialogue du **Projet de déploiement d’Azure** , le plug-in sera désormais détecte automatiquement et être en mesure de déployer la jetée 9 de manière automatique, similaire aux versions plus anciennes de la jetée déjà dans la liste.
* **Ajouter un rôle à partir du menu contextuel du projet.** Le menu contextuel du projet **Azure** contient désormais un nouvel élément de menu, **Ajouter un rôle**, qui fournit un moyen plus rapide et plus moyen détectable d’ajouter un nouveau rôle à votre projet Azure.
* **Une mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque de Java.** Ceci est basé sur la version 0.4.6 de l' [API de Client Microsoft Azure].

### <a name="september-25-2013"></a>25 septembre 2013

Le plug-in Azure pour Eclipse - septembre 2013 Preview a publié. Cette mise à jour comprend les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation depuis l’août 2013 aperçu :

* **Capacité à déployer le package Azul Zoulou OpenJDK disponible sur Azure.** Une nouvelle option a été ajoutée lors de la spécification JDK à utiliser avec votre déploiement d’Azure. À l’aide de cette option, vous pouvez déployer un package JDK de tiers directement sur le nuage Azure, sans devoir télécharger votre propre. Systèmes de azul fournit la première tel package Zoulou appelée, en fonction de l’OpenJDK, qui peut désormais être déployée à l’aide de cette option.
* **Une mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque de Java.** Ceci est basé sur la version 0.4.5 de l' [API de Client Microsoft Azure].

### <a name="august-1-2013"></a>1er août 2013

Le plug-in Azure pour Eclipse - août 2013 Preview a publié. Il s’agit d’une mise à jour qui accompagnent la version de la 2.1 Azure SDK, qui est une condition préalable et seront téléchargés automatiquement lorsque vous installez le plug-in. Cette mise à jour comprend les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation dans la mesure où le juillet 2013 aperçu :

* **Suppression des options à inclure le JDK local et le serveur de l’application locale dans le cadre du package de déploiement.** Téléchargement du JDK et serveur d’application à partir de stockage au cours du déploiement en nuage est préférable à l’incorporation de ces composants dans le package, depuis le téléchargement les résultats concernant les éléments de plus petite taille de package de déploiement, le temps de déploiement plus rapides et une maintenance plus facile. Par conséquent, les options à inclure le JDK et serveur d’applications dans le package de déploiement ont été supprimés. Les projets existants qui ont été configurés pour inclure le JDK local et un serveur d’application local comme partie du package de déploiement est automatiquement converti pour le téléchargement automatique de JDK et serveur d’application pour le stockage en nuage.
* **Prise en charge pour la version 2.1 du SDK Azure.** Le plug-in Azure pour Eclipse - août 2013 Preview nécessite Azure SDK 2.1. N’utilisez pas l’aperçu août 2013 avec les versions antérieures du Kit de développement Azure et ne pas utiliser d’Azure SDK 2.1 avec les versions antérieures du plug-in d’Azure pour Eclipse.
* **Prise en charge pour la version Kepler d’Eclipse.** Associé à cela, la nouvelle version minimale requise Eclipse IDE Indigo. Le plug-in Eclipse Azure est testé ne seront plus officiellement sur Helios.

### <a name="july-3-2013"></a>3 juillet 2013

Le plug-in Azure pour Eclipse - juillet 2013 Preview a publié. Cette mise à jour comprend les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation dans la mesure où le mai 2013 aperçu :

* **Possibilité de créer un nouveau compte de stockage.** Un bouton **Nouveau** a été ajouté à la boîte de dialogue **Ajouter un compte de stockage** . Cela vous permet de créer un compte de stockage dans le plug-in Eclipse, sans avoir à ouvrir une session sur le portail de gestion Azure. (Vous devez avoir un abonnement Azure pour utiliser cette fonctionnalité.) Pour plus d’informations sur la création d’un nouveau compte de stockage, consultez [pour créer un nouveau compte de stockage].
* **Nouveaux &quot;(auto)&quot; option compte de stockage utilisé pour le déploiement automatique du serveur et du JDK et la mise en cache.** Lors de l’utilisation de l’option **Télécharger automatiquement** pour JDK et serveur d’applications, vous pouvez maintenant spécifier **(automatique)** pour le compte de stockage et URL à utiliser lors du téléchargement de JDK et serveur d’application, ou lors de l’utilisation de la mise en cache d’Azure. Ensuite, ces fonctionnalités utilisera le même compte de stockage que celui que vous sélectionnez dans la boîte de dialogue **publier sur Azure** . Le didacticiel de [Création d’une Application Hello World pour Azure dans Eclipse] a été mis à jour pour utiliser la nouvelle option **(auto)** .
* **Capacité de définir vos points de terminaison de service Azure.** Spécifier les points de terminaison de service qui déterminent que si votre application est déployée sur et gérée par la plateforme Azure globale, Azure exploités par 21Vianet en Chine ou privé plateforme Azure. Pour plus d’informations, consultez [Points de terminaison de Service Azure].
* **Déploiements à grandes échelle peuvent spécifier une ressource de stockage local.** Dans le cas où votre déploiement est trop volumineux pour être contenus dans le dossier approot par défaut, vous pouvez désormais spécifier une ressource de stockage local en tant que la destination du déploiement de votre JDK et serveur d’application. Pour plus d’informations, consultez [Déploiement de déploiements de grande envergure].
* **Prise en charge des tailles de A6 et A7 Azure Virtual Machine.** Vous pouvez désormais déployer un service en nuage à la mémoire haute A6 et A7 Virtual Machine tailles. Pour plus d’informations sur ces formats, reportez-vous à la section [ordinateur virtuel et des tailles de Service Cloud pour Azure].
* **Une mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque de Java.** Ceci est basé sur la version 0.4.4 de l' [API de Client Microsoft Azure].

### <a name="may-1-2013"></a>1er mai 2013

Le plug-in Eclipse - Azure peut 2013 Preview a publié. Il s’agit d’une mise à jour majeure qui accompagnent la version de Azure SDK 2.0, qui est une condition préalable et seront téléchargés automatiquement lorsque vous installez le plug-in. Cette version inclut les nouvelles fonctionnalités, des correctifs de bogues et certaines améliorations pilotée par des commentaires sur la facilité d’utilisation dans la mesure où le février 2013 aperçu :

* **Téléchargement automatique du JDK et serveur d’applications et de déploiement du stockage Azure.** Une nouvelle option qui télécharge le JDK sélectionné et le serveur de l’application, lorsque cela est nécessaire, à un compte de stockage Azure spécifié automatiquement et de déployer ces composants à partir de là, au lieu d’incorporer dans le package de déploiement ou avoir le téléchargement de l’utilisateur puis manuellement. Cette fonctionnalité souvent demandée peut améliorer considérablement la facilité de déploiement des composants serveur et JDK, en particulier pour les utilisateurs débutants. Pour une vue d’ensemble qui utilise ces options, reportez-vous à la section [Création d’une Application Hello World pour Azure dans Eclipse].
* **Possibilité pour les comptes de stockage référence plus facilement (via un contrôle de liste déroulante) et le suivi du compte de stockage centralisé.** Cela s’applique à plusieurs fonctionnalités qui s’appuient sur le stockage, par exemple le JDK et de déploiement des composants de serveur et de la mise en cache. Pour plus d’informations, consultez la [Liste des comptes du stockage Azure].
* **Installation simplifiée de l’accès distant dans l’Assistant Publier sur Cloud.** Il vous suffit de faire est de type dans un nom d’utilisateur et le mot de passe pour activer l’accès distant, ou laissez le champ vide pour conserver l’accès à distance est désactivé.
* **Une mise à jour du Package pour les bibliothèques d’Azure pour la bibliothèque de Java.** Ceci est basé sur la version 0.4.2 de l' [API de Client Microsoft Azure].
* **Prise en charge des sessions persistantes sur Windows Server 2012.** Sessions persistantes fonctionnaient uniquement sur Windows Server 2008 R2, à présent à la fois l’affinité de système d’exploitation cible prise en charge de session en nuage.
* **Améliorations de performances de chargement de package.** Même lorsque le JDK et serveur d’application sont incorporées dans le package de déploiement, la partie du téléchargement du processus de déploiement peut être environ deux fois plus rapide par rapport aux versions précédentes.

### <a name="february-8-2013"></a>8 février 2013

Le plug-in Azure pour Eclipse - février 2013 Preview a publié. Il s’agit d’une mise à jour mineure qui inclut les correctifs de bogues, améliorations pilotée par des commentaires et des nouvelles fonctionnalités comme aperçu de novembre 2012 :

* Prise en charge pour les kits JDK, serveurs d’applications, de déploiement et arbitraire autres composants d’Azure public ou privé blob téléchargements de stockage au lieu de les inclure dans le package de déploiement lors du déploiement sur le cloud.
* Possibilité de modifier l’ordre dans lequel les composants définis par l’utilisateur d’un rôle sont traités, grâce à l’ajout des boutons **Déplacer vers le haut** et **Déplacer vers le bas** dans la section **composants** , des **Propriétés de rôle d’Azure**.
* Une mise à jour de la bibliothèque de **Package pour les bibliothèques d’Azure pour Java** , en fonction de la version 0.4.0 de l' [API de Client Microsoft Azure].

### <a name="november-5-2012"></a>5 novembre 2012

Le plug-in Azure pour Eclipse - novembre 2012 Aperçu a publié. Il s’agit d’une mise à jour majeure qui inclut un certain nombre de nouvelles fonctionnalités, ainsi que les autres correctifs et améliorations pilotée par des commentaires dans la mesure où le septembre 2012 aperçu :

* Prise en charge de Microsoft Windows Server 2012 en tant que le système d’exploitation en nuage.
* Prise en charge situé Azure prise en charge de la mise en cache pour les clients de memcached.
* Inclusion des bibliothèques Apache Qpid JMS client permettant de tirer parti de la messagerie AMQP d’Azure.
* Un Assistant de **Nouveau projet** amélioré, avec une nouvelle page à la fin qui fournit aux utilisateurs la possibilité d’activer rapidement plusieurs fonctionnalités clés communes dans leur projet : sessions persistantes, la mise en cache et le débogage distant.
* Réduction automatique des instances de rôle 1 lors de l’exécution dans l’émulateur de calcul, pour éviter des conflits de liaison de port entre les instances de serveur.

### <a name="september-28-2012"></a>28 septembre 2012

Le plug-in Azure pour Eclipse - septembre 2012 Aperçu a publié. Cette mise à jour de service comprend un certain nombre de correctifs supplémentaires depuis l’août 2012 prévisualiser, ainsi que des améliorations de commentaires pilotée par la facilité d’utilisation dans les fonctionnalités existantes :

* Prise en charge de Windows 8 de Microsoft et Microsoft Windows Server 2012 en tant que le système d’exploitation de développement, résolution des problèmes qui vous empêchaient précédemment le plug-in de fonctionner correctement sur ces systèmes d’exploitation.
* Prise en charge améliorée pour la spécification des plages de ports de point de terminaison.
* Correctifs de bogues liés aux chemins d’accès contenant des espaces.
* Améliorations des menu contextuel rôle pour accélérer l’accès aux paramètres de configuration spécifiques au rôle.
* Perfectionnements mineurs dans l’Assistant **publication vers le cloud** et un certain nombre de résolutions de bogues supplémentaires.

### <a name="august-28-2012"></a>28 août 2012

Le plug-in Azure pour Eclipse - août 2012 Aperçu a publié. Ce service de mise à jour inclut des correctifs supplémentaires depuis le juillet 2012 prévisualiser, ainsi que plusieurs améliorations pilotée par des commentaires sur la facilité d’utilisation des fonctionnalités existantes :

* Dans la boîte de dialogue filtre de Services de contrôle de l’accès Azure :
    * **Option pour incorporer le certificat de signature** dans le fichier de votre application WAR, de simplifier le déploiement de cloud.
    * **Option permettant de créer un certificat auto-signé** dans l’interface utilisateur du filtre ACS. Pour plus d’informations sur le filtre de Services de contrôle de l’accès Azure, consultez [comment authentifier des utilisateurs Web avec Azure Access contrôle Service à l’aide d’Eclipse].
* Dans l’Assistant projet de déploiement d’Azure (s’applique également à la page de propriétés de Configuration du serveur du rôle) :
    * **La détection automatique de l’emplacement de JDK** sur votre ordinateur (vous pouvez remplacer si vous le souhaitez).
    * **La détection automatique du type de serveur** lorsque vous sélectionnez le répertoire d’installation d’application server.

### <a name="july-15-2012"></a>15 juillet 2012

Le plug-in Azure pour Eclipse - juillet 2012 aperçu, qui résout un certain nombre des bogues de priorité le plus élevés constaté ou signalés par les utilisateurs après la version de juin 2012, a publié. Il s’agit d’une mise à jour du service, aucuns nouvelles fonctionnalités ne sont contenues.

### <a name="june-7-2012"></a>7 juin 2012

Plug-in d’Azure pour Eclipse - version CTP de juin 2012 a publié. Les nouvelles fonctionnalités incluent :

* **Assistant Nouveau projet de déploiement d’Azure :** Vous pouvez sélectionner votre JDK, serveur d’applications Java et les applications Java directement dans l’interface utilisateur d’Assistant amélioré. Inclus dans la liste-de-l’emploi des configurations de serveurs de sont Tomcat 6, Tomcat 7, GlassFish OSE 3, 7 de la jetée, jetée 8, JBoss 6 et 7 de JBoss (autonome). En outre, vous pouvez personnaliser la liste des configurations de serveur. Cette amélioration de l’interface utilisateur est une alternative à faire glisser et déplacer des fichiers compressés et copie via des scripts de démarrage, qui a été précédemment l’approche principal. Cette méthode fonctionne toujours, mais sera probablement utilisée uniquement pour les scénarios plus avancés.
* **Page de propriétés de rôle de Configuration du serveur :** Vous permet de basculer aisément les kits JDK, serveurs d’applications Java et les applications associées à votre déploiement après avoir créé le projet. Pour plus d’informations, consultez [Propriétés de configuration de serveur].
* **&quot;Publier sur le nuage&quot; Assistant :** fournit un moyen facile de déployer votre projet vers Azure directement à partir d’Eclipse, automatisant le manuel précédemment lourdes de l’extraction des informations d’identification, l’ouverture de session sur le portail de gestion Azure, télécharger un package, l’etc.. Pour obtenir un exemple de la façon de déployer directement de votre projet vers Azure, consultez [Création d’une Application Hello World pour Azure dans Eclipse].
* **Une barre d’outils azure :** Une barre d’outils Azure est maintenant disponible dans Eclipse, qui contient des boutons qui appellent les fonctions suivantes :
    * ![][ic710879]**Exécution dans l’émulateur Azure**: exécute votre projet dans l’émulateur.
    * ![][ic710880]**Réinitialiser un émulateur Azure**: réinitialise l’émulateur.
    * ![][ic710881]**Générer le lot pour Azure Cloud**: Compile votre package de déploiement.
    * ![][ic710876]**Nouveau projet de déploiement d’Azure**: crée un nouveau projet de déploiement d’Azure.
    * ![][ic710882]**Publier sur Azure Cloud**: publie votre projet sur Azure.
    * ![][ic710883]**Annuler la publication**: supprime de votre déploiement.
    * La plupart de ces boutons de la barre d’outils Azure sont utilisées lors de la [Création d’une Application Hello World pour Azure dans Eclipse].
* **Des bibliothèques azure pour Java :** Désormais disponible en tant que partie du Package unique pour les bibliothèques d’Azure pour la bibliothèque Java dans Eclipse, qui accompagne l’installation de plug-in et contenant toutes les dépendances nécessaires ainsi. Il suffit d’ajouter une référence à la bibliothèque de votre projet Java et vous n’avez pas besoin de télécharger quoi que ce soit séparément. Pour plus d’informations, consultez [installer le Shared Computer Toolkit Azure pour Eclipse].
* **4.0 de pilote JDBC de Microsoft pour SQL Server de disponibles pendant l’installation du plug-in :** Lors de l’installation du plug-in de nouveau, la version la plus récente du pilote JDBC Microsoft SQL Server peut être installée.
* **Disponibles pendant l’installation du plug-in de filtre de Service du contrôle accès azure :** Ce nouveau composant, inclus en tant que dans la boîte à outils, une bibliothèque d’Eclipse permet à votre application de web de Java profiter en toute transparence d’authentification Azure Access Control Service (ACS) à l’aide de différents fournisseurs d’identité, tels que Yahoo!, Google et Live.com. Vous n’aurez pas à écrire de logique d’authentification vous-même simplement configurer quelques options et laisser le filtre faire l’essentiel de permettre aux utilisateurs de se connecter à l’aide d’ACS. Vous pouvez vous concentrer uniquement sur l’écriture du code qui permet aux utilisateurs l’accès aux ressources en fonction de son identité, tel qu’il est renvoyé à votre application par le filtre à l’intérieur de l’objet de la demande. Pour un didacticiel sur l’utilisation du filtre de l’ACS, consultez [comment authentifier des utilisateurs Web avec Azure Access contrôle Service à l’aide d’Eclipse].
* **La détection automatique de la condition préalable d’Azure SDK 1.7 :** Lorsque vous créez un projet de déploiement d’Azure, Azure SDK 1.7 sera automatiquement téléchargé s’il n’est pas déjà installé.
* **Points de terminaison d’instance :** Autorise l’accès du point de terminaison direct port pour la communication avec des instances de rôle d’équilibrage de la charge. Les points de terminaison instance peuvent être ajoutés par le biais de l’interface utilisateur, disponible par le biais de la page de [Propriétés des points de terminaison] de points de terminaison. Cela permet d’activer le débogage à distance et diagnostics JMX spécifique calculent les instances en cours d’exécution dans le nuage dans les scénarios avec multi-instance des déploiements. 
* **Composants de l’interface utilisateur :** Elle facilite aux utilisateurs avancés de définir des dépendances de projet entre les rôles d’Azure dans le projet et d’autres ressources externes, tels que des projets d’application Java ; facilite également décrire leur logique de déploiement. Pour plus d’informations, consultez [Propriétés de composants].
* **Mise à niveau automatique de versions de projets :** Lorsque vous ouvrez un espace de travail a Azure projet créé avec une version précédente du plug-in, les anciens projets seront affichera dans Eclipse fermé, car les versions antérieures de projets ne sont pas compatibles avec la nouvelle version. Si vous tentez d’ouvrir l’un de ces anciens projets, un Assistant de mise à niveau démarre. Si vous acceptez la mise à niveau, un nouveau projet, avec **_Upgraded** est ajouté au nom, sera créé et mis à jour automatiquement pour fonctionner avec la nouvelle version. Vous pouvez renommer le nouveau projet selon vos besoins. Dans le cadre de la mise à niveau, votre projet d’origine ne sera pas modifié (et reste fermé).

### <a name="december-10-2011"></a>10 décembre 2011

Plug-in d’Azure pour Eclipse - version CTP de décembre 2011 a publié. Les nouvelles fonctionnalités incluent :

* **L’affinité de session (&quot;sessions persistantes&quot;) prend en charge :** Aidant à activer avec état, ordonné en clusters d’applications Java avec une seule case à cocher. Pour plus d’informations, consultez [l’Affinité de la Session].
* **Préfabriqué des exemples de scripts de démarrage :** Pour les serveurs Java les plus populaires (Tomcat, jetée, JBoss, GlassFish), que vous pouvez simplement copier-coller à partir de votre projet répertoire d’exemples dans votre script de démarrage.
* **Sortie du démarrage émulateur en temps réel :** Vous pouvez désormais regarder l’exécution de toutes les étapes à partir de votre script de démarrage dans une fenêtre de console dédiée, en vous montrant la progression et les erreurs dans votre script telle qu’elle est exécutée par Azure.
* **De surveillance java.exe automatique, léger :** Qui forcera un recyclage de rôle lorsque java.exe s’arrête en cours d’exécution, en utilisant un script léger, préfabriqué automatiquement inclus dans votre déploiement.
* **Interface utilisateur de configuration de débogage de l’application Java distant :** Vous permet de facilement activer le débogueur distant d’Eclipse accéder à votre application Java en cours d’exécution dans l’émulateur ou le nuage Azure, pour vous permettre de parcourir et de déboguer votre code Java en temps réel. Pour plus d’informations, consultez [Débogage d’Applications Azure dans Eclipse].
* **Interface utilisateur de configuration de ressources stockage local :** Par conséquent, vous n’avez plus à configurer des ressources locales en manipulant le XML directement. Cette fonctionnalité vous permet également d’accéder au chemin de fichier efficace de vos ressources locales après que qu’il est déployé via une variable d’environnement que vous pouvez référencer directement à partir de votre script de démarrage à. Pour plus d’informations, consultez [Propriétés de stockage Local].
* **Configuration de variable d’environnement interface utilisateur :** Par conséquent, vous n’avez plus à définir les variables d’environnement via la modification manuelle de la configuration XML. Pour plus d’informations, consultez [Propriétés de variables d’environnement].
* **Pilote JDBC pour SQL Azure :** Est installé via le plug-in comme une bibliothèque Eclipse intégrée, l’activation de programmation plus simple par rapport à SQL Azure. 
* **Accès rapide de menu contextuel à l’interface utilisateur de configuration de rôle**: simplement avec le bouton droit sur le dossier rôles, puis cliquez sur **Propriétés**.
* **Icônes du dossier de projet et de rôle personnalisé Azure :** Pour une meilleure visibilité et faciliter la navigation dans votre espace de travail et un projet.

## <a name="see-also"></a>Voir aussi ##

Pour plus d’informations sur les boîtes à outils Azure pour IDE de Java, consultez les liens suivants :

- [Azure Shared Computer Toolkit pour Éclipse]
  - [L’installation de la Shared Computer Toolkit Azure pour Éclipse]
  - [Créer une application de Web de Hello World pour Azure dans Éclipse]
  - *Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Eclipse (Cet Article)*
- [Azure Shared Computer Toolkit pour IntelliJ]
  - [L’installation de la Shared Computer Toolkit Azure pour IntelliJ]
  - [Créer une application de Web de Hello World pour Azure dans IntelliJ]
  - [Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

<!-- URL List -->

[Azure Shared Computer Toolkit pour Éclipse]: ./azure-toolkit-for-eclipse.md
[Azure Shared Computer Toolkit pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application de Web de Hello World pour Azure dans Éclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application de Web de Hello World pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: ./azure-toolkit-for-eclipse-installation.md
[L’installation de la Shared Computer Toolkit Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Page web de systèmes de azul pour la OpenJDK Zoulou]: http://go.microsoft.com/fwlink/?LinkId=402457
[Points de terminaison des services Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Liste des comptes de stockage Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Propriétés des composants]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Débogage d’Applications Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Déploiement des déploiements à grandes échelle]: http://go.microsoft.com/fwlink/?LinkID=699536
[Propriétés des points de terminaison]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Propriétés de variables d’environnement]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Plug-in d’HDInsight outils Eclipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Comment faire pour authentifier les utilisateurs Web avec le Service de contrôle d’accès de Azure utilisant Eclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Comment faire pour utiliser le déchargement SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés de stockage local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API Client de Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Propriétés de configuration de serveur]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Affinité de session]: http://go.microsoft.com/fwlink/?LinkID=699548
[Le déchargement SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Pour créer un nouveau compte de stockage]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Machine virtuelle et les tailles de Service Cloud pour Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png
