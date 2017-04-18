<properties
    pageTitle="Protection d’identité Azure Active Directory | Microsoft Azure"
    description="Découvrez comment Azure AD identité Protection vous permet de limiter la capacité d’un pirate à exploiter un compromis d’identité ou d’un périphérique et pour sécuriser une identité ou un périphérique qui a été précédemment ou suspecté d’être compromise."
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, découverte d’application cloud, gestion des applications, sécurité, risques, le niveau de risque, vulnérabilité, stratégie de sécurité"
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
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Protection d’identité Azure Active Directory 

Azure Active Protection d’identité de répertoire est une fonctionnalité de l’édition Premium de publicité Azure P2 qui vous offre une vue consolidée des événements de risques et des vulnérabilités potentielles affectant les identités de votre entreprise. Microsoft a été la sécurisation des identités basée sur le cloud pour plus d’une décennie, et avec Protection d’identité de publicité Azure, Microsoft rend ces mêmes systèmes de protection disponible pour les clients d’entreprise. Protection d’identité tire parti des fonctionnalités de détection des anomalies d’annonce Azure existante (disponibles par l’intermédiaire de rapports d’activité anormale d’Azure AD) et introduit de nouveaux types d’événement de risque qui peuvent détecter les anomalies en temps réel.



##<a name="getting-started"></a>Mise en route

La grande majorité des violations de la sécurité ont lieu lorsque les pirates accéder à un environnement en vol d’identité d’un utilisateur. Les pirates sont devenus plus efficaces en exploitant les failles de tiers et en utilisant les attaques par phishing élaborées. Une fois qu’un utilisateur malveillant parvient à accéder à la même un compte utilisateur doté de privilèges faibles, il est relativement simple pour pouvoir accéder aux ressources d’une société importante par le biais de mouvement latéral. Il est donc essentiel de protéger toutes les identités et, lorsqu’une identité est compromise, l’identité compromise empêche d’être utilisé abusivement. 

Découverte des identités compromises n’est pas une tâche facile. Heureusement, peut aider à la Protection d’identité : Protection d’identité utilise des algorithmes d’apprentissage adaptatif automatique et l’analyse heuristique pour détecter les anomalies et les risques des événements susceptibles d’indiquer qu’une identité a été compromise.
 
Protection d’identité à l’aide de ces données, génère des rapports et des alertes qui vous permet d’analyser ces événements risque et de prendre des mesures correctives appropriées ou l’action d’atténuation.
 
Mais la Protection d’identité Azure Active Directory est plus qu’un outil de surveillance et de reporting. Basé sur les événements de risques, Protection d’identité calcule un niveau de risque d’utilisateur pour chaque utilisateur, ce qui vous permet de configurer les stratégies fondées sur le risque pour protéger automatiquement l’identité de votre organisation.  Ces stratégies fondées sur le risque, en plus des autres contrôles d’accès conditionnel fournies par Active Directory de Azure et EMS, peuvent automatiquement bloquer ou proposer adaptive correctives qui incluent des réinitialisations de mot de passe et la mise en œuvre de l’authentification à plusieurs facteurs.  

####<a name="explore-identity-protections-capabilities"></a>Explorez les fonctionnalités de Protection de l’identité 

**Les événements de détection des risques et des comptes risques :**  

- Les types d’événements risque 6 à l’aide d’apprentissage automatique et règles heuristiques de détection 

- Calcul des niveaux de risque d’utilisateur

- Fournit des recommandations personnalisées pour améliorer la sécurité globale en mettant en évidence les vulnérabilités



**Analyse de risque :**

- Envoi de notifications pour les événements de risque

- Analyse des événements de risques à l’aide de pertinentes et les informations contextuelles

- Fournissant des workflows de base pour effectuer le suivi des enquêtes

- Fournissant un accès facile à des actions de mise à jour telles que la réinitialisation de mot de passe



**Stratégies en fonction du risque d’accès conditionnel :**

- Stratégie pour limiter les connexions dangereuses en bloquant des connexions ou en exigeant des défis d’authentification à plusieurs facteurs.

- Stratégie de blocage ou comptes de comportements d’utilisateur sécurisé

- Stratégie pour obliger les utilisateurs à s’inscrire pour plusieurs facteurs d’authentification


