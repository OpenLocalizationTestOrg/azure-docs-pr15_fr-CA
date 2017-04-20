<properties
    pageTitle="Ins après plusieurs échecs de signe"
    description="Un rapport qui indique les utilisateurs qui ont signé avec succès dans après l’échec de signe tentatives consécutives de plusieurs."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Après plusieurs échecs de connexions
Ce rapport indique les utilisateurs qui ont signé avec succès dans après l’échec de signe tentatives consécutives de plusieurs. Les causes possibles sont les suivantes :

- Utilisateur a oublié son mot de passe</li><li>L’utilisateur est victime d’un mot de passe réussie deviner brute force d’attaque

Les résultats de ce rapport vous montrera le nombre de consécutives ayant échouées tentatives de connexion effectuées avant la connexion à réussie et un cachet de date associés à la première réussie signe.

**Les paramètres de rapport**: vous pouvez configurer le nombre minimal de signe d’échecs consécutifs de tentatives qui doivent se produire avant d’être affichée dans le rapport. Lorsque vous apportez des modifications à ce paramètre, il est important de noter que ces modifications ne seront pas appliquées à tout signe d’échec existant ins qui apparaissent dans votre rapport existant. Toutefois, ils seront appliqués à toutes les connexions futures. Les modifications apportées à ce rapport peuvent uniquement être effectuées par les administrateurs sous licence.


![Ins après plusieurs échecs de signe](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
