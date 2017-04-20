<properties
    pageTitle="Ajouter des solutions d’Analytique de journal à partir de la galerie de Solutions | Microsoft Azure"
    description="Solutions d’Analytique de journal sont qu'une collection de logique, de visualisation et d’acquisition de données des règles qui fournissent des métriques de pivoter autour d’une zone de problème particulier."
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

# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>Ajouter des solutions d’Analytique de journal à partir de la galerie de Solutions

Solutions d’Analytique de journal sont un ensemble de **logique**, de **visualisation** et de **règles d’acquisition des données** qui fournissent des métriques de pivoter autour d’une zone de problème particulier. Cet article les solutions de listes de prise en charge de journal Analytique et vous indique comment ajouter et supprimer à l’aide de la galerie de Solutions.

Les solutions permettent plus précieuse à :

- aider à étudier et à résoudre les problèmes opérationnels plus rapidement
- collecte et corrélation des différents types de données de l’ordinateur
- vous aider à adopter une attitude proactive avec les activités de planification de la capacité, rapport d’état du correctif et l’audit de sécurité.


>[AZURE.NOTE] Analytique de journal inclut une fonctionnalité de recherche de journal, afin que vous n’avez pas besoin d’installer une solution pour l’activer. Toutefois, vous pouvez obtenir des visualisations de données, des suggestions de recherche et des idées en ajoutant des solutions à partir de la galerie de solutions.

Après que vous avez ajouté une solution, les données sont collectées à partir des serveurs de votre infrastructure et envoyées au service OMS. Traitement par l’OMS service prend généralement quelques minutes à une heure. Une fois le service traite les données, vous pouvez l’afficher dans OMS.

Vous pouvez facilement supprimer une solution lorsqu’il n’est plus nécessaire. Lorsque vous supprimez une solution, ses données ne sont pas envoyées à OMS, ce qui permet de réduire la quantité de données utilisées par votre quota quotidien, si vous en avez.


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Solutions de prise en charge par l’Agent de surveillance Microsoft

À ce stade, les serveurs qui sont connectés à l’OMS, à l’aide de l’Agent de surveillance de Microsoft peuvent utiliser la plupart des solutions disponibles, y compris :

- Évaluation de Active Directory
- Gestion des alertes (sans les alertes SCOM)
- Contre les logiciels malveillants
- Suivi des modifications
- Sécurité
- Évaluation de SQL
- Mises à jour du système

Toutefois, les solutions suivantes ne sont *pas* pris en charge par l’Agent de surveillance de Microsoft et que nécessitent l’agent de System Center Operations Manager (SCOM).

- Gestion des alertes (y compris les alertes SCOM)
- Gestion de la capacité
- Évaluation de la configuration

Reportez-vous à [Connexion Operations Manager pour journal Analytique](log-analytics-om-agents.md) pour plus d’informations sur la connexion de l’agent SCOM à Analytique du journal.

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>Pour ajouter une solution à l’aide de la galerie de Solutions

1. Dans la page Vue d’ensemble de l’OMS, cliquez sur la vignette de la **Galerie de Solutions** .    
    ![Galerie de solutions](./media/log-analytics-add-solutions/sol-gallery.png)
2. Dans la page Galerie de Solutions OMS, obtenir des informations sur chaque solution disponible. Cliquez sur le nom de la solution que vous souhaitez ajouter pour OMS.
3. Sur la page pour la solution que vous avez choisi, des informations détaillées sur la solution s’affiche. Cliquez sur **Ajouter**.
4. Une nouvelle vignette pour la solution que vous avez ajouté s’affiche dans l’aperçu de page OMS et vous pouvez commencer à l’utiliser une fois que le service OMS traite les données.

## <a name="to-configure-solutions"></a>Pour configurer des solutions
1. Vous devez configurer certaines solutions. Par exemple, vous devrez configurer Automation Azure Site sauvegarde, restauration et avant de pouvoir les utiliser.
2. Pour chacune de ces solutions, cliquez sur sa mosaïque dans la page Vue d’ensemble.  
    ![configuration de solution](./media/log-analytics-add-solutions/configure-additional.png)
