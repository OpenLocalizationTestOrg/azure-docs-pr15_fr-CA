<properties
   pageTitle="Créer une identité de travail ou de l’école dans DAS | Microsoft Azure"
   description="Apprenez à créer une identité de travail ou de l’école dans Azure Active Directory à utiliser avec vos ordinateurs virtuels de Windows."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-windows-vms"></a>Création d’une identité de travail ou à l’école dans Azure Active Directory à utiliser avec les ordinateurs virtuels de Windows

Si vous créé un compte Azure personnel ou disposez d’un abonnement MSDN personnel et créé le compte Azure pour tirer parti des crédits MSDN Azure--une identité de *compte Microsoft* vous permet de le créer. De nombreuses fonctionnalités d’Azure-- [modèles de groupe de ressources](../azure-resource-manager/resource-group-overview.md) est un exemple : besoin d’un compte de travail ou à l’école (une identité gérée par Azure Active Directory) pour fonctionner. Vous pouvez suivre les instructions ci-dessous pour créer un nouveau travail ou le compte de l’établissement, étant fort heureusement, une des meilleures choses de votre compte personnel sur Azure qui l’accompagne un domaine Azure Active Directory par défaut que vous pouvez utiliser pour créer un nouveau travail ou le compte de l’établissement que vous pouvez utiliser avec les fonctions Azure qui en ont besoin.

Toutefois, les modifications récentes rendent possible gérer votre abonnement à n’importe quel type de compte Azure à l’aide de la `azure login` méthode d’ouverture de session interactive décrite [ici](../xplat-cli-connect.md). Vous pouvez utiliser ce mécanisme, ou vous pouvez suivre les instructions qui suivent. Vous pouvez également [créer un travail ou l’école d’identité dans Azure Active Directory à utiliser avec les ordinateurs virtuels de Linux](virtual-machines-linux-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
