<properties 
   pageTitle="Comment créer des NSGs en mode ARM via le portail Azure | Microsoft Azure"
   description="Découvrez comment créer et déployer des NSGs de ARM via le portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Comment gérer les NSGs à l’aide du portail Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [créer des NSGs dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

L’exemple PowerShell commandes ci-dessous s’attendent à un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes tels qu’ils sont affichés dans ce document, tout d’abord créer l’environnement de test par le déploiement de [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)et cliquez sur **déployer vers Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, suivez les instructions dans le portail. Les étapes ci-dessous utilisent **RG-NSG** comme nom du groupe de ressources que le modèle a été déployé sur.

## <a name="create-the-nsg-frontend-nsg"></a>Créer le NSG NSG-FrontEnd

Pour créer le **Frontal-NSG** NSG comme illustré dans le scénario ci-dessus, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Parcourir >** > **groupes de sécurité réseau**.

    ![Azure portal - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. De la lame de **groupes de sécurité réseau** , cliquez sur **Ajouter**.
  
    ![Azure portal - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. Dans la lame du **groupe de sécurité de réseau de créer** , créer un NSG nommé *NSG-frontal* du *NSG RG* groupe de ressources, puis cliquez sur **créer**.

    ![Azure portal - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Créer des règles dans un NSG existant

Pour créer des règles dans un NSG existant à partir du portail Azure, suivez les étapes ci-dessous.

2. Cliquez sur **Parcourir >** > **groupes de sécurité réseau**.

3. Dans la liste de NSGs, cliquez sur **NSG-FrontEnd** > **les règles de sécurité entrante**

    ![Azure portal - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Dans la liste des **règles de sécurité entrante**, cliquez sur **Ajouter**.

    ![Azure portal - ajouter une règle](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Créer une règle nommée *web-règle* de priorité de *200* accès via *TCP* vers le port *80* à toute machine virtuelle à partir de n’importe quelle source dans la blade **d’Ajouter une règle sécurité entrante** et puis cliquez sur **OK**. Notez que la plupart de ces paramètres est déjà les valeurs par défaut.

    ![Azure portal - paramètres de la règle](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Après quelques secondes, vous verrez la nouvelle règle dans le NSG.

    ![Azure portal - nouvelle règle](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Répétez les étapes 6 pour créer une règle de trafic entrant nommée *rdp-règle* avec une priorité de *250* accès via *TCP* sur le port *3389* pour tout ordinateur virtuel à partir de n’importe quelle source.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associer le NSG au sous-réseau frontal

1. Cliquez sur **Parcourir >** > **groupes de ressources** > **RG-NSG**.
2. Dans la lame **RG-NSG** , cliquez sur **...**  >  **TestVNet**.

    ![Azure portal - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. De la lame de **paramètres** , cliquez sur **sous-réseaux** > **FrontEnd** > **groupe de sécurité réseau** > **NSG-frontal**.

    ![Azure portal - paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. Dans la **partie frontale** lame, cliquez sur **Enregistrer**.

    ![Azure portal - paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Créer le NSG NSG-back-end

Pour créer le NSG **NSG-back-end** et l’associer au sous-réseau **back-end** , suivez les étapes ci-dessous.

1. Répétez les étapes de la [création du NSG NSG-frontal](#Create-the-NSG-FrontEnd-NSG) pour créer un NSG nommé *NSG-back-end*
2. Répétez les étapes dans [créer des règles dans un NSG existant](#Create-rules-in-an-existing-NSG) afin de créer les règles de **trafic entrant** dans le tableau ci-dessous.

  	|Règle de trafic entrant|Règle de trafic sortant|
  	|---|---|
  	|![Azure portal - règle de trafic entrant](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Azure portal - règle de trafic sortant](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Répétez les étapes de l' [associer le NSG au sous-réseau frontal](#Associate-the-NSG-to-the-FrontEnd-subnet) pour associer le **NSG-back-end** NSG au sous-réseau **back-end** .

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer les NSGs existants](virtual-network-manage-nsg-arm-portal.md)
- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.