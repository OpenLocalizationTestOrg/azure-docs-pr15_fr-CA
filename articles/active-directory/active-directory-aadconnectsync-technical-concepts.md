<properties
    pageTitle="Azure Connect de AD sync : concepts techniques | Microsoft Azure"
    description="Explique les concepts techniques de synchronisation d’Azure Connect d’AD."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure Connect de AD sync : Concepts techniques
Cet article est un résumé de la rubrique [Présentation de l’architecture](active-directory-aadconnectsync-technical-concepts.md).

Azure sync de connexion d’Active Directory s’appuie sur une plate-forme de synchronisation du méta-annuaire solide.
Les sections suivantes présentent les concepts de la synchronisation du méta-annuaire.
S’appuyant sur MIIS, ILM et FIM, les Services de synchronisation Active Directory Azure fournit la plate-forme suivante pour se connecter aux sources de données, la synchronisation des données entre des sources de données, ainsi que la mise en service et hors service des identités.

![Concepts techniques](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Les sections suivantes fournissent plus de détails sur le Service de synchronisation FIM les aspects suivants :

- Connecteur
- Flux des attributs
- Espace de connecteur
- Métaverse
- Mise en service

## <a name="connector"></a>Connecteur

Les modules de code utilisés pour communiquer avec un annuaire connecté sont appelés connecteurs (anciennement appelé (MAs) des agents de gestion).

Ceux-ci sont installés sur l’ordinateur exécutant Azure Connect de AD sync.
Les connecteurs permettent sans agent à communiquer à l’aide des protocoles de système distant plutôt que sur le déploiement d’agents spécialisés. Cela signifie une réduction des risques et le temps de déploiement, en particulier les systèmes et les applications critiques.

Dans l’illustration ci-dessus, le connecteur est synonyme de l’espace de connecteur mais englobe toutes les communications avec le système externe.

Le connecteur est responsable de l’importation de toutes les fonctionnalité d’exportation au système et libère les développeurs d’avoir à apprendre à se connecter à chaque système en mode natif lors de l’utilisation déclarative de provisionnement pour personnaliser les transformations de données.

Importations et exportations ne se produisent lors de la planification, ce qui permet une isolation supplémentaire à partir des modifications qui se produisent dans le système, dans la mesure où les modifications ne se propagent pas automatiquement à la source de données connectée. En outre, les développeurs peuvent également créer leurs propres connecteurs pour se connecter à pratiquement n’importe quelle source de données.

## <a name="attribute-flow"></a>Flux des attributs

Le méta-verse est la vue d’ensemble de toutes les identités de jointes de voisins espace de connecteur. Dans la figure ci-dessus, le flux des attributs est représentée par les lignes comportant des flèches pour les flux entrant et sortant. Flux des attributs est le processus de copie ou de transformation des données à partir d’un système à un autre, et tous les attributs de flux (entrants ou sortants).

Flux des attributs se produit entre l’espace de connecteur et méta-verse bidirectionnelle opérations (complet ou delta) de synchronisation sont planifiées pour s’exécuter.

Flux des attributs se produit uniquement lorsque les synchronisations sont exécutées. Les flux d’attributs sont définis dans les règles de synchronisation. Il peut s’agir (ISR dans l’image ci-dessus) entrant ou sortant (OSR dans l’image ci-dessus).

## <a name="connected-system"></a>Système connecté

Système connecté (également appelé un annuaire connecté) faisant référence au système distant Azure Connect AD sync a connecté à et lit et écrit les données d’identité vers et à partir de.

## <a name="connector-space"></a>Espace de connecteur

Chaque source de données connectée est représenté comme un sous-ensemble filtré des objets et des attributs dans l’espace de connecteur.
Cela permet au service de synchronisation fonctionner localement sans avoir à contacter le système distant lors de la synchronisation des objets et limite l’interaction aux importations et exporte uniquement.

Lorsque la source de données et le connecteur ont la possibilité de fournir une liste des modifications (une importation delta), puis l’efficacité opérationnelle augmente considérablement en tant que seules les modifications apportées depuis le dernier cycle d’interrogation sont échangés. L’espace de connecteur isole de changements se propageant automatiquement en exigeant que la planification du connecteur importe et exporte la source de données connectée. Cette assurance supplémentaire accorde la tranquillité d’esprit lors de tests, de l’aperçu ou de confirmation de la mise à jour suivante.

## <a name="metaverse"></a>Métaverse

Le méta-verse est la vue d’ensemble de toutes les identités de jointes de voisins espace de connecteur.

À mesure que les identités sont liées entre elles et autorité est affectée pour divers attributs par le biais de mappages de flux d’importation, l’objet du métaverse central commence pour regrouper les informations à partir de plusieurs systèmes. À partir de ce flux attribut d’objet, les mappages comportent des informations aux systèmes sortants.

Les objets sont créés lorsqu’un système faisant autorité les projets dans le métaverse. Dès que toutes les connexions sont supprimées, l’objet du métaverse est supprimé.

Impossible de modifier directement les objets du métaverse. Toutes les données de l’objet doivent être fournies par le biais de flux des attributs. Le métaverse conserve les connecteurs permanents avec chaque espace de connecteur. Ces connecteurs ne nécessitent pas de réévaluation pour chaque synchronisation. Cela signifie que Azure AD connexion synchronisation n’a pas à localiser l’objet distant correspondant à chaque fois. Cela permet d’éviter la nécessité d’agents coûteuses empêcher les modifications aux attributs qui seraient normalement responsables de la corrélation entre les objets.

Lors de la découverte de nouvelles sources de données qui peuvent avoir des objets qui doivent être gérés, Azure Connect de AD sync utilise un processus appelé règle de jointure pour évaluer les candidats potentiels avec lequel établir un lien.
Une fois le lien établi, cette évaluation ne se reproduit pas et le flux des attributs normal peut se produire entre la source de données connectées et le métaverse.

## <a name="provisioning"></a>Mise en service

Lorsque des projets d’une source faisant autorité un nouvel objet dans le métaverse, un nouvel objet espace de connecteur peut être créé dans un autre connecteur qui représente une source de données de connectés en aval.

Il établit, par nature, une liaison et flux des attributs peut se poursuivre de façon bidirectionnelle.

Chaque fois qu’une règle détermine qu’un nouvel objet espace de connecteur doit être créé, il est appelé approvisionnement. Toutefois, étant donné que cette opération est limitée à l’espace de connecteur, il ne supporte pas dans la source de données connectée jusqu'à ce que l’exportation est effectuée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Azure Sync de connexion Active Directory : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
