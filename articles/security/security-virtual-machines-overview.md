<properties
   pageTitle="Vue d’ensemble de la sécurité des Machines virtuelles Azure | Microsoft Azure"
   description=" Les Machines virtuelles Azure vous donnent la flexibilité de la virtualisation sans avoir à acheter et à gérer le matériel physique qui exécute l’ordinateur virtuel.  Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure qui peuvent être utilisées avec Azure Virtual Machines. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Présentation de la sécurité Azure Machines virtuelles

Les Machines virtuelles Azure vous permet de déployer une large gamme de solutions informatiques d’une manière agile. Avec la prise en charge de Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP et les Services BizTalk Azure, vous pouvez déployer les charges de travail et de n’importe quel langage sur pratiquement n’importe quel système d’exploitation.

Une machine virtuelle Azure vous donne la flexibilité de la virtualisation sans avoir à acheter et à gérer le matériel physique qui exécute l’ordinateur virtuel.  Vous pouvez générer et déployer vos applications avec l’assurance que vos données sont protégées et sécurité dans nos centres de données hautement sécurisé.

Azure, vous pouvez créer des solutions conformes, amélioration de la sécurité qui :

- Protégez vos ordinateurs virtuels contre les virus et les logiciels malveillants
- Crypter vos données sensibles
- Sécuriser le trafic réseau
- Identifier et de détecter les menaces
- Répondre aux exigences de conformité

L’objectif de cet article est de fournir une vue d’ensemble des principales fonctionnalités de sécurité Azure qui peuvent être utilisées avec les ordinateurs virtuels. Nous fournissons également des liens vers des articles qui donnent des détails de chaque fonctionnalité et vous pouvez en savoir plus.  

Les fonctionnalités de sécurité principaux Azure Virtual Machine qui seront traités dans cet article :

- Contre les logiciels malveillants
- Module de sécurité matériel
- Chiffrement de disque de machine virtuelle
- Sauvegarde des machines virtuelles
- Récupération de Site Azure
- Réseau virtuel
- Gestion des stratégies de sécurité et de génération de rapports
- Respect de la réglementation

## <a name="antimalware"></a>Contre les logiciels malveillants

Avec Azure, vous pouvez utiliser des logiciels anti-programmes malveillants à partir des fournisseurs de sécurité, tels que Microsoft, Symantec, Trend Micro, McAfee et Kaspersky pour protéger vos ordinateurs virtuels à partir des fichiers malveillants, les logiciels publicitaires et autres menaces. Reportez-vous à la section informations supplémentaires ci-dessous pour trouver des articles sur les partenaires solutions.

Microsoft Antimalware pour les Services en nuage Azure et les ordinateurs virtuels est une fonction de protection en temps réel qui vous aide à identifier et supprimer les virus, les logiciels espions et autres logiciels malveillants.  Microsoft Antimalware fournit des alertes configurables connues de logiciel malveillant ou indésirable tente de s’installer ou s’exécuter sur vos systèmes Azure.

Microsoft Antimalware est une solution d’agent unique pour les applications et les environnements clients, conçus pour s’exécuter en arrière-plan sans aucune intervention humaine. Vous pouvez déployer une protection en fonction des besoins de votre application des charges de travail avec une base sécurisée par défaut ou la configuration personnalisée, y compris contre les logiciels malveillants analyse avancée.

Lorsque vous déployez et activez Microsoft Antimalware, les fonctionnalités de base suivantes sont disponibles :

- Protection en temps réel - surveille l’activité dans les Services en nuage et sur des Machines virtuelles pour détecter et bloquer l’exécution de logiciels malveillants.
- Analyse programmée - exécute périodiquement une analyse ciblée afin de détecter les logiciels malveillants, y compris les programmes en cours d’exécution.
- Résolution - intervient automatiquement sur les logiciels malveillants détectés, telles que la suppression ou de mise en quarantaine des fichiers malveillants et de nettoyage des entrées de Registre malveillant.
- Mises à jour de signature - installe les dernières signatures de protection (définitions de virus) pour assurer la protection est automatiquement mise à jour à une fréquence prédéterminée.
- Moteur du logiciel anti-programme malveillant – met automatiquement à jour met à jour le moteur Microsoft Antimalware.
- Plate-forme de logiciels anti-programmes malveillants – met automatiquement à jour mises à jour de la plate-forme Microsoft Antimalware.
- Protection active - les rapports sur les menaces détectées et ressources suspects pour garantir une réponse rapide aux métadonnées de télémétrie Azure et permet une livraison signatures synchrone en temps réel via le système de Protection Active (MAPS) Microsoft.
- Exemples de création de rapports - fournit et des rapports des échantillons au service Microsoft Antimalware pour aider à améliorer le service et permettre la résolution des problèmes.
- Exclusions – permet l’application et les administrateurs de services configurer certains fichiers, des processus et de les exclure de la protection et l’analyse pour des raisons de performances et autres lecteurs.
- Collecte des événements contre les logiciels malveillants - enregistre le fonctionnement du service contre les logiciels malveillants, les activités suspectes et les actions correctives prises dans le journal des événements système d’exploitation et les rassemble dans le compte du client le stockage Azure.