3. Ensuite, configurez la solution avec les informations nécessaires et puis cliquez sur **Enregistrer**.  
    ![configuration de solution](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>Pour supprimer une solution à l’aide de la galerie de Solutions

1. Dans la page Vue d’ensemble de l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Dans la page Paramètres, sous l’onglet Solutions, cliquez sur **Supprimer** de la solution que vous souhaitez supprimer.
3. Dans la boîte de dialogue de confirmation, cliquez sur **Oui** pour supprimer la solution.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>Détails de collecte de données pour les solutions et fonctionnalités OMS

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour solutions et fonctionnalités OMS. Agents de directs et SCOM sont essentiellement les mêmes, mais l’agent directe inclut des fonctionnalités supplémentaires pour qu’il puisse se connecter à l’espace de travail OMS et router via un proxy. Si vous utilisez un agent SCOM, il doit être ciblé comme un agent de l’OMS pour communiquer avec l’OMS. Agents SCOM dans ce tableau sont des agents d’OMS connectés à SCOM. Pour plus d’informations sur la connexion de votre environnement existant de SCOM à OMS, reportez-vous à la section [Connexion Operations Manager pour l’Analytique de journal](log-analytics-om-agents.md) .

>[AZURE.NOTE] Le type d’agent que vous utilisez détermine la façon dont les données sont envoyées à OMS, aux conditions suivantes :

- Vous utilisez l’agent direct ou un agent SCOM-attaché l’OMS.
- Lorsque SCOM est requise, les données de l’agent SCOM pour la solution sont toujours envoyées à OMS en utilisant le groupe de gestion de SCOM. En outre, lorsque le SCOM est requise, seul l’agent SCOM est utilisé par la solution.
- Lorsque SCOM n’est pas nécessaire et que le tableau indique que les données de l’agent SCOM sont envoyées à l’OMS en utilisant le groupe d’administration, puis les données de l’agent SCOM sont toujours envoyées à OMS à l’aide de groupes d’administration. Les agents directs ignorer le groupe d’administration et envoyer leurs données directement à l’OMS.
- Lorsque les données de l’agent SCOM ne sont pas envoyées à l’aide d’un groupe d’administration, puis les données sont envoyées directement à l’OMS, en contournant le groupe d’administration.


|type de données| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|---|
|Évaluation de l’annonce|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 derniers jours|
|État de la réplication AD|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 jours|
|Alertes (Nagios)|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|à l’arrivée|
|Alertes (Zabbix)|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minute|
|Alertes (Operations Manager)|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutes|
|Contre les logiciels malveillants|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| toutes les heures|
|Gestion de la capacité|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| toutes les heures|
|Suivi des modifications|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| toutes les heures|
|Suivi des modifications|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|toutes les heures|
|Évaluation de la configuration (conseiller hérité)|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| deux fois par jour|
|ETW|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutes|
|Journaux IIS|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutes|
|Clé de coffres-forts|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutes|
|Passerelles d’Application réseau|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutes|
|Groupes de sécurité de réseau|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutes|
|Office 365|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|lors de la notification|
|Compteurs de performance|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|selon les prévisions, moins de 10 secondes|
|Compteurs de performance|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|selon les prévisions, moins de 10 secondes|
|TISSU de service|Windows|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutes|
|Évaluation de SQL|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 derniers jours|
|SurfaceHub|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|à l’arrivée|
|Journal système|Linux|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|à partir du stockage Azure : 10 minutes ; à partir de l’agent : à l’arrivée|
|Mises à jour du système|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| au moins 2 fois par jour et 15 minutes après l’installation d’une mise à jour|
|Journaux d’événements de sécurité de Windows|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)| pour le stockage Azure : 10 min ; pour l’agent : à l’arrivée|
|Journaux du pare-feu Windows|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)| à l’arrivée|
|Journaux des événements Windows|Windows|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)| pour le stockage Azure : 1 minute ; pour l’agent : à l’arrivée|
|Données de fil|Windows (2012 R2 / 8.1 ou version ultérieure)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Oui](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N°](./media/log-analytics-add-solutions/oms-bullet-red.png)| toutes les minutes|

## <a name="log-analytics-preview-solutions-and-features"></a>Les fonctionnalités et les Solutions d’Aperçu Analytique de journal

En exécutant un service et en suivant les pratiques de devops, nous sommes en mesure de collaborer avec les clients pour développer des solutions et fonctionnalités.

Lors de l’aperçu privée nous donner un petit groupe de clients d’accéder à une implémentation anticipée de la fonction ou de la solution pour obtenir des commentaires et apporter des améliorations. Cette implémentation anticipée a des caractéristiques minimales et des capacités opérationnelles.

Notre objectif est d’essayer des choses rapidement afin que nous pouvons trouver ce qui fonctionne et ce qui ne fonctionne pas. Ce processus nous itérer jusqu'à ce que les commentaires des clients Aperçu privée nous informe que nous sommes prêts pour une version d’évaluation.

Lors de la visualisation de public, nous rendre la fonctionnalité ou une solution disponible pour tous les utilisateurs pour obtenir plus de commentaires et de valider notre mise à l’échelle et d’efficacité. Pendant cette phase :

