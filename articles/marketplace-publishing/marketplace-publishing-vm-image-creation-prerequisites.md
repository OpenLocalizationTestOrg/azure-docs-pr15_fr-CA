<properties
   pageTitle="Les composants techniques requis pour la création d’une image de machine virtuelle pour le marché Azure | Microsoft Azure"
   description="Comprendre les besoins pour créer et déployer une image de machine virtuelle sur le marché d’Azure pour d’autres d’acheter."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Composants techniques requis pour la création d’une image de machine virtuelle pour l’Azure Marketplace
Lire attentivement avant de commencer le processus d’et comprendre où et pourquoi chaque étape est effectuée. Autant que possible, vous devez préparer les informations sur votre société et autres données, téléchargez les outils nécessaires et/ou créer les composants techniques avant de commencer le processus de création d’offre. Ces éléments doivent être claires de la révision de cet article.  

## <a name="download-needed-tools--applications"></a>Téléchargez des applications et les outils nécessaires
Vous devez disposer des éléments suivants avant de commencer le processus :

- En fonction du système d’exploitation que vous ciblez, installer les [applets de commande PowerShell de Azure](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) ou d’un [outil d’interface de ligne de commande Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) à partir de la page de [Téléchargements d’Azure](https://azure.microsoft.com/downloads/) .
- Installer Explorateur de stockage Azure CodePlex.
- Téléchargez et installez l’outil de Test de Certification pour Azure certifiés :
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Vous avez besoin d’un ordinateur fonctionnant sous Windows pour exécuter l’outil de certification. Si vous ne disposez pas d’un ordinateur fonctionnant sous Windows, vous pouvez exécuter l’outil dans une machine virtuelle basée sur Windows Azure.

## <a name="platforms-supported"></a>Plates-formes prises en charge
Vous pouvez développer sur Azure des ordinateurs virtuels sous Windows ou Linux. Certains éléments du processus de publication, telles que la création d’un Azure compatibles avec le disque dur virtuel (VHD)--utilisation de différents outils et étapes en fonction du système d’exploitation que vous utilisez :  

- Si vous utilisez Linux, reportez-vous à la section « Création d’un disque dur virtuel compatible avec Azure (basé sur Linux) » du [guide de publication d’image de machine virtuelle](marketplace-publishing-vm-image-creation.md).
- Si vous utilisez Windows, reportez-vous à la section « Création d’un disque dur virtuel compatible avec Azure (basé sur Windows) » du [guide de publication d’image de machine virtuelle](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Vous devez accéder à un ordinateur fonctionnant sous Windows pour :
- Exécutez l’outil de validation de certification.
- Créer l’URL de signature accès disque dur virtuel partagé pour la présentation de la certification VHD.

## <a name="develop-your-vhd"></a>Développer votre disque dur virtuel
Vous pouvez développer les disques durs virtuels dans le nuage ou sur site Azure :

- Développement basé sur le nuage signifie que toutes les étapes de développement sont effectuées à distance sur un disque dur virtuel résident sur Azure.
- Le développement de sur site nécessite le téléchargement d’un disque dur virtuel et le développement à l’aide d’infrastructure sur site. Bien que cela est possible, nous ne le recommandons pas. Notez que développer pour Windows ou SQL sur site, vous devez avoir les clés de licence locaux concernés. Vous ne peut pas inclure ou l’installation de SQL Server après avoir créé un ordinateur virtuel. Vous devez aussi utiliser votre offre une image SQL approuvée à partir du portail Azure. Si vous décidez de développer en local, vous devez effectuer certaines étapes différemment si vous développez dans le nuage. Vous pouvez rechercher des informations pertinentes dans [créer une image de machine virtuelle sur site](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Étapes suivantes
Révisé les conditions préalables et de terminer les tâches nécessaires, vous pouvez avancer à la création de votre offre d’image de machine virtuelle comme indiqué dans le [guide de publication d’image de machine virtuelle](marketplace-publishing-vm-image-creation.md).

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)
- [Créer un ordinateur virtuel exécutant Windows dans le portail d’aperçu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
