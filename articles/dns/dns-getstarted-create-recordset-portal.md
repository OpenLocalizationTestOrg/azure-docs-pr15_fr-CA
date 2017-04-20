<properties
   pageTitle="Créer un jeu d’enregistrements et d’enregistrements pour une Zone DNS à l’aide du portail Azure | Microsoft Azure"
   description="Comment créer des enregistrements d’hôte pour DNS d’Azure et créer des jeux d’enregistrements et les enregistrements à l’aide du portail Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>



# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Créer des enregistrements et jeux d’enregistrements DNS à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI Azure](dns-getstarted-create-recordset-cli.md)


Cet article vous guide tout au long du processus de création d’enregistrements et jeux d’enregistrements à l’aide du portail Azure. Après avoir créé votre zone DNS, vous ajoutez les enregistrements DNS pour votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## <a name="create-a-record-set-and-record"></a>Créer un jeu d’enregistrements et l’enregistrement

L’exemple suivant vous guide tout au long du processus de création d’un enregistrement et un jeu d’enregistrements à l’aide du portail Azure. Nous allons utiliser le type d’enregistrement DNS « A ».

1. Ouvrez une session sur le portail.

2. Accédez à la lame de **zone DNS** dans lequel vous souhaitez créer un jeu d’enregistrements.

3. En haut de la lame de **zone DNS** , sélectionnez le **jeu d’enregistrements** pour ouvrir la lame du **jeu d’enregistrements d’ajouter** .

    ![Nouveau jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Sur la lame de **jeu d’enregistrements de l’ajouter** , nommez votre jeu d’enregistrements. Par exemple, vous pouvez nommer votre jeu d’enregistrements «**www**».

    ![Ajouter le jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Sélectionnez le type d’enregistrement que vous souhaitez créer. Par exemple, sélectionnez **A**.

6. Définir la **durée de vie**. La durée de vie dans le portail défaut est d’une heure.

7. Ajoutez les adresses IP, une adresse IP par ligne. Lorsque vous utilisez le nom du jeu d’enregistrements suggérées et le type d’enregistrement décrite plus haut, vous ajoutez les adresses IP IPv4 vers l’enregistrement **A** pour le jeu d’enregistrements de www.

8. Après avoir ajouté les adresses IP, cliquez sur **OK** au bas de la lame. Le jeu d’enregistrements DNS sera créé.


## <a name="next-steps"></a>Étapes suivantes

Pour gérer votre jeu d’enregistrements et des enregistrements, voir [Gérer les DNS des enregistrements et enregistrement définit à l’aide du portail Azure](dns-operations-recordsets-portal.md).

Pour plus d’informations sur le service DNS d’Azure, consultez la [vue d’ensemble de DNS d’Azure](dns-overview.md).
