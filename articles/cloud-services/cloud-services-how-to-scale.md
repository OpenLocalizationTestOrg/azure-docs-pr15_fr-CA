<properties
    pageTitle="Automatiquement mettre à l’échelle un service cloud dans le portail | Microsoft Azure"
    description="(classique) Apprenez à utiliser le portail classique pour configurer des règles d’échelle automatique pour un rôle de web service nuage ou travailleur dans Azure."
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
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Comment un service en nuage à l’échelle automatique

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-scale-portal.md)
- [Azure portal classique](cloud-services-how-to-scale.md)

Sur la page d’échelle du portail Azure classique, vous pouvez manuellement mettre à l’échelle votre rôle de collaborateur ou web, ou vous pouvez activer la mise à l’échelle automatique en fonction de la charge de l’UC ou à une file d’attente.

>[AZURE.NOTE] Cet article se concentre sur les rôles web et worker de Service Cloud. Lorsque vous créez une machine virtuelle (classic) directement, elle est hébergée dans un service en nuage. Certaines de ces informations s’applique à ces types de machines virtuelles. Mise à l’échelle d’un ensemble de disponibilité des machines virtuelles est simplement en les désactiver selon les règles d’échelle que vous configurez. Pour plus d’informations sur les Machines virtuelles et ensembles de disponibilité, voir [Gérer la disponibilité des ordinateurs virtuels](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Tenez compte des informations suivantes avant de configurer la mise à l’échelle de votre application :

- Mise à l’échelle n’est affecté par l’utilisation de base. Les instances de rôle plus grandes utilisent plus de cœurs. Vous pouvez adapter une application uniquement dans la limite de cœurs de votre abonnement. Par exemple, si votre abonnement a une limite de vingt cœurs et que vous exécutez une application avec le milieu de deux de taille (un total de quatre cœurs) des services cloud, vous ne pouvez échelonner les autres déploiements de service cloud dans votre abonnement par 16 noyaux. Pour plus d’informations sur les tailles, reportez-vous à la section [Tailles du Service nuage](cloud-services-sizes-specs.md) .

- Vous devez créer une file d’attente et l’associer à un rôle avant de la mettre à l’échelle une application basée sur un seuil pour les messages. Pour plus d’informations, voir [comment utiliser le Service de stockage de file d’attente](../storage/storage-dotnet-how-to-use-queues.md).

- Vous pouvez mettre à l’échelle les ressources qui sont liés à votre service cloud. Pour plus d’informations sur la liaison des ressources, consultez [Comment : lier une ressource à un service en nuage](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Pour activer la haute disponibilité de votre application, vous devez vous assurer qu’il est déployé avec deux ou plusieurs instances de rôle. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Planifier la mise à l’échelle

Par défaut, tous les rôles ne suivent pas un calendrier spécifique. Par conséquent, les paramètres modifiés s’appliquent à toutes les heures et tous les jours pendant toute l’année. Si vous le souhaitez, vous pouvez configurer la mise à l’échelle manuelle ou automatique pour :

- Jours de la semaine
- Week-ends
- Soirs de semaine
- Matin de la semaine
- Dates spécifiques
- Plages de dates spécifiques

Il s’agit de conigured dans [Azure portal classique](https://manage.windowsazure.com/) sur le  
**Services en nuage** > **\[votre service cloud\]** > **échelle** > **\[Production ou intermédiaire\] ** page.

Cliquez sur le bouton **définir les temps de planification** pour chaque rôle que vous souhaitez modifier.

![Mise à l’échelle automatique en fonction d’un calendrier de service de cloud][scale_schedules]



## <a name="manual-scale"></a>Échelle manuelle

Sur la page **d’échelle** , vous pouvez manuellement augmenter ou diminuer le nombre d’instances en cours d’exécution dans un service cloud. Il est configuré pour chaque planification que vous avez créé ou pour tout le temps si vous n’avez pas créé une planification.

1. Dans [Azure portal classique](https://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier à partir de la **Production** en **transit** ou vice versa.

2. Cliquez sur **l’échelle**.

3. Sélectionnez le calendrier que vous souhaitez modifier les options de mise à l’échelle pour. La valeur par défaut *N° heures* si vous n’avez aucune planification définie.

4. La section **échelle de mesure** , sélectionnez **NONE**. Il s’agit du paramètre par défaut pour tous les rôles.

5. Chaque rôle dans le service cloud est un curseur permettant de modifier le nombre d’instances à utiliser.

    ![Mettre à l’échelle un rôle de service cloud manuellement][manual_scale]

    Si vous avez besoin de plusieurs instances, vous devrez modifier la [taille de machine virtuelle de service cloud](cloud-services-sizes-specs.md).

6. Cliquez sur **Enregistrer**.  
Les instances de rôle seront ajoutés ou supprimés en fonction de vos sélections.

>[AZURE.TIP] Chaque fois que vous consultez ![][tip_icon] déplacer votre souris dessus et vous pouvez obtenir de l’aide sur un paramètre spécifique de ce que fait.


## <a name="automatic-scale---cpu"></a>Échelle automatique - UC

Cela met à l’échelle si le pourcentage moyen d’utilisation du processeur passe au-dessus ou en dessous des seuils spécifiés ; les instances de rôle sont créées ou supprimées.

1. Dans [Azure portal classique](https://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier à partir de la **Production** en **transit** ou vice versa.

2. Cliquez sur **l’échelle**.

3. Sélectionnez le calendrier que vous souhaitez modifier les options de mise à l’échelle pour. La valeur par défaut *N° heures* si vous n’avez aucune planification définie.

4. Recherchez la section **échelle de mesure** et sélectionnez **CPU**.

5. Maintenant, vous pouvez configurer une plage minimale et maximale des instances de rôles, la cible de l’utilisation du processeur (pour déclencher une échelle haut) et le nombre d’instances à évoluer par.

![Mettre à l’échelle un rôle de service cloud en charge de l’UC][cpu_scale]

>[AZURE.TIP] Chaque fois que vous consultez ![][tip_icon] déplacer votre souris dessus et vous pouvez obtenir de l’aide sur un paramètre spécifique de ce que fait.





## <a name="automatic-scale---queue"></a>Échelle automatique - file d’attente

Il s’adapte automatiquement si le nombre de messages dans une file d’attente est au-dessus ou en dessous d’un seuil spécifié ; les instances de rôle sont créées ou supprimées.

1. Dans [Azure portal classique](https://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier à partir de la **Production** en **transit** ou vice versa.

2. Cliquez sur **l’échelle**.

3. Recherchez la section **échelle de mesure** et sélectionnez **CPU**.

4. Maintenant, vous pouvez configurer une plage minimale et maximale des instances de rôles, la file d’attente et le montant de la file d’attente de messages à traiter pour chaque instance et le nombre d’instances à évoluer par.

![Un rôle de service cloud mise à l’échelle par une file d’attente][queue_scale]

>[AZURE.TIP] Chaque fois que vous consultez ![][tip_icon] déplacer votre souris dessus et vous pouvez obtenir de l’aide sur un paramètre spécifique de ce que fait.


## <a name="scale-linked-resources"></a>Mettre à l’échelle les ressources liées

Souvent, lorsque vous redimensionnez un rôle, il est intéressant de mettre à l’échelle de la base de données que l’application utilise également. Si vous liez à la base de données pour le service en nuage, vous pouvez accéder aux paramètres mise à l’échelle pour cette ressource en cliquant sur le lien approprié.

1. Dans [Azure portal classique](https://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier à partir de la **Production** en **transit** ou vice versa.

2. Cliquez sur **l’échelle**.

3. Recherchez la section de **ressources liées** et cliqué sur **l’échelle de la gestion de cette base de données**.

    > [AZURE.NOTE] Si vous ne voyez pas une section de **ressources liées** , probablement inutile des ressources liées.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
