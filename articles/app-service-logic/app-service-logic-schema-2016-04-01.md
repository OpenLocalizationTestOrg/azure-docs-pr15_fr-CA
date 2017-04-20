<properties 
    pageTitle="Nouvelle version du schéma 2016-06-01 | Microsoft Azure" 
    description="Apprenez à écrire la définition de JSON de la dernière version des applications de logique" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Nouvelle version du schéma 2016-06-01

Le nouveau schéma et une API de version pour les applications de logique a un certain nombre d’améliorations qui améliorent la fiabilité et la facilité d’utilisation des applications de logique. Il existe 3 principales différences :

1. Ajout d’étendues, qui sont des actions qui contiennent un ensemble d’actions.
1. Conditions et boucles sont des actions de première classe
1. L’exécution de commande plus détaillée via `runAfter` propriété (qui remplace `dependsOn`)

Pour plus d’informations sur la mise à niveau de vos applications de logique à partir du schéma 2015-08-01-aperçu vers le schéma de 2016-06-01, [consultez la section mise à niveau ci-dessous.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. les étendues de

Un des changements plus importants dans ce schéma est l’ajout d’étendues et la possibilité d’imbriquer des actions dans d’autres.  Ceci est utile lorsque le regroupant un ensemble d’actions, ou lorsque vous avez besoin d’imbriquer des actions dans d’autres (par exemple une condition peut contenir une autre condition).  Plus d’informations sur la syntaxe de portée peuvent être trouvés [ici](app-service-logic-loops-and-scopes.md), mais un exemple simple de portée, vous pouvez trouver ci-dessous :


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. conditions et boucles de modifications

Dans les versions précédentes du schéma, conditions et des boucles ont des paramètres associés à une seule action.  Cette restriction a été levée dans ce schéma et conditions et des boucles affichent sous la forme d’un type d’action.  Vous trouverez plus d’informations [dans cet article](app-service-logic-loops-and-scopes.md), et un exemple simple d’une action de condition est illustré ci-dessous :

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. propriété de RunAfter

Les nouveaux `runAfter` du remplacement de propriété `dependsOn` afin de permettre une plus grande précision dans l’exécution de la commande.  `dependsOn`a été synonyme de « l’action exécuté et a réussi, » cependant autant de fois que vous avez besoin exécuter une action si l’action précédente a réussi, échec ou ignorés.  `runAfter`permet de cette flexibilité.  Il est un objet qui spécifie tous les noms d’action qu'il s’exécutera après et définit un tableau d’état qui sont acceptables pour déclencher à partir de.  Par exemple si vous souhaitez exécuter après l’étape A a réussi et l’étape B a réussi ou a échoué, vous devez le créer suivantes `runAfter` propriété :

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Mise à niveau vers le schéma de 2016-06-01

Mise à niveau vers le nouveau schéma de 2016-06-01 ne prend que quelques étapes.  Vous trouverez plus d’informations sur les modifications du schéma [dans cet article](app-service-logic-schema-2016-04-01.md).  Le processus de mise à niveau inclut l’exécution du script de mise à niveau, l’enregistrement sous la forme d’une nouvelle application logique et pouvant écraser vieille application de logique si nécessaire.

1. Ouvrez votre application de logique en cours.
1. Cliquez sur le bouton de **Mise à jour de schéma** dans la barre d’outils
   
    ![][1]
   
    La définition de la mise à niveau sera retournée.  Vous pouvez copier et coller dans une définition de ressource si vous avez besoin, mais nous **recommandons vivement** le bouton **Enregistrer sous** vous permet de garantir la connexion toutes les références sont valides dans l’application de la logique de mise à niveau.
1. Cliquez sur le bouton **Enregistrer sous** dans la barre d’outils de la lame de mise à niveau.
1. Remplir l’état application logique et du nom et cliquez sur **créer** pour déployer votre application de la logique de mise à niveau.
1. Vérifiez que votre application de la logique de mise à niveau fonctionne comme prévu.

    >[AZURE.NOTE] Si vous utilisez un déclencheur manuel ou sur demande, l’URL de rappel aura changé dans votre nouvelle application logique.  Utilisez la nouvelle URL pour vérifier qu’il fonctionne de bout en bout, et vous pouvez cloner sur votre application logique existant afin de préserver les URL précédentes.

1. *Facultatif* Utilisez le bouton **Dupliquer** dans la barre d’outils (adjacent à l’icône de **Mise à jour de schéma** dans l’image ci-dessus) pour remplacer votre application logique précédente par la nouvelle version de schéma.  Cela est nécessaire uniquement si vous souhaitez conserver le même ID de ressource ou demander des URL de déclencheur de votre application logique.

### <a name="upgrade-tool-notes"></a>Notes de l’outil de mise à niveau

#### <a name="condition-mapping"></a>Mappage de condition

L’outil fera de son mieux pour regrouper les actions de la branche de true et false dans une étendue dans la définition de la mise à niveau.  En particulier le modèle Concepteur de `@equals(actions('a').status, 'Skipped')` doit s’afficher sous la forme d’une `else` action.  Toutefois si l’outil détecte qu’il ne le reconnaît pas de modèles seront potentiellement créer des conditions distinctes pour la branche false et la valeur true.  Les actions peuvent être re-mappées valider la mise à niveau si nécessaire.

#### <a name="foreach-with-condition"></a>ForEach avec Condition
  
Le modèle précédent d’une boucle foreach avec une condition par article peut être répliqué dans le nouveau schéma avec l’action de filtrage.  Cela doit se produire automatiquement sur la mise à niveau.  La condition devienne une action de filtrage avant la boucle foreach (pour retourner uniquement un tableau d’éléments qui correspondent à la condition), et ce tableau est passé à l’action de foreach.  Vous pouvez afficher un exemple de cette [dans cet article](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Balises de ressources

Les balises de ressources seront supprimés sur la mise à niveau, et vous devez les définir à nouveau pour le workflow mis à niveau.

## <a name="other-changes"></a>Autres modifications

### <a name="manual-trigger-renamed-to-request-trigger"></a>Renommé en demande de déclencheur de déclenchement manuel

Le type `manual` a été désapprouvée et renommé en `request` avec le type de `http`.  Il s’agit plus cohérente avec le type de modèle utilisé pour générer le déclencheur.

### <a name="new-filter-action"></a>Nouvelle action de « filtre »

Si vous travaillez avec un grand tableau et de la nécessité de filtrer sur un ensemble réduit d’éléments, vous pouvez utiliser le nouveau type de « filtre ».  Il accepte un tableau et une condition et évaluer la condition pour chaque élément et retourner un tableau d’éléments qui satisfont à la condition.

### <a name="foreach-and-until-action-restrictions"></a>ForEach et jusqu'à ce que les restrictions de l’action

Foreach et jusqu'à ce que la boucle sont limités à une seule action.

### <a name="trackedproperties-on-actions"></a>TrackedProperties sur les Actions

Actions disposez désormais d’une propriété supplémentaire (frère du `runAfter` et `type`) appelé `trackedProperties`.  Il s’agit d’un objet qui spécifie certaines actions entrées ou des sorties à inclure dans la télémétrie Diagnostic Azure qui est émis dans le cadre d’un flux de travail.  Par exemple :

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
- [Utiliser la définition de flux de travail d’application logique](app-service-logic-author-definitions.md)
- [Créer un modèle de déploiement d’application logique](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png
