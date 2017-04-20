<properties
    pageTitle="Répliquer les machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure (hérité) | Microsoft Azure" 
    description="Décrit comment répliquer les VM sur site et serveurs physiques Linux/Windows Azure à l’aide de récupération de Site Azure dans un déploiement hérité dans le portail classique." 
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Répliquer les machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure via le portail classique (hérité)

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmware-to-azure.md)
- [Portail classique](site-recovery-vmware-to-azure-classic.md)
- [Portail classique (hérité)](site-recovery-vmware-to-azure-classic-legacy.md)


Bienvenue dans restauration du Site Azure ! Cet article décrit un déploiement hérité pour la réplication des machines virtuelles VMware sur site ou les serveurs physiques Windows/Linux vers Azure à l’aide de la récupération de Site Azure dans le portail classique.

## <a name="overview"></a>Vue d’ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR doit conserver les données d’entreprise d’et s’assurer que les charges de travail restent disponibles en permanence en cas de reprise après sinistre.

Récupération de site est un service Azure qui participe à votre stratégie de BCDR par l’orchestration de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure) ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous basculent vers l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal. Pour en savoir plus [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)


>[AZURE.WARNING] Cet article contient des **instructions héritées**. Ne pas l’utiliser pour les nouveaux déploiements. Au lieu de cela, [Suivez ces instructions](site-recovery-vmware-to-azure.md) pour déployer la récupération de Site dans le portail Azure, ou [Utilisez les instructions suivantes](site-recovery-vmware-to-azure-classic.md) pour configurer le déploiement amélioré dans le portail classique. Si vous avez déjà déployé à l’aide de la méthode décrite dans cet article, nous vous recommandons de migrer vers le déploiement amélioré dans le portail classique.


## <a name="migrate-to-the-enhanced-deployment"></a>Migrer vers le déploiement amélioré

Cette section est pertinente uniquement si vous avez déjà déployé la récupération de Site en suivant les instructions de cet article.

Pour migrer votre déploiement existant, que vous devez :

1. Déployer les nouveaux composants de récupération de Site dans votre site local.
2. Configurer les informations d’identification pour la découverte automatique des ordinateurs virtuels VMware sur le nouveau serveur de configuration.
3. Découvrez les serveurs VMware avec le nouveau serveur de configuration.
3. Créer un nouveau groupe de protection avec le nouveau serveur de configuration.


Avant de commencer :

- Nous vous conseillons de définir une fenêtre de maintenance pour la migration.
- L’option de **Migrer des Machines** est disponible uniquement si vous avez des groupes de protection existants qui ont été créés au cours d’un déploiement hérité.
- Une fois que vous avez effectué les étapes de la migration peut prendre 15 minutes ou plus pour actualiser les informations d’identification et pour découvrir et actualiser les ordinateurs virtuels afin que vous pouvez les ajouter à un groupe de protection. Vous pouvez actualiser manuellement au lieu d’attendre. 

La migration comme suit :

