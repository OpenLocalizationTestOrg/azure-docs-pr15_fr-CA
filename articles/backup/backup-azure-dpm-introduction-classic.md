<properties
    pageTitle="Présentation de la sauvegarde de DPM d’Azure | Microsoft Azure"
    description="Introduction à la sauvegarde des serveurs DPM en utilisant le service de sauvegarde d’Azure"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="System Center Data Protection Manager, Gestionnaire de protection des données, sauvegarde de dpm"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Préparation de la sauvegarde des charges de travail vers Azure avec DPM

> [AZURE.SELECTOR]
- [Serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Serveur de sauvegarde Azure (classique)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (classique)](backup-azure-dpm-introduction-classic.md)


Cet article fournit une introduction à l’utilisation de Microsoft Azure sauvegarde pour protéger vos serveurs de System Center Data Protection Manager (DPM) et les charges de travail. En le lisant, vous serez en mesure de :

- Fonctionne de la sauvegarde du serveur DPM d’Azure
- Les conditions requises pour obtenir une meilleure expérience de sauvegarde
- Les erreurs classiques rencontrées et comment y faire face
- Scénarios pris en charge

System Center DPM sauvegarde les données de fichiers et d’applications. Données sauvegardées pour DPM peuvent être stockées sur bande, sur disque, ou sauvegardées vers Azure avec Microsoft Azure Backup. DPM interagit avec Azure sauvegarde comme suit :

- **DPM est déployé comme un ordinateur physique de virtual server ou sur site** , DPM si est déployé comme un serveur physique ou une machine virtuelle de Hyper-V sur site vous pouvez sauvegarder les données dans un coffre-fort Azure sauvegarde en plus de disques et les bandes sauvegarde.
- **DPM déployé comme une machine virtuelle Azure** : de System Center 2012 des R2 avec mise à jour 3, DPM peut être déployé comme une machine virtuelle Azure. Si DPM est déployé comme une machine virtuelle Azure, que vous pouvez sauvegarder les données sur disques Azure attaché à la machine virtuelle de DPM Azure, ou vous pouvez décharger le stockage de données à sauvegarder jusqu'à un coffre-fort Azure sauvegarde.

## <a name="why-backup-your-dpm-servers"></a>Pourquoi sauvegarder vos serveurs DPM ?

Les avantages de l’utilisation d’Azure sauvegarde pour sauvegarder les serveurs DPM sont les suivantes :

- Pour le déploiement de DPM sur site, vous pouvez utiliser la sauvegarde Azure au lieu d’un déploiement à long terme sur bande.
- Pour les déploiements DPM dans Azure, Azure sauvegarde vous permet de déléguer le stockage à partir du disque d’Azure, ce qui vous permet d’évoluer en stockant les données plus anciennes dans Azure sauvegarde et les nouvelles données sur le disque.

## <a name="how-does-dpm-server-backup-work"></a>Comment fonctionne la sauvegarde du serveur DPM ?
Pour sauvegarder une machine virtuelle, tout d’abord un instantané de point-à-temps des données est nécessaire. Le service de sauvegarde Azure lance l’opération de sauvegarde à l’heure planifiée et déclenche l’extension pour prendre un instantané de sauvegarde. L’extension de sauvegarde coordonne avec le service VSS dans-invité pour assurer la cohérence et appelle l’API de capture instantanée de blob du service stockage Azure une fois atteinte la cohérence. Cela est fait pour obtenir un instantané cohérent des disques de l’ordinateur virtuel, sans avoir à arrêter.

Une fois l’instantané a été pris, les données sont transférées par le service de sauvegarde Azure dans le coffre-fort de sauvegarde. Le service s’occupe d’identification et de transférer uniquement les blocs qui ont changé depuis la dernière sauvegarde, optimiser le stockage de sauvegardes et le réseau. Lorsque le transfert de données est terminé, le snapshot est supprimé et un point de restauration est créé. Ce point de récupération peut être vu dans Azure portal classique.

