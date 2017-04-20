<properties
    pageTitle="Basculement de la restauration du Site | Microsoft Azure" 
    description="Récupération de Site Azure coordonne la réplication, le basculement et la restauration des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement vers Azure ou un centre de données secondaire." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="failover-in-site-recovery"></a>Basculement de la récupération de Site

Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des informations et des instructions de récupération (impossibilité de plus et à défaut précédent) ordinateurs virtuels et les serveurs physiques qui sont protégés par la restauration du Site. 

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="types-of-failover"></a>Types de basculement

Une fois la protection est activée pour les machines virtuelles et des serveurs physiques et qu’ils sont la réplication, vous pouvez exécuter basculements en cas de besoin. Récupération de site prend en charge un certain nombre de types de basculement.

**Basculement sur incident** | **Moment de l’exécution** | **Détails** | **Processus**
---|---|---|---
**Basculement de test** | Exécuté pour valider votre stratégie de réplication ou d’effectuer une extraction de récupération après sinistre | Aucune perte de données ou les interruptions de service.<br/><br/>Aucun impact sur la réplication<br/><br/>Aucun impact sur votre environnement de production | Démarrer le basculement<br/><br/>Spécifiez comment les machines de test seront connectés aux réseaux après basculement<br/><br/>Suivre la progression de l’onglet **tâches** . Machines de test sont créés et le démarrer dans l’emplacement secondaire<br/><br/>Azure - se connecter à l’ordinateur dans le portail Azure<br/><br/>Secondaire site - d’accéder à l’ordinateur sur le même hôte et nuage<br/><br/>Test terminé et nettoie automatiquement les paramètres de basculement test.
**Basculement planifié** | Exécuter pour répondre aux exigences de conformité<br/><br/>Exécution de la maintenance planifiée<br/><br/>Exécuter pour basculer les données à conserver des charges de travail pour les arrêts connus - comme une coupure de courant attendu ou grave météo<br/><br/>Exécuter à la restauration après basculement sur incident du primaire au secondaire | Aucune perte de données<br/><br/>Il y a temps d’arrêt pendant le temps que nécessaire pour arrêter l’ordinateur virtuel sur le serveur primaire et l’afficher sur le site secondaire.<br/><br/>Les machines virtuelles sont impact que les ordinateurs cible devient machines d’origine après le basculement. | Démarrer le basculement<br/><br/>Suivre la progression de l’onglet **tâches** . Machines d’origine ne sont pas arrêtés<br/><br/>Ordinateurs de réplica démarrent à l’emplacement secondaire<br/><br/>Azure - se connecter à la machine de réplica dans le portail Azure<br/><br/>Secondaire site - d’accéder à l’ordinateur sur le même hôte et dans le même nuage<br/><br/>Valider le basculement
**Basculement non planifié** | Exécuter ce type de basculement de façon réactive lorsqu’un site principal est inaccessible en raison d’un incident inattendu, comme une attaque de virus ou de panne d’alimentation <br/><br/> Vous pouvez exécuter une commande planifiée basculement peut survenir même si le site principal n’est pas disponible. | Perte de données dépend des paramètres de fréquence de réplication<br/><br/>Les données seront actualisées selon la dernière synchronisation | Démarrer le basculement<br/><br/>Suivre la progression de l’onglet **tâches** . Essayez éventuellement d’arrêt des machines virtuelles et de synchroniser les données les plus récentes<br/><br/>Ordinateurs de réplica démarrent à l’emplacement secondaire<br/><br/>Azure - se connecter à la machine de réplica dans le portail Azure<br/><br/>Accéder à l’ordinateur sur le même hôte et dans le même nuage site secondaire<br/><br/>Valider le basculement


Les types de basculement qui est prises en charge dépendent de votre scénario de déploiement.

**Direction de basculement** | **Basculement de test** | **Basculement planifié** | **Basculement non planifié**
---|---|---|---
Site principal de VMM au site de VMM secondaire | Prise en charge | Prise en charge | Prise en charge 
Site secondaire de VMM au site de VMM principal | Prise en charge | Prise en charge | Prise en charge
Nuage à nuage (seul serveur VMM) |  Prise en charge | Prise en charge | Prise en charge
Site VMM vers Azure | Prise en charge | Prise en charge | Prise en charge 
Azure au site VMM | Non pris en charge | Prise en charge | Non pris en charge 
Site de Hyper-V vers Azure | Prise en charge | Prise en charge | Prise en charge
Azure au site de Hyper-V | Non pris en charge | Prise en charge | Non pris en charge
Site de VMware pour Azure | Prise en charge de (scénario amélioré)<br/><br/> Non pris en charge (scénario hérité) |Non pris en charge | Prise en charge
Serveur physique vers Azure | Prise en charge de (scénario amélioré)<br/><br/> Non pris en charge (scénario hérité) | Non pris en charge | Prise en charge

