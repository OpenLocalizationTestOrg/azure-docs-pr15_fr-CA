<properties
    pageTitle="Glossaire de Protection d’identité Azure Active Directory | Microsoft Azure"
    description="Glossaire de Protection d’identité Azure Active Directory"
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, découverte d’application cloud, gestion des applications, sécurité, risques, le niveau de risque, vulnérabilité, stratégie de sécurité, le glossaire"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Glossaire de Protection d’identité Azure Active Directory 


### <a name="at-risk-user"></a>À risque (utilisateur)  
Un utilisateur avec un ou plusieurs événements risque actif. 

### <a name="atypical-sign-in-location"></a>Emplacement de connexion atypique   
Une connexion à partir d’un emplacement géographique qui n’est pas généralement utilisé pour l’utilisateur spécifique, les utilisateurs similaires ou le locataire.

### <a name="azure-ad-identity-protection"></a>Protection d’identité AD Azure    
Un module de sécurité d’Azure Active Directory qui offre une vue consolidée des événements de risques et des vulnérabilités potentielles affectant les identités d’une organisation.

### <a name="conditional-access"></a>Accès conditionnel  
Une stratégie de sécurisation de l’accès aux ressources. Les règles d’accès conditionnel sont stockés dans Active Directory Azure et sont évaluées par AD Azure avant d’accorder l’accès à la ressource.  Exemples de règles incluent la restriction de l’accès en fonction de l’emplacement de l’utilisateur, procédé de dispositif santé ou de l’utilisateur de l’authentification.

### <a name="credentials"></a>Informations d’identification     
Informations comprenant l’identification et la preuve d’identification est utilisée pour accéder aux locaux et des ressources réseau. Exemples d’informations d’identification sont des noms d’utilisateur et les mots de passe, les cartes à puce et les certificats.

### <a name="event"></a>Événement   
Un enregistrement d’une activité dans Azure Active Directory.

### <a name="false-positive-risk-event"></a>Faux positifs (événement à risque) 
Statut de l’événement risque défini manuellement par un utilisateur de Protection d’identité, qui indique que l’événement de risque a été soumis à l’enquête et qu’il a tort, comme un événement à risque.

### <a name="identity"></a>Identité    
Une personne ou une entité qui doit être vérifiée au moyen de l’authentification basée sur des critères de mot de passe ou un certificat.

### <a name="identity-risk-event"></a>Événement de risque d’identité     
Événement DAS a été marqué comme anormale par la Protection de l’identité et peut indiquer qu’une identité a été compromise.

### <a name="ignored-risk-event"></a>Ignoré (événement à risque)    
Statut de l’événement risque défini manuellement par un utilisateur de Protection d’identité, indiquant que l’événement risque est fermé sans entreprendre une action de conversion.

### <a name="impossible-travel-from-atypical-locations"></a>Déplacement Impossible à partir d’emplacements atypiques   
Un événement à risque déclenché lorsque deux connexions pour le même utilisateur sont détectées, où au moins un d'entre eux est à partir d’un emplacement de connexion atypique et le délai entre les modules de connexion est inférieur à la durée minimale qu’il faudrait physiquement voyager entre ces emplacements.  

###<a name="investigation"></a>Enquête    
Le processus de révision des activités, les journaux et les autres informations pertinentes relatives à un événement à risque pour décider si les étapes de conversion ou d’atténuation sont nécessaires, comprendre si et comment l’identité a été compromis et comprendre l’utilisation de l’identité de compromis.

### <a name="leaked-credentials"></a>Informations d’identification de fuite  

Déclenché lorsque les informations d’identification utilisateur actuel (nom d’utilisateur et mot de passe) sont trouvées validée publiquement sur le site web foncé par nos chercheurs un événement à risque.

### <a name="mitigation"></a>Réduction des risques  
Une action de limiter ou d’éliminer la possibilité d’un pirate à exploiter un compromis d’identité ou d’un périphérique sans restaurer l’identité ou le périphérique à un état stable. Une atténuation ne résout pas les événements précédents du risque associés à l’identité ou le périphérique.

