<properties
    pageTitle="Activité de connexion irrégulière"
    description="Un rapport comprenant signer les modules qui ont été identifiés comme anormale par notre apprentissage des algorithmes de l’ordinateur."
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

# <a name="irregular-sign-in-activity"></a>Activité de connexion irrégulière

Connexions irréguliers sont ceux qui ont été identifiés par notre apprentissage algorithmes, sur la base d’une condition de « voyage impossible » combiné avec un signe anormal dans l’emplacement et le périphérique de l’ordinateur. Cela peut indiquer qu’un pirate a signé en utilisant ce compte.
Nous enverrons une notification par e-mail pour les administrateurs globaux si nous rencontrer de 10 ou plus anormaux connexion événements dans une période de 30 jours ou moins. Veillez à inclure les aad-alerts-noreply@mail.windowsazure.com dans votre liste d’expéditeurs fiables.
