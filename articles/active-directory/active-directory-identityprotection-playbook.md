<properties
    pageTitle="Manuel de Protection de l’identité Active Directory Azure | Microsoft Azure"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Manuel de Protection de l’identité Active Directory Azure 

Ce manuel vous aidera à :

- Remplir des données dans l’environnement de Protection de l’identité en simulant les événements des risques et des vulnérabilités
- Configurez les stratégies en fonction du risque d’accès conditionnel et de tester l’impact de ces stratégies


## <a name="simulating-risk-events"></a>Simuler des événements de risque

Cette section fournit des étapes permettant de simuler les types d’événements suivants risque :

- Connexions à partir des adresses IP anonymes (faciles)
- Connexions à partir des emplacements inhabituels (modéré)
- Déplacement impossible aux emplacements atypiques (difficiles)

Autres événements risque ne peuvent être simulés de manière sécurisée.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Connexions à partir des adresses IP anonymes

Ce type d’événement des risques identifie les utilisateurs qui ont signé avec succès à partir d’une adresse IP qui a été identifiée en tant qu’adresse IP proxy anonymes. Ces serveurs proxy est utilisés par des personnes dont vous voulez masquer l’adresse IP de leur périphérique et peuvent être utilisés dans un but malveillant.

**Pour simuler une sign-in à partir d’une adresse IP anonyme, effectuez les opérations suivantes**:

1.  Téléchargez le [Navigateur de Tor](https://www.torproject.org/projects/torbrowser.html.en).
2.  Le Tor Browser, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Entrez les informations d’identification du compte que vous souhaitez voir apparaître dans le rapport de **connexions à partir des adresses IP anonymes** .

Le signe d’apparaîtra sur le tableau de bord de Protection de l’identité dans les 5 minutes. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Connexions à partir des emplacements inhabituels

Le risque de sites inconnus est un mécanisme d’évaluation de connexion en temps réel qui prend en compte au-delà de signe dans les emplacements (IP, Latitude / Longitude et ASN) pour déterminer les emplacements des nouveaux / inconnus. Le système enregistre les IPs précédente, Latitude / Longitude et les APE d’un utilisateur et considère que ces emplacements familiers. Un emplacement de connexion est considéré comme inconnu si l’emplacement de connexion ne correspond pas aux emplacements familiers existants.

Protection d’identité Azure Active Directory :  

 - a une période de formation initiale de quatorze jours au cours de laquelle il ne signale pas d’emplacements comme emplacements inhabituels.
 - ignore les connexions à partir de périphériques familiers et des emplacements qui sont géographiquement proches un familier existant.

Pour simuler des emplacements inhabituels, vous devez vous connecter à partir d’un emplacement et un périphérique qui le compte n’a pas signé dans à partir d’avant. 


**Pour simuler une connexion à partir d’un emplacement inconnu, effectuez les opérations suivantes**:

1.  Choisir un compte qui a au moins 14 jours ouverture de session. 

2.  Effectuez l’ou l’autre :
    
    une barre d’outils. Lorsque vous utilisez un réseau privé virtuel, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com) et entrez les informations d’identification du compte que vous souhaitez simuler l’événement risque de.

    b. Demandez à une entreprise associée dans un emplacement différent pour vous connecter à l’aide des informations d’identification du compte (non recommandées).

Le signe d’apparaîtra sur le tableau de bord de Protection de l’identité dans les 5 minutes.
 
### <a name="impossible-travel-to-atypical-location"></a>Déplacement Impossible à emplacement atypique
Il est difficile de simuler la condition de déplacement impossible parce que l’algorithme utilise l’apprentissage permet d’éliminer les fausses déplacement impossible à partir de périphériques familiers ou connexions de réseaux privés virtuels qui sont utilisés par d’autres utilisateurs dans le répertoire de l’ordinateur. En outre, l’algorithme requiert un signe-historique de 3 à 14 jours pour l’utilisateur avant le début de la génération d’événements de risque.

**Pour simuler un déplacement impossible à emplacement atypique, effectuez les opérations suivantes**:

1.  À l’aide de votre navigateur standard, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Entrez les informations d’identification du compte que vous voulez générer un événement à risque voyage impossible pour.

3.  Modifier votre agent utilisateur. Vous pouvez modifier un agent de l’utilisateur dans Internet Explorer à partir des outils de développement, ou modifier votre agent utilisateur Firefox ou Chrome à l’aide d’un module complémentaire de commutation de l’agent utilisateur.

4.  Modifiez votre adresse IP. Vous pouvez modifier votre adresse IP à l’aide d’un VPN, un module complémentaire Tor, ou de révolution un nouvel ordinateur dans Azure dans un autre centre de données.

