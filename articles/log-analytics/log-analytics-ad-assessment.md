<properties
    pageTitle="Optimisez votre environnement grâce à la solution Active Directory évaluation Analytique de journal | Microsoft Azure"
    description="Vous pouvez utiliser la solution Active Directory évaluation afin d’évaluer les risques et la santé de vos environnements serveur à intervalles réguliers."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Optimisez votre environnement grâce à la solution Active Directory évaluation Analytique du journal

Vous pouvez utiliser la solution Active Directory évaluation afin d’évaluer les risques et la santé de vos environnements serveur à intervalles réguliers. Cet article vous aidera à installer et à utiliser la solution de sorte que vous pouvez prendre des mesures correctives pour les problèmes potentiels.

Cette solution fournit une liste hiérarchisée des recommandations spécifiques pour votre infrastructure de serveur déployé. Les recommandations sont classées dans quatre domaines clés qui vous permettent de rapidement comprendre les risques et effectuer une opération.

Les recommandations sont basées sur les connaissances et l’expérience acquise par les ingénieurs de Microsoft de milliers de visites de clients. Chaque recommandation fournit des recommandations sur la raison pour laquelle un problème peut avoir une importance pour vous et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre la progression vers un environnement libre et sain en cours d’exécution.

Une fois que vous avez ajouté à la solution et une évaluation est terminée, le résumé des informations pour les domaines sont indiquées sur le tableau de bord **d’évaluation d’Active Directory** pour l’infrastructure de votre environnement. Les sections suivantes décrivent comment utiliser les informations du tableau de bord **AD évaluation** , dans laquelle vous pouvez afficher et puis entreprendre des actions recommandées pour votre infrastructure de serveur Active Directory.

