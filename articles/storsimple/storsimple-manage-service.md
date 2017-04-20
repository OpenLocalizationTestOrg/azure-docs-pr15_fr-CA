<properties 
   pageTitle="Déployer le service Gestionnaire de StorSimple | Microsoft Azure"
   description="Explique comment créer et supprimer le service de gestionnaire de StorSimple dans le portail classique Azure et explique comment gérer la clé de l’enregistrement du service."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-manager-service"></a>Déployer le service Gestionnaire de StorSimple

## <a name="overview"></a>Vue d’ensemble

Le service Gestionnaire de StorSimple s’exécute dans Microsoft Azure et se connecte à plusieurs périphériques StorSimple. Après avoir créé le service, vous pouvez l’utiliser pour gérer les périphériques à partir du portail classique Microsoft Azure s’exécutant dans un navigateur. Cela vous permet de surveiller tous les périphériques qui sont connectés au service du Gestionnaire de StorSimple à partir d’un emplacement central unique, ce qui réduit la charge administrative.

La page de lancement du Gestionnaire de StorSimple répertorie tous les services que vous pouvez utiliser pour gérer vos périphériques de stockage de StorSimple Gestionnaire de StorSimple. Pour chaque service de gestionnaire de StorSimple, les informations suivantes sont présentées sur la page Gestionnaire de StorSimple :

- **Nom** : le nom affecté à votre service de gestionnaire de StorSimple lorsqu’il a été créé. Le nom du service ne peut pas être modifié une fois que le service est créé.

- **Statut** : le statut du service, qui peut être **Active**, **Création**ou **en ligne**.

- **Emplacement** – l’emplacement géographique dans lequel le périphérique StorSimple sera déployé.

- **Abonnement** : l’abonnement de facturation associé à votre service.

Les tâches courantes qui peuvent être effectuées par le biais de la page du Gestionnaire de StorSimple sont les suivantes :

- Création d’un service
- Supprimer un service
- Obtention de la clé d’inscription de service
- Régénérer la clé d’inscription de service

Ce didacticiel explique comment effectuer chacune de ces tâches.

## <a name="create-a-service"></a>Création d’un service

Utilisez l’option de **Création rapide** pour créer un service de gestionnaire de StorSimple si vous souhaitez déployer votre périphérique StorSimple. Pour créer un service, vous devez disposer :

- Un abonnement avec un accord d’entreprise
- Un compte de stockage actif Microsoft Azure
- Les informations de facturation qui sont utilisées pour la gestion des accès

Vous pouvez également générer un compte de stockage par défaut lorsque vous créez le service.

Un seul service peut gérer plusieurs périphériques. Toutefois, un périphérique ne peut pas s’étendre sur plusieurs services. Une grande entreprise peut avoir plusieurs instances de service pour travailler avec différents abonnements, une organisation ou même les emplacements de déploiement. Notez que vous devez séparer les instances du service StorSimple Manager pour gérer les périphériques série 8000 de StorSimple et StorSimple des réseaux virtuels.

Procédez comme suit pour créer un service.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>Supprimer un service

Avant de supprimer un service, assurez-vous qu’aucun périphérique connecté n’est l’aide. Si le service est en cours d’utilisation, désactiver les périphériques connectés. L’opération de désactiver sera la connexion entre le périphérique et le service de serveur, mais conserver les données de périphérique dans le nuage. 

[AZURE.IMPORTANT] Après la suppression d’un service, l’opération ne peut pas être annulée. N’importe quel périphérique qui utilise le service devez être réinitialisé avant de pouvoir être utilisé avec un autre service de fabrique. Dans ce scénario, les données locales sur le périphérique, ainsi que la configuration, seront perdues.

Effectuez les étapes suivantes pour supprimer un service.

### <a name="to-delete-a-service"></a>Pour supprimer un service

1. Sur la page **Gestionnaire de StorSimple service** , sélectionnez le service que vous souhaitez supprimer.

1. Cliquez sur **Supprimer** au bas de la page.

1. Cliquez sur **Oui** dans la notification de la confirmation. Il peut prendre quelques minutes pour le service à supprimer.

## <a name="get-the-service-registration-key"></a>Obtention de la clé d’inscription de service

Une fois que vous avez créé un service, vous devez enregistrer votre dispositif de StorSimple avec le service. Pour enregistrer votre premier périphérique StorSimple, vous aurez besoin de la clé d’inscription du service. Pour enregistrer des périphériques supplémentaires avec un StorSimple service existant, vous devez à la fois la clé d’enregistrement et la clé de cryptage de données de service (ce qui est générée sur le premier périphérique lors de l’inscription). Pour plus d’informations sur la clé de cryptage de données de service, reportez-vous à la section [sécurité de StorSimple](storsimple-security.md). Vous pouvez obtenir la clé d’enregistrement en accédant à la **Clé d’enregistrement** dans la page **Services** .

Effectuez les opérations suivantes pour obtenir la clé d’inscription du service.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Conserver la clé d’inscription du service dans un endroit sûr. Vous aurez besoin de cette clé, ainsi que la clé de cryptage de données de service, inscrire des périphériques supplémentaires auprès de ce service. Après avoir obtenu la clé d’inscription du service, vous devez configurer votre périphérique par le biais du Windows PowerShell pour l’interface de StorSimple.

Pour plus d’informations sur l’utilisation de cette clé d’enregistrement, consultez [étape 3 : configurer et inscrire le périphérique par le biais de Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-2-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Régénérer la clé d’inscription de service

Vous devez recréer une clé d’enregistrement de service si vous êtes amené à effectuer la rotation de la clé ou si la liste des administrateurs de service ont changé. Lorsque vous régénérez la clé, la nouvelle clé est utilisée uniquement pour l’enregistrement des périphériques suivants. Les périphériques qui ont été déjà enregistrés ne sont pas affectés par ce processus.

Effectuez les étapes suivantes pour générer une clé d’enregistrement de service.

### <a name="to-regenerate-the-service-registration-key"></a>Pour régénérer la clé d’inscription de service

1. Dans la page **Gestionnaire de StorSimple service** , cliquez sur **La clé d’enregistrement**.

1. Dans la boîte de dialogue **Clé de Service d’enregistrement** , cliquez sur **Régénérer**.

1. Vous verrez un message de confirmation. Cliquez sur **OK** pour poursuivre la régénération.

1. Une nouvelle clé d’enregistrement de service s’affiche.

1. Copiez cette clé et l’enregistrer pour enregistrer les nouveaux périphériques grâce à ce service.

1. Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-manage-service/HCS_CheckIcon.png) pour fermer cette boîte de dialogue.


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le [processus de déploiement de StorSimple](storsimple-deployment-walkthrough.md).

- Pour en savoir plus sur la [gestion de votre compte de stockage StorSimple](storsimple-manage-storage-accounts.md).

- Pour en savoir plus sur la façon [d’utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

 