## <a name="detection-and-risk"></a>Détection et les risques

### <a name="risk-events"></a>Événements de risque

Les événements de risques sont des événements qui ont été marqués comme suspects par la Protection de l’identité et indiquent qu’une identité a peut-être été compromise. Pour obtenir une liste complète des événements de risques, consultez [Types d’événements risque détectés par la Protection d’identité Azure Active Directory](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Niveau de risque

Pour un événement à risque, le niveau de risque est une indication (haute, moyenne ou faible), de la gravité de l’événement à risque. Le niveau de risque permet aux utilisateurs de Protection de l’identité hiérarchiser les actions qu’ils doivent prendre pour réduire les risques dans leur organisation. La gravité de l’événement à risque représente l’intensité du signal comme PRÉDICTEUR de compromission d’identité, combinée avec la quantité de bruit qu’il introduit en général. 

- **Élevé**: niveau de confiance élevé et événements risque de gravité élevée. Ces événements sont des indicateurs forts que l’identité de l’utilisateur ont été compromise, et tous les comptes d’utilisateur concernés doivent être convertis immédiatement.

- **Support**: niveau de gravité élevé, mais d’événement à risque faible confiance, ou vice versa. Ces événements sont potentiellement dangereux, et tous les comptes d’utilisateur concernés doivent être résolues.

- **Faible**: faible confiance et événement à risque faible gravité. Cet événement ne peut pas nécessiter une action immédiate, mais lorsqu’elle est combinée avec d’autres événements risque, peut-être fournir une indication forte que l’identité de sécurité est compromise. 


![Niveau de risque] (./media/active-directory-identityprotection/01.png "Niveau de risque")

 

Événements de risque sont soit identifiés dans **en temps réel**, ou de post-traitement après l’événement risque a déjà eu lieu (hors connexion). Actuellement, la plupart des événements risque de Protection de l’identité sont calculées en mode hors connexion et apparaissent dans la Protection de l’identité dans les 2 à 4 heures. Lors de l’évaluation dans en temps réel, les événements en temps réel risque seront afficheront dans la Console de Protection d’identité dans les 5 à 10 minutes.

Plusieurs clients hérités ne prennent pas en charge prévention et détection d’événement des risques en temps réel. En conséquence, les connexions à partir de ces clients ne peut pas être détectées ou bloquées en temps réel.


## <a name="investigation"></a>Enquête
Votre voyage par le biais de la Protection d’identité commence généralement avec le tableau de bord de Protection d’identité. 

![Mise à jour] (./media/active-directory-identityprotection/1000.png "Mise à jour")

Le tableau de bord vous permet d’accéder à :
 
- Rapports **d’utilisateurs avec indicateur de risque**, **les événements de risques** et des **vulnérabilités**
- Paramètres de la configuration de vos **Stratégies de sécurité**, de **Notifications** et de **l’enregistrement de l’authentification multicritères**
 

Il est généralement votre point de départ de l’enquête, ce qui est le processus de révision des activités, les journaux et les autres informations pertinentes relatives à un événement à risque pour décider si les étapes de conversion ou d’atténuation sont nécessaires, et comment l’identité a été compromis et comprendre l’utilisation de l’identité de compromis.

Vous pouvez lier vos activités d’enquête pour les [notifications](active-directory-identityprotection-notifications.md) Qu'azure Active Protection Directory envoie par e-mail.

Les sections suivantes vous fournissent plus de détails et les étapes associées à une enquête.  



## <a name="what-is-a-user-risk-level"></a>Ce qu’est un niveau de risque d’utilisateur ?

Un niveau de risque des utilisateurs est une indication (haute, moyenne ou faible) de la probabilité que l’identité de l’utilisateur a été compromise. Il est calculé en fonction des événements utilisateur risque associés à l’identité de l’utilisateur. 

L’état d’un événement à risque est **actif** ou **fermé**. Uniquement les événements de risques **actifs** contribuent pour le calcul du risque utilisateur. 

Niveau de risque de l’utilisateur est calculé à l’aide des entrées suivantes :

- Événements risque actif impact sur l’utilisateur
- Niveau de risque de ces événements. 
- Si les actions de conversion ont été prises 

![Risques pour l’utilisateur] (./media/active-directory-identityprotection/1001.png "Risques pour l’utilisateur")



Vous pouvez utiliser les niveaux de risque de l’utilisateur pour créer des stratégies d’accès conditionnel pour empêcher les utilisateurs de risques de la connexion, ou les forcer à changer en toute sécurité. 


## <a name="closing-risk-events-manually"></a>Fermer manuellement des événements de risque

Dans la plupart des cas, vous prendrez les actions correctives comme un mot de passe sécurisé reset pour fermer automatiquement des événements de risque. Toutefois, cela ne peut pas toujours possible.  
Il s’agit, par exemple, la casse, lorsque :

- Un utilisateur avec des événements de risque actif a été supprimé.
- Une enquête révèle qu’un événement à risque signalé a été effectuer par l’utilisateur légitime

Étant donné que les événements de risques **actifs** contribuent pour le calcul du risque utilisateur, il se peut que vous deviez manuellement réduire un niveau de risque en fermant les événements risque manuellement.  
Au cours de l’enquête, vous pouvez choisir d’effectuer l’une des actions suivantes pour modifier le statut d’un événement à risque :

![Actions] (./media/active-directory-identityprotection/34.png "Actions")

- **Résoudre** - si après avoir examiné un événement à risque, vous avez effectué une action de mise à jour appropriée en dehors de la Protection d’identité, et vous pensez que l’événement risque doit être considérées comme fermées, de marquer l’événement comme résolu. Résolution des événements définit le statut de l’événement risque fermé et l’événement risque n’est plus prises au risque de l’utilisateur.

- **Marquer comme faux positifs** - dans certains cas, vous pouvez rechercher un événement à risque et découvrir qu’il a été incorrectement marqué comme une opération risquée. Vous pouvez réduire le nombre de ces faits en marquant l’événement risque comme faux positifs. Cela permettra l’apprentissage algorithmes pour améliorer le classement des événements similaires à l’avenir de l’ordinateur. L’état des événements de faux positifs est **fermé** et qu’ils ne sont plus contribuera à risque de l’utilisateur.

- **Ignorer** - si vous n’avez pas pris les mesures correctives, mais l’événement risque d’être supprimé de la liste active, vous pouvez marquer un événement à risque ignorer et le statut de l’événement va être fermé. Les événements ignorés ne contribuent pas au risque de l’utilisateur. Cette option doit être utilisée uniquement dans des circonstances inhabituelles. 

- **Réactiver** - événements de risques qui ont été fermées manuellement (en cliquant sur **résoudre**, **faux positif**ou **Ignorer**) peuvent être réactivés, définir le statut de l’événement à **l’actif**. Événements de risque réactivé contribuent pour le calcul du niveau utilisateur risque. Événements risque fermés par le biais de correction (telles que la réinitialisation d’un mot de passe sécurisé) ne peuvent pas être réactivées. 




**Pour ouvrir la boîte de dialogue de configuration connexes**:

1. Sur la lame de **Protection de l’identité de publicité Azure** , **examiner**, cliquez sur **événements de risque**.

    ![Réinitialisation du mot de passe manuelle] (./media/active-directory-identityprotection/1002.png "Réinitialisation du mot de passe manuelle")

2. Dans la liste **des événements de risque** , cliquez sur un risque.

    ![Réinitialisation du mot de passe manuelle] (./media/active-directory-identityprotection/1003.png "Réinitialisation du mot de passe manuelle")

2. Sur la lame de risques, cliquez droit sur un utilisateur.

    ![Réinitialisation du mot de passe manuelle] (./media/active-directory-identityprotection/1004.png "Réinitialisation du mot de passe manuelle")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Fermeture de tous les événements risque pour un utilisateur manuellement

Au lieu de manuellement fermer individuellement des événements risque d’un utilisateur, Protection d’identité Azure Active Directory vous donne également une méthode pour fermer tous les événements pour un utilisateur en un seul clic.


![Actions] (./media/active-directory-identityprotection/2222.png "Actions")

Lorsque vous cliquez sur **faire disparaître tous les événements**, tous les événements sont fermés, et l’utilisateur concerné n’est plus à risque.



## <a name="remediating-user-risk-events"></a>Événements de risques utilisateur appropriées

Une conversion est une action pour sécuriser une identité ou un périphérique qui a été précédemment ou suspecté d’être compromise. Une action de conversion rétablit l’identité ou le périphérique en toute sécurité et résout les événements précédents du risque associés à l’identité ou le périphérique.

Pour résoudre les événements risque d’utilisateur, vous pouvez :

- Effectuer un mot de passe sécurisé réinitialisé pour corriger manuellement les événements risque d’utilisateur 

- Configurer une stratégie de sécurité utilisateur risque pour atténuer ou correction automatique des événements risque d’utilisateur

- Nouvelle image du périphérique infecté  


### <a name="manual-secure-password-reset"></a>Réinitialisation du mot de passe sécurisé manuelle

Une réinitialisation du mot de passe sécurisé est une conversion efficace pour de nombreux événements de risques et lors de l’exécution, automatiquement ferme ces événements risque et recalcule le niveau de risque de l’utilisateur. Vous pouvez utiliser le tableau de bord de Protection de l’identité pour lancer une réinitialisation pour un utilisateur risque de mot de passe. 

La boîte de dialogue connexe fournit deux méthodes pour réinitialiser un mot de passe :

**Réinitialiser le mot de passe** - sélectionnez **demander à l’utilisateur de réinitialiser le mot de passe** pour permettre à l’utilisateur de récupérer automatiquement le cas l’utilisateur a enregistré pour une authentification à plusieurs facteurs. Pendant l’utilisateur suivant signe, l’utilisateur est nécessaire pour résoudre un problème d’authentification à plusieurs facteurs avec succès et ensuite obligé de modifier le mot de passe. Cette option n’est pas disponible si le compte d’utilisateur n’est pas déjà inscrit une authentification multifactorielle.

**Mot de passe temporaire** - sélectionnez **Générer un mot de passe temporaire** immédiatement invalider le mot de passe existant et de créer un nouveau mot de passe temporaire pour l’utilisateur. Envoyer le nouveau mot de passe temporaire à une adresse de messagerie alternative pour l’utilisateur ou le Gestionnaire de l’utilisateur. Parce que le mot de passe est temporaire, l’utilisateur sera invité à modifier le mot de passe lors de la connexion.


![Stratégie de] (./media/active-directory-identityprotection/1005.png "Stratégie de")


**Pour ouvrir la boîte de dialogue de configuration connexes**:

1. Sur la lame de **Protection de l’identité de publicité Azure** , cliquez sur **utilisateurs avec indicateur de risque**.

    ![Réinitialisation du mot de passe manuelle] (./media/active-directory-identityprotection/1006.png "Réinitialisation du mot de passe manuelle")


2. À partir de la liste des utilisateurs, sélectionnez un utilisateur ayant au moins un risque.

    ![Réinitialisation du mot de passe manuelle] (./media/active-directory-identityprotection/1007.png "Réinitialisation du mot de passe manuelle")


2. Sur la lame de l’utilisateur, cliquez sur **Réinitialiser le mot de passe**.

    ![Réinitialisation du mot de passe manuelle] (./media/active-directory-identityprotection/1008.png "Réinitialisation du mot de passe manuelle")





## <a name="user-risk-security-policy"></a>Stratégie de sécurité utilisateur risque

Une stratégie de sécurité d’utilisateur risque est une stratégie d’accès conditionnel qui évalue le niveau de risque à un utilisateur spécifique et qui applique des mesures de correction et de réduction des actions basées sur des règles et des conditions prédéfinies.


![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1009.png "Stratégie de l’utilisateur ridk")


La Protection d’identité de publicité Azure vous permet de gérer l’atténuation et la mise à jour des utilisateurs avec indicateur de risque en vous permettant :

- Définir les utilisateurs et les groupes d’à que la stratégie s’applique : 

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1010.png "Stratégie de l’utilisateur ridk")


