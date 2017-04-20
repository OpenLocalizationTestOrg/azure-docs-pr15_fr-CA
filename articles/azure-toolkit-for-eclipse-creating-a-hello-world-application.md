<properties
    pageTitle="Créer un Service en nuage Hello World pour Azure dans Éclipse"
    description="Apprenez à créer une application Hello World simple à l’aide de la Shared Computer Toolkit Azure pour Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Créer un Service en nuage Hello World pour Azure dans Éclipse #

Les étapes suivantes vous montrent comment créer et déployer une application de base JSP vers Azure à l’aide de la Shared Computer Toolkit Azure pour Eclipse. Un exemple JSP est affiché par souci de simplicité, mais une procédure très similaire serait appropriée pour un servlet Java, comme déploiement d’Azure est concerné.

L’application sera semblable au suivant :

![][ic600360]

## <a name="prerequisites"></a>Conditions préalables ##

* Un Java Developer Kit (JDK), v 1.7 ou version ultérieure.
* Eclipse IDE pour les développeurs Java EE, Indigo ou une version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat, GlassFish, serveur d’applications JBoss, jetée ou IBM® WebSphere® Liberty Application Server Core.
* Un abonnement Azure, qui peut être acquis à partir de <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Shared Computer Toolkit pour Eclipse Azure. Pour plus d’informations, consultez [installer le Shared Computer Toolkit Azure pour Eclipse][].

## <a name="to-create-a-hello-world-application"></a>Pour créer une application Hello World ##

Tout d’abord, nous commencerons à la création d’un projet Java.

*  Démarrer Eclipse et dans le menu, cliquez sur **fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet de Web dynamique**. (Si vous ne voyez pas **Les projets Web dynamique** répertorié comme un projet disponible après avoir cliqué sur **fichier**, **Nouveau**, puis effectuez les opérations suivantes : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur le **projet...**, développer **Web**, cliquez sur **Le projet Web dynamique**et cliquez sur **suivant**.)
*  Pour les besoins de ce didacticiel, nommez le projet **MyHelloWorld**. (Assurez-vous que vous utilisez ce nom, les étapes suivantes de ce didacticiel attendent votre fichier WAR d’être nommé MyHelloWorld). L’écran s’affiche semblable à la suivante : ![][ic589576]
* Cliquez sur **Terminer**.
* Dans l’affichage de l’Explorateur de projet de Eclipse, développez **MyHelloWorld**. Droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.
* Dans la boîte de dialogue **Nouveau fichier JSP** , nommez fichier **index.jsp**. Conserver le dossier parent en tant que **MyHelloWorld/contenu Web**, comme illustré dans le code suivant :  ![][ic659262]
* Dans la boîte de dialogue **Sélectionner un modèle JSP** , pour les besoins de ce didacticiel, sélectionnez **Nouveau fichier JSP (html)** et cliquez sur **Terminer**.
* Lorsque le fichier index.jsp s’ouvre dans Eclipse, ajouter dans le texte à afficher de façon dynamique **Hello World !** dans les fichiers `<body>` élément. Votre mise à jour `<body>` contenu doit apparaître comme suit :
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Enregistrer les index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Pour déployer votre application dans Azure, la méthode rapide et simple ##

Dès que vous disposez d’une application de web Java prête à tester, vous pouvez utiliser le raccourci suivant d’essayer cette opération directement sur le nuage Azure.

1. Dans l’Explorateur de projet de Eclipse, cliquez sur **MyHelloWorld**.
1. Dans la barre d’outils Eclipse, cliquez sur le bouton déroulant **Publier** et puis cliquez sur **Publier en tant que Service de Cloud Azure**
    ![][publishDropdownButton]
1. Si vous publiez cette application Azure pour la première fois et que vous n’avez pas créé un projet de déploiement d’Azure pour cette application avant, un projet de déploiement d’Azure est automatiquement créé pour vous. Vous devez voir l’invite suivante s’affiche, qui répertorie également le package JDK et le serveur d’applications qui est automatiquement déployée pour exécuter votre application.
    ![][ic789598]

    Cette approche de raccourci permet un moyen rapide et facile tester votre application dans Azure sans avoir à configurer un serveur spécifique ou un JDK qui est différente de la valeur par défaut. Si vous êtes satisfait avec les paramètres par défaut, vous pouvez cliquer sur **OK** pour continuer avec les étapes suivantes.
    Toutefois, si vous souhaitez modifier le JDK ou serveur d’applications à utiliser pour votre application, vous pouvez le faire ultérieurement en modifiant le projet de déploiement d’Azure qui a été créé automatiquement pour vous, ou cliquez sur **Annuler** et lisez la **section projets de déploiement sur Azure** de ce didacticiel.
