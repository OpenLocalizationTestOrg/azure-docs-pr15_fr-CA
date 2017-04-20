<properties
   pageTitle="Exécution de plusieurs ordinateurs virtuels | Architecture de référence | Microsoft Azure"
   description="L’exécution de plusieurs instances de machine virtuelle sur Azure pour l’évolutivité, de résilience, de gérabilité et de sécurité."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Exécution de plusieurs ordinateurs virtuels sur Azure d’évolutivité et de disponibilité 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article présente un ensemble de pratiques éprouvées pour l’exécution de plusieurs instances de machines virtuelles (VM) pour améliorer l’évolutivité, de disponibilité, de gérabilité et de sécurité.   

Dans cette architecture, la charge de travail est réparti entre les instances de la machine virtuelle. Il existe une seule adresse IP publique et le trafic Internet est distribué aux ordinateurs virtuels à l’aide d’un équilibreur de charge. Cette architecture peut être utilisée pour une application à un niveau, tel qu’un cluster de stockage ou application web sans état. Il est également un bloc de construction pour les applications multicouches. 

Cet article est basé sur [l’exécution d’une machine virtuelle unique sur Azure][single vm]. Les recommandations de cet article s’appliquent également à cette architecture.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Ordinateurs virtuels dans Azure nécessitent la prise en charge des ressources du réseau et de stockage.

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur le « Compute - onglet de machine virtuelle multi. » 

![[0]][0]

L’architecture comprend les éléments suivants : 

- **Ensemble de disponibilité.** La [valeur de disponibilité] [ availability set] contient les ordinateurs virtuels et est nécessaire pour la prise en charge de la [disponibilité des SLA pour les machines virtuelles d’Azure][vm-sla].

- **VNet**. Chaque machine virtuelle dans Azure est déployé dans un réseau virtuel (VNet) qui est encore divisé en **sous-réseaux**.

- **Équilibrage de la charge d’Azure.** L' [équilibrage de la charge] distribue les demandes entrantes d’Internet vers les instances de la machine virtuelle dans un jeu de disponibilité. L’équilibreur de charge inclut certaines ressources connexes :

  - **Adresses IP publiques.** Une adresse IP publique est nécessaire pour l’équilibreur de charge recevoir le trafic Internet.

  - **Configuration frontale.** Associe l’adresse IP publique de l’équilibreur de charge.

  - **Pool d’adresse de back-end.** Contient les interfaces réseau (cartes réseau) pour les ordinateurs virtuels qui recevront le trafic entrant.

- **Charger les règles d’équilibrage.** Permet de répartir le trafic réseau sur tous les ordinateurs virtuels dans le pool d’adresses de back-end. 

- **Règles NAT.** Utilisé pour acheminer le trafic vers un ordinateur virtuel spécifique. Par exemple, pour activer le protocole remote desktop protocol (RDP) pour les ordinateurs virtuels, créer une règle de traduction d’adresses réseau distinct pour chaque ordinateur virtuel. 

- **Interfaces réseau (cartes réseau)**. Chaque machine virtuelle dispose d’une carte réseau pour se connecter au réseau.

- **Stockage.** Comptes de stockage contiennent les images de machine virtuelle et d’autres ressources liées aux fichiers, telles que les données de diagnostic de machine virtuelle capturées par Azure.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit les recommandations décrites ci-dessous. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations, à moins que vous n’ayez un besoin spécifique une recommandation ne prenant pas en charge. 

### <a name="availability-set-recommendations"></a>Définie des recommandations de disponibilité

Vous devez créer au moins deux VM dans la disponibilité, la valeur prise en charge de la [disponibilité des SLA pour les machines virtuelles d’Azure][vm-sla]. Notez que l’équilibreur de charge Azure requiert également qu’équilibrage de machines virtuelles appartiennent au même jeu de disponibilité.

### <a name="network-recommendations"></a>Recommandations de réseau

Tous les ordinateurs virtuels situé derrière l’équilibreur de charge doivent être placés dans le même sous-réseau. N’exposez pas les ordinateurs virtuels directement à Internet, mais plutôt attribuer à chaque machine virtuelle une adresse IP privée. Les clients se connectent à l’aide de l’adresse IP publique de l’équilibreur de charge.

### <a name="load-balancer-recommendations"></a>Recommandations d’équilibrage de la charge

Ajouter tous les ordinateurs virtuels dans la disponibilité définie pour le pool d’adresses de back-end de l’équilibreur de charge.

