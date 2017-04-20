<properties
   pageTitle="Sécurité de Suite de gestion des opérations et de la base de Solution d’Audit | Microsoft Azure"
   description="Ce document explique comment utiliser la sécurité de l’OMS et d’Audit de la solution pour effectuer une évaluation initiale de tous les ordinateurs contrôlés à des fins de conformité et sécurité."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Évaluation planifiée dans opérations Management Suite Solution de sécurité et d’Audit

Ce document vous aide à utiliser les fonctionnalités d’évaluation de base [sécurité de Suite (OMS) de gestion des opérations et de la Solution d’Audit](operations-management-suite-overview.md) pour accéder à l’état de sécurité de vos ressources analysées.

## <a name="what-is-baseline-assessment"></a>Nouveautés d’évaluation de base ?

Microsoft, ainsi que de l’industrie et les administrations publiques aux entreprises dans le monde entier, définit une configuration de fenêtres représentant les déploiements de serveur hautement sécurisé. Cette configuration est un ensemble de clés de Registre, les paramètres de stratégie d’audit et des paramètres de stratégie de sécurité ainsi que les valeurs recommandées de Microsoft pour ces paramètres. Cet ensemble de règles de sécurité de base est appelé. Capacité d’évaluation initiale d’Audit et de sécurité de l’OMS peut analyser en toute transparence de tous vos ordinateurs pour la conformité. 

Il existe trois types de règles :

- **Règles de Registre**: Vérifiez que les clés de Registre sont correctement définies.
- **Les règles de stratégie d’audit**: règles concernant votre stratégie d’audit.
- **Les règles de stratégie de sécurité**: les règles concernant les autorisations de l’utilisateur sur l’ordinateur.

> [AZURE.NOTE] [Utiliser la sécurité OMS pour évaluer la Configuration de sécurité de base](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) pour une vue d’ensemble de cette fonctionnalité.

## <a name="security-baseline-assessment"></a>Évaluation de base de sécurité

Vous pouvez consulter votre évaluation de base de sécurité en cours pour tous les ordinateurs qui sont surveillés par la sécurité de l’OMS et l’Audit en utilisant le tableau de bord.  Exécutez les étapes suivantes pour accéder au tableau d’évaluation de base de sécurité :

1. Dans le **Microsoft Operations Management Suite** principale du tableau de bord, cliquez sur mosaïque **d’Audit et de sécurité** .
2. Dans le tableau de bord **d’Audit et de sécurité** , cliquez sur **Évaluation de base** dans **Les domaines de sécurité**. Le tableau de bord **d’Évaluation de base de sécurité** s’affiche comme illustré dans l’image suivante :
    
    ![Sécurité de l’OMS et l’évaluation de planification d’Audit](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Ce tableau de bord est divisé en trois zones principales :

- **Ordinateurs par rapport à la ligne de base**: cette section propose un récapitulatif du nombre d’ordinateurs accessibles et le pourcentage d’ordinateurs qui a réussi l’évaluation. Il fournit également au top 10 ordinateurs et le résultat de pourcentage pour l’évaluation.
- **État de règles requises**: cette section a l’intention de mettre la sensibilisation aux règles ayant échouées par degré de gravité et l’échec des règles par type. En consultant le premier graphique, vous pouvez rapidement identifier si la plupart des règles ayant échouées sont critiques, ou non. Il donne également une liste des 10 règles ayant échouées et de leur gravité. Le deuxième graphique affiche le type de règle ayant échoué au cours de l’évaluation. 
- **Ordinateurs n’apparaissant pas d’évaluation de la ligne de base**: cette section répertorient les ordinateurs qui ont été inaccessible en raison d’une incompatibilité de système d’exploitation ou des échecs. 

### <a name="accessing-computers-compared-to-baseline"></a>L’accès à des ordinateurs par rapport à la ligne de base

Dans l’idéal, tous vos ordinateurs sont soit conforme à l’évaluation de base de sécurité. Toutefois il est prévu que dans certains cas cela ne se produit pas. Dans le cadre du processus de gestion de sécurité, il est important d’inclure l’examen des ordinateurs qui n’a pas pu passer tous les tests d’évaluation de sécurité. Un moyen rapide de visualiser qui est en sélectionnant l’option d' **accès des ordinateurs** situés dans la section **ordinateurs par rapport à la ligne de base** . Vous devriez voir le résultat de la recherche journal affichant la liste des ordinateurs, comme le montre dans l’écran suivante :

![Résultats de l’ordinateur](./media/oms-security-baseline/oms-security-baseline-fig2.png)

Le résultat de la recherche s’affiche dans un tableau, où la première colonne porte le nom de l’ordinateur et la deuxième couleur a le nombre de règles ayant échoué. Pour récupérer les informations concernant le type de règle en échec, cliquez sur le nombre de règles ayant échoué, outre le nom de l’ordinateur. Vous devriez obtenir un résultat similaire à celui illustré dans l’image suivante :

![Détails des résultats des ordinateur accédé](./media/oms-security-baseline/oms-security-baseline-fig3.png)

Dans le résultat de cette recherche, vous avez le total des règles d’accès, le nombre de règles critiques qui ont échoué, les règles d’avertissement et les règles d’informations a échoué.

### <a name="accessing-required-rules-status"></a>L’accès au statut de règles requises

Après avoir obtenu les informations concernant le pourcentage du nombre d’ordinateurs qui a réussi l’évaluation, vous souhaiterez obtenir plus d’informations sur les règles échouent en fonction de leur degré de gravité. Cette visualisation vous aide à classer les ordinateurs qui doivent être traités en premier pour vous assurer qu’ils seront conformes dans la prochaine évaluation. Placez le curseur sur la partie critique du graphique qui se trouve dans la mosaïque de **l’Échec des règles par gravité** , sous **état de règles requise** et cliquez dessus. Vous devriez obtenir un résultat semblable à l’écran suivante :

![Échec des règles en détails de gravité](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

Dans ce résultat de journal du type de règle de ligne de base qui a échoué, la description de cette règle et que l’ID de l’énumération de Configuration commun (CCE) de cette règle de sécurité s’affiche. Ces attributs doivent être suffisante pour effectuer une action corrective pour corriger ce problème dans l’ordinateur cible.

> [AZURE.NOTE] Pour plus d’informations sur la CCE, accéder à la [Base de données nationale de vulnérabilité](https://nvd.nist.gov/cce/index.cfm).

### <a name="accessing-computers-missing-baseline-assessment"></a>Accès aux ordinateurs n’apparaissant pas d’évaluation de la ligne de base

OMS prend en charge le profil de base de membre de domaine sur Windows Server 2008 R2 à Windows Server 2012 R2. Ligne de base Windows Server 2016 n’est pas encore final et est ajoutée dès qu’elle est publiée. Tous les autres systèmes d’exploitation analysés par évaluation de planification d’Audit et de sécurité de l’OMS s’affiche sous la section **ordinateurs manquants d’évaluation de la ligne de base** .

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris sur l’évaluation de planification d’Audit et de sécurité de l’OMS. Pour en savoir plus sur la sécurité de l’OMS, consultez les articles suivants :

- [Vue d’ensemble de la Suite de gestion (OMS) opérations](operations-management-suite-overview.md)
- [Surveiller et répondre aux alertes de sécurité dans les opérations Management Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)
- [Surveillance des ressources dans des opérations de sécurité Suite de gestion et de la Solution d’Audit](oms-security-monitoring-resources.md)