![image de mosaïque d’évaluation de SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![image du tableau de bord d’évaluation de SQL](./media/log-analytics-ad-assessment/ad-assessment.png)


## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer les solutions.

- Agents doivent être installés sur les contrôleurs de domaine qui sont membres du domaine à être évaluée.
- La solution Active Directory évaluation nécessite.NET Framework 4 est installé sur chaque ordinateur qui dispose d’un agent de l’OMS.
- Ajouter la solution Active Directory évaluation à votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.

    >[AZURE.NOTE] Après que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs avec des agents. Données de configuration sont lues et ensuite envoyées au service OMS dans le nuage pour le traitement. La logique est appliquée pour les données reçues et le service en nuage enregistre les données.

## <a name="active-directory-assessment-data-collection-details"></a>Détails de collection de données actives Directory évaluation

Active Directory évaluation collecte les données WMI, les données du Registre et des données de performances à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données pour les agents, Operations Manager (SCOM) est nécessaire et la façon dont souvent les données sont collectées par un agent.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Oui](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![N°](./media/log-analytics-ad-assessment/oms-bullet-red.png)|   ![N°](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![Oui](./media/log-analytics-ad-assessment/oms-bullet-green.png)| 7 derniers jours|


## <a name="understanding-how-recommendations-are-prioritized"></a>Compréhension des priorités des recommandations

Une valeur de pondération qui identifie l’importance relative de la recommandation est attribuée à chaque recommandation formulée. Uniquement les dix recommandations plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Mode de calcul des pondérations

Pondérations sont des valeurs de regroupement en fonction des trois facteurs clés :

- La *probabilité* qu’un problème identifié provoque des problèmes. Une plus forte probabilité équivaut à un score global supérieur de la recommandation.

- L' *impact* du problème sur votre organisation, si elle n’entraîne pas un problème. Un impact plus important équivaut à un score global supérieur de la recommandation.

- L' *effort* requis pour mettre en oeuvre la recommandation. Un effort supérieur équivaut à un plus petit score global de la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage de la note totale pour chaque zone de focus. Par exemple, si une recommandation dans la zone de focus de sécurité et de conformité a un score de 5 %, mise en oeuvre de cette recommandation augmentera votre globale score à 5 % de sécurité et de conformité.

### <a name="focus-areas"></a>Domaines

**Sécurité et conformité** - ce sujet affiche des recommandations pour les menaces de sécurité potentielles et les violations, les stratégies d’entreprise et les exigences de conformité réglementaires, techniques et juridiques.

**Disponibilité et continuité d’activité** - ce sujet affiche des recommandations pour la disponibilité du service, de la souplesse de votre infrastructure et de protection de l’entreprise.

**Performances et évolutivité** - ce sujet affiche des recommandations pour aider votre organisation à infrastructure informatique augmentent, assurez-vous que votre environnement informatique répond aux exigences de performances actuelles et qu’il est capable de répondre à l’évolution des besoins de l’infrastructure.

**Mise à niveau, déploiement et Migration** - ce sujet présente des recommandations pour vous aider à mettre à jour, migrer et déployer Active Directory dans votre infrastructure existante.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Devraient viser à score de 100 % dans chaque zone de focus ?

Pas nécessairement. Les recommandations sont basées sur les connaissances et l’expérience acquise par les ingénieurs de Microsoft sur des milliers de visites de clients. Toutefois, aucune des infrastructures deux serveurs ne sont les mêmes, et des recommandations spécifiques peuvent être plus ou moins pertinentes pour vous. Par exemple, certaines recommandations de sécurité peuvent être moins pertinentes si vos ordinateurs virtuels ne sont pas exposés à Internet. Des recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent une collection de données ad hoc de faible priorité et génération de rapports. Problèmes qui sont importants pour une entreprise peuvent être moins importantes d’une start-up. Vous souhaiterez peut-être identifier les domaines sont vos priorités et observez comment modifier de vos résultats au fil du temps.

Chaque recommandation inclut des instructions sur la raison pour laquelle il est important. Ce guide vous permet de déterminer si la mise en oeuvre de la recommandation est adaptée à votre cas, étant donné la nature de vos services informatiques et aux besoins de votre organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Utiliser les recommandations de zone évaluation focus

Avant de pouvoir utiliser une solution d’évaluation de l’OMS, vous devez disposer de la solution est installée. Pour en savoir plus sur l’installation des solutions, reportez-vous à la section [Analytique de journal ajouter des solutions à partir de la galerie de Solutions](log-analytics-add-solutions.md). Après que l’avoir installé, vous pouvez afficher le résumé des recommandations à l’aide de la mosaïque de l’évaluation sur la page de vue d’ensemble de l’OMS.

Afficher les évaluations de la synthèse de conformité pour votre infrastructure et puis globaliser recommandations.


### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher des recommandations pour une zone de focus et action corrective

1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque de **l’évaluation** de votre infrastructure de serveur.
2. Sur la page **d’évaluation** , consultez les informations résumées de l’une des lames de zone du focus et puis cliquez sur une option pour afficher les recommandations de cette zone de focus.
3. Sur toutes les pages de la zone de focus, vous pouvez afficher les recommandations par ordre de priorité pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour afficher plus d’informations sur la raison pour laquelle la recommandation a lieu.  
    ![image de recommandations de l’évaluation](./media/log-analytics-ad-assessment/ad-focus.png)
4. Vous pouvez prendre des mesures correctives suggérées dans **Les Actions suggérées**. Lorsque l’élément a été adressé, les évaluations ultérieures enregistrera recommandé des actions entreprises et augmente votre score de conformité. Les éléments corrigés apparaissent comme **Des objets de passé**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations

Si vous avez des recommandations que vous souhaitez ignorer, vous pouvez créer un fichier texte par OMS pour empêcher les recommandations d’apparaître dans les résultats de l’évaluation.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Pour identifier des recommandations qui vous ignore

1.  Vous connecter à votre espace de travail et ouvrir de recherche des journaux. Utilisez la requête suivante pour les recommandations de liste qui ont échoué pour les ordinateurs de votre environnement.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Voici une capture d’écran montrant la requête de recherche de journal : ![Échec de recommandations](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.  Choisissez les recommandations que vous souhaitez ignorer. Vous allez utiliser les valeurs de RecommendationId dans la procédure suivante.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier de texte IgnoreRecommendations.txt

1.  Créez un fichier nommé IgnoreRecommendations.txt.
2.  Collez ou tapez chaque RecommendationId pour chaque recommandation que vous souhaitez Analytique du journal à ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3.  Placez le fichier dans le dossier suivant sur chaque ordinateur où vous souhaitez OMS pour ignorer les recommandations.
    - Sur les ordinateurs avec la surveillance de l’Agent Microsoft (connecté directement ou par l’intermédiaire de Operations Manager) - *lecteur_système*: \Program Files\Microsoft Agent\Agent de surveillance
    - Sur le serveur d’administration Operations Manager - *lecteur_système*: \Program Files\Microsoft Manager\Server R2\Operations de System Center 2012

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées.

Après que la prochaine planifiée évaluation, par défaut tous les 7 jours, les recommandations spécifiées sont marquées *ignorés* et n’apparaissent pas sur le tableau de bord d’évaluation.

1. Vous pouvez utiliser les requêtes de recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.  Si vous décidez ultérieurement que vous souhaitez voir Recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou vous pouvez supprimer les RecommendationIDs.

## <a name="ad-assessment-solutions-faq"></a>Solutions d’évaluation AD Forum aux questions

*La fréquence à laquelle une évaluation s’exécute-t-il ?*
- L’évaluation s’exécute tous les 7 jours.

*Existe-t-il un moyen pour configurer la fréquence à laquelle l’évaluation s’exécute ?*
- Pas pour l’instant.

*Si un autre serveur pour est découverte une fois que j’ai ajouté une solution d’évaluation, il examinera ?*
- Oui, une fois qu’il est découvert qu’il est évalué à partir de là, tous les 7 jours.

*Si un serveur est mis hors service, lorsqu’il disparaissent de l’évaluation ?*
- Si un serveur ne présente pas les données pour les 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*
- AdvisorAssessment.exe

*Combien de temps faut-il pour collecter les données ?*
- La collection de données sur le serveur prend environ 1 heure. Il peut prendre plus de temps sur les serveurs qui possèdent un grand nombre de serveurs Active Directory.

*Le type de données est recueilli ?*
- Les types de données suivantes sont collectées :
    - WMI
    - Registre
    - Compteurs de performance

*Y a-t-il un moyen de configurer les données collectées ?*
- Pas pour l’instant.

*Pourquoi afficher uniquement les recommandations du top 10 ?*
- Au lieu de vous donner une liste exhaustive écrasante de tâches, nous vous recommandons que vous concentrer tout d’abord sur les recommandations hiérarchisées. Une fois que vous y remédier, des recommandations supplémentaires seront disponibles. Si vous préférez afficher la liste détaillée, vous pouvez afficher à l’aide de la recherche des journaux de toutes les recommandations.

*Existe-t-il un moyen d’ignorer une recommandation ?*
- Oui, consultez [recommandations relatives à ignorer](#ignore-recommendations) ci-dessus.


## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher des recommandations et des données AD évaluation détaillées.