1. Dans la boîte de dialogue **publier sur Azure** :
    1. S’il n’y a aucun abonnement pour sélectionner dans la liste **d’abonnements** encore, procédez comme suit pour importer les informations de votre abonnement :
        1. Cliquez sur **Importer depuis un fichier de paramètres de publication**.
        1. Dans la boîte de dialogue **Informations sur l’abonnement importation** , cliquez sur **Télécharger un fichier de paramètres de publication**. Si vous n’êtes pas encore connecté à votre compte Azure, vous serez invité à ouvrir une session. Puis vous serez invité à enregistrer un Azure publier le fichier de paramètres. L’enregistrer sur votre ordinateur local.
        1. Toujours dans la boîte de dialogue **Informations sur l’abonnement importation** , cliquez sur le bouton **Parcourir** , sélectionnez le fichier de paramètres de publication que vous avez enregistré localement à l’étape précédente, puis cliquez sur **Ouvrir**. Votre écran doit ressembler à ce qui suit : ![][ic644267]
        1. Cliquez sur **OK**.
    1. Pour l' **abonnement**, sélectionnez l’abonnement que vous souhaitez utiliser pour votre déploiement.
    1. Pour le **compte de stockage**, sélectionnez le compte de stockage que vous souhaitez utiliser, ou cliquez sur **Nouveau** pour créer un nouveau compte de stockage.
    1. **Nom du Service**, sélectionnez le service en nuage que vous souhaitez utiliser, ou cliquez sur **Nouveau** pour créer un nouveau service en nuage.
    1. Pour **Système d’exploitation cible**, sélectionnez la version du système d’exploitation que vous souhaitez utiliser pour votre déploiement.
    1. Pour un **environnement cible**, pour les besoins de ce didacticiel, sélectionnez **Staging**. (Lorsque vous êtes prêt à déployer sur votre site de production, vous pouvez modifier à la **Production**.)
    1. Facultatif : Vérifiez que l’option **Remplacer le déploiement précédent** est activée si vous souhaitez que votre nouveau déploiement d’écraser automatiquement le déploiement précédent. Lorsque vous activez cette option, vous ne pas les problèmes d’expérience « 409 conflit » lors de la publication vers le même emplacement.
        Notez que la boîte de dialogue **publier sur Azure** contienne une section pour **l’Accès distant**. Par défaut, accès à distance n’est pas activé, et nous n’activera pas il pour cet exemple. Pour activer l’accès distant, vous devez entrer un nom d’utilisateur et le mot de passe à utiliser lors de la connexion à distance. Pour plus d’informations sur l’accès distant, consultez [Activation de l’accès distant pour les déploiements d’Azure dans Eclipse][].
        La boîte de dialogue **publier sur Azure** s’affiche semblable à la suivante : ![][ic719488]
1. Cliquez sur **Publier** pour publier dans l’environnement intermédiaire.
    Lorsque vous êtes invité à effectuer une génération complète, cliquez sur **Oui**. Cette opération peut prendre plusieurs minutes pour la première génération.
    Un **Journal d’activité Azure** s’affichera dans votre section de vues Eclipse avec onglets.
    ![][ic719489]
   Vous pouvez utiliser ce journal, ainsi que l’affichage de la **Console** , pour voir l’avancement de votre déploiement. Une alternative consiste à ouvrir une session sur le [Portail de gestion Azure][]et permet de surveiller l’état de la section de **Services en nuage** .
1. Lorsque le déploiement est déployé avec succès, le **Journal d’activité Azure** affiche un état **publié**. Cliquez sur **publié**, comme illustré dans l’image suivante, et une instance de votre déploiement s’ouvre dans votre navigateur.
    ![][ic719490]

Comme il s’agissait d’un déploiement dans un environnement intermédiaire, le nom DNS sera de la forme http://&lt;*guid*&gt;. cloudapp.net et la volonté d’URL contient le nom DNS et un suffixe pour votre application. Par exemple, http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (La partie **MyHelloWorld** est sensible à la casse). Vous pouvez également voir le nom DNS si vous cliquez sur le nom de déploiement dans le portail de gestion de plateforme Azure (dans la partie des Services en nuage du portail de gestion).

Bien que cette procédure a pour un déploiement dans l’environnement intermédiaire, un déploiement en production suit la même procédure, sauf dans la boîte de dialogue **publier sur Azure** , sélectionnez **Production** au lieu de **la zone de transit** pour l' **environnement cible**. Un déploiement en production se traduit par une URL basée sur le nom DNS de votre choix, plutôt qu’un GUID que celui utilisé pour la mise en attente.

