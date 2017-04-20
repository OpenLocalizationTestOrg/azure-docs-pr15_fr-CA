<properties
   pageTitle="Les capacités de détection dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous aide à comprendre le fonctionnement des fonctionnalités de détection Azure le centre de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Capacités de détection Azure le centre de sécurité
Ce document traite des fonctionnalités de détection avancées du centre de sécurité Azure qui permet d’identifier les actifs menaces ciblant vos ressources Microsoft Azure et vous fournit les informations nécessaires pour répondre rapidement.

> [AZURE.NOTE] Détections avancées sont disponibles dans la couche Standard d’Azure Security Center. Une version d’évaluation gratuite de 90 jours est disponible. Vous pouvez mettre à niveau à partir de la sélection du niveau de prix dans la [Stratégie de sécurité](security-center-policies.md). Visitez la [page du centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/) pour en savoir plus sur la tarification. 


## <a name="responding-to-todays-threats"></a>Réponse aux menaces
Des modifications ont été importantes dans le paysage des menaces au cours des 20 dernières années. Dans le passé, sociétés n’étaient à vous soucier de dégradation de sites web par des pirates qui étaient principalement intéressés par voir « que qu’ils pourraient faire ». Les pirates d’aujourd'hui sont beaucoup plus sophistiquées et plus organisées. Ils ont souvent des objectifs financiers et stratégiques spécifiques. Ils ont également davantage de ressources à leur disposition, comme ils peuvent être financées par des Etats ou le crime organisé.

Cette approche a conduit à un niveau sans précédent de professionnalisme dans les rangs de l’attaquant. N’est plus qu’ils souhaitent dans la dégradation de pages web. Ils sont intéressés par le vol d’informations, les comptes financiers et les données privées – qui ils peuvent utiliser pour générer des disponibilités sur le marché libre ou de tirer parti d’une entreprise, de la position politique ou militaire. Encore plus relatif à ces intrus avec un objectif financier sont les attaquants qui enfreignent les réseaux à nuire à l’infrastructure et des personnes.

