<properties
    pageTitle="Méthodes conseillées pour moniteur d’Azure autoscaling. | Microsoft Azure"
    description="Découvrez les principes pour utiliser efficacement l’autoscaling dans Azure moniteur."
    authors="kamathashwin"
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Méthodes conseillées pour moniteur d’Azure autoscaling

Les sections suivantes de ce document vous aideront à comprendre les méthodes conseillées pour l’échelle automatique dans Azure. Après avoir examiné ces informations, vous pourrez mieux utiliser efficacement autoscale dans votre infrastructure Azure.

## <a name="autoscale-concepts"></a>Concepts de l’échelle automatique

- Une ressource peut avoir uniquement *un* paramètre d’échelle automatique
- Un paramètre d’échelle automatique peut avoir un ou plusieurs profils et chaque profil peuvent avoir une ou plusieurs règles d’échelle automatique.
- Un paramètre d’échelle automatique met à l’échelle instances horizontalement, qui est *en* augmentant *les instances et* en diminuant le nombre d’instances.
 Un paramètre d’échelle a un maximum, minimum et valeur par défaut d’instances.
- Un travail autoscale lit toujours la métrique associée à l’échelle, de vérifier si elle a atteint le seuil configuré pour horizontale ou d’échelle. Vous pouvez afficher une liste des mesures qu’autoscale peut évoluer au [Moniteur d’Azure autoscaling les mesures communes](insights-autoscale-common-metrics.md).
- Tous les seuils sont calculées au niveau de l’instance. Par exemple, « échelle par 1 instance lorsque moyenne du processeur > 80 % lorsque le nombre d’instances est 2 », signifie horizontale lorsque l’UC moyenne par toutes les instances est supérieure à 80 %.
- Vous recevez toujours des messages d’échec. Plus précisément, le propriétaire, collaborateurs et les lecteurs de la ressource cible recevoir des e-mails. Vous recevez également toujours un e-mail de *récupération* lorsque l’échelle automatique permet de récupérer d’une défaillance et commencera à fonctionner normalement.
- Vous pouvez choisir de recevoir une notification d’action réussie d’échelle par e-mail et webhooks.

## <a name="autoscale-best-practices"></a>Échelle de meilleures pratiques

