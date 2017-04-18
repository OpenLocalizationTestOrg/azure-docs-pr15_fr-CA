<properties 
    pageTitle="Comment faire pour utiliser des propriétés dans les stratégies de gestion des API Azure" 
    description="Découvrez comment utiliser les propriétés dans les stratégies de gestion des API Azure." 
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


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Comment faire pour utiliser des propriétés dans les stratégies de gestion des API Azure

Stratégies de gestion de l’API sont une fonctionnalité puissante du système qui permettent de l’éditeur modifier le comportement de l’API de configuration. Les stratégies sont un ensemble d’instructions qui sont exécutées de façon séquentielle dans la demande ou la réponse d’une API. Instructions de stratégie peuvent être construites à l’aide des propriétés, des expressions de stratégie et des valeurs de texte littéral. 

Chaque instance de service de gestion de l’API possède une collection de propriétés de paires clé/valeur qui sont globales pour l’instance de service. Ces propriétés peuvent être utilisées pour gérer des valeurs de chaîne constante dans toutes les stratégies et de configuration de l’API. Chaque propriété possède les attributs suivants.


| Attribut | Type de            | Description                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nom      | chaîne          | Le nom de la propriété. Il peut contenir uniquement des lettres, des chiffres, période, tiret et caractères de soulignement. |
| Valeur     | chaîne          | La valeur de la propriété. Il ne peut pas être vide ou se composer uniquement d’espaces blancs.                           |
| Secret    | valeur booléenne         | Détermine si la valeur est un secret doit être chiffrée ou non.                                |
| Balises      | tableau de chaînes | Facultatif balises lorsque fourni peut être utilisé pour filtrer la liste des propriétés.                               |

Les propriétés sont configurées dans le portail de l’éditeur dans l’onglet **Propriétés** . Dans l’exemple suivant, les trois propriétés sont configurées.

![Propriétés][api-management-properties]

Les valeurs de propriété peuvent contenir des chaînes littérales et des [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx). Le tableau suivant affiche les propriétés de trois exemples précédents et leurs attributs. La valeur de `ExpressionProperty` est une expression de stratégie qui retourne une chaîne contenant la date et l’heure. La propriété `ContosoHeaderValue` est marqué comme un secret, afin que sa valeur n’est pas affichée.

| Nom               | Valeur                      | Secret | Balises    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | False  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | False  |         |

## <a name="to-use-a-property"></a>Pour utiliser une propriété

Pour utiliser une propriété dans une stratégie, placez le nom de la propriété à l’intérieur d’une double paire d’accolades comme `{{ContosoHeader}}`, comme illustré dans l’exemple suivant.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

Dans cet exemple, `ContosoHeader` est utilisé comme nom d’en-tête dans un `set-header` stratégie, et `ContosoHeaderValue` est utilisé en tant que la valeur de cet en-tête. Lorsque cette stratégie est évaluée au cours d’une demande ou une réponse à la passerelle API gestion, `{{ContosoHeader}}` et `{{ContosoHeaderValue}}` sont remplacées par leurs valeurs de propriété respectives.