En réponse, les organisations déploient souvent des diverses solutions de point, qui portent essentiellement sur la défense de périmètre de l’entreprise ou de points de terminaison en recherchant des signatures d’attaques connues. Ces solutions ont tendance à générer un volume élevé d’alertes de faible fidélité, qui nécessitent un analyste en sécurité trier et analyser. La plupart des organisations ne disposent pas du temps et du savoir-faire requis pour répondre à ces alertes – autant aller unaddressed.  Pendant ce temps, les pirates ont évolué leurs méthodes pour renverser les nombreuses défenses de signature et de [s’adapter aux environnements de cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nouvelles approches sont nécessaires à l’identification des nouvelles menaces plus rapidement et d’accélérer la détection et réponse. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Comment le centre de sécurité Azure détecte et réagit aux menaces

Chercheurs en sécurité Microsoft sont constamment à la recherche de menaces. Ils ont accès à un vaste jeu de télémétrie de présence mondiale de Microsoft dans le nuage et sur site. Cette collection large et diversifiée de jeux de données permet à Microsoft de découvrir de nouveaux modèles et tendances sur ses produits d’entreprise et le consommateur sur site, ainsi que ses services en ligne. Par conséquent, le centre de sécurité peut mettre à jour rapidement ses algorithmes de détection que les attaquants relâchez les attaques nouvelles et de plus en plus sophistiquées. Cette approche vous permet de faire face à un environnement menace. 

Détection des menaces de sécurité Centre fonctionne automatiquement collecte des informations sur la sécurité de vos ressources d’Azure, le réseau et les solutions des partenaires connectés. Il analyse ces informations, souvent mise en corrélation des informations provenant de plusieurs sources, d’identifier les menaces. Alertes de sécurité sont classés par priorité dans le centre de sécurité ainsi que des recommandations sur la manière de corriger la menace.

![Collecte de données de centre de sécurité et de la présentation](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Centre de sécurité emploie analytique de sécurité avancée, qui dépassent largement les approches basées sur la signature. Innovations de données volumineuses et [machine de formation](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) de technologies sont exploitées pour évaluer des événements via le fabric entière cloud – détection des menaces qu’il seraient impossibles d’identifier l’utilisation des méthodes manuelles et de prédiction de l’évolution des attaques de. Ces analytique de sécurité est les suivantes : 

- **Menaces intégrée**: recherche des acteurs de mauvais connus en tirant parti des menaces globales à partir des produits et services, Microsoft Digital Crimes unité (DCU), le centre de réponse de sécurité Microsoft (MSRC) et Microsoft alimentations externes.
- **Analytique comportementale**: s’applique des modèles connus pour découvrir les comportements malveillants. 
- **Détection d’anomalie**: utilise un profil statistique pour créer une configuration de référence historique. Il alerte sur les écarts par rapport à des configurations de référence établies qui se conforment à un vecteur d’attaque potentiel.


### <a name="threat-intelligence"></a>Informations sur les menaces
Microsoft dispose d’une quantité considérable de menaces globales. Télémétrie suit à partir de plusieurs sources, notamment Azure, Office 365, Microsoft CRM les en ligne, Microsoft Dynamics AX, outlook.com, MSN.com, l’unité Crimes numériques Microsoft (DCU) et le centre de réponse de sécurité Microsoft (MSRC). Chercheurs également recevoir des renseignements de menace qui sont partagés par les principaux fournisseurs et s’abonne à des flux d’intelligence de menace par des tiers. Le centre de sécurité Azure peut utiliser ces informations pour vous alerter de menaces connues mauvais acteurs. Voici quelques exemples :

- **Les communications sortantes vers une adresse IP malveillante**: le trafic sortant vers un botnet connu ou un darknet probablement indique que votre ressource a été compromise et qu’un attaquant, en essayant d’exécuter des commandes sur ces données système ou exfiltrate. Le centre de sécurité Azure compare le trafic réseau vers la base de données de menaces de Microsoft et vous avertit s’il détecte la communication à une adresse IP malveillante.

## <a name="behavioral-analytics"></a>Analytique comportementale

Analytique comportementale est une technique qui analyse et compare les données à une collection de modèles connus. Toutefois, ces modèles ne sont pas des signatures simples. Ils sont déterminés par le biais de machine complexe des algorithmes qui sont appliqués à des groupes de données massive d’apprentissage. Ils sont également déterminés par une analyse minutieuse des comportements malveillants par les analystes spécialisés. Le centre de sécurité Azure pouvez utiliser analytique comportementale afin d’identifier les ressources compromis basées sur l’analyse des journaux de la machine virtuelle, journaux de périphérique de réseau virtuel, journaux de fabric, vidages sur incident et d’autres sources. 

En outre, il existe des corrélation avec les autres signaux pour vérifier les preuves d’une campagne généralisée. Cette corrélation permet d’identifier les événements qui sont cohérents avec les indicateurs établis de compromis. Voici quelques exemples :

- **L’exécution de processus suspects**: les pirates utilisent plusieurs techniques pour exécuter des logiciels malveillants sans risque de détection. Par exemple, un attaquant peut donner des logiciels malveillants le même nom que des fichiers système légitimes mais placer ces fichiers dans un autre emplacement, utilisez un nom qui est très similaire à un fichier inoffensif ou masque l’extension du fichier true. Centre de sécurité modélise les comportements de processus et traitent les exécutions pour détecter les valeurs aberrantes ces moniteurs.  
- **Contre les logiciels malveillants cachés et à l’exploitation tente**: sophistiquées de logiciel malveillant sont capable échapper aux produits contre les logiciels malveillants traditionnels par jamais une écriture sur le disque ou le cryptage des composants logiciels stockés sur le disque.  Toutefois, les logiciels malveillants peuvent être détectés en utilisant l’analyse de la mémoire, que le logiciel malveillant doit laisser des traces dans la mémoire pour fonctionner. Lorsque le logiciel cesse de fonctionner, un vidage sur incident capture une partie de la mémoire au moment de l’incident.  En analysant la mémoire dans le vidage sur incident, le centre de sécurité Azure peut détecter les techniques utilisées pour exploiter les vulnérabilités des logiciels et accéder à des données confidentielles subrepticement persistent avec un ordinateur compromis sans impact sur les performances de votre ordinateur.
- **Latérale de mouvement et reconnaissance interne**: pour rendre persistantes dans une compromis réseau et recherchez/récolte importantes de données, les attaquants tentent souvent déplacer latéralement sur l’ordinateur compromis à d’autres personnes au sein du même réseau. Centre de sécurité surveille les activités de processus et de la connexion afin de découvrir les tentatives pour développer la brèche de l’attaquant dans le réseau, tels que la détection de réseau du exécution de commande à distance et l’énumération du compte.
- **Les Scripts malveillants PowerShell**: PowerShell est utilisé par les pirates pour exécuter du code malveillant sur les machines virtuelles de cible pour une variété de buts. Centre de sécurité inspecte l’activité PowerShell pour preuve de toute activité suspecte. 
- **Sortant des attaques**: les attaquants ciblent souvent les ressources de cloud avec l’objectif de l’utilisation de ces ressources pour lancer d’autres attaques. Des machines virtuelles compromis, par exemple, peut être utilisés pour lancer des attaques en force contre d’autres machines virtuelles, envoyer du courrier indésirable ou analyser les ports ouverts et les autres périphériques sur internet. En appliquant l’apprentissage automatique pour le trafic réseau, le centre de sécurité peut détecter lorsque les communications réseau sortant dépassent la norme. Dans le cas de courrier indésirable, centre de sécurité également met en corrélation du trafic de messagerie inhabituel avec intelligence dans Office 365 pour déterminer si le message est probablement mal intentionnés désireux ou le résultat d’une campagne de courrier électronique légitime.  

### <a name="anomaly-detection"></a>Détection d’anomalie

Centre de sécurité Azure utilise également la détection des anomalies pour identifier les menaces. Contrairement à l’analytique comportementale (qui dépend des modèles connus dérivés de grands ensembles de données), détection des anomalies est plus « personnalisée » et se concentre sur les configurations de référence qui sont spécifiques à votre déploiement. Apprentissage automatique est appliqué pour déterminer l’activité normale pour vos déploiements et ensuite les règles sont générées pour définir les conditions isolés qui peuvent représenter un événement de sécurité. Voici un exemple :

- **En force entrant un brute RDP/SSH**: vos déploiements est peut-être occupé de machines virtuelles avec un grand nombre de connexions chaque jour autres des machines virtuelles et qui ont peu ou les connexions. Le centre de sécurité Azure peut déterminer l’activité de connexion initiale pour ces ordinateurs virtuels et utiliser l’apprentissage définir ce qui est en dehors de l’activité de connexion normal de l’ordinateur. Si le nombre de connexions, ou de l’heure du jour, des connexions, ou de l’emplacement à partir duquel les connexions sont demandées, ou d’autres caractéristiques relatifs à la connexion est considérablement différent de celui de la ligne de base, une alerte peut être générée. À nouveau, apprentissage automatique détermine ce qui est important.

## <a name="continuous-threat-intelligence-monitoring"></a>Analyse des menaces en continu intelligence

Le centre de sécurité Azure fonctionne recherches et données scientifiques équipes de sécurité qui surveillent en permanence les modifications dans le paysage des menaces. Cela inclut les initiatives suivantes :

- **Surveillance de Threat intelligence**: Threat intelligence inclut des mécanismes, des indicateurs, des implications et des conseils pratiques sur existantes ou de nouvelles menaces. Cette information est partagée dans la Communauté de la sécurité et Microsoft surveille en permanence le flux d’intelligence de menaces provenant de sources internes et externes.
- **Partage de signal**: aperçus des équipes de sécurité sur un large portefeuille de Microsoft de nuage et sur site périphériques de point de terminaison client, les serveurs et les services sont partagés et analysées. 
- **Spécialistes de la sécurité Microsoft**: engagement en cours avec des équipes au sein de Microsoft qui fonctionne dans les champs de sécurité spécialisés, comme l’analyse post-mortem et web de détection des attaques.
- **Optimisation de la détection**: algorithmes sont exécutés sur des ensembles de données client réel et les chercheurs en sécurité informatique travaillent avec les clients pour valider les résultats. Positifs de True et false sont utilisés pour affiner les algorithmes d’apprentissage machine.

Ces efforts combinés débouchent sur les détections de nouvelle et améliorées, vous pouvez bénéficier instantanément de – il n’y a aucune action à prendre.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à la détection du centre de sécurité Azure capacités de fonctionnement. Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Guide des opérations et la planification du centre de sécurité Azure](security-center-planning-and-operations-guide.md)
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
- [Alertes de sécurité par Type dans le centre de sécurité Azure](security-center-alerts-type.md)
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment surveiller la santé de vos ressources d’Azure.
- [Solutions de contrôle partenaire avec le centre de sécurité Azure](security-center-partner-solutions.md) : Apprenez à surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité d’Azure](http://blogs.msdn.com/b/azuresecurity/) , trouver des billets de blog sur la sécurité Azure et de conformité.