## <a name="failover-and-failback"></a>Basculement et restauration automatique

Basculement des machines virtuelles sur un site secondaire sur site ou Azure, en fonction de votre déploiement. Un ordinateur qui bascule vers Azure est créé comme une machine virtuelle Azure. Vous pouvez basculer vers une seule machine virtuelle ou serveur physique ou un plan de récupération. Les plans de récupération se compose d’un ou plusieurs classés les groupes qui contiennent des machines virtuelles protégées ou serveurs physiques. Ils sont utilisés pour harmoniser le basculement de plusieurs machines qui basculent en fonction du groupe, ils se trouvent dans. [Pour en savoir plus](site-recovery-create-recovery-plans.md) sur les plans de reprise. 

Après l’exécution de basculement et de vos ordinateurs sont en cours d’exécution sur un site secondaire Notez que :

- Si vous n’avez pas sur Azure, une fois que les ordinateurs de basculement ne sont pas protégés ou de la réplication dans l’emplacement principal ou secondaire. Dans Azure les ordinateurs virtuels sont stockés dans le stockage de réplication géographique qui fournit la résilience, mais pas la réplication.
- Si vous avez effectué un basculement non planifié pour un site secondaire, une fois que les ordinateurs de basculement dans l’emplacement secondaire ne sont pas protégés ou de réplication.
- Si vous avez effectué un basculement planifié vers un site secondaire, une fois que les ordinateurs de basculement dans l’emplacement secondaire sont protégés.
 

### <a name="failback-from-secondary-site"></a>Retour arrière depuis le site secondaire

Restauration à partir d’un site secondaire à un principal utilise le même processus que le basculement du site principal sur le site secondaire. Après que le basculement du centre de données secondaire est validée et terminée, vous pouvez lancer la réplication inverse lorsque votre site principal devient disponible. Inverse réplication lance la réplication entre le site secondaire et principal en synchronisant les données du delta. Réplication inverse amène les ordinateurs virtuels dans un état de protection, mais le centre de données secondaire est toujours à l’emplacement actif. Afin de rendre le site principal dans l’emplacement actif vous initiez un basculement planifié à partir de secondaire vers le primaire, suivie d’une autre réplication inverse.

### <a name="failback-from-azure"></a>Restauration à partir d’Azure

Si vous avez basculé vers Azure vos machines virtuelles sont protégés par les fonctionnalités de résilience Azure pour les machines virtuelles. Pour rendre le site principal initial dans l’emplacement actif vous exécuter un basculement planifié. Si votre site d’origine n’est pas disponible, vous pouvez échouer et l’emplacement d’origine ou vers un autre emplacement. Pour démarrer la réplication après la restauration à l’emplacement principal vous lancez une réplication inverse.

### <a name="failover-considerations"></a>Considérations de basculement

- **Adresse IP après le basculement**, par défaut un échec sur ordinateur aura une adresse IP autre que l’ordinateur source. Si vous souhaitez conserver le même voir d’adresse IP : 
    - **Site secondaire**: Si vous êtes basculement vers un site secondaire et que vous souhaitez conserver une adresse IP [de lire](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) cet article. Notez que vous pouvez conserver une adresse IP publique si votre fournisseur de services Internet prend en charge.
    - **Azure**: Si vous êtes basculent vers Azure, vous pouvez spécifier l’adresse IP que vous souhaitez attribuer dans l’onglet **configurer** les propriétés de la machine virtuelle. Vous ne peut pas conserver une adresse IP publique après basculement sur Azure. Vous pouvez conserver les espaces d’adressage non - RFC 1918 sont utilisées comme adresses internes.
