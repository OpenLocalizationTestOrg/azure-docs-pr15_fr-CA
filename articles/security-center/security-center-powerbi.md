<properties
   pageTitle="Obtenir des informations à partir des données du centre de sécurité Azure avec alimentation BI | Microsoft Azure"
   description="Le pack de contenus BI de puissance de centre de sécurité Azure permet de facilement rechercher des alertes de sécurité, des recommandations, attaqué des ressources et des tendances, basé sur un groupe de données qui a été créé pour votre rapport."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Obtenir des informations à partir des données du centre de sécurité Azure avec alimentation BI
Le [Tableau de bord d’alimentation](http://aka.ms/azure-security-center-power-bi) pour le centre de sécurité Azure permet de visualiser, analyser et filtrer les recommandations et les alertes de sécurité à partir de n’importe où, y compris votre appareil mobile. Le tableau de bord d’alimentation permet de révéler les tendances et modèles - afficher les alertes de sécurité par ressource ou par adresse IP source et les risques de sécurité unaddressed par la ressource ou l’âge d’attaque. 

Vous pouvez également modifier les recommandations du centre de sécurité et des alertes de sécurité avec d’autres données de façons intéressantes, par exemple à l’aide de données à partir des [Journaux d’Audit d’Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) et [d’Audit de base de données SQL Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Les deux offrent des tableaux de bord d’alimentation BI, et vous pouvez également exporter des données vers Excel de reporting facile sur l’état de la sécurité de vos ressources de cloud.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>À l’aide du tableau de bord Azure le centre de sécurité pour accéder aux BI de puissance
Vous pouvez également utiliser le tableau de bord du centre de sécurité Azure pour accéder aux rapports d’analyse Décisionnelle de puissance. Suivez les étapes pour effectuer cette tâche : 

1. Dans le tableau de bord **Azure le centre de sécurité** , cliquez sur **Explorer dans BI d’alimentation** .

    ![Se connecter au centre de sécurité Azure à l’aide de BI de puissance](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. La lame **Explorer BI d’alimentation** s’ouvre sur le côté droit, comme indiqué dans l’écran suivante :

    ![Se connecter au centre de sécurité Azure à l’aide de BI de puissance](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Si vous créez le tableau de bord d’alimentation pour la première fois, vous pouvez choisir une des options suivantes dans la lame **Explorer BI d’alimentation** : 

    - **Tableau de bord de perspectives sur la sécurité**: choisissez cette option si vous souhaitez créer un tableau de bord qui comprend l’état de la sécurité, les threads et les détections. Cette option est la plus commune pour rôle DevOps qui est responsable de l’analyse de leur état de protection et a détecté les alertes sur les abonnements.
    - **Tableau de bord de gestion stratégie**: choisissez cette option si vous souhaitez explorer les stratégies de gestion et d’application.  Cette option est la plus commune pour le service informatique Central qui sont davantage axées sur la gouvernance. Ils peuvent utiliser ce tableau de bord pour améliorer la visibilité et les conseils éclairés concernant l’adhésion aux stratégies de sécurité au sein de leur organisation.
    - Si vous disposez déjà d’un tableau de bord d’alimentation, cliquez sur **votre tableau de bord d’alimentation BI en cours**.

4. Pour cet exemple, cliquez sur tableau de bord de **perspectives sur la sécurité** . Si c’est la première fois, vous créez un tableau de bord BI de puissance pour le centre de sécurité vous êtes invité à installer le pack de contenu. Cliquez sur **obtenir** un bouton dans la fenêtre de **contenu packs pour alimentation BI** comme indiqué dans l’écran suivante :

    ![Tableau de bord de perspectives sur la sécurité Centre sécurité Azure](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. La fenêtre **connexion au perspectives sur la sécurité Azure Security Center** s’affichent. Assurez-vous que la méthode **d’authentification** est **oAuth2** comme indiqué ci-dessous et cliquez sur le bouton de **connexion** .
    
    ![Authentification](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Vous pouvez être invité à s’authentifier à nouveau vos informations d’identification Azure. Après l’authentification de votre tableau de bord sera créé. Une fois créé le tableau de bord vous consultez un rapport avec la structure similaire comme indiqué dans l’écran suivante :

    ![Tableau de bord d’alimentation](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] Une actualisation de l’état est planifiée pour avoir lieu sur une base quotidienne. Dans le cas où vous rencontrez un problème sur cette actualisation, lire [Des problèmes potentiels actualiser avec la BI Azure Security Center d’alimentation](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), pour plus d’informations sur la façon de résoudre les problèmes.

Ici, vous pouvez voir le nombre d’alertes de sécurité et de recommandations, ainsi que le nombre d’ordinateurs virtuels, les bases de données SQL Azure et les ressources réseau surveillés par le centre de sécurité Azure.

Un lien vers le centre de sécurité Azure vous redirige vers le portail Azure. Les graphiques facilitent la visualisation d’informations sur les recommandations de sécurité et des alertes, y compris :

- État de sécurité de ressource
- Recommandations en attente
- Recommandations de machine virtuelle
- Alertes au fil du temps
- Attaque de ressources
- IPs attaqués

Derrière chaque graphique, il y a des analyses supplémentaires. Sélectionnez un carré pour voir plus d’informations. Par exemple, la mosaïque de **l’État de sécurité de ressource** vous montre plus de détails sur en attente de recommandations par les ressources comme indiqué dans l’écran suivante :

![Recommandations](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Si vous cliquez sur n’importe quelle ligne de ce graphique, les autres vont griser et vous concentrer uniquement sur celui que vous avez sélectionné. Pour revenir au tableau de bord, cliquez sur **Centre de sécurité Azure** sous l’option de **tableaux de bord** dans le volet gauche de cette page.

> [AZURE.NOTE] Si vous souhaitez personnaliser vos rapports en ajoutant des champs supplémentaires ou en modifiant des objets visuels existants, vous pouvez modifier le rapport. Pour plus d’informations, lisez [interagir avec un rapport en mode de modification de puissance BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) .

Les mosaïques **d’alertes dans le temps, les ressources d’une attaque** et **IPs de l’attaquant** aura la sortie similaire lorsque vous cliquez sur chacun des. Cela se produit parce que l’état regroupe les informations concernant ces trois variables et appelle **ressources attaque** comme indiqué dans l’écran suivante :

![Ressources une attaque](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

À ce stade vous pouvez également enregistrer une copie de ce rapport, l’imprimer ou publier sur le web à l’aide des options disponibles dans le menu **fichier** .

![Menu fichier](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Exploration de vos données Azure le centre de sécurité avec les services d’alimentation BI

Se connecter à la [Puissance BI Content Pack Services](https://msit.powerbi.com/groups/me/getdata/services) dans BI d’alimentation et exécutez les étapes suivantes :

1. Dans la fenêtre de **Contenu Pack pour alimentation BI** , vous verrez deux options comme indiqué ci-dessous.

    ![Pack de contenu pour le décisionnel de puissance](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Si déjà exécutée la première partie de cet article, vous verrez qu’une seule option, qui est la gestion de stratégies de centre de sécurité Azure.

2. Pour cet exemple, cliquez sur **Extraire** dans la mosaïque de la **Gestion de stratégies de centre de sécurité Azure** .

3. Dans la fenêtre de **connexion à la gestion de stratégies de centre de sécurité Azure** , assurez-vous de sélectionner **oAuth2** dans la **Méthode d’authentification** de liste déroulante comme indiqué ci-dessous et cliquez sur le bouton de **connexion** .

    ![Fenêtre Gestion de stratégie](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Vous allez être redirigé vers une page d’authentification dans laquelle vous devez taper les informations d’identification que vous utilisez pour vous connecter au centre de sécurité Azure. Une fois terminé le processus d’authentification, BI d’alimentation commence l’importation de données pour générer vos rapports. Pendant ce temps, vous pouvez voir le message suivant sur la droite de votre navigateur :

    ![Se connecter au centre de sécurité Azure à l’aide de BI de puissance](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Lorsque le tableau de bord est créé pour la première fois, il peut prendre plus longtemps que d’habitude, principalement pour les scénarios où vous disposez de plusieurs abonnements. 

5. Une fois le processus terminé, votre tableau de bord analyse Décisionnelle d’alimentation du centre de sécurité Azure se chargera avec le rapport de **Gestion de la stratégie** similaire à celle présentée ci-dessous :

    ![Tableau de bord de gestion des stratégies](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris comment utiliser l’alimentation BI dans le centre de sécurité Azure. Pour en savoir plus sur le centre de sécurité Azure, consultez les rubriques suivantes :

- [Guide des opérations et la planification de la sécurité Azure centre](security-center-planning-and-operations-guide.md) : Apprenez comment planifier l’adoption du centre de sécurité Azure.
- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer les paramètres de sécurité dans le centre de sécurité Azure
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : trouver des billets de blog sur la sécurité Azure et de conformité