>[AZURE.WARNING] À ce stade, vous avez déployé votre application Azure vers le nuage. Cependant, avant de continuer, savoir qu’une application déployée, même si elle n’est pas en cours d’exécution, continue à accumuler les heures facturables pour votre abonnement. Par conséquent, il est extrêmement important que vous supprimez des déploiements indésirables de votre abonnement Azure.

## <a name="about-azure-deployment-projects"></a>À propos des projets de déploiement d’Azure ##

Pour déployer une ou plusieurs applications Java vers Azure, un projet de déploiement d’Azure est nécessaire. Il joue le rôle de « package » nécessitant à encapsuler afin d’être publié sur Azure dans vos applications.

Outre les informations sur vos applications, un projet de déploiement d’Azure contient également des informations sur d’autres composants clés de votre déploiement, plus important encore : le conteneur de serveur d’applications pour exécuter votre application web dans et exécuter sur le runtime Java. Azure prend en charge un large éventail de Java Runtime et vous pouvez choisir parmi les serveurs d’applications Java.

Bien que l’exemple utilisé ici est grandement simplifiée à des fins pédagogiques, un projet de déploiement d’Azure peut également contenir d’autres informations de configuration importantes qui vous permet de créer des services en nuage presque arbitrairement complexes, évolutive, hautement disponible, à plusieurs niveaux avec vos applications. Vous pouvez activer **l’affinité (« sticky sessions ») de la session**, **rapide de mise en cache**, **le débogage distant**, **le déchargement SSL**, **routage/port du pare-feu**, **accès distant**et un certain nombre d’autres fonctionnalités puissantes.

Si vous avez terminé la section précédente de ce didacticiel (« pour déployer votre application dans Azure, la méthode rapide et simple »), vous verrez maintenant un nouveau projet de déploiement d’Azure dans l’Explorateur de projet généré automatiquement et nommé «**MyHelloWorld_onAzure**».

Vous pourriez également avoir démarré ce didacticiel en créant tout d’abord un projet de déploiement d’Azure vide et en ajoutant vos applications lui. Il est plus long processus, mais ce qui vous donne davantage de contrôle sur la configuration initiale à partir du début.

Pour créer un nouveau projet de déploiement d’Azure à partir de zéro, cliquez sur le bouton **Nouveau projet de déploiement d’Azure** ![][ic710876].

Que si vous travaillez avec un projet de déploiement d’Azure déjà existant, ou créer une de toutes pièces, vous ne pouvez modifier ses paramètres de configuration et les composants, tels que le JDK ou le serveur d’application, tout aussi facilement à tout moment.

Pour modifier le JDK, ou le serveur d’applications ou la liste des applications dans un projet de déploiement d’Azure existant :

1. Développez le nœud de projet (par exemple, **MyHelloWorld_onAzure**) dans l’Explorateur de projets
2. Cliquez sur **WorkerRole1**
3. Développer le sous-menu **Azure** dans le menu contextuel
4. Cliquez sur **Configuration du serveur**

