<properties
   pageTitle="Meilleures pratiques pour les entreprises migrant vers Azure | Microsoft Azure"
   description="Décrit un échafaudage que les entreprises peuvent recourir pour garantir un environnement sécurisé et facile à gérer."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>ECHAFAUDAGE Azure entreprise - gouvernance d’abonnement normatifs

Plus les entreprises adoptent le cloud public pour son agilité et la flexibilité. Ils utilisent les forces du nuage pour générer des revenus ou à optimiser les ressources de l’entreprise. Microsoft Azure fournit une multitude de services que les entreprises peuvent assembler comme blocs de construction pour résoudre une vaste gamme d’applications et de charges de travail. 

Toutefois, il est souvent difficile de connaître où commencer. Après avoir décidé d’utiliser Azure, posez des questions se posent fréquemment :

- « Comment respecter nos obligations légales de souveraineté des données dans certains pays ? »
- « Comment être sûr que quelqu'un ne change pas par inadvertance un système critique ? »
- « Comment savoir que toutes les ressources sont prise en charge afin que je peux compte et facturation précise de nouveau ? »

La perspective d’un abonnement vide avec aucun garde-corps est une tâche ardue. Cet espace vide peut entraver votre migration vers Azure.

Cet article fournit un point de départ pour les spécialistes techniques répondre au besoin de gouvernance et équilibrer avec le besoin en souplesse. Elle introduit le concept d’un échafaudage de l’entreprise qui guide les organisations dans la mise en œuvre et la gestion de leurs abonnements Azure. 

## <a name="need-for-governance"></a>Besoin de gouvernance des

Lorsque vous déplacez sur Azure, vous devez résoudre le sujet de la gouvernance dès le début pour garantir le succès de l’utilisation du nuage au sein de l’entreprise. Malheureusement, l’heure et la bureaucratie de création d’un système de gouvernance complète signifie que certains groupes professionnels accéder directement à des fournisseurs sans impliquer informatiques de l’entreprise. Cette approche peut laisser l’entreprise ouverte vulnérabilités si les ressources ne sont pas gérés correctement. Les caractéristiques du cloud public - réactivité, de flexibilité et de tarification en fonction de la consommation - sont importants pour les groupes qui ont besoin de répondre rapidement aux demandes des clients (internes et externes). Mais, entreprise informatique doit s’assurer que les données et les systèmes sont efficacement protégés.

Dans la réalité, la structure est utilisée pour créer la base de la structure. L’échafaudage guide le plan général et fournit des points d’ancrage des systèmes plus permanents doit être monté. Une structure d’entreprise est le même : un ensemble de contrôles flexibles et capacités Azure qui fournissent la structure pour l’environnement et les points d’ancrage pour les services basés sur le cloud public. Il fournit les générateurs (informatique et de groupes) de base pour créer et joindre des nouveaux services.

La structure est basée sur les pratiques que nous avons collectées à partir des nombreux des contrats avec des clients de tailles diverses. Plage de ces clients de petites entreprises, développement de solutions dans le nuage à des entreprises du classement Fortune 500 et éditeurs de logiciels indépendants chargés de migration et de développement de solutions dans le nuage. La structure d’entreprise est « spécialisé » pour être flexible pour prendre en charge les charges de travail informatiques traditionnelles et de charges de travail agiles ; Ainsi, les développeurs qui créent des applications de logiciel-as-a-service (SaaS) basée sur les fonctionnalités de Azure.

La structure d’entreprise constitue le fondement même de chaque nouvel abonnement dans Azure. Il permet aux administrateurs d’assurer les charges de travail la configuration requise minimale de gouvernance d’une organisation sans empêcher les divisions et les développeurs à partir de leurs propres objectifs rapidement.

> [AZURE.IMPORTANT] La gouvernance est essentielle au succès d’Azure. Cet article vise la mise en oeuvre de technique d’un échafaudage de l’entreprise mais ne concerne que les processus et les relations entre les composants plus large. Gouvernance de règles des flux à partir du haut vers le bas et est déterminée par ce que l’entreprise souhaite atteindre. Naturellement, la création d’un modèle de gouvernance pour Azure comprend des représentants de l’informatique, mais plus important encore, il doit avoir une représentation forte de chefs de groupe d’entreprise et de gestion des risques et de la sécurité. En fin de compte, une structure d’entreprise est sur la réduction des risques pour l’entreprise afin de faciliter la mission et les objectifs de l’organisation.

