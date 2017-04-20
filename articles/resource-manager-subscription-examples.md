<properties
   pageTitle="Scénarios et exemples de gouvernance de l’abonnement | Microsoft Azure"
   description="Fournit des exemples d’implémentation de gouvernance abonnement Azure pour des scénarios courants."
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

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exemples d’implémentation d’ECHAFAUDAGE d’entreprise Azure

Cette rubrique fournit des exemples de la façon dont une entreprise peut implémenter les recommandations pour une [structure d’entreprise Azure](resource-manager-subscription-governance.md). Il utilise une société fictive nommée Contoso pour illustrer les meilleures pratiques pour des scénarios courants. 

## <a name="background"></a>Arrière-plan

Contoso est une société dans le monde entier qui fournit des solutions de chaîne d’approvisionnement pour les clients de tous les éléments d’un modèle « Logiciel en tant que Service » à un modèle de package déployé sur site.  Ils développement des logiciels dans le monde des centres de développement en Inde, États-Unis et Canada. 

La partie de l’ISV de la société est divisée en plusieurs unités opérationnelles indépendantes qui gèrent des produits dans une entreprise importante. Chaque division possède ses propres développeurs, responsables de produits et des architectes. 

La division de Services de technologie d’entreprise (ETS) offre des capacités d’informatique centralisée et gère plusieurs centres de données où les divisions hébergent leurs applications. Et gère les centres de données, l’organisation ETS fournit et gère les centraliser la collaboration (par exemple, les e-mails et sites Web) et les services de téléphonie/réseau. Ils gèrent également les charges de travail des clients pour les unités commerciales plus petites qui ne disposent pas du personnel opérationnel. 

Les personnages suivants sont utilisés dans cette rubrique :

- Dave est l’administrateur ETS Azure.
- Alice est directeur du développement de Contoso de la division de chaîne d’approvisionnement. 

Contoso doit créer une application de métier et une application côté client. Il a décidé d’exécuter des applications sur Azure. Lit la rubrique [gouvernance d’abonnement normatives](resource-manager-subscription-governance.md) et est prêt à mettre en oeuvre les recommandations. 

## <a name="scenario-1-line-of-business-application"></a>Scénario 1 : métier d’application

Contoso crée un système de gestion du code source (BitBucket) pour être utilisée par les développeurs dans le monde entier.  L’application utilise l’Infrastructure en tant que Service (IaaS) pour héberger et se compose de serveurs web et un serveur de base de données. Les développeurs accéder aux serveurs dans leurs environnements de développement, mais ils ne doivent pas accéder aux serveurs dans Azure. Contoso ETS souhaite autoriser le propriétaire de l’application et l’équipe gérer l’application. L’application est uniquement disponible sur le réseau de l’entreprise de Contoso. Dave doit définir l’abonnement pour cette application. L’abonnement sera également héberger des autres logiciels liés au développement à l’avenir.  

### <a name="naming-standards--resource-groups"></a>Les normes d’attribution de noms et groupes de ressources

Dave crée une souscription pour prendre en charge des outils de développement qui sont communes à toutes les divisions. Il a besoin créer des noms évocateurs pour les groupes de ressources et d’abonnement (pour l’application et les réseaux). Il crée les groupes suivants d’abonnement et de ressources :

| Élément | Nom | Description |
| ---- | ---- | ----------- |
| Abonnement | Production de DeveloperTools de Contoso ETS | Prend en charge les outils de développement courants |
| Groupe de ressources | rgBitBucket | Contient le serveur d’application web et le serveur de base de données |
| Groupe de ressources | rgCoreNetworks | Contient les réseaux virtuels et la connexion de la passerelle de site à site |


### <a name="role-based-access-control"></a>Contrôle d’accès basé sur les rôles

Après avoir créé son abonnement, Dave souhaite s’assurer que les équipes appropriées et les propriétaires d’applications peuvent accéder à leurs ressources. Dave reconnaît que chaque équipe a des besoins différents. Il utilise les groupes qui ont été synchronisées à partir sur site de Contoso Active Directory (AD) pour Azure Active Directory et fournit le niveau de droit d’accès aux équipes. 

