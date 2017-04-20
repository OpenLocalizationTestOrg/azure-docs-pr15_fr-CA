<properties 
    pageTitle="Tâches courantes de gestion des service nuage | Microsoft Azure" 
    description="Apprenez à gérer les services de cloud dans le portail Azure. Ces exemples utilisent le portail Azure." 
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
    ms.date="08/02/2016"
    ms.author="adegeo"/>


# <a name="how-to-manage-cloud-services"></a>La gestion des Services en nuage

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-manage-portal.md)
- [Azure portal classique](cloud-services-how-to-manage.md)

Votre service cloud est géré dans la zone **Services en nuage (classique)** du portail Azure. Cet article décrit certaines actions courantes que vous prendriez lors de la gestion de vos services de nuage. Qui inclut la mise à jour, suppression, mise à l’échelle et la promotion d’un déploiement par étapes de la production.

Plus d’informations sur la façon de mettre à l’échelle de votre service cloud sont disponibles [ici](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Comment : mettre à jour un déploiement ou un rôle de service cloud

Si vous devez mettre à jour le code d’application pour votre service cloud, utilisez **mise à jour** sur la lame de service cloud. Vous pouvez mettre à jour un seul rôle ou tous les rôles. Pour mettre à jour, vous pouvez charger un nouveau package de service ou d’un fichier de configuration de service.

1. Dans le [portail Azure][], sélectionnez le service de cloud que vous souhaitez mettre à jour. Cette étape ouvre la blade d’instance de service cloud.

2. La lame, cliquez sur le bouton de **mise à jour** .

    ![Bouton Mettre à jour](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Mettre à jour le déploiement avec un nouveau fichier de package de service (.cspkg) et un fichier de configuration de service (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Éventuellement** mettre à jour l’étiquette du déploiement et du compte de stockage. 

5. Si tous les rôles ont une seule instance de rôle, sélectionnez le **déploiement même si un ou plusieurs rôles contiennent une seule instance** pour activer la mise à niveau continuer. 

    Azure peut garantir la disponibilité de service 99,95 % au cours d’une mise à jour du service de cloud uniquement si chaque rôle a au moins deux instances de rôle (machines virtuelles). Avec deux instances de rôle, une machine virtuelle traitera les demandes de client alors que l’autre est mis à jour.

6. Cochez cette case **Démarrer le déploiement** pour la mise à jour appliquée après le chargement du package.

7. Cliquez sur **OK** pour démarrer le service de mise à jour.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Comment : permuter des déploiements à promouvoir un déploiement par étapes de la production

Lorsque vous décidez de déployer une nouvelle version d’un service en nuage, scène et tester votre nouvelle version dans votre environnement de test de service cloud. Permet de **Permuter** basculer les URL par laquelle les deux déploiements sont traités et promouvoir une nouvelle version en production. 

Vous pouvez permuter les déploiements à partir de la page des **Services en nuage** ou le tableau de bord.

1. Dans le [portail Azure][], sélectionnez le service de cloud que vous souhaitez mettre à jour. Cette étape ouvre la blade d’instance de service cloud.

2. La lame, cliquez sur le bouton **Remplacer** .

    ![Échange de Services de cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Le message de confirmation suivant s’ouvre.

    ![Échange de Services de cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Après avoir vérifié les informations de déploiement, cliquez sur **OK** pour remplacer les déploiements.

    L’échange de déploiement se produit rapidement, car la seule chose qui change est les adresses IP virtuelles (VIP) pour les déploiements.

    Pour enregistrer les coûts de traitement informatique, vous pouvez supprimer le déploiement intermédiaire après avoir vérifié que votre déploiement de production fonctionne comme prévu.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Comment : lier une ressource à un service en nuage

Le portail Azure ne lie pas ensemble les ressources comme le portail classique Azure actuel. À la place, déployer des ressources supplémentaires pour le même groupe de ressources utilisé par le Service en nuage.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Comment : supprimer un service en nuage et les déploiements

Avant de supprimer un service en nuage, vous devez supprimer chaque déploiement existant.

Pour enregistrer les coûts de traitement informatique, vous pouvez supprimer le déploiement intermédiaire après avoir vérifié que votre déploiement de production fonctionne comme prévu. Vous êtes facturé pour le calcul des coûts pour les instances de rôle déployés qui sont arrêtés.

Utilisez la procédure suivante pour supprimer un déploiement ou votre service cloud. 

1. Dans le [portail Azure][], sélectionnez le service de cloud que vous souhaitez supprimer. Cette étape ouvre la blade d’instance de service cloud.

2. Dans le serveur lame, cliquez sur le bouton **Supprimer** .

    ![Échange de Services de cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Vous pouvez supprimer le service en nuage tout en activant le **service en nuage et ses déploiements** ou choisissez le **déploiement en Production** ou le **déploiement de la mise en attente**.

    ![Échange de Services de cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. Cliquez sur le bouton **Supprimer** en bas.

5. Pour supprimer le service en nuage, cliquez sur **Supprimer le service de cloud**. À l’invite de confirmation, cliquez sur **Oui**.

> [AZURE.NOTE]
> Lorsqu’un service en nuage est supprimé, et la surveillance détaillée est configurée, vous devez supprimer manuellement les données à partir de votre compte de stockage. Pour plus d’informations sur l’endroit où trouver les tables de mesures, consultez [cet](cloud-services-how-to-monitor.md) article.

[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Étapes suivantes

* [Configuration générale de votre service cloud](cloud-services-how-to-configure-portal.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurer un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate-portal.md).