<properties
    pageTitle="Nombre d’instances à l’échelle manuellement ou automatiquement | Microsoft Azure"
    description="Découvrez comment faire évoluer vos services Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Nombre d’instances à l’échelle manuellement ou automatiquement

Dans le [Portail Azure](https://portal.azure.com/), vous pouvez définir manuellement le nombre d’instances de votre service, ou vous pouvez définir des paramètres de façon à automatiquement échelle basée sur la demande. Ceci est généralement appelé *mise à l’échelle* ou *d’échelle en*.

Avant la mise à l’échelle basée sur le nombre d’instances, vous devez envisager que mise à l’échelle est affecté par un **niveau de tarification** en plus du nombre d’instances. Différents niveaux de tarification peuvent avoir mémoire et noyaux de nombres différents, et donc qu’ils auront de meilleures performances pour le même nombre d’instances (c'est-à-dire *évolution verticale* ou à *l’échelle vers le bas*). Cet article traite plus particulièrement *échelle en* entrée et *sortie*.

Vous pouvez mettre à l’échelle dans le portail, et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour ajuster l’échelle automatiquement ou manuellement.

> [AZURE.NOTE] Cet article décrit comment créer un paramètre d’échelle automatique dans le portail à [http://portal.azure.com](http://portal.azure.com). Paramètres d’échelle automatique créés dans ce portail ne peut pas être édité le portail classique ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Mise à l’échelle manuellement

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis accédez à la ressource que vous souhaitez mettre à l’échelle, tel qu’un **plan de Service de l’application**.

2. La mosaïque de **l’échelle** dans les **opérations** vous indiquera l’état de mise à l’échelle : **Off** lorsque vous sont mise à l’échelle manuelle, **sur** lorsque vous sont mise à l’échelle par une ou plusieurs mesures de performances.
    ![Mosaïque d’échelle](./media/insights-how-to-scale/Insights_UsageLens.png)

3. En cliquant sur la mosaïque vous amènera à la blade **d’échelle** . En haut de la lame d’échelle vous pouvez voir l’historique des actions de l’échelle du service.  
    ![Lame d’échelle](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Seules les actions qui sont effectuées à l’échelle automatique seront afficheront dans ce graphique. Si vous modifiez manuellement le nombre d’instances, la modification n’apparaîtra pas dans ce graphique.

4. Vous pouvez ajuster manuellement les **Instances** nombre avec le curseur.
5. Cliquez sur la commande **Enregistrer** et vous allez être mis à l’échelle à ce nombre d’instances presque immédiatement.

## <a name="scaling-based-on-a-pre-set-metric"></a>Mise à l’échelle basée sur une mesure prédéfinie

Si vous souhaitez que le nombre d’instances d’ajuster automatiquement selon une mesure, sélectionnez la mesure que vous souhaitez dans la liste déroulante **d’échelle par** . Par exemple, pour un **plan de Service de l’application** , vous pouvez évoluer en **Pourcentage de l’UC**.

1. Lorsque vous sélectionnez une mesure, vous obtiendrez un curseur et/ou, entrez le nombre d’instances que vous souhaitez mettre à l’échelle entre les zones de texte :

    ![Lame d’échelle avec un pourcentage de l’UC](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Échelle automatique n’aura jamais votre service au-dessous ou au-dessus des limites que vous définissez, quel que soit votre charge.

2. Ensuite, vous choisissez la plage cible pour la mesure. Par exemple, si vous avez choisi le **pourcentage de l’UC**, vous pouvez définir une cible pour l’UC moyenne entre toutes les instances dans votre service. Une mise à l’échelle se produit lorsque la moyenne du processeur dépasse la valeur maximale que vous définissez, de la même manière, une échelle de se produira chaque fois que la moyenne du processeur est inférieur à la valeur minimale.

3. Cliquez sur la commande **Enregistrer** . Échelle automatique vérifiera quelques minutes pour vous assurer que vous êtes dans la plage de l’instance et la cible pour votre mesure. Lorsque votre service reçoit le trafic supplémentaire, vous obtiendrez plusieurs instances sans rien faire.

## <a name="scale-based-on-other-metrics"></a>Échelle basée sur d’autres mesures

Vous pouvez adapter en fonction des mesures autres que les paramètres prédéfinis qui s’affichent dans la liste déroulante **d’échelle** et peuvent même ont un ensemble complexe de mise à l’échelle et mise à l’échelle dans les règles.

### <a name="adding-or-changing-a-rule"></a>Ajout ou modification d’une règle

1. Choisissez les **règles de planification et de performance** dans la liste déroulante **d’échelle par** : ![les règles de Performance](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Si vous aviez précédemment autoscale, sur vous verrez une vue de définir les règles exactes que vous aviez.

3. Pour mettre à l’échelle selon un autre clic métrique la ligne **Ajouter une règle** . Vous pouvez également cliquer sur une des lignes existantes à modifier à partir de la mesure que vous aviez précédemment à la mesure que vous le souhaitez à l’échelle par.
![Ajouter une règle](./media/insights-how-to-scale/Insights_AddRule.png)

4. Maintenant vous devez sélectionner le mesure que vous souhaitez mettre à l’échelle. Lors du choix d’une mesure, il existe quelques points à prendre en compte :
    * La *ressource* que provient de la mesure. En général, ce sera la même que la ressource qu'est mise à l’échelle. Toutefois, si vous souhaitez mettre à l’échelle de la profondeur d’une file d’attente de stockage, la ressource est la file d’attente que vous souhaitez mettre à l’échelle.
    * Le *nom de mesure* .
    * L' *agrégation de temps* de la mesure. C’est comment les données de combiner sur la *durée*.

5. Après avoir choisi votre métrique, vous choisissez le seuil pour la mesure et l’opérateur. Par exemple, vous pouvez dire **supérieure à** **80 %**.

6. Puis choisissez l’action que vous souhaitez prendre. Il existe deux différents types d’actions :
    * Augmenter ou diminuer en - cela ajouter ou supprimer le numéro de la **valeur** des instances que vous définissez
    * Augmentez ou diminuez le pourcentage, cette opération modifie le nombre d’instances par un pourcentage. Par exemple, vous pouvez placer 25 dans le champ **valeur** , et si vous avez actuellement 8 instances, 2 seront ajoutés.
    * Augmenter ou diminuer pour - Ceci permettra de définir le nombre d’instances de la **valeur** que vous définissez.

7. Enfin, vous pouvez choisir de refroidissement - la durée pendant laquelle cette règle doit attendre après l’action précédente échelle mise à l’échelle à nouveau.

8. Après avoir configuré votre règle, cliquez sur **OK**.

9. Une fois que vous avez configuré toutes les règles que vous le souhaitez, n’oubliez pas de positionnement de la commande **Enregistrer** .

### <a name="scaling-with-multiple-steps"></a>Mise à l’échelle avec plusieurs étapes

Les exemples ci-dessus sont assez simples. Toutefois, si vous voulez être plus agressif sur l’évolution vers le haut (ou vers le bas), vous pouvez même ajouter plusieurs règles d’échelle pour la même mesure. Par exemple, vous pouvez définir deux règles d’échelle sur le pourcentage de l’UC :

1. Évoluer par 1 instance, si le pourcentage de l’UC est supérieure à 60 %
2. Évoluer par 3 instances si le pourcentage de l’UC est supérieur à 85 %

![Plusieurs règles d’échelle](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Avec cette règle supplémentaire, si votre charge dépasse 85 % avant une action d’échelle, vous obtiendrez deux instances supplémentaires au lieu d’un.

## <a name="scale-based-on-a-schedule"></a>Échelle en fonction d’une planification


Par défaut, lorsque vous créez une règle d’échelle il s’appliquera toujours. Vous pouvez voir que lorsque vous cliquez sur l’en-tête de profil :

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Toutefois, vous souhaiterez ont plus agressive mise à l’échelle pendant le jour ou la semaine, que pendant le week-end. Vous pourriez même arrêter votre service entièrement hors des heures de travail.

1. Pour ce faire, sur le profil que vous avez, sélectionnez **répétition** au lieu de **toujours** et choisir les heures auxquelles vous voulez que le profil à appliquer.

2. Par exemple, pour disposer d’un profil qui s’applique au cours de la semaine, dans la liste déroulante de **jours** désélectionnez **samedi** et **dimanche**.

3. Pour disposer d’un profil qui s’applique au cours de la journée, définissez l' **heure de début** à l’heure du jour à laquelle vous souhaitez commencer à.
    ![Périodicité par défaut](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Cliquez sur **OK**.

5. Ensuite, vous devrez ajouter le profil que vous souhaitez appliquer à d’autres moments. Cliquez sur la ligne **Ajouter un profil** .
    ![Désactiver un travail](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Votre deuxième nouveau, profil, par exemple vous pouvez l’appeler **travail**.

7. Sélectionnez à nouveau la **périodicité** , puis choisissez la plage de nombre d’instance que vous souhaitez pendant ce temps.

8. Comme avec le profil par défaut, choisissez les **jours** souhaité à appliquer à ce profil, l' **heure de début** pendant la journée.

>[AZURE.NOTE] Échelle automatique utilise les règles de l’heure d’été pour le **fuseau horaire** que vous sélectionnez. Toutefois, pendant l’heure l’offset UTC indique l’offset de fuseau horaire de base, pas l’offset d’UTC d’épargne l’heure d’été.

9. Cliquez sur **OK**.

10. Maintenant, vous devrez ajouter quelque règles que vous voulez appliquer pendant votre second profil. Cliquez sur **Ajouter une règle**, et ensuite, vous pouvez créer la même règle que vous avez pendant le profil par défaut.
    ![Ajouter une règle pour l’arrêt de travail](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Veillez à créer une règle de mise à l’échelle et la mise à l’échelle dans, soit pendant le profil, le nombre d’instances est uniquement augmenter (ou réduire).

12. Enfin, cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Métriques de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
* [Activer l’analyse et les tests de diagnostic](insights-how-to-use-diagnostics.md) pour collecter les mesures détaillées de haute fréquence sur votre service.
* [Recevoir des notifications alertes](insights-receive-alert-notifications.md) chaque fois que se produisent les événements opérationnels ou métriques traverser un seuil.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous souhaitez comprendre exactement comment votre code s’exécute dans le nuage.
* [Afficher les événements et les journaux d’audit](insights-debugging-with-events.md) pour découvrir tout ce qui est arrivé dans votre service.
* [Analyser la disponibilité et le temps de réponse d’une page web](../application-insights/app-insights-monitor-web-app-availability.md) avec les perspectives d’Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
