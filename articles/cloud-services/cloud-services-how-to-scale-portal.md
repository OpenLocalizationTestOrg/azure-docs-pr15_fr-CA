<properties
    pageTitle="Automatiquement mettre à l’échelle un service cloud dans le portail | Microsoft Azure"
    description="Apprenez à utiliser le portail pour configurer des règles d’échelle automatique pour un rôle de web service nuage ou travailleur dans Azure."
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

Conditions peuvent être définies pour un rôle de collaborateur de service cloud qui déclenchent une échelle dans ou opération. Les conditions pour le rôle peuvent être basées sur le processeur, le disque ou la charge réseau du rôle. Vous pouvez également définir une conditation basée sur une file d’attente ou la métrique d’une autre ressource Azure associé à votre abonnement.

>[AZURE.NOTE] Cet article se concentre sur les rôles web et worker de Service Cloud. Lorsque vous créez une machine virtuelle (classic) directement, elle est hébergée dans un service en nuage. Vous pouvez mettre à l’échelle un ordinateur virtuel standard en l’associant à une [disponibilité définie](../virtual-machines/virtual-machines-windows-classic-configure-availability.md) et manuellement les activer ou désactiver.

## <a name="considerations"></a>Considérations relatives à la

Tenez compte des informations suivantes avant de configurer la mise à l’échelle de votre application :

- Mise à l’échelle n’est affecté par l’utilisation de base. Les instances de rôle plus grandes utilisent plus de cœurs. Vous pouvez adapter une application uniquement dans la limite de cœurs de votre abonnement. Par exemple, si votre abonnement a une limite de vingt cœurs et que vous exécutez une application avec le milieu de deux de taille (un total de quatre cœurs) des services cloud, vous ne pouvez échelonner les autres déploiements de service cloud dans votre abonnement par 16 noyaux. Pour plus d’informations sur les tailles, reportez-vous à la section [Tailles du Service nuage](cloud-services-sizes-specs.md) .

- Vous pouvez mettre à l’échelle en fonction d’un seuil de message file d’attente. Pour plus d’informations sur l’utilisation de files d’attente, voir [comment utiliser le Service de stockage de file d’attente](../storage/storage-dotnet-how-to-use-queues.md).

- Vous pouvez également adapter les autres ressources associées à votre abonnement.

- Pour activer la haute disponibilité de votre application, vous devez vous assurer qu’il est déployé avec deux ou plusieurs instances de rôle. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

## <a name="where-scale-is-located"></a>Où se trouve l’échelle

Après avoir sélectionné votre service cloud, vous devez disposer de la lame de service cloud visible.

1. Sur la lame de service cloud, dans la fenêtre **rôles et les Instances** , sélectionnez le nom du service nuage.   
**IMPORTANT**: Assurez-vous que vous cliquez sur le rôle de service cloud, pas l’instance de rôle qui se trouve sous le rôle.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)

2. Sélectionnez la vignette de **l’échelle** .

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Échelle automatique

Vous pouvez configurer les paramètres d’échelle pour un rôle avec deux modes **manuel** ou **automatique**. Manuel est que vous pouvez vous y attendre, vous définissez le nombre absolu d’instances. Automatique permet cependant à un ensemble de règles qui régissent le comment et à quel beaucoup vous devez mise à l’échelle.

Définissez l’option **Echelle par** les règles de **performances et planification**.

![Paramètres d’échelle de services cloud avec le profil et de règle](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Un profil existant.
2. Ajouter une règle pour le profil du parent.
3. Ajouter un autre profil.

Sélectionnez **Ajouter un profil**. Le profil détermine le mode que vous souhaitez utiliser pour l’échelle : **toujours**, **périodicité**, **date fixe**.

Après avoir configuré les règles et le profil, cliquez sur l’icône **Enregistrer** dans la partie supérieure.

#### <a name="profile"></a>Profil

Le profil définit des instances minimales et maximales pour l’échelle, et également lorsque cette plage de l’échelle est active.

* **Toujours**

    Toujours conserver cette plage d’instances disponibles.  

    ![Service de cloud toujours mettre à l’échelle](./media/cloud-services-how-to-scale-portal/select-always.png)
    
* **Périodicité**

    Choisissez un ensemble de jours de la semaine à l’échelle.

    ![Échelle de service cloud avec une fréquence de répétition](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
    
* **Date fixe**

    Une plage de dates fixe à l’échelle le rôle.

    ![Échelle de service cLoud avec une date fixe](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Après avoir configuré le profil, sélectionnez le bouton **OK** en bas de la lame de profil.

#### <a name="rule"></a>Règle

Les règles sont ajoutées à un profil et représentent une condition qui doit déclencher l’échelle. 

Le déclencheur de la règle est basé sur une mesure de service cloud (utilisation de l’UC, l’activité du disque ou activité réseau) à laquelle vous pouvez ajouter une valeur conditionnelle. En outre, vous pouvez avoir le déclencheur basé sur une file d’attente ou la métrique d’une autre ressource Azure associé à votre abonnement.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Une fois que vous avez configuré la règle, sélectionnez le bouton **OK** en bas de la lame de règle.

## <a name="back-to-manual-scale"></a>Retour à l’échelle manuelle

Accédez aux [paramètres d’échelle](#where-scale-is-located) et la valeur de l’option **échelle par** **un nombre d’instances de saisies manuellement**.

![Paramètres d’échelle de services cloud avec le profil et de règle](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Cette opération supprime la mise à l’échelle automatique du rôle et vous pouvez définir le nombre d’instances directement. 

1. L’option scale (manuel ou automatisé).
2. Un curseur d’instance de rôle pour définir les instances pour s’adapter aux.
3. Instances du rôle à mettre à l’échelle à.

Après avoir configuré les paramètres d’échelle, cliquez sur l’icône **Enregistrer** dans la partie supérieure.

