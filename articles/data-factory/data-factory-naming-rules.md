<properties 
    pageTitle="Factory de données - règles d’affectation de noms | Microsoft Azure" 
    description="Décrit les règles d’affectation de noms pour les entités de données usine." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Factory de données Azure - règles d’affectation de noms 
Le tableau suivant fournit les règles d’affectation de noms pour les artefacts de données usine.



Nom | Unicité des noms | Contrôles de validation
:--- | :-------------- | :----------------
Fabrique de données | Le point d’entrée unique dans Microsoft Azure. Noms respectent la casse, c'est-à-dire, MyDF et mydf font référence à la même usine de données. |<ul><li>Chaque usine de données est lié à un abonnement Azure.</li><li>Les noms d’objet doivent commencer par une lettre ou un chiffre et peuvent contenir uniquement des lettres, des chiffres et le caractère de tiret (-).</li><li>Chaque caractère de tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Tirets consécutifs ne sont pas autorisés dans les noms de conteneur.</li><li>Nom peut être de 3 à 63 caractères.</li></ul>
Lié Tables/Services/Pipelines | Unique à l’aide d’une fabrique de données. Noms respectent la casse. | <ul><li>Nombre maximal de caractères dans un nom de table : 260.</li><li>Les noms d’objet doivent commencer par un numéro de la lettre ou un trait de soulignement (_).</li><li>Caractères suivants ne sont pas autorisés : ». », « + », « ? », « / », « < », « > «, » * », « % », « & », » : «, »\\"</li></ul>
Groupe de ressources | Le point d’entrée unique dans Microsoft Azure. Noms respectent la casse. | <ul><li>Nombre maximal de caractères : 1000.</li><li>Nom peut contenir des lettres, des chiffres et les caractères suivants : «- », « _ «, », « et ». ».</li></ul>