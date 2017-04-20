<properties 
   pageTitle="Gérer à l’aide du portail de l’aperçu dans le Gestionnaire de ressources de NSGs | Microsoft Azure"
   description="Découvrez comment gérer l’existant à l’aide du portail de l’aperçu dans le Gestionnaire de ressources de NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Gestion des NSGs de via le portail de l’aperçu

> [AZURE.SELECTOR]
- [Portail](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [CLI Azure](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Extraire des informations

Vous pouvez afficher votre NSGs existantes, récupérer les règles pour une NSG existant et savoir quelles ressources un NSG est associé à.

### <a name="view-existing-nsgs"></a>Afficher NSGs existants
Pour afficher toutes les NSGs existants dans un abonnement, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Parcourir >** > **groupes de sécurité réseau**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Vérifiez la liste des NSGs de la lame de **groupes de sécurité réseau** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Pour afficher la liste des NSGs dans le groupe de ressources **RG-NSG** , suivez les étapes ci-dessous. 

1. Cliquez sur **les groupes de ressources >** > **- RG NSG** > **...**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Dans la liste des ressources, rechercher des éléments de l’affichage de l’icône NSG, comme illustré à la lame de **ressources** ci-dessous.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Liste de toutes les règles pour une NSG

Pour afficher les règles d’un NSG nommés **FrontEnd-NSG**, suivez les étapes ci-dessous. 

1. À partir de la lame de **groupes de sécurité de réseau** , ou sur la lame de **ressources** ci-dessus, cliquez sur **NSG-frontal**.
2. Dans l’onglet **paramètres** , cliquez sur **règles de sécurité entrante**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. La lame de **sécurité règles de trafic entrant** s’affiche comme illustré ci-dessous.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Dans l’onglet **paramètres** , cliquez sur **règles de sécurité sortante** pour afficher les règles de trafic sortant.

>[AZURE.NOTE] Pour afficher les règles par défaut, cliquez sur l’icône de **règles par défaut** en haut de la lame qui affiche les règles.

### <a name="view-nsgs-associations"></a>Afficher les associations de NSGs

Pour afficher les ressources que du NSG **NSG-frontal** est associer, suivez les étapes ci-dessous.

1. À partir de la lame de **groupes de sécurité de réseau** , ou sur la lame de **ressources** ci-dessus, cliquez sur **NSG-frontal**.
2. Dans l’onglet **paramètres** , cliquez sur **sous-réseaux** pour afficher les sous-réseaux sont associés à la NSG.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Dans l’onglet **paramètres** , cliquez sur **interfaces réseau** pour afficher les cartes réseau associées à du NSG.

## <a name="manage-rules"></a>Gérer les règles

Vous pouvez ajouter des règles à un NSG existant, modifier les règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle

Pour ajouter une règle autorisant le trafic **entrant** sur le port **443** à partir de n’importe quel ordinateur à **NSG-FrontEnd** NSG, suivez les étapes ci-dessous.

1. À partir de la lame de **groupes de sécurité de réseau** , ou sur la lame de **ressources** ci-dessus, cliquez sur **NSG-frontal**.
2. Dans l’onglet **paramètres** , cliquez sur **règles de sécurité entrante**.
3. Dans la lame **entrant des règles de sécurité** , cliquez sur **Ajouter**. Puis, dans la lame de **règle de sécurité entrante d’ajouter** , remplir les valeurs comme indiqué ci-dessous, puis cliquez sur **OK**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Après quelques secondes, notez la nouvelle règle dans la lame de **règles de sécurité entrante** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Modifier une règle

Pour modifier la règle créée ci-dessus pour autoriser le trafic entrant à partir d' **Internet** uniquement, procédez comme suit.

1. À partir de la lame de **groupes de sécurité de réseau** , ou sur la lame de **ressources** ci-dessus, cliquez sur **NSG-frontal**.
2. Dans l’onglet **paramètres** , cliquez sur la règle créée ci-dessus.
3. Dans la lame **https autoriser** , modifier la propriété **Source** , comme indiqué ci-dessous, puis cliquez sur **Enregistrer**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Supprimer une règle

Pour supprimer la règle créée ci-dessus, suivez les étapes ci-dessous.

1. À partir de la lame de **groupes de sécurité de réseau** , ou sur la lame de **ressources** ci-dessus, cliquez sur **NSG-frontal**.
2. Dans l’onglet **paramètres** , cliquez sur la règle créée ci-dessus.
3. De la lame **- https autoriser** , cliquez sur **Supprimer**, puis cliquez sur **Oui**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gérer les associations

Vous pouvez associer un NSG à des sous-réseaux et des cartes réseau. Vous pouvez également dissocier un NSG à partir de n’importe quelle ressource qu'il est associé.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un NSG à une carte réseau

Pour associer les **NSG-FrontEnd** NSG à la **TestNICWeb1** de carte réseau, suivez les étapes ci-dessous.

1. À partir de la lame de **groupes de sécurité de réseau** , ou sur la lame de **ressources** ci-dessus, cliquez sur **NSG-frontal**.
2. Dans l’onglet **paramètres** , cliquez sur **interfaces réseau** > **associer** > **TestNICWeb1**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier un NSG à partir d’une carte réseau

Pour dissocier **NSG-FrontEnd** NSG à partir de la **TestNICWeb1** de carte réseau, suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **les groupes de ressources >** > **- RG NSG** > **...**  >  **TestNICWeb1**.
2. De la lame de **TestNICWeb1** , cliquez sur **sécurité de modification...**  > **None**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Vous pouvez également utiliser cette blade pour associer la carte réseau à n’importe quel NSG existant.

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier un NSG à partir d’un sous-réseau

Pour dissocier **NSG-FrontEnd** NSG à partir du sous-réseau **frontal** , suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **les groupes de ressources >** > **- RG NSG** > **...**  >  **TestVNet**.
2. De la lame de **paramètres** , cliquez sur **sous-réseaux** > **FrontEnd** > **groupe de sécurité réseau** > **Aucun**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Dans la **partie frontale** lame, cliquez sur **Enregistrer**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associer un NSG à un sous-réseau

Pour associer à nouveau les **NSG-FrontEnd** NSG au sous-réseau **FronEnd** , suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **les groupes de ressources >** > **- RG NSG** > **...**  >  **TestVNet**.
2. De la lame de **paramètres** , cliquez sur **sous-réseaux** > **FrontEnd** > **groupe de sécurité réseau** > **NSG-frontal**.
3. Dans la **partie frontale** lame, cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez également associer un NSG à un sous-réseau de lame de **paramètres** du NSG thh.

## <a name="delete-an-nsg"></a>Supprimer un NSG

Vous ne pouvez supprimer un NSG que si elle n’a pas été associé à une ressource. Pour supprimer un NSG, suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **les groupes de ressources >** > **- RG NSG** > **...**  >  **- FrontEnd NSG**.
2. De la lame de **paramètres** , cliquez sur **interfaces réseau**.
3. S’il existe des cartes réseau répertoriées, cliquez sur la carte réseau et suivez l’étape 2 de [Dissociate un NSG à partir d’une carte réseau](#Dissociate-an-NSG-from-a-NIC).
4. Répétez l’étape 3 pour chaque carte réseau.
5. De la lame de **paramètres** , cliquez sur **sous-réseaux**.
6. S’il existe des sous-réseaux répertoriés, cliquez sur le sous-réseau et suivez les étapes 2 et 3 dans [Dissociate un NSG à partir d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet).
7. Fait défiler de gauche à la lame **NSG-frontal** , puis cliquez sur **Supprimer** > **Oui**.

[Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Étapes suivantes

- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.
