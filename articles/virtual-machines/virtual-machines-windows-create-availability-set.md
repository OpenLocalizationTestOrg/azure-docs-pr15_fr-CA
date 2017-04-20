<properties
    pageTitle="Créer un jeu de disponibilité de machine virtuelle | Microsoft Azure"
    description="Apprenez à créer une disponibilité pour vos ordinateurs virtuels à l’aide d’Azure portal ou PowerShell en utilisant le modèle de déploiement du Gestionnaire de ressources."
    keywords="ensemble de disponibilité"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Créer un jeu de disponibilité 

Lors de l’utilisation du portail, si vous souhaitez que votre ordinateur virtuel faisant partie de la disponibilité, vous devez créer la disponibilité définie au préalable.

Pour plus d’informations sur la création et l’utilisation de jeux de disponibilité, voir [Gérer la disponibilité des ordinateurs virtuels](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Le portail permet de créer une disponibilité à définir avant de créer vos ordinateurs virtuels

1. Dans le menu de concentrateur, cliquez sur **Parcourir** et sélectionnez **jeux de disponibilité**.

2. Sur la **disponibilité définit la lame**, cliquez sur **Ajouter**.

    ![Capture d’écran qui affiche le bouton Ajouter pour la création de disponibilité d’une nouvelle valeur.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Sur la lame de **disponibilité de créer la valeur** , complétez les informations pour votre jeu.

    ![Capture d’écran qui affiche les informations que vous devez entrer pour créer le jeu de disponibilité.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nom** : le nom doit être composés de chiffres, lettres, périodes, des traits de soulignement et les tirets des caractères 1 à 80. Le premier caractère doit être une lettre ou un chiffre. Le dernier caractère doit être une lettre, un nombre ou un trait de soulignement.
    - **Domaines d’erreur** - domaines d’erreur définissent le groupe de machines virtuelles qui partagent un courant d’alimentation source et le commutateur réseau. Par défaut, les ordinateurs virtuels sont séparés dans les trois domaines d’erreur et peuvent être modifiées en compris entre 1 et 3.
    - **Mettre à jour les domaines** - mise à jour de cinq domaines sont affectés par défaut et cela peut être définie entre 1 et 20. Les domaines de mise à jour indiquent les groupes de machines virtuelles et le matériel physique sous-jacent qui peut être redémarré en même temps. Par exemple, si vous nous indiquez les cinq domaines, mise à jour lorsque plus de cinq ordinateurs virtuels sont configurés au sein d’un seul ensemble de disponibilité, la sixième machine virtuelle sera placé dans le même domaine de mise à jour que la première machine virtuelle, le septième dans la même UD comme le deuxième ordinateur virtuel et ainsi de suite. La commande de redémarrage de le peut ne pas être séquentielle, mais mise à jour qu’un seul domaine sera redémarré à la fois.
    - **Abonnement** , sélectionnez l’abonnement à utiliser si vous en avez plusieurs.
    - **Groupe de ressources** - sélectionnez un groupe de ressources existant en cliquant sur la flèche, puis en sélectionnant un groupe de ressources dans la liste déroulante vers le bas. Vous pouvez également créer un nouveau groupe de ressources en tapant un nom. Le nom peut contenir aucun des caractères suivants : lettres, chiffres, points, tirets, traits de soulignement et ouverture ou parenthèse fermante. Le nom ne peut pas se terminer dans une période. Tous les ordinateurs virtuels dans le groupe de disponibilité doivent être créés dans le même groupe de ressources que l’ensemble de la disponibilité.
    - **Emplacement** , sélectionnez un emplacement dans la liste déroulante.

4. Lorsque vous avez terminé entrer les informations, cliquez sur **créer**. Une fois le groupe de disponibilité a été créé, vous pouvez le voir dans la liste après avoir actualisé le portail.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Le portail permet de créer une machine virtuelle et une disponibilité de définir en même temps

Si vous créez une nouvelle machine virtuelle via le portail, vous pouvez également créer une nouveau disponibilité défini pour la machine virtuelle lors de la création de la machine virtuelle première dans le jeu.

![Capture d’écran qui illustre le processus de création d’une nouveau disponibilité définie lors de la création de la machine virtuelle.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Ajouter une machine virtuelle à un jeu existant de la disponibilité

Pour chaque machine virtuelle supplémentaire que vous créez qui doit appartiennent dans le jeu, assurez-vous que vous créez dans le même **groupe de ressources** et que vous sélectionnez la disponibilité existante définie à l’étape 3. 

![Capture d’écran montrant comment sélectionner une disponibilité existante pour utiliser pour votre machine virtuelle.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>PowerShell utilisation pour créer un jeu de disponibilité

Cet exemple crée une disponibilité définie dans le groupe de ressources **RMResGroup** à l’emplacement de **l’Ouest des États-Unis** . Cela doit être fait avant de créer la première machine virtuelle qui figurer dans le jeu.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Pour plus d’informations, consultez [New-AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Résolution des problèmes

- Lorsque vous créez une machine virtuelle, si le jeu de disponibilité souhaité ne figure pas dans la liste déroulante dans le portail peut avoir créé dans un groupe de ressources différent. Si vous ne connaissez pas le groupe de ressources pour votre disponibilité définie, allez dans le menu de concentrateur et cliquez sur Parcourir > disponibilité définit pour afficher une liste de vos jeux de disponibilité dont ils appartiennent à des groupes de ressources.


## <a name="next-steps"></a>Étapes suivantes

Ajouter un stockage supplémentaire pour votre ordinateur virtuel en ajoutant un [disque de données](virtual-machines-windows-attach-disk-portal.md).