- Définir l’utilisateur au niveau seuil de risque (faible, moyen ou élevé) qui déclenche la stratégie : 

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1011.png "Stratégie de l’utilisateur ridk")


- Définir les contrôles à appliquer lorsque la stratégie déclenche :

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1012.png "Stratégie de l’utilisateur ridk")


- Basculer l’état de votre stratégie :

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/403.png "Inscription de l’AMF")


- Passez en revue et évaluer l’impact d’un changement avant son activation :

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1013.png "Stratégie de l’utilisateur ridk")


Choix d’un seuil **élevé** permet de réduire le nombre de fois où une stratégie est déclenchée et minimise l’impact sur les utilisateurs.
Toutefois, il exclut les utilisateurs de **basse** et **moyenne** est un indicateur de risque à partir de la stratégie, qui ne peut pas sécuriser identités ou les périphériques qui ont été précédemment ou suspectées d’être compromis.

Lors de la définition de la stratégie,

- Exclure les utilisateurs qui sont susceptibles de générer un grand nombre de faux positifs (les développeurs, les analystes de la sécurité)

- Exclure des utilisateurs dans des paramètres régionaux où l’activation de la stratégie n’est pas pratique (par exemple, aucun accès au service d’assistance)

- Utilisation d’un seuil **élevé** au cours de la première stratégie de déploiement, ou si vous devez minimiser les défis visualisés par les utilisateurs finaux.

