<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les exigences en matière de gestion de contenu | Microsoft Azure"
    description="Fournit un aperçu de la façon de déterminer les besoins de gestion de contenu de votre entreprise. Généralement lorsqu’un utilisateur a son propre appareil il peut-être également plusieurs informations d’identification qui va être en alternance en fonction de l’application qu’il utilise. Il est important de différencier le contenu qui a été créé à l’aide des informations d’identification personnelles par rapport à ceux qui sont créés à l’aide des informations d’identification d’entreprise. Votre solution d’identité doit être en mesure d’interagir avec le cloud services pour fournir une expérience transparente à l’utilisateur final lors de garantir sa confidentialité et renforcer la protection contre les fuites de données."
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

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins de gestion de contenu pour votre solution d’identité hybride

Comprendre les besoins de gestion de contenu pour votre entreprise peut affecter directement votre décision sur quelle solution d’identité hybride à utiliser. Avec la prolifération des dispositifs multiples et la capacité des utilisateurs à afficher leurs propres périphériques ([BYOD](http://aka.ms/byodcg)), la société doit protéger ses propres données, mais elle aussi doit conserver confidentialité de l’utilisateur. Généralement lorsqu’un utilisateur a son propre appareil il peut-être également plusieurs informations d’identification qui va être en alternance en fonction de l’application qu’il utilise. Il est important de différencier le contenu qui a été créé à l’aide des informations d’identification personnelles par rapport à ceux qui sont créés à l’aide des informations d’identification d’entreprise. Votre solution d’identité doit être en mesure d’interagir avec le cloud services pour fournir une expérience transparente à l’utilisateur final lors de garantir sa confidentialité et renforcer la protection contre les fuites de données. 

Votre solution d’identité est exploitée par les différents contrôles techniques pour assurer la gestion de contenu comme illustré dans la figure ci-dessous :
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Contrôles de sécurité qui va exploiter votre système de gestion d’identité**

En général, les exigences en matière de gestion de contenu exploiteront votre système de gestion d’identité dans les domaines suivants :

- Confidentialité : identification de l’utilisateur qui possède une ressource et en appliquant les contrôles appropriés pour maintenir l’intégrité.
- La Classification des données : identifier l’utilisateur ou le groupe et le niveau d’accès à un objet en fonction de sa classification. 
- Protection de fuites de données : des contrôles de sécurité responsables de la protection des données pour éviter des fuites devra interagir avec le système d’identité pour valider l’identité de l’utilisateur. Il est également important pour la fin de la piste d’audit.

>[AZURE.NOTE]
Lire [la classification des données pour la mise en nuage](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) pour plus d’informations sur les méthodes conseillées et des lignes directrices pour la classification des données.

Lorsque la planification de votre solution d’identité hybride assurent que les questions suivantes sont traitées en fonction des besoins de votre organisation :

- Votre société possède-t-elle des contrôles de sécurité mis en place pour appliquer la confidentialité des données ?
 - Si Oui, ces contrôles de sécurité sera en mesure d’intégrer la solution hybride identité que vous allez adopter ?
- Votre société utilise-t-elle la classification des données ?
 - Si Oui, est en mesure d’intégrer la solution hybride identité que vous allez adopter la solution en cours ?
- Votre entreprise dispose actuellement une solution pour la fuite de données ? 
 - Si Oui, est en mesure d’intégrer la solution hybride identité que vous allez adopter la solution en cours ?
- Votre entreprise a-t-elle besoin d’auditer l’accès aux ressources ?
 - Si Oui, de quel type de ressources ?
 - Si Oui, quel est le niveau d’informations est-elle nécessaire ?
 - Si Oui, où le journal d’audit doit se trouver ? Sur site ou dans le nuage ?
- Votre société a besoin de crypter les e-mails qui contiennent des données confidentielles (numéros de sécurité sociale, numéros de carte de crédit, etc.) ?
- Votre société a besoin pour crypter tous les documents/contenu partagé avec des partenaires commerciaux externes ?
- Votre société a besoin appliquer des stratégies d’entreprise sur certains types d’e-mails (ne pas répondre à tous, ne pas transférer) ?
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et de comprendre le raisonnement derrière la réponse. [Définir une stratégie de Protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) risque de dépasser les options disponibles et les avantages et les inconvénients de chaque option.  En ayant répondu à ces questions que vous sélectionnez option les adaptée à votre entreprise a besoin.


## <a name="next-steps"></a>Étapes suivantes
[Déterminer les exigences de contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
