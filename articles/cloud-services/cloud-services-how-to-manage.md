<properties 
    pageTitle="Tâches courantes de cloud service gestion (standard) | Microsoft Azure" 
    description="Apprenez à gérer les services en nuage dans Azure portal classique." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/10/2016"
    ms.author="adegeo"/>





# <a name="how-to-manage-cloud-services"></a>La gestion des Services en nuage

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-manage-portal.md)
- [Azure portal classique](cloud-services-how-to-manage.md)

Dans la zone **Services en nuage** du portail Azure classique, vous pouvez mettre à jour le rôle d’un service ou d’un déploiement, promouvoir un déploiement progressif en production, lier des ressources à votre service cloud de sorte que vous pouvez afficher les dépendances de ressource et mettre à l’échelle les ressources entre eux et supprimer un déploiement ou un service en nuage.


## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Comment : mettre à jour un déploiement ou un rôle de service cloud

Si vous devez mettre à jour le code d’application pour votre service cloud, utilisez **mise à jour** sur le tableau de bord, page des **Services en nuage** ou page **d’Instances** . Vous pouvez mettre à jour un seul rôle ou tous les rôles. Vous devez télécharger un package de service et d’un fichier de configuration de service.

1. Dans [Azure portal classique](https://manage.windowsazure.com/), sur le tableau de bord, page des **Services en nuage** ou **Instances de** page, cliquez sur **mise à jour**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. Dans **étiquette de déploiement**, entrez un nom pour identifier le déploiement (par exemple, mycloudservice4). Vous trouverez l’étiquette de déploiement sous la **main** sur le tableau de bord.

3. Dans le **Package**, utilisez **Parcourir** pour charger le fichier de package de service (.cspkg).

4. Dans la **Configuration**, utilisez **Parcourir** pour charger le fichier de configuration de service (.cscfg).

5. Dans **rôle**, sélectionnez **tous** si vous voulez mettre à niveau tous les rôles dans le service cloud. Pour effectuer une mise à jour du rôle, sélectionnez le rôle que vous souhaitez mettre à jour. Même si vous sélectionnez un rôle spécifique pour mettre à jour, les mises à jour dans le fichier de configuration de service sont appliqués à tous les rôles.

6. Si la mise à jour modifie le nombre de rôles ou de la taille de n’importe quel rôle, activez la case à cocher **Autoriser mise à jour si des tailles de rôle ou le nombre de rôles est modifié** pour activer la mise à jour continuer. 

    Sachez que si vous modifiez la taille d’un rôle (autrement dit, la taille d’une machine virtuelle qui héberge une instance du rôle) ou le nombre de rôles, chaque instance de rôle (machine virtuelle) doit être de nouveau mis en image, et toutes les données locales seront perdues.

7. Si les rôles service n'ont qu’une seule instance de rôle, sélectionnez la **mise à jour même si une ou plusieurs des rôles contiennent une seule case à cocher** pour activer la mise à niveau continuer. 

    Azure peut garantir la disponibilité de service 99,95 % au cours d’une mise à jour du service de cloud uniquement si chaque rôle a au moins deux instances de rôle (machines virtuelles). Qui permet à un ordinateur virtuel traiter les demandes de client alors que l’autre est en cours de mise à jour.

8. Cliquez sur **OK** (coche) pour démarrer le service de mise à jour.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Comment : permuter des déploiements à promouvoir un déploiement par étapes de la production

Permet de **Permuter** promouvoir un déploiement intermédiaire d’un service de nuage à la production. Lorsque vous décidez de déployer une nouvelle version d’un service en nuage, vous pouvez préparer et tester votre nouvelle version dans votre environnement de test de service cloud tandis que vos clients utilisent la version en cours de production. Lorsque vous êtes prêt à promouvoir la nouvelle version de production, vous pouvez utiliser la commande **Permuter** pour passer l’URL adressés par lequel les deux déploiements. 

Vous pouvez permuter les déploiements à partir de la page des **Services en nuage** ou le tableau de bord.

1. Dans [Azure portal classique](https://manage.windowsazure.com/), cliquez sur **Services dans le nuage**.

2. Dans la liste des services en nuage, cliquez sur le service en nuage pour la sélectionner.

3. Cliquez sur **Permuter**.

    Le message de confirmation suivant s’ouvre.

    ![Échange de Services de cloud](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Après avoir vérifié les informations de déploiement, cliquez sur **Oui** pour remplacer les déploiements.

    L’échange de déploiement se produit rapidement, car la seule chose qui change est les adresses IP virtuelles (VIP) pour les déploiements.

    Pour enregistrer les coûts de calcul, vous pouvez supprimer le déploiement dans l’environnement intermédiaire lorsque vous êtes sûr que le nouveau déploiement de production s’exécute comme prévu.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Comment : lier une ressource à un service en nuage

Pour afficher les dépendances du service votre nuage sur d’autres ressources, vous pouvez lier une instance de base de données de SQL Azure ou un compte de stockage pour le service en nuage. Vous pouvez lier et supprimer le lien de ressources sur la page de **Ressources liées** et puis surveiller leur utilisation sur le tableau de bord de service cloud. Si un compte de stockage a la surveillance est activée, vous pouvez surveiller le nombre Total de demandes sur le tableau de bord de service cloud.

Cliquez sur **lien** pour lier un compte du stockage ou instance du SQL de base de données nouvel ou existant à votre service cloud. Vous pouvez ensuite mettre à l’échelle la base de données ainsi que le rôle de service de cloud qui l’utilise sur la page **d’échelle** . (Un compte de stockage met à l’échelle automatiquement en tant que l’utilisation augmente.) Pour plus d’informations, consultez [comment mettre un Service en nuage et les ressources liées à l’échelle](cloud-services-how-to-scale.md). 

Vous pouvez également surveiller, gérer et mettre à l’échelle la base de données dans le nœud **bases de données** du portail Azure classique. 

« Liaison » d’une ressource en ce sens ne connecter votre application à la ressource. Si vous créez une nouvelle base de données à l’aide du **lien**, vous devez ajouter les chaînes de connexion à votre code d’application et ensuite mettre à niveau le service en nuage. Vous devrez également ajouter des chaînes de connexion si votre application utilise des ressources dans un compte de stockage.

La procédure suivante décrit comment lier une nouvelle instance de la base de données SQL, déployée sur un serveur de base de données SQL, à un service cloud.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Pour lier une instance de base de données SQL à un service cloud

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Services dans le nuage**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2. Cliquez sur **ressources liées**.

    La page de **Ressources liées** s’ouvre.

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Cliquez sur **une ressource de liaison** ou de **lien**.

    L’Assistant **Lien ressources** démarre.

    ![Lien Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Cliquez sur **créer une nouvelle ressource** ou **lier une ressource existante**.

5. Choisissez le type de ressource à lier. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Base de données SQL**. (Le portail classique aperçu Azure ne gère pas la liaison d’un compte de stockage à un service cloud.)

6. Pour terminer la configuration de la base de données, suivez les instructions dans l’aide de la zone de **Bases de données SQL** du portail Azure classique.

    Vous pouvez suivre la progression de l’opération d’attache dans la zone de message.

    ![Avancement de la liaison](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

    Lorsque la liaison est terminée, vous pouvez surveiller l’état de la ressource liée sur le tableau de bord de service cloud. Pour plus d’informations sur la mise à l’échelle d’une base de données SQL liée, voir [Comment faire évoluer un Service en nuage et les ressources liées](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>Pour supprimer une ressource liée

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Services dans le nuage**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2. Cliquez sur **Ressources liées**et sélectionnez la ressource.

3. Cliquez sur **Supprimer le lien**. Cliquez ensuite sur **Oui** à l’invite de confirmation.

    Suppression du lien d’une base de données SQL n’a aucun effet sur la base de données ou les connexions de l’application à la base de données. Vous pouvez gérer la base de données dans la zone de **Bases de données SQL** du portail Azure classique.



## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Comment : supprimer un service en nuage et les déploiements

Avant de supprimer un service en nuage, vous devez supprimer chaque déploiement existant.

Pour enregistrer les coûts de calcul, vous pouvez supprimer votre déploiement intermédiaire après avoir vérifié que votre déploiement de production fonctionne comme prévu. Vous êtes compute facturé des coûts pour les instances de rôle même si un service en nuage n’est pas en cours d’exécution.

Utilisez la procédure suivante pour supprimer un déploiement ou votre service cloud. 

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Services dans le nuage**.

2. Sélectionnez le service cloud, puis cliquez sur **Supprimer**. (Pour sélectionner un service cloud sans ouvrir le tableau de bord, cliquez n’importe où, sauf le nom dans l’entrée de service nuage.)

    Si vous avez un déploiement de transit ou de production, vous verrez un menu de choix similaire à la suivante au bas de la fenêtre. Avant de supprimer le service en nuage, vous devez supprimer tous les déploiements existants.

    ![Supprimer le Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Pour supprimer un déploiement, cliquez sur **Supprimer le déploiement en production** ou **déploiement intermédiaire**. À l’invite de confirmation, cliquez sur **Oui**. 

4. Si vous envisagez de supprimer le service en nuage, répétez l’étape 3, si nécessaire, pour supprimer d’autre votre déploiement.

5. Pour supprimer le service en nuage, cliquez sur **Supprimer le service de cloud**. À l’invite de confirmation, cliquez sur **Oui**.

> [AZURE.NOTE]
> Si la surveillance détaillée est configuré pour votre service cloud, Azure ne supprime pas les données d’analyse à partir de votre compte de stockage lorsque vous supprimez le service en nuage. Vous devez supprimer manuellement les données. Pour plus d’informations sur l’endroit où trouver les tables de mesures, consultez « Comment : accès détaillé d’analyse des données à l’extérieur du portail classique Azure » dans [le moniteur de Services Cloud](cloud-services-how-to-monitor.md).

## <a name="next-steps"></a>Étapes suivantes

 * [Configuration générale de votre service cloud](cloud-services-how-to-configure.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy.md).
* Configurer un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate.md).