- **Basculement partiel**, si vous souhaitez basculer à partie d’un site plutôt que tout un site Notez que : 
    - **Site secondaire**: Si vous ne parvenez pas sur une partie d’un site principal vers un site secondaire et vous souhaitez vous connecter sur le site principal, utilisez une connexion VPN de site à site à l’échec de la connexion sur des applications sur le site secondaire pour les composants de l’infrastructure en cours d’exécution sur le site principal. Si un sous-réseau entier bascule vous pouvez de conserver l’adresseIP de l’ordinateur virtuel. Si vous faites basculer un sous-réseau partiel vous ne peut pas conserver l’adresseIP de l’ordinateur virtuel car les sous-réseaux ne peut pas être fractionnées entre les sites.
    - **Azure**: Si vous basculer sur un site partiel vers Azure et que vous voulez vous connecter sur le site principal, vous pouvez utiliser un réseau VPN de site à site pour connecter un échec sur une application dans Azure pour les composants de l’infrastructure en cours d’exécution sur le site principal. Notez que si le sous-réseau entier échoue sur vous pouvez conserver l’adresseIP de l’ordinateur virtuel. Si vous faites basculer un sous-réseau partiel vous ne peut pas conserver l’adresseIP de l’ordinateur virtuel car les sous-réseaux ne peut pas être fractionnées entre les sites.
 