Définir les règles d’équilibrage de charge pour diriger le trafic de réseau pour les ordinateurs virtuels. Par exemple, pour activer le trafic HTTP, vous devez créer une règle qui mappe le port 80 à partir de la configuration frontale au port 80 sur le pool d’adresses de back-end. Lorsque l’équilibrage de charge reçoit une demande sur le port 80 de l’adresse IP publique, il achemine la demande vers le port 80 sur l’une des cartes réseau dans le pool d’adresses de back-end.

Définir des règles NAT pour acheminer le trafic vers un ordinateur virtuel spécifique. Par exemple, pour activer RDP pour les ordinateurs virtuels créer une règle NAT séparée pour chaque ordinateur virtuel. Chaque règle doit être mappé à un numéro de port distinct sur le port 3389, le port par défaut pour le protocole RDP. (Par exemple, utilisez le port 50001 pour « VM1, « port 50002 pour « Ordinateur virtuel 2, » et ainsi de suite). Affecter les règles NAT pour les cartes réseau sur les ordinateurs virtuels. 

### <a name="storage-account-recommendations"></a>Recommandations de compte de stockage

Créer des comptes de stockage Azure distinct pour chaque ordinateur virtuel stocker les disques durs virtuels (VHD), afin d’éviter de passer par les opérations d’entrées/sorties par seconde [(IOPS) limites] [ vm-disk-limits] pour les comptes de stockage. 

Créer un compte de stockage pour les journaux de diagnostic. Ce compte de stockage peut être partagé par tous les ordinateurs virtuels.

## <a name="scalability-considerations"></a>Évolutivité

Il existe deux options pour la mise à l’échelle des ordinateurs virtuels dans Azure : 

- Un équilibreur de charge permet de distribuer le trafic réseau sur un ensemble de machines virtuelles. Pour évoluer, de configurer des ordinateurs virtuels supplémentaires et de les placer derrière l’équilibreur de charge. 

- Utiliser des [jeux d’échelle de Machine virtuelle][vmss]. Un ensemble d’échelle contient un nombre spécifié de VMs identiques derrière un équilibreur de charge. Échelle de la machine virtuelle définit autoscaling prise en charge basée sur des mesures de performances. L’augmentation de la charge sur les ordinateurs virtuels, machines virtuelles supplémentaires sont automatiquement ajoutés à l’équilibreur de charge. 

Les sections suivantes comparent ces deux options.

### <a name="load-balancer-without-vm-scale-sets"></a>Équilibreur de charge sans jeux d’échelle de machine virtuelle

Un équilibreur de charge prend les demandes entrantes du réseau et les distribue vers les cartes réseau dans le pool d’adresses de back-end. Pour mettre à l’échelle horizontalement, ajouter d’autres instances de la machine virtuelle à l’ensemble de la disponibilité (ou libérer des machines virtuelles à l’échelle vers le bas). 

Par exemple, supposons que vous exécutez un serveur web. Vous devez ajouter une règle d’équilibrage de la charge pour le port 80 et/ou le port 443 (pour SSL). Lorsqu’un client envoie une demande HTTP, l’équilibreur de charge sélectionne une adresse IP de back-end à l’aide d’un [algorithme de hachage] [ load balancer hashing] qui inclut l’adresse IP source. De cette façon, les demandes des clients sont réparties entre tous les ordinateurs virtuels. 

> [AZURE.TIP] Lorsque vous ajoutez un nouvel ordinateur virtuel pour une disponibilité définie, assurez-vous de créer une carte d’interface réseau de la machine virtuelle et ajoutez la carte d’interface réseau au pool d’adresses de back-end de l’équilibreur de charge. Dans le cas contraire, le trafic Internet ne sont pas routé vers la nouvelle machine virtuelle.

Chaque abonnement Azure a des limites par défaut en place, y compris un nombre maximum de machines virtuelles par région. Vous pouvez augmenter la limite en déposant une demande de support. Pour plus d’informations, reportez-vous à la section [abonnement Azure et les limites de service, les quotas et les contraintes][subscription-limits].  

### <a name="vm-scale-sets"></a>Jeux d’échelle de machine virtuelle 

Jeux d’échelle de machine virtuelle vous aident à déployer et à gérer un ensemble de machines virtuelles identiques. Avec tous les ordinateurs virtuels configurés de la même, prend en charge les jeux d’échelle VM autoscale true, sans avant mise en service des machines virtuelles, rendant ainsi plus facile de créer des services à grande échelle ciblant grand compute, données volumineuses et des charges de travail. 

Pour plus d’informations sur les jeux d’échelle de machine virtuelle, voir [Vue d’ensemble de Machine virtuelle échelle définit][vmss].

