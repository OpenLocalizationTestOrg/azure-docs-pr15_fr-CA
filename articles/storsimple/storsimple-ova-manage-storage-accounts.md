<properties 
   pageTitle="Gérer votre compte de stockage StorSimple | Microsoft Azure"
   description="Explique comment vous pouvez utiliser la page de configuration du Gestionnaire de StorSimple d’ajouter, de modifier, de supprimer ou de faire pivoter les clés de sécurité d’un compte de stockage associé à la baie virtuelle StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Le service Gestionnaire de StorSimple permet de gérer les comptes de stockage pour le tableau virtuel de StorSimple

## <a name="overview"></a>Vue d’ensemble

La page de **configuration** présente les paramètres de service global qui peuvent être créés dans le service Gestionnaire de StorSimple. Ces paramètres peuvent être appliqués à tous les périphériques connectés au service et comprennent :

- Comptes de stockage 
- Enregistrements de contrôle d’accès 

Ce didacticiel explique comment vous pouvez utiliser la page de **configuration** pour ajouter, modifier ou supprimer des comptes de stockage pour votre tableau virtuel StorSimple. Les informations contenues dans ce didacticiel ne s’applique qu’au tableau virtuel StorSimple mars 2016 GA version du logiciel en cours d’exécution.

 ![Configurer la page](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Les comptes de stockage contiennent les informations d’identification que le périphérique utilise pour accéder à votre compte de stockage auprès de votre fournisseur de service de cloud. Pour les comptes de stockage de Microsoft Azure, il s’agit d’informations d’identification telles que le nom de compte et la clé d’accès principal. 

Sur la page de **configuration** , tous les comptes de stockage qui sont créées pour l’abonnement de facturation sont affichées dans un format de tableau qui contient les informations suivantes :

- **Nom** : nom unique attribué au compte lors de sa création.
- **SSL activé** – si le SSL est activé et que la communication de périphérique-nuage est sur le canal sécurisé.

Associés à des comptes de stockage qui peuvent être effectuées sur la page **configurer** les tâches les plus courantes sont les suivantes :

- Ajouter un compte de stockage 
- Modifier un compte de stockage 
- Supprimer un compte de stockage 


## <a name="types-of-storage-accounts"></a>Types de comptes de stockage

Il existe trois types de comptes de stockage qui peuvent être utilisés avec votre périphérique StorSimple.

- **Comptes de stockage de générée automatiquement** , comme son nom l’indique, ce type de compte de stockage est généré automatiquement lorsque le service est créé. Pour plus d’informations sur la création de ce compte de stockage, voir [Création d’un nouveau service](storsimple-ova-manage-service.md#create-a-service). 
- **Les comptes de stockage dans l’abonnement au service** – ce sont les comptes de stockage Azure qui sont associés à l’abonnement de même que celui du service. Pour en savoir plus sur le stockage de ces comptes sont créés, reportez-vous à la section [Sur les comptes de stockage Azure](../storage/storage-create-storage-account.md). 
- **Les comptes de stockage en dehors de l’abonnement au service** – ce sont les comptes de stockage Azure qui ne sont pas associées à votre service et probablement existait avant que le service a été créé.

Chaque baie virtuelle StorSimple crée un conteneur (avec un hcs préfixe) dans le compte de stockage associé. Ce conteneur possède toutes les données de nuage pour votre périphérique. Ne supprimez pas ce conteneur en y accédant via le service de stockage Azure que cette action entraîne la perte de données.

## <a name="add-a-storage-account"></a>Ajouter un compte de stockage

Vous pouvez ajouter un compte de stockage pour la configuration de votre service de gestionnaire de StorSimple en fournissant un nom convivial unique et les informations d’accès qui sont liées au compte de stockage. Vous avez également l’option d’activer le mode de secure sockets layer (SSL) pour créer un canal sécurisé pour la communication réseau entre votre périphérique et le nuage.

Vous pouvez créer plusieurs comptes pour un fournisseur de services de cloud donné. Lors de l’enregistrement du compte de stockage, le service tente de communiquer avec votre fournisseur de services de cloud. Les informations d’identification et le matériel d’accès que vous avez fournies seront authentifiés en ce moment. Un compte de stockage est créé uniquement si l’authentification réussit. Si l’authentification échoue, un message d’erreur s’affichera.

Comptes de stockage du Gestionnaire de ressources créés dans Azure portal sont également prises en charge StorSimple. Les comptes de stockage du Gestionnaire de ressources ne s’affichera pas dans la liste déroulante pour la sélection, seul le stockage comptes créés dans le portail classique Azure seront affiche. Vous devrez les comptes de stockage de gestionnaire de ressources à ajouter à l’aide de la procédure pour ajouter un compte de stockage, comme décrit ci-dessous.

La procédure d’ajout d’un compte de stockage classique Azure est détaillée ci-dessous.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modifier un compte de stockage

Vous pouvez modifier un compte de stockage utilisé par votre périphérique. Si vous modifiez un compte de stockage qui est actuellement en cours d’utilisation, les champs disponibles à modifier sont la clé d’accès et le mode SSL pour le compte de stockage. Vous pouvez fournir le nouveau stockage touche d’accès rapide ou modifier la sélection du **mode d’activer SSL** et enregistrer les paramètres de mise à jour.

#### <a name="to-edit-a-storage-account"></a>Pour modifier un compte de stockage

1. Sur la page service de lancement, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue **Ajouter ou modifier les comptes de stockage** :

  1. Dans la liste déroulante des **Comptes de stockage**, choisissez un compte existant que vous souhaitez modifier. 
  2. Si nécessaire, vous pouvez modifier la sélection **d’Activer le Mode SSL** .
  3. Vous pouvez choisir de régénérer les touches d’accès compte votre stockage. Pour plus d’informations, reportez-vous à [Régénérer les clés de compte de stockage](storage-create-storage-account.md#manage-your-storage-access-keys). Fournir la nouvelle clé de compte de stockage. Pour un compte de stockage Azure, il s’agit de la clé d’accès principal. 
  4. Cliquez sur l’icône de vérification ![Vérifiez l’icône de](./media/storsimple-ova-manage-storage-accounts/checkicon.png) pour enregistrer les paramètres. Sur la page **configurer** les paramètres seront actualisées. 
  5. En bas de la page, cliquez sur **Enregistrer** pour enregistrer les paramètres qui vient d’être mis à jour. 

     ![Modifier un compte de stockage](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

> [AZURE.IMPORTANT] Vous pouvez supprimer un compte de stockage uniquement si elle n’est pas en cours d’utilisation. Si un compte de stockage est en cours d’utilisation, vous seront communiquées.

#### <a name="to-delete-a-storage-account"></a>Pour supprimer un compte de stockage

1. Sur la page de lancement du service Gestionnaire de StorSimple, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Dans la liste sous forme de tableau de comptes de stockage, pointez sur le compte que vous souhaitez supprimer.

3. Une icône Supprimer (**x**) s’affiche dans la colonne de droite extrême pour ce compte de stockage. Cliquez sur l’icône **x** pour supprimer les informations d’identification.

4. Lorsque vous êtes invité à confirmer une opération, cliquez sur **Oui** pour confirmer la suppression. La liste sous forme de tableau va être mis à jour pour refléter les modifications.

5. En bas de la page, cliquez sur **Enregistrer** pour enregistrer les paramètres qui vient d’être mis à jour.


## <a name="next-steps"></a>Étapes suivantes

- Apprenez comment [administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
