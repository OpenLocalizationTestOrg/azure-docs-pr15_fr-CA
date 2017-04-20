Cet article présente un ensemble de pratiques éprouvées pour l’exécution d’une machine virtuelle de Windows (VM) sur Azure, en accordant une attention à l’évolutivité, de disponibilité, de gérabilité et de sécurité. 

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources Azure] [ resource-manager-overview] et classique. Cet article utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

N’est pas recommandé à l’aide d’une machine virtuelle unique pour les charges de travail, car il n’existe aucun contrat de niveau du temps de service (SLA) pour les machines virtuelles d’unique sur Azure. Pour obtenir le contrat SLA, vous devez déployer plusieurs ordinateurs virtuels dans un [jeu de disponibilité][availability-set]. Pour plus d’informations, consultez [exécution de plusieurs ordinateurs virtuels de Windows Azure sur les][multi-vm]. 

## <a name="architecture-diagram"></a>Diagramme d’architecture

Mise en service d’un ordinateur virtuel dans Azure implique des pièces mobiles de plus que simplement la machine virtuelle elle-même. Il existe des éléments de calcul, de réseau et de stockage.

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « Compute - page de machine virtuelle unique.

![[0]][0]


- **Groupe de ressources.** Un [_groupe de ressources_] [ resource-manager-overview] est un conteneur qui conserve les ressources connexes. Créer un groupe de ressources contenant les ressources de cet ordinateur virtuel.

- **VM**. Vous pouvez configurer un ordinateur virtuel à partir d’une liste d’images publiées ou à partir d’un fichier de disque dur virtuel (VHD) que vous téléchargez dans le stockage blob Azure.

- **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel stocké dans [le stockage Azure][azure-storage]. Cela signifie qu’il persiste même si l’ordinateur hôte tombe en panne.

- **Disque temporaire.** La machine virtuelle est créée avec un disque temporaire (le `D:` lecteur sous Windows). Ce disque est stocké sur un lecteur physique sur la machine hôte. Il n’est _pas_ enregistré dans le stockage Azure et peut disparaître au cours des redémarrages et autres événements de cycle de vie de machine virtuelle. Utilisez ce disque uniquement pour les données temporaires, tels que les fichiers de page ou de remplacement.

- **Disques de données.** Un [disque de données] [ data-disk] est un disque dur virtuel permanent utilisé pour les données de l’application. Disques de données sont stockés dans le stockage Azure, tels que le disque du système d’exploitation.

- **Réseau virtuel (VNet) et le sous-réseau.** Chaque machine virtuelle dans Azure est déployé dans un VNet, qui est encore divisé en sous-réseaux.

- **Adresses IP publiques.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle&mdash;par exemple, via le Bureau à distance (RDP).

- **L’interface réseau (NIC)**. La carte réseau permet à l’ordinateur virtuel communiquer avec le réseau virtuel.

- **Groupe de sécurité réseau (NSG)**. Le [NSG] [ nsg] est utilisé pour autoriser ou refuser du trafic réseau vers le sous-réseau. Vous pouvez associer un NSG avec une carte d’interface réseau individuelle ou un sous-réseau. Si vous l’associez à un sous-réseau, les règles NSG s’appliquent à tous les ordinateurs virtuels de ce sous-réseau.
 
- **Tests de diagnostic.** L’enregistrement des diagnostics est essentiel pour la gestion et la résolution des problèmes liés à la machine virtuelle.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="vm-recommendations"></a>Recommandations de machine virtuelle

Nous vous recommandons de la série DS et GS car ces tailles de l’ordinateur prend en charge le [Stockage de prime][premium-storage]. Sélectionnez un des ces formats de machine à moins d’avoir une charge de travail spécialisé tels que l’informatique hautes performances. Pour plus d’informations, voir [formats de machine virtuelle][virtual-machine-sizes]. Lors du déplacement d’une charge de travail existante vers Azure, démarrez avec la taille de la mémoire virtuelle qui correspond le plus à vos serveurs sur site. Puis la mesure les performances de votre charge de travail réelle par rapport à l’unité centrale, mémoire et disque d’entrée/sortie d’opérations par seconde (IOPS) et la taille si nécessaire. Si vous avez besoin de plusieurs cartes réseau, sachez également de la limite de la carte d’interface réseau pour chaque taille.  

Lorsque vous configurez la machine virtuelle et autres ressources, vous devez spécifier un emplacement. En règle générale, choisissez un emplacement le plus proche de vos utilisateurs internes ou des clients. Toutefois, pas toutes les tailles de mémoire virtuelle peuvent être disponibles dans tous les emplacements. Pour plus d’informations, consultez [Services par région][services-by-region]. Pour répertorier les tailles de mémoire virtuelle disponibles dans un emplacement donné, exécutez la commande suivante de la Azure interface de ligne de commande (CLI) :

```
    azure vm sizes --location <location>
```

Pour plus d’informations sur le choix d’une image de machine virtuelle publiée, voir [Naviguer et sélectionnez machine virtuelle Azure images][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recommandations de disque et le stockage

Pour de meilleures performances d’e/s disque, nous vous recommandons de [Prime stockage][premium-storage], qui stocke les données sur les disques (SSD). Coût est basé sur la taille du disque mis en service. Ops ES/s et un débit également dépendant de taille de disque, ainsi, lorsque vous configurez un disque, prendre en compte trois facteurs (Ops ES/s, capacité et débit). 

Un compte de stockage peut prendre en charge les ordinateurs virtuels de 1 à 20.

Ajouter un ou plusieurs disques de données. Lorsque vous créez un nouveau disque dur virtuel, il n’est pas formatée. Vous connecter à la machine virtuelle pour formater le disque.

Si vous avez un grand nombre de disques de données, gardez à l’esprit des limites d’e/s total du compte de stockage. Pour plus d’informations, reportez-vous à la section [Limites de disque de Machine virtuelle][vm-disk-limits].

Pour optimiser les performances, créez un compte de stockage distinct pour stocker les journaux de diagnostic. Un compte standard de stockage redondants localement (LRS) est suffisant pour les journaux de diagnostic.

Dans la mesure du possible, installer des applications sur un disque de données, n’est pas le disque du système d’exploitation. Toutefois, certaines applications héritées peut-être d’installer des composants sur le lecteur C:. Dans ce cas, vous pouvez [redimensionner le disque du système d’exploitation] [ resize-os-disk] à l’aide de PowerShell.

### <a name="network-recommendations"></a>Recommandations de réseau

L’adresse IP publique peut être statique ou dynamique. La valeur par défaut est dynamique.

- Réserver une [adresse IP statique] [ static-ip] si vous avez besoin d’une adresse IP fixe qui ne changera pas &mdash; par exemple, si vous devez créer un enregistrement A dans DNS ou si avez besoin de l’adresse IP à la liste d’autorisation.

- Vous pouvez également créer un nom de domaine pleinement qualifié (FQDN) pour l’adresse IP. Vous pouvez alors inscrire un [enregistrement CNAME] [ cname-record] dans le système DNS qui pointe vers le nom de domaine complet. Pour plus d’informations, voir [Création d’un nom de domaine complet dans le portail Azure][fqdn].

Tous les NSGs contiennent un ensemble de [règles par défaut][nsg-default-rules], y compris une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peut pas être supprimés, mais les autres règles peuvent les substituer. Pour activer le trafic Internet, créer des règles qui autorisent le trafic entrant vers des ports spécifiques &mdash; , par exemple, le port 80 pour HTTP.  

Pour activer le protocole RDP, ajouter une règle NSG qui autorise le trafic entrant sur le port TCP 3389.

## <a name="scalability-considerations"></a>Évolutivité

Vous pouvez mettre à l’échelle une machine virtuelle vers le haut ou vers le bas en [modifiant la taille de la mémoire virtuelle][vm-resize]. 

Pour faire évoluer horizontalement, placez deux ou plus de machines virtuelles dans une disponibilité définie derrière un équilibreur de charge. Pour plus d’informations, consultez [exécution de plusieurs ordinateurs virtuels de Windows Azure sur les][multi-vm].

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Comme mentionné ci-dessus, il n’y a pas de SLA pour une machine virtuelle unique. Pour obtenir le contrat SLA, vous devez déployer plusieurs ordinateurs virtuels en un ensemble de disponibilité.

Votre ordinateur virtuel peut-être être affecté par [l’opération de maintenance planifiée] [ planned-maintenance] [maintenance planifiée]ou[manage-vm-availability]. Vous pouvez utiliser la [machine virtuelle redémarrer journaux] [ reboot-logs] pour déterminer si un redémarrage de l’ordinateur virtuel a été causé par la maintenance planifiée.

Disques durs virtuels sont soutenues par [Le stockage Azure][azure-storage], qui est répliqué de durabilité et de disponibilité.

Afin de protéger contre la perte accidentelle de données en fonctionnement normal (par exemple, en raison d’une erreur de l’utilisateur), vous devez également implémenter des sauvegardes de point-à-temps, à l’aide [d’instantanés de blob] [ blob-snapshot] ou un autre outil.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

**Groupes de ressources.** Ressources fortement couplés qui partagent le même cycle de vie dans un même [groupe de ressources][resource-manager-overview]. Groupes de ressources permettent de déployer et surveiller les ressources en tant que groupe et revenir à la version des coûts par groupe de ressources de facturation. Vous pouvez également supprimer des ressources sous la forme d’un jeu, ce qui est très utile pour les déploiements de test. Fournir les ressources des noms explicites. Qui le rend plus facile de localiser une ressource spécifique et de comprendre son rôle. Consultez [les Conventions d’appellation recommandées pour les ressources Azure][naming conventions].

**Diagnostic de la machine virtuelle.** Activer de surveillance et de diagnostic, y compris les mesures de santé de base, les journaux d’infrastructure de diagnostics et [Démarrage diagnostic][boot-diagnostics]. Diagnostics de démarrage peut vous aider à diagnostiquer un échec de démarrage si votre ordinateur virtuel est dans un état non-amorçable. Pour plus d’informations, voir [Activer les tests de diagnostic et de surveillance][enable-monitoring]. Utilisez la [Collection des journaux Azure] [ log-collector] extension pour collecter les journaux de la plateforme Azure et les télécharger vers le stockage Azure.   

La commande CLI suivante permet des tests de diagnostic :

```
    azure vm enable-diag <resource-group> <vm-name>
```

**L’arrêt d’une machine virtuelle.** Azure établit une distinction entre les États « Arrêté » et « Deallocated ». Vous sont facturés lorsque l’état de la machine virtuelle est « arrêté ». Vous ne sont pas facturés lorsque la machine virtuelle est libéré.

Pour désallouer un ordinateur virtuel, utilisez la commande CLI suivante :

```
    azure vm deallocate <resource-group> <vm-name>
```

Le bouton **Arrêter** dans le portail Azure libère également l’ordinateur virtuel. Toutefois, si vous arrêtez le système d’exploitation par le biais de connecté, la machine virtuelle est arrêtée mais _pas_ désalloués, donc toujours être débités.

**Suppression d’un ordinateur virtuel.** Si vous supprimez un ordinateur virtuel, les disques durs virtuels ne sont pas supprimés. Cela signifie que vous pouvez supprimer en toute sécurité de la machine virtuelle sans perte de données. Toutefois, vous devrez toujours payer pour le stockage. Pour supprimer du disque dur virtuel, supprimez le fichier de [stockage des objets blob][blob-storage].

Pour éviter une suppression accidentelle, utilisez un [verrou de ressource] de[ resource-lock] pour verrouiller les ressource entière groupe ou verrouillage des ressources individuelles comme la machine virtuelle. 

## <a name="security-considerations"></a>Considérations sur la sécurité

Utiliser le [Centre de sécurité Azure] [ security-center] pour obtenir une vue centralisée de l’état de la sécurité de vos ressources d’Azure. Centre de sécurité surveille les problèmes de sécurité potentiels tels que le système met à jour, contre les logiciels malveillants et fournit une image complète de l’état de sécurité de votre déploiement. 

- Centre de sécurité est configuré par abonnement Azure. Activer la collecte de données de sécurité comme décrit dans [Utilisation du centre de sécurité].

- Lors de la collecte de données est activée, le centre de sécurité analyse automatiquement les ordinateurs virtuels créés sous cet abonnement.

**Gestion des correctifs.** Si activée, le centre de sécurité vérifie si des mises à jour critiques et sécurité sont manquants. Utiliser les [paramètres de stratégie de groupe] [ group-policy] sur l’ordinateur virtuel pour permettre les mises à jour système automatique.

**Contre les logiciels malveillants.** Si activée, le centre de sécurité vérifie si des logiciels anti-programmes malveillants est installé. Centre de sécurité vous permet également d’installer des logiciels contre les logiciels malveillants à l’intérieur du portail Azure.

Utilisez le [contrôle d’accès basé sur les rôles] [ rbac] (RBAC) pour contrôler l’accès aux ressources Azure que vous déployez. RBAC permet d’assigner des rôles d’autorisations aux membres de votre équipe DevOps. Par exemple, le rôle de lecteur peut afficher les ressources Azure mais pas créer, gérer ou les supprimer. Certains rôles sont spécifiques aux types de ressources Azure particulier. Par exemple, le rôle de collaborateur de la Machine virtuelle peut redémarrer ou libérer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, etc.. Autres [les rôles RBAC intégrés] [ rbac-roles] qui peut être utile pour cette architecture de référence incluent des [Utilisateurs de laboratoires DevTest] [ rbac-devtest] et [Collaborateur du réseau][rbac-network]. Un utilisateur peut être affecté à plusieurs rôles, et vous pouvez créer des rôles personnalisés pour d’autres autorisations affinées.

> [AZURE.NOTE] RBAC ne limite pas les actions qu’un utilisateur connecté à un ordinateur virtuel peut exécuter. Ces autorisations sont déterminées par le type de compte sur le système d’exploitation invité.   

Pour réinitialiser le mot de passe d’administrateur local, exécutez le `vm reset-access` commande CLI d’Azure.

```
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
```

Utiliser les [journaux d’audit] [ audit-logs] pour voir la mise en service d’actions et autres événements de la machine virtuelle.

Envisagez de [Cryptage de disque Azure] [ disk-encryption] si vous avez besoin crypter les disques du système d’exploitation et des données. 

## <a name="solution-deployment"></a>Déploiement de la solution

Un [déploiement] [ github-folder] pour obtenir une architecture qui illustre ces meilleures pratiques est disponible. Cette architecture de référence comprend un réseau virtuel (VNet), le groupe de sécurité réseau (NSG) et une seule machine virtuelle (VM).

Il existe plusieurs manières de déployer cette architecture de référence. Le moyen le plus simple est de suivre les étapes suivantes : 

1. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre ».  
[![Déployer vers Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Une fois le lien ouvert dans Azure portal, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-single-vm-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Sélectionnez le **Type de système d’exploitation** à partir de la zone de liste déroulante, **windows**.
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

4. Les fichiers de paramètres incluent un nom d’utilisateur codé en dur l’administrateur et le mot de passe, et il est fortement recommandé que vous modifiez immédiatement à la fois. Cliquez sur l’ordinateur virtuel nommé `ra-single-vm0 `dans le portail Azure. Ensuite, cliquez sur **Réinitialiser le mot de passe** dans la lame **de prise en charge + de résolution des problèmes** . Sélectionnez **Réinitialiser le mot de passe** dans la zone de liste déroulante **en Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et un **mot de passe**. Cliquez sur le bouton de **mise à jour** pour rendre persistant le nouveau nom d’utilisateur et le mot de passe.

Pour plus d’informations sur les autres façons de déployer cette architecture de référence, consultez le fichier readme dans le [Guide-unique-vm][github-folder]] Github dossier. 

## <a name="customize-the-deployment"></a>Personnaliser le déploiement

Si vous devez modifier le déploiement afin de répondre à vos besoins, suivez les instructions dans le [fichier readme][github-folder]. 

## <a name="next-steps"></a>Étapes suivantes

Dans la commande pour le [contrat SLA pour les Machines virtuelles] [ vm-sla] pour l’appliquer, vous devez déployer deux instances ou plus dans un jeu de disponibilité. Pour plus d’informations, consultez [exécution de plusieurs ordinateurs virtuels sur Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm
[group-policy]: https://technet.microsoft.com/en-us/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/en-us/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[security-center]: https://azure.microsoft.com/en-us/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Utilisez le centre de sécurité]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architecture de mémoire virtuelle de Windows unique dans Azure"
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[blocks]: https://github.com/mspnp/template-building-blocks