- Utilisez un seuil **faible** si votre organisation nécessite une plus grande sécurité. Sélectionner un seuil **faible** présente des utilisateur supplémentaires connexion défis, mais une sécurité accrue.

La valeur par défaut recommandée pour la plupart des organisations est de configurer une règle pour un seuil **moyen** parvenir à un équilibre entre sécurité et facilité d’utilisation.

Pour une vue d’ensemble de l’expérience utilisateur, voir :

- [Flux de restauration de compte compromis](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Compte de compromis bloqué des flux](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Pour ouvrir la boîte de dialogue de configuration connexes**:

1. Sur la lame de **Protection d’identité Azure AD** , dans la section de **configuration** , cliquez sur **stratégie risque de l’utilisateur**.

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1009.png "Stratégie de l’utilisateur ridk")






## <a name="mitigating-user-risk-events"></a>Atténuation des événements risque d’utilisateur
Les administrateurs peuvent définir une stratégie de sécurité d’utilisateur risque de bloquer les utilisateurs lors de l’authentification en fonction du niveau de risque. 

Blocage d’une connexion à :
 
- Empêche la génération de nouveaux événements de risque d’utilisateur pour l’utilisateur concerné

- Permet aux administrateurs de mettre à jour les événements de risques affectant l’identité de l’utilisateur manuellement et de le restaurer sur un état sécurisé



