<properties
    pageTitle="Restreindre l’accès à votre contenu Azure CDN par pays | Microsoft Azure"
    description="Découvrez comment limiter l’accès au contenu à l’aide de la fonctionnalité de filtrage Geo Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="akucer"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="Lichard"/>

#<a name="restrict-access-to-your-content-by-country---akamai"></a>Restreindre l’accès à votre contenu par pays - Akamai

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Norme d’Akamai](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Vue d’ensemble

Lorsqu’un utilisateur demande le contenu, par défaut, le contenu est pris en charge que lorsque l’utilisateur fait cette demande à partir de. Dans certains cas, vous souhaiterez peut-être restreindre l’accès à votre contenu par pays. Cette rubrique explique comment utiliser la fonctionnalité de **Filtrage Geo** afin de configurer le service pour autoriser ou bloquer l’accès par pays.

> [AZURE.IMPORTANT] Les produits de Verizon et Akamai fournissent les mêmes fonctionnalités de filtrage géo, mais diffère de l’interface utilisateur. Ce document décrit l’interface pour **Azure CDN Standard à partir d’Akamai**. Geo-filtrage avec **Azure CDN Standard/Premium de Verizon**, voir [restreindre l’accès à votre contenu par pays - Verizon](cdn-restrict-access-by-country.md).

Pour plus d’informations sur les considérations qui s’appliquent à la configuration de ce type de restriction, reportez-vous à la section [Considérations](cdn-restrict-access-by-country.md#considerations) à la fin de la rubrique.  

![Filtrage du pays](./media/cdn-filtering/cdn-country-filtering-akamai.png)

##<a name="step-1-define-the-directory-path"></a>Étape 1 : Définir le chemin d’accès du répertoire

Sélectionnez votre point de terminaison au sein du portail et de trouver l’onglet Filtrage Geo sur la navigation de gauche pour accéder à cette fonctionnalité.

Lorsque vous configurez un filtre de pays, vous devez spécifier le chemin d’accès relatif à l’emplacement auquel les utilisateurs seront accordés ou refusés. Vous pouvez appliquer le filtrage-geo pour tous vos fichiers avec « / » ou les dossiers sélectionnés en spécifiant des chemins d’accès de répertoire « / images / ». Vous pouvez également appliquer le filtrage de geo dans un seul fichier en spécifiant le fichier et en laissant la barre oblique « / pictures/city.png ».

Filtre de chemin d’accès du répertoire exemple :

    /                                 
    /Photos/
    /Photos/Strasbourg/
    /Photos/Strasbourg/city.png

##<a name="step-2-define-the-action-block-or-allow"></a>Étape 2 : Définition de l’action : autoriser ou bloquer

**Bloc :** Les utilisateurs des pays spécifiés seront refusés accès aux ressources demandées à partir de ce chemin d’accès récursif. Si aucune autre option de filtrage des pays n’ont été configurées pour cet emplacement, puis tous les autres utilisateurs pourront accéder.

**Autoriser :** Seuls les utilisateurs des pays spécifiés pourront l’accès aux ressources demandées à partir de ce chemin d’accès récursif.

##<a name="step-3-define-the-countries"></a>Étape 3 : Définir les pays

Sélectionnez les pays que vous souhaitez bloquer ou autoriser le chemin d’accès. Pour plus d’informations, consultez [Azure CDN à partir des Codes de pays d’Akamai](https://msdn.microsoft.com/library/mt761717.aspx).

Par exemple, la règle de blocage /Photos/Strasbourg/filtre les fichiers, y compris :

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Codes de pays

La fonctionnalité de **Filtrage Geo** utilise des codes de pays pour définir les pays à partir duquel une demande sera autorisée ou bloquée pour un répertoire sécurisé. Vous trouverez les codes de pays dans [Azure CDN à partir des Codes de pays d’Akamai](https://msdn.microsoft.com/library/mt761717.aspx). 

##<a id="considerations"></a>Considérations relatives à la

- Il peut prendre quelques minutes pour que les modifications apportées à votre configuration de filtrage de pays en vigueur.
- Cette fonctionnalité ne prend pas en charge des caractères génériques (par exemple, « * »).
- La configuration de filtrage geo associée avec le chemin d’accès relatif sera appliquée de manière récursive vers ce chemin.
- Une seule règle peut être appliquée pour le même chemin d’accès relatif (vous ne pouvez pas créer plusieurs filtres pays qui pointent vers le même chemin d’accès relatif. Toutefois, un dossier peut avoir plusieurs filtres de pays. C’est en raison de la nature récursive de filtres de pays. En d’autres termes, un sous-dossier d’un dossier déjà configuré peut être affecté à un filtre de différents pays.

