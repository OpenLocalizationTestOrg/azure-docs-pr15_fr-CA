<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les exigences de protection de données | Microsoft Azure"
    description="Lors de la planification de votre solution d’identité hybride, identifier les exigences de protection de données pour votre entreprise et quelles options sont disponibles pour mieux respecter ces exigences."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Plan d’amélioration de la sécurité des données grâce à la solution de forte identité

La première étape pour protéger les données est d’identifier qui peut accéder à ces données et dans le cadre de ce processus, que vous devez disposer d’une identité, solution qui peut s’intègre avec votre système pour fournir des fonctionnalités d’authentification et d’autorisation. Authentification et autorisation sont confondent souvent avec eux et leurs rôles mal comprise. Dans la réalité ils sont très différents, comme illustré dans la figure ci-dessous :

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Étapes de périphérique mobile gestion du cycle de vie**

Lors de la planification de votre solution d’identité hybride, vous devez comprendre les exigences de protection de données pour votre entreprise et quelles options sont disponibles pour mieux respecter ces exigences.
 
>[AZURE.NOTE]
Une fois que vous avez terminé la planification de la sécurité des données, passez en revue de [déterminer les besoins d’authentification à plusieurs facteurs](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) pour vous assurer que vos sélections en ce qui concerne les exigences d’authentification à plusieurs facteurs n’ont pas été affectées par les décisions que vous prenez dans cette section.

## <a name="determine-data-protection-requirements"></a>Déterminer les exigences de protection de données
À l’ère de la mobilité, la plupart des entreprises ont un objectif commun : activer leurs utilisateurs à être productifs sur leurs appareils mobiles tout en local ou à distance depuis n’importe où pour accroître la productivité. Alors que ce serait un objectif commun, les entreprises ayant cette exigence sera également problème concernant le montant de menaces qui doivent être réduits afin de sécuriser les données de la société et de maintenir la confidentialité de l’utilisateur. Chaque société peut avoir des besoins différents à cet égard ; différentes des règles de conformité qui varient selon le secteur d’activité, la société est agissant entraîne des décisions de conception différente. 

Toutefois, il y a certains aspects de sécurité qui doivent être examinées et validés, quel que soit le secteur, qui sont décrites dans la section suivante.

## <a name="data-protection-paths"></a>Chemins d’accès de la protection des données

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Chemins d’accès de la protection des données**

Dans le diagramme ci-dessus, le composant d’identité sera le premier à vérifier avant d’accéder aux données. Toutefois, ces données peuvent être dans différents états pendant le temps qu’il a été accédé. Chaque nombre sur ce diagramme représente un chemin d’accès dans lequel les données peuvent être situées à un moment donné dans le temps. Ces numéros sont décrites ci-dessous :

1. Protection des données au niveau du périphérique.
2. Protection des données lors de leur transit.
3. Protection des données tout en reste sur site.
4. Protection des données tout en reste dans le nuage.

Bien que seront les contrôles techniques permettent au personnel informatique afin de protéger les données elles-mêmes sur chacun de ces phases ne sont pas directement proposé par la solution d’identité hybride, il est nécessaire que la solution d’identité hybride est capable d’exploiter à la fois locaux et cloud ressources de gestion des identités pour identifier l’utilisateur avant d’accordent l’accès aux données. Lorsque la planification de votre solution d’identité hybride assurent que les questions suivantes sont traitées en fonction des besoins de votre organisation :

## <a name="data-protection-at-rest"></a>Protection des données au repos
Quel que soit l’où les données au repos (périphérique, nuage ou sur site), il est important d’effectuer une évaluation pour comprendre les besoins de l’organisation à cet égard. Pour cette zone, s’assurer que les questions suivantes sont posées :

- Votre société a besoin pour protéger les données au repos ?
 - Si Oui, est la solution d’identité hybride capables de s’intégrer à votre infrastructure sur site actuel ?
 - Si Oui, est la solution d’identité hybride capables de s’intégrer avec vos charges de travail que qui se trouve dans le nuage ?
- La gestion des identités nuage n’est en mesure de protéger les informations d’identification de l’utilisateur et d’autres données stockées dans le nuage ?

## <a name="data-protection-in-transit"></a>Protection des données en transit
Données en transit entre l’appareil et le centre de données ou entre le périphérique et le nuage doivent être protégées. Toutefois, en cours de transit ne signifie pas nécessairement un processus de communication avec un composant en dehors de votre service cloud ; Il déplace également, en interne, par exemple, entre deux réseaux virtuels. Pour cette zone, s’assurer que les questions suivantes sont posées :

- Votre société a besoin pour protéger les données en transit ?
 - Si Oui, est-elle la solution d’identité hybride capables de s’intégrer avec les contrôles sécurisés, tels que SSL/TLS
- La gestion des identités nuage évite que le trafic d’et dans le magasin d’annuaires (dans et entre les centres de données) signé ?


## <a name="compliance"></a>Respect de la réglementation
L’industrie appartenant à votre société dépend de réglementations, lois et réglementations en vigueur. Sociétés dans les secteurs réglementés élevés doivent résoudre les problèmes de gestion des identités liés à des problèmes de conformité. Les réglementations telles que Sarbanes-Oxley (SOX), Health Insurance Portability Accountability Act (HIPAA), la loi Gramm-Leach-Bliley Act (GLBA) et le paiement carte norme Industry Data Security Standard (PCI DSS) sont très strictes en ce qui concerne des identités et des accès. La solution d’identité hybride qui adopte votre société doit avoir les fonctionnalités de base qui répond à vos exigences d’une ou plusieurs de ces règlements. Pour cette zone, s’assurer que les questions suivantes sont posées :

- La solution hybride d’identité n’est compatible avec les exigences réglementaires de votre entreprise ?
- La solution hybride d’identité est intégrée dans les fonctionnalités qui permettront à votre entreprise être conformes exigences réglementaires ? 
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et de comprendre le raisonnement derrière la réponse. [Définir une stratégie de Protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) risque de dépasser les options disponibles et les avantages et les inconvénients de chaque option.  En ayant répondu à ces questions que vous sélectionnez option les adaptée à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
 [Déterminer les exigences en matière de gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