## <a name="what-is-a-sign-in-risk-level"></a>Quel est un niveau de risque de connexion ?

Un niveau de risque de connexion est une indication (haute, moyenne ou basse), la probabilité que pour une spécifique sign-in, quelqu'un d’autre tente de s’authentifier avec l’identité de l’utilisateur. Le niveau de risque de connexion est évalué au moment d’un signe et estime le risque et les indicateurs détectée dans en temps réel pour cette connexion spécifique. 

## <a name="mitigating-sign-in-risk-events"></a>Événements d’ouverture de session risque de réduction 
Une réduction des risques sont une action à limiter la capacité d’un utilisateur malveillant d’exploiter un compromis d’identité ou d’un périphérique sans restaurer l’identité ou le périphérique à un état stable. Une atténuation ne résout pas les événements précédents connexion risque associés à l’identité ou le périphérique.

Vous pouvez utiliser l’accès conditionnel dans la Protection de l’identité de publicité Azure atténuer automatiquement les événements d’ouverture de session risque. À l’aide de ces stratégies, tenez compte du niveau de risque de l’utilisateur ou le signe-pour bloquer les connexions risquées ou demander à l’utilisateur pour effectuer une authentification multifactorielle. Ces actions empêchent un utilisateur malveillant d’exploiter une identité dérobée pour causer des dommages et peuvent vous donner quelques temps pour sécuriser l’identité. 


## <a name="sign-in-risk-security-policy"></a>Stratégie de sécurité d’ouverture de session risque

Une stratégie d’ouverture de session risque est une stratégie d’accès conditionnel qui évalue le risque à une connexion spécifique sign-in et applique les atténuations basées sur les règles et conditions prédéfinies.

