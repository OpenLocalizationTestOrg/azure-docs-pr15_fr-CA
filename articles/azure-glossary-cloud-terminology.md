<properties
    pageTitle="Glossaire Azure - dictionnaire Azure | Microsoft Azure"
    description="Le glossaire Azure permet de comprendre la terminologie de nuage sur la plateforme Azure. Ce court dictionnaire Azure fournit les définitions des termes courants de nuage pour Azure."
    keywords="Dictionnaire Azure, la terminologie du nuage, Azure glossaire, définitions, termes de nuage"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossaire de Microsoft Azure : un dictionnaire des termes de nuage sur la plateforme Azure

Le glossaire Microsoft Azure est un dictionnaire court de la terminologie de nuage pour la plateforme Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Rechercher les définitions de service et les autres conditions de nuage

* Pour les définitions de services Azure et leurs AWS équivalents voir [Microsoft Azure et Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Générales en matière de conditions de nuage voir [Cloud computing des termes](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Le glossaire Azure avec les deux références ci-dessus fournit une taxonomie de bout en bout pour Azure et de l’industrie de nuage.  

## <a name="azure-glossary-list"></a>Liste de glossaire Azure


### <a name="account"></a>compte  
Un travail ou une école ou un compte personnel Microsoft qui est utilisé pour accéder et gérer un abonnement Azure.  
Voir aussi [comment Azure abonnements sont associés à Active Directory de Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>ensemble de disponibilité  
Une collection d’ordinateurs virtuels qui sont gérés ensemble afin de fournir la fiabilité et redondance des applications. L’utilisation d’un ensemble de disponibilité garantit que, pendant un événement d’entretien planifié ou au moins un ordinateur virtuel est disponible.  
Voir aussi [gestion de la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou de [Gérer la disponibilité des ordinateurs virtuels Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Modèle de déploiement classique d’Azure  
Un des deux [modèles de déploiement](resource-manager-deployment-model.md) utilisés pour déployer des ressources dans Azure (le nouveau modèle est le Gestionnaire de ressources Azure). Certaines ressources Azure peuvent être déployés dans un seul modèle ou de l’autre, alors que d’autres peuvent être déployés dans les deux modèles. Conseils pour les détails des ressources Azure qui model(s) une ressource peut être déployé avec.


### <a name="cli"></a>Azure interface de ligne de commande (CLI)  
Une [interface de ligne de commande](xplat-cli-install.md) qui peut être utilisé pour gérer des services Azure à partir de Windows et Linux PC OSX.


### <a name="powershell"></a>PowerShell Azure  
Une [interface de ligne de commande](powershell-install-configure.md) pour gérer les services Azure via une ligne de commande à partir de PC sous Windows. Certains services ou certaines fonctionnalités du service peuvent être gérées uniquement par PowerShell ou l’interface CLI. Conseils pour les détails de chaque ressource Azure qui model(s) une ressource peut être déployé avec.   
Voir également [comment installer et configurer Azure PowerShell](powershell-install-configure.md)


### <a name="arm-model"></a>Modèle de déploiement de gestionnaire de ressources Azure  
Un des deux [modèles de déploiement](resource-manager-deployment-model.md) utilisés pour déployer des ressources dans Microsoft Azure (l’autre est le modèle de déploiement classique). Certaines ressources Azure peuvent être déployés dans un seul modèle ou de l’autre, alors que d’autres peuvent être déployés dans les deux modèles. Conseils pour les détails des ressources Azure qui model(s) une ressource peut être déployé avec.


### <a name="fault-domain"></a>domaine d’erreur  
Collection d’ordinateurs virtuels dans un jeu de disponibilité peut échouer en même temps. Un exemple est un groupe d’ordinateurs qui partagent un commutateur de source et le réseau d’alimentation commun dans un rack. Dans Azure, les ordinateurs virtuels dans un jeu de disponibilité sont automatiquement répartis sur plusieurs domaines d’erreur.  
Voir aussi [gestion de la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou de [Gérer la disponibilité des ordinateurs virtuels Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>geo  
Une limite définie pour la délégation de données qui en général contient deux ou plusieurs régions. Les limites peuvent être au sein ou au-delà des frontières nationales et sont influencées par le règlement de la taxe. Chaque région possède au moins une région. Asie-Pacifique et Japon sont des exemples de zones géographiques. Également appelé *geography*.  
Voir aussi [les régions Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>geo-réplication  
Le processus de réplication automatique de contenus tels que les objets BLOB, les tables et les files d’attente dans une paire régionale.  
Voir aussi [Active géo-réplication de la base de données SQL Azure](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>image  
Un fichier qui contient le système d’exploitation et la configuration de l’application qui peut être utilisée pour créer un nombre illimité de machines virtuelles. Dans Azure, il existe deux types d’images : machine virtuelle d’images et images de système d’exploitation. Une image de machine virtuelle comprend un système d’exploitation et tous les disques attachés à une machine virtuelle lors de la création de l’image. Une image de système d’exploitation contient uniquement un système d’exploitation généralisé avec aucune configuration de disque de données.  
Voir aussi [Naviguer et sélectionnez images de machine virtuelle Windows dans Azure avec PowerShell ou l’interface CLI](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>limites  
Le nombre de ressources qui peuvent être créés ou les bancs d’essai des performances est possible. Les limites sont généralement associés à des abonnements, des services et des offres.  
Voir aussi [abonnement Azure et les limites de service, les quotas et les contraintes](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>l’équilibreur de charge  
Une ressource qui distribue le trafic entrant sur les ordinateurs d’un réseau. Dans Azure, un équilibreur de charge répartit le trafic vers des machines virtuelles définies dans un jeu d’équilibrage de la charge. Un [équilibreur de charge](./load-balancer/load-balancer-overview.md) peuvent être exposés à internet, ou elle peut être interne.  


### <a name="offer"></a>offre  
La tarification, les crédits et les conditions connexes applicables à un abonnement Azure.  
Consultez l' [Azure offre la page Détails](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>portail  
Le portail Web sécurisé permet de déployer et de gérer des services Azure.  Il existe deux portails : le [portail Azure](http://portal.azure.com/) et le [portail classique](http://manage.windowsazure.com/). Certains services sont disponibles dans les deux portails, alors que d’autres sont disponibles uniquement dans un ou l’autre. Le [graphique de la disponibilité de portail Azure](https://azure.microsoft.com/features/azure-portal/availability/) répertorie les services qui sont disponibles dans le portail.  


### <a name="region"></a>région  
Une zone dans une zone géographique frontières pas croix nationales et contient un ou plusieurs centres de données. Tarification, services régionaux et offre les types sont exposées au niveau de la région. Une région est généralement associée à une autre région, qui peut être jusqu'à plusieurs centaines distance, pour former une paire régionale. Paires régionaux peuvent servir en tant que mécanisme de reprise après sinistre et les scénarios de haute disponibilité. Également appelée en général en tant *qu’emplacement*.  
Voir aussi [les régions Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>ressources  
Un élément qui fait partie de votre solution Azure. Chaque service Azure vous permet de déployer différents types de ressources, telles que les bases de données ou des machines virtuelles.   
Voir aussi [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>groupe de ressources  
Dans le Gestionnaire de ressources qui contient les ressources associées à une application conteneur. Le groupe de ressources peut inclure toutes les ressources d’une application, ou que les ressources sont regroupés de manière logique. Vous pouvez décider comment vous souhaitez affecter des ressources aux groupes de ressources en fonction de ce que fait le mieux à votre organisation.  
Voir aussi [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Modèle de gestionnaire de ressources  
Un fichier JSON qui définit de façon déclarative une ou plusieurs ressources Azure et qui définit les dépendances entre les ressources déployées. Le modèle peut être utilisé pour déployer les ressources à plusieurs reprises et de façon cohérente.  
Voir aussi [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>fournisseur de ressources  
Un service qui fournit les ressources que vous pouvez déployer et gérer par le biais du Gestionnaire de ressources. Chaque fournisseur de ressources offre des opérations permettant de travailler avec les ressources qui sont déployés. Les fournisseurs de ressources sont accessibles via le portail d’Azure, Azure PowerShell et plusieurs kits de développement de programmation.  
Voir aussi [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>rôle  
Un moyen de contrôle d’accès qui peuvent être affectés à des utilisateurs, des groupes et des services. Les rôles sont en mesure d’effectuer des actions telles que créer, gérer et lire sur les ressources d’Azure.  
Voir aussi [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>contrat de niveau de service (SLA)  
Le contrat qui décrit les engagements de Microsoft pour un temps de fonctionnement et la connectivité. Chaque service Azure a un SLA spécifique.  
Consultez également [les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>compte de stockage  
Un compte de stockage qui vous donne accès aux services Azure Blob, la file d’attente, Table et fichier dans le stockage Azure. Votre compte de stockage fournit l’espace de noms unique pour vos objets de données de stockage Azure.  
Voir aussi [les comptes de stockage sur Azure](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>abonnement  
Contrat d’un client Microsoft qui leur permet d’obtenir des services Azure. Le prix de l’abonnement et les termes associés sont régis par l’offre choisie pour l’abonnement. Consultez le [contrat d’abonnement en ligne de Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Voir aussi [comment Azure abonnements sont associés à Active Directory de Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>balise  
Un terme d’indexation qui vous permet de catégoriser les ressources en fonction de vos besoins en matière de gestion ou de facturation. Vous pouvez utiliser les balises lorsque vous avez un ensemble complexe de groupes de ressources et de ressources, et que vous avez besoin visualiser ces éléments de la manière qui vous convient. Par exemple, vous pourriez baliser des ressources qui ont un rôle similaire dans votre organisation ou qui appartiennent au même service.  
Voir aussi [en utilisant des balises pour organiser vos ressources Azure](resource-group-using-tags.md)


### <a name="update-domain"></a>mettre à jour le domaine  
La collection d’ordinateurs virtuels dans un jeu de disponibilité sont mis à jour en même temps. Les ordinateurs virtuels dans le même domaine de mise à jour sont redémarrés ensemble pendant les maintenances planifiées. Azure ne redémarre jamais plus d’un domaine de mise à jour à la fois. Également appelé un domaine de mise à niveau.  
Voir aussi [gestion de la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou de [Gérer la disponibilité des ordinateurs virtuels Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>machine virtuelle  
L’implémentation logicielle d’un ordinateur physique qui exécute un système d’exploitation. Plusieurs machines virtuelles peuvent s’exécuter simultanément sur le même matériel. Dans Azure, les ordinateurs virtuels sont disponibles dans une variété de tailles.  
Consultez également la [documentation de Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>extension de la machine virtuelle  
Une ressource qui implémente des comportements ou des fonctionnalités permettant de soit autres programmes de travail ou de fournir la possibilité d’interagir avec un ordinateur en cours d’exécution. Par exemple, vous pouvez utiliser l’extension de l’accès de la machine virtuelle pour réinitialiser ou modifier les valeurs d’accès à distance sur un ordinateur virtuel Azure.  
Voir aussi [sur les extensions de la machine virtuelle et fonctionnalités (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) ou [sur les extensions de la machine virtuelle et de fonctionnalités (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>réseau virtuel  
Un réseau qui assure la connectivité entre vos ressources Azure isolé de tous les autres locataires Azure. Il peut être connecté à d’autres réseaux virtuels Azure via une [Passerelle de VPN Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) et à votre réseau local à l’aide de [plusieurs options](./vpn-gateway/vpn-gateway-cross-premises-options.md). Vous pouvez contrôler totalement les paramètres blocs d’adresses IP, DNS, les stratégies de sécurité et des tables de routage au sein de ce réseau.  
Voir aussi [vue d’ensemble du réseau virtuel](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Voir aussi**  
- [Mise en route avec Azure](https://azure.microsoft.com/get-started/)
- [Centre de ressources de cloud](https://azure.microsoft.com/resources/)  
- [Azure pour votre application d’entreprise](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure dans votre centre de données](https://azure.microsoft.com/overview/business-apps-on-azure/) 