>[AZURE.NOTE] Pour les machines virtuelles de Linux, la sauvegarde uniquement de fichiers-cohérente est possible.

## <a name="prerequisites"></a>Conditions préalables
Préparer la sauvegarde Azure pour sauvegarder les données DPM comme suit :

1. **Créer un coffre-fort de sauvegarde** — créer un coffre-fort dans la console de sauvegarde d’Azure.
2. **Informations d’identification du coffre-fort de téléchargement** — dans Azure sauvegarde, télécharger le certificat de gestion que vous avez créé dans la chambre forte.
3. **Installation de l’Agent de sauvegarde Azure et enregistrer le serveur** — d’Azure sauvegarde, installer l’agent sur chaque serveur DPM et inscrire le serveur DPM dans le coffre-fort de sauvegarde.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Configurations requises (et les restrictions)

- DPM peut fonctionner comme un serveur physique ou une machine virtuelle de Hyper-V installé sur System Center 2012 SP1 ou de System Center 2012 R2. Il peut également s’exécuter comme une machine virtuelle Azure exécutant au moins sur System Center 2012 R2 avec correctif cumulatif 3 pour DPM 2012 R2 ou une machine virtuelle de Windows dans les environnements VMWare en cours d’exécution au moins sur System Center 2012 R2 avec correctif cumulatif 5.
- Si vous utilisez DPM avec System Center 2012 SP1, vous devez installer le correctif cumulatif 2 pour System Center Data Protection Manager SP1. Ceci est nécessaire avant de pouvoir installer l’Agent de sauvegarde Azure.
- Le serveur DPM doit comporter de Windows PowerShell et .net Framework 4.5 est installé.
- DPM peut sauvegarder de la plupart des charges de travail à la sauvegarde d’Azure. Pour une liste complète de ce qui est pris en charge voir la sauvegarde Azure prend en charge les éléments ci-dessous.
- Impossible de récupérer les données stockées dans Azure sauvegarde avec l’option « Copier sur bande ».
- Vous aurez besoin d’un compte Azure avec la fonction de sauvegarde de Azure activée. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Lire sur la [tarification de la sauvegarde d’Azure](https://azure.microsoft.com/pricing/details/backup/).
- À l’aide de la sauvegarde d’Azure requiert l’Agent de sauvegarde Azure doit être installé sur les serveurs que vous souhaitez sauvegarder. Chaque serveur doit disposer d’au moins 10 % de la taille des données qui sont sauvegardées, disponible en tant que stockage local. Par exemple, la sauvegarde de 100 Go de données nécessite un minimum de 10 Go d’espace libre dans l’emplacement de montage. La valeur minimale est de 10 %, 15 % de l’espace de stockage local à utiliser pour l’emplacement du cache est recommandée.
- Données seront stockées dans le stockage Azure coffre-fort. Il n’y a aucune limite à la quantité de données que vous pouvez sauvegarder une sauvegarde Azure chambre forte, mais la taille d’une source de données (par exemple un ordinateur virtuel ou base de données) ne doit pas dépasser 54,400 Go.

Ces types de fichiers sont pris en charge pour sauvegarder sur Azure :

- Crypté (sauvegardes complètes uniquement)
- Compressé (prise en charge des sauvegardes incrémentielles)
- Sparse (prise en charge des sauvegardes incrémentielles)
- Compressés et éparpillés (considérées comme Sparse)

Et Voici non pris en charge :

- Serveurs sur les systèmes de fichiers respectant la casse ne sont pas pris en charge.
- Liens en dur (ignoré)
- Points (ignoré) d’analyse
- Cryptés et compressés (ignoré)
- Chiffrés et éparpillés (ignorée)
- Flux compressé
- Flux de données fragmentée

>[AZURE.NOTE] À partir de System Center 2012 DPM avec SP1, vous pouvez sauvegarder des charges de travail protégés par DPM pour Azure à l’aide de la sauvegarde de Microsoft Azure.
