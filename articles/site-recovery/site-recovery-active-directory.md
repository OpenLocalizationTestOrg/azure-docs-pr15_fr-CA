<properties
    pageTitle="Protéger Active Directory et DNS avec récupération de Site Azure | Microsoft Azure"
    description="Cet article décrit comment mettre en œuvre une solution de reprise après sinistre pour Active Directory à l’aide de la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Protéger Active Directory et DNS avec récupération de Site Azure

Les applications d’entreprise telles que SharePoint, Dynamics AX et SAP dépendent de Active Directory et une infrastructure DNS fonctionnent correctement. Lorsque vous créez une solution de reprise après sinistre pour les applications, il est important de se rappeler que vous devez protéger et restaurer Active Directory et DNS avant les autres composants d’application, afin de garantir que les choses fonctionnent correctement en cas de reprise après sinistre.

Récupération de site est un service Azure fournit la reprise après sinistre par l’orchestration de réplication, le basculement et la restauration des machines virtuelles. Récupération de site prend en charge un certain nombre de scénarios de réplication pour toujours protéger et en toute transparence machines virtuelles de basculement des applications et à nuages hébergeur, public ou privé.

À l’aide de la récupération de Site, vous pouvez créer un plan complet de récupération automatisée pour Active Directory. En cas d’interruptions de service, vous pouvez initier un basculement en secondes depuis n’importe où et devenir Active Directory opérationnel en quelques minutes. Si vous avez déployé Active Directory pour plusieurs applications, telles que SharePoint et SAP sur votre site principal, et que vous voulez basculer vers le site terminé, vous pouvez basculer vers Active Directory à l’aide de restauration du Site et puis basculer sur les autres applications à l’aide de plans de récupération d’application spécifiques.

Cet article explique comment créer une solution de reprise après sinistre pour Active Directory, comment effectuer planifié, planifié et les basculements de test à l’aide d’un plan de récupération d’un seul clic, les configurations prises en charge et les conditions préalables.  Avant de commencer, vous devez être familiarisé avec Active Directory et de la récupération de Site Azure.

Il existe deux options recommandées en fonction de la complexité de votre environnement.

### <a name="option-1"></a>Option 1

Si vous avez un petit nombre d’applications et un seul contrôleur de domaine et que vous souhaitez basculer l’ensemble du site, puis nous vous recommandons de répliquer le contrôleur de domaine vers le site secondaire (si vous êtes basculent vers Azure ou sur un site secondaire) à l’aide de la récupération de Site. La même machine virtuelle répliquée peut servir pour le test de basculement trop.

### <a name="option-2"></a>Option 2

Si vous avez un grand nombre d’applications et il existe plus d’un contrôleur de domaine dans l’environnement, ou si vous souhaitez basculer quelques applications à la fois, nous vous recommandons en plus de la réplication de l’ordinateur virtuel de contrôleur de domaine avec la récupération de Site, vous allez également configurer un contrôleur de domaine supplémentaire sur le site cible (Azure ou un centre de données secondaire sur site).

