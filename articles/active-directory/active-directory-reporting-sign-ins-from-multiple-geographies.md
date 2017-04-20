<properties
    pageTitle="Modules de connexion à partir de plusieurs zones géographiques"
    description="Un rapport qui indique aux utilisateurs où deux signer ins semble issus de différentes régions et le temps écoulé entre le signe qu'ins ne permet pas à l’utilisateur d’ayant transité entre ces régions."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-from-multiple-geographies"></a>Connexions de plusieurs régions

Ce rapport inclut les connexions réussies à partir d’un utilisateur dans lequel deux connexions semblent issus de différentes régions et le délai entre les modules de connexion ne permet pas à l’utilisateur de passé entre ces régions. Les causes possibles sont les suivantes :

- Utilisateur partage son mot de passe avec d’autres utilisateurs

- Utilisateur utilise un ordinateur de bureau à distance pour lancer un navigateur web pour la connexion

- Un pirate s’est inscrit pour le compte d’un utilisateur à partir d’un autre pays

- Utilisateur est à l’aide d’un VPN ou un proxy

- Utilisateur n’est connecté à partir de plusieurs périphériques en même temps, par exemple un ordinateur de bureau et un téléphone mobile, et l’adresse IP du téléphone mobile est rare.

Les résultats de ce rapport affichera la connexion réussie, ainsi que le délai entre les connexions et les régions où les connexions semblent issus de la durée du trajet estimé entre ces régions. Le temps de trajet indiqué n’est qu’une estimation et peut être différent de la durée de déplacement entre les emplacements.


![Modules de connexion à partir de plusieurs zones géographiques](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)
