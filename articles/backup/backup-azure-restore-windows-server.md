<properties
   pageTitle="Restaurer des données à un Client Windows ou de Windows Server à partir d’Azure en utilisant le modèle de déploiement du Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à restaurer à partir d’un Client Windows ou de Windows Server."
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
     ms.date="08/02/2016"
     ms.author="trinadhk; jimpark; markgal;"/>

# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restaurer les fichiers vers un Windows server ou un ordinateur de client Windows à l’aide du Gestionnaire de ressources du modèle de déploiement

> [AZURE.SELECTOR]
- [Azure portal](backup-azure-restore-windows-server.md)
- [Portail classique](backup-azure-restore-windows-server-classic.md)

Cet article décrit les étapes nécessaires pour effectuer les deux types d’opérations de restauration :

- Restaurer les données sur le même ordinateur que celui sur lequel les sauvegardes ont été prélevés.
- Restaurer les données sur une autre machine.

Dans les deux cas, les données sont récupérées à partir du coffre-fort de Services de récupération Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

## <a name="recover-data-to-the-same-machine"></a>Restaurer des données sur le même ordinateur
Si vous avez accidentellement supprimé un fichier et que vous souhaitez restaurer sur le même ordinateur (à partir de laquelle est effectuée la sauvegarde), les étapes suivantes vous permettra de récupérer les données.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** .
2. Cliquez sur **Récupérer les données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-restore-windows-server/recover.png)

3. Sélectionnez le * *ce serveur (*yourmachinename*) ** option pour restaurer les données sauvegardées fichier sur la même machine.

    ![Même machine](./media/backup-azure-restore-windows-server/samemachine.png)

4. Choisissez cette option pour **Rechercher des fichiers** ou **Rechercher des fichiers**.

    Laissez l’option par défaut si vous souhaitez restaurer un ou plusieurs fichiers dont le chemin est connu. Si vous n’êtes pas sûr à propos de la structure de dossiers mais que vous souhaitez rechercher un fichier, choisissez l’option de **recherche des fichiers** . Dans cette section, nous continuerons avec l’option par défaut.

    ![Parcourir les fichiers](./media/backup-azure-restore-windows-server/browseandsearch.png)

5. Sélectionnez le volume à partir duquel vous souhaitez restaurer le fichier.

    Vous pouvez restaurer à partir de n’importe quel point dans le temps. Les dates qui apparaissent en **gras** dans le contrôle calendar permet d’indiquer la disponibilité d’un point de restauration. Une fois qu’une date est sélectionnée, en fonction de votre planification de sauvegarde (et la réussite d’une opération de sauvegarde), vous pouvez sélectionner un point dans le temps dans la liste déroulante **heure** vers le bas.

    ![Volume et la Date](./media/backup-azure-restore-windows-server/volanddate.png)

6. Sélectionnez les éléments à récupérer. Vous pouvez sélections plusieurs fichiers que vous souhaitez restaurer.

    ![Sélectionnez les fichiers](./media/backup-azure-restore-windows-server/selectfiles.png)

7. Spécifiez les paramètres de récupération.

    ![Options de récupération](./media/backup-azure-restore-windows-server/recoveroptions.png)

  - Vous disposez d’une option de restauration à l’emplacement d’origine (dans lequel le fichier/dossier seraient remplacé) ou vers un autre emplacement dans le même ordinateur.
  - Si le dossier que vous souhaitez restaurer existe dans l’emplacement cible, vous pouvez créer des copies (deux versions du même fichier), remplacer les fichiers dans l’emplacement cible ou ignorer la restauration des fichiers qui existent dans la cible.
  - Il est fortement recommandé que vous laissez l’option par défaut de la restauration des ACL sur les fichiers qui sont en cours de récupération.

8. Une fois que ces entrées sont fournies, cliquez sur **suivant**. Le flux de travail de récupération qui restaure les fichiers sur cet ordinateur, commencera.

## <a name="recover-to-an-alternate-machine"></a>Récupérer à un autre ordinateur
Si l’intégralité du serveur est perdu, vous pouvez toujours récupérer des données à partir de la sauvegarde d’Azure vers une autre machine. Les étapes suivantes illustrent le flux de travail.  

La terminologie utilisée dans ces étapes comprend :

- *Machine source* – l’ordinateur d’origine à partir de laquelle la sauvegarde a été effectuée et qui n’est pas disponible.
- *L’ordinateur cible* – la machine à laquelle les données sont restaurées.
- *Coffre-fort d’exemple* – coffre-fort les Services de récupération auquel la *machine Source* et *cible* sont enregistrés. <br/>

> [AZURE.NOTE] Les sauvegardes effectuées à partir d’un ordinateur ne peut pas être restaurées sur un ordinateur qui exécute une version antérieure du système d’exploitation. Par exemple, si les sauvegardes sont effectuées à partir d’un ordinateur Windows 7, elle peut être restaurée sur un Windows 8 ou au-dessus de machine. Toutefois l’inverse n’est pas vraie.

1. Ouvrez le composant logiciel enfichable de **Microsoft Azure Backup** dans sur l' *ordinateur cible*.
2. Assurez-vous que l' *ordinateur cible* et l' *ordinateur Source* sont enregistrés dans le coffre-fort de Services de récupération de même.
3. Cliquez sur **Récupérer les données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-restore-windows-server/recover.png)

4. Sélectionnez **un autre serveur**

    ![Un autre serveur](./media/backup-azure-restore-windows-server/anotherserver.png)

5. Fournir le fichier d’informations d’identification de chambre forte qui correspond dans le *coffre-fort de l’échantillon*. Si le fichier d’informations d’identification de coffre-fort est non valide (ou expirés) Télécharger un nouveau fichier d’informations d’identification de coffre-fort dans le *coffre-fort de l’échantillon* dans le portail Azure. Une fois le fichier d’informations d’identification de coffre-fort est fourni, le coffre-fort de Services de récupération par rapport au fichier d’informations d’identification de coffre-fort s’affiche.

6. Sélectionnez la *machine Source* dans la liste des ordinateurs affichées.

    ![Liste des ordinateurs](./media/backup-azure-restore-windows-server/machinelist.png)

7. Sélectionnez l’option **Rechercher des fichiers** ou **Rechercher des fichiers** . Dans cette section, nous allons utiliser l’option de **recherche des fichiers** .

    ![Recherche](./media/backup-azure-restore-windows-server/search.png)

8. Dans l’écran suivant, sélectionnez le volume et la date. Recherchez le nom de fichier/dossier que vous souhaitez restaurer.

    ![Éléments de recherche](./media/backup-azure-restore-windows-server/searchitems.png)

9. Sélectionnez l’emplacement où les fichiers doivent être restaurés.

    ![Emplacement de restauration](./media/backup-azure-restore-windows-server/restorelocation.png)

10. Fournir le mot de passe de chiffrement qui a été fourni lors de l’inscription de *l’ordinateur Source* au *coffre-fort de l’échantillon*.

    ![Cryptage](./media/backup-azure-restore-windows-server/encryption.png)

11. Une fois que l’entrée est fournie, cliquez sur **récupérer**, qui déclenche la restauration de fichiers sauvegardés à la destination prévues.

## <a name="next-steps"></a>Étapes suivantes
- Maintenant que vous avez récupéré vos fichiers et dossiers, vous pouvez [gérer vos sauvegardes](backup-azure-manage-windows-server.md).