Propriétés qui peuvent être utilisées comme complet d’attributs ou les valeurs d’élément comme indiqué dans l’exemple précédent, mais peut également être insérés dans ou combinés avec une partie d’une expression de texte littéral, comme illustré dans l’exemple suivant :`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propriétés peuvent également contenir des expressions de stratégie. Dans l’exemple suivant, la `ExpressionProperty` est utilisé.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Lorsque cette stratégie est évaluée, `{{ExpressionProperty}}` est remplacée par sa valeur : `@(DateTime.Now.ToString())`. Dans la mesure où la valeur est une expression de stratégie, l’expression est évaluée et la stratégie poursuit son exécution.

Vous pouvez tester cette sortie dans le portail de développement en appelant une opération qui dispose d’une stratégie avec des propriétés dans la portée. Dans l’exemple suivant, une opération est appelée avec l’exemple précédent de deux `set-header` stratégies avec des propriétés. Notez que la réponse contienne deux en-têtes personnalisés qui ont été configurés à l’aide de stratégies avec les propriétés.

![Portail des développeurs][api-management-send-results]

Si vous examinez la [trace de l’API inspecteur](api-management-howto-api-inspector.md) pour un appel qui inclut les deux précédents exemples de stratégies d’avec les propriétés, vous pouvez voir les deux `set-header` stratégies avec les valeurs de propriété insérées, ainsi que l’évaluation d’expression de stratégie pour la propriété contenant l’expression de stratégie.

![Trace de l’inspecteur de l’API][api-management-api-inspector-trace]

Notez que tandis que les valeurs de propriété peuvent contenir des expressions de stratégie, les valeurs de propriété ne peut pas contenir d’autres propriétés. Si le texte qui contient une référence de propriété est utilisée pour une valeur de propriété, tels que `Property value text {{MyProperty}}`, cette référence de propriété ne sont pas remplacée et sera incluse dans le cadre de la valeur de la propriété.

## <a name="to-create-a-property"></a>Pour créer une propriété

Pour créer une propriété, cliquez sur **Ajouter une propriété** sous l’onglet **Propriétés** .

![Ajout de propriété][api-management-properties-add-property-menu]

**Nom** et la **valeur** sont des valeurs requises. Si la valeur de cette propriété n’est un secret, la case **Ceci est un secret** . Entrez une ou plusieurs balises facultatives pour aider à organiser vos propriétés, puis cliquez sur **Enregistrer**.

![Ajout de propriété][api-management-properties-add-property]

Lorsqu’une propriété est enregistrée, la zone de texte de **la propriété de recherche** est rempli avec le nom de la nouvelle propriété et la propriété nouveau s’affiche. Pour afficher toutes les propriétés, désactivez la zone de texte de **la propriété de recherche** et appuyez sur ENTRÉE.

![Propriétés][api-management-properties-property-saved]

Pour plus d’informations sur la création d’une propriété à l’aide de l’API REST, voir [Création d’une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Pour modifier une propriété

Pour modifier une propriété, cliquez sur **Modifier** en regard de la propriété à modifier.

![Modifier la propriété][api-management-properties-edit]

Apportez les modifications souhaitées, puis cliquez sur **Enregistrer**. Si vous modifiez le nom de propriété, toutes les stratégies qui font référence à cette propriété sont automatiquement mis à jour pour utiliser le nouveau nom.

![Modifier la propriété][api-management-properties-edit-property]

Pour plus d’informations sur la modification d’une propriété à l’aide de l’API REST, consultez [modification d’une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Pour supprimer une propriété

Pour supprimer une propriété, cliquez sur **Supprimer** en regard de la propriété à supprimer.

![Supprimer la propriété][api-management-properties-delete]

Cliquez sur **Oui, le supprimer** pour confirmer.

![Confirmer la suppression][api-management-delete-confirm]

>[AZURE.IMPORTANT] Si la propriété est référencée par toutes les stratégies, vous ne pourrez pas supprimer correctement jusqu'à ce que vous supprimiez la propriété de toutes les stratégies qui l’utilisent.

Pour plus d’informations sur la suppression d’une propriété à l’aide de l’API REST, consultez [suppression d’une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Recherche et filtre de propriétés

L’onglet **Propriétés** inclut des fonctionnalités pour vous aider à gérer vos propriétés de filtrage et de recherche. Pour filtrer la liste des propriétés par nom de propriété, entrer un terme de recherche dans la zone de texte de **la propriété de recherche** . Pour afficher toutes les propriétés, désactivez la zone de texte de **la propriété de recherche** et appuyez sur ENTRÉE.

![Recherche][api-management-properties-search]

Pour filtrer la liste des propriétés des valeurs de balise, entrez une ou plusieurs balises dans la zone de texte de **filtre par des balises** . Pour afficher toutes les propriétés, désactivez la **Filtrer par balises de** la zone de texte et appuyez sur ENTRÉE.

![Filtre][api-management-properties-filter]

## <a name="next-steps"></a>Étapes suivantes

-   Pour en savoir plus sur l’utilisation des stratégies
    -   [Stratégies de gestion de l’API](api-management-howto-policies.md)
    -   [Référence de la stratégie](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Regarder une vidéo de présentation

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

