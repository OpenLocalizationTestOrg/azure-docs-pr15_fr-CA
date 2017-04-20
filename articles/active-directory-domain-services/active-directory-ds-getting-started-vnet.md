<properties
    pageTitle="Services de domaine Active Directory Azure : Créer ou sélectionner un réseau virtuel | Microsoft Azure"
    description="Mise en route avec Azure des Services de domaine Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Créez ou sélectionnez un réseau virtuel pour les Services de domaine Active Directory de Azure

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Instructions pour sélectionner un réseau virtuel Azure
> [AZURE.NOTE] **Avant de commencer**: reportez-vous à la [mise en réseau des considérations relatives à des Services de domaine Active Directory de Azure](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Étape 2 : Création d’un réseau virtuel Azure
La tâche de configuration suivante est de créer un réseau virtuel Azure et un sous-réseau au sein de celui-ci. Vous activez les Services de domaine Active Directory de Azure dans ce sous-réseau au sein du réseau virtuel. Si vous disposez déjà d’un réseau virtuel existant que vous préférez utiliser, vous pouvez ignorer cette étape.

> [AZURE.NOTE] Assurez-vous que le réseau virtuel Azure vous créez ou que vous choisissez d’utiliser avec les Services de domaine Active Directory de Azure appartient à une région Azure qui est pris en charge par les Services de domaine Active Directory de Azure. Consultez la page [des services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lequel les Services de domaine Active Directory Azure est disponible.

Notez le nom du réseau virtuel afin de sélectionner le réseau virtuel droite lors de l’activation des Services de domaine Active Directory de Azure lors d’une étape de configuration suivantes.

Effectuez les étapes de configuration suivantes pour créer un réseau virtuel Azure dans lequel vous souhaitez activer les Services de domaine Active Directory de Azure.

1. Accédez au **portail de classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Sélectionnez le nœud de **réseaux** dans le volet gauche.

    ![Nœud de réseaux](./media/active-directory-domain-services-getting-started/networks-node.png)

3. Dans le volet Office au bas de la page, cliquez sur **Nouveau** .

    ![Nœud de réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Dans le nœud **Des Services de réseau** , sélectionnez le **Réseau virtuel**.

5. Cliquez sur **Création rapide** pour créer un réseau virtuel.

    ![Créer des réseaux virtuels - rapide](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Spécifiez un **nom** pour votre réseau virtuel. Vous pouvez également choisir de configurer **l’espace d’adressage** ou le **nombre de machine virtuelle maximal** pour ce réseau. Vous pouvez laisser le **serveur DNS** défini à 'None' pour l’instant. Vous pouvez mettre à jour le serveur DNS définition après votre domaine Active Directory de Azure d’activer des Services.

7. Veillez à sélectionner une région Azure pris en charge dans la liste déroulante **emplacement** . Consultez la page [des services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lequel les Services de domaine Active Directory Azure est disponible.

8. Pour créer votre réseau virtuel, cliquez sur le bouton **créer un réseau virtuel** .

    ![Créer un réseau virtuel pour les Services de domaine Active Directory de Azure.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Une fois le réseau virtuel est créé, sélectionnez le réseau virtuel et cliquez sur l’onglet **configurer** .

    ![Créer un sous-réseau](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Accédez à la section **espaces d’adressage de réseau virtuel** . Cliquez sur **Ajouter un sous-réseau** et spécifier un sous-réseau avec le nom **AaddsSubnet**. Cliquez sur **Enregistrer** pour créer le sous-réseau.

    ![Créer un sous-réseau pour les Services de domaine Active Directory de Azure.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tâche 3 - Activer Azure AD domaine Services
La tâche suivante de la configuration consiste à [Activer les Services de domaine Active Directory de Azure](active-directory-ds-getting-started-enableaadds.md).