![Ouverture de session stratégie de risque] (./media/active-directory-identityprotection/1014.png "Ouverture de session stratégie de risque")


Azure Protection d’identité d’Active Directory vous permet de gérer l’atténuation des connexions risques en vous permettant :

- Définir les utilisateurs et les groupes d’à que la stratégie s’applique : 

    ![Ouverture de session stratégie de risque] (./media/active-directory-identityprotection/1015.png "Ouverture de session stratégie de risque")


- Définissez la connexion au niveau seuil de risque (faible, moyen ou élevé) qui déclenche la stratégie : 

    ![Ouverture de session stratégie de risque] (./media/active-directory-identityprotection/1016.png "Ouverture de session stratégie de risque")


- Définir les contrôles à appliquer lorsque la stratégie déclenche :  

    ![Ouverture de session stratégie de risque] (./media/active-directory-identityprotection/1017.png "Ouverture de session stratégie de risque")
    

- Basculer l’état de votre stratégie :

    ![Inscription de l’AMF] (./media/active-directory-identityprotection/403.png "Inscription de l’AMF")

- Passez en revue et évaluer l’impact d’un changement avant son activation : 

    ![Ouverture de session stratégie de risque] (./media/active-directory-identityprotection/1018.png "Ouverture de session stratégie de risque")


### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

Vous pouvez configurer une stratégie de sécurité d’ouverture de session risque pour exiger l’authentification selon plusieurs facteur :

![Ouverture de session stratégie de risque] (./media/active-directory-identityprotection/1017.png "Ouverture de session stratégie de risque")

Toutefois, pour des raisons de sécurité, ce paramètre fonctionne uniquement pour les utilisateurs qui ont déjà été enregistrés pour une authentification multifactorielle. Si la condition pour exiger une authentification multifactorielle est satisfaite pour un utilisateur qui n’est pas encore inscrit à plusieurs facteurs d’authentification, l’utilisateur est bloqué. 

Pour obtenir les meilleurs résultats, si vous souhaitez exiger une authentification multifactorielle pour connexions risquées, vous devez :

