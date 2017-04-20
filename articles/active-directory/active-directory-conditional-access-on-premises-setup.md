<properties
    pageTitle="Configuration d’un accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement | Microsoft Azure"
    description="Un guide pas à pas pour activer l’accès conditionnel à des applications sur site à l’aide du Service de fédération Active Directory (Active Directory Federation Services) dans Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Configuration d’un accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement

Personnellement propriétaire des périphériques de vos utilisateurs peuvent être marqués connu à votre organisation nécessitant que les utilisateurs les join de lieu de travail leurs périphériques pour le service d’enregistrement de dispositif Azure Active Directory. Voici un guide pas à pas pour activer l’accès conditionnel à des applications sur site à l’aide du Service de fédération Active Directory (Active Directory Federation Services) dans Windows Server 2012 R2.

> [AZURE.NOTE]
> Licence d’Office 365 ou Azure AD Premium est requis lors de l’utilisation de périphériques inscrits dans les stratégies d’accès conditionnel Azure Active Directory périphérique d’enregistrement service. Cela inclut les stratégies appliquées par les Services de fédération Active Directory (Active Directory Federation Services) à des ressources sur site.

Pour plus d’informations sur les scénarios d’accès conditionnel pour locaux, consultez [joindre au poste de travail à partir de n’importe quel périphérique pour l’authentification unique et transparente deuxième facteur d’authentification entre les Applications de la société](https://technet.microsoft.com/library/dn280945.aspx).

Ces fonctionnalités sont disponibles pour les clients qui achètent une licence Azure Active Directory prime.

<a name="supported-devices"></a>Périphériques pris en charge
-------------------------------------------------------------------------
* Appareils de domaine joint de Windows 7.
* Personnel de Windows 8.1 et domaine rejoint les périphériques.
* e/s 6 et version ultérieure, du navigateur Safari
* Android 4.0 ou version ultérieure, Samsung GS3 ou au-dessus de téléphones, Samsung Note2 ou au-dessus de comprimés.


<a name="scenario-prerequisites"></a>Conditions préalables du scénario
------------------------------------------------------------------------
* Abonnement à Office 365 ou Azure Active Directory prime
* Un locataire Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 ou une version ultérieure)
* Mise à jour de schéma Windows Server 2012 R2
* Licence pour Azure Active Directory prime
* Windows Server 2012 R2 Federation Services, configuré pour l’authentification unique pour Azure AD
* Windows Server 2012 R2 Web Application Proxy Microsoft Azure Active Directory se connecter (AD Azure se connecter). [Connexion de publicité Azure de télécharger ici](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Domaine vérifié.

<a name="known-issues-in-this-release"></a>Problèmes connus dans cette version
-------------------------------------------------------------------------------
* Stratégies d’accès conditionnel en fonction des périphériques nécessitent périphérique objet différée à Active Directory d’Azure Active Directory. Il peut prendre jusqu'à 3 heures pour les objets de périphérique soit précédent écrit à Active Directory
* périphériques d’e/s 7 toujours invitera l’utilisateur à sélectionner un certificat lors de l’authentification de certificat client.
* Certaines versions d’iOS8, avant iOS 8.3 ne fonctionnent pas.

## <a name="scenario-assumptions"></a>Hypothèses de scénario
Ce scénario suppose que vous disposez d’un environnement hybride comprenant un locataire Azure AD et un sur site Active Directory. Les locataires doivent être connectés à l’aide d’Azure Connect d’Active Directory et un domaine vérifié et ADFS pour l’authentification unique. L’aide-mémoire ci-dessous vous aidera à configurer votre environnement à l’étape ci-dessus.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Liste de vérification : Conditions préalables pour le scénario d’accès conditionnel
--------------------------------------------------------------
Connectez vos clients AD Azure avec Active Directory sur site.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurer le Service d’enregistrement de dispositif Azure Active Directory
Utilisez ce guide pour déployer et configurer le Service de l’inscription de périphérique Azure pour Active Directory de votre organisation.

Ce guide suppose que vous avez configuré les Windows Server Active Directory et que vous êtes abonné à Active Directory de Microsoft Azure. Reportez-vous à la section conditions requises ci-dessus.

Pour déployer le Service Azure Active Directory périphérique d’enregistrement avec vos clients Azure Active Directory, effectuez les tâches dans la liste de contrôle suivante dans l’ordre. Lorsqu’un lien de référence vous amène à une rubrique conceptuelle, retournez à cette liste de contrôle une fois que vous passez en revue la rubrique conceptuelle afin que vous pouvez poursuivre les reste des tâches de cette liste de vérification. Certaines tâches inclut une étape de validation de scénario qui peut vous aider à confirmer que l’étape s’est terminée correctement.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Partie 1 : Enregistrement de dispositif activer Azure Active Directory

Suivez la liste de contrôle ci-dessous pour activer et configurer le Service d’enregistrement du périphérique Azure Active Directory.

| Tâche                                                                                                                                                                                                                                                                                                                                                                                             | Référence                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Activer l’enregistrement du périphérique dans vos clients Azure Active Directory pour permettre les périphériques à rejoindre l’espace de travail. Par défaut, plusieurs facteurs d’authentification n’est pas activé pour le service. Toutefois, plusieurs facteurs d’authentification est recommandé lors de l’enregistrement d’un périphérique. Avant d’activer une authentification multifacteur en multidiffusion, assurez-vous que AD FS est configuré pour un fournisseur d’authentification à plusieurs facteurs. | [Activer l’enregistrement du périphérique Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)               |
| Périphériques découvre votre Azure périphérique d’enregistrement le Service Active Directory en recherchant des enregistrements DNS connus. Vous devez configurer votre société DNS afin que les périphériques puissent découvrir votre Azure périphérique d’enregistrement le Service Active Directory.                                                                                                                                                   | [Configurer la recherche de l’enregistrement de dispositif Azure Active Directory.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Partie 2 : Déployer et configurer Windows Server 2012 R2 Active Directory Federation Services et définir une relation de fédération avec Azure AD

| Tâche                                                                                                                                                                                                                                                                                                                                                                                             | Référence                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Déployez un domaine des Services de domaine Active Directory avec les extensions de schéma Windows Server 2012 R2. Vous n’avez pas besoin de mettre à niveau un de vos contrôleurs de domaine vers Windows Server 2012 R2. La mise à niveau du schéma est la seule exigence. | [Mise à niveau de votre schéma de Services de domaine Active Directory](#upgrade-your-active-directory-domain-services-schema)               |
| Périphériques découvre votre Azure périphérique d’enregistrement le Service Active Directory en recherchant des enregistrements DNS connus. Vous devez configurer votre société DNS afin que les périphériques puissent découvrir votre Azure périphérique d’enregistrement le Service Active Directory.                                                                                                                                                   | [Préparation de vos périphériques de prise en charge d’Active Directory](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Partie 3 : Activer périphérique écriture différée dans Azure AD

| Tâche                                                                                                                                                                                                                                                                                                                                                                                             | Référence                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Terminer la partie 2 de l’écriture différée de périphérique activation dans Azure Connect d’AD. Une fois terminée, retournez cette ce guide. | [L’activation de l’écriture différée de dispositif dans Azure Connect d’AD](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Facultatif] Partie 4 : Activer une authentification multifactorielle

Il est fortement recommandé de configurer une de plusieurs options d’authentification à plusieurs facteurs. Si vous souhaitez exiger AMF, reportez-vous à [Choisir la solution de sécurité à facteurs multiples pour vous](../multi-factor-authentication/multi-factor-authentication-get-started.md). Il comprend une description de chaque solution, ainsi que des liens pour vous aider à configurer la solution de votre choix.

## <a name="part-5-verification"></a>Partie 5 : vérification

Le déploiement est maintenant terminé. Vous pouvez maintenant essayer de certains scénarios. Suivez les liens ci-dessous pour faire des essais avec le service et de vous familiariser avec les fonctionnalités


| Tâche                                                                                                                                                                                                                         | Référence                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Joindre certains périphériques à votre espace de travail à l’aide d’Azure Active Directory périphérique d’enregistrement. Vous pouvez joindre des appareils Android, iOS et Windows                                                                                         | [Joindre des périphériques à votre espace de travail à l’aide d’Azure Active Directory périphérique d’enregistrement](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Vous pouvez afficher et activer ou désactiver des périphériques à l’aide de l’administrateur de portail. Dans cette tâche, vous allez afficher certains des périphériques via le portail de l’administrateur.                                                        | [Vue d’ensemble de l’enregistrement de périphérique Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)                             |
| Vérifiez que les objets du périphérique sont écrites d’Azure Active Directory pour Windows Server Active Directory.                                                                                                                  | [Vérifiez les appareils inscrits sont différée écrit dans Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Maintenant que les utilisateurs peuvent enregistrer leurs périphériques, vous pouvez créer des applications dans AD FS qui autorise uniquement les périphériques des stratégies d’accès. Dans cette tâche, que vous allez créer une règle d’accès application et personnalisé accéder à message de refus | [Créer une application stratégie et accès personnalisé message d’accès refusé](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Intégrer Azure Active Directory sur site Active Directory
Ceci vous aidera à intégrer vos clients AD Azure sur site active directory, à l’aide d’Azure Connect d’Active Directory. Bien que les étapes sont disponibles dans le portail classique Azure, notez les instructions spéciales répertoriées dans cette section.

1.  Ouvrez une session sur le portail Azure classique, à l’aide d’un compte qui est un administrateur Global dans Active Directory Azure.
2.  Dans le volet gauche, sélectionnez **Active Directory**.
3.  Sous l’onglet **répertoire** , sélectionnez le répertoire.
4.  Sélectionnez l’onglet **Intégration d’annuaire** .
5.  Sous la section **déployer et à gérer** , suivez les étapes 1 à 3 pour intégrer Azure Active Directory à votre répertoire local.
  1.    Ajouter des domaines.
  2.    Installer et exécuter Azure AD connexion : connexion d’AD Azure d’installer en suivant les instructions suivantes, [installation personnalisée de Azure Connect d’Active Directory](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Vérifier et gérer la synchronisation d’annuaire. Instructions d’ouverture de session unique sont disponibles dans cette étape.

  > [AZURE.NOTE]
  > Configurer la fédération avec AD FS comme indiqué dans le document lié ci-dessus. Vous n’avez pas besoin configurer toutes les fonctionnalités de l’aperçu.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Mise à niveau de votre schéma des Services de domaine Active Directory

> [AZURE.NOTE]
> Mise à niveau de votre schéma Active Directory ne peut pas être annulée. Il est recommandé que vous tout d’abord effectuez ceci dans un environnement de test.

1. Connectez-vous à votre contrôleur de domaine avec un compte disposant de droits d’administrateur de l’entreprise et administrateurs du schéma à la fois.
2. Copiez le **[media] \support\adprep** répertoires et sous-répertoires à l’un de vos contrôleurs de domaine Active Directory.
3. Où [media] est le chemin d’accès du support d’installation de Windows Server 2012 R2.
4. À partir d’une invite de commandes, accédez au répertoire adprep et exécuter : **adprep.exe /forestprep**. Suivez les instructions à l’écran pour terminer la mise à niveau de schéma.

## <a name="prepare-your-active-directory-to-support-devices"></a>Préparer Active Directory pour prendre en charge des périphériques

>[AZURE.NOTE] Il s’agit d’une opération unique que vous devez exécuter pour préparer votre forêt Active Directory pour prendre en charge des périphériques. Vous devez ouvrir une session avec des autorisations d’administrateur d’entreprise et de votre forêt Active Directory doit avoir le schéma de Windows Server 2012 R2 pour effectuer cette procédure.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Préparer votre forêt Active Directory pour prendre en charge des périphériques

> [AZURE.NOTE]
>Il s’agit d’une opération unique que vous devez exécuter pour préparer votre forêt Active Directory pour prendre en charge des périphériques. Vous devez ouvrir une session avec des autorisations d’administrateur d’entreprise et de votre forêt Active Directory doit avoir le schéma de Windows Server 2012 R2 pour effectuer cette procédure.

### <a name="prepare-your-active-directory-forest"></a>Préparer votre forêt Active Directory

1.  Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : Initialize-ADDeviceRegistration
2.  Lorsque vous y êtes invité pour **ServiceAccountName**, entrez le nom du compte de service que vous avez sélectionné comme compte de service pour AD FS. S’il s’agit d’un compte gMSA, saisissez le compte dans le format de la **forme domaine\nom_compte$** . Pour un compte de domaine, utilisez le format **forme domaine\nom_compte**.



### <a name="enable-device-authentication-in-ad-fs"></a>Activer l’authentification dans AD FS périphérique

1. Sur votre serveur de fédération, ouvrez la console de gestion AD FS et accédez à **AD FS** > **Stratégies d’authentification**.
2. Sélectionnez **Modifier Global d’authentification principal...** dans le volet **Actions** .
3. Cochez **Activer l’authentification du périphérique** , puis sélectionnez**OK**.
4. Par défaut, AD FS supprime régulièrement des périphériques non utilisés à partir d’Active Directory. Vous devez désactiver cette tâche lors de l’utilisation d’enregistrement de dispositif Azure Active Directory afin que les périphériques peuvent être gérés dans Azure.


### <a name="disable-unused-device-cleanup"></a>Désactiver le nettoyage du périphérique non utilisés
1. Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : Set-AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Préparer Azure Connect d’AD pour l’écriture différée de périphérique

1.  Remplit la partie 1 : Préparer Active Directory Azure se connecter.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Joindre des périphériques à votre espace de travail à l’aide d’Azure Active Directory périphérique d’enregistrement

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Participez à un périphérique d’e/s à l’aide d’Azure Active Directory périphérique d’enregistrement

Azure inscription de périphérique Active Directory utilise le processus d’inscription de profil de Over-the-Air pour les périphériques d’e/s. Ce processus commence par l’utilisateur qui se connecte à l’URL de l’inscription de profil en utilisant le navigateur web Safari. Le format de l’URL est la suivante :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Où `yourdomainname` est le nom de domaine que vous avez configurés avec Azure Active Directory. Par exemple, si votre nom de domaine est contoso.com, l’URL serait :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Il existe de nombreuses façons de communiquer cette URL à vos utilisateurs. Une méthode recommandée est de publier cette URL dans un accès à une application personnalisée refusé dans AD FS. Ce sujet est traité dans la section suivante : [créer une stratégie d’accès application et personnalisé message d’accès refusé](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Joindre un périphérique 8.1 de Windows Azure Active Directory périphérique d’enregistrement

1. Sur votre périphérique Windows 8.1, accédez à **Paramètres PC** > **réseau** > **espace de travail**.
2. Entrez votre nom d’utilisateur dans le format UPN. Par exemple, dan@contoso.com..
3. Cliquez sur **joindre**.
4. Lorsque vous y êtes invité, connectez-vous à l’aide vos informations d’identification. Le périphérique est maintenant joint.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Participez à un périphérique Windows 7 à l’aide d’Azure Active Directory périphérique d’enregistrement
Pour enregistrer Windows 7 domaine rejoint les périphériques que vous avez besoin pour déployer le package de logiciel de périphérique d’enregistrement. Le progiciel est appelé jointure poste de travail pour Windows 7 et est disponible au téléchargement sur le [site Web Microsoft Connect](https://connect.microsoft.com/site1164). Comment faire pour utiliser le package sont disponibles à la [configuration d’enregistrement de dispositif automatique pour Windows 7 domaine rejoint périphériques](active-directory-conditional-access-automatic-device-registration-windows7.md)des instructions.

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Joindre un appareil Android à l’aide d’Azure Active Directory périphérique d’enregistrement

L' [Authentificateur Azure pour Android rubrique](active-directory-conditional-access-azure-authenticator-app.md) contient des instructions sur la manière d’installer l’authentificateur Azure application sur votre appareil Android et ajouter un compte. Lorsqu’un compte est créé avec succès sur un appareil Android, ce périphérique est joint à l’organisation un espace de travail.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Vérifiez les appareils inscrits sont différée écrit dans Active Directory
Vous pouvez afficher et vérifier que vos objets du périphérique ont été écrits revenir à Active Directory à l’aide de LDP.exe ou ADSI Edit. Les deux sont disponibles avec les outils d’administration du répertoire actif.

Par défaut, les objets périphériques différée écrit d’Azure Active Directory sont effectués dans le même domaine que votre batterie de serveurs ADFS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Créer une application stratégie et accès personnalisé message d’accès refusé
Envisagez le scénario suivant : vous créez une approbation de partie Relying dans AD FS d’application et configurer une règle d’autorisation d’émission qui autorise uniquement les appareils inscrits. Maintenant que les périphériques qui sont enregistrés sont autorisés à accéder à l’application. Pour faciliter vos utilisateurs d’accéder à l’application, vous configurez un personnalisé message d’accès refusé qui inclut des instructions sur la façon de rejoindre leur appareil. Désormais, vos utilisateurs ont un moyen transparent d’inscrire leurs périphériques pour accéder à une application.

Les étapes suivantes vous montrer comment implémenter ce scénario.

>[AZURE.NOTE]
Cette section suppose que vous avez déjà configuré une approbation de tiers de confiance de votre application dans AD FS.

1. Ouvrez l’outil AD FS MMC et accédez à AD FS > relations d’approbation > approbations des parties de confiance.
2. Recherchez l’application pour laquelle cette règle d’accès s’applique. Droit de l’application et sélectionnez Modifier les règles de revendications...
3. Sélectionnez l’onglet **Règles de délivrance d’autorisation** , puis **Ajouter une règle**
4. À partir de la **règle de revendication** modèle liste déroulante, sélectionnez **Autoriser ou refuser aux utilisateurs sur une revendication entrante**. Cliquez sur **suivant**.
5. Dans le nom de la règle de revendication : type de champ : **Autoriser l’accès à partir des périphériques**
6. À partir de l’entrée de type de revendication : zone de liste déroulante, sélectionnez **Est un utilisateur inscrit**.
7. En entrant la valeur de revendication :, tapez : **true**
8. Sélectionnez le bouton radio **Autoriser l’accès aux utilisateurs disposant de cette revendication entrante** .
9. Sélectionnez **Terminer** , puis sélectionnez **Appliquer**.
10. Supprimer toutes les règles qui sont plus permissifs que la règle que vous venez de créer. Par exemple, supprimez la règle de **Permettre l’accès à tous les utilisateurs** par défaut.

Votre application est maintenant configurée pour autoriser l’accès uniquement lorsque l’utilisateur provient d’un périphérique qu’ils inscrites et joint au poste de travail. Pour les stratégies d’accès plus avancées, voir [Gérer les risques avec contrôle d’accès de plusieurs facteurs](https://technet.microsoft.com/library/dn280949.aspx).

Ensuite, vous allez configurer un message d’erreur personnalisé pour votre application. Le message d’erreur permet aux utilisateurs de savoir qu’ils doivent relient leur périphérique au poste de travail avant de pouvoir accéder à l’application. Vous pouvez créer une application personnalisée message d’accès refusé à l’aide de HTML et Windows PowerShell personnalisées.

Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez la commande suivante. Remplacer des portions de la commande avec des éléments qui sont spécifiques à votre système :

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Vous devez inscrire votre appareil avant d’accéder à cette application.

**Si vous utilisez un périphérique iOS, cliquez sur ce lien pour joindre votre périphérique**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Joindre ce périphérique iOS à votre espace de travail.


**Si vous utilisez un périphérique Windows 8.1**, vous pouvez joindre votre périphérique en cliquant sur **Paramètres PC ** >  **réseau** > **espace de travail**.


«**Utiliser le nom de partie de confiance**» étant le nom de votre objet de confiance de partie s’appuyer des applications dans AD FS.
Où **« votredomaine.com »** est le nom de domaine que vous avez configurés avec Azure Active Directory. Par exemple, contoso.com.
Veillez à supprimer tous les sauts de ligne (le cas échéant) à partir du contenu html que vous passez à l’applet de commande **Set-AdfsRelyingPartyWebContent** .


Maintenant, lorsque les utilisateurs accèdent à votre application à partir d’un périphérique qui n’est pas enregistré avec le Service d’enregistrement du périphérique Azure Active Directory, ils recevront une page semblable à la capture d’écran ci-dessous.

![Screeshot d’une erreur lorsque les utilisateurs n’ont pas enregistré leur périphérique avec Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
