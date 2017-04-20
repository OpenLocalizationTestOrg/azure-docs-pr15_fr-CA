<properties
    pageTitle="Mettre en service un ordinateur virtuel dans la pile d’Azure (clients) | Microsoft Azure"
    description="Comme un client, découvrez comment mettre en service un ordinateur virtuel dans la pile d’Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="provision-a-virtual-machine"></a>Fourniture d’une machine virtuelle

En tant qu’administrateur, vous pouvez créer des machines virtuelles pour évaluer les ressources avant de les proposer dans les plans.

## <a name="provision-a-virtual-machine"></a>Fourniture d’une machine virtuelle

1.  Sur l’ordinateur de démonstration de pile Azure, connectez-vous au `https://portal.azurestack.local` en tant qu' [administrateur](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator), puis cliquez sur **Nouveau** > **machines virtuelles** > **Windows Server 2012-R2-centre de données**.  

    ![](media/azure-stack-provision-vm/image01.png)

2.  Dans la lame de **Notions de base** , tapez un **nom**, un **nom d’utilisateur**et un **mot de passe**. Pour le **type de disque de machine virtuelle**, cliquez sur **disque dur**. Choisissez un **abonnement**. Créer un **groupe de ressources**, ou sélectionnez-en un, puis cliquez sur **OK**.  

3.  De la lame de **Choisir une taille** , cliquez sur **Base de A1**, puis cliquez sur **Sélectionner**.  

4.  De la lame de **paramètres** , cliquez sur le **réseau virtuel**. De la lame de **réseau virtuel de choisir** , cliquez sur **Créer nouveau**. Dans la lame de **créer des réseaux virtuels** , acceptez les valeurs par défaut et cliquez sur **OK**. De la lame de **paramètres** , cliquez sur **OK**.

    ![](media/azure-stack-provision-vm/image04.png)

5.  De la lame de **Résumé** , cliquez sur **OK** pour créer l’ordinateur virtuel.  

6. Pour afficher votre nouvel ordinateur virtuel, cliquez sur **toutes les ressources**, puis recherchez l’ordinateur virtuel et cliquez sur son nom.

    ![](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Étapes suivantes

[Comptes de stockage](azure-stack-provision-storage-account.md)
