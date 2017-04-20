<properties
   pageTitle="Meilleures pratiques pour des mises à jour logicielles de Microsoft Azure IaaS | Microsoft Azure"
   description="L’article fournit un ensemble de meilleures pratiques pour des mises à jour de logiciel dans un environnement Microsoft Azure IaaS.  Il est conçu pour les professionnels de l’informatique et de modifier les analystes de sécurité qui traitent des contrôle, gestion des actifs et de la mise à jour de logiciels sur une base quotidienne, y compris celles responsables de la sécurité et la conformité de leur organisation."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Méthodes conseillées pour les mises à jour logicielles de Microsoft Azure IaaS

Avant de plonger dans n’importe quel type de discussion sur les meilleures pratiques dans un environnement d' Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) , il est important de comprendre les scénarios vous aurez vous gestion des mises à jour logicielles et des responsabilités. Le diagramme ci-dessous doit vous aider à comprendre ces limites :

![Modèles de cloud et de la charge](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

La colonne la plus à gauche affiche les sept responsabilités (définies dans les sections qui suivent) que les entreprises doivent envisager, qui contribuent à la sécurité et la confidentialité d’un environnement informatique.
 
La classification des données et de responsabilité et de protection du Client et le point de terminaison sont les responsabilités qui sont uniquement dans le domaine des clients et des responsabilités physique, hôte et réseau sont dans le domaine des fournisseurs de services de cloud dans les modèles de services PaaS et SaaS. 

Les responsabilités restantes sont partagées entre les clients et les fournisseurs de services en nuage. Certaines responsabilités requièrent le CSP et le client gérer et administrer la responsabilité, notamment l’audit de leurs domaines. Par exemple, considérez l’identité & accéder à la gestion lors de l’utilisation des Services Azure de Active Directory ; la configuration de services tels que l’authentification à plusieurs facteurs est décidée par le client, mais des fonctionnalités efficaces est la responsabilité de Microsoft Azure.

> [AZURE.NOTE] Pour plus d’informations sur les responsabilités partagées dans le nuage, lire [Des responsabilités partagées pour le Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Ces mêmes principes s’appliquent dans un scénario hybride où votre société est à l’aide de machines virtuelles IaaS Azure qui communiquent avec des ressources sur site comme indiqué dans le schéma ci-dessous.

![Scénario typique hybride avec Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Évaluation initiale

Même si votre société utilise déjà un système de gestion des mises à jour et que vous avez déjà des stratégies de mise à jour logicielle en place, il est important de revoir les évaluations de stratégie précédente fréquemment et de les mettre à jour en fonction de vos exigences actuelles. Cela signifie que vous devez être familiarisé avec l’état actuel des ressources de votre société. Pour accéder à cet état, vous devez connaître :

-   Les ordinateurs physiques et virtuels de votre entreprise.

-   Systèmes d’exploitation et versions s’exécutant sur chacun de ces ordinateurs physiques et virtuels.

-   Les mises à jour de logiciels actuellement installés sur chaque ordinateur (versions des service packs, mises à jour logicielles et les autres modifications).

-   La fonction effectue par chaque ordinateur de votre entreprise.

-   Les applications et les programmes qui s’exécutent sur chaque ordinateur.

-   Informations de propriété et de contact pour chaque ordinateur.

-   Les actifs présent dans votre environnement et de leur valeur relative pour déterminer que les zones nécessitant le plus d’attention et de protection.

-   Problèmes de sécurité connus et les processus de votre entreprise a mis en place pour l’identification des nouveaux problèmes de sécurité ou les changements de niveau de sécurité.

-   Contre-mesures ont été déployés pour sécuriser votre environnement.

Vous devez mettre à jour ces informations régulièrement, et il doit être facilement accessibles à ceux impliqués dans le processus de gestion de mise à jour de logiciel.

## <a name="establish-a-baseline"></a>Établir une planification initiale

Une partie importante du processus de gestion de mise à jour de logiciel consiste à créer des installations standard initiales des versions du système d’exploitation, d’applications et de matériel pour les ordinateurs de votre entreprise. Il s’agit des configurations de référence. Une ligne de base est la configuration d’un produit ou d’un système établi à un moment spécifique dans le temps. Une application ou un système d’exploitation, ligne de base, par exemple, permet de reconstruire un ordinateur ou un service à un état spécifique.

Configurations de référence servent de base pour trouver et corriger les problèmes potentiels et simplifient le processus de gestion de mise à jour de logiciels, à la fois en réduisant le nombre de mises à jour logicielles que vous devez déployer dans votre entreprise et en augmentant votre capacité à surveiller le respect de la réglementation.

Après l’exécution de la vérification initiale de votre entreprise, vous devez utiliser les informations obtenues à partir de l’audit pour définir une planification opérationnelle pour les composants informatiques au sein de votre environnement de production. Un certain nombre de configurations de référence peut être nécessaire, selon les différents types de matériel et logiciels déployés en production.

Par exemple, certains serveurs nécessitent une mise à jour de logiciels pour les empêcher de se bloquer lors de la saisie du processus d’arrêt lors de l’exécution de Windows Server 2012. Une ligne de base pour ces serveurs doit inclure cette mise à jour de logiciel.

Dans les grandes organisations, il est souvent utile de diviser les ordinateurs de votre entreprise dans des catégories d’actifs et de conserver chaque catégorie sur une ligne de base standard en utilisant les mêmes versions des logiciels et des mises à jour logicielles. Vous pouvez ensuite utiliser ces catégories d’actifs dans la définition des priorités d’une distribution de mise à jour de logiciel.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>S’abonner pour les services de notification de mise à jour de logiciel approprié

Après avoir effectué un audit initial du logiciel en cours d’utilisation dans votre entreprise, vous devez déterminer la meilleure méthode pour la réception des notifications des nouvelles mises à jour de logiciels pour chaque produit logiciel et chaque version. Selon le produit logiciel, la meilleure méthode de notification peut-être être des notifications par courrier électronique, des sites Web ou des publications de l’ordinateur.

Par exemple, le centre de réponse sécurité Microsoft (MSRC) répond à tous les problèmes liés à la sécurité sur les produits Microsoft et fournit le Service de Bulletin de sécurité Microsoft, une notification gratuite par courrier électronique des vulnérabilités récemment identifiées et des mises à jour logicielles qui sont publiées pour résoudre ces problèmes. Vous pouvez vous abonner à ce service à http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Considérations relatives à la mise à jour de logiciel

Après avoir effectué un audit initial du logiciel en cours d’utilisation dans votre entreprise, vous devez déterminer la configuration requise pour l’installation de votre système de gestion de mise à jour de logiciels, dont dépend le système de gestion de mise à jour de logiciel que vous utilisez. Pour WSUS lire méthodes [Conseillées avec Windows Server Update Services](https://technet.microsoft.com/library/Cc708536), System Center adapté [planification des mises à jour de logiciel dans Configuration Manager](https://technet.microsoft.com/library/gg712696).

Toutefois, il existe certaines considérations générales et les meilleures pratiques que vous pouvez appliquer quelle que soit la solution que vous utilisez comme indiqué dans les sections qui suit.

### <a name="setting-up-the-environment"></a>Configuration de l’environnement

Prenez en compte les pratiques suivantes lors de la planification vous permet de configurer le logiciel de mise à jour de l’environnement de gestion :

-   **Collections de mise à jour de logiciels de production créer en fonction de critères de stabilité**: en règle générale, pour créer des collections de votre inventaire de mise à jour de logiciel et de la distribution à l’aide de critères de stabilité permettra de simplifier toutes les étapes du processus de gestion de mise à jour de logiciel. Les critères de stabilité peuvent inclure la version du système d’exploitation client installé et niveau de service pack, rôle système ou l’organisation cible.

-   **Créer les collections de pré-production qui incluent les ordinateurs de référence**: la collection de pré-production doit inclure les configurations représentatives des autres logiciels fonctionnant dans votre entreprise, gamme de logiciels d’entreprise et les versions de système d’exploitation.

Vous devez également envisager où le serveur de mise à jour de logiciel trouvera, s’il est dans l’infrastructure IaaS d’Azure dans le nuage ou s’il est sur site. Il s’agit d’une décision importante car vous devez évaluer la quantité de trafic entre les ressources en local et de l’infrastructure Azure. Pour plus d’informations sur la connexion de votre infrastructure sur site vers Azure, lire [se connecter un réseau local à un réseau virtuel Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) .

Les options de conception qui déterminent où se trouvera le serveur de mise à jour peut également varier en fonction de votre infrastructure actuelle et le système de mise à jour de logiciel que vous utilisez actuellement. Pour WSUS lisez [Déployer Windows Server Update Services dans votre organisation](https://technet.microsoft.com/library/hh852340.aspx) ainsi que de System Center Configuration Manager [planification des Sites et des hiérarchies dans le Gestionnaire de Configuration](https://technet.microsoft.com/library/Gg712681.aspx).

### <a name="backup"></a>Sauvegarde

Des sauvegardes régulières sont importantes non seulement pour la mise à jour de plate-forme d’administration logicielle elle-même mais également pour les serveurs qui seront mis à jour. Organisations qui disposent d’un [processus de gestion des changements](https://technet.microsoft.com/library/cc543216.aspx) en place nécessitera informatiques de justifier les raisons pourquoi le serveur doit être mis à jour, l’estimation de temps d’arrêt et l’impact possible. Pour vous assurer que vous disposez d’une configuration de restauration en place en cas d’échec d’une mise à jour, assurez-vous de sauvegarder le système régulièrement.

Certaines options de sauvegarde pour Azure IaaS sont les suivantes :

-   [Protection de la charge de travail IaaS Azure à l’aide de Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Sauvegarder des ordinateurs virtuels Azure](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Surveillance

Vous devez exécuter des rapports réguliers à surveiller le nombre de manquant ou installé des mises à jour, ou avec état incomplet, pour chaque mise à jour de logiciel est autorisé. De la même façon, le reporting des mises à jour de logiciels qui ne sont pas encore autorisées peut faciliter les décisions de déploiement plus faciles.

Vous devez également envisager les tâches suivantes :

-   Effectuer un audit des mises à jour de sécurité applicables et installées pour tous les ordinateurs de votre société.

-   Autoriser et déployer les mises à jour aux ordinateurs appropriés.

-   Effectuer le suivi de l’inventaire et mettre à jour l’état d’installation et de progression pour tous les ordinateurs de votre société.

En outre de Généralités qui ont été décrites dans cet article, vous devez également envisager de chaque produit 's meilleur pratiques, par exemple : Si vous avez un ordinateur virtuel dans Azure avec SQL Server, assurez-vous que vous suivez la recommandation de mises à jour de logiciel pour ce produit.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les directives décrites dans cet article pour vous aider à déterminer les meilleures options pour les mises à jour logicielles pour les machines virtuelles au sein d’Azure IaaS. Il existe de nombreuses similitudes entre les pratiques recommandées de mise à jour de logiciel dans un centre de données traditionnel et Azure IaaS, par conséquent, il est recommandé d’évaluer vos stratégies de mise à jour logicielle en cours pour inclure Azure VM et les meilleures pratiques appropriées à partir de cet article dans le processus de mise à jour de logiciel global.
