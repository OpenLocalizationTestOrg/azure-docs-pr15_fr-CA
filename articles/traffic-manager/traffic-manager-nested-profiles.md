<properties
    pageTitle="Imbriqué des profils de trafic Manager | Microsoft Azure"
    description="Cet article explique la fonctionnalité « Profils imbriqués » du Gestionnaire de trafic Azure"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="nested-traffic-manager-profiles"></a>Profils de Traffic Manager imbriqués

Traffic Manager comprend une gamme de méthodes de routage de trafic qui vous permettent de contrôler comment Traffic Manager choisit le point de terminaison doit recevoir le trafic de chaque utilisateur final. Pour plus d’informations, consultez [méthodes de routage de trafic de Traffic Manager](traffic-manager-routing-methods.md).

Chaque profil de Traffic Manager indique une seule méthode de routage du trafic. Toutefois, il existe des scénarios qui requièrent le routage du trafic plus sophistiqué que le routage fournie par un seul profil de Traffic Manager. Vous pouvez imbriquer des profils de Traffic Manager pour combiner les avantages de plus d’une méthode de routage du trafic. Profils imbriqués permettent de modifier le comportement de Traffic Manager par défaut pour la prise en charge de plus grande et les déploiements d’applications plus complexes.

Les exemples suivants illustrent l’utilisation de profils de Traffic Manager imbriqué dans divers scénarios.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemple 1 : Combinaison de « Performances » et « Weighted » le routage du trafic

Supposons que vous avez déployé une application dans Azure régions suivantes : ouest des États-Unis, l’Europe de l’ouest et Asie de l’est. Méthode de routage de trafic « Performances » du Traffic Manager vous permet de distribuer le trafic à la région le plus proche de l’utilisateur.

![Profil du Gestionnaire de trafic unique][1]

Maintenant, supposons que vous souhaitez tester une mise à jour à votre service avant de le déployer plus largement. Vous souhaitez utiliser la méthode de routage de trafic « pondérée » pour diriger un petit pourcentage du trafic à votre déploiement de test. Permet de paramétrer le déploiement de test ainsi que le déploiement en production existant en Europe de l’ouest.

Vous ne pouvez pas combiner les deux « Weighted » et « performances-le routage du trafic dans un seul profil. Pour prendre en charge ce scénario, vous créez un profil de gestionnaire de trafic à l’aide de deux points de terminaison Europe de l’ouest et la méthode de routage de trafic « Weighted ». Ensuite, vous ajoutez ce profil 'child' sous la forme d’un point de terminaison pour le profil « parent ». Le profil de parent utilise la méthode de routage du trafic de performances toujours et contient les autres déploiements globaux comme points de terminaison.

Le diagramme suivant illustre cet exemple :

![Profils de Traffic Manager imbriqués][2]

Dans cette configuration, le trafic dirigé via le profil parent répartit le trafic sur les régions normalement. Au sein de l’Europe de l’ouest, le profil imbriqué distribue le trafic vers les points de terminaison de production et de test en fonction des poids affectés.