1. Activez la [stratégie d’inscription de plusieurs facteurs d’authentification](#multi-factor-authentication-registration-policy) pour les utilisateurs.
2. Exiger les utilisateurs à se connecter dans une session non dangereux pour effectuer une inscription de l’AMF

À la fin de ces étapes permet de s’assurer que l’authentification par plusieurs facteur est requise pour une connexion à risque. 


### <a name="best-practices"></a>Meilleures pratiques

 
Choix d’un seuil **élevé** permet de réduire le nombre de fois où une stratégie est déclenchée et minimise l’impact sur les utilisateurs.  
 
Toutefois, il exclut le **faible** et **moyenne** connexions avec indicateur de risque à partir de la stratégie, qui ne peut pas bloquer un pirate d’exploiter une identité compromise. 

Lors de la définition de la stratégie, 

- Exclure des utilisateurs qui ne le font pas / ne peut pas avoir plusieurs facteurs d’authentification

- Exclure des utilisateurs dans des paramètres régionaux où l’activation de la stratégie n’est pas pratique (par exemple, aucun accès au service d’assistance)

- Exclure les utilisateurs qui sont susceptibles de générer un grand nombre de faux positifs (les développeurs, les analystes de la sécurité)

- Utilisation d’un seuil **élevé** au cours de la première stratégie de déploiement, ou si vous devez minimiser les défis visualisés par les utilisateurs finaux.

- Utilisez un seuil **faible** si votre organisation nécessite une plus grande sécurité. Sélectionner un seuil **faible** présente des utilisateur supplémentaires connexion défis, mais une sécurité accrue.

La valeur par défaut recommandée pour la plupart des organisations est de configurer une règle pour un seuil **moyen** parvenir à un équilibre entre sécurité et facilité d’utilisation.

 
La stratégie d’ouverture de session risque est :

- Appliquer à tous le trafic et les connexions à l’aide de l’authentification moderne.
- Pas appliquées aux applications à l’aide d’anciens protocoles de sécurité en désactivant le point de terminaison WS-Trust à la IDP fédéré, par exemple d’ADFS.

La page **Événements de risque** dans la console de Protection d’identité répertorie tous les événements :

- Cette stratégie a été appliquée à
- Vous pouvez valider l’activité et déterminer si l’action était appropriée ou non 

Pour une vue d’ensemble de l’expérience utilisateur, voir :

- [Récupération de connexion risquée](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Risque reconnectez-vous bloqué](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Inscription de plusieurs facteurs d’authentification pendant un signe risqué](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Pour ouvrir la boîte de dialogue de configuration connexes**:

1. Sur la lame de **Protection d’identité Azure AD** , dans la section de **configuration** , cliquez sur **ouverture de session stratégie de risques**.

    ![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1014.png "Stratégie de l’utilisateur ridk")





## <a name="multi-factor-authentication-registration-policy"></a>Politique d’enregistrement de l’authentification multicritères

Authentification à plusieurs facteurs Azure est une méthode de vérification qui vous êtes qui nécessite l’utilisation de plus qu’un nom d’utilisateur et le mot de passe. Il fournit une couche de sécurité pour les connexions utilisateur et des transactions.  
Nous vous conseillons de définir Azure plusieurs facteurs d’authentification pour les connexions utilisateur car il :

- Fournit une authentification renforcée grâce à une gamme d’options de vérification facile

- Joue un rôle clé dans la préparation de votre organisation pour protéger et récupérer à partir de compte met en danger

![Stratégie de l’utilisateur ridk] (./media/active-directory-identityprotection/1019.png "Stratégie de l’utilisateur ridk")



Pour plus d’informations, consultez [Nouveautés Azure une authentification multifactorielle ?](../multi-factor-authentication/multi-factor-authentication.md)


La Protection d’identité de publicité Azure vous permet de gérer la sortie de l’enregistrement de l’authentification multicritères en configurant une stratégie qui vous permet de : 



- Définir les utilisateurs et les groupes d’à que la stratégie s’applique : 

    ![Stratégie de l’AMF] (./media/active-directory-identityprotection/1020.png "Stratégie de l’AMF")



- Définir les contrôles à appliquer lorsque la stratégie déclenche ::  

    ![Stratégie de l’AMF] (./media/active-directory-identityprotection/1021.png "Stratégie de l’AMF")


- Basculer l’état de votre stratégie :

    ![Stratégie de l’AMF] (./media/active-directory-identityprotection/403.png "Stratégie de l’AMF")

- Afficher le statut de l’enregistrement en cours : 

    ![Stratégie de l’AMF] (./media/active-directory-identityprotection/1022.png "Stratégie de l’AMF")


Pour une vue d’ensemble de l’expérience utilisateur, voir :

- [Flux de l’enregistrement d’authentification à plusieurs facteurs](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [L’inscription de plusieurs facteurs d’authentification pendant un signe risqué](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Pour ouvrir la boîte de dialogue de configuration connexes**:

1. Sur la lame de **Protection d’identité Azure AD** , dans la section de **configuration** , cliquez sur **l’enregistrement de l’authentification multifactorielle**.

    ![Stratégie de l’AMF] (./media/active-directory-identityprotection/1019.png "Stratégie de l’AMF")





## <a name="next-steps"></a>Étapes suivantes

 - [Channel 9 : Les annonces Azure et afficher d’identité : aperçu de Protection d’identité](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Types d’événements risque détectés par la Protection d’identité Azure Active Directory](active-directory-identityprotection-risk-events-types.md)
 - [Vulnérabilités détectées par la Protection d’identité Azure Active Directory](active-directory-identityprotection-vulnerabilities.md)
 - [Notifications de Protection de l’identité Active Directory Azure](active-directory-identityprotection-notifications.md)
 - [Manuel de Protection de l’identité Active Directory Azure](active-directory-identityprotection-playbook.md)
 - [Glossaire de la Protection d’identité Active Directory Azure](active-directory-identityprotection-glossary.md)

 - [Connexion à des expériences avec Protection d’identité de publicité Azure](active-directory-identityprotection-flows.md)

 - [Activation de la Protection d’identité Azure Active Directory](active-directory-identityprotection-enable.md)
 - [Azure Active Directory identité Protection - comment débloquer les utilisateurs](active-directory-identityprotection-unblock-howto.md)

 - [Mise en route avec Protection d’identité Azure Active Directory et de Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


