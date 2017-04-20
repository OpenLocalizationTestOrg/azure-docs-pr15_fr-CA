<properties
    pageTitle="Active Directory Federation Services gestion et personnalisation avec Azure Connect d’annonce | Microsoft Azure"
    description="Gestion des AD FS avec Azure Connect de publicité et de personnalisation d’utilisateur AD FS expérience de connexion avec Azure Connect de publicité et de PowerShell."
    keywords="AD FS, ADFS, Federation Services gestion, DAS, se connecter, connexion, reconnectez-vous, AD FS personnalisation, réparer la fédération O365, approbation, partie de confiance"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Personnalisation avec Azure Connect de publicité et la gestion de Active Directory Federation Services

Cette tâches Détails de l’article liés à Active Directory Federation Services (ADFS) qui peuvent être effectuées à l’aide de Microsoft Azure Active Directory se connecter, plus d’autres tâches courantes AD FS qui peuvent être requis pour une configuration complète d’une batterie de serveurs ADFS.

| Rubrique | Qu’il couvre. |
|:------|:-------------|
|**Gestion d’Active Directory Federation Services**|
|[Réparer l’approbation](#repairthetrust)| Réparation de l’approbation de fédération avec Office 365 |
|[Ajouter un serveur ADFS](#addadfsserver) | Extension de la batterie de serveurs ADFS en ajoutant un serveur ADFS|
|[Ajouter un serveur proxy de Federation Services web application](#addwapserver) | Extension de la batterie de serveurs ADFS en ajoutant un serveur WAP|
|[Ajouter un domaine fédéré](#addfeddomain)| Ajout d’un domaine fédéré|
| **Personnalisation de AD FS**|
|[Ajouter un logo d’entreprise personnalisés ou d’une illustration](#customlogo)| Personnalisation d’une page de connexion AD FS avec un logo de société et d’une illustration |
|[Ajoutez une description de connexion](#addsignindescription) | Ajout d’une description de la page de connexion |
|[Modifier les règles de revendications AD FS](#modclaims) | Modification des revendications AD FS pour divers scénarios de fédération |

## <a name="ad-fs-management"></a>Gestion d’Active Directory Federation Services

Azure Connect d’annonce fournit diverses tâches liées à AD FS qui peuvent être exécutées avec une intervention minime de l’utilisateur à l’aide de l’Assistant Azure Connect d’Active Directory. Lorsque vous avez terminé l’installation Azure Connect d’Active Directory en exécutant l’Assistant, vous pouvez exécuter l’Assistant à nouveau pour effectuer des tâches supplémentaires.

### Réparer l’approbation<a name=repairthetrust></a>

Azure Connect d’annonce peut vérifier l’état actuel de l’approbation AD FS et Azure Active Directory et prenez les mesures appropriées pour réparer l’approbation. Suivez ces étapes pour réparer votre annonce Azure et approbation d’ADFS.

1. Dans la liste des tâches supplémentaires, sélectionnez **réparation DAS et approbation ADFS** .
![Réparer les DAS et ADFS d’approbation](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Sur la page de **connexion à Active Directory Azure** , vos informations d’identification de l’administrateur global pour Azure annonce et cliquez sur **suivant**.
![Connexion à Active Directory Azure](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Sur la page **d’informations d’identification d’accès à distance** , entrez les informations d’identification pour l’administrateur de domaine.
![Informations d’identification d’accès distant](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Après avoir cliqué sur **suivant**, Azure Connect d’Active Directory vérifie pour la santé du certificat et afficher tous les problèmes.

    ![État des certificats](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    La page **prêt à configurer** affiche la liste des actions qui seront effectuées pour réparer l’approbation.

    ![Prêt à configurer](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Cliquez sur **installer** pour réparer l’approbation.

>[AZURE.NOTE] Azure de réparation de publicité Connect peut uniquement ou d’agir sur des certificats auto-signés. Azure Connect d’Active Directory ne peut pas réparer des certificats tiers.

### Ajouter un serveur ADFS<a name=addadfsserver></a>

> [AZURE.NOTE] Azure Connect d’Active Directory nécessite le fichier de certificat PFX pour ajouter un serveur ADFS. Par conséquent, vous pouvez effectuer cette opération uniquement si vous configuré la batterie de serveurs AD FS à l’aide d’Azure Connect d’Active Directory.

1. Sélectionnez **déployer un serveur supplémentaire de la fédération** , puis cliquez sur **suivant**.
![Serveur de fédération supplémentaires](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Sur la page de **connexion à Active Directory Azure** , entrez vos informations d’identification de l’administrateur global AD Azure et cliquez sur **suivant**.
![Connexion à Active Directory Azure](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Fournissent des informations d’identification de l’administrateur du domaine.
![Informations d’identification d’administrateur de domaine](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure Connect d’Active Directory vous demandera le mot de passe du fichier PFX que vous avez fournies lors de la configuration de votre nouvelle batterie de serveurs ADFS avec Azure Connect d’Active Directory. Cliquez sur **Mot de passe** pour fournir le mot de passe du fichier PFX.
![Mot de passe de certificat](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Spécifier le certificat SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Dans la page **Serveurs de AD FS** , entrez le nom du serveur ou l’adresse IP à ajouter à la batterie de serveurs ADFS.
![Serveurs AD FS](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Cliquez sur **suivant** et passez à la dernière page de **configuration** . Une fois, Azure Connect d’AD a fini d’ajouter des serveurs à la batterie de serveurs ADFS, vous aurez la possibilité de vérifier la connectivité.
![Prêt à configurer](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Installation terminée](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Ajouter un serveur proxy de Federation Services web application<a name=addwapserver></a>

> [AZURE.NOTE] Azure Connect d’Active Directory nécessite le fichier de certificat PFX pour ajouter un serveur de proxy d’application web. Par conséquent, vous serez en mesure d’effectuer cette opération uniquement si vous configuré la batterie de serveurs AD FS à l’aide d’Azure Connect d’Active Directory.

1. Sélectionnez **Déployer le Proxy d’Application Web** dans la liste des tâches disponibles.
![Déploiement de proxy de l’application web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Fournir les informations d’identification administrateur de global Azure.
![Connexion à Active Directory Azure](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. Dans la page **certificat SSL de spécifier** , fournir le mot de passe pour le fichier PFX que vous avez fournies lors de la configuration de la batterie de serveurs ADFS avec Azure Connect d’Active Directory.
![Mot de passe de certificat](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Spécifier le certificat SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Ajoutez le serveur à ajouter comme un proxy d’application web. Étant donné que le serveur de proxy d’application web ne peut pas être joint au domaine, l’Assistant vous demandera des informations d’identification administratives sur le serveur en cours d’ajout.
![Informations d’identification du serveur d’administration](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. Sur la page **d’informations d’identification de Proxy confiance** , fournir des informations d’identification d’administration pour configurer l’approbation de proxy et d’accéder au serveur principal de la batterie de serveurs ADFS.
![Informations d’identification de proxy confiance](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. Dans la page **prêt à configurer** , l’Assistant affiche la liste des actions qui seront effectuées.
![Prêt à configurer](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Cliquez sur **installer** pour terminer la configuration. Une fois la configuration terminée, l’Assistant vous donne la possibilité de vérifier la connectivité aux serveurs. Cliquez sur **Vérifier** pour vérifier la connectivité.
![Installation terminée](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Ajouter un domaine fédéré<a name=addfeddomain></a>

Il est facile d’ajouter un domaine à être fédéré avec Azure AD à l’aide d’Azure Connect d’Active Directory. Azure Connect d’Active Directory ajoute le domaine pour la fédération et modifie les règles de revendication pour refléter correctement l’émetteur lorsque vous avez plusieurs domaines fédérés avec AD Azure.

1. Pour ajouter un domaine fédéré, sélectionnez la tâche **Ajouter un autre domaine AD Azure**.
![Autres domaines Active Directory Azure](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. Sur la page suivante de l’Assistant, fournir les informations d’identification de l’administrateur global de publicité Azure.
![Connexion à Active Directory Azure](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. Sur la page **d’informations d’identification d’accès distant** , fournissent des informations d’identification de l’administrateur le domaine.
![Informations d’identification d’accès distant](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Sur la page suivante, l’Assistant propose une liste de domaines Active Directory Azure avec lequel vous pouvez fédérer votre répertoire local. Choisissez le domaine dans la liste.
![Domaines Active Directory Azure](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Après avoir choisi le domaine, l’Assistant vous fournira les informations nécessaires concernant les plus les actions que l’Assistant effectuera et l’impact de la configuration. Dans certains cas, si vous sélectionnez un domaine qui n’est pas encore vérifié dans Azure AD, l’Assistant vous fournira des informations pour vous aider à vérifier le domaine. Pour plus d’informations, reportez-vous à la section [Ajouter votre nom de domaine personnalisé pour Azure Active Directory](active-directory-add-domain.md) .

5. Cliquez sur **suivant**, et affiche la liste des actions qui Azure Connect d’Active Directory va effectuer la page **prêt à configurer** . Cliquez sur **installer** pour terminer la configuration.
![Prêt à configurer](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personnalisation de AD FS

Les sections suivantes fournissent plus d’informations sur certaines des tâches courantes que vous pouvez avoir à effectuer lors de la personnalisation de votre page de connexion AD FS.

### Ajouter un logo d’entreprise personnalisés ou d’une illustration<a name=customlogo></a>

Pour modifier le logo de la société qui s’affiche sur la page de **connexion** , utilisez l’applet de commande Windows PowerShell suivante et la syntaxe.

> [AZURE.NOTE] Les dimensions recommandées pour le logo sont 260 x 35 @ 96 PPP avec une taille de fichier non supérieure à 10 Ko.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Le paramètre *TargetName* est requis. Le thème par défaut qui est publié avec AD FS est nommé par défaut.


### Ajoutez une description de connexion<a name=addsignindescription></a>

Pour ajouter une description de la page de connexion à la **page de connexion**, utilisez l’applet de commande Windows PowerShell suivante et la syntaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modifier les règles de revendications AD FS<a name=modclaims></a>

ADFS prend en charge un langage enrichi demande que vous pouvez utiliser pour créer des règles de la revendication personnalisée. Pour plus d’informations, voir [Le rôle de la langue de la règle de revendication](https://technet.microsoft.com/library/dd807118.aspx).

Les sections suivantes décrivent comment vous pouvez écrire des règles personnalisées pour certains scénarios se rapportant à AD Azure et fédération AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID immuable conditionnelle sur une valeur de présence de l’attribut

Azure Connect d’Active Directory vous permet de spécifier un attribut à utiliser comme un point d’ancrage de la source lorsque les objets sont synchronisées dans AD Azure. Si la valeur de l’attribut personnalisé n’est pas vide, vous pouvez souhaiter d’émettre une demande d’indemnisation ID immuable. Par exemple, vous pourriez sélectionnez **ms-ds-consistencyguid** que l’attribut de l’ancre de source et d’émettre des **ImmutableID** en tant que **ms-ds-consistencyguid** dans le cas où l’attribut a une valeur. S’il n’existe aucune valeur à l’attribut, **objectGuid** de délivrer comme ID immuable.  Vous pouvez construire l’ensemble de règles de revendication personnalisée comme décrit dans la section suivante.

**La règle 1 : Attributs de la requête**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Dans cette règle, vous interrogez les valeurs de **ms-ds-consistencyguid** et l' **objectGuid** de l’utilisateur à partir d’Active Directory. Modifier le nom du magasin à un nom de magasin approprié dans votre déploiement AD FS. Également modifier le type de demandes au type des déclarations appropriées de votre fédération, comme défini pour les **objectGuid** et **ms-ds-consistencyguid**.

Également, à l’aide de **Ajouter** et pas le **problème**, vous évitez l’ajout d’un problème sortant pour l’entité et permet les valeurs comme des valeurs intermédiaires. Vous émettez la revendication dans une règle ultérieure après avoir établi la valeur à utiliser comme identificateur immuable.

**Règle 2 : Vérifier l’existence de ms-ds-consistencyguid pour l’utilisateur**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Cette règle définit un indicateur temporaire appelé **idflag** qui a la valeur **useguid** s’il n’existe aucun **ms-ds-concistencyguid** rempli pour l’utilisateur. La logique derrière cela est le fait que AD FS ne permet pas de déclarations vides. Ainsi, lorsque vous ajoutez des revendications http://contoso.com/ws/2016/02/identity/claims/objectguid et http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid dans la règle 1, vous retrouver avec un **msdsconsistencyguid** de revendication uniquement si la valeur est remplie pour l’utilisateur. Si elle n’est pas remplie, ADFS voit qu’il aura une valeur vide et qu’il supprime immédiatement. Tous les objets a **objectGuid**, cette demande sera toujours disponible après l’exécution de la règle 1.

**Règle 3 : Émettre ms-ds-consistencyguid en tant qu’ID immuable, si elle est présente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Il s’agit d’une vérification **d’existence** implicite. Si la valeur de la revendication existe, puis problème comme ID immuable. L’exemple précédent utilise la revendication **nameidentifier** . Vous devrez modifier pour le type de déclaration approprié pour l’ID immuable dans votre environnement.

**Règle 4 : Émettre objectGuid comme immuable ID si ms-ds-consistencyGuid n’est pas présent**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Dans cette règle, vous vérifiez simplement l' indicateur temporaire **idflag**. Vous décidez s’il faut émettre la demande en fonction de sa valeur.

> [AZURE.NOTE] La séquence de ces règles est importante.

#### <a name="sso-with-a-subdomain-upn"></a>Authentification unique avec un sous-domaine UPN

Vous pouvez ajouter plus d’un domaine à être fédéré à l’aide d’Azure Connect d’Active Directory, comme décrit dans [Ajout d’un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Vous devez modifier la revendication de nom UPN afin que l’ID d’émetteur correspond au domaine racine et pas le sous-domaine, car le domaine racine fédérés traite également de l’enfant.

Par défaut, la règle de revendication pour l’ID de l’émetteur est définie comme :

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Demande de ID émetteur par défaut](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

La règle par défaut prend le suffixe UPN simplement et l’utilise dans la demande d’ID de l’émetteur. Par exemple, Jean est un utilisateur de sub.contoso.com et contoso.com est fédéré à AD Azure. John insère john@sub.contoso.com comme nom d’utilisateur lors de l’ouverture de session AD Azure et l’ID d’émetteur par défaut réclamer la règle dans AD FS gère de la manière suivante.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valeur de revendication :** http://sub.contoso.com/adfs/services/trust/

Pour que seul le domaine racine dans la valeur de revendication de l’émetteur, modifiez la règle de revendication pour faire correspondre les éléments suivants.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les [options de connexion de l’utilisateur](active-directory-aadconnect-user-signin.md).