Lorsque le profil parent utilise la méthode de routage de trafic 'Performances', chaque point de terminaison doit être affecté à un emplacement. L’emplacement est affecté lorsque vous configurez le point de terminaison. Choisissez la région Azure le plus proche de votre déploiement. Les zones Azure sont les valeurs d’emplacement pris en charge par la Table de latence d’Internet. Pour plus d’informations, consultez [Traffic Manager 'Performances' méthode de routage de trafic](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemple 2 : Surveillance de point de terminaison dans les profils d’imbriqués

Traffic Manager surveille activement l’état de santé de chaque point de terminaison de service. Si un point de terminaison est défectueux, Traffic Manager dirige les utilisateurs vers les autres points de terminaison afin de préserver la disponibilité de votre service. Ce comportement de point de terminaison de surveillance et de basculement s’applique à toutes les méthodes de routage du trafic. Pour plus d’informations, consultez [Analyse de point de terminaison de Traffic Manager](traffic-manager-monitoring.md). Contrôle de point de terminaison fonctionne différemment pour les profils imbriqués. Avec les profils imbriqués, le profil de parent n’effectue pas les vérifications de fonctionnement de l’enfant directement. Au lieu de cela, l’état de santé des points de terminaison du profil de l’enfant est utilisée pour calculer l’état général du profil de l’enfant. Ces informations de santé se propage la hiérarchie imbriquée de profil. Le profil parent de cette agrégation santé pour déterminer s’il faut diriger le trafic vers le profil de l’enfant. Reportez-vous à la section [Forum aux questions](#faq) de cet article pour plus d’informations sur la surveillance de l’intégrité des profils imbriqués.

Revenir à l’exemple précédent, supposons que le déploiement de la production en Europe de l’ouest échoue. Par défaut, le profil 'child' dirige tout le trafic vers le déploiement de test. Si le déploiement de test échoue également, le profil parent détermine que le profil de l’enfant ne doit pas recevoir le trafic étant donné que tous les points de terminaison enfant sont défectueux. Ensuite, le profil parent répartit le trafic vers les autres régions.

![Basculement de profil imbriqué (par défaut)][3]

Vous serez peut-être satisfait de cette disposition. Ou vous pouvez être concerné va maintenant que tout le trafic pour l’Europe de l’ouest pour le déploiement de test au lieu d’un trafic de sous-ensemble limité. Quel que soit l’état de santé du déploiement test, que vous voulez basculer vers les autres régions lorsque le déploiement en production en Europe de l’ouest échoue. Pour activer ce basculement, vous pouvez spécifier le paramètre « MinChildEndpoints » lors de la configuration du profil de l’enfant sous la forme d’un point de terminaison dans le profil du parent. Le paramètre détermine le nombre minimal de points de terminaison disponibles dans le profil de l’enfant. La valeur par défaut est '1'. Dans ce scénario, vous définissez la valeur de MinChildEndpoints 2. Inférieure à ce seuil, le profil de parent prend en considération le profil tout enfant à ne pas être disponibles et dirige le trafic vers les autres points de terminaison.

La figure suivante illustre cette configuration :

![Imbriqué de basculement du profil avec 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>La méthode de routage de trafic 'Priority' distribue tout le trafic vers un seul point de terminaison. Il est donc peu d’utilité dans un paramètre MinChildEndpoints autre que '1' pour le profil d’un enfant.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemple 3 : Les régions de basculement par ordre de priorité dans le routage du trafic 'Performances'

Le comportement par défaut de la méthode de routage de trafic 'Performances' est conçu pour éviter trop de chargement suivant le plus proche du point de terminaison et à l’origine d’une série en cascade d’échecs. En cas de défaillance d’un point de terminaison, tout le trafic qui devrait avoir été dirigé vers ce point de terminaison est réparti uniformément aux autres points de terminaison dans toutes les régions.

![Trafic de 'Performances' routage avec basculement sur incident de la valeur par défaut][5]

Cependant, supposons que vous préférez le basculement de trafic d’Europe de l’ouest des États-Unis Ouest, uniquement de diriger le trafic vers d’autres régions lorsque les deux points de terminaison ne sont pas disponibles. Vous pouvez créer cette solution à l’aide d’un profil de l’enfant avec la méthode de routage de trafic 'Priority'.

![Trafic de 'Performances' routage avec basculement préférentiel][6]

Dans la mesure où le point de terminaison d’Europe de l’Ouest a une priorité plus élevée que le point de terminaison États-Unis Ouest, tout le trafic est envoyé à l’extrémité de l’Europe de l’ouest lorsque les deux extrémités sont en ligne. En cas d’échec de l’Europe de l’ouest, le trafic est dirigé vers ouest des États-Unis. Avec le profil imbriqué, le trafic est dirigé pour l’Asie de l’est uniquement lorsque les Europe de l’ouest et États-Unis Ouest échouent.

Vous pouvez répéter ce modèle pour toutes les régions. Remplacer tous les trois points de terminaison dans le profil de parent avec trois profils enfant, chacune fournissant une séquence par ordre de priorité de basculement.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemple 4 : Contrôle 'Performances' le routage du trafic entre plusieurs points de terminaison dans la même région.

Supposons que la méthode de routage de trafic est utilisé dans un profil qui a le plus d’un point de terminaison dans une région donnée 'performances'. Par défaut, le trafic dirigé vers cette région est réparti équitablement entre tous les points de terminaison disponibles dans cette région.

![« Performances » du trafic de distribution de routage du trafic de dans la région (par défaut)][7]

Au lieu d’ajouter plusieurs points de terminaison en Europe de l’ouest, ces points de terminaison sont placés dans un profil distinct. Le profil de l’enfant est ajouté au parent en tant que le point de terminaison uniquement en Europe de l’ouest. Les paramètres du profil de l’enfant peuvent contrôler la distribution du trafic avec l’Europe de l’ouest en activant le routage de trafic basé sur des priorités ou pondéré dans cette région.

![Trafic de 'Performances' routage avec une distribution de trafic de dans la région personnalisée][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemple 5 : Paramètres de surveillance par point de terminaison

Supposons que vous utilisez le Gestionnaire de trafic pour passer facilement le trafic provenant d’un héritage sur site site web vers une nouvelle version en nuage hébergée dans Azure. Pour l’ancien site, que vous souhaitez utiliser l’URI de la page d’accueil pour surveiller la santé du site. Mais pour la nouvelle version basée sur le nuage, vous implémentez une page personnalisée de surveillance (chemin d’accès « / monitor.aspx ») qui inclut des contrôles supplémentaires.

![Point de terminaison de Traffic Manager contrôle (comportement par défaut)][9]

Les paramètres de contrôle dans un profil de gestionnaire de trafic s’appliquent à tous les points de terminaison au sein d’un seul profil. Avec les profils imbriqués, vous utilisez un profil différent par site permet de définir différents paramètres d’analyse.

![Traffic Manager de point de terminaison de surveillance avec les paramètres de per point de terminaison][10]

## <a name="faq"></a>FAQ

### <a name="how-do-i-configure-nested-profiles"></a>Comment pour configurer les profils imbriqués ?

Les profils de Traffic Manager imbriqués peuvent être configurés en utilisant le Gestionnaire de ressources Azure et classique API reste Azure, applets de commande PowerShell de Azure et inter-plateforme Azure CLI commands. Ils sont également pris en charge via le nouveau portail Azure. Ils ne sont pas pris en charge dans le portail classique.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Le nombre de couches d’imbrication est Gestionnaire de trafic pris en charge ?

Vous pouvez imbriquer des profils jusqu'à 10 niveaux de profondeur. 'Effectue une boucle' n’est pas autorisée.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Puis-je combiner des autres types de point de terminaison avec des profils d’enfants imbriqués dans le même profil de Traffic Manager ?

Oui. Il n’y a pas de restrictions sur comment combiner des points de terminaison de différents types au sein d’un profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Comment le modèle de facturation s’applique pour les profils de Nested ?

Il est non négative de tarification de l’utilisation de profils imbriqués.

Facturation de Traffic Manager comporte deux composants : les vérifications de fonctionnement de point de terminaison et des millions de requêtes DNS

- Les vérifications de fonctionnement de point de terminaison : il n’y a aucun frais pour un profil de l’enfant lorsqu’il est configuré comme un point de terminaison dans un profil de parent. Analyse des points de terminaison dans le profil de l’enfant sont facturés de la manière habituelle.
- Requêtes DNS : chaque requête est comptée seulement une fois. Une requête sur un profil de parent retourne un point de terminaison à partir d’un profil de l’enfant est comptée par rapport au profil de parent uniquement.

Pour plus de détails, reportez-vous à la [page tarification de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Y a-t-il un impact sur les performances des profils imbriqués ?

N° Il n’y a aucun impact sur les performances encourus lors de l’utilisation de profils imbriqués.

Les serveurs de noms du Gestionnaire de trafic traversent la hiérarchie de profils en interne lors du traitement de chaque requête DNS. Une requête DNS à un profil de parent peut recevoir une réponse DNS avec un point de terminaison à partir d’un profil de l’enfant. Un seul enregistrement CNAME est utilisé si vous utilisez un seul profil ou profils imbriqués. Il est inutile de créer un enregistrement CNAME pour chaque profil dans la hiérarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Comment Traffic Manager calcule l’état de santé d’un point de terminaison imbriqué dans un profil de parent ?

Le profil de parent n’effectue pas les vérifications de fonctionnement de l’enfant directement. Au lieu de cela, l’état de santé des points de terminaison du profil de l’enfant sont utilisés pour calculer l’état général du profil de l’enfant. Cette information est propagée la hiérarchie imbriquée profil pour déterminer l’état de santé du point de terminaison imbriqué. Le profil de parent utilise cette intégrité agrégée pour déterminer si le trafic peut être dirigé vers l’enfant.

Le tableau suivant décrit le comportement du Gestionnaire de trafic des contrôles sanitaires pour un point de terminaison imbriqué.

|État du Gestionnaire de profils d’enfant|État du Moniteur du point de terminaison parent|Notes|
|---|---|---|
|Désactivé. Le profil de l’enfant a été désactivé.|Arrêté|L’état du point de terminaison parent est arrêté, ne pas désactivé. L’état désactivé est réservé pour indiquer que vous avez désactivé le point de terminaison dans le profil du parent.|
|Dégradé. Point de terminaison de profil au moins un enfant est dans un état dégradé.| Online : le nombre de points de terminaison de ligne dans le profil de l’enfant est au moins la valeur de MinChildEndpoints.<BR>CheckingEndpoint : le nombre de points de terminaison en ligne plus CheckingEndpoint dans le profil de l’enfant est d’au moins la valeur de MinChildEndpoints.<BR>Dégradé : dans le cas contraire.|Le trafic est acheminé vers un point de terminaison de l’état CheckingEndpoint. Si MinChildEndpoints est trop élevée, le point de terminaison est toujours altérée.|
|En ligne. Point de terminaison de profil au moins un enfant est un état en ligne. Aucun point de terminaison n’est dans un état dégradé.|Voir ci-dessus.||
|CheckingEndpoints. Point de terminaison de profil au moins un enfant est 'CheckingEndpoint'. Aucun point de terminaison n’est « En ligne » ou « Dégradé »|Même que ci-dessus.||
|Inactif. Tous les points de terminaison de profil d’enfant sont soit désactivé ou arrêté, ou si ce profil ne possède aucun point de terminaison.|Arrêté||


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le [fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md)

Découvrez comment [créer un profil de Traffic Manager](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