Considérations sur l’utilisation des jeux d’échelle de machine virtuelle :

- Tenez compte des jeux d’échelle si vous devez rapidement mettre à l’échelle des ordinateurs virtuels, ou à l’échelle automatique. 

- Actuellement, jeux d’échelle ne supportent pas les disques de données. Les options de stockage des données sont le stockage de fichiers d’Azure, le lecteur du système d’exploitation, le lecteur Temp ou un magasin externe, tels que le stockage Azure. 

- Toutes les instances de machine virtuelle dans une échelle définie automatiquement appartiennent au même jeu de disponibilité, avec les domaines de panne 5 et 5 mise à jour.

- Par défaut, les jeux d’échelle utilisent « overprovisioning », qui signifie que le jeu d’échelle initialement dispositions davantage d’ordinateurs virtuels que vous le demandera, puis supprime les ordinateurs virtuels supplémentaires. Cela permet d’améliorer le taux de réussite globale lors de la mise en service des ordinateurs virtuels. 

- Nous vous recommandons de ne plus puis à 20 VM par stockage compte avec overprovisioning VM activé ou pas plus de 40 avec overprovisioning désactivé.  

- Vous pouvez trouver des modèles de gestionnaire de ressources pour le déploiement d’échelle se définit dans les [Modèles de Quickstart Azure][vmss-quickstart].

- Il existe deux façons de configurer des ordinateurs virtuels déployés dans un ensemble d’échelle : créer une image personnalisée, ou utiliser des extensions pour configurer l’ordinateur virtuel après qu’il est mis en service.

    - Un ensemble d’échelle basé sur une image personnalisée doit créer tous les disques virtuels de système d’exploitation au sein d’un seul compte de stockage. 

    - Avec des images personnalisées, vous devez l’image mise à jour.

    - Avec les extensions, il peut prendre plus de temps pour une VM qui vient d’être mis en service pour commencer à tourner.

Pour des considérations supplémentaires, voir [Conception de machine virtuelle échelle jeux pour échelle][vmss-design].

> [AZURE.TIP]  Lorsque vous utilisez une solution à l’échelle automatique, la tester avec des charges de travail de niveau de production à l’avance. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

La disponibilité ensemble rend votre application plus résistants aux planifiés et événements de maintenance.

- _Maintenance de planifié_ se produit lorsque Microsoft met à jour la plate-forme sous-jacente, ce qui provoque parfois des ordinateurs virtuels être redémarré. Azure permet que les ordinateurs virtuels dans un jeu de disponibilité ne sont pas tous redémarré en même temps, un au moins est conservé pendant que d’autres sont le redémarrage en cours d’exécution.

- _Maintenance planifiée_ se produit s’il existe une défaillance matérielle. Azure permet de s’assurer que les ordinateurs virtuels dans un jeu de disponibilité sont mis en service sur plus d’un rack de serveurs. Cela permet de réduire l’impact des défaillances matérielles, panne réseau, d’alimentation des interruptions et ainsi de suite.

Pour plus d’informations, voir [Gérer la disponibilité des machines virtuelles][availability set]. La vidéo suivante a également un bon aperçu de la disponibilité des jeux : [Comment configurer un ensemble de disponibilité aux ordinateurs virtuels d’échelle][availability set ch9]. 

> [AZURE.WARNING]  Assurez-vous de configurer la disponibilité de jeu lorsque vous configurez la machine virtuelle. Actuellement, il n’existe aucun moyen d’ajouter un gestionnaire de ressources de VM pour une disponibilité définie après que la machine virtuelle est mis en service.

L’équilibreur de charge utilise des [sondes de santé] pour surveiller la disponibilité des instances de la machine virtuelle. Si une sonde ne peut pas atteindre une instance dans un délai d’expiration, l’équilibreur de charge cesse d’envoyer du trafic de cette machine virtuelle. Cependant, l’équilibreur de charge continuera à détecter, et si la machine virtuelle est de nouveau disponible, l’équilibreur de charge reprend la transmission du trafic à cette machine virtuelle.

Voici quelques recommandations sur les sondes de santé d’équilibrage de la charge :

- Sondes peuvent tester TCP ou HTTP. Si l’exécution de machines virtuelles un serveur HTTP (IIS, nginx, Node.js app, etc.), créez une sonde HTTP. Sinon, créez une sonde TCP.