- **Lettre de lecteur**, si vous souhaitez conserver la lettre de lecteur sur les machines virtuelles après le basculement, vous pouvez définir la stratégie SAN pour la machine virtuelle pour **sur**. Disques de l’ordinateur virtuel en ligne automatiquement. [Pour en savoir plus](https://technet.microsoft.com/library/gg252636.aspx).
- **Acheminer les demandes client**, récupération de Site fonctionne avec Azure Traffic Manager pour acheminer les demandes client à votre application après le basculement.




## <a name="run-a-test-failover"></a>Exécutez un test de basculement

Lorsque vous exécutez un test de basculement vous demandera de sélectionner des paramètres réseau pour les machines de réplica de test. Vous avez un certain nombre d’options.  

**Option de basculement de test** | **Description** | **Vérification du basculement sur incident** | **Détails**
---|---|---|---
**Basculer vers Azure — sans réseau** | Ne sélectionnez pas une cible de réseau Azure | Les contrôles de basculement qui permettent de tester l’ordinateur virtuel démarre comme prévu dans Azure | Tous les ordinateurs virtuels de test dans un plan de récupération sont ajoutés dans un service cloud unique et peuvent se connecter à l’autre<br/><br/>Machines ne sont pas connectés à un réseau Azure après le basculement.<br/><br/>Les utilisateurs peuvent se connecter aux machines de test avec une adresse IP publique
**Basculer vers Azure — avec le réseau** | Sélectionnez une cible de réseau Azure | Basculement vérifie que les ordinateurs de test sont connectés au réseau | Créer un réseau Azure qui a isolé de votre réseau de production Azure et configurez l’infrastructure de la machine virtuelle répliquée à fonctionner comme prévu.<br/><br/>Le sous-réseau de l’ordinateur virtuel de test est basé sur le sous-réseau sur lequel l’a échoué sur une machine virtuelle est prévu pour se connecter à des cas de basculement planifié ou non.
**Basculement vers un site secondaire de VMM — sans réseau** | Ne sélectionnez pas un réseau de machine virtuelle | Basculement vérifie que les ordinateurs de test sont créés.<br/><br/>La machine virtuelle de test sera créée sur le même hôte que l’hôte sur lequel la machine virtuelle de réplica existe. Il n’est pas ajouté au nuage dans lequel se trouve l’ordinateur virtuel de réplica. | <p>L’échec sur l’ordinateur n’est pas connecté à un réseau.<br/><br/>L’ordinateur peut être connecté à un réseau d’ordinateur virtuel après que qu’il a été créé
**Basculement vers un site secondaire de VMM, avec le réseau** | Sélectionnez un réseau existant de la machine virtuelle un | Basculement vérifie que les ordinateurs virtuels sont créés. | La machine virtuelle de test sera créée sur le même hôte que l’hôte sur lequel la machine virtuelle de réplica existe. Il n’est pas ajouté au nuage dans lequel se trouve l’ordinateur virtuel de réplica.<br/><br/>Créer un réseau de machine virtuelle a isolé de votre réseau de production<br/><br/>Si vous utilisez un réseau VLAN nous vous recommandons de que vous créez un réseau logique distinct (non utilisé en production) dans VMM à cet effet. Ce réseau logique est utilisé pour créer des réseaux de machine virtuelle pour le test de basculement.<br/><br/>Le réseau logique doit être associé au moins une des cartes réseau de tous les serveurs Hyper-V héberge des ordinateurs virtuels.<br/><br/>Pour les réseaux logiques de VLAN, les sites de réseau que vous ajoutez au réseau logique doivent être isolés.<br/><br/>Si vous utilisez un réseau logique basé sur la virtualisation du réseau Windows, récupération de Site Azure crée automatiquement les réseaux isolés de machine virtuelle.
**Basculement vers un site secondaire de VMM : création d’un réseau** | Un réseau de test temporaire sera créé automatiquement en fonction du paramètre que vous spécifiez dans le **Réseau logique** et ses sites de réseau associés | Basculement vérifie que les ordinateurs virtuels sont créés. | Utilisez cette option si le plan de récupération utilise plus d’un réseau de la machine virtuelle. Si vous utilisez des réseaux de la virtualisation du réseau Windows, cette option peut créer automatiquement des réseaux de machine virtuelle avec les mêmes paramètres (pools d’adresses IP et les sous-réseaux) dans le réseau de la machine virtuelle de réplica. Ces réseaux VM est automatiquement nettoyées après que le basculement de test est terminé.</p><p>La machine virtuelle de test sera créée sur le même hôte que l’hôte sur lequel la machine virtuelle de réplica existe. Il n’est pas ajouté au nuage dans lequel se trouve l’ordinateur virtuel de réplica.

>[AZURE.NOTE] L’adresse IP donnée à une machine virtuelle au cours du test de basculement fait même que l’adresse IP qu’il reçoit lorsqu’un basculement planifié ou non (en supposant que l’adresse IP est disponible dans le réseau de basculement test. Si la même adresse IP n’est pas disponible dans le réseau de basculement test machine virtuelle recevra une autre adresse IP disponible dans le réseau de basculement test.



### <a name="run-a-test-failover-from-on-premises-to-azure"></a>Exécutez un test de basculement sur site vers Azure

Cette procédure décrit comment exécuter un basculement de test pour un plan de récupération. Vous pouvez également exécuter le basculement sur un seul ordinateur dans l’onglet **ordinateurs virtuels** .

1. Sélectionnez des **Plans de reprise** > *recoveryplan_name*. Cliquez sur **Basculer** > **basculement de Test**.
2. Dans la page **Confirmer le basculement Test** , indiquez comment les machines réplica seront connectés à un réseau Azure après le basculement.
3. Si vous êtes basculent vers Azure et cryptage des données est activé pour le cloud, dans la **Clé de cryptage** , sélectionnez le certificat émis lorsque vous avez activé le cryptage des données au cours de l’installation du fournisseur. 
4. Suivre la progression de basculement dans l’onglet **tâches** . Vous permettre de voir la machine de duplication de test dans le portail Azure.
5. Vous pouvez accéder des machines de réplica dans Azure à partir de votre site local initier à une connexion RDP à l’ordinateur virtuel. le port 3389 devez être ouvert sur le point de terminaison pour l’ordinateur virtuel.
5. Une fois que vous avez terminé, lorsque le basculement atteint la phase **de test terminée** , cliquez sur **Test complet** pour terminer.
5. Dans **Notes** , enregistrement d’observations éventuelles associées à la reprise du test.
8. Cliquez sur **le test de basculement est achevé** nettoie automatiquement l’environnement de test. Une fois cette procédure terminée le basculement de test affiche un statut**omplet** C.

> [AZURE.NOTE] Si un test de basculement s’étend sur plus de deux semaines il d’exécution en vigueur. Tous les éléments ou les machines virtuelles créées automatiquement pendant le basculement de test seront supprimés.
  

### <a name="run-a-test-failover-from-a-primary-on-premises-site-to-a-secondary-on-premises-site"></a>Exécuter un test de basculement à partir d’un site sur site principal vers un site secondaire sur site

Vous devez faire un certain nombre de choses à exécuter un basculement de test, y compris la création d’une copie du contrôleur de domaine et placement des serveurs DHCP et DNS de test dans votre environnement de test. Vous pouvez effectuer cela de deux façons :

- Si vous souhaitez exécuter un basculement de test à l’aide d’un réseau existant, vous devez préparer Active Directory, DHCP et DNS de ce réseau.
- Si vous souhaitez exécuter un basculement de test à l’aide de l’option pour créer automatiquement des réseaux de la machine virtuelle, ajoutez l’étape manuelle avant le groupe-1 dans le plan de récupération que vous souhaitez utiliser pour le basculement de test puis ajouter les ressources de l’infrastructure au réseau automatiquement créé avant d’exécuter le test de basculement.

#### <a name="things-to-note"></a>Points à noter

- Lors de la réplication sur un site secondaire, le type de réseau utilisé par l’ordinateur de réplica n’a pas besoin de correspondre au type de réseau logique utilisé pour le test de basculement, mais certaines combinaisons peuvent ne pas fonctionneraient. Si le réplica utilise DHCP et isolement fondés sur les VLAN, le réseau de la machine virtuelle pour le réplica n’a pas besoin d’un pool d’adresses IP statiques. À l’aide de la virtualisation du réseau Windows pour le basculement de test ne fonctionnera pas car les pools d’adresses sont disponibles. En outre test de basculement ne fonctionnera pas si le réseau est non Isolation et le réseau de test est la virtualisation de réseau Windows. C’est pourquoi le réseau d’Isolation de N° n’a pas les sous-réseaux nécessaires à la création d’un réseau de virtualisation du réseau Windows.
- La façon dont le réplica des machines virtuelles sont connectés à mappé réseaux de l’ordinateur virtuel après que basculement dépend de la configuration de réseau de la machine virtuelle dans la console VMM :
    - **Réseau d’ordinateur virtuel configuré avec aucun isolement ou l’isolation des VLAN**: DHCP si est définie pour le réseau de la machine virtuelle, la machine virtuelle de réplica sera connectée à l’ID de VLAN en utilisant les paramètres spécifiés pour le site de réseau dans le réseau logique associé. La machine virtuelle recevra son adresse IP du serveur DHCP disponible. Vous ne devez pas un pool d’adresses IP défini pour le réseau de la machine virtuelle cible. Si un pool d’adresses IP statique est utilisé pour le réseau de la machine virtuelle l’ordinateur virtuel de réplica sera être connecté à l’ID de VLAN en utilisant les paramètres spécifiés pour le site de réseau dans le réseau logique associé. La machine virtuelle recevra son adresse IP à partir du pool défini pour le réseau de la machine virtuelle. Si un pool d’adresses IP statiques n’est pas défini sur le réseau de la machine virtuelle cible, l’allocation d’adresses IP va échouer. Le pool d’adresses IP doit être créé sur les serveurs VMM à la fois la source et la cible que vous souhaitez utiliser pour la protection et la récupération.
    - **La virtualisation de réseau du réseau de machine virtuelle avec Windows**: si un réseau de l’ordinateur virtuel est configuré avec ce paramètre, un pool statique doit être défini pour le réseau de la machine virtuelle cible, que si le réseau de la machine virtuelle source est configuré pour utiliser DHCP ou une adresse IP statique pool d’adresses. Si vous définissez le protocole DHCP, le serveur VMM cible sera agir comme un serveur DHCP et de fournir une adresse IP à partir du pool est défini pour le réseau de la machine virtuelle cible. Si l’utilisation d’un pool d’adresses IP statiques est définie pour le serveur source, le serveur VMM cible alloue une adresse IP à partir du pool. Dans les deux cas, l’allocation d’adresses IP échoue si un pool d’adresses IP statiques n’est pas défini.

#### <a name="run-test"></a>Exécuter le test

Cette procédure décrit comment exécuter un basculement de test pour un plan de récupération. Vous pouvez également exécuter le basculement pour une seule machine virtuelle ou d’un serveur physique sous l’onglet **ordinateurs virtuels** .

1. Sélectionnez des **Plans de reprise** > *recoveryplan_name*. Cliquez sur **Basculer** > **basculement de Test**.
2. Dans la page **Confirmer le basculement de tester** , indiquez comment les machines virtuelles doivent être connectés à des réseaux après le basculement de test.
3. Suivre la progression de basculement dans l’onglet **tâches** . Lorsque le basculement atteint la phase** de test terminée** , cliquez sur **Test complet** pour terminer le test de basculement.
4. Cliquez sur **Notes** pour enregistrer et enregistrer les observations associées avec le basculement de test.
4. Une fois celle-ci terminée Vérifiez que les ordinateurs virtuels démarrent correctement.
5. Après avoir vérifié que les ordinateurs virtuels démarrent correctement, effectuez le basculement test pour nettoyer l’environnement isolé. Si vous avez choisi de créer automatiquement des réseaux de la machine virtuelle, le nettoyage supprime tous les ordinateurs virtuels de test et de test des réseaux.

> [AZURE.NOTE] Si un test de basculement s’étend sur plus de deux semaines il d’exécution en vigueur. Tous les éléments ou les machines virtuelles créées automatiquement pendant le basculement de test seront supprimés.


#### <a name="prepare-dhcp"></a>Préparer le DHCP

Si les ordinateurs virtuels impliqué dans test de basculement utiliser le protocole DHCP, un serveur DHCP de test doit être créé au sein du réseau isolé qui est créé pour le test de basculement.


### <a name="prepare-active-directory"></a>Préparation d’Active Directory
Pour exécuter un basculement de test pour des tests d’applications, vous avez besoin d’une copie de l’environnement Active Directory de production dans votre environnement de test. Passez en revue les [Considérations de basculement pour active directory de tester](site-recovery-active-directory.md#considerations-for-test-failover) la section pour plus de détails. 


### <a name="prepare-dns"></a>Préparation de DNS

Préparer un serveur DNS pour le basculement de test comme suit :

- **DHCP**— si les ordinateurs virtuels utilisent DHCP, l’adresse IP du test DNS doit être mis à jour sur le serveur DHCP de test. Si vous utilisez un type de réseau de la virtualisation réseau de Windows, le serveur VMM joue le rôle du serveur DHCP. Par conséquent, l’adresse IP du serveur DNS doit être mis à jour dans le réseau de basculement test. Dans ce cas, les ordinateurs virtuels s’inscrire au serveur DNS approprié.
- **Adresse statique**, si les ordinateurs virtuels utilisent une adresse IP statique, l’adresse IP du serveur DNS test doit être mis à jour dans le réseau de basculement test. Vous devrez peut-être mettre à jour le DNS avec l’adresse IP des ordinateurs virtuels test. Vous pouvez utiliser l’exemple de script suivant à cet effet : 

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-planned-failover-primary-to-secondary"></a>Exécuter un basculement planifié (primaire au secondaire)

 Cette procédure décrit comment exécuter un basculement planifié pour un plan de récupération. Vous pouvez également exécuter le basculement pour une seule machine virtuelle sur l’onglet **ordinateurs virtuels** .

1. Avant de commencer, veillez à tous les ordinateurs virtuels que vous souhaitez basculer ont terminé la réplication initiale.
2. Sélectionnez des **Plans de reprise** > *recoveryplan_name*. Cliquez sur **Basculer** > **prévu de basculement**. 
3. Dans la page **Confirmer le basculement planifié **, choisissez les emplacements source et cible. Notez le sens de basculement.

    - Si les basculements précédentes a fonctionné comme prévu et que tous les serveurs de la machine virtuelle sont situés dans l’emplacement de la source ou la cible, les détails de direction de basculement sont à titre informatif uniquement. 
    - Si les ordinateurs virtuels sont actifs sur les emplacements source et cible, le bouton **Modifier l’orientation** s’affiche. Ce bouton permet de modifier et de spécifier la direction dans laquelle la reprise doit avoir lieu.

5. Si vous êtes basculent vers Azure et cryptage des données est activé pour le cloud, dans la **Clé de cryptage** , sélectionnez le certificat émis lorsque vous avez activé le cryptage des données au cours de l’installation du fournisseur sur le serveur VMM. 
6. Lorsqu’un basculement planifié commence la première étape consiste à arrêter les machines virtuelles, pour éviter toute perte de données. Vous pouvez suivre la progression de la reprise de l’onglet **tâches** . Si une erreur se produit dans le basculement (soit sur une machine virtuelle ou dans un script qui est inclus dans le plan de récupération), le basculement planifié d’un plan de restauration s’arrête. Vous pouvez lancer le basculement à nouveau.
8. Une fois que les ordinateurs virtuels de réplica sont créés, ils sont dans une validation en état d’attente. Cliquez sur **validation** pour valider le basculement. 
9. Lorsque la réplication est exécuter l’ordinateurs virtuels de démarrage à l’emplacement secondaire. 

## <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

Cette procédure décrit comment exécuter un basculement non planifié pour un plan de récupération. Vous pouvez également exécuter le basculement pour une seule machine virtuelle ou d’un serveur physique sous l’onglet **ordinateurs virtuels** .

1. Sélectionnez des **Plans de reprise** > *recoveryplan_name*. Cliquez sur **Basculer** > **basculement non planifié**. 
3. Dans la page **Confirmer le basculement non planifié **, choisissez les emplacements source et cible. Notez le sens de basculement.

    - Si les basculements précédentes a fonctionné comme prévu et que tous les serveurs de la machine virtuelle sont situés dans l’emplacement de la source ou la cible, les détails de direction de basculement sont à titre informatif uniquement. 
    - Si les ordinateurs virtuels sont actifs sur les emplacements source et cible, le bouton **Modifier l’orientation** s’affiche. Ce bouton permet de modifier et de spécifier la direction dans laquelle la reprise doit avoir lieu.

4. Si vous êtes basculent vers Azure et cryptage des données est activé pour le cloud, dans la **Clé de cryptage** , sélectionnez le certificat émis lorsque vous avez activé le cryptage des données au cours de l’installation du fournisseur sur le serveur VMM. 
5. Sélectionnez **Arrêter les machines virtuelles et synchroniser les données les plus récentes pour spécifier que la récupération de Site doit essayer à arrêter les ordinateurs virtuels protégés et synchroniser les données de manière à ce que la dernière version des données est alors basculée.** Si vous ne sélectionnez pas cette option, ou la tentative n’aboutit pas, le basculement sur incident sera à partir du dernier point de récupération disponible pour l’ordinateur virtuel.
6. Vous pouvez suivre la progression de la reprise de l’onglet **tâches** . Notez que même si des erreurs se produisent au cours d’un basculement non planifié, le plan de récupération s’exécute jusqu'à ce qu’elle soit terminée.
7. Après le basculement, les ordinateurs virtuels sont dans un état de **validation en attente** . Cliquez sur **validation** pour valider le basculement.
8. Si vous configurez la réplication pour utiliser plusieurs points de récupération, de changement de Point de récupération permet d’utiliser un point de récupération qui n’est pas des plus tard (le plus récent est utilisé par défaut). Après avoir validé supplémentaires seront supprimés des points de récupération.
9. Une fois la réplication terminée les ordinateurs virtuels démarrent et s’exécutent à l’emplacement secondaire. Toutefois, ils ne sont pas protégés ou de la réplication. Lorsque le site principal est disponible à l’aide de la même infrastructure sous-jacente, cliquez sur **Répliquer inverse** pour commencer la réplication inverse. Cela garantit que toutes les données sont répliquées sur le site principal, et que l’ordinateur virtuel est prêt pour le basculement de nouveau. Inverser la réplication après qu’un basculement non planifié entraîne une surcharge de transfert de données. Le transfert utilise la même méthode que celle qui est configurée pour les paramètres de réplication initiale pour le cloud.

## <a name="failback-from-secondary-to-primary"></a>Retour arrière depuis le secondaire principal

 Après le basculement du site principal vers un site secondaire, les machines virtuelles dupliquées ne sont pas protégés par la restauration du Site et le site secondaire est maintenant agissant comme le serveur principal. Suivez ces procédures pour basculer sur le site principal initial. Cette procédure décrit comment exécuter un basculement planifié pour un plan de récupération. Vous pouvez également exécuter le basculement pour une seule machine virtuelle sur l’onglet **ordinateurs virtuels** .

1. Sélectionnez des **Plans de reprise** > *recoveryplan_name*. Cliquez sur **Basculer** > **prévu de basculement**.
2. Dans la page **Confirmer le basculement planifié **, choisissez les emplacements source et cible. Notez le sens de basculement. Si le basculement de principal a fonctionné comme prévu et toutes les machines virtuelles sont dans l’emplacement secondaire, que c’est à titre informatif uniquement.
3. Si vous êtes échouent à partir de Azure sélectionnez les paramètres de **Synchronisation**de données :

    - **Synchroniser les données avant le basculement (synchroniser les changements delta uniquement)**: cette option permet de réduire les interruptions de service pour les machines virtuelles comme il synchronise sans les fermer. Il effectue les opérations suivantes :
        - Phase 1 : Prend un instantané de la machine virtuelle dans Azure et la copie sur l’hôte Hyper-V sur site. L’ordinateur continue de s’exécuter Azure.
        - Phase 2 : S’arrête l’ordinateur virtuel dans Azure afin qu’aucune nouvelle modification se produire il. Le dernier ensemble de modifications sont transférées vers le serveur local et l’ordinateur virtuel sur site est démarré.
    

    - **Synchroniser les données au cours du basculement uniquement (téléchargement complet)**: utilisez cette option si vous avez déjà exécuté dans Azure pendant une longue période. Cette option est plus rapide car nous prévoyons que la plupart du disque a changé et que nous ne souhaitez pas consacrer de temps dans le calcul du checksum. Il effectue un téléchargement du disque. Il est également utile lorsque la machine virtuelle sur prem a été supprimée.
    
    > [AZURE.NOTE] Nous vous recommandons d’utiliser cette option si vous avez déjà exécuté Azure pendant un certain temps (un mois ou plus) ou la machine virtuelle sur prem a été supprimée. Cette option n’effectue pas les calculs de somme de contrôle.
    
5. Si vous êtes basculent vers Azure et cryptage des données est activé pour le cloud, dans la **Clé de cryptage** , sélectionnez le certificat émis lorsque vous avez activé le cryptage des données au cours de l’installation du fournisseur sur le serveur VMM. 
5. Le dernier point de reprise est utilisé par défaut, mais dans le **Changement de Point de récupération** , vous pouvez spécifier un point de restauration différent. 
6. Cliquez sur la case à cocher pour démarrer la restauration.  Vous pouvez suivre la progression de la reprise de l’onglet **tâches** . 
7. f, vous avez sélectionné l’option pour synchroniser les données avant le basculement sur incident, une fois la synchronisation des données initiale est terminée et vous êtes prêt à fermer les ordinateurs virtuels dans Azure, cliquez sur **tâches**  >  <planned failover job name> **Basculement complet**. Cela arrête la machine Azure transfère les dernières modifications apportées à la machine virtuelle sur site et lance l’application.
8. Vous pouvez maintenant vous ouvrez une session sur l’ordinateur virtuel à valider est disponible comme prévu. 
9. L’ordinateur virtuel est une validation en état d’attente. Cliquez sur **validation** pour valider le basculement.
10. Pour terminer la restauration automatique Cliquez sur **Inverser répliquer** pour commencer à protéger l’ordinateur virtuel dans le site principal.



## <a name="failback-to-an-alternate-location"></a>Restauration vers un autre emplacement

Si vous avez déployé entre un [site de Hyper-V et Azure](site-recovery-hyper-v-site-to-azure.md) à capacité de restauration à partir d’Azure pour avoir une autre local emplacement. Cela est utile si vous avez besoin définir un nouveau matériel sur site. Voici comment procéder.

1. Si vous configurez un nouveau matériel installer Windows Server 2012 R2 et le rôle Hyper-V sur le serveur.
2. Créer un commutateur réseau virtuel portant le même nom qui se trouvaient sur le serveur d’origine.
3. Sélectionner des **Éléments de protection** -> **Groupe de Protection**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> vous souhaitez restaurer et sélectionnez **Planifié de basculement**.
4. Dans **Confirmer le basculement planifié** , sélectionnez **créer sur site virtual machine si elle n’existe pas**. 
5. Dans la zone **Nom de l’hôte** , sélectionnez le nouveau serveur hôte Hyper-V sur lequel vous souhaitez placer l’ordinateur virtuel.
6. Synchronisation de données, nous vous recommandons de sélectionner l’option **synchroniser les données avant le basculement**. Cela permet de minimiser les interruptions de service pour les machines virtuelles comme il synchronise sans les fermer. Il effectue les opérations suivantes :

    - Phase 1 : Prend un instantané de la machine virtuelle dans Azure et la copie sur l’hôte Hyper-V sur site. L’ordinateur continue de s’exécuter Azure.
    - Phase 2 : S’arrête l’ordinateur virtuel dans Azure afin qu’aucune nouvelle modification se produire il. Le dernier ensemble de modifications sont transférées vers le serveur local et l’ordinateur virtuel sur site est démarré.
    
7. Cliquez sur la case à cocher pour commencer la reprise (restauration).
8. Après la synchronisation initiale est terminée et vous êtes prêt à arrêter la machine virtuelle dans Azure, cliquez sur **tâches** > <planned failover job> > **Basculement complet**. Cela arrête la machine Azure, transfère les dernières modifications apportées à la machine virtuelle sur site et le démarre.
9. Vous pouvez ouvrir une session sur l’ordinateur virtuel sur site pour vérifier que tout fonctionne comme prévu. Puis cliquez sur **Valider** pour terminer le basculement.
10. Cliquez sur **Inverser répliquer** pour commencer à protéger la machine virtuelle sur site.

    >[AZURE.NOTE] Si vous annulez la tâche de restauration alors qu’il est dans l’étape de la synchronisation des données, la machine virtuelle sur site sera dans un état endommagées. C’est parce que les copies de synchronisation de données les données les plus récentes à partir d’Azure VM de disques sur les disques de données sur prem et jusqu'à la synchronisation est terminée, le disque de données ne peuvent pas être dans un état cohérent. Si la machine virtuelle de On-prem est démarrée après que la synchronisation de données est annulée, elle peut ne pas amorcer. Nouveau déclencher le basculement pour terminer la synchronisation de données.
 