### <a name="multi-factor-authentication"></a>Plusieurs facteurs d’authentification 
Une méthode d’authentification qui requiert deux ou plusieurs méthodes d’authentification, qui peuvent inclure quelque chose que l’utilisateur a, un certificat de ce type ; quelque chose l’utilisateur connaît, comme les noms d’utilisateur, mots de passe ou des phrases de passe ; attributs physiques, comme une empreinte numérique ; et les attributs personnels, par exemple une signature personnelle.

### <a name="offline-detection"></a>Détection en mode hors connexion   
La détection d’anomalies et d’évaluation du risque d’un événement, tels que la tentative de connexion après le fait, pour un événement qui s’est déjà produit.

### <a name="policy-condition"></a>Condition de stratégie    
Une partie d’une stratégie de sécurité qui définit les entités (groupes, utilisateurs, applications, plates-formes de périphérique, États de périphérique, les plages IP, types de client) inclus dans la stratégie, ou exclus de ce dernier.

### <a name="policy-rule"></a>Règle de stratégie     
Partie d’une stratégie de sécurité qui décrit les circonstances seraient de déclencher la stratégie et les actions exécutées lorsque la stratégie est déclenchée.

### <a name="prevention"></a>Prévention  
Une action afin d’éviter des dommages à l’organisation par abus d’une identité ou un périphérique suspects ou savoir pour être compromis. Une action de prévention ne sécurise pas le périphérique ou l’identité et les événements risque précédent n’est pas résolu.

### <a name="privileged-user"></a>Privilégié (utilisateur)
Un utilisateur ayant au moment d’un événement à risque, des autorisations d’administration permanente ou temporaire pour un ou plusieurs des ressources dans Azure d’Active Directory, tel qu’un administrateur Global, administrateur de facturation, administrateur de Service, administrateur d’utilisateur et mot de passe administrateur. 

###<a name="real-time"></a>En temps réel    
Reportez-vous à la section détection en temps réel.

###<a name="real-time-detection"></a>Détection en temps réel  
La détection d’anomalies et d’évaluation du risque d’un événement, tels que la tentative de connexion avant que l’événement est autorisée à continuer.

### <a name="remediated-risk-event"></a>Résolues (événement à risque)     
Statut de l’événement risque défini automatiquement par la Protection d’identité, qui indique que l’événement de risque a été résolue à l’aide de l’action de mise à jour standard pour ce type d’événement à risque. Par exemple, lorsque le mot de passe est réinitialisé, de nombreux événements risque d’indiquer que le mot de passe précédent était compromise sont automatiquement convertis.

### <a name="remediation"></a>Mise à jour     
Action pour sécuriser une identité ou un périphérique qui ont été précédemment ou suspectées d’être compromise. Une action de conversion rétablit l’identité ou le périphérique en toute sécurité et résout les événements précédents du risque associés à l’identité ou le périphérique.

### <a name="resolved-risk-event"></a>Résolu (événement à risque)   
Statut de l’événement risque défini manuellement par un utilisateur de Protection d’identité, indiquant que l’utilisateur d’effectuer une opération de mise à jour appropriée en dehors de la Protection d’identité, et que l’événement risque doit être considérées comme fermé.

###<a name="risk-event-status"></a>Statut de l’événement risque    
Une propriété d’un événement à risque, indiquant si l’événement est actif et, si fermé, la raison de sa fermeture.

###<a name="risk-event-type"></a>Type d’événement de risque  
Catégorie de l’événement à risque, indiquant le type d’anomalie qui a provoqué l’événement considéré dangereux.

###<a name="risk-level-risk-event"></a>Niveau de risque (événement à risque)  
Une indication (haute, moyenne ou faible), de la gravité de l’événement à risque pour aider à la Protection d’identité utilisateurs hiérarchiser les actions qu’ils prennent pour réduire les risques dans leur organisation. 

###<a name="risk-level-sign-in"></a>Niveau de risque (sign-in) 
Indication (haute, moyenne ou faible) de la probabilité que pour une connexion complémentaire spécifique, quelqu'un d’autre tente d’utiliser l’identité de l’utilisateur.