1. Découvrez le [déploiement amélioré dans le portail classique](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Passez en revue meilleure [architecture](site-recovery-vmware-to-azure-classic.md#scenario-architecture)et [composants requis](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Désinstaller le service de la mobilité des machines que vous êtes actuellement la réplication. Une nouvelle version du service doit être installée sur les ordinateurs lorsque vous les ajoutez au nouveau groupe de protection.
3. Télécharger une [clé d’enregistrement de chambre forte](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) et [d’exécuter l’Assistant programme d’installation unifié](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) pour installer le serveur de configuration, serveur de traitement et composants de serveur maître cible. Plus d’informations sur la [planification de la capacité](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Configurer les informations d’identification](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) que la récupération Site permet d’accéder aux serveur VMware pour découvrir automatiquement les ordinateurs virtuels VMware. Obtenir des informations sur les [autorisations requises](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Ajouter des [Serveurs vCenter ou vSphere hôtes](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Il peut prendre 15 minutes pour en savoir plus pour les serveurs apparaissent dans le portail de récupération de Site.
6. Créer un [Nouveau groupe de protection](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Il peut prendre jusqu'à 15 minutes pour le portail actualiser afin que les ordinateurs virtuels sont découverts et s’affichent. Si vous ne voulez pas attendre, vous pouvez sélectionner le nom de serveur de gestion (ne cliquez pas dessus) > **Actualiser**.
7. Sous le nouveau groupe de protection, cliquez sur **Effectuer la migration des ordinateurs**.

    ![Ajouter compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. Dans **Sélectionner des ordinateurs** , sélectionnez le groupe de protection que vous voulez migrer à partir de, et les ordinateurs que vous souhaitez migrer.

    ![Ajouter compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. Dans **Configurer les paramètres de cible** , indiquez si vous souhaitez utiliser les mêmes paramètres pour tous les ordinateurs, puis sélectionnez le serveur de traitement et d’un compte de stockage Azure. Si vous n’avez pas un serveur de traitement distinct, il s’agit de l’adresse IP du serveur server configuration.


    ![Ajouter compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

10. **Spécifier les comptes**, sélectionnez le compte que vous avez créé pour le serveur de traitement pour accéder à l’ordinateur pour envoyer la nouvelle version de service de la mobilité.

    ![Ajouter compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Récupération de site sera migrer vos données répliquées vers le compte de stockage Azure que vous avez fournies. Si vous le souhaitez, vous pouvez réutiliser le compte de stockage que vous avez utilisé dans le déploiement hérité.
12. Une fois que la tâche se termine les ordinateurs virtuels se synchronise automatiquement. Une fois la synchronisation terminée, vous pouvez supprimer les ordinateurs virtuels du groupe de protection existants.
13. Une fois que tous les ordinateurs ont migré, vous pouvez supprimer le groupe de protection existants.
14. N’oubliez pas de spécifier les propriétés de basculement sur incident pour les machines et les paramètres de réseau Azure une fois la synchronisation terminée.
15. Si vous avez des plans de récupération existants, vous pouvez les migrer vers le déploiement amélioré avec l’option **Migrer un Plan de récupération** . Vous devez uniquement procéder une fois que tous les ordinateurs protégés qui ont été migrés. 

    ![Ajouter compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Une fois que vous avez terminé la migration poursuivre l' [amélioration de l’article](site-recovery-vmware-to-azure-classic.md). Le reste de cet article hérité ne seront plus pertinent et vous n’avez pas besoin de suivre les étapes décrites dans l’informatique ** autres.




## <a name="what-do-i-need"></a>Que faut-il ?

Ce diagramme montre les composants de déploiement.

![Nouveau coffre-fort](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Voici ce dont vous aurez besoin :

**Composant** | **Déploiement** | **Détails**
--- | --- | ---
**Serveur de configuration** | Une Azure standard A3 machine virtuelle dans la souscription de même que la récupération de Site. | Le serveur de configuration coordonne les communications entre ordinateurs protégés, le serveur de traitement et des serveurs cible principal dans Azure. Il configure la réplication et de récupération de coordonnées dans Azure lors du basculement.
**Serveur cible principale** | Une machine virtuelle Azure — un serveur Windows basé sur une photo de la galerie Windows Server 2012 R2 (pour protéger des ordinateurs Windows), ou sous la forme d’un serveur Linux basée sur une photo de la galerie de OpenLogic CentOS 6.6 (pour protéger les ordinateurs Linux).<br/><br/> Options de dimensionnement trois sont disponibles : Standard A4, D14 Standard et DS4 Standard.<br/><br/> Le serveur est connecté au même réseau que le serveur de configuration Azure.<br/><br/> Vous avez configuré dans le portail de récupération de Site | Il reçoit et conserve les données répliquées à partir de vos machines protégées à l’aide de joint de disques durs virtuels créés sur le stockage des objets blob dans votre compte de stockage Azure.<br/><br/> Sélectionnez DS4 Standard spécialement pour la configuration de la protection des charges de travail nécessitant des performances élevées et une faible latence à l’aide du compte de stockage Premium.
**Serveur de traitement** | Un serveur virtuel ou physique sur site exécutant Windows Server 2012 R2<br/><br/> Nous vous recommandons il est placé sur le même réseau et un segment de réseau local que les ordinateurs que vous souhaitez protéger, mais il peut s’exécuter sur un autre réseau tant que machines protégées disposent d’une visibilité de réseau L3 lui.<br/><br/> Vous définissez et l’enregistrer sur le serveur de configuration du portail de la récupération de Site. | Ordinateurs protégés envoient des données de réplication pour le serveur de traitement sur place. Contient une cache de mettre en cache la réplication sur disque de données qu’il reçoit. Il exécute un nombre d’actions sur ces données.<br/><br/> Il optimise les données par la mise en cache, de compression et de cryptage avant de l’envoyer au serveur maître cible.<br/><br/> Il gère l’installation par diffusion du Service de la mobilité.<br/><br/> Il effectue la détection automatique des machines virtuelles VMware.
**Machines sur site** | Les machines virtuelles VMware sur site ou des serveurs physiques exécutant Windows ou Linux. | Vous configurez les paramètres de réplication qui s’appliquent à un ou plusieurs ordinateurs. Vous pouvez échouer sur un ordinateur individuel ou plus fréquemment, plusieurs ordinateurs vous rassemblez dans un plan de récupération. 
**Service de la mobilité** | Installé sur chaque ordinateur virtuel ou d’un serveur physique à protéger<br/><br/> Peuvent être installées manuellement ou poussée et installé automatiquement par le serveur de traitement lorsque vous activez la réplication pour une machine. | Le service de la mobilité envoie les données vers le serveur de traitement durant la réplication initiale (resynchronisation). Une fois que l’ordinateur est dans un état de protection (fin de resynchronisation) le service de la mobilité capture les écritures sur disque en mémoire et les envoie au serveur de traitement. Cohérencede l’application concernée pour les serveurs Windows est obtenue à l’aide de VSS.
**Azure coffre-fort de récupération de Site** | Vous créez un coffre-fort de récupération de Site avec un abonnement Azure et enregistrez des serveurs dans la chambre forte. | Le coffre-fort coordonne et orchestre la réplication des données, le basculement et restauration entre votre site local et le Azure.
**Mécanisme de réplication** | **Sur Internet**, de canal communique et réplique des données à partir des serveurs sur site protégé vers Azure à l’aide de SSL/TLS sécurisée sur internet. Il s’agit de l’option par défaut.<br/><br/> **VPN/ExpressRoute**— communique et réplique des données entre des serveurs sur site et Azure via une connexion VPN. Vous aurez besoin configurer un VPN de site à site ou d’une connexion ExpressRoute entre le site local et le réseau Azure.<br/><br/> Vous allez sélectionner comment vous souhaitez répliquer au cours du déploiement de la récupération de Site. Vous ne pouvez pas modifier le mécanisme après que qu’il est configuré sans impact sur la réplication des ordinateurs existants. | Aucune option ne vous oblige à ouvrir tous les ports réseau entrant sur les ordinateurs protégés. Toutes les communications réseau sont lancée à partir du site local. 

## <a name="capacity-planning"></a>Planification de la capacité

Les principales zones que vous devrez prendre en compte :

- **Environnement source**: VMware de l’infrastructure, les paramètres machine source et besoins.
- **Serveurs de composants**, le processus serveur, serveur de configuration et maître serveur cible 

### <a name="considerations-for-the-source-environment"></a>Considérations sur l’environnement source

- **Taille de disque maximale**, la taille maximale actuelle du disque qui peut être associée à une machine virtuelle est de 1 To. Ainsi, la taille maximale d’un disque source pouvant être répliquées est également limitée à 1 To.
- **Taille maximum par source**: la taille maximale d’une machine source unique est 31 To (avec 31 disques) et avec une instance D14 mis en service pour le serveur cible principale. 
- **Nombre de sources par serveur cible principale**: plusieurs ordinateurs source peuvent être protégées avec un serveur cible de maître unique. Toutefois, un ordinateur source unique ne peut pas être protégé sur plusieurs serveurs cible principal, étant donné que les disques répliquent, un disque dur virtuel qui reflète la taille du disque est créé sur le stockage des objets blob Azure et attaché sous la forme d’un disque de données sur le serveur cible principale.  
- **Maximum quotidiennement modifier taux par source**, il existe trois facteurs à prendre en considération lorsque vous étudiez le taux de modification recommandée par la source. Pour les considérations relatives à la cible en fonction Ops ES/deux s sont requis sur le disque cible pour chaque opération de la source. C’est parce qu’une lecture d’anciennes données et l’écriture de nouvelles données se produit sur le disque cible. 
    - **Prise en charge par le serveur de traitement de rythme quotidien de modification**— une machine source ne peut pas s’étendre sur plusieurs serveurs de traitement. Un seul serveur peut prendre en charge jusqu'à 1 To de taux de change quotidien. 1 To est donc que le plus de données quotidiennes modifier taux pris en charge pour une machine de source. 
    - **Le débit maximal pris en charge par le disque cible**: évolution du Maximum par disque source ne peut pas être plus de 144 Go par jour (avec une taille d’écriture de 8 Ko). Consultez le tableau dans la section cible principale pour le débit et les e/s de la cible pour écrire des tailles différentes. Ce nombre doit être divisé par deux, car chaque source IOP génère 2 Ops ES/s sur le disque cible. Obtenir des informations sur [des cibles de performances et d’évolutivité Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) lors de la configuration de la cible pour les comptes de stockage premium.
    - **Le débit maximal pris en charge par le compte de stockage**— une source ne peut pas s’étendre sur plusieurs comptes de stockage. Étant donné qu’un compte de stockage prend un maximum de 20 000 requêtes par seconde, et que chaque source IOP génère 2 Ops ES/s sur le serveur cible principal, nous vous recommandons de vous le nombre des Ops ES/s sur la source à 10 000. Obtenir des informations sur [des cibles de performances et d’évolutivité Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) lors de la configuration de la source pour les comptes de stockage premium.

### <a name="considerations-for-component-servers"></a>Considérations pour les serveurs de composants

Le tableau 1 récapitule les tailles de machine virtuelle pour la configuration et les serveurs cibles maître.

**Composant** | **Instances d’Azure déployés** | **Noyaux** | **Mémoire** | **Disques max** | **Taille du disque**
--- | --- | --- | --- | --- | ---
Serveur de configuration | A3 standard | 4 | 7 GO | 8 | 1023 GO
Serveur cible principale | A4 standard | 8 | 14 GO | 16 | 1023 GO
 | D14 standard | 16 | 112 GO | 32 | 1023 GO
 | DS4 standard | 8 | 28 GO | 16 | 1023 GO

**Tableau 1**

#### <a name="process-server-considerations"></a>Considérations sur le serveur de traitement

Taille du processus serveur dépend généralement le taux de modification quotidien sur toutes les charges de travail protégés.


- Vous avez besoin de calcul suffisante pour effectuer des tâches telles que le cryptage et la compression en ligne.
- Le serveur utilise le cache disque. Assurez-vous que le débit d’espace disque et de cache recommandée est disponible afin de faciliter les modifications de données stockées en cas de goulot d’étranglement du réseau ou de panne. 
- Garantir une bande passante suffisante pour que le serveur de traitement peut télécharger les données vers le serveur cible maître pour fournir une protection continue des données. 

Le tableau 2 fournit un résumé des instructions sur le serveur le processus.

**Taux de modification des données** | **UNITÉ CENTRALE** | **Mémoire** | **Taille du cache disque**| **Débit du cache disque** | **ENTREE/sortie de la bande passante**
--- | --- | --- | --- | --- | ---
< 300 Go | 4 vCPUs (2 sockets * 2 noyaux @ 2.5 GHz) | 4 GO | 600 GO | 7 voire 10 Mo par seconde | Mbits/s de 30 Mbits/s/21
300 à 600 Go | 8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz) | 6 GO | 600 GO | 11 à 15 de Mo par seconde | 60 Mbits/s de 42 Mbits/s
600 Go à 1 to | 12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz) | 8 GO | 600 GO | pour entre 16 et 20 Mo par seconde | 100 Mbits/s Mbps/70
> 1 to | Déployer un autre serveur de traitement | | | | 

**Le tableau 2**

Où : 

- Pénétration est la bande passante de téléchargement (intranet entre le serveur source et le processus).
- Sortie est la bande passante de téléchargement (internet entre le serveur et le serveur cible principale). Les numéros de sortie présument moyenne compression serveur de processus de 30 %.
- Cache un disque distinct du système d’exploitation minimum 128 Go est recommandé pour tous les serveurs de traitement.
- Débit de disque cache le stockage suivant a été utilisé, les bancs d’essai : 8 disques SAS de 10 K TPM avec une configuration RAID 10.

#### <a name="configuration-server-considerations"></a>Considérations sur la configuration serveur

Chaque serveur de configuration peut prendre en charge jusqu'à 100 machines d’origine avec des volumes de 3-4. Si votre déploiement est plus grand, nous vous recommandons de que déployer un autre serveur de configuration. Reportez-vous au tableau 1 pour les propriétés de la machine virtuelle par défaut du serveur de configuration. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Considérations relatives au compte cible principale serveurs et du stockage

Le stockage pour chaque serveur cible maître comprend un disque du système d’exploitation, d’un volume de rétention et de disques de données. Le lecteur de rétention gère le journal des modifications de disque pour la durée de la période de rétention définie dans le portail de récupération de Site.  Reportez-vous au tableau 1 pour les propriétés de la machine virtuelle du serveur cible principale. Le tableau 3 montre comment les disques de A4 sont utilisés.

**Instance** | **Disque du système d’exploitation** | **Rétention** | **Disques de données**
--- | --- | --- | ---
 | | **Rétention** | **Disques de données**
A4 standard | 1 disque (1 * 1 023 Go) | 1 disque (1 * 1 023 Go) | 15 disques (Go 15 * 1023)
D14 standard |  1 disque (1 * 1 023 Go) | 1 disque (1 * 1 023 Go) | 31 disques (Go 15 * 1023)
DS4 standard |  1 disque (1 * 1 023 Go) | 1 disque (1 * 1 023 Go) | 15 disques (Go 15 * 1023)

**Tableau 3**

Dépendant de la planification des capacités pour le serveur cible principale :

- Limitations et les performances du stockage azure
    - Le nombre maximal de très utilisés des disques d’un ordinateur virtuel de couche Standard, environ 40 (20 000/500 IOPS par disque) dans un compte de stockage unique. Lire sur [les cibles de l’évolutivité de stockage standard sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) et de [sccounts de stockage de prime](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Taux de modification quotidien 
-   Stockage du volume de rétention.

Notez que :

- Une source ne peut pas s’étendre sur plusieurs comptes de stockage. Cela s’applique sur le disque de données vers les comptes de stockage sélectionnés lors de la configuration de protection. Le système d’exploitation et les disques de rétention généralement atteindre le compte de stockage déployé automatiquement.
- Le volume de stockage de rétention nécessaire dépend du taux de modification quotidien et le nombre de jours de rétention. Le stockage de rétention requis par le serveur maître cible = évolution totale du code à partir de la source par jour * nombre de jours de rétention. 
- Chaque serveur cible principale n'a qu’un seul volume de rétention. Le volume de rétention est partagé entre les disques reliés au serveur cible principale. Par exemple :
    - S’il existe une machine source avec 5 disques, et chaque disque génère 120 IOPS (8 Ko la taille) dans la source, cela se traduit à 240 Ops ES/s par disque (2 opérations sur le disque cible par source d’e/s). Ops ES/240 s est dans l’Azure par la limite d’e/s de disque de 500.
    - Sur le volume de rétention, cela devient 120 * 5 = 600 IOPS et cela peuvent devenir un goulot de la bouteille. Dans ce scénario, une bonne stratégie serait d’ajouter des disques au volume de rétention et s’étendent sur, sous la forme d’une configuration d’agrégats par bandes RAID. Cela améliorera les performances car les e/s sont réparties sur plusieurs disques. Le nombre de lecteurs à ajouter au volume de rétention sera comme suit :
        - Nombre total d’e/s à partir de l’environnement source / 500
        - Évolution totale du code par jour à partir de l’environnement source (sans compression) / 287 go. 287 Go est le débit maximal pris en charge par un disque cible par jour. Cette mesure peut varier en fonction de la taille d’écriture avec un facteur de 8K, car dans ce cas 8K est thé supposé taille d’écriture. Par exemple, si la taille d’écriture est de 4 Ko débit sera 287/2. Et si la taille d’écriture est de 16 Ko, le débit sera 287 * 2.
- Le nombre de comptes de stockage requises = total source Ops ES/s/10000.


## <a name="before-you-start"></a>Avant de commencer

**Composant** | **Configuration requise** | **Détails**
--- | --- | --- 
**Compte Azure** | Vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
**Stockage Azure** | Vous devez posséder un compte de stockage Azure pour stocker des données répliquées<br/><br/> Le compte doit être un [Compte de stockage redondant Geo Standard](../storage/storage-redundancy.md#geo-redundant-storage) ou d’un [Compte de stockage Premium](../storage/storage-premium-storage.md).<br/><br/> Il doit dans la même région, comme le service de récupération de Site Azure et être associé à l’abonnement de même. Nous ne supportent pas le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) entre des groupes de ressources.<br/><br/> Pour en savoir plus lire [l’Introduction au stockage Azure de Microsoft](../storage/storage-introduction.md)
**Azure réseau virtuel** | Vous aurez besoin d’un réseau virtuel Azure sur lequel le serveur de configuration et d’un serveur cible principale seront déployés. Il doit être dans le même abonnement et région comme le coffre-fort de la récupération de Site Azure. Si vous souhaitez répliquer les données sur une connexion VPN ou de ExpressRoute le réseau virtuel Azure doit être connecté à votre réseau local via une connexion ExpressRoute ou un VPN de Site à Site.
**Ressources Azure** | Assurez-vous que vous disposez de suffisamment de ressources Azure pour déployer tous les composants. Lire en plusieurs [Limites d’abonnement Azure](../azure-subscription-service-limits.md).
**Machines virtuelles Azure** | Ordinateurs virtuels que vous souhaitez protéger doivent être conformes avec [conditions préalables Azure](site-recovery-best-practices.md).<br/><br/> **Nombre de disques**: 31 disques au maximum peuvent être pris en charge sur un seul serveur protégé<br/><br/> **Les tailles de disque**: disque individuel ne doit pas être supérieure à 1 023 Go<br/><br/> **Gestion de clusters**, ordonné en clusters les serveurs ne sont pas pris en charge.<br/><br/> **Démarrage**: unifié Extensible Firmware Interface (UEFI) Extensible Firmware Interface(EFI) démarrage n’est pas pris en charge.<br/><br/> **Volumes**, Bitlocker volumes cryptés ne sont pas pris en charge.<br/><br/> **Les noms de serveur**, les noms doivent contenir entre 1 et 63 des caractères (lettres, chiffres et traits d’union). Le nom doit commencer par une lettre ou un nombre et se terminer par une lettre ou un chiffre. Une fois une machine est protégée, vous pouvez modifier le nom Azure.
**Serveur de configuration** | Machine de virtuelle A3 standard basé sur une image de la galerie Azure Site récupération Windows Server 2012 R2 sera créé dans votre abonnement pour le serveur de configuration. Il est créé en tant que la première instance d’un service en nuage. Si vous sélectionnez Internet publiques comme le type de connexion pour le serveur de configuration du service de cloud sera créé avec l’adresse IP publique réservée.<br/><br/> Le chemin d’installation doit être en caractères anglais uniquement.
**Serveur cible principale** | Machine virtuelle Azure, standard A4, D14 ou DS4.<br/><br/> Le chemin d’installation doit être en caractères anglais uniquement. Par exemple, le chemin d’accès doit être **/usr/local/ASR** pour un serveur maître cible sous Linux.
**Serveur de traitement** | Vous pouvez déployer le serveur de traitement sur l’ordinateur physique ou virtuel Windows Server 2012 R2 en cours d’exécution avec les dernières mises à jour. Installer sur C: /.<br/><br/> Nous vous recommandons de que vous placez le serveur sur le même réseau et sous-réseau que les ordinateurs que vous souhaitez protéger.<br/><br/> Installez VMware vSphere CLI 5.5.0 sur le serveur de traitement. Le composant CLI VMware vSphere est requis sur le serveur de processus afin de découvrir les ordinateurs virtuels gérés par un serveur vCenter ou les machines virtuelles s’exécutant sur un hôte ESXi.<br/><br/> Le chemin d’installation doit être en caractères anglais uniquement.<br/><br/> Système de fichiers (Refs) n’est pas pris en charge.
**VMware** | Serveur VMware vCenter gérer votre vSphere les hyperviseurs VMware. Il doit être exécuté vCenter version 5.1 ou 5.5 avec les dernières mises à jour.<br/><br/> Un ou plusieurs hyperviseurs vSphere contenant des machines virtuelles VMware que vous souhaitez protéger. L’hyperviseur doit être en cours d’exécution ESX/ESXi version 5.1 ou 5.5 avec les dernières mises à jour.<br/><br/> Les machines virtuelles VMware doivent avoir les outils VMware installé et en cours d’exécution. 
**Ordinateurs Windows** | Protégés des serveurs physiques ou des machines virtuelles VMware Windows en cours d’exécution ont un certain nombre d’exigences.<br/><br/> Une prise en charge de système d’exploitation 64 bits : **Windows Server 2012 R2**, **Windows Server 2012**, ou **de Windows Server 2008 R2 avec au moins SP1**.<br/><br/> Le nom d’hôte, les points de montage, les noms de périphériques, les chemin d’accès du système de Windows (par ex : C:\Windows) doit exister en anglais uniquement.<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\.<br/><br/> Seuls les disques de base sont pris en charge. Les disques dynamiques ne sont pas pris en charge.<br/><br/> Règles de pare-feu sur les ordinateurs protégés doivent leur permettre d’atteindre les serveurs cibles de configuration et maître dans Azure.p ><p>Vous devez fournir un compte d’administrateur (doit être d’un administrateur local sur l’ordinateur Windows) à l’installation « push » du Service de la mobilité sur des serveurs Windows. Si le compte fourni est un compte de domaine, vous devez désactiver le contrôle de l’accès utilisateur à distance sur l’ordinateur local. Pour faire cela d’ajouter l’entrée de Registre LocalAccountTokenFilterPolicy DWORD avec la valeur 1 sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Pour ajouter l’entrée de Registre d’une cmd ouvert du CLI ou les powershell et entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [En savoir plus](https://msdn.microsoft.com/library/aa826699.aspx) à propos du contrôle d’accès.<br/><br/> Après le basculement, si vous souhaitez vous connecter à des ordinateurs virtuels Windows Azure avec Bureau à distance Assurez-vous que Bureau à distance est activé pour l’ordinateur local. Si vous vous connectez pas via un VPN, les règles de pare-feu doivent permettre des connexions Bureau à distance sur internet.
**Ordinateurs Linux** | Un système d’exploitation de prise en charge 64 bits : **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux versions 6.4 et 6.5 exécutant incassable entreprise noyau version 3 (UEK3) ou noyau compatible de Red Hat**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Règles de pare-feu sur les ordinateurs protégés devraient leur permettre d’accéder à la configuration et les serveurs cible principal dans Azure.<br/><br/> les fichiers/etc/hosts sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local pour les adresses IP associées à toutes les cartes réseau <br/><br/> Si vous souhaitez vous connecter à un ordinateur virtuel Azure exécutant Linux après le basculement à l’aide d’un SSH client (ssh), assurez-vous que le service de Shell sécurisé sur l’ordinateur protégé est défini pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent un ssh connexion à ce dernier.<br/><br/> Nom d’hôte, les points de montage, les noms de périphériques et chemins d’accès de système de Linux et les noms de fichier (par exemple, / etc /, / usr) doivent exister en anglais uniquement.<br/><br/> Protection peut être activée pour les ordinateurs locaux avec le stockage suivant :-<br>Système de fichiers : EXT3, ETX4, ReiserFS, XFS<br>Périphérique logiciel MPIO (multipath) le Mappeur<br>Le Gestionnaire de volumes : LVM2<br>Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge.
**Tiers** | Certains composants dans ce scénario de déploiement dépendent d’un logiciel tiers pour fonctionner correctement. Pour une liste complète, consultez [informations et avis de logiciels tiers](#third-party)


### <a name="network-connectivity"></a>Connectivité réseau

Vous disposez de deux options lors de la configuration de la connectivité réseau entre le site local et le réseau virtuel Azure sur lequel les composants d’infrastructure (serveur de configuration, les serveurs cibles maître) sont déployés. Vous devez choisir des options de connectivité de réseau à utiliser avant de pouvoir déployer votre serveur de configuration. Vous devez choisir ce paramètre au moment du déploiement. Il ne peut pas être modifié ultérieurement.

**Internet :** Communication et la réplication de données entre les serveurs locaux (serveur de traitement, machines protégées) et les serveurs de composant de l’infrastructure Azure (serveur de configuration, serveur maître cible) se produit sur une connexion SSL/TLS sécurisée sur site pour les points de terminaison publics sur les serveurs cibles de configuration et le masque. (La seule exception est la connexion entre le serveur et le serveur cible principal sur le port TCP 9080 n’est pas crypté. Seules les informations de contrôle relatives au protocole de réplication pour la configuration de réplication sont échangées sur cette connexion.)

![Diagramme de déploiement internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: Communication et la réplication de données entre les serveurs locaux (serveur de traitement, machines protégées) et les serveurs de composant de l’infrastructure Azure (serveur de configuration, serveur maître cible) se produit sur une connexion VPN entre votre réseau local et le réseau virtuel Azure sur lequel le serveur de configuration et de serveurs cibles maître sont déployés. Assurez-vous que votre réseau local est connecté au réseau virtuel Azure par une connexion de ExpressRoute ou d’une connexion VPN de site à site.

![Diagramme de déploiement VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Étape 1 : Création d’un coffre-fort

1. Connectez-vous au [portail de gestion](https://portal.azure.com).


2. Développez **Services de données** > **Services de récupération** et cliquez sur le **Coffre de récupération de Site**.


3. Cliquez sur **créer une nouvelle** > **Création rapide**.

4. Dans la zone **nom**, entrez un nom convivial pour identifier le coffre-fort.

5. Dans la **zone**, sélectionnez la zone géographique pour la chambre forte. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Cliquez sur **créer coffre-fort**.

    ![Nouveau coffre-fort](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Vérifiez la barre d’état pour confirmer que le coffre-fort a été créé avec succès. Le coffre-fort apparaît comme **actif** sur la page principale **Des Services de récupération** .

## <a name="step-2-deploy-a-configuration-server"></a>Étape 2 : Déployer un serveur de configuration

### <a name="configure-server-settings"></a>Configurer les paramètres du serveur

1. Dans la page **Services de restauration** , cliquez sur le coffre pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Dans la liste déroulante, sélectionnez **entre un site local avec les serveurs VMware/physiques et Azure**.
3. De **Préparer les ressources Target(Azure)** cliquez sur **Déployer un serveur de Configuration**.

    ![Déployer le serveur de configuration](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. **Nouvelles informations de serveur de Configuration** de spécifier :

    - Un nom pour le serveur de configuration et les informations d’identification pour vous connecter à.
    - Dans le type de connectivité réseau liste déroulante Sélectionnez **Internet Public** ou **VPN**. Notez que vous ne pouvez pas modifier ce paramètre lorsqu’il est appliqué.
    - Sélectionnez le réseau Azure sur lequel le serveur doit être localisé. Si vous utilisez la création VPN que le réseau Azure est toujours connecté à votre réseau local comme prévu. 
    - Spécifiez l’adresse IP interne et le sous-réseau qui sera affecté au serveur. Notez que les quatre premières adresses IP de tous les sous-réseaux sont réservées pour un usage interne Azure. Utilisez une autre adresse IP disponible.
    
    ![Déployer le serveur de configuration](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Lorsque vous cliquez sur **OK** une VM A3 standard basé sur une image de la galerie Azure Site récupération Windows Server 2012 R2 sera créé dans votre abonnement pour le serveur de configuration. Il est créé en tant que la première instance d’un service en nuage. Si vous avez sélectionné se pour connecter via internet, le service en nuage est créé avec l’adresse IP publique réservée. Vous pouvez surveiller la progression dans l’onglet **tâches** .

    ![Surveiller la progression](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Si vous vous connectez via internet, une fois le serveur de configuration déployé Remarque l’adresse IP publique lui est assigné dans la page **ordinateurs virtuels** dans le portail Azure. Puis sur les **points de terminaison** Remarque d’onglet le port HTTPS public mappé au port privé 443. Vous aurez besoin de ces informations ultérieurement lorsque vous enregistrez la cible principale et les serveurs de traitement avec le serveur de configuration. Le serveur de configuration est déployé avec ces points de terminaison :

    - HTTPS : Un port public est utilisé pour coordonner la communication entre les serveurs de composants et d’Azure via internet. Port privé 443 est utilisé pour coordonner la communication entre les serveurs de composants et d’Azure via VPN.
    - Personnalisé : Un port public est utilisé pour la communication des outils de restauration sur internet. Port privé 9443 est utilisé pour la communication des outils de restauration sur VPN.
    - PowerShell : Port privé 5986
    - Bureau à distance : le port 3389 privé
    
    ![Points de terminaison de machine virtuelle](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Ne pas supprimer ou modifier le numéro de port publics ou privés des points de terminaison créés au cours du déploiement de serveur de configuration.

Le serveur de configuration est déployé dans un service cloud d’Azure créée automatiquement avec une adresse IP réservée. L’adresse réservée est nécessaire pour s’assurer que l’adresse IP de configuration serveur cloud service reste identique entre les redémarrages des ordinateurs virtuels (y compris le serveur de configuration) sur le service en nuage. L’adresse IP publique réservée il faudra manuellement non réservés lorsque le serveur de configuration est désactivé ou qu’il vous reste réservée. Il existe une limite par défaut de 20 adresses IP publiques réservées par abonnement. [En savoir plus](../virtual-network/virtual-networks-reserved-private-ip.md) sur les adresses IP réservées. 

### <a name="register-the-configuration-server-in-the-vault"></a>Inscrire le serveur de configuration dans le coffre-fort

1. Dans la page de **Démarrage rapide** , cliquez sur **Préparer les ressources cible** > **télécharger une clé d’enregistrement**. Le fichier de clé est généré automatiquement. Il est valable 5 jours après sa création. Copiez-le sur le serveur de configuration.
2. Dans des **Machines virtuelles** sélectionnez le serveur de configuration à partir de la liste des ordinateurs virtuels. Ouvrez l’onglet **tableau de bord** , puis cliquez sur **se connecter**. **Ouvrir** le fichier téléchargé RDP pour se connecter au serveur de configuration à l’aide du Bureau à distance. Si vous utilisez un VPN, utilisez l’adresse IP interne (l’adresse que vous avez spécifié lorsque vous avez déployé le serveur de configuration) pour une connexion Bureau à distance à partir du site local. L’Assistant d’installation de serveur de Configuration Azure Site récupération s’exécute automatiquement lorsque vous ouvrez une session pour la première fois.

    ![Inscription](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. Dans **Installation de logiciels tiers** , cliquez sur **J’accepte** pour télécharger et installer MySQL.

    ![Installation de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. **Détails relatifs au serveur MySQL** de créer les informations d’identification pour vous connecter à l’instance de serveur MySQL.

    ![Informations d’identification de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Dans **Les paramètres Internet** , spécifiez comment le serveur de configuration se connectera à internet. Notez que :

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur.
    - Lorsque vous cliquez sur **suivant** , un test s’exécute pour vérifier la connexion du proxy.
    - Si vous utilisez un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez saisir les détails de proxy, y compris les informations d’identification, le port et l’adresse.
    - Les URL suivantes doivent être accessibles via le serveur proxy :
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Si vous avez basé sur l’adresse IP des règles de pare-feu s’assurer que les règles sont définies pour permettre la communication entre le serveur de configuration aux adresses IP décrit dans des [Plages d’IP Azure Datacenter](https://msdn.microsoft.com/library/azure/dn175718.aspx) et HTTPS protocole (443). Vous seriez obligé de plages IP de liste blanche de la région Azure que vous prévoyez d’utiliser et de l’ouest des États-Unis.

    ![Inscription de proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. Dans les **Paramètres de localisation de Message d’erreur fournisseur de** spécifier la langue dans laquelle vous souhaitez que les messages d’erreur s’affiche.

    ![Enregistrement de message d’erreur](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. Enregistrement de **récupération** de Site Azure Parcourir et sélectionnez le fichier de clé que vous avez copié sur le serveur.

    ![Enregistrement du fichier de clé](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Sur la dernière page de l’Assistant, sélectionnez ces options :

    - Sélectionnez **Lancer un dialogue de gestion de compte** pour spécifier que la boîte de dialogue Gérer les comptes doit s’ouvrir lorsque vous avez terminé l’Assistant.
    - Sélectionnez **créer une icône de bureau pour Cspsconfigtool** pour ajouter un raccourci sur le bureau sur le serveur de configuration de sorte que vous pouvez ouvrir la boîte de dialogue **Gérer les comptes** à tout moment sans avoir à réexécuter l’Assistant.

    ![Terminer l’inscription](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Cliquez sur **Terminer** pour terminer l’Assistant. Une phrase de passe est généré. Copier dans un emplacement sécurisé. Vous aurez besoin d’authentifier et d’enregistrer le processus et les serveurs cibles maître avec le serveur de configuration. Il est également utilisé pour garantir l’intégrité des canaux de communications de serveur de configuration. Vous pouvez régénérer le mot de passe, mais vous devrez puis réenregistrer la cible principale et traiter des serveurs en utilisant le nouveau mot de passe.

    ![Mot de passe](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Après l’enregistrement du serveur de configuration apparaît sur la page de **Configuration des serveurs** dans la chambre forte.

### <a name="set-up-and-manage-accounts"></a>Configurer et gérer les comptes

Au cours du déploiement récupération de Site demande des informations d’identification pour les actions suivantes :

- Un compte de VMware pour thatSite restauration puisse automatiquement VMs de découverte sur serveurs vCenter ou vSphere hôtes. 
- Lorsque vous ajoutez des machines pour la protection, récupération de Site permettant d’installer le service de la mobilité sur ceux-ci.

Une fois que vous avez enregistré le serveur de configuration, vous pouvez ouvrir la boîte de dialogue **Gérer les comptes** pour ajouter et gérer des comptes qui doivent être utilisés pour ces actions. Il existe deux façons de procéder :

- Ouvrez le raccourci que vous avez choisi de créer la boîte de dialogue sur la dernière page du programme d’installation pour le serveur de configuration (cspsconfigtool).
- Ouvrir la boîte de dialogue fin de l’installation de serveur de configuration.

1. Dans **Gestion des comptes** , cliquez sur **Ajouter un compte**. Vous pouvez également modifier et supprimer les comptes existants.

    ![Gérer les comptes](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. Dans **Détail du compte** , spécifiez un nom de compte à utiliser dans Azure et informations d’identification (nom de domaine/utilisateur). 

    ![Gérer les comptes](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Se connecter au serveur de configuration 

Il existe deux façons de se connecter au serveur de configuration :

- Sur un VPN de site à site ou ExpressRoute
- Sur internet 

Notez que :

- Une connexion internet utilise les points de terminaison de la machine virtuelle en association avec l’adresse IP publique virtuelle du serveur.
- Une connexion VPN utilise l’adresse IP interne du serveur et les ports privé de point de terminaison.
- Il est une décision unique pour décider si vous souhaitez vous connecter (contrôle et la réplication des données) à partir de vos serveurs sur site pour les différents serveurs de composant (serveur de configuration, serveur maître cible) en cours d’exécution dans Azure via une connexion VPN ou d’internet. Vous ne pouvez pas modifier ce paramètre par la suite. Si vous le faites, vous devrez redéployer le scénario et de restaurer vos ordinateurs.  


## <a name="step-3-deploy-the-master-target-server"></a>Étape 3 : Déployer le serveur cible principale

1. Cliquez sur **Préparer les ressources Target(Azure)** > **serveur maître cible de déploiement**.
2. Spécifiez les détails du serveur maître cible et les informations d’identification. Le serveur sera déployé dans le même réseau que le serveur de configuration Azure. Lorsque vous cliquez sur Terminer une machine virtuelle Azure sera créé avec une photo de la galerie Windows ou Linux.

    ![Paramètres du serveur cible](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Notez que les quatre premières adresses IP de tous les sous-réseaux sont réservées pour un usage interne Azure. Spécifiez une autre adresse IP disponible.

>[AZURE.NOTE] Sélectionnez DS4 Standard lors de la configuration de la protection des charges de travail qui nécessitent des performances d’e/s élevées et une faible latence pour héberger les charges de travail intensives d’e/s à l’aide du [Compte de stockage Premium](../storage/storage-premium-storage.md).


3. Maître de Windows serveur cible virtuelle est créée avec ces points de terminaison. Notez que les points de terminaison publics sont créées uniquement si votre connexion sur internet.

    - Personnalisé : Port Public utilisé par le serveur de traitement pour envoyer des données de la réplication sur internet. Port privé 9443 est utilisé par le serveur de traitement pour envoyer des données de réplication sur le serveur cible principal sur VPN.
    - Custom1 : Port Public utilisé par le serveur de traitement pour envoyer des métadonnées via internet. Port privé 9080 est utilisé par le serveur de traitement pour envoyer des métadonnées sur le serveur cible principale via VPN.
    - PowerShell : Port privé 5986
    - Bureau à distance : le port 3389 privé

4. Un serveur maître cible de Linux VM est créé avec ces points de terminaison. Notez que les points de terminaison publics sont créées uniquement si vous vous connectez via internet.

    - Personnalisé : Port Public utilisé par le serveur de traitement pour envoyer des données de la réplication sur internet. Port privé 9443 est utilisé par le serveur de traitement pour envoyer des données de réplication sur le serveur cible principal sur VPN.
    - Custom1 : Port Public est utilisé par le serveur de traitement pour envoyer des métadonnées via internet. Port privé 9080 est utilisé par le serveur de traitement pour envoyer des métadonnées sur le serveur cible principale via VPN
    - SSH : Port privé 22

    >[AZURE.WARNING] Ne pas supprimer ou modifier le numéro de port publics ou privés d’un points de terminaison créés lors du déploiement de serveur maître cible.

5. Dans l’attente de **Machines virtuelles** pour démarrer l’ordinateur virtuel.

    - S’il s’agit d’une note de serveur Windows vers le bas les détails de bureau à distance.
    - Si il s’agit d’un serveur Linux et que vous vous connectez via un VPN, notez l’adresse IP interne de l’ordinateur virtuel. Si vous vous connectez sur internet, notez l’adresse IP publique.

6.  Ouvrez une session sur le serveur pour terminer l’installation et l’inscrire auprès du serveur de configuration. 
7.  Si vous utilisez Windows :

    1. Lancer une connexion Bureau à distance sur l’ordinateur virtuel. La première fois que vous ouvrez une session sur un script s’exécute dans une fenêtre PowerShell. Ne fermez pas il. Une fois l’outil de configuration de l’Agent hôte s’ouvre automatiquement pour inscrire le serveur.
    2. Dans la **Configuration de l’Agent hôte** , spécifiez l’adresse IP interne du serveur de configuration et le port 443. Vous pouvez utiliser les adresses internes et privé port 443 même si vous ne vous connectez pas via un VPN, car l’ordinateur virtuel est connecté au même réseau que le serveur de configuration Azure. Laisser **Utiliser HTTPS** est activée. Entrez le mot de passe pour le serveur de configuration que vous avez notée précédemment. Cliquez sur **OK** pour enregistrer le serveur. Vous pouvez ignorer les options de NAT. Ils ne sont pas utilisés.
    3. Si votre lecteur rétention estimée est supérieure à 1 To, vous pouvez configurer le volume de rétention (r) à l’aide d’un disque virtuel et les [espaces de stockage](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Serveur cible principal de Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Si vous êtes sous Linux :
    1. Assurez-vous que vous avez installé la dernière Linux Integration Services (LIS) installé avant d’installer le serveur de la cible principale. Vous pouvez trouver la dernière version de LIS, ainsi que des instructions sur la façon d’installer [ici](https://www.microsoft.com/download/details.aspx?id=46842). Redémarrez l’ordinateur après l’installation de LIS.
    2. Dans les **ressources de la cible de préparer (Azure)** , cliquez sur **télécharger et installer des logiciels supplémentaires (uniquement pour le serveur maître de Linux cible)**. Copiez le fichier tar téléchargé sur l’ordinateur virtuel à l’aide d’un client sftp. Ou bien vous pouvez ouvrir une session sur le serveur cible principale de linux déployés et utiliser *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* pour télécharger le fichier.
    2. Ouvrez une session sur le serveur à l’aide d’un client Secure Shell. Si vous êtes connecté au réseau Azure sur VPN utilisent l’adresse IP interne. Sinon, utilisez l’adresse IP externe et le point de terminaison publique SSH.
    3. Extrayez les fichiers en exécutant le programme d’installation compressés : **tar-xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![serveur cible principale de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Vérifiez que vous êtes dans le répertoire dans lequel vous avez extrait le contenu du fichier tar.
    5. Copier le mot de passe de serveur de configuration dans un fichier local à l’aide de la commande * *echo* `<passphrase>` * > passphrase.txt**
    6. Exécutez la commande "**sudo. /Install -t les deux - a -R l’hôte MasterTarget des /usr/local/ASR -d -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt** ».

    ![Inscrire un serveur cible](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Attendez quelques minutes (10-15) et sur la page, vérifiez que le serveur cible principal est répertorié comme enregistré sur les **serveurs** > onglet **Détails du serveur** de**Configuration de serveurs** . Si vous êtes sous Linux, et il n’a pas inscrit réexécuter l’hôte outil de configuration à partir de /usr/local/ASR/Vx/bin/hostconfigcli. Vous devez définir des autorisations d’accès en exécutant chmod en tant que racine.

    ![Vérifiez le serveur cible](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Il peut prendre jusqu'à 15 minutes après que l’inscription est terminée pour le serveur de la cible principale de figurer dans le portail. Pour mettre à jour immédiatement, cliquez sur **Actualiser** sur la page de **Configuration des serveurs** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Étape 4 : Déployer le serveur de traitement local

Avant de commencer, nous vous recommandons de configurer une adresse IP statique sur le serveur de processus afin qu’il a la garantie d’être persistante entre les redémarrages.

1. Cliquez sur démarrage rapide > **installer le processus serveur local** > **télécharger et installer le serveur de traitement**.

    ![Installer le serveur de traitement](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copiez le fichier zip téléchargé sur le serveur sur lequel vous allez installer le serveur de traitement. Le fichier zip contient deux fichiers d’installation :

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Décompressez l’archive et copier les fichiers d’installation vers un emplacement sur le serveur.
4. Exécuter le **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** installation de fichier et suivez les instructions. Cette procédure installe les composants tiers requis pour le déploiement.
5. Puis exécutez **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Dans la page **Mode de serveur** , sélectionnez **Serveur de traitement**.
7. Sur la page de **Détails de l’environnement** , effectuez le des opérations suivantes :


    - Si vous souhaitez protéger VMware virtual machines cliquez sur **Oui**
    - Si vous souhaitez uniquement protéger les serveurs physiques et que vous ne devez donc vCLI VMware installé sur le serveur de traitement. Cliquez sur **non** et continuer.

8. Notez les points suivants lors de l’installation de VMware vCLI :

    - **Seul VMware vSphere CLI 5.5.0 est pris en charge**. Le serveur ne fonctionne pas avec les autres versions ou des mises à jour de vSphere CLI.
    - Télécharger vSphere CLI 5.5.0 de [ici.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Si vous avez installé vSphere CLI juste avant le démarrage de l’installation du serveur de traitement, et le programme d’installation ne détecte pas, attendez cinq minutes avant d’essayer à nouveau le programme d’installation. Cela garantit que toutes les variables d’environnement nécessaires pour la détection de CLI vSphere ont été correctement initialisées.

9.  Dans **Sélection de carte réseau pour serveur de traitement** , sélectionnez la carte réseau que le serveur de traitement doit utiliser.

    ![Sélectionner la carte](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. Dans les **Détails de Configuration serveur**:

    - Pour l’adresse IP et le port, si vous vous connectez via un VPN, spécifiez l’adresse IP interne du serveur configuration et 443 pour le port. Sinon, spécifiez l’adresse IP publique virtuelle et mappé point de terminaison HTTP public.
    - Tapez le mot de passe du serveur de configuration.
    - Désactivez la **signature de logiciel de service de mobilité de vérifier** si vous souhaitez désactiver la vérification lors de la transmission automatique vous permet d’installer le service. Vérification de la signature a besoin de la connectivité internet à partir du serveur de traitement.
    - Cliquez sur **suivant**.

    ![Serveur de configuration de Registre](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. Dans le **lecteur d’Installation de sélectionner** permet de sélectionner un lecteur de cache. Le serveur a besoin d’un lecteur de cache au moins 600 Go d’espace libre. Puis cliquez sur **installer**. 

    ![Serveur de configuration de Registre](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Notez que vous devrez peut-être redémarrer le serveur pour terminer l’installation. Dans le **Serveur de Configuration** > vérifier les**Détails du serveur** que le serveur de traitement apparaît et qu’il a été enregistré dans la chambre forte.

>[AZURE.NOTE]Il peut prendre jusqu'à 15 minutes après que l’inscription est terminée pour le serveur de processus s’affiche sous le serveur de configuration. Pour mettre à jour immédiatement, actualiser le serveur de configuration en cliquant sur le bouton Actualiser au bas de la page configuration du serveur
 
![Valider le processus serveur](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Si vous n’avez pas désactiver la vérification de signature pour le service de la mobilité lors de l’enregistrement du serveur de traitement vous pouvez le faire ultérieurement comme suit :

1. Ouvrez une session sur le serveur de traitement en tant qu’administrateur et ouvrez le fichier C:\pushinstallsvc\pushinstaller.conf pour l’édition. Ajoutez la ligne suivante sous la section **[PushInstaller.transport]** : **SignatureVerificationChecks = « 0 »**. Enregistrez et fermez le fichier.
2. Redémarrez le service InMage PushInstall.


## <a name="step-5-update-site-recovery-components"></a>Étape 5 : Composants de restauration du Site de mise à jour

Composants de restauration de site sont mises à jour de temps en temps. Lorsque de nouvelles mises à jour sont disponibles, vous devez les installer dans l’ordre suivant :

1. Serveur de configuration
2. Serveur de traitement
3. Serveur cible principale
4. Outil de restauration (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Obtenir et installer les mises à jour


1. Vous pouvez obtenir des mises à jour pour les serveurs cibles maître, des processus et configuration de la récupération de Site **tableau de bord**. Pour l’installation de Linux, extrayez les fichiers dans le programme d’installation compressés et exécutez la commande « sudo. / install » pour installer la mise à jour.
2. [Téléchargez](http://go.microsoft.com/fwlink/?LinkID=533813) la dernière mise à jour de la tool(vContinuum) de restauration automatique.
3. Si vous utilisez des machines virtuelles ou des serveurs physiques qui ont déjà installé le service de mobilité, vous pouvez obtenir les mises à jour pour le service comme suit :

    - **Option 1**: télécharger des mises à jour :
        - [Windows Server (64 bits uniquement)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 (64 bits uniquement)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (64 bits uniquement)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (64 bits uniquement)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Une fois le serveur de traitement de mise à jour la version mise à jour de service de la mobilité sera disponible dans le dossier C:\pushinstallsvc\repository sur le serveur de traitement.
    - **Option 2**: Si vous avez un ordinateur avec une version antérieure du service mobilité installé, vous pouvez mettre automatiquement à jour le service de la mobilité sur l’ordinateur à partir du portail de gestion.

        1. Assurez-vous que le serveur est mis à jour.
        2. Assurez-vous que l’ordinateur protégé est conforme avec les [conditions préalables](#install-the-mobility-service-automatically) pour diffuser automatiquement le service de la mobilité, afin que la mise à jour fonctionne comme prévu.
        2. Sélectionnez le groupe de protection, sélectionnez l’ordinateur protégé et cliquez sur **service de mise à jour de mobilité**. Ce bouton n’est disponible que s’il existe une version plus récente du service de la mobilité. 

            ![Sélectionnez les serveurs vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Sélectionnez les comptes spécifier le compte d’administrateur à utiliser pour mettre à jour le service de mobilité sur le serveur protégé. Cliquez sur OK et attendez la fin du travail déclenché.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Étape 6 : Ajouter des serveurs vCenter ou vSphere hôtes

1. Cliquez sur **serveurs** > **Serveurs de Configuration** > serveur de configuration >**Ajouter vCenter Server** pour ajouter un hôte de serveur ou vSphere vCenter.

    ![Sélectionnez les serveurs vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Spécifiez les détails pour le serveur ou l’hôte, sélectionnez le serveur de processus qui permettra de le détecter.

    - Si le serveur vCenter n’est pas en cours d’exécution sur le port 443 par défaut, spécifiez le numéro de port sur lequel le serveur vCenter est en cours d’exécution.
    - Le serveur de traitement doit être sur le même réseau que l’hôte de serveur/vSphere vCenter et doit avoir VMware vSphere CLI 5.5.0 installé.

    ![paramètres du serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Après la découverte du serveur vCenter sera répertorié sous les informations de serveur de configuration.

    ![paramètres du serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Si vous utilisez un compte non administrateur pour ajouter le serveur ou l’hôte, assurez-vous que le compte possède les privilèges suivants :

    - les comptes de vCenter doivent avoir Datacenter, magasin de données, dossier, hôte, réseau, ressources, stockage vues, machine virtuelle et les privilèges de commutateur de distribution vSphere activés.
    - comptes d’hôte vSphere doivent avoir le centre de données magasin de données, le dossier, hôte, réseau, ressource, machine virtuelle et les privilèges de commutateur de distribution vSphere activés



## <a name="step-7-create-a-protection-group"></a>Étape 7 : Créer un groupe de protection

1. Ouvrir les **Éléments de protection** > **Groupe de Protection** > **Créer groupe de protection**.

    ![Créer le groupe de protection](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Dans la page **Spécifier les paramètres du groupe de Protection** , spécifiez un nom pour le groupe et sélectionnez le serveur de configuration sur lequel vous souhaitez créer le groupe.

    ![Paramètres de groupe de protection](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Dans la page **Spécifier les paramètres de réplication** , configurez les paramètres de réplication qui seront utilisées pour tous les ordinateurs dans le groupe.

    ![Réplication de groupe de protection](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Paramètres :
    - **La cohérence de plusieurs VM**: Si vous activez cette option elle crée des points de partagé récupération des applications sur les ordinateurs dans le groupe de protection. Ce paramètre est plus approprié lorsque tous les ordinateurs du groupe de protection sont en cours d’exécution la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Cette commande est disponible uniquement pour les serveurs Windows.
    - **Seuil RPO**: alertes seront générées lorsque le RPO de réplication de protection continue des données dépasse la valeur de seuil RPO configurée.
    - **Rétention de point de récupération**: Spécifie la fenêtre de la rétention. Ordinateurs protégés peuvent être récupérées à tout moment dans cette fenêtre.
    - **Fréquence des snapshots cohérents au niveau application**: Spécifie la fréquence des points de récupération contenant des snapshots cohérents avec l’application doit être créées.

Vous pouvez analyser le groupe de protection qu’elles sont créées sur la page **Les éléments protégés** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Étape 8 : Configurer des ordinateurs que vous souhaitez protéger

Vous devez installer le service de la mobilité sur les machines virtuelles et des serveurs physiques que vous souhaitez protéger. Vous pouvez pour cela de deux façons :

- Distribuer et installer le service sur chaque ordinateur du serveur de traitement automatiquement.
- Installer manuellement le service. 

### <a name="install-the-mobility-service-automatically"></a>Installer le service de la mobilité automatiquement

Lorsque vous ajoutez des ordinateurs à un groupe de protection du service de la mobilité est automatiquement poussé et installé sur chaque ordinateur par le serveur de traitement. 

**Push installer automatiquement le service de la mobilité sur des serveurs Windows :** 

1. Installer les dernières mises à jour pour le serveur de traitement, comme décrit dans [étape 5 : installation des dernières mises à jour](#step-5-install-latest-updates)et assurez-vous que le serveur est disponible. 
2. Vérifiez la connectivité réseau entre l’ordinateur source et le serveur de traitement est, et que l’ordinateur source est accessible depuis le serveur de traitement.  
3. Configurer le pare-feu Windows pour autoriser les **fichiers et le partage d’imprimante** et **Windows Management Instrumentation**. Sous paramètres du pare-feu Windows, sélectionnez l’option « Autoriser une application ou une fonctionnalité via le pare-feu » et sélectionnez les applications, comme indiqué dans l’image ci-dessous. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

    ![Paramètres de pare-feu](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. Le compte utilisé pour exécuter l’installation par diffusion doit être dans le groupe Administrateurs sur l’ordinateur que vous souhaitez protéger. Ces informations d’identification sont utilisées uniquement pour l’installation par diffusion du service de la mobilité, et vous devez les fournir lorsque vous ajoutez un ordinateur à un groupe de protection.
5. Si le compte fourni n’est pas un compte de domaine, vous devez désactiver le contrôle de l’accès utilisateur à distance sur l’ordinateur local. Pour faire cela d’ajouter l’entrée de Registre LocalAccountTokenFilterPolicy DWORD avec la valeur 1 sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Pour ajouter l’entrée de Registre d’une cmd ouvert du CLI ou les powershell et entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Push installer automatiquement le service de la mobilité sur des serveurs Linux :**

1. Installer les dernières mises à jour pour le serveur de traitement, comme décrit dans [étape 5 : installation des dernières mises à jour](#step-5-install-latest-updates)et assurez-vous que le serveur est disponible.
2. Vérifiez la connectivité réseau entre l’ordinateur source et le serveur de traitement est, et que l’ordinateur source est accessible depuis le serveur de traitement.  
3. Assurez-vous que le compte est un utilisateur racine sur le serveur source Linux.
4. Assurez-vous que le fichier/etc/hosts sur le serveur Linux source contienne des entrées qui mappent le nom d’hôte local pour les adresses IP associées à toutes les cartes réseau.
5. Installation des dernière openssh, openssh-server, packages d’openssl sur l’ordinateur que vous souhaitez protéger.
6. Assurez-vous que SSH est activé et en cours d’exécution sur le port 22. 
7. Activer l’authentification de mot de passe et le sous-système SFTP dans le fichier sshd_config comme suit : 

    - un) se connecter en tant que racine.
    - b) dans le fichier /etc/ssh/sshd_config, recherchez la ligne qui commence par **PasswordAuthentication**.
    - c) ne commentez pas la ligne et remplacez la valeur « no » à « Oui ».

        ![Mobilité de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) rechercher la ligne qui commence par le sous-système et ne commentez pas la ligne.
    
        ![Mobilité de commande Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Assurez-vous que le type variant de source machine Linux est pris en charge. 
 
### <a name="install-the-mobility-service-manually"></a>Installer le service de la mobilité manuellement

Les modules logiciels permettant d’installer le service de la mobilité sont sur le serveur de traitement dans C:\pushinstallsvc\repository. Ouvrez une session sur le serveur de traitement et de copier le package d’installation approprié sur l’ordinateur source basé sur la table ci-dessous :-

| Système d’exploitation source                               | Package de service de mobilité sur le serveur de traitement                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (64 bits uniquement)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (64 bits uniquement)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Pour installer le service de la mobilité manuellement sur un serveur Windows**, effectuez les opérations suivantes :

1. Copiez le package **Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** dans le chemin d’accès du serveur processus répertorié dans le tableau ci-dessus à l’ordinateur source.
2. Installez le service de la mobilité en exécutant le fichier exécutable sur l’ordinateur source.
3. Suivez les instructions du programme d’installation.
4. Sélectionnez le rôle de **service de la mobilité** et cliquez sur **suivant**.
    
    ![Installer le service de la mobilité](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Laissez le répertoire d’installation que le chemin d’installation par défaut et cliquez sur **installer**.
6. Dans la **Configuration de l’Agent hôte** , spécifiez l’adresse IP et le port HTTPS du serveur de configuration.

    - Permet de spécifier l’adresse IP publique virtuelle et le point de terminaison HTTPS public comme le port si vous vous connectez via internet.
    - Si vous vous connectez via VPN spécifier l’adresse IP interne et 443 pour le port. Laisser **Utiliser HTTPS** est activée.

    ![Installer le service de la mobilité](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Spécifier le mot de passe de serveur de configuration et cliquez sur **OK** pour enregistrer le service de la mobilité avec le serveur de configuration.

**Pour exécuter la ligne de commande :**

1. Copier le mot de passe à partir de la CX dans le fichier « C:\connection.passphrase » sur le serveur et exécutez la commande suivante. Dans notre exemple CX i 104.40.75.37 et le port HTTPS est 62519 :

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installer le service de la mobilité manuellement sur un serveur Linux**:

1. Copiez l’archive tar approprié selon le tableau ci-dessus, le serveur de traitement sur l’ordinateur source.
2. Ouvrir un programme shell et extraire l’archive tar compressé à un chemin d’accès local en exécutant`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Créer un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar en entrant *`echo <passphrase> >passphrase.txt`* à partir du shell.
4. Installer le service de la mobilité en entrant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l’adresse IP et le port :

    - Si vous vous connectez sur le serveur de configuration via internet spécifier la configuration serveur virtuel adresse IP publique et les données de point de terminaison HTTPS publique dans `<IP address>` et `<port>`.
    - Si vous vous connectez via une connexion VPN spécifier l’adresse IP interne et 443.

**Exécuter à partir de la ligne de commande**:

1. Copier le mot de passe à partir de la CX dans le fichier « passphrase.txt » sur le serveur et exécuter des commandes de cette. Dans notre exemple CX i 104.40.75.37 et le port HTTPS est 62519 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Pour installer sur le serveur cible :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Lorsque vous ajoutez à un groupe de protection des ordinateurs qui exécutent déjà une version appropriée du service mobilité puis l’installation par diffusion est ignorée.


## <a name="step-9-enable-protection"></a>Étape 9 : Activer la protection

Pour activer la protection vous ajoutez des machines virtuelles et des serveurs physiques à un groupe de protection. Avant de commencer, notez que :

- Machines virtuelles sont découverts toutes les 15 minutes et qu’il peut prendre jusqu'à 15 minutes pour apparaître dans la récupération de Site Azure après la découverte.
- Modifications de l’environnement sur l’ordinateur virtuel (par exemple, l’installation des outils VMware) peuvent également prendre jusqu'à 15 minutes pour être mis à jour dans récupération du Site.
- Vous pouvez vérifier la dernière découverte dans le champ **Dernier CONTACT à** l’hôte de serveur/ESXi vCenter sur la page de **Configuration des serveurs** .
- Si vous avez déjà créé un groupe de protection et d’ajouter un hôte de serveur ou ESXi vCenter par la suite, elle prend 15 minutes pour le portail de récupération de Site Azure actualiser et pour les machines virtuelles figurer dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection** .
- Si vous souhaitez procéder sans délai à l’ajout d’ordinateurs au groupe de protection sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser** .
- Lorsque vous ajoutez des ordinateurs virtuels ou ordinateurs physiques dans un groupe de protection, le serveur de traitement pousse automatiquement et installe le service de la mobilité sur le serveur source si l’informatique n’est pas déjà installé.
- Pour que le mécanisme de transmission automatique fonctionne, assurez-vous que vous avez configuré vos machines protégées comme décrit à l’étape précédente.

Ajoutez les ordinateurs comme suit :

1. Cliquez sur les **éléments protégés** > **Groupe de Protection** > **Machines** > **Ajouter des ordinateurs**. Comme meilleure pratique, nous vous recommandons que les groupes de protection doivent refléter vos charges de travail afin que vous ajoutez une application spécifique au même groupe d’ordinateurs.
2. Dans **Sélectionner les ordinateurs virtuels** si vous vous protégez des serveurs physiques, dans l’Assistant **Ajouter des Machines physiques** fournir l’adresse IP et un nom convivial. Sélectionnez la famille de systèmes d’exploitation.

    ![Ajoutez le serveur de centre de V](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. Dans **Sélectionner les ordinateurs virtuels** si vous vous protégez des machines virtuelles VMware, sélectionnez un serveur vCenter qui gère vos machines virtuelles (ou l’hôte EXSi sur lequel ils sont en cours d’exécution) et puis sélectionnez les ordinateurs.

    ![Ajoutez le serveur de centre de V](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. **Spécifier** les ressources cible, sélectionnez les serveurs maître et le stockage à utiliser pour la réplication et permet d’indiquer si les paramètres doivent être utilisés pour toutes les charges de travail. Sélectionnez le [Compte de stockage Premium](../storage/storage-premium-storage.md) lors de la configuration de la protection des charges de travail qui nécessitent des performances d’e/s élevées et une faible latence pour les charges de travail intensives d’e/s de l’hôte. Si vous souhaitez utiliser un compte de stockage de la prime pour les disques de votre charge de travail, vous devez utiliser la cible principale de la série DS. Vous ne pouvez pas utiliser des disques de stockage de la prime avec la cible principale de la gamme DS series.

    >[AZURE.NOTE] Nous ne supportent pas le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) entre des groupes de ressources.

    ![vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. De **Spécifier les comptes** , sélectionnez le compte que vous souhaitez utiliser pour l’installation du service de la mobilité sur les ordinateurs protégés. Les informations d’identification de compte sont nécessaires pour l’installation automatique du service de la mobilité. Si vous ne pouvez pas sélectionner un compte Veillez vous définir un tel que décrit à l’étape 2. Notez que ce compte ne sont pas accessibles par Azure. Pour Windows server, le compte doit avoir des privilèges d’administrateur sur le serveur source. Pour Linux, le compte doit être racine.

    ![Informations d’identification de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Cliquez sur la case à cocher pour terminer d’ajouter des ordinateurs au groupe de protection et pour lancer la réplication initiale de chaque machine. Vous pouvez surveiller l’état sur la page **tâches** .

    ![Ajoutez le serveur de centre de V](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. En outre, vous pouvez surveiller l’état de protection en cliquant sur **Les éléments protégés par** > nom du groupe de protection > **Machines virtuelles** . Après la réplication initiale est terminée et que les ordinateurs sont la synchronisation des données, ils s’affichent les état **protégé** .

    ![Tâches de l’ordinateur virtuel](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Propriétés de l’ordinateur protégé de jeu

1. Une fois un ordinateur a un état de **protection** , vous pouvez configurer ses propriétés de basculement. Dans les détails de groupe de protection, sélectionnez l’ordinateur et ouvrez l’onglet **configurer** .
2. Vous pouvez modifier le nom qui sera donné à la machine dans Azure après basculement sur incident et la taille des machines virtuelles Azure. Vous pouvez également sélectionner le Azure réseau auquel l’ordinateur sera connecté après le basculement.

    > [AZURE.NOTE] [Migration de réseaux](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

    ![Définir les propriétés de la machine virtuelle](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Notez que :

- Le nom de la machine Azure doit satisfaire aux exigences Azure.
- Par défaut les machines virtuelles dupliquées dans Azure ne sont pas connectés à un réseau d’Azure. Si vous souhaitez répliquer les machines virtuelles de communiquer Assurez-vous que définir le même réseau Azure pour eux.
- Si vous redimensionnez un volume sur une machine virtuelle VMware ou d’un serveur physique, il est dans un état critique. Si vous n’avez pas besoin de modifier la taille, effectuez le des opérations suivantes :

    - un) de modifier le paramètre de taille.
    - b) dans l’onglet **ordinateurs virtuels** , sélectionnez l’ordinateur virtuel et cliquez sur **Supprimer**.
    - c) de **Supprimer un ordinateur virtuel** , sélectionnez l’option **désactiver la protection (utilisé pour la récupération à effectuer un zoom avant et volume redimensionner)**. Cette option désactive la protection mais conserve les points de récupération dans Azure.

        ![Définir les propriétés de la machine virtuelle](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) réactiver la protection de l’ordinateur virtuel. Lorsque vous réactivez la protection des données pour le volume redimensionnée sera en Azure.

    

## <a name="step-10-run-a-failover"></a>Étape 10 : Exécution d’un basculement sur incident

Vous ne pouvez actuellement exécuter basculement non planifié pour les machines virtuelles VMware protégés et des serveurs physiques. Notez les points suivants :



- Avant d’initier un basculement, assurez-vous que les serveurs cibles de configuration et le masque sont en cours d’exécution et en bon état. Basculement échoue dans le cas contraire.
- Machines d’origine ne sont pas arrêtés dans le cadre d’un basculement non planifié. Exécution d’un basculement non planifié arrête la réplication des données pour les serveurs protégés. Vous devez supprimer les ordinateurs du groupe de protection et de les ajouter de nouveau pour commencer à protéger les ordinateurs à nouveau après que le basculement non planifié est terminée.
- Si vous souhaitez basculer sans perte de données, assurez-vous que les ordinateurs virtuels de site principal sont mis hors tension avant de lancer le basculement.

1. Sur les **Plans de** la page et ajouter un plan de récupération. Spécifiez les détails du plan et sélectionnez **Azure** comme cible.

    ![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. Dans **Sélectionner l’ordinateur virtuel** sélectionnez un groupe de protection et sélectionnez ordinateurs dans le groupe à ajouter au plan de récupération. [Pour en savoir plus](site-recovery-create-recovery-plans.md) sur les plans de reprise.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Si nécessaire, vous pouvez personnaliser le plan pour créer des groupes et la séquence de l’ordre dans les machines dans la restauration plan est basculé. Vous pouvez également ajouter des invites pour les scripts et les actions manuelles. Les scripts lorsque vous restaurez vers Azure peuvent être ajoutés à l’aide de [Procédures opérationnelles de Automation Azure](site-recovery-runbook-automation.md).

4. Dans la page des **Plans de reprise** permet de sélectionner le plan, puis cliquez sur le **Basculement non planifié**.
5. Dans **Confirmer le basculement** , vérifiez le sens de basculement (sur Azure), puis sélectionnez le point de récupération pour basculer vers.
6. Attendez que la tâche de basculement se termine et puis vérifiez que le basculement sur incident a fonctionné comme prévu et que les machines virtuelles dupliquées démarrer avec succès dans Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Étape 11 : Échec précédent a échoué sur des ordinateurs à partir d’Azure

[En savoir plus](site-recovery-failback-azure-to-vmware-classic-legacy.md) sur comment mettre votre a échoué sur des ordinateurs en cours d’exécution Azure revenir à votre environnement sur site.


## <a name="manage-your-process-servers"></a>Gérer vos serveurs de traitement

Le serveur envoie des données de réplication sur le serveur cible principal dans Azure et découvre de nouvelles machines virtuelles de VMware ajoutés à un serveur vCenter. Dans les circonstances suivantes, vous souhaiterez modifier le serveur de traitement dans votre déploiement :

- Si le serveur en cours de processus tombe en panne
- Si votre objectif de point de récupération (RPO) augmente jusqu'à un niveau inacceptable de votre organisation.

Si nécessaire vous pouvez déplacer la réplication de tout ou partie de vos locaux des machines virtuelles VMware et des serveurs physiques à un autre serveur. Par exemple :

- **Échec**: si un serveur tombe en panne ou n’est pas disponible, vous pouvez déplacer la réplication des ordinateurs protégés vers un autre serveur de traitement. Métadonnées de la source et machine de réplicas seront déplacées vers le nouveau serveur de processus et de données sont resynchronisées. Le nouveau serveur de processus se connecte automatiquement au serveur vCenter pour exécuter une découverte automatique. Vous pouvez surveiller l’état des serveurs de traitement sur le tableau de bord de récupération de Site.
- **Pour ajuster le RPO équilibrage de la charge**, pour équilibrage amélioré vous pouvez sélectionner un serveur différent dans le portail de récupération de Site et déplacer la réplication d’un ou plusieurs ordinateurs à pour équilibrer manuellement la charge. Dans ce cas, les métadonnées des ordinateurs source et de réplica sélectionnés sont déplacée vers le nouveau serveur de processus. Le serveur de processus d’origine reste connecté au serveur vCenter. 

### <a name="monitor-the-process-server"></a>Surveiller le processus de serveur

Si un serveur est dans un état critique un avertissement d’état s’affiche sur le tableau de bord de récupération de Site. Vous pouvez cliquer sur l’état, ouvrez l’onglet Evénements et ensuite une vue détaillée des tâches spécifiques dans l’onglet tâches. 

### <a name="modify-the-process-server-used-for-replication"></a>Modifier le serveur de traitement utilisé pour la réplication

1. Ouvrir les **serveurs** > **Serveurs de Configuration** > serveur configuration > **Détails du serveur**.
2. Cliquez sur **Serveurs de traitement** > **Modifier le processus de serveur** correspondante au serveur que vous souhaitez modifier.

    ![Modifier les processus serveur 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. Dans les **Processus de changer de serveur** > **Serveur cible** sélectionnez le nouveau serveur que vous souhaitez utiliser, puis sélectionnez les ordinateurs virtuels que vous souhaitez répliquer sur le nouveau serveur. Cliquez sur l’icône d’informations en regard du nom du serveur pour plus d’informations de l’espace disque et la mémoire utilisée. L’espace moyen qui sera nécessaire pour répliquer chaque ordinateur virtuel sélectionné pour le nouveau serveur de processus s’affiche pour vous aider à prendre des décisions de charger.

    ![Modifier les processus serveur 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Cliquez sur la case à cocher pour commencer la réplication vers le nouveau serveur de processus. Notez que si vous supprimez tous les ordinateurs virtuels d’un serveur de processus qui a été essentiel il doit ne plus afficher un avertissement critique dans le tableau de bord.


## <a name="third-party-software-notices-and-information"></a>Les informations et les avis de logiciels tiers

Ne pas traduire ou localiser

Le logiciel et le microprogramme utilisé dans le produit ou service Microsoft repose sur ou incorpore des matières à partir des projets répertoriés ci-dessous (collectivement, « Code tiers »).  Microsoft est l’auteur de pas d’origine du Code tiers.  La notice de copyright d’origine et de la licence, dans lesquelles Microsoft a reçu un tel Code de tierce partie, sont définis ci-dessous.

Les informations contenues dans la Section A sont en ce qui concerne les composants tiers Code des projets répertoriés ci-dessous. Ces licences et ces informations sont fournies à titre d’information uniquement.  Ce Code de tierce partie est en cours relicensed pour vous par Microsoft sous les termes de licence de logiciel Microsoft pour le produit ou service Microsoft.  

Les informations dans la Section B sont concernant des composants de Code de tiers qui sont en cours mis à votre disposition par Microsoft sous les termes de la licence d’origine.

Vous trouverez le fichier complet sur le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft réserve tous les droits non expressément concédés par les présentes, que ce soit par implication, estoppel ou autrement.
