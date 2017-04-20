<properties
    pageTitle="Déplacement des données vers et depuis le stockage Azure | Microsoft Azure"
    description="Cet article fournit une vue d’ensemble des différentes méthodes permettant de déplacer des données vers et depuis le stockage Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Déplacement des données vers et depuis le stockage Azure

Si vous souhaitez déplacer des données sur site pour le stockage Azure (ou vice versa), il existe plusieurs façons de procéder. L’approche qui vous convient le mieux dépend de votre scénario. Cet article fournit une vue d’ensemble rapide des différents scénarios et offres appropriées pour chacun d’eux.

## <a name="building-applications"></a>Développement d’Applications

Si vous créez une application, développement par rapport à l’API REST ou l’un de nos nombreuses bibliothèques client est un excellent moyen de déplacer des données vers et depuis le stockage Azure.

Stockage Azure fournit des bibliothèques de client riche pour .NET, iOS, Java, Android, plate-forme de Windows universel (UWP), Xamarin, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes proposent des fonctionnalités avancées telles que la logique des nouvelles tentatives, de consignation et de téléchargements parallèles. Vous pouvez également développer directement par rapport à l’API REST, qui peut être appelée par n’importe quel langage qui facilite les demandes HTTP/HTTPS.

Reportez-vous à la section [Mise en route avec le stockage Blob Azure](storage-dotnet-how-to-use-blobs.md) pour en savoir plus.

En outre, nous proposons également la [Bibliothèque de mouvement de données de stockage Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) qui est une bibliothèque conçue pour hautes performances copie des données vers et à partir d’Azure. Reportez-vous à notre bibliothèque de mouvement de données [documentation](https://github.com/Azure/azure-storage-net-data-movement) pour en savoir plus. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rapidement affichage/interaction avec vos données

Si vous souhaitez un moyen simple de visualiser vos données de stockage Azure tout en ayant la possibilité de charger et télécharger vos données, puis utilisez un Explorateur de stockage Azure.

Consultez notre liste des [Explorateurs du stockage Azure](storage-explorers.md) pour en savoir plus.

## <a name="system-administration"></a>Administration système

Si vous devez ou sentez plus à l’aise avec un utilitaire de ligne de commande (par exemple, les administrateurs système), voici quelques options à prendre en compte :

### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour hautes performances copie des données vers et depuis le stockage Azure. Vous pouvez également copier des données dans un compte de stockage, ou entre des comptes de stockage différents.

Pour en savoir plus, reportez-vous à [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md) .

### <a name="azure-powershell"></a>PowerShell Azure

PowerShell Azure est un module qui fournit des applets de commande pour gérer les services sur Azure. Il s’agit d’un environnement de ligne de commande des tâches et un langage de script conçu spécialement pour l’administration du système.

Consultez [L’aide de PowerShell Azure avec stockage Azure](storage-powershell-guide-full.md) pour en savoir plus.

### <a name="azure-cli"></a>CLI Azure

CLI Azure fournit un ensemble d’open source, multiplates-formes des commandes pour travailler avec des services Azure. CLI Azure est disponible sur Windows et Linux OSX.

Reportez-vous [à l’aide de la CLI Azure avec stockage Azure](storage-azure-cli.md) pour en savoir plus.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Déplacement de grandes quantités de données à l’aide d’un réseau lent

Un des plus grands défis associés au déplacement d’importants volumes de données est le temps de transfert. Si vous souhaitez obtenir des données vers/depuis le stockage Azure sans soucier des coûts de réseaux ou de l’écriture de code, Azure importation/exportation est une solution appropriée.

Voir la section [Importation/exportation d’Azure](storage-import-export-service.md) pour en savoir plus.

## <a name="backing-up-your-data"></a>Sauvegarde de vos données

Si vous devez simplement sauvegarder vos données sur le stockage Azure, Azure sauvegarde est la meilleure façon de procéder. Il s’agit d’une solution puissante pour sauvegarder les données sur site et Azure VM.

Consultez la rubrique [Sauvegarde d’Azure](../backup/backup-introduction-to-azure-backup.md) pour en savoir plus.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Accéder à vos données en local et le nuage

Si vous avez besoin d’une solution pour accéder à vos données en local et le nuage, vous devez envisager l’utilisation de solution de stockage cloud d’Azure hybride, StorSimple. Cette solution se compose d’un périphérique StorSimple physique qu’intelligemment les banques fréquemment sur SSDs de données utilisées, utilisé de façon occasionnelle des données sur les disques durs et des données inactives/sauvegarde/archivage sur le stockage Azure.

Consultez [StorSimple](../storsimple/storsimple-overview.md) pour en savoir plus.

## <a name="recovering-your-data"></a>Récupération de vos données

Lorsque vous avez des applications et charges de travail sur site, vous aurez besoin d’une solution qui permet à votre entreprise de continuer à fonctionner en cas de sinistre. Récupération de Site Azure gère la réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Les données répliquées sont stockées dans le stockage Azure, ce qui vous permet d’éliminer la nécessité d’un centre de données sur site secondaire.

Consultez la section [Récupération de Site Azure](../site-recovery/site-recovery-overview.md) pour en savoir plus.