- Pour une sonde HTTP, spécifiez le chemin d’accès au point de terminaison HTTP. La sonde vérifie une réponse HTTP 200 à partir de ce chemin d’accès. Cela peut être le chemin d’accès racine (« / ») ou un point de terminaison de surveillance qui implémente une logique personnalisée pour vérifier l’état de l’application. Le point de terminaison doit autoriser les demandes HTTP anonymes.

- La sonde est envoyée à partir d’un [connu] [ health-probe-ip] adresse IP, 168.63.129.16. Veillez à que ne pas bloquer le trafic vers ou à partir de cette adresse IP dans les stratégies de pare-feu ou les règles de groupe (NSG) de sécurité de réseau.

- Utiliser les [journaux de sonde de santé] [ health probe log] pour afficher l’état des sondes de santé. Activer l’enregistrement dans le portail Azure pour chaque équilibreur de charge. Dans les journaux pour le stockage des objets Blob Azure. Les journaux indiquent le nombre de machines virtuelles sur le back-end ne reçoivent pas le trafic du réseau en raison de réponses de sonde a échoué.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Avec plusieurs ordinateurs virtuels, il est important d’automatiser les processus, afin qu’elles soient fiables et reproductibles. Vous pouvez utiliser [Automation d’Azure] [ azure-automation] pour automatiser le déploiement, l’application de correctifs du système d’exploitation et d’autres tâches. Automation Azure est un service d’automatisation qui s’exécute sur Azure et est basé sur Windows PowerShell. Exemples de scripts automation sont disponibles à partir de la [Galerie de procédure opérationnelle] sur TechNet.

## <a name="security-considerations"></a>Considérations sur la sécurité

Les réseaux virtuels sont une limite d’isolement de trafic dans Azure. Ordinateurs virtuels dans un que vnet ne peut pas communiquer directement avec des ordinateurs virtuels dans un VNet différent. Ordinateurs virtuels dans le même VNet peuvent communiquer, sauf si vous créez des [groupes de sécurité réseau] [ nsg] (NSGs) pour limiter le trafic. Pour plus d’informations, consultez [services de nuage de Microsoft et de la sécurité du réseau][network-security].

Pour le trafic Internet entrant, les règles d’équilibrage de charge définissent le trafic que peut atteindre le back-end. Toutefois, les règles d’équilibrage de la charge ne prend en charge Création de listes autorisées IP, donc si vous souhaitez des adresses certaine IP publiques liste d’autorisation, d’ajouter un NSG au sous-réseau.

## <a name="solution-deployment"></a>Déploiement de la solution

Un déploiement d’une architecture de référence qui implémente ces recommandations est disponible sur GitHub. Cette architecture de référence comprend un réseau virtuel (VNet), le groupe de sécurité réseau (NSG), équilibrage de la charge et deux machines virtuelles (VM).

L’architecture de référence peut être déployée avec Windows ou Linux VM en suivant les instructions ci-dessous : 

1. Cliquez sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Une fois le lien ouvert dans Azure portal, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres, donc **Utiliser existant** et tapez `ra-multi-vm-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Sélectionnez le **Type de système d’exploitation** dans la liste déroulante zone, **windows** ou **linux**. 
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

4. Les fichiers de paramètres incluent un nom d’utilisateur codé en dur l’administrateur et le mot de passe, et il est fortement recommandé que vous modifiez immédiatement à la fois. Cliquez sur l’ordinateur virtuel nommé `ra-multi-vm1` dans le portail Azure. Ensuite, cliquez sur **Réinitialiser le mot de passe** dans la lame **de prise en charge + de résolution des problèmes** . Sélectionnez **Réinitialiser le mot de passe** dans la zone de liste déroulante **en Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et un **mot de passe**. Cliquez sur le bouton de **mise à jour** pour enregistrer le nouveau nom d’utilisateur et le mot de passe. Répétez ces étapes pour la machine virtuelle nommée `ra-multi-vm2`.

Pour plus d’informations sur les autres façons de déployer cette architecture de référence, consultez le fichier readme dans le [Guide-unique-vm] [ github-folder] GitHub dossier. 

## <a name="next-steps"></a>Étapes suivantes

Placer plusieurs VM derrière un équilibreur de charge est un bloc de construction pour la création des architectures multicouches. Pour plus d’informations, consultez [Exécution des machines virtuelles Windows d’une architecture à N niveaux sur Azure] [ n-tier-windows] et [Exécuter des ordinateurs virtuels Linux pour une architecture à N niveaux sur Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[sondes de santé]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[l’équilibreur de charge]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Galerie de procédure opérationnelle]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Architecture d’une solution multi-VM sur Azure comprenant une disponibilité avec deux VM et un équilibreur de charge"
