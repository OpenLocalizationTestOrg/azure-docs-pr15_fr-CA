<properties 
    pageTitle="Comment personnaliser le portail des développeurs à l’aide de modèles de gestion des API Azure | Microsoft Azure" 
    description="Apprenez à personnaliser le portail des développeurs Azure API gestion à l’aide de modèles." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Comment personnaliser le portail des développeurs Azure API gestion à l’aide de modèles

Gestion des API Azure fournit plusieurs fonctionnalités de personnalisation pour permettre aux administrateurs de [Personnaliser l’apparence du portail développeur](api-management-customize-portal.md), ainsi que pour personnaliser le contenu des pages de portail développeur à l’aide d’un ensemble de modèles qui configurent le contenu des pages eux-mêmes. À l’aide de la syntaxe de [DotLiquid](http://dotliquidmarkup.org/) et un ensemble fourni des ressources de chaînes localisées, des icônes et des contrôles de la page, vous disposez d’une grande flexibilité pour configurer le contenu des pages comme vous le souhaitez à l’aide de ces modèles.

## <a name="developer-portal-templates-overview"></a>Vue d’ensemble des modèles de portail développeur

Modèles de portail développeur sont gérés dans le portail de développement par les administrateurs de l’instance de service de gestion de l’API. Pour gérer les modèles de développement, accédez à votre instance de service de gestion de l’API dans le portail classique Azure et cliquez sur **Parcourir**.

![Portail des développeurs][api-management-browse]

Si vous êtes déjà dans le portail de publisher, vous pouvez accéder le portail des développeurs en cliquant sur le **portail des développeurs**.

![Menu de portail développeur][api-management-developer-portal-menu]

Pour accéder aux modèles portail développeur, cliquez sur l’icône personnaliser à gauche pour afficher le menu de personnalisation, cliquez sur **modèles**.

![Modèles de portail développeur][api-management-customize-menu]

La liste de modèles affiche les différentes catégories de modèles couvrant les différentes pages du portail de développement. Chaque modèle est différent, mais les étapes pour les modifier et publier les modifications apportées sont les mêmes. Pour modifier un modèle, cliquez sur le nom du modèle.

![Modèles de portail développeur][api-management-templates-menu]

Cliquer sur un modèle permet d’atteindre la page du portail développeur qui est personnalisable par ce modèle. Dans cet exemple, la **liste des produits** du modèle s’affiche. Le modèle de **produit liste** contrôle la zone de l’écran indiqué par le rectangle rouge. 

![Modèle de liste de produits][api-management-developer-portal-templates-overview]

Certains modèles, comme les modèles de **Profil utilisateur** , personnaliser des différentes parties de la même page. 

![Modèles de profil utilisateur][api-management-user-profile-templates]

L’éditeur pour chaque modèle de portail développeur a deux sections affichées au bas de la page. La partie gauche affiche le volet d’édition du modèle, et la partie droite affiche le modèle de données pour le modèle. 

Le volet de modification de modèle contient le balisage qui contrôle l’apparence et le comportement de la page correspondante dans le portail des développeurs. Le balisage dans le modèle utilise la syntaxe [DotLiquid](http://dotliquidmarkup.org/) . Un éditeur populaires pour DotLiquid est [DotLiquid pour les concepteurs](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Toutes les modifications apportées au modèle lors de la modification sont affichées en temps réel dans le navigateur, mais ne sont pas visibles à vos clients jusqu'à ce que vous [enregistrez](#to-save-a-template) et [publiez](#to-publish-a-template) le modèle.

![Balisage de modèle][api-management-template]

Le volet de **données de modèle** fournit un guide pour le modèle de données pour les entités qui sont disponibles pour une utilisation dans un modèle particulier. Il fournit ce guide en affichant les données réelles qui sont actuellement affichées dans le portail des développeurs. Vous pouvez développer les volets du modèle en cliquant sur le rectangle dans l’angle supérieur droit du volet de **données du modèle** .

![Modèle de données][api-management-template-data]

Dans l’exemple précédent, il existe deux produits affichés dans le portail de développement qui ont été récupérées à partir des données affichées dans le volet de **données de modèle** , comme illustré dans l’exemple suivant.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

Le balisage dans le modèle de **liste de produits** traite les données afin de fournir la sortie désirée en effectuant une itération sur la collection de produits pour afficher des informations et un lien pour chacun des produits. Remarque le `<search-control>` et `<page-control>` les éléments dans le balisage. Elles contrôlent l’affichage de la recherche et la pagination des contrôles de la page. `ProductsStrings|PageTitleProducts`est une référence de chaîne localisée qui contient le `h2` texte de l’en-tête de la page. Pour une liste de chaînes ressources et icônes disponibles pour les contrôles de la page dans les modèles de portail développeur, voir [référence de modèles portail de gestion de l’API du développeur](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Pour enregistrer un modèle

Pour enregistrer un modèle, cliquez sur Enregistrer dans l’éditeur de modèles.

![Enregistrer le modèle][api-management-save-template]

Les modifications enregistrées ne sont pas actifs dans le portail de développement jusqu'à leur publication.

## <a name="to-publish-a-template"></a>Pour publier un modèle

Les modèles enregistrés peuvent être publiés individuellement, ou tous ensemble. Pour publier un modèle particulier, cliquez sur Publier dans l’éditeur de modèles.

![Modèle de publication][api-management-publish-template]

Cliquez sur **Oui** pour confirmer que le modèle soit actif sur le portail des développeurs.

![Confirmer la publication][api-management-publish-template-confirm]

Pour publier toutes les versions de modèle actuellement non publié, cliquez sur **Publier** dans la liste modèles. Modèles non publiés sont désignées par un astérisque placé après le nom du modèle. Dans cet exemple, les modèles de **liste de produits** et de **produit** sont en cours de publication.

![Publier des modèles][api-management-publish-templates]

Cliquez sur **publier les personnalisations** pour confirmer.

![Confirmer la publication][api-management-publish-customizations]

Modèles récemment publiés sont effectives immédiatement dans le portail des développeurs.

## <a name="to-revert-a-template-to-the-previous-version"></a>Pour revenir à la version précédente

Pour revenir à la précédente version publiée, cliquez sur Rétablir dans l’éditeur de modèles.

![Rétablir le modèle][api-management-revert-template]

Cliquez sur **Oui** pour confirmer.

![Confirmer][api-management-revert-template-confirm]

La version précédemment publiée d’un modèle est en ligne dans le portail de développement une fois la restauration terminée.

## <a name="to-restore-a-template-to-the-default-version"></a>Pour restaurer un modèle de la version par défaut

Restauration des modèles à leur version par défaut est un processus en deux étapes. Tout d’abord, les modèles doivent être restaurées, et puis les versions restaurées doivent être publiées.

Pour restaurer un seul modèle à la version par défaut, cliquez sur Restaurer dans l’éditeur de modèles.

![Rétablir le modèle][api-management-reset-template]

Cliquez sur **Oui** pour confirmer.

![Confirmer][api-management-reset-template-confirm]

Pour restaurer tous les modèles de leurs versions par défaut, cliquez sur **restaurer les modèles par défaut** dans la liste modèle.

![Restaurer les modèles de][api-management-restore-templates]

Les modèles restaurés doivent alors être publiés individuellement ou tous à la fois en suivant la procédure [pour publier un modèle de](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Référence du développeur de modèles de portail

Informations de référence pour les modèles de portail développeur, des ressources de type chaîne, des icônes et des contrôles de la page, consultez [référence de modèles portail développeur API de gestion](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Regarder une vidéo de présentation

Regardez la vidéo suivante pour savoir comment ajouter des notes et un forum de discussion aux pages API et le fonctionnement du portail de développement à l’aide de modèles.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