Utilisez les méthodes conseillées suivantes lorsque vous utilisez l’échelle automatique.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Assurez-vous que les valeurs maximales et minimales sont différents et une marge suffisante entre les
Si vous avez un paramètre qui a minimum = 2, maximum = 2 et actuel nombre d’instance est 2, aucune action de l’échelle ne peut se produire. Conserver une marge suffisante entre le nombre d’instance maximale et minimale, qui sont inclus. Échelle automatique systématiquement à l’échelle entre ces limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Mise à l’échelle manuelle est réinitialisée par max et min d’échelle automatique
Si vous mettre à jour manuellement le nombre d’instances d’une valeur supérieure ou inférieure au maximum, le moteur de l’échelle s’ajuste automatiquement à la valeur minimale (si elle est inférieure) ou la valeur maximale (si les cas ci-dessus). Par exemple, vous définissez la plage comprise entre 3 et 6. Si vous disposez d’une instance en cours d’exécution, le moteur autoscale met à l’échelle à 3 instances lors de sa prochaine exécution. De même, il serait échelle de 8 instances jusqu'à 6 lors de sa prochaine exécution.  Mise à l’échelle manuelle est temporaire, sauf si vous réinitialisez ainsi que les règles d’échelle automatique.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Toujours utiliser une combinaison de la règle horizontale et de l’échelle qui effectue une augmentation et la diminution
Si vous utilisez uniquement une partie de la combinaison, autoscale échelle en un seul, ou, jusqu'à ce que le maximum ou le minimum, est atteinte.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Ne pas basculer entre le portail Azure et d’Azure portail classique lors de la gestion d’échelle automatique
Pour les Services en nuage et les Services d’application (applications Web), utilisez le portail Azure (portal.azure.com) pour créer et gérer les paramètres d’échelle. Pour les jeux d’échelle de Machine virtuelle utiliser PoSH, CLI ou API REST pour créer et gérer les paramètres d’échelle. Ne change pas entre le portail classique Azure (manage.windowsazure.com) et le portail Azure (portal.azure.com) lors de la gestion des configurations de l’échelle. Le portail classique Azure et son serveur principal sous-jacent a ses limites. Déplacer vers le portail Azure pour gérer l’échelle à l’aide d’une interface utilisateur graphique. Les options doivent utiliser l’échelle PowerShell, CLI ou API REST (via l’Explorateur de ressources Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Choisissez la statistique appropriée pour votre mesure de diagnostic
Pour les mesures de diagnostics, vous pouvez choisir entre *moyen*, *Minimum*, *Maximum* et *Total* comme métrique à l’échelle par. La statistique la plus commune est *moyenne*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Choisissez les seuils avec soin pour tous les types de mesure
Nous vous recommandons de soigneusement le choix des seuils différents pour horizontale et échelle en fonction d’un cas pratiques.

Nous *recommandons de ne pas* les paramètres échelle automatique comme les exemples ci-dessous avec les valeurs de seuil identique ou très similaire pour et dans les conditions :

- Augmente les instances de 1 lorsque count nombre de threads < = 600
- Réduire les instances de 1 lorsque count nombre de threads > = 600

Examinons un exemple de ce qui peut entraîner un comportement qui peut paraître déroutant. La séquence suivante de cosider.

1. Supposons que 2 instances sont dans un premier temps et le nombre moyen de threads par instance grossit alors à 625.
2. Échelle automatique met à l’échelle en ajoutant une instance 3e.
3. Ensuite, supposons que le nombre de threads de moyenne entre l’instance se situe à 575.
4. Avant la mise à l’échelle vers le bas, échelle automatique tente d’estimer que l’état final sera si elle est redimensionnée dans. Par exemple, 575 x 3 (nombre d’instances en cours) = 1,725 / 2 (nombre final d’instances lors de la mise à l’échelle vers le bas) = 862.5 threads. Cela signifie qu’autoscale devrait immédiatement horizontale à nouveau même après que qu’il mis à l’échelle, si le nombre de threads moyenne reste la même ou même tombe qu’une petite quantité. Toutefois, si elle est redimensionnée à nouveau, le processus entier répétera, conduisant à une boucle infinie.
5. Pour éviter cette situation (appelée « ailes »), échelle automatique n’évolue pas vers le bas du tout. Au lieu de cela, il ignore et réévalue la condition lors de la prochaine que tâche du service s’exécute. Cela peut induire en erreur beaucoup de gens, car l’échelle automatique ne s’afficherait pas fonctionner lorsque le nombre de threads moyenne a été 575.

Estimation lors d’une échelle est destinée à éviter les situations de « flappy ». Vous devez conserver ce comportement à l’esprit lorsque vous choisissez les mêmes seuils pour horizontale et dans.

Nous vous recommandons de choisir une marge suffisante entre l’horizontale et les seuils. Par exemple, considérez la meilleure combinaison de règle suivante.

- Augmenter les instances en 1 compter lorsque processeur % > = 80
- Réduire les instances de 1 compter lorsque processeur % < = 60

Dans ce cas  

1. Supposons que 2 instances sont d’origine.
2. Si le % d’UC moyen entre les instances atteint 80, échelle automatique met à l’échelle en ajoutant une troisième instance.
3. Supposons maintenant que le % de l’UC tombe à 60 dans le temps.
4. Règles d’échelle d’échelle évalue l’état final s’il s’agissait à l’échelle. Par exemple, 60 x 3 (nombre d’instances en cours) = 180 / 2 (nombre final d’instances lors de la mise à l’échelle vers le bas) = 90. Afin qu’autoscale ne pas échelle de car il aurait à évolution cloisonnée immédiatement. Au lieu de cela, il ignore la mise à l’échelle vers le bas.
5. L’échelle de temps suivante vérifie l’UC continue à 50. Il estime à nouveau - 50 x 3 instance = 150 / 2 instances = 75, ce qui n’est inférieur au seuil horizontale de 80, il met à l’échelle dans correctement aux instances de 2.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considérations relatives à la mise à l’échelle des valeurs de seuil pour les mesures spéciales
 Pour les mesures spéciales comme métrique de longueur de file d’attente de Bus de Service ou de stockage, le seuil est le nombre moyen de messages par le nombre actuel d’instances. Choisissez soigneusement la choisir la valeur seuil pour cette mesure.

Nous allons illustrer par un exemple afin de que comprendre le comportement de mieux.

- Augmenter les instances en 1 nombre lorsque le nombre de messages de la file d’attente de stockage > = 50
- Diminuer les instances par 1 nombre lorsque le nombre de messages de la file d’attente de stockage < = 10

Prenez en compte la séquence suivante :

1. Il existe des instances de file d’attente de stockage 2.
2. Messages adopteront et lorsque vous examinez la file d’attente de stockage, le nombre total de lectures 50. Vous pouvez alors supposer qu’autoscale doit démarrer une action de l’horizontale. Toutefois, notez qu’il est toujours 50/2 = 25 messages par instance. Par conséquent, horizontale ne se produit pas. Pour la première horizontale à se produire, le nombre total de messages dans la file d’attente de stockage doit être 100.
3. Ensuite, supposons que le nombre de messages total atteint 100.
4. Une instance de file d’attente de stockage 3e est ajoutée en raison d’une action de l’horizontale.  La prochaine action horizontale ne se produit pas jusqu'à ce que le nombre total de messages dans la file d’attente atteint 150 car 150/3 = 50.
5. Maintenant il diminue le nombre de messages dans la file d’attente. Avec les instances de 3, la première action d’échelle qui se produit lorsque le nombre total de messages dans les files d’attente d’ajouter jusqu'à 30 car 30/3 = 10 messages par instance, ce qui constitue le seuil dans l’échelle.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considérations relatives à la mise à l’échelle de lorsque plusieurs profils sont configurés dans un paramètre d’échelle automatique

Dans un paramètre d’échelle automatique, vous pouvez choisir un profil par défaut, qui est toujours appliqué indépendamment de toute planification ou l’heure, ou vous pouvez choisir un profil d’abonnement ou d’un profil pour une période fixe, avec une plage de date et d’heure.

Lorsque l’échelle automatique service les traite, il vérifie toujours dans l’ordre suivant :

1. Profil de Date fixe
2. Profil d’abonnement
3. Profil de (« toujours ») par défaut

Si une profil condition est remplie, échelle automatique ne vérifie pas la condition de profil suivante juste en dessous. Échelle automatique traite uniquement un seul profil à la fois. Cela signifie que si vous souhaitez également inclure une condition de traitement d’un profil de niveau inférieur, vous devez inclure également ces règles dans le profil actif.

Examinons cela à l’aide d’un exemple :

L’illustration ci-dessous indique un paramètre échelle automatique avec un profil par défaut d’instances minimales = instances 2 et maximum = 10. Dans cet exemple, les règles sont configurés pour horizontale lorsque le nombre de messages dans la file d’attente est supérieur à 10 et échelle de lorsque le nombre de messages dans la file d’attente est inférieur à 3. Maintenant, la ressource peut évoluer entre 2 et 10 instances.

En outre, un abonnement profil n’est défini pour le lundi. Il est défini pour les instances minimales = instances 2 et maximum = 12. Cela signifie que le lundi, l’échelle de temps premier vérifie cette condition, si le nombre d’instances est 2, il met à l’échelle pour le nouveau niveau minimum de 3. Tant qu’autoscale continue à rechercher cette condition profil correspondant (lundi), il ne traite que processeur échelle-out/in règles configurées pour ce profil. À ce stade, il ne vérifie pas la longueur de la file d’attente. Toutefois, si vous souhaitez également que la condition de longueur de file d’attente doit être vérifiée, vous devez inclure également ces règles à partir du profil par défaut dans votre profil de lundi.

De même, lorsque l’échelle automatique bascule vers le profil par défaut, il vérifie d’abord si les conditions minimales et maximales sont remplies. Si le nombre d’instances à l’heure est 12, il met à l’échelle de 10, la valeur maximale autorisée pour le profil par défaut.

![paramètres d’échelle automatique](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considérations relatives à la mise à l’échelle de lorsque plusieurs règles sont configurés dans un profil
Il existe des cas où vous devrez peut-être définir plusieurs règles dans un profil. L’ensemble des règles d’échelle suivantes sont utilisées par l’utilisation de services lorsque plusieurs règles sont définies.

*Mise à l’échelle*, échelle automatique s’exécute si n’importe quelle règle est respectée.
Dans la zone *de l’échelle*, autoscale nécessitent toutes les règles à respecter.

Pour illustrer, supposez que vous disposez de 4 autoscale règles suivantes :

- Si UC < 30 %, échelle-1
- Si mémoire < 50 %, échelle de par 1
- Si UC > 75 %, montée par 1
- Si mémoire > 75 %, montée par 1

Le suivi se produit alors :

- Si le processeur est de 76 % et la mémoire est de 50 %, nous horizontale.
- Si le processeur est de 50 % et la mémoire est de 76 % nous horizontale.

D’autre part, si le processeur est de 25 % et de mémoire autoscale de 51 % n’a pas **pas** dans l’échelle. Pour échelle-, processeur doit être 29 % et mémoire 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Sélectionnez toujours un nombre d’instances de valeur par défaut sécurisée
Le nombre d’instances par défaut est important autoscale met à l’échelle votre service pour ce compte lorsque les mesures ne sont pas disponibles. Par conséquent, sélectionnez un nombre d’instances par défaut qui est sûr pour vos charges de travail.

### <a name="configure-autoscale-notifications"></a>Configurer les notifications d’échelle automatique
Échelle automatique avertit les administrateurs et les collaborateurs de la ressource par courrier électronique si une des conditions suivantes se produit :

- service d’échelle automatique ne parvient pas à effectuer une action.
- Les mesures ne sont pas disponibles pour service d’échelle prendre une décision d’échelle.
- Les métriques sont disponible (restauration) pour prendre une décision d’échelle.
Outre les conditions ci-dessus, vous pouvez configurer des notifications d’e-mail ou webhook pour recevoir une notification pour les actions de l’échelle réussie.