Indépendamment de si vous avez démarré ces étapes de configuration du serveur en modifiant un projet de déploiement d’Azure existant comme illustré ci-dessus, ou créer une de toutes pièces, vous verrez le même type de boîtes de dialogue qui vous permet de configurer votre JDK, les composants de serveur et d’application. Pour en savoir plus modifier les paramètres dans ces boîtes de dialogue, par exemple pour modifier le JDK, le serveur d’application et d’ajouter ou de supprimer des applications dans un déploiement, consultez l’article de [Propriétés de configuration de serveur][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Windows uniquement : pour déployer votre application dans l’émulateur de calcul ##

>[AZURE.NOTE] L’émulateur Azure n’est disponible que sous Windows. Ignorer cette section si vous utilisez un système d’exploitation autre que Windows.

Si vous avez créé un nouveau projet de déploiement d’Azure, suivant la procédure décrite plus haut, soit implicitement, par la publication de votre application Azure, le JDK et les applications serveurs ont été configurés pour le nuage, mais pas pour l’émulation locale. Pour préparer votre projet de test dans l’émulateur local, procédez comme suit :

1. Dans l’Explorateur de projet de Eclipse, cliquez sur **MyHelloWorld_onAzure**.
1. Avec le bouton droit sur **WorkerRole1**.
1. Développez le sous-menu **Azure** dans le menu contextuel.
1. Cliquez sur **Configuration du serveur**.
1. Sous l’onglet **JDK** , vérifiez si la boîte à outils est préconfigurée par défaut local JDK pour vous. Dans le cas contraire, ou si vous souhaitez modifier les paramètres par défaut supposés, vérifiez que la case à cocher **utiliser le JDK à partir de ce chemin d’accès de fichier pour tester localement** est activée et l’emplacement d’installation de JDK que vous souhaitez utiliser est spécifié. Si vous souhaitez modifier, cliquez sur le bouton **Parcourir** et utilisez le contrôle de navigation, sélectionnez l’emplacement du répertoire du JDK à utiliser.
1. Cliquez sur l’onglet **serveur** .
1. Dans la zone de texte **chemin d’accès au serveur Local** en bas de la boîte de dialogue, entrez le chemin d’accès d’un serveur installé localement qui correspond au type et au numéro de version principale du serveur sélectionné en haut de la boîte de dialogue, sous la case à cocher **déployer un serveur de ce type** . Si vous souhaitez utiliser un autre type ou la version principale du serveur d’applications, modifiez d’abord la sélection sous cette case à cocher.
1. Cliquez sur **OK**.
1. Dans la barre d’outils Eclipse, cliquez sur le bouton **exécuter dans Azure émulateur** , ![][ic710879]. Si le bouton **exécuter dans Azure émulateur** n’est pas activé, assurez-vous que **MyHelloWorld_onAzure** est sélectionné dans l’Explorateur de projet de Eclipse et que projet Explorateur de Eclipse a le focus en tant que la fenêtre active. Ce sera tout d’abord démarrer une génération complète de votre projet et puis lancez votre application de web Java dans l’émulateur de calcul. (Notez qu’en fonction des caractéristiques de performances de votre ordinateur, la première génération peut prendre entre quelques secondes à quelques minutes, mais les builds suivantes obtenez plus rapide.) Une fois la première étape de la génération est terminée, vous demandera par contrôle de compte utilisateur (UAC) Windows pour permettre à cette commande apporter des modifications à votre ordinateur. Cliquez sur **Oui**.

>[AZURE.IMPORTANT] Si vous ne voyez pas l’invite UAC, vérifiez la barre des tâches de Windows pour l’icône du contrôle de compte utilisateur, puis cliquez dessus tout d’abord. Parfois, l’UAC invite ne s’affiche pas sous la forme d’une fenêtre de niveau supérieur, mais est visible uniquement sous la forme d’une icône de la barre des tâches.

1. Examinez la sortie de l’émulateur de calcul l’interface utilisateur afin de déterminer s’il existe des problèmes avec votre projet. En fonction du contenu de votre déploiement, il peut prendre quelques minutes pour votre application dans l’émulateur de calcul démarrage complet.
1. Démarrez votre navigateur et utiliser l’URL `http://localhost:8080/MyHelloWorld` comme l’adresse (le `MyHelloWorld` partie de l’URL est sensible à la casse). Vous devez voir votre application MyHelloWorld (la sortie de index.jsp), image semblable à la suivante : ![][ic589579]

Lorsque vous êtes prêt à arrêter votre application s’exécute dans l’émulateur de calcul, dans la barre d’outils Eclipse, cliquez sur le bouton **Réinitialiser un émulateur Azure** , ![][ic710880].

## <a name="to-delete-your-deployment"></a>Pour supprimer votre déploiement ##

Pour supprimer votre déploiement au sein de la Shared Computer Toolkit Azure pour Eclipse, assurez-vous que **MyHelloWorld_onAzure** est sélectionné dans l’Explorateur de projet du Eclipse, assurez-vous que l’Explorateur de projets Eclipse a la fenêtre en cours se concentrer, puis cliquez sur le bouton **Annuler la publication** , ![][ic710883], dans la barre d’outils Eclipse. (Impossible de faire la même opération en cliquant **MyHelloWorld_onAzure** dans l’Explorateur de projet de Eclipse, sur **Azure** puis en cliquant sur **Annuler le déploiement du Cloud d’Azure**.) Cela affiche la boîte de dialogue **Annuler la publication d’un projet Azure** .

![][ic719491]

Sélectionnez le service d’abonnement et de nuage qui contient votre déploiement, sélectionnez le déploiement que vous souhaitez supprimer, puis cliquez sur **Annuler la publication**.

(Alternative à l’utilisation de la trousse à outils pour supprimer le déploiement est d’utiliser la section **Services de Cloud** du portail de gestion d’Azure : accédez à votre déploiement, sélectionnez-le et puis cliquez sur le bouton **Supprimer** . Cela arrêtera et avant de supprimer le déploiement. Si vous souhaitez seulement arrêter le déploiement et pas le supprimer, cliquez sur le bouton **Arrêter** , au lieu du bouton **Supprimer** , mais comme indiqué ci-dessus, si vous ne supprimez pas le déploiement, les frais facturables continuera à accumuler pour votre déploiement, même s’il est arrêté).

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[L’activation de l’accès distant pour les déploiements Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés de configuration de serveur]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