L’image suivante décrit les composants de la structure. La Fondation s’appuie sur un plan solide pour les départements, les comptes et les abonnements. Les montants sont constitués de stratégies du Gestionnaire de ressources et les normes d’attribution de noms forts. Le reste de l’échafaudage est fourni à partir de fonctions Azure et fonctionnalités qui permettent à un environnement sécurisé et facile à gérer.

![composants d’ECHAFAUDAGE](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure s’est développée rapidement depuis son lancement en 2008. Cette croissance requis les équipes d’ingénieurs Microsoft à repenser leur approche en matière de gestion et de déploiement des services. Le modèle de gestionnaire de ressources Azure a été introduit en 2014 et remplace le modèle de déploiement classique. Le Gestionnaire de ressources permet aux entreprises de déployer plus facilement, organiser et contrôler les ressources Azure. Gestionnaire de ressources comprend la parallélisation lors de la création des ressources pour accélérer le déploiement de solutions complexes interdépendantes. Il inclut également le contrôle d’accès granulaire et la capacité de ressources de balise avec des métadonnées. Microsoft recommande de créer toutes les ressources dans le modèle de gestionnaire de ressources. La structure d’entreprise est explicitement conçu pour le modèle de gestionnaire de ressources.

## <a name="define-your-hierarchy"></a>Définir votre hiérarchie

La base de l’échafaudage est l’inscription d’entreprise Azure (et l’Enterprise Portal). L’inscription de l’entreprise définit la forme et utiliser des services Azure au sein d’une société et est la structure de gouvernance de base. Au sein de l’accord d’entreprise, les clients sont en mesure de subdiviser encore l’environnement dans des départements, des comptes et enfin, des abonnements. Un abonnement Azure est l’unité de base dont toutes les ressources sont contenues. Il définit également plusieurs limites dans Azure, par exemple nombre de cœurs, les ressources, etc..

![hiérarchie](./media/resource-manager-subscription-governance/agreement.png)

Chaque entreprise est différente et que la hiérarchie dans l’image précédente permet une grande flexibilité dans l’organisation au sein de la société Azure. Avant d’implémenter les instructions contenues dans ce document, vous devez modéliser votre hiérarchie et comprendre l’impact sur la facturation, l’accès aux ressources et de la complexité.

Les trois modèles courants pour les inscriptions Azure sont les suivants :

- Le modèle **fonctionnel**

    ![fonctionnel](./media/resource-manager-subscription-governance/functional.png)

- Le modèle **Centre de profit** 

    ![entreprise](./media/resource-manager-subscription-governance/business.png)

- Le modèle **géographique**

    ![géographique](./media/resource-manager-subscription-governance/geographic.png)

Vous appliquez la structure au niveau de l’abonnement pour étendre la gouvernance de besoins de l’entreprise dans l’abonnement.

## <a name="naming-standards"></a>Normes de dénomination

Le nom de premier pilier de l’échafaudage est conventions d’appellation standard. Les normes d’attribution de noms bien conçus vous permettent identifier les ressources sur le portail, sur une facture et dans des scripts. Le plus souvent, vous avez déjà conventions d’appellation standard pour une infrastructure sur site. Lors de l’ajout d’Azure dans votre environnement, vous devez étendre ces normes d’attribution de noms à vos ressources d’Azure. Norme de faciliter une gestion plus efficace de l’environnement à tous les niveaux.

> [AZURE.TIP]
>
> - Examiner et adopter où possible les [instructions de modèles et pratiques](./guidance/guidance-naming-conventions.md). Ce guide vous aide à déterminer une norme d’affectation des noms significatif.
> - Utilisez la casse mixte pour les noms des ressources (telles que myResourceGroup et vnetNetworkName). Remarque : Il existe certaines ressources, telles que les comptes de stockage, où la seule option consiste à utiliser des minuscules (et aucune des autres caractères spéciaux).
> - Envisagez l’utilisation de stratégies de gestionnaire de ressources Azure (décrites dans la section suivante) d’appliquer des normes d’affectation de noms.
 
## <a name="policies-and-auditing"></a>Audit et stratégies

Le deuxième pilier de l’échafaudage implique la création de [stratégies de gestionnaire de ressources Azure](resource-manager-policy.md) et [l’audit du journal d’activité](resource-group-audit.md). Stratégies du Gestionnaire de ressources vous donnent la possibilité de gérer le risque dans Azure. Vous pouvez définir des stratégies qui garantissent la souveraineté de données en limitant, d’application ou de certaines actions d’audit. 

- La stratégie est un système **d’Autoriser** par défaut. Vous contrôlez les actions par définition et affectation de stratégies aux ressources qui bloquent ou d’audit des actions sur les ressources.
- Les stratégies sont décrites par les définitions de stratégie dans un langage de définition de stratégie (conditions si-alors).
- Vous créez des stratégies avec JSON (Javascript Object Notation) fichiers mis en forme. Après avoir défini une stratégie, vous affectez à une portée particulière : abonnement, groupe de ressources. ou une ressource.

Les stratégies ont plusieurs actions qui permettent une approche précise pour vos scénarios. Les actions sont les suivantes :

-   **Refuser**: bloque la demande de ressources
-   **Audit**: autorise la demande, mais ajoute une ligne dans le journal d’activité (qui peut être utilisé pour fournir des alertes ou de déclencher des procédures opérationnelles)
-   **Append**: ajoute les informations spécifiées pour la ressource. Par exemple, s’il n’existe pas une balise « Centre de coût » sur une ressource, ajouter cette balise avec la valeur par défaut.

### <a name="common-uses-of-resource-manager-policies"></a>Utilisations courantes de stratégies du Gestionnaire de ressources

Les stratégies de gestionnaire de ressources Azure sont un outil puissant dans la boîte à outils Azure. Ils vous permettent d’éviter les coûts imprévus, pour identifier un centre de coût pour les ressources par le biais de balisage et à garantir le respect des exigences de conformité. Lorsque les stratégies sont combinées avec les fonctions d’audit intégrées, vous pouvez mode solutions complexes et souples. Les stratégies permettent aux entreprises de fournir les contrôles pour les charges de travail « Informatique traditionnelle » et « Agile » de charges de travail ; tel que, développement d’applications client. Les modèles les plus communs que nous voir pour les stratégies sont les suivantes :

- **Geo-conformité/données relevant de la souveraineté** - Azure fournit des régions dans le monde entier. Les entreprises souhaitent souvent contrôler où les ressources sont créées (que ce soit pour garantir la souveraineté des données ou pour garantir que les ressources sont créées près les consommateurs finaux des ressources).
- **Gestion des coûts** - abonnement Azure un peut contenir des ressources de nombreux types et d’échelle. Les sociétés souhaitent souvent afin de garantir que les abonnements standard évitent d’utiliser des ressources inutilement volumineux, ce qui peuvent coûter des centaines de dollars, un mois ou plus.
- **Par défaut le governance via les balises requises** - nécessitant des balises est une des fonctionnalités plus courantes et hautement souhaitées. À l’aide de stratégies de gestionnaire de ressources Azure entreprises sont en mesure de garantir qu’une ressource est correctement balisée. Les balises plus courantes sont : type de service, le propriétaire de la ressource et environnement (par exemple, production, test et développement)

**Exemples**

« Traditionnel informatique « abonnement pour les applications métier de

-   Appliquer des balises de service et le propriétaire de toutes les ressources
-   Restreindre la création de la ressource à la région de l’Amérique du Nord
-   Limiter la capacité à créer des ordinateurs virtuels de G-Series et les Clusters HDInsight

Environnement « Agile » pour une division de création d’applications en nuage

- Pour répondre aux exigences en matière de données relevant de la souveraineté, autoriser la création de ressources uniquement dans une région spécifique.
- Appliquer des balises d’environnement sur toutes les ressources. Si une ressource est créée sans la balise, ajoutez le **environnement : inconnu** balise à la ressource.
- Lorsque des ressources sont créés en dehors de l’Amérique du Nord, mais n’empêchent pas l’audit.
- Auditer les ressources de coûteux sont créées.

> [AZURE.TIP]
>
> L’utilisation la plus courante de stratégies du Gestionnaire de ressources entre des organisations est de contrôle *où* les ressources peuvent être créées et *Quels sont* les types de ressources peuvent être créées. En plus des contrôles *où* et *que*, de nombreuses entreprises utilisent des stratégies afin de garantir les ressources ont les métadonnées appropriées à facturer pour la consommation. Nous vous recommandons d’appliquer les stratégies au niveau de l’abonnement pour :
>
> - Geo-conformité/données relevant de la souveraineté
> - Gestion des coûts
> - Balises requises (déterminée par les besoins de l’entreprise, par exemple FacturerÀ, propriétaire de l’Application)
>
> Vous pouvez appliquer des stratégies supplémentaires à des niveaux inférieurs de la portée.
 
### <a name="audit---what-happened"></a>Audit - pourquoi ?

Pour afficher le fonctionne de votre environnement, vous devez auditer l’activité de l’utilisateur. La plupart des types de ressources dans Azure créent des journaux de diagnostic que vous pouvez analyser via un outil de journal ou dans Azure Operations Management Suite. Vous pouvez collecter des journaux d’activité sur plusieurs abonnements pour fournir un service ou vue d’entreprise. Les enregistrements d’audit sont à la fois un outil de diagnostic important et un mécanisme crucial pour déclencher des événements dans l’environnement Azure.

Placez des journaux d’activité à partir du Gestionnaire de ressources des déploiements permettent de déterminer les **opérations** qui ont eu et qui a effectué chacune d’elles. Journaux d’activité peuvent être collectées et agrégées à l’aide d’outils tels que journal Analytique.

## <a name="resource-tags"></a>Balises de ressources

Ajoutent des ressources à l’abonnement, les utilisateurs de votre organisation devient plus en plus important associer des ressources avec le service approprié, le client et l’environnement. Vous pouvez attacher des métadonnées aux ressources via des [balises](resource-group-using-tags.md). Vous utilisez des balises pour fournissent des informations sur la ressource ou le propriétaire. Les balises vous permettent non seulement de regrouper et de regrouper les ressources de différentes manières, mais utiliser ces données pour les besoins de refacturation. Vous pouvez baliser des ressources avec jusqu'à 15 paires de clé : valeur. 

Les balises de ressources sont flexibles et doivent être associés à la plupart des ressources. Exemples de balises de ressources courantes sont :

- BillTo
- Département (ou division)
- Environnement (Production, étape, développement)
- Couche (couche Web, couche Application)
- Propriétaire de l’application
- ProjectName

![balises](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Pour plus d’exemples de balises, consultez [recommandé de conventions d’appellation des ressources Azure](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Créer une stratégie de balisage qui identifie sur vos abonnements, les métadonnées sont nécessaires pour l’entreprise finance, sécurité, gestion des risques et la gestion globale de l’environnement. Envisagez une stratégie qui impose de repérage pour :
>
> - Groupes de ressources
> - Stockage
> - Ordinateurs virtuels
> - Serveurs web/environnements de Service d’application

## <a name="resource-group"></a>Groupe de ressources 

Le Gestionnaire de ressources vous permet de vous permet de placer les ressources en groupes significatifs pour l’affinité de facturation ou naturelle, de gestion. Comme mentionné précédemment, Azure dispose de deux modèles de déploiement. Dans le précédent modèle classique, l’unité de gestion de base, a été l’abonnement. Il était difficile de diviser des ressources au sein d’un abonnement, ce qui conduit à la création d’un grand nombre de souscriptions. Avec le modèle de gestionnaire de ressources, nous avons vu l’introduction des groupes de ressources. Groupes de ressources sont des conteneurs de ressources qui ont un cycle de vie commune ou partagent un attribut tel que « tous les serveurs SQL » ou « A ».

Groupes de ressources ne peut pas être contenues dans d’autres, et ressources ne peuvent appartenir qu’à un groupe de ressources. Vous pouvez appliquer certaines actions sur toutes les ressources dans un groupe de ressources. Par exemple, la suppression d’un groupe de ressources supprime toutes les ressources au sein du groupe de ressources. En règle générale, vous placez un ensemble de l’application ou le système associés dans le même groupe de ressources. Par exemple, une application à trois niveaux appelée Application Web de Contoso contient le serveur web, le serveur d’applications et le serveur SQL dans le même groupe de ressources.

> [AZURE.TIP]
>
> La façon dont vous organisez vos groupes de ressources peut-être différer des charges de travail « Informatique traditionnelle » aux charges de travail « IT Agile »
>
> - « Traditionnel informatique « charges de travail sont généralement regroupés par les éléments dans le même cycle de vie, telle qu’une application. Permet de regrouper par application pour la gestion des applications.
> - « Agile informatique « charges de travail ont tendance à se concentrer sur les applications en nuage des clients externes. Les groupes de ressources doivent refléter les couches de déploiement (par exemple les couche Web, couche application) et de gestion.

## <a name="role-based-access-control"></a>Contrôle d’accès basé sur les rôles

Vous sont vous demandez probablement « qui doit avoir accès aux ressources ? » et « comment contrôler cet accès ? » Autorisant ou interdisant l’accès au portail Azure et contrôle de l’accès aux ressources dans le portail sont cruciale. 

Lorsque Azure a été publié initialement, les contrôles d’accès à un abonnement ont été base : administrateur ou administrateur de collaboration. L’accès à un abonnement dans le standard modèle impliqué l’accès à toutes les ressources dans le portail. Ce manque de contrôle précis a entraîné la prolifération des abonnements pour fournir un niveau de contrôle d’accès raisonnable pour une inscription d’Azure.

Cette prolifération des abonnements n’est plus nécessaire. Avec contrôle d’accès basé sur les rôles, vous pouvez affecter des utilisateurs aux rôles standard (par exemple, « lecteur » et « rédacteur » des courants rôles). Vous pouvez également définir des rôles personnalisés.

> [AZURE.TIP]
>  
> - Se connecter à votre banque d’identités d’entreprise (généralement Active Directory) pour Azure Active Directory à l’aide de l’outil de connexion d’Active Directory.
> - Contrôle l’Admin/Co-Admin d’un abonnement à l’aide d’une identité gérée. **** N’affectez pas Admin/Co-admin à un nouveau propriétaire de l’abonnement. Utilisez plutôt les rôles RBAC pour fournir les droits de **propriétaire** à un groupe ou individuellement.
> - Ajouter des utilisateurs Azure à un groupe (par exemple, X aux propriétaires d’applications) dans Active Directory. Utilisez le groupe synchronisé pour fournir des membres d’un groupe les droits appropriés pour gérer le groupe de ressources contenant l’application.
> - Suivez le principe de l’octroi de **privilèges** nécessaires pour effectuer le travail prévu. Par exemple :
>     - Groupe de déploiement : Un groupe qui n’est pas en mesure de déployer des ressources.
>     - : Ordinateur virtuel un groupe d’administration Qui est capable de redémarrer des ordinateurs virtuels (pour les opérations)

## <a name="azure-resource-locks"></a>Verrous de ressources Azure

À mesure que votre organisation ajoute des services essentiels à l’abonnement, il devient de plus en plus important de s’assurer que ces services sont disponibles afin d’éviter l’interruption de service. [Verrouillages des ressources](resource-group-lock-resources.md) vous permettent de limiter les opérations sur des ressources de grande valeur où la modification ou la suppression aurait un impact significatif sur vos applications ou l’infrastructure de nuage. Vous pouvez appliquer des verrous à un abonnement, le groupe de ressources ou la ressource. Généralement, vous appliquez des verrous sur les ressources telles que les réseaux virtuels, des passerelles et des comptes de stockage. 

Verrouillages des ressources prend actuellement en charge deux valeurs : CanNotDelete et en lecture seule. CanNotDelete signifie que les utilisateurs (disposant des droits appropriés) peuvent toutefois lire ou modifier une ressource mais ne peut le supprimer. En lecture seule signifie que les utilisateurs autorisés ne peuvent pas supprimer ou modifier une ressource.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` actions.
Les rôles intégrés, seuls propriétaire et administrateur d’accès utilisateur sont accordées à ces actions.

> [AZURE.TIP] Options de réseau de base doivent être protégées avec des verrous. Suppression accidentelle d’une passerelle VPN de site à site serait désastreux à un abonnement Azure. Azure ne vous autorise pas à supprimer un réseau virtuel qui est en cours d’utilisation, mais l’application de restrictions plus est une mesure utile. Les stratégies sont également essentiels pour la gestion des contrôles appropriés. Nous vous conseillons d’appliquer un verrou de **CanNotDelete** aux stratégies qui sont en cours d’utilisation.
>
> - Réseau virtuel : CanNotDelete
> - Groupe de sécurité réseau : CanNotDelete
> - Stratégies : CanNotDelete

## <a name="core-networking-resources"></a>Ressources de mise en réseau de base

Accès aux ressources peut être interne (au sein de réseau de la société) ou externe (sur internet). Il est facile pour les utilisateurs de votre organisation par inadvertance ressources au mauvais endroit, et les ouvrir potentiellement à accès malveillant. Comme avec les périphériques sur site, les entreprises doivent ajouter les contrôles appropriés pour s’assurer que les utilisateurs Azure prendre les bonnes décisions. Pour la gouvernance de l’abonnement, nous identifions les ressources principales qui fournissent le contrôle d’accès de base. Les ressources principales comprennent :

- **Réseaux virtuels** sont des objets conteneur de sous-réseaux. Si elle est absolument nécessaire, il est souvent utilisé lors de la connexion d’applications à des ressources internes de l’entreprise.
- **Les groupes de sécurité de réseau** sont similaires à un pare-feu et fournissent les règles pour la façon dont une ressource peut « parler » sur le réseau. Ils fournissent un contrôle granulaire sur la si un sous-réseau (ou la machine virtuelle) peuvent se connecter à Internet ou d’autres sous-réseaux dans le même réseau virtuel.

![réseau de base](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Créer des réseaux virtuels dédiés pour les charges de travail externe et interne de charges de travail. Cette approche réduit les risques de mise par inadvertance des ordinateurs virtuels qui sont destinés à des charges de travail internes dans un espace en vis-à-vis externe.
> - Les groupes de sécurité de réseau sont essentiels pour cette configuration. Au minimum, bloquer l’accès à internet à partir de réseaux virtuels internes et bloquer l’accès au réseau d’entreprise à partir de réseaux virtuels externes.

### <a name="automation"></a>Automation

Gestion de ressources individuellement est à la fois du temps et potentiellement risquée pour certaines opérations. Azure fournit diverses fonctionnalités d’automatisation automatisation d’Azure, logique d’applications et fonctions d’Azure. [Automation d’Azure](./automation/automation-intro.md) permet aux administrateurs de créer et définir des procédures opérationnelles pour gérer les tâches courantes de gestion des ressources. Vous créez des procédures opérationnelles à l’aide d’un éditeur de code PowerShell ou un éditeur graphique. Vous pouvez générer des flux de travail de plusieurs étapes complexes. Automation Azure est souvent utilisée pour gérer les tâches courantes telles que l’arrêt des ressources inutilisées, ou la création de ressources en réponse à un déclencheur spécifique sans avoir besoin d’intervention humaine.

> [AZURE.TIP]
>
> - Créer un compte Azure Automation et passez en revue le disponible procédures opérationnelles (ligne de commande et graphique) disponibles dans la [Galerie de procédure opérationnelle](./automation/automation-runbook-gallery.md).
> - Importer et personnaliser les procédures opérationnelles de clé pour votre propre utilisation.
>
> Un scénario courant est la possibilité de démarrage/arrêt des machines virtuelles sur un calendrier. Voici un exemple procédures opérationnelles qui sont disponibles dans la galerie de vous apprendre à développer et gérer ce scénario.


## <a name="azure-security-center"></a>Le centre de sécurité Azure

Parmi les plus grands BLOQUEURS à l’adoption du nuage ont été peut-être les problèmes de sécurité. Gestionnaires des risques informatiques et services de sécurité est nécessaire garantir la sécurité des ressources dans Azure. 

Le [Centre de sécurité Azure](./security-center/security-center-intro.md) fournit une vue centralisée de l’état de sécurité de ressources dans les abonnements et fournit des recommandations permettant d’empêcher les ressources de compromis. Il peut activer des stratégies plus granulaires (par exemple, application des stratégies à des groupes de ressources spécifiques qui permettent de l’entreprise d’adapter leur position sur les risques qu’ils font face). Enfin, le centre de sécurité Azure est une plate-forme ouverte qui permet aux partenaires de Microsoft et des fournisseurs de logiciels indépendants créer des logiciels qui se branche dans le centre de sécurité Azure pour améliorer ses fonctionnalités. 

> [AZURE.TIP]
>
> Le centre de sécurité Azure est activé par défaut dans chaque abonnement. Toutefois, vous devez activer la collecte des données à partir d’ordinateurs virtuels pour permettre le centre de sécurité Azure installer son agent et commencer à collecter des données.
>
> ![collecte de données](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous savez sur la gouvernance de l’abonnement, il est temps de voir ces recommandations en pratique. Voir des [exemples de mise en œuvre de la gouvernance d’abonnement Azure](resource-manager-subscription-examples.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) a contribué à cette rubrique.*
