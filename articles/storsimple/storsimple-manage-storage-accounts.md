<properties 
   pageTitle="Gérer votre compte de stockage StorSimple | Microsoft Azure"
   description="Explique comment vous pouvez utiliser la page de configuration du Gestionnaire de StorSimple pour ajouter, modifier, supprimer ou faire pivoter les clés de sécurité d’un compte de stockage."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Le service Gestionnaire de StorSimple permet de gérer votre compte de stockage

## <a name="overview"></a>Vue d’ensemble

La page **configurer** présente tous les paramètres qui peuvent être créés dans le service Gestionnaire de StorSimple service global. Ces paramètres peuvent être appliqués à tous les périphériques connectés au service et comprennent :

- Comptes de stockage 
- Modèles de la bande passante 
- Enregistrements de contrôle d’accès 

Ce didacticiel explique comment vous pouvez utiliser la page de **configuration** pour ajouter, modifier, ou supprimer des comptes de stockage ou faire pivoter les clés de sécurité d’un compte de stockage.

 ![Configurer la page](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Les comptes de stockage contiennent les informations d’identification que le périphérique utilise pour accéder à votre compte de stockage auprès de votre fournisseur de service de cloud. Pour les comptes de stockage de Microsoft Azure, il s’agit d’informations d’identification telles que le nom de compte et la clé d’accès principal. 

Sur la page de **configuration** , tous les comptes de stockage qui sont créées pour l’abonnement de facturation sont affichées dans un format de tableau qui contient les informations suivantes :

- **Nom** : nom unique attribué au compte lors de sa création.
- **SSL activé** – si le SSL est activé et que la communication de périphérique-nuage est sur le canal sécurisé.
- **Utilisé par** : le nombre de volumes à l’aide du compte de stockage.

Associés à des comptes de stockage qui peuvent être effectuées sur la page **configurer** les tâches les plus courantes sont les suivantes :

- Ajouter un compte de stockage 
- Modifier un compte de stockage 
- Supprimer un compte de stockage 
- Rotation des clés des comptes de stockage 

## <a name="types-of-storage-accounts"></a>Types de comptes de stockage

Il existe trois types de comptes de stockage qui peuvent être utilisés avec votre périphérique StorSimple.

- **Comptes de stockage de générée automatiquement** , comme son nom l’indique, ce type de compte de stockage est généré automatiquement lorsque le service est créé. Pour plus d’informations sur la création de ce compte de stockage, reportez-vous à la section [étape 1 : créer un nouveau service](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) dans [déployer votre périphérique de StorSimple local](storsimple-deployment-walkthrough.md). 
- **Les comptes de stockage dans l’abonnement au service** – ce sont les comptes de stockage Azure qui sont associés à l’abonnement de même que celui du service. Pour en savoir plus sur le stockage de ces comptes sont créés, reportez-vous à la section [Sur les comptes de stockage Azure](../storage/storage-create-storage-account.md). 
- **Les comptes de stockage en dehors de l’abonnement au service** – ce sont les comptes de stockage Azure qui ne sont pas associées à votre service et probablement existait avant que le service a été créé.

## <a name="add-a-storage-account"></a>Ajouter un compte de stockage

Vous pouvez ajouter un compte de stockage en fournissant un nom convivial unique et les informations d’accès qui sont liées au compte de stockage (avec le fournisseur de service nuage spécifié). Vous avez également l’option d’activer le mode de secure sockets layer (SSL) pour créer un canal sécurisé pour la communication réseau entre votre périphérique et le nuage.

Vous pouvez créer plusieurs comptes pour un fournisseur de services de cloud donné. Sachez, toutefois, que, après avoir créé un compte de stockage, vous ne pouvez pas modifier le fournisseur de services de cloud.

Lors de l’enregistrement du compte de stockage, le service tente de communiquer avec votre fournisseur de services de cloud. Les informations d’identification et le matériel d’accès que vous avez fournies seront authentifiés en ce moment. Un compte de stockage est créé uniquement si l’authentification réussit. Si l’authentification échoue, un message d’erreur s’affichera.

Comptes de stockage du Gestionnaire de ressources créés dans Azure portal sont également prises en charge StorSimple. Les comptes de stockage du Gestionnaire de ressources seront afficheront pas dans la liste déroulante pour la sélection lors de la tentative de création d’un conteneur de volume, seuls les comptes de stockage créés dans Azure portal classique s’affiche. Vous devrez les comptes de stockage de gestionnaire de ressources à ajouter à l’aide de la procédure pour ajouter un compte de stockage décrit ci-dessous.

> [AZURE.NOTE] La procédure d’ajout d’un compte de stockage diffère selon la version de logiciel StorSimple que vous utilisez. Veillez à suivre la procédure appropriée à votre version de StorSimple.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modifier un compte de stockage

Vous pouvez modifier un compte de stockage qui est utilisé par un conteneur de volume. Si vous modifiez un compte de stockage qui est actuellement en cours d’utilisation, le seul champ disponible pour modifier est la clé d’accès pour le compte de stockage. Vous pouvez fournir la nouvelle clé d’accès de stockage et enregistrer les paramètres de mise à jour.

#### <a name="to-edit-a-storage-account"></a>Pour modifier un compte de stockage

1. Sur la page service de lancement, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue **Ajouter ou modifier les comptes de stockage** :

  1. Dans la liste déroulante des **Comptes de stockage**, choisissez un compte existant que vous souhaitez modifier. Cela peut également inclure les comptes de stockage qui ont été générées automatiquement lors de la création du service premier.
  2. Si nécessaire, vous pouvez modifier la sélection **d’Activer le Mode SSL** .
  3. Vous pouvez choisir de faire pivoter vos clés de l’accès de compte de stockage. Pour plus d’informations sur la façon d’effectuer la rotation des clés, reportez-vous à la section [la rotation des clés des comptes de stockage](#key-rotation-of-storage-accounts) .
  4. Cliquez sur l’icône de vérification ![Vérifiez l’icône de](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) pour enregistrer les paramètres. Sur la page **configurer** les paramètres seront actualisées. Cliquez sur **Enregistrer** pour enregistrer les paramètres qui vient d’être mis à jour.

     ![Modifier un compte de stockage](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

> [AZURE.IMPORTANT] Vous pouvez supprimer un compte de stockage uniquement si elle n’est pas utilisé par un conteneur de volume. Si un compte de stockage est utilisé par un conteneur de volume, supprimez d’abord le conteneur de volume, puis supprimez le compte de stockage associé.

#### <a name="to-delete-a-storage-account"></a>Pour supprimer un compte de stockage

1. Sur la page de lancement du service Gestionnaire de StorSimple, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Dans la liste sous forme de tableau de comptes de stockage, pointez sur le compte que vous souhaitez supprimer.

3. Une icône Supprimer (**x**) s’affiche dans la colonne de droite extrême pour ce compte de stockage. Cliquez sur l’icône **x** pour supprimer les informations d’identification.

4. Lorsque vous êtes invité à confirmer une opération, cliquez sur **Oui** pour confirmer la suppression. La liste sous forme de tableau va être mis à jour pour refléter les modifications.

## <a name="key-rotation-of-storage-accounts"></a>Rotation des clés des comptes de stockage

Pour des raisons de sécurité, la rotation des clés est souvent exigé dans les centres de données. 

> [AZURE.NOTE] Les informations suivantes de la rotation de la clé et la procédure de rotation s’appliquent aux comptes de stockage de Microsoft Azure uniquement. Si vous utilisez un autre fournisseur de services de cloud, vous pouvez gérer les clés de compte de stockage par le biais du tableau de bord de ce fournisseur.
 
Chaque abonnement Microsoft Azure peut avoir un ou plusieurs comptes de stockage associé. L’accès à ces comptes est contrôlée par les clés d’abonnement et d’accès pour chaque compte de stockage. 

Lorsque vous créez un compte de stockage, Microsoft Azure génère deux clés d’accès de stockage de 512 bits sont utilisés pour l’authentification lors de l’accès du compte de stockage. Deux clés d’accès de stockage permet de régénérer les clés sans interruption de votre service de stockage ou d’accès à ce service. La clé qui est actuellement en cours d’utilisation est la clé *primaire* et la clé de sauvegarde est appelée la clé *secondaire* . Une de ces deux clés doit être fournie lors de votre périphérique Microsoft Azure StorSimple accède à votre fournisseur de service de stockage cloud.

## <a name="what-is-key-rotation"></a>Quelle est la rotation des clés ?

En règle générale, les applications utilisent un seul des clés pour accéder à vos données. Après une certaine période de temps, vous pouvez avoir vos applications pour passer à la deuxième clé. Une fois que vous avez changé vos applications à la clé secondaire, vous pouvez retirer la première clé et puis générer une nouvelle clé. À l’aide de deux clés de cette manière permet à vos applications un accès aux données sans subir d’interruption de service.

Les clés de compte de stockage sont toujours stockées dans le service sous une forme cryptée. Toutefois, il peuvent être réinitialisées via le service Gestionnaire de StorSimple. Le service peut obtenir la clé primaire et la clé secondaire de tous les comptes de stockage dans l’abonnement même, y compris les comptes créés dans le service de stockage, ainsi que le stockage par défaut comptes générés lorsque le service de gestionnaire de StorSimple service a d’abord été créé. Le service Gestionnaire de StorSimple sera toujours obtenir ces clés à partir du portail classique Azure et les stockent sous une forme cryptée.

## <a name="rotation-workflow"></a>Workflow de rotation

Un administrateur de Microsoft Azure peut régénérer ou modifier la clé primaire ou secondaire en accédant directement à du compte de stockage (via le service Microsoft Azure Storage). Le service Gestionnaire de StorSimple ne voit pas automatiquement ce changement.

Pour informer le service Gestionnaire de StorSimple de la modification, vous avez besoin pour accéder au service Gestionnaire de StorSimple, accéder au compte de stockage et ensuite synchroniser la clé primaire ou secondaire (en fonction de celui qui a été modifié). Le service obtient la clé la plus récente, crypte les clés, puis envoie la clé chiffrée pour le périphérique.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Pour synchroniser des clés pour les comptes de stockage dans l’abonnement de même que le service (Azure uniquement)

1. Dans la page **Services** , cliquez sur l’onglet **configurer** .

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue, effectuez le des opérations suivantes :

  1. Sélectionnez le compte de stockage avec la clé que vous souhaitez synchroniser. Les clés de compte de stockage sont cryptés lorsqu’ils sont affichés.
  2. Dans le service Gestionnaire de StorSimple, vous devez mettre à jour la clé qui a été précédemment modifiée dans le service de stockage de Microsoft Azure. Si la clé d’accès principal a été modifiée (régénérée), cliquez sur **synchroniser la clé primaire**. Si la clé secondaire a été modifiée, cliquez sur **synchroniser la clé secondaire**.

    ![synchroniser les clés](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Pour synchroniser des clés pour les comptes de stockage en dehors de l’abonnement au service

1. Dans la page **Services** , cliquez sur l’onglet **configurer** .

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue, effectuez le des opérations suivantes :

  1. Sélectionnez le compte de stockage avec la clé d’accès que vous souhaitez mettre à jour.
  2. Vous devez mettre à jour la clé d’accès de stockage dans le service Gestionnaire de StorSimple. Dans ce cas, vous pouvez voir la clé d’accès de stockage. Dans la zone y de **Clé d’accès de compte de stockage**, entrez la nouvelle clé. 
  3. Enregistrez vos modifications. Votre clé d’accès de stockage compte doit maintenant être mis à jour.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la [sécurité de StorSimple](storsimple-security.md).
- En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
