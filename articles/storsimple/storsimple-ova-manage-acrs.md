<properties 
   pageTitle="Gérer les enregistrements de contrôle d’accès pour le tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit comment gérer des enregistrements de contrôle d’accès (ACRs) afin de déterminer quels hôtes peuvent se connecter à un volume sur le tableau virtuel StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Le service Gestionnaire de StorSimple permet de gérer les enregistrements de contrôle d’accès pour le tableau virtuel StorSimple 

## <a name="overview"></a>Vue d’ensemble

Enregistrements de contrôle d’accès (ACRs) vous permettent de spécifier quels hôtes peuvent se connecter à un volume sur le tableau virtuel de StorSimple (également connu comme le StorSimple sur site périphérique virtuel). ACRs sont définis pour un volume spécifique et contient les noms qualifiés iSCSI (IQN) des hôtes. Lorsqu’un hôte tente de se connecter à un volume, le périphérique vérifie l’ACR associé à ce volume pour le nom IQN, et s’il existe une correspondance, la connexion est établie. La section des **enregistrements de contrôle d’accès** sur la page **configurer** affiche tous les enregistrements de contrôle d’accès avec l’IQN correspondante des hôtes.

Ce didacticiel explique les tâches courantes liées à blocage suivantes :

- Obtenir le nom qualifié
- Ajouter un enregistrement de contrôle d’accès 
- Modification d’un enregistrement de contrôle d’accès 
- Supprimer un enregistrement de contrôle d’accès 

> [AZURE.IMPORTANT] 
> 
> - Lorsque vous affectez un blocage à un volume, veillez à ce que le volume n’est pas simultanément accessible par plusieurs hôtes non-cluster, car cela pourrait endommager le volume. 
> - Lors de la suppression d’un blocage d’un volume, assurez-vous que l’hôte correspondant n'est pas accéder au volume, car la suppression pourrait entraîner une interruption en lecture-écriture.

## <a name="get-the-iqn"></a>Obtenir le nom qualifié

Effectuez les opérations suivantes pour obtenir le nom qualifié d’un hôte Windows qui exécute Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Ajouter un blocage

La page de **Configuration** du service Gestionnaire de StorSimple vous permet d’ajouter des ACRs. En général, vous associez un blocage à un volume.

Pour plus d’informations sur l’association d’un blocage avec un volume, passez à [Ajout d’un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Lorsque vous affectez un blocage à un volume, veillez à ce que le volume n’est pas simultanément accessible par plusieurs hôtes non-cluster, car cela pourrait endommager le volume.
 
Procédez comme suit pour ajouter un blocage.

#### <a name="to-add-an-acr"></a>Pour ajouter un blocage

1. Sur la page service de lancement, sélectionnez votre service, double-cliquez sur le nom du service et puis cliquez sur l’onglet **Configuration** .

    ![onglet Configuration](./media/storsimple-ova-manage-acrs/acr1.png)

2. Dans la liste sous forme de tableau dans les **enregistrements de contrôle d’accès**, fournir un **nom** pour votre ACR.

3. Sous **nom de l’initiateur iSCSI**, fournissez le nom IQN de votre hôte Windows. 

4. Cliquez sur **Enregistrer** en bas de la page pour enregistrer l’ACR nouvellement créé. Le message de confirmation suivant s’affiche.

    ![message de confirmation](./media/storsimple-ova-manage-acrs/acr2.png)

5. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-manage-acrs/check-icon.png). La liste sous forme de tableau sera mis à jour pour tenir compte de cet ajout.

## <a name="edit-an-acr"></a>Modifier un blocage

La page **Configuration** du portail classique Azure vous permet de modifier les ACRs. 

> [AZURE.NOTE] Vous devez modifier uniquement ces ACRs qui ne sont pas en cours d’utilisation. Pour modifier un blocage associé à un volume qui est actuellement en cours d’utilisation, vous devez tout d’abord déconnecter le volume.

Effectuez les opérations suivantes pour modifier un blocage.

#### <a name="to-edit-an-acr"></a>Pour modifier un blocage

1. Sur la page service de lancement, sélectionnez votre service, double-cliquez sur le nom du service et puis cliquez sur l’onglet **Configuration** .

2. Dans la liste sous forme de tableau, les enregistrements de contrôle d’accès, survolez le blocage que vous souhaitez modifier.

3. Fournir un nouveau nom et/ou le nom qualifié de l’ACR.

4. Cliquez sur **Enregistrer** en bas de la page pour enregistrer l’ACR modifié. Vous verrez un message de confirmation. 

5. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-manage-acrs/check-icon.png). La liste sous forme de tableau va être mis à jour pour refléter cette modification.

## <a name="delete-an-access-control-record"></a>Supprimer un enregistrement de contrôle d’accès

La page **Configuration** du portail classique Azure vous permet de supprimer les ACRs. 

> [AZURE.NOTE] 
> 
> - Vous devez supprimer uniquement les ACRs qui ne sont pas en cours d’utilisation. Pour supprimer un blocage associé à un volume qui est actuellement en cours d’utilisation, vous devez tout d’abord déconnecter le volume.
> - Lors de la suppression d’un blocage d’un volume, assurez-vous que l’hôte correspondant n'est pas accéder au volume, car la suppression pourrait entraîner une interruption en lecture-écriture.

Effectuez les étapes suivantes pour supprimer un enregistrement de contrôle d’accès.

#### <a name="to-delete-an-access-control-record"></a>Pour supprimer un enregistrement de contrôle d’accès

1. Sur la page service de lancement, sélectionnez votre service, double-cliquez sur le nom du service et puis cliquez sur l’onglet **Configuration** .

2. Dans la liste sous forme de tableau, les enregistrements de contrôle d’accès (ACRs), survolez le blocage que vous souhaitez supprimer.

3. Une icône Supprimer (**x**) s’affiche dans la colonne de droite extrême pour le blocage que vous sélectionnez. Cliquez sur l’icône **x** pour supprimer le blocage. Le message de confirmation suivant s’affiche.

    ![message de confirmation](./media/storsimple-ova-manage-acrs/acr3.png)

5. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-manage-acrs/check-icon.png). La liste sous forme de tableau va être mis à jour pour refléter la suppression.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’Ajout de volumes et de configuration ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).