5.  Connexion à [https://myapps.microsoft.com](https://myapps.microsoft.com) en utilisant les mêmes informations d’identification comme avant et après quelques minutes après la connexion précédente.

La connexion à s’afficheront dans le tableau de bord de Protection de l’identité dans les 2 à 4 heures.<br>
En raison de la machine complexe apprentissage des modèles concernés, il est possible qu’il n’est pas obtenir récupéré.<br> Vous pouvez souhaiter répliquer ces étapes pour plusieurs comptes AD Azure.


## <a name="simulating-vulnerabilities"></a>Simulation de vulnérabilités 
Les vulnérabilités sont les faiblesses dans un environnement AD Azure qui peut être exploitée par un acteur incorrect. 3 types de vulnérabilités utilisées actuellement dans la Protection d’identité de publicité Azure qui tirent parti d’autres fonctionnalités de publicité Azure. Ces vulnérabilités seront affiche automatiquement sur le tableau de bord de Protection d’identité une fois ces fonctionnalités définies.

-   Azure AD [une authentification multifactorielle ?](../multi-factor-authentication/multi-factor-authentication.md)
-   Annonce Azure [Cloud App découverte](active-directory-cloudappdiscovery-whatis.md).
-   [Gestion des identités privilégié](active-directory-privileged-identity-management-configure.md)de AD Azure. 



##<a name="user-compromise-risk"></a>Risque de compromission d’utilisateur

**Pour tester le risque de compromission d’utilisateur, effectuez les opérations suivantes**:

1.  Ouvrez une session dans [https://portal.azure.com](https://portal.azure.com) en tant qu’administrateur global pour vos clients.

2.  Accédez à la **Protection de l’identité**. 

3.  Sur la lame de **Protection Azure AD identité** principale, cliquez sur **paramètres**. 

4.  Sur la lame de **Paramètres du portail** , sous **règles de sécurité**, cliquez sur **utilisateur risque de compromettre**. 

5.  Sur la lame de **signer les risques** , désactiver le **Activer la règle** , puis cliquez sur **Enregistrer** les paramètres.

6.  Pour un compte utilisateur donné, simuler un inconnu emplacements ou événement à risque IP anonyme. Cela élève le niveau de risque utilisateur de l’utilisateur **moyen**.

7.  Attendez quelques minutes, puis vérifiez que le niveau de l’utilisateur pour l’utilisateur est **moyen**.

8.  Accédez à la lame de **Paramètres du portail** .

9.  **Sur la lame **Risque de compromission d’utilisateur** , sous **Activer la règle**, sélectionnez.** 

10. Sélectionnez une des options suivantes :

    une barre d’outils. Pour bloquer, sélectionnez **support** sous **bloc de vous connecter**.

    b. Pour appliquer la modification du mot de passe sécurisé, sélectionnez **support** sous **Exiger une authentification multifactorielle**.

13. Cliquez sur **Enregistrer**.

14. Vous pouvez maintenant tester l’accès conditionnel en fonction du risque en vous connectant à l’aide d’un utilisateur avec un niveau de risque de sécurité élevé. Si le risque est moyenne, en fonction de la configuration de votre stratégie, votre connexion est soit bloqué ou vous êtes obligé de modifier votre mot de passe. 
<br><br>
![Manuel explicatif](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Connexion à risque

 
**Pour tester un signe risque, effectuez les opérations suivantes :**

1.  Ouvrez une session dans [https://portal.azure.com](https://portal.azure.com) en tant qu’administrateur global pour vos clients.

2.  Accédez à la **Protection de l’identité**.

3.  Sur la lame de **Protection Azure AD identité** principale, cliquez sur **paramètres**. 

4.  Sur la lame de **Paramètres du portail** , sous **règles de sécurité**, cliquez sur **signer les risques**.

5.  Sur la lame **risque de vous connecter **, sélectionnez **sur** sous **Activer la règle**. 

7.  Sélectionnez une des options suivantes :

    une barre d’outils. Pour bloquer, sélectionnez **support** sous le **bloc de connexion**

    b. Pour appliquer la modification du mot de passe sécurisé, sélectionnez **support** sous **Exiger une authentification multifactorielle**.

8.  Pour bloquer, sélectionnez moyen dans le bloc de se connecter.

9.  Pour appliquer plusieurs facteurs d’authentification, sélectionnez **support** sous **Exiger une authentification multifactorielle**.

10. Cliquez sur **Enregistrer**.

11. Vous pouvez maintenant tester en fonction du risque d’accès conditionnel en simulant les emplacements inhabituels ou des événements de risque IP anonymes car ils sont les deux événements de risque **moyen** .

<br>
![Manuel explicatif](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Voir aussi

 - [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)
