<properties
 pageTitle="Quel est le planificateur Azure ? | Microsoft Azure"
 description="Azure planificateur vous permet décrire de façon déclarative des actions à exécuter dans le nuage. Il planifie et exécute les actions automatiquement."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>Quel est le planificateur Azure ?

Azure planificateur vous permet décrire de façon déclarative des actions à exécuter dans le nuage. Il planifie et exécute les actions automatiquement.  Planificateur effectue cette opération en utilisant [le portail Azure](scheduler-get-started-portal.md), code, [API REST](https://msdn.microsoft.com/library/mt629143.aspx)ou Azure PowerShell.

Le planificateur crée, gère et appelle le travail prévu.  Planificateur de ne pas héberger les charges de travail ou exécuter le code. Informatique, seul le code _appelle_ hébergé ailleurs — dans Azure, sur site, ou avec un autre fournisseur. Il appelle via HTTP, HTTPS, une file d’attente de stockage, une file d’attente de bus de service ou une rubrique de bus de service.

Planificateur planifie des [tâches](scheduler-concepts-terms.md), conserve l’historique des résultats de l’exécution des travaux qu’une peut passer en revue et planifie les charges de travail à exécuter de façon déterministe et de manière fiable. Azure WebJobs (partie de la fonctionnalité d’applications Web dans le Service d’application Azure) et autre fonctionnalités de planification de Azure utilisent le planificateur en arrière-plan. L' [API REST de planificateur](https://msdn.microsoft.com/library/mt629143.aspx) vous permet de gérer la communication de ces actions. En tant que tel, le planificateur prend en charge [des planifications complexes et périodicité avancée](scheduler-advanced-complexity.md) facilement.

Il existe plusieurs scénarios qui se prêtent à l’utilisation du planificateur. Par exemple :

+ _Périodique des actions :_ Périodiquement collecte des données à partir de Twitter dans un flux.
+ _Maintenance quotidienne :_ Nettoyage quotidien des journaux, exécution de sauvegardes et autres tâches de maintenance. Par exemple, un administrateur peut choisir de sauvegarder la base de données à 1:00. tous les jours pour les neuf mois suivant.

Planificateur vous permet de créer, de mettre à jour, de supprimer, d’afficher et de gérer par programme, les travaux et les [collections de projet](scheduler-concepts-terms.md) à l’aide de scripts et dans le portail.

## <a name="see-also"></a>Voir aussi

 [Hiérarchie d’entités, terminologie et des concepts du planificateur Azure](scheduler-concepts-terms.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence d’applets de commande PowerShell de planificateur Azure](scheduler-powershell-reference.md)

 [Azure planificateur haute disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)

 [Azure d’authentification sortante du planificateur](scheduler-outbound-authentication.md)
