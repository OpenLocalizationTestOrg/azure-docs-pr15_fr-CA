<properties 
    pageTitle="Stratégies de gestion des API Azure | Microsoft Azure" 
    description="Découvrez comment créer, modifier et configurer des stratégies de gestion de l’API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


#<a name="policies-in-azure-api-management"></a>Stratégies de gestion des API Azure

Dans Gestion de l’API Azure, les stratégies sont une fonctionnalité puissante du système qui permettent de l’éditeur modifier le comportement de l’API de configuration. Les stratégies sont un ensemble d’instructions qui sont exécutées de façon séquentielle dans la demande ou la réponse d’une API. Instructions les plus courants incluent la conversion du format JSON à partir de XML et appellent des taux de limitation pour restreindre le montant des appels entrants à partir d’un développeur. Plus grand nombre de stratégies sont disponibles.

Consultez la [Stratégie de référence][] pour une liste complète des instructions de stratégie et leurs paramètres.

Les stratégies sont appliquées à l’intérieur de la passerelle qui se trouve entre le client de l’API et l’API managée. La passerelle reçoit toutes les demandes et généralement les transfère en l’état à l’API sous-jacente. Toutefois une stratégie peut appliquer des modifications à la requête entrante et la réponse sortante.

Les expressions de stratégie peuvent être utilisées en tant que valeurs d’attribut ou de valeurs de texte dans une des stratégies de gestion de l’API, sauf indication contraire de la stratégie. Certaines stratégies comme les stratégies de [flux de contrôle][] et de [définir de variable][] sont basées sur des expressions de stratégie. Pour plus d’informations, consultez [Stratégies avancées][] et des [expressions de stratégie][].

## <a name="scopes"> </a>Comment faire pour configurer des stratégies
Stratégies peuvent être configurées au niveau d’un [produit][], une [API][] ou [opération][]ou globalement. Pour configurer une stratégie, accédez à l’éditeur de stratégies dans le portail de publisher.

![Menu stratégies][policies-menu]

L’éditeur de stratégies se compose de trois sections principales : l’étendue de la stratégie (en haut), la définition de la stratégie dans laquelle les stratégies sont modifiées (à gauche) et les instructions de liste (à droite) :

![Éditeur de stratégies][policies-editor]

Pour commencer à configurer une stratégie, que vous devez d’abord sélectionner le champ d’application à laquelle la stratégie doit s’appliquer. Dans la capture d’écran ci-dessous la **Starter** produit est sélectionné. Notez que le symbole carré en regard du nom de la stratégie indique qu’une stratégie est déjà appliquée à ce niveau.

![Champ d’application][policies-scope]

Dans la mesure où une stratégie a déjà été appliquée, la configuration est affichée dans la vue de la définition.

![Configurer][policies-configure]

La stratégie s’affiche en lecture seule dans un premier temps. Pour modifier la définition, cliquez sur l’action de **Configurer la stratégie** .

![Modifier][policies-edit]

La définition de la stratégie est un simple document XML qui décrit une séquence d’instructions entrantes et sortantes. Le fichier XML peut être modifié directement dans la fenêtre de définition. Une liste d’instructions est fournie vers la droite et les instructions applicables à la portée actuelle sont activées et mis en surbrillance ; comme cela est indiqué par l’instruction de **Vitesse d’appeler limite** dans la capture d’écran ci-dessus.

Cliquer sur une instruction activée ajoutera le XML approprié à l’emplacement du curseur dans la vue de la définition. 

>[AZURE.NOTE] Si la stratégie que vous souhaitez ajouter n’est pas activée, assurez-vous que vous êtes dans l’étendue correcte pour cette stratégie. Chaque instruction de stratégie est conçue pour une utilisation dans certaines zones et les sections de la stratégie. Pour passer en revue les sections de la stratégie et les étendues pour une stratégie, consultez la section **utilisation** de cette stratégie dans la [Stratégie de référence][].

Une liste complète des instructions de stratégie et leurs paramètres sont disponibles dans la [Stratégie de référence][].

Par exemple, pour ajouter une nouvelle instruction afin de limiter les demandes entrantes aux adresses IP spécifiées, placez le curseur à l’intérieur du contenu de la `inbound` élément XML et cliquez sur l’instruction de **l’appelant de limiter IPs** .

![Stratégies de restriction][policies-restrict]

Cette action ajoute un extrait XML à la `inbound` élément qui fournit des instructions sur la configuration de l’instruction.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Pour limiter les demandes entrantes et d’accepter uniquement ceux d’une adresse IP 1.2.3.4 modifier le code XML comme suit :

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Enregistrer][policies-save]

Une fois terminée configuration les instructions pour la stratégie, cliquez sur **Enregistrer** et la modification sera propagée vers la passerelle API gestion immédiatement.

##<a name="sections"> </a>Configuration de la stratégie présentation

Une stratégie est une série d’instructions qui s’exécutent dans l’ordre pour une demande et une réponse. La configuration est divisée de manière appropriée en `inbound`, `backend`, `outbound`, et `on-error` sections comme indiqué dans la configuration suivante.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

S’il existe une erreur lors du traitement d’une demande, les étapes restantes du `inbound`, `backend`, ou `outbound` sections sont ignorées et l’exécution se passe pour les instructions de la `on-error` section. En plaçant des instructions de stratégie dans le `on-error` section, vous pouvez examiner l’erreur à l’aide de la `context.LastError` Inspecter et personnaliser la réponse d’erreur à l’aide de la propriété, la `set-body` stratégie et configurer ce qui se passe si une erreur se produit. Il existe des codes d’erreur pour des étapes prédéfinies et les erreurs qui peuvent se produire lors du traitement des instructions de stratégie. Pour plus d’informations, consultez [Gestion des erreurs dans les stratégies de gestion de l’API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Dans la mesure où les stratégies peuvent être spécifiés à différents niveaux (global, produit, api et opération) la configuration offre un moyen vous permettant de spécifier l’ordre dans lequel les instructions de la définition de la stratégie exécutent par rapport à la stratégie du parent. 

Les zones de stratégie sont évaluées dans l’ordre suivant.

1. Étendue globale
2. Portée du produit
3. Portée de l’API
4. Portée de l’opération

Les instructions qu’ils contiennent sont évaluées en fonction de la position de la `base` élément, s’il est présent.

Par exemple, si vous avez une stratégie au niveau global et une stratégie configurée pour une API, puis chaque fois que cette API particulière est utilisée deux stratégies seront appliquées. API de gestion autorise un ordre déterministe des instructions de stratégie combinée via l’élément de base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Dans l’exemple de données de définition de stratégie ci-dessus, les `cross-domain` instruction exécuterait avant les stratégies plus élevés qui seraient à son tour, être suivie par le `find-and-replace` stratégie.

Si la même stratégie apparaît deux fois dans l’instruction de stratégie, la stratégie la plus récemment évaluée est appliquée. Vous pouvez l’utiliser pour substituer des stratégies définies avec une portée plus élevée. Pour afficher les stratégies dans la portée actuelle dans l’éditeur de stratégie, cliquez sur **Recalculer une stratégie efficace pour l’étendue sélectionnée**.

Notez ne qu’aucune stratégie parent de stratégie globale et l’utilisation de la `<base>` élément qu’il n’a aucun effet. 

## <a name="next-steps"></a>Étapes suivantes

Consultez suivant la vidéo sur les expressions de stratégie.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Référence de la stratégie]: api-management-policy-reference.md
[Produit]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Opération]: api-management-howto-add-operations.md

[Stratégies avancées]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flux de contrôle]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Jeu variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expressions de stratégie]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
