<properties
   pageTitle="Comment faire pour conserver une adresse IP virtuelle constante un service en nuage | Microsoft Azure"
   description="Apprenez à vous assurer que l’adresse IP virtuelle (VIP) de votre service cloud Azure ne change pas."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Comment faire pour conserver une adresse IP virtuelle constante un service cloud

Lorsque vous mettez à jour un service en nuage hébergé dans Azure, vous devrez vous assurer que l’adresse IP virtuelle (VIP) du service ne change pas. De nombreux services de gestion de domaine utilisent le système de nom de domaine (DNS) pour l’enregistrement des noms de domaine. DNS ne fonctionne que si l’adresse IP virtuelle reste le même. Vous pouvez utiliser l' **Assistant Publication** dans Azure Tools pour vous assurer que l’adresse IP virtuelle de votre service cloud ne change pas lorsque vous mettez à jour. Pour plus d’informations sur l’utilisation de gestion de domaine DNS pour les services en nuage, consultez [configuration d’un nom de domaine personnalisé pour un service cloud Azure](./cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publication d’un service en nuage sans modifier son adresse IP virtuelle

L’adresse IP virtuelle d’un service en nuage est allouée lorsque vous déploierez d’abord sur Azure dans un environnement particulier, tel que l’environnement de Production. L’adresse IP virtuelle ne change pas, sauf si vous supprimez le déploiement explicitement ou il est implicitement supprimé par le processus de mise à jour du déploiement. Pour conserver l’adresse VIP, vous ne devez pas supprimer votre déploiement, et vous devez également vous assurer que Visual Studio ne supprime pas votre déploiement automatiquement. Vous pouvez contrôler le comportement en spécifiant les paramètres de déploiement dans l' **Assistant Publication**, qui prend en charge plusieurs options de déploiement. Vous pouvez spécifier un déploiement fraîche ou un déploiement de mise à jour, ce qui peut être incrémentielle ou simultanée, et les deux types de déploiements de mise à jour de conservent l’adresse VIP. Pour les définitions de ces différents types de déploiement, reportez-vous à la section [Publier Assistant Création d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).  En outre, vous pouvez contrôler si le précédent déploiement d’un service en nuage est supprimé si une erreur se produit. L’adresse IP virtuelle peut changer de manière inattendue si vous ne définissez pas cette option correctement.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Pour mettre à jour un service en nuage sans modifier son adresse IP virtuelle

1. Après avoir déployé votre service cloud au moins une fois, ouvrez le menu contextuel pour le nœud de votre projet d’Azure et puis cliquez sur **Publier**. L’Assistant de **Publication des applications Azure** s’affiche.

1. Dans la liste des abonnements, choisir celle à laquelle vous souhaitez déployer, puis cliquez sur le bouton **suivant** . La page **paramètres** de l’Assistant s’affiche.

1. Sous l’onglet **Paramètres communs** , vérifiez que le nom du service cloud pour lesquels vous déployez, l' **environnement**, la **Configuration de Build**et la **Configuration du Service** sont toutes correctes.

1. Sous l’onglet **Paramètres avancés** , vérifiez que le compte de stockage et de l’étiquette de déploiement sont corrects, que la case à cocher **Supprimer le déploiement en cas d’échec** est désactivée et que la case à cocher de mise à jour de **déploiement** est sélectionnée. En sélectionnant la case à cocher de mise à jour de **déploiement** , vous assurez que votre déploiement n’est pas supprimé et que votre adresse IP virtuelle n’est pas perdue lorsque vous republiez votre application. En désactivant le **Supprimer le déploiement sur la case à cocher Échec**, vous assurez que votre adresse IP virtuelle n’est pas perdue si une erreur se produit pendant le déploiement.

1. Pour spécifier comment vous souhaitez que les rôles soient mises à jour, cliquez sur le lien **paramètres** en regard de la zone de **déploiement mise à jour** , puis choisissez l’option de mise à jour simultanée ou mise à jour incrémentielle dans la boîte de dialogue Paramètres de **mise à jour du déploiement** . Si vous choisissez la mise à jour incrémentielle, chaque instance est mis à jour un après l’autre, afin que l’application est toujours disponible. Si vous choisissez la mise à jour simultanée, toutes les instances sont mises à jour en même temps. Mise à jour simultanée est plus rapide, mais votre service ne soit pas disponible pendant le processus de mise à jour.

1. Lorsque vous êtes satisfait de vos paramètres, cliquez sur le bouton **suivant** .

1. Dans la page **Résumé** de l’Assistant, vérifiez vos paramètres et cliquez sur le bouton **Publier** .

  >[AZURE.WARNING] Si le déploiement échoue, vous devez résoudre les raisons pour lesquelles il a échoué et redéployer dans les moindres délais, pour éviter de laisser votre service cloud dans un état endommagé.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la publication pour Azure à partir de Visual Studio, consultez [Assistant d’application Azure de publier](vs-azure-tools-publish-azure-application-wizard.md).
