<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.5.1" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.5.1

Ce document contient les notes de publication pour le SDK Azure pour .NET 2.5.1 version. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Notes de publication du SDK Azure pour .NET 2.5.1

Voici les nouveautés et les mises à jour du SDK Azure pour .NET 2.5.1.

- Features\scenarios nouveau liés aux **Extensions d’outils Web**. 

    - Sites Web Azure a été renommé Service d’application Azure. Pour plus d’informations, voir [Service d’application Azure et des Services Azure existants](app-service-changes-existing-services.md).
    - Prise en charge des applications de l’API (aperçu) Azure a été ajoutée afin que les clients puissent publier des projets ASP.NET en tant qu’applications d’API et puis utilisez Ajout > projets de mouvement Azure API application Client C# pour générer du code basé sur la structure de l’application API déployé. 
    - Le nœud sites Web dans l’Explorateur de serveurs a été désapprouvé à la place le nœud Service d’application Azure, qui contient la prise en charge de la ressource groupe-en fonction de regroupement applications d’API Azure applications Mobile et applications Web.
    - Prise en charge des applications de Mobile (aperçu) Azure a été ajoutée afin que les clients peuvent créer de nouveaux projets d’applications Mobile, ajouter des contrôleurs d’applications Mobile, publier les projets et déboguer des applications à distance.
    - Ajouter > mouvement de Client de l’application API Azure prend désormais en charge les fichiers Swagger de JSON locaux, afin que l’API Web permet aux développeurs NuGets tiers comme Swashbuckle pour générer Swagger ou créez-la manuellement. De cette façon, les développeurs client peuvent utiliser les fonctionnalités de génération de code pour consommer n’importe quel point de terminaison Swagger dans les projets C#. 
    - Web App et les boîtes de dialogue de publication API application ont été améliorées pour prendre en charge de la notion de portail Azure de regroupement des ressources, et sélection/création de groupes de ressources Azure et Plans de Service d’application sont représentés dans l’application Web et application de l’API provisionnement boîte de dialogue Nouveau. 
    - Nœuds d’Explorateur de serveurs d’application API Azure fournissent des liens vers l’applications d’API lien vers le portail Azure, ainsi que d’autres fonctionnalités telles que la diffusion en continu de journal et le débogage distant.

    Les problèmes connus et limitations actuelles dans Azure SDK .NET 2.5.1 [cette](app-service-release-notes.md#known_issues_2_5_1) section ci-dessous.


- Nouvelle features\scenarios liés aux **Outils de HDInsight** dans Visual Studio sont activées dans cette version. 
    - Contrôle local des scripts de la ruche. Cliquez sur le bouton de script de validation dans la barre d’outils pour voir s’il existe des erreurs dans votre script. 
    - Débogage des travaux de la ruche amélioré. Vous pouvez maintenant déboguer des travaux de la ruche en accédant aux journaux de fils dans Visual Studio. Si votre application présente des problèmes de performances, analyse des journaux de fils offrent des informations utiles...
    - (Version d’évaluation) Mot clé semi-automatique et IntelliSense prend en charge de la ruche. Pour aider à créer des scripts de la ruche, ajouter des outils de HDInsight pour Visual Studio prend en charge IntelliSense et saisie semi-automatique mot clé de la ruche.
    - Prise en charge de la tempête. Vous pouvez désormais utiliser des outils de HDInsight pour Visual Studio pour développer des topologies/becs verseurs/boulons de la tempête dans C#. Vous pouvez ensuite soumettre la topologie de développé vers un cluster de tempête et voir l’état de la topologie/boulon/bec. Vous pouvez utiliser les journaux système et les journaux du client pour résoudre les problèmes de votre tempête topologies/boulons/becs verseurs. Vous pouvez également utiliser les ressources existantes de JAVA dans la tempête sur HDInsight.
    
    Pour plus d’informations, consultez [mise en route à l’aide d’outils d’Hadoop HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Azure SDK pour .NET 2.5.1 des limitations et problèmes connus

- Applications d’API Azure apparaît sous la forme d’une cible de déploiement pour les applications mobiles. Web applications doit être la seule destination pour applications Mobile jusqu'à ce qu’une version ultérieure. 
- Provisionnement des API application Azure peut entraîner la réussite mais échouer de façon intermittente à mettre à jour la progression dans la fenêtre de l’activité de Service d’application Azure. Solution de contournement consiste à vérifier l’état de la nouvelle application API Azure dans le portail Azure. 
- Fichier > Nouveau projet > API App > F5 expérience provoque une erreur HTTP car il n’y a aucun default/index.html. Solution de contournement consiste à parcourir manuellement l’URL /api/values. 
- Par intermittence, les icônes de l’Explorateur de serveurs affichent aplaties. Redémarrage de VS résout ce problème. 
- Si une exception est levée au cours de la mise en service Web App ou API application (par exemple, les erreurs de dépassement de quota ou nom de la passerelle Azure API App en double), les erreurs affichent du texte JSON brut. 
- Problèmes intermittents de création de projet lors de l’aperçu de l’Application est vérifiée au moment de la création du projet.
- Parfois, le code Client de l’application API Azure généré manque des espaces de noms, ils doivent être inclus manuellement (ou importées automatiquement par l’intermédiaire des signaux de Visual Studio) pour le code à compiler. 
- Projets d’application mobile doivent être publiées dans les applications web, mais vous devez sélectionner un site que vous avez créé sous la forme d’une application Mobile dans le portail Azure dans la mesure où les projets d’application Mobile requièrent une base de données. 
- La page de démarrage pour applications Mobile utilise le terme « service mobile » au lieu de « les applications mobiles » 
- Création du projet d’application mobile peut prendre une minute pour créer. 
- Au cours de l’API application mise en service, dans certains cas, une erreur revient à partir de l’API d’Azure, reflétant que les autorisations ne pourraient pas définies correctement, alors que l’application API a été correctement configurée et qu’il est prête à l’emploi. Vous pouvez définir manuellement les autorisations à l’aide du portail Azure.
- Idées d’application n’est pas pris en charge sur les modèles d’API App et des modèles de l’application Mobile.
- Projets d’API App ne peut pas être utilisés conjointement avec les projets de Service Cloud.
- Les modèles de projet API App ne sont disponibles dans C#.
- La consommation des API application via le menu contextuel Ajouter Azure API App « Client » est uniquement pris en charge dans C#.

 