Pour en savoir plus : pour en savoir plus sur les logiciels anti-programmes malveillants pour protéger vos ordinateurs virtuels, voir :

- [Logiciel anti-programme malveillant de Microsoft pour les Services en nuage Azure et les Machines virtuelles](../security/azure-security-antimalware.md)
- [Déploiement de Solutions contre les logiciels malveillants sur les ordinateurs virtuels Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Comment faire pour installer et configurer la sécurité Trend Micro en tant que Service sur un ordinateur virtuel de Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Comment faire pour installer et configurer Symantec Endpoint Protection sur un ordinateur virtuel de Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nouvelles Options de logiciels anti-programmes malveillants pour protéger les ordinateurs virtuels Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Solutions de sécurité sur le marché d’Azure](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Sécurité du matériel Module

Authentification et cryptage n’améliorent pas sécurité sauf si les clés elles-mêmes sont protégés. Vous pouvez simplifier la gestion et la sécurité de vos secrets stratégiques clés en les stockant dans Azure clé coffre-fort. Coffre-fort de clé permet de stocker les clés dans les modules de sécurité matérielle (HSM) certifiés FIPS 140-2 de niveau 2 normes. Vos clés de chiffrement de SQL Server pour la sauvegarde ou le [chiffrement de données transparent](https://msdn.microsoft.com/library/bb934049.aspx) peuvent tous être stockés dans le coffre-fort de la clé avec les clés ou les secrets à partir de vos applications. Les autorisations et l’accès à ces éléments protégés sont gérés grâce à [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Pour en savoir plus :

- [Quel est le coffre-fort de clé Azure ?](../key-vault/key-vault-whatis.md)
- [Mise en route de la chambre forte de clé Azure](../key-vault/key-vault-get-started.md)
- [Blog de clé coffre-fort Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Chiffrement de disque de machine virtuelle

Chiffrement de disque Azure est une nouvelle fonctionnalité qui vous permet de crypter vos disques Windows et Linux Azure Virtual Machine. Chiffrement de disque Azure utilise la fonctionnalité de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de secteur standard de Windows et de Linux, la fonctionnalité de [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) pour fournir le cryptage de volume pour le système d’exploitation et les disques de données.

La solution est intégrée à la chambre forte de clé Azure pour vous aider à contrôler et de gérer les clés de cryptage de disque et les secrets de votre abonnement au coffre-fort clé, tout en s’assurant que toutes les données sur les disques de l’ordinateur virtuel sont cryptées au reste de votre stockage Azure.

Pour en savoir plus :

- [Cryptage disque Azure pour Windows et Linux IaaS VMs](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Cryptage disque Azure pour Linux et les Machines virtuelles Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Crypter une machine virtuelle](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Sauvegarde des machines virtuelles

Sauvegarde Azure est une solution évolutive qui protège vos données d’application avec zéro investissement et les coûts d’exploitation minimum. Erreurs d’application peuvent endommager vos données et erreurs humaines peuvent introduire des bogues dans vos applications. Sauvegarde d’Azure, vos ordinateurs virtuels exécutant Windows et Linux sont protégés.

Pour en savoir plus :

- [Quelle est la sauvegarde d’Azure ?](../backup/backup-introduction-to-azure-backup.md)
- [Cursus sauvegarde Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Service de sauvegarde Azure - Forum aux questions](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Récupération de Site Azure

Une partie importante de la stratégie de votre organisation BCDR est de savoir comment gérer des charges de travail d’entreprise et des applications et en cours d’exécution lorsque les interruptions planifiées et non planifiées se produisent. Récupération de Site Azure permet d’orchestrer la réplication, le basculement et la restauration des applications et de charges de travail afin qu’ils soient disponibles à partir d’un emplacement secondaire si votre emplacement principal tombe en panne.

Récupération de site :

- **Simplifie votre stratégie BCDR** : récupération de Site permet de gérer la réplication, le basculement et la restauration de plusieurs charges de travail métier et les applications à partir d’un emplacement unique. Récupération de site orchestre la réplication et le basculement sur incident mais n’intercepter vos données d’application ou dispose d’informations à ce sujet.
- **Flexibilité de réplication fournit** , à l’aide de la récupération de Site, vous pouvez répliquer les charges de travail s’exécutant sur des ordinateurs virtuels Hyper-V, des machines virtuelles VMware et des serveurs physiques Windows/Linux.
- **Prend en charge basculement et restauration** — restauration du Site fournit des basculements tests pour prendre en charge des exercices de récupération d’urgence sans affecter les environnements de production. Vous pouvez également exécuter des basculements planifiés avec une perte de données zéro pour les interruptions prévues ou basculement non planifié avec perte de données minimale (en fonction de la fréquence de réplication) pour les sinistres. Après le basculement, vous pouvez la restauration automatique à vos sites principaux. Récupération de site fournit des plans de récupération qui peuvent inclure des scripts et des classeurs d’automation Azure afin que vous pouvez personnaliser le basculement et la reprise d’applications multicouches.
- **Supprime de centre de données secondaire** , vous pouvez répliquer vers un site secondaire sur site ou sur Azure. À l’aide d’Azure comme destination de la récupération d’urgence élimine le coût et la complexité de la gestion d’un site secondaire. Les données répliquées sont stockées dans le stockage Azure.
- **S’intègre avec les technologies existantes de BCDR** , récupération de Site collabore avec d’autres fonctionnalités BCDR d’application. Par exemple, vous pouvez utiliser la restauration du Site pour protéger le SQL Server back-end de charges de travail d’entreprise. Cela inclut la prise en charge native pour AlwaysOn de SQL Server gérer le basculement des groupes de disponibilité.

Pour en savoir plus :

- [Quelle est la récupération de Site Azure ?](../site-recovery/site-recovery-overview.md)
- [Comment fonctionne la récupération de Site Azure ?](../site-recovery/site-recovery-components.md)
- [Les charges de travail sont protégés par la récupération de Site Azure ?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Réseau virtuel

Machines virtuelles ont besoin de connectivité réseau. Pour prendre en charge cette exigence, Azure nécessite des ordinateurs virtuels pour être connecté à un réseau virtuel d’Azure. Un réseau virtuel Azure est une construction logique basée sur le fabric physique réseau Azure. Chaque réseau virtuel Azure logique est isolé de tous les autres réseaux virtuels Azure. Cette isolation permet de s’assurer que le trafic réseau dans vos déploiements n’est pas accessible aux autres clients Microsoft Azure.

Pour en savoir plus :

- [Vue d’ensemble de la sécurité réseau Azure](security-network-overview.md)
- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)
- [Les fonctionnalités de mise en réseau et des partenariats pour des scénarios d’entreprise](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestion des stratégies de sécurité et de génération de rapports

Azure le centre de sécurité vous aide à empêcher, détecter et répondre aux menaces et fournit le qu'augmentation de visibilité et de contrôler, de la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

Centre de sécurité Azure vous aide à optimiser et à contrôler la sécurité des machines virtuelles par :

- Fournissant des [recommandations de sécurité](../security-center/security-center-recommendations.md) de la machine virtuelle telles qu’appliquer les mises à jour système, configurer des points de terminaison ACL, activer contre les logiciels malveillants, activer des groupes de sécurité réseau et appliquer le cryptage de disque.
- Surveillance de l’état de vos machines virtuelles

Pour en savoir plus :

- [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md)
- [Questions fréquentes sur le centre de sécurité Azure](../security-center/security-center-faq.md)
- [Opérations et la planification du centre de sécurité Azure](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Respect de la réglementation

Les Machines virtuelles Azure est certifiée pour FISMA, FedRAMP, HIPAA, PCI DSS niveau 1 et autres programmes de conformité de clé. Cette certification permet plus facilement de vos propres applications Azure répondre aux exigences de conformité et de votre entreprise résoudre une large gamme d’exigences de réglementations nationaux et internationaux.

Pour en savoir plus :

- [Centre de confidentialité de Microsoft : respect de la réglementation](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Nuage approuvé : Conformité, de confidentialité et de sécurité de Microsoft Azure](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