>[AZURE.NOTE] Même si vous implémentez Option-2, pour effectuer un basculement sur incident test que vous devrez répliquer le contrôleur de domaine à l’aide de la récupération de Site. Lire le [test des considérations de basculement](#considerations-for-test-failover) pour plus d’informations.


Les sections suivantes expliquent comment activer la protection d’un contrôleur de domaine de récupération de Site et comment configurer un contrôleur de domaine dans Azure.


## <a name="prerequisites"></a>Conditions préalables

- Un déploiement sur site, de serveur DNS et Active Directory.
- Un coffre-fort de Services de récupération de Site Azure dans un abonnement Microsoft Azure.
- Si vous utilisez la réplication vers Azure exécuter l’outil Readiness Assessment de la Machine virtuelle Azure sur des ordinateurs virtuels pour s’assurer qu’elles sont compatibles avec Azure VM et les Services de récupération de Site Azure.


## <a name="enable-protection-using-site-recovery"></a>Activer la protection à l’aide de la récupération de Site


### <a name="protect-the-virtual-machine"></a>Protéger l’ordinateur virtuel

Activer la protection de l’ordinateur virtuel de/DNS du contrôleur de domaine de récupération de Site. Configurer les paramètres de récupération de Site en fonction du type d’ordinateur virtuel (Hyper-V ou VMware). Nous vous recommandons d’une fréquence de réplication cohérente de blocage de 15 minutes.

###<a name="configure-virtual-machine-network-settings"></a>Configurer les paramètres de réseau de machine virtuelle

Pour la machine virtuelle / DNS du contrôleur de domaine, configurer les paramètres réseau de la récupération de Site afin que l’ordinateur virtuel sera joint au réseau droit après le basculement. Par exemple, si vous êtes réplication des ordinateurs virtuels Hyper-V vers Azure vous pouvez sélectionner la machine virtuelle dans le nuage VMM ou du groupe de protection pour configurer les paramètres réseau, comme indiqué ci-dessous

![Paramètres de réseau de machine virtuelle](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Protéger Active Directory avec la réplication Active Directory

### <a name="site-to-site-protection"></a>Protection de site à site

Créer un contrôleur de domaine sur le site secondaire et spécifiez le nom qui est utilisé sur le site principal lorsque vous promouvez le serveur à un contrôleur de domaine du même domaine. Vous pouvez utiliser le composant logiciel enfichable **Services et Sites Active Directory** pour configurer les paramètres sur l’objet lien de sites auquel les sites sont ajoutés. En configurant des paramètres sur un lien de site, vous pouvez contrôler quand la réplication se produit entre deux sites ou plus et à quelle fréquence. Pour plus d’informations, reportez-vous à la section [Planification de la réplication entre les Sites](https://technet.microsoft.com/library/cc731862.aspx) .

###<a name="site-to-azure-protection"></a>Protection du site-à-Azure

Suivez les instructions pour [créer un contrôleur de domaine dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous promouvez le serveur à un contrôleur de domaine, spécifiez le même nom de domaine qui est utilisé sur le site principal.

Puis [Reconfigurez le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), pour utiliser le serveur DNS dans Azure.

![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Considérations de basculement test

Basculement de test se produit dans un réseau qui est isolé du réseau de production afin qu’il n’y a aucun impact sur les charges de travail.

La plupart des applications nécessitent également la présence d’un contrôleur de domaine et un serveur DNS fonctionne, avant que l’application a basculé, un contrôleur de domaine doit être créé dans le réseau isolé à utiliser pour le test de basculement. Le moyen le plus simple de procéder est pour activer la protection sur l’ordinateur virtuel contrôleur/DNS du domaine avec récupération de Site et exécuter un basculement de test de cet ordinateur virtuel, avant d’exécuter un basculement de test du plan de récupération de l’application. Voici comment procéder :

1. Activer la protection dans la récupération de Site pour l’ordinateur virtuel/DNS du contrôleur du domaine.
2. Créer un réseau isolé. Un réseau virtuel est créé par défaut dans Azure est isolé des autres réseaux. Nous recommandons que la plage d’adresses IP pour ce réseau est la même que celle de votre réseau de production. N’activez pas la connectivité site à site sur ce réseau.
3. Fournissez une adresse IP DNS dans le réseau créé, comme l’adresse IP que vous attendez de la machine virtuelle DNS pour obtenir. Si vous utilisez la réplication vers Azure, puis indiquez l’adresse IP de la machine virtuelle qui sera utilisée lors du basculement dans le paramètre **d’Adresse IP cible** dans les propriétés de la machine virtuelle. Si vous effectuez une réplication vers un autre site et locaux vous utilisez suivi DHCP les instructions pour [l’installation de DNS et DHCP pour le test de basculement](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] L’adresse IP allouée à une machine virtuelle lors d’un basculement de test est identique à l’adresse IP, qu'il obtiendra lors d’un basculement planifié ou, si l’adresse IP est disponible dans le réseau de basculement test. Si elle n’est pas le cas, l’ordinateur virtuel reçoit une adresse IP différente qui est disponible dans le réseau de basculement test.

4. Sur l’ordinateur virtuel de contrôleur de domaine exécuter un test de basculement de celui-ci dans le réseau isolé. Dernier point de récupération cohérent d’application disponible de l’ordinateur virtuel de contrôleur de domaine permet de faire le test de basculement. 
5. Exécutez un test de basculement pour le plan de récupération d’application.
6. Une fois le test terminé, vous marquez la tâche de basculement de test de l’ordinateur virtuel contrôleur du domaine et du plan de récupération « Complète » dans l’onglet **tâches** dans le portail de récupération de Site.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS et contrôleur de domaine sur des ordinateurs différents

Si DNS n’est pas sur la même machine virtuelle comme contrôleur de domaine, vous devrez créer un DNS VM pour le test de basculement. S’ils sont sur l’ordinateur virtuel même, vous pouvez ignorer cette section.

Vous pouvez utiliser un serveur DNS fraîche et créer toutes les zones requises. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone DNS avec le nom contoso.com. Les entrées correspondant à Active Directory doivent être mis à jour dans le système DNS, comme suit :

1. Assurez-vous que ces paramètres sont en place avant que toute autre machine virtuelle dans le plan de récupération :

    - La zone doit être nommée d’après le nom de la racine de la forêt.
    - La zone doit être sauvegardés.
    - La zone doit être activée pour les mises à jour sécurisées et non sécurisées.
    - Le programme de résolution de l’ordinateur virtuel de contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle DNS.

2. Sur l’ordinateur du contrôleur de domaine virtuel, exécutez la commande suivante :

    `nltest /dsregdns`

3. Ajouter une zone sur le serveur DNS autorise les mises à jour non sécurisées et ajoutez-lui une entrée DNS :

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Étapes suivantes

Lecture [les charges de travail puis-je protéger ?](../site-recovery/site-recovery-workload.md) pour en savoir plus sur la protection de fortes charges de travail avec la récupération de Site Azure.