###<a name="risk-level-user-compromise"></a>Niveau de risque (compromission de l’utilisateur) 
Indication (haute, moyenne ou faible) de la probabilité qu’une identité a été compromise.

### <a name="risk-level-vulnerability"></a>Niveau de risque (vulnérabilité)  
Une indication (haute, moyenne ou faible), de la gravité de la vulnérabilité pour aider à la Protection d’identité utilisateurs hiérarchiser les actions qu’ils prennent pour réduire les risques dans leur organisation.

### <a name="secure-identity"></a>Sécurisé (identité)   
Prendre des mesures correctives tel qu’un changement de mot de passe ou d’une machine de recréation de l’image pour restaurer une identité potentiellement compromise à un état sans compromis.

### <a name="security-policy"></a>Stratégie de sécurité
Une collection de règles de stratégie et de la condition. Une stratégie peut être appliquée à des entités telles que des utilisateurs, groupes, applications, périphériques, plates-formes de périphérique, États de périphérique, les plages IP et Auth2.0 les types de clients. Lorsqu’une stratégie est activée, elle est évaluée à chaque fois qu’une entité incluse dans la stratégie est délivrée un jeton pour une ressource.

### <a name="sign-in-v"></a>Ouvrez une session (v) 
Pour authentifier une identité dans Azure Active Directory.

### <a name="sign-in-n"></a>Connexion à (n) 
Le processus ou l’action d’authentification d’une identité dans Azure Active Directory et l’événement qui capture de cette opération.

###<a name="sign-in-from-anonymous-ip-address"></a>Reconnectez-vous à partir de l’adresse anonyme    
Un événement à risque se déclenche après une réussite sign-in à partir de l’adresse IP qui a été identifié en tant qu’adresse IP proxy anonymes.

###<a name="sign-in-from-infected-device"></a>Connexion à l’aide de périphérique infecté 
Un événement à risque déclenché lorsqu’une sign-in provient d’une adresse IP qui est connue pour être utilisée par un ou plusieurs périphériques de compromis, qui sont activement tentative de communiquer avec un serveur de robots.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Reconnectez-vous à partir de l’adresse IP avec une activité suspecte 
Déclenche d’un événement à risque après une réussite sign-in à partir d’une adresse IP adresse avec un grand nombre de tentatives infructueuses de connexion entre plusieurs comptes d’utilisateurs pendant une courte période de temps.

###<a name="sign-in-from-unfamiliar-location"></a>Reconnectez-vous à partir de l’emplacement inconnu 
Déclenché lorsqu’un utilisateur se connecte avec succès un nouvel emplacement (adresse IP, Latitude/Longitude et ASN) à partir d’un événement à risque.

###<a name="sign-in-risk"></a>Connexion à risque     
Risque de voir le niveau (sign-in)

###<a name="sign-in-risk-policy"></a>Stratégie d’ouverture de session risque  
Une stratégie d’accès conditionnel qui évalue le risque à une connexion spécifique sign-in et applique les atténuations basées sur les règles et conditions prédéfinies.

###<a name="user-compromise-risk"></a>Risque de compromission d’utilisateur     
Risque de voir le niveau (compromission de l’utilisateur)

###<a name="user-risk"></a>Risque de l’utilisateur    
Risque de voir le niveau (compromission de l’utilisateur).

###<a name="user-risk-policy"></a>Stratégie de risques d’utilisateur     
Une stratégie d’accès conditionnel qui considère que la connexion à et applique les atténuations basées sur les règles et conditions prédéfinies.

###<a name="users-flagged-for-risk"></a>Utilisateurs avec indicateur de risque   
Utilisateurs qui ont des événements risque qui sont actifs ou corrigée

###<a name="vulnerability"></a>Vulnérabilité    
Une configuration ou une condition dans Azure Active Directory qui en fait le répertoire vulnérable à des exploitations ou des menaces.


## <a name="see-also"></a>Voir aussi

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)