Dave attribue les rôles suivants pour l’abonnement : 

| Rôle | Affecté à | Description |
| ---- | ----------- | ----------- |
| [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) | Gestion d’ID à partir de Contoso AD | Ce code est contrôlé avec juste dans access Time (JIT) via l’outil de gestion des identités de Contoso et garantit que les accès de propriétaire d’abonnement est entièrement auditées. |
| [Gestionnaire de sécurité](./active-directory/role-based-access-built-in-roles.md#security-manager) | Service de gestion des risques et de la sécurité | Ce rôle permet aux utilisateurs de consulter le centre de sécurité Azure et l’état des ressources. |
| [Collaborateur du réseau](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Équipe réseau | Ce rôle permet d’équipe du réseau de Contoso gérer la connexion VPN de Site à Site et les réseaux virtuels. |
| *Rôle personnalisé* | Propriétaire de l’application | Dave crée un rôle qui accorde la possibilité de modifier les ressources dans le groupe de ressources. Pour plus d’informations, voir [Rôles personnalisés dans Azure RBAC](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Stratégies de

Dave a les exigences suivantes pour la gestion des ressources de l’abonnement :

- Les outils de développement prenant en charge les développeurs dans le monde entier, qu’il ne souhaite pas empêcher les utilisateurs de la création de ressources dans une région. Toutefois, il doit savoir où les ressources sont créées. 
- Il est concerné par les coûts. Par conséquent, il souhaite empêcher les propriétaires d’applications de créer des machines virtuelles inutilement coûteux.  
- Étant donné que cette application sert aux développeurs de nombreuses divisions, il souhaite balise chaque ressource avec le propriétaire d’unité et l’application métier. À l’aide de ces balises, ETS peut facturer les équipes appropriés.

Il crée des [stratégies du Gestionnaire de ressources](resource-manager-policy.md)suivantes : 

| Champ | Effet | Description |
| ----- | ------ | ----------- |
| emplacement | audit | Auditer la création des ressources dans n’importe quelle région |
| type de | refuser | Refuser la création de machines virtuelles de la série G |
| balises | refuser | Exiger la balise propriétaire de l’application |
| balises | refuser | Nécessite de balise de centre de coût |
| balises | ajouter | Ajouter le nom de balise **division** et la valeur de balise **ETS** à toutes les ressources |


### <a name="resource-tags"></a>Balises de ressources

Dave sait qu’il doit avoir des informations spécifiques sur la nomenclature pour identifier le centre de coût pour la mise en oeuvre de BitBucket. En outre, Dave souhaite savoir ETS possède toutes les ressources. 

Il ajoute des [balises](resource-group-using-tags.md) suivantes pour les groupes de ressources et les ressources. 
 
| Nom de la balise | Valeur de la balise |
| -------- | --------- |
| ApplicationOwner | Le nom de la personne qui gère cette application. |
| Centre de coût | Le centre de coût du groupe qui a payé pour la consommation Azure. |
| Division | **ETS** (la division associée à l’abonnement) |

### <a name="core-network"></a>Réseau de base

L’équipe de gestion Contoso ETS informations risques et examine la proposition de plan de Dave pour déplacer l’application Azure. Ils veulent s’assurer que l’application n’est pas exposée à internet.  Dave a également des applications de développeur à l’avenir seront déplacées vers Azure. Ces applications nécessitent des interfaces publiques.  Pour répondre à ces exigences, il fournit à la fois internes et externes des réseaux virtuels et un groupe de sécurité pour limiter l’accès réseau.

Il crée les ressources suivantes :

| Type de ressource | Nom | Description |
| ------------- | ---- | ----------- |
| Réseau virtuel | vnInternal | Utilisé avec l’application BitBucket et est connecté au réseau d’entreprise de Contoso via ExpressRoute.  Un sous-réseau (sbBitBucket) fournit à l’application avec un espace d’adresse IP spécifique. |
| Réseau virtuel | vnExternal | Disponible pour les futures applications qui nécessitent des points de terminaison accessible au public. |
| Groupe de sécurité réseau | nsgBitBucket | Garantit que la surface d’attaque de cette charge de travail est réduite en autorisant des connexions uniquement sur le port 443 pour le sous-réseau où l’application réside (sbBitBucket). |

### <a name="resource-locks"></a>Verrous de ressources 

Dave reconnaît que la connectivité du réseau d’entreprise de Contoso au réseau virtuel interne doit être protégée à partir de n’importe quel script versatiles ou d’une suppression accidentelle. 

Il crée le [verrou de ressource](resource-group-lock-resources.md)de suivantes :

| Type de verrou | Ressources | Description |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Empêche les utilisateurs de supprimer le réseau virtuel ou sous-réseaux, mais n’empêche pas l’ajout de nouveaux sous-réseaux. |

### <a name="azure-automation"></a>Automation Azure 

Dave n’a rien à automatiser pour cette application. Bien qu’il a créé un compte Azure Automation, il ne l’utiliser initialement. 

### <a name="azure-security-center"></a>Le centre de sécurité Azure 

Gestion des services informatiques de Contoso doit rapidement identifier et gérer les menaces. Ils souhaitent également comprendre quels problèmes peuvent exister.  

Pour répondre à ces besoins, le [Centre de sécurité Azure](./security-center/security-center-intro.md)et fournit l’accès au rôle de gestionnaire de sécurité. 

## <a name="scenario-2-customer-facing-app"></a>Scénario 2 : côté client app

La direction de l’entreprise dans le centre de profit de chaîne d’approvisionnement a identifié plusieurs opportunités afin d’augmenter l’engagement avec les clients de Contoso à l’aide d’une carte de fidélité. L’équipe d’Alice doit créer cette application et décide que Azure augmente leur capacité à répondre aux besoins de l’entreprise. Alice fonctionne avec Dave de ETS pour configurer deux abonnements pour le développement et le fonctionnement de cette application.

### <a name="azure-subscriptions"></a>Abonnements Azure 

Dave se connecte au portail d’entreprise Azure et voit que le département de la chaîne d’approvisionnement existe déjà.  Toutefois, lorsque ce projet est le premier projet de développement de l’équipe de la chaîne d’approvisionnement dans Azure, Dave reconnaît la nécessité d’un nouveau compte pour l’équipe de développement d’Alice.  Il crée le compte « R & D » de son équipe et attribue un accès à Alice. Alice se connecte via le portail de compte et crée deux abonnements : destiné à contenir les serveurs de développement et destiné à contenir les serveurs de production.  Elle suit les normes d’attribution de noms précédemment établies lorsque vous créez les abonnements suivants : 

| Utilisation de l’abonnement | Nom |
| ---------------- | ---- |
| Développement | Développement de LoyaltyCard de ResearchDevelopment de SupplyChain |
| Production | Production de LoyaltyCard d’opérations SupplyChain |

### <a name="policies"></a>Stratégies de

Dave Alice discutera de l’application et identifier que cette application ne sert qu’aux clients dans la région Amérique du Nord.  Alice et son équipe envisagez d’utiliser l’environnement de Service d’Application d’Azure et les Azure SQL pour créer l’application. Il faudra créer des ordinateurs virtuels au cours du développement.  Alice souhaite faire en sorte que ses ressources que nécessaires pour Explorer et examiner les problèmes sans extraction dans ETS. 

Pour l' **abonnement de développement**, ils créent la stratégie suivante :

| Champ | Effet | Description |
| ----- | ------ | ----------- |
| emplacement | audit | Auditer la création des ressources dans toute la région. |

Elles ne limitent pas le type de référence, qu'un utilisateur peut créer dans le développement, et ils ne nécessitent pas de balises pour les groupes de ressources ou les ressources.

Pour l' **abonnement de production**, ils créent les stratégies suivantes :

| Champ | Effet | Description |
| ----- | ------ | ----------- |
| emplacement | refuser | Refuser la création de ressources à l’extérieur les centres de données américains. |
| balises | refuser | Exiger la balise propriétaire de l’application |
| balises | refuser | Nécessite de balise de service. | 
| balises | ajouter | Ajouter des balises à chaque groupe de ressources qui indique l’environnement de production. |

Elles ne limitent pas le type de référence, qu'un utilisateur peut créer dans la production.

### <a name="resource-tags"></a>Balises de ressources 

Dave sait qu’il doit avoir des informations spécifiques pour identifier les groupes d’activité correcte pour la facturation et la propriété. Il définit les balises de ressources pour les ressources et les groupes de ressources. 
 
Nom de la balise | Valeur de la balise |
| -------- | --------- |
| ApplicationOwner | Le nom de la personne qui gère cette application. |
| Département | Le centre de coût du groupe qui a payé pour la consommation Azure. |
| EnvironmentType | **Production** (Même si l’abonnement comprend la **Production** dans le nom, y compris cette balise permet de faciliter l’identification lors de la recherche dans les ressources dans le portail, ou sur la facture.) |

### <a name="core-networks"></a>Réseaux de base

L’équipe de gestion Contoso ETS informations risques et examine la proposition de plan de Dave pour déplacer l’application Azure. Ils veulent s’assurer que l’application de la carte de fidélité est correctement isolée et protégée dans un réseau de la DMZ.  Pour satisfaire à cette exigence, Dave et Alice créent un réseau virtuel externe et un groupe de sécurité réseau pour isoler l’application de la carte de fidélité à partir du réseau d’entreprise de Contoso.  

Pour l' **abonnement de développement**, ils créent :

| Type de ressource | Nom | Description |
| ------------- | ---- | ----------- |
| Réseau virtuel | vnInternal | Fait Office de l’environnement de développement de carte de fidélité de Contoso et est connecté au réseau d’entreprise de Contoso via ExpressRoute. |

Pour l' **abonnement de production**, ils créent :

| Type de ressource | Nom | Description |
| ------------- | ---- | ----------- |
| Réseau virtuel | vnExternal | Héberge l’application de la carte de fidélité et n’est pas connecté directement à Contoso ExpressRoute. Code est envoyée via leur système de Code Source directement pour les services PaaS. |
| Groupe de sécurité réseau | nsgBitBucket | Garantit que la surface d’attaque de cette charge de travail est réduite en autorisant uniquement les communications entrant sur TCP 443.  Contoso étudie également l’utilisation d’un pare-feu d’Application Web pour une protection supplémentaire. |  

### <a name="resource-locks"></a>Verrous de ressources 

Dave Alice conférer et décider d’ajouter des verrouillages des ressources sur des ressources clés dans l’environnement pour empêcher la suppression accidentelle au cours d’une émission de code errant. 

Ils créent le verrou suivant :

| Type de verrou | Ressources | Description |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | Pour empêcher les utilisateurs de supprimer le réseau virtuel ou sous-réseaux. Le verrou n’empêche pas l’ajout de nouveaux sous-réseaux. |

### <a name="azure-automation"></a>Automation Azure 

Alice et son équipe de développement ont des procédures opérationnelles d’étendue pour gérer l’environnement pour cette application. Les procédures opérationnelles permettant l’ajout/la suppression de nœuds pour l’application et les autres tâches DevOps. 

Pour utiliser ces procédures opérationnelles, elles permettent [l’automatisation](./automation/automation-intro.md).

### <a name="azure-security-center"></a>Le centre de sécurité Azure 

Gestion des services informatiques de Contoso doit rapidement identifier et gérer les menaces. Ils souhaitent également comprendre quels problèmes peuvent exister.  

Pour répondre à ces exigences, Dave permet le centre de sécurité Azure. Il garantit que le centre de sécurité Azure surveille les ressources et fournit l’accès aux équipes de sécurité et DevOps. 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles du Gestionnaire de ressources, consultez [méthodes conseillées pour la création de modèles du Gestionnaire de ressources Azure](resource-manager-template-best-practices.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) a contribué à cette rubrique.*