- Fonctionnalités d’aperçu seront affiche dans l’onglet Paramètres et peuvent être activées par un utilisateur
- Solutions d’aperçu peuvent être ajoutées par le biais de la galerie ou à l’aide d’un script de publication

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Que dois-je savoir sur les Solutions et les fonctionnalités ?

Nous sommes impatients de nouvelles fonctionnalités et solutions et nous t’aime travailler avec vous pour les développer.

Fonctions d’aperçu et solutions ne sont pas droite pour tout le monde, donc avant de demander à participer à un aperçu privé ou l’activation d’une version d’évaluation que vous utilisez OK avec quelque chose qui est en cours de développement.

Lors de l’activation d’une fonctionnalité d’aperçu via le portail, vous serez un avertissement s’affiche pour vous rappeler que la fonction est en mode Aperçu.

#### <a name="for-both-private-and-public-preview"></a>Pour l’aperçu *privés* et *publics*

Les remarques suivantes concernent les aperçus publiques et privées :

- Choses ne peut ne pas toujours fonctionnent correctement.
  - Plage de problèmes d’être une nuisance secondaire via à quelque chose ne fonctionne ne pas du tout
- Possibilités de l’aperçu vous permet d’avoir un impact négatif sur vos systèmes / environnement
  - Nous essayez d’éviter les choses négatifs se produit sur les systèmes vous utilisez avec OMS mais parfois inattendus des événements se produisent
- Perte de données / corruption peut se produire.
- Nous pouvons vous demander de collecter les journaux de diagnostic ou d’autres données pour aider à résoudre les problèmes
- La fonction ou la solution peut être supprimée (temporairement ou définitivement)
  - Selon nos connaissances lors de la visualisation, que nous pouvons décider de ne libère ne pas la fonction ou la solution
- Les aperçus peuvent ne pas fonctionnent ou a ne peut-être pas été testés avec toutes les configurations, et nous pouvons limiter :
  - Les systèmes d’exploitation qui peuvent être utilisés (par exemple, une fonctionnalité peut être appliqué uniquement à Linux en mode Aperçu avant)
  - Le type d’agent (MMA, SCOM) qui peut être utilisé (par exemple, une fonctionnalité peut ne pas fonctionne avec SCOM dans l’aperçu)  
- Aperçu solutions et fonctionnalités ne sont pas couverts par l’accord de niveau de Service
- L’utilisation des fonctionnalités d’aperçu entraîne des frais d’utilisation
- Fonctionnalités ou fonctions que vous avez besoin de la fonctionnalité / solution utile peut être manquante ou incomplète
- Fonctionnalités / solutions ne soient pas disponibles dans toutes les régions
- Fonctionnalités / solutions ne peuvent pas être localisées.
- Fonctionnalités / solutions peuvent avoir une limite sur le nombre de clients ou de périphériques qui peuvent l’utiliser
- Vous devrez peut-être utiliser des scripts pour effectuer la configuration et à activer la fonctionnalité/solution de
- L’interface utilisateur (IU) est incomplète et peut changer d’un jour à l’autre
- Aperçus publics peuvent ne pas être appropriées pour votre production / critiques systèmes

#### <a name="for-private-preview"></a>Pour un aperçu *privé*

Outre les éléments ci-dessus, voici spécifiques aux aperçus privés :

- Nous prévoyons de nous fournir des commentaires sur votre expérience afin que nous puissions améliorer la fonctionnalité/solution
- Nous pouvons vous contacter pour les commentaires à l’aide des enquêtes, des appels téléphoniques ou par courrier électronique
- Choses ne fonctionneront pas toujours correctement
- Nous participation peut nécessiter un accord de Non divulgation (NDA) ou peut inclure du contenu confidentiel
  - Avant la création de blogs, Tweeter ou autre moyen de communication avec des tiers, veuillez vérifier avec le Gestionnaire de programmes chargés de l’aperçu de comprendre toutes les restrictions à la divulgation
- N’exécutez pas de production / critiques systèmes


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Comment obtenir des accès à des solutions et fonctionnalités privé ?

Nous invitons les clients à des aperçus privés par le biais de différentes manières en fonction de l’aperçu.

- Répondre à l’enquête mensuelle de client et de nous donner l’autorisation de suivi améliorent vos chances d’être invité à un aperçu privé.
- L’équipe de votre compte Microsoft peut désigner, vous.
- Vous pouvez vous inscrire en fonction des informations validées sur twitter [msopsmgmt](https://twitter.com/msopsmgmt)
- Vous pouvez vous inscrire en fonction des événements de communauté partagé de détails – consulter pour que nous puissions répondre à ups, conférences et des Communautés en ligne.


## <a name="next-steps"></a>Étapes suivantes

- [Journaux de recherche](log-analytics-log-searches.md) permet d’afficher des informations détaillées collectées par les solutions.
