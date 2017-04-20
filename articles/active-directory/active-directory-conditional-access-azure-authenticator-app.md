
<properties
    pageTitle="Authentificateur Azure pour Android | Microsoft Azure"
    description="App de Microsoft Azure authentificateur peut servir à la connexion pour accéder aux ressources de travail. L’application Azure authentificateur vous informe d’une demande de vérification de deux facteurs en attente par l’affichage d’une alerte sur votre appareil mobile."
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

# <a name="azure-authenticator-for-android"></a>Authentificateur Azure pour Android

Votre administrateur peut avoir recommandé vous permet d’utiliser l’authentificateur Azure de Microsoft pour vous connecter pour accéder aux ressources de travail. Cette application fournit ces deux options de connexion :

* Plusieurs facteurs d’authentification vous permet de sécuriser vos comptes de travail ou à l’école avec vérification de deux étapes. Vous connectez-vous en accomplissant quelque chose vous savez (par exemple, votre mot de passe) et de Protégez le compte encore plus loin avec quelque chose, vous disposez (une clé de sécurité à partir de cette application). L’application Azure authentificateur vous informe d’une demande de vérification de deux facteurs en attente par l’affichage d’une alerte sur votre appareil mobile. Vous devez afficher simplement vérifier la demande de l’application et appuyez sur ou sur Annuler. Alternativement, vous pouvez être invité à entrer le mot de passe s’affichée dans l’application.

* Compte de travail vous permet d’activer votre téléphone Android ou votre Tablet PC dans un périphérique de confiance et de fournir des Single Sign-On (SSO) pour les applications d’entreprise. Votre administrateur peut nécessiter que vous permet d’ajouter un compte pour accéder aux ressources de l’entreprise. SSO permet de se connecter une seule fois et automatiquement faire usage de la connexion pour toutes les applications que votre société a mis à votre disposition.

## <a name="installing-the-azure-authenticator-app"></a>L’installation de l’application de l’authentificateur Azure

Vous pouvez installer l’application Azure authentificateur Google Play magasin.
Les instructions pour ajouter le compte à partir d’un périphérique vs un Android non - Samsung Samsung Android sont légèrement différentes. Vous trouverez ci-dessous les instructions pour les deux.

<a name="adding-the-work-account-from-samsung-android-device"></a>Ajouter le compte à partir du périphérique de Samsung Android
----------------------------------------------------------------------------------------------------------------
###<a name="adding-the-work-account-through-the-app-home-screen"></a>Ajout du compte de travail par le biais de l’écran d’accueil app

Les instructions suivantes sont applique à Samsung GS3 et au-dessus de téléphones ou Remarque 2 et au-dessus de comprimés.

1. Sur l’écran d’accueil de l’application, acceptez le contrat de licence utilisateur final (CLUF).
2. Sur l’écran activer le compte, cliquez sur le menu contextuel de droite et sélectionnez le **compte de travail**.
3. Dans l’écran Ajouter un compte, sélectionnez** Compte de travail**.
4. Dans Activer périphérique administrateur, cliquez sur **Activer**.
5. Sur l’écran de la politique de confidentialité, activez la case à cocher et cliquez sur **Confirmer**.
6. Sur l’écran de jointure de l’espace de travail, entrez le nom d’utilisateur fourni par votre organisation, puis cliquez sur **joindre**.
7. Pour vous connecter à l’application de l’authentificateur d’Azure, entrez à votre organisation une *** compte et mot de passe et cliquez sur **connexion**.
8. L’écran suivant affiche des informations sur l’authentification à plusieurs facteurs (AMF) est ajouté pour sécurité et est facultative. Vous verrez cet écran si votre travail ou votre école requiert une authentification du second facteur pour la création de compte. Il fournit des instructions pour vérifier votre compte.
9. L’écran de la jointure de l’espace de travail affiche le message «**joindre votre espace de travail**». L’application Azure authentificateur tente de joindre votre appareil à votre espace de travail.
10. Vous devez voir le message joint d’espace de travail sur l’écran suivant.

>[AZURE.NOTE]
> Vous êtes autorisé à un compte unique sur votre périphérique.

### <a name="adding-the-work-account-from-the-settings-menu"></a>Ajouter le compte à partir du menu Paramètres
Après avoir installé l’application Azure authentificateur, vous pouvez également créer un compte à partir du Gestionnaire de compte Android.

1. Dans le menu Paramètres, accédez aux **comptes** , puis cliquez sur **Ajouter un compte**.
2. Suivez les étapes 3 à 10 de la procédure, ajoutez le compte de travail par le biais de l’écran d’accueil app, pour ajouter un compte.

<a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Ajouter le compte à partir d’un périphérique non - Samsung Android
------------------------------------------------------------------------------------------------------------------
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Ajout du compte de travail par le biais de l’écran d’accueil app

1. Sur l’écran d’accueil de l’application, acceptez le contrat de licence utilisateur final (CLUF).
2. Sur l’écran activer le compte, cliquez sur le menu contextuel de droite et sélectionnez le **compte de travail**.
3. Dans l’écran comptes, cliquez sur **Ajouter un compte**.
4. Si vous voyez l’écran de comptes, cliquez sur **Ajouter un compte**. Si un compte est a déjà été créé précédemment, vous verrez un écran de synchronisation indiquant l’existant compte de travail. Vous pouvez conserver le compte par simplement cliquer sur la flèche vers la gauche de l’écran d’accueil. Vous pouvez également sélectionner le compte à supprimer et recréer un écran sur le lieu de travail joindre de nouveau travail compte, entrez le nom d’utilisateur fourni par votre organisation et cliquez sur joindre.
5. Pour vous connecter à l’application de l’authentificateur d’Azure, entrez votre compte d’organisation et un mot de passe et cliquez sur **connexion**.
7. L’écran suivant affiche des informations sur l’authentification à plusieurs facteurs (AMF) est ajouté pour sécurité et est facultative. Vous verrez cet écran si votre travail ou votre école requiert une authentification du second facteur pour la création de compte. Il fournit des instructions pour vérifier votre compte.
8. Sur l’écran suivant, cliquez sur **OK** . Ne modifiez pas le nom du certificat.
le message « Jonction de votre espace de travail ». L’application Azure authentificateur tente de joindre votre appareil à votre espace de travail.
Vous devez voir le message joint d’espace de travail sur l’écran suivant.

>[AZURE.NOTE]
> Vous êtes autorisé à un compte unique sur votre périphérique.

Après avoir installé l’application Azure authentificateur, vous pouvez également créer un compte à partir du Gestionnaire de compte Android.

1. Dans le menu **paramètres** , accédez aux comptes, puis cliquez sur **Ajouter un compte**.
2. Suivez les étapes 2 à 7 de la procédure, en ajoutant le compte de travail par le biais de l’application home écran **, pour ajouter un compte.

### <a name="how-to-find-out-which-version-is-installed"></a>Comment savoir quelle version est installée.

1. Vous pouvez connaître la version de l’application Azure authentificateur et versions de service associé sont installées sur votre périphérique.
2. Dans le menu contextuel, cliquez sur à **propos**.
3. L’écran à propos affiche les services de l’application et les versions installées sur votre appareil.
 
### <a name="sending-log-files-to-report-issues"></a>Envoi de journaux pour signaler des problèmes

1. Suivez les instructions sur le Support de Microsoft pour signaler un incident avec l’application Azure authentificateur, obtenir un nombre d’incidents et d’envoyer les fichiers journaux contre le numéro d’incident attribué comme suit :
2. Dans le menu contextuel, cliquez sur **connexion**.
3. Si vous avez un incident ouvert avec le Support Microsoft, notez le numéro de l’incident (vous en aurez besoin pour une étape ultérieure). Si vous n’avez pas encore créé un incident de support et que vous souhaitez obtenir de l’aide, suivez les conseils de [Prise en charge de Microsoft](https://support.microsoft.com/en-us/contactus) pour ouvrir un nouvel incident.
4. Sur l’écran d’enregistrement, cliquez sur **Envoyer**.
5. Sélectionnez le fournisseur de messagerie que vous souhaitez utiliser.
7. Si vous disposez déjà d’un incident ouvert de Support de Microsoft, contactez le technicien du support technique attribué à votre demande pour savoir comment envoyer les données du journal et l’avez associé à votre incident. L’ingénieur de Support vous fournira les informations de la ligne d’adresse et l’objet de message. Si vous ne disposez pas déjà d’un incident de support, veuillez suivre les instructions au Support Microsoft pour ouvrir un nouvel incident.
9. Modifiez la ligne **à** et ligne **objet** avec les informations que vous avez reçu du Support Microsoft.
10. L’application Azure authentificateur attache le fichier journal pour le courrier électronique que vous envoyez. Décrivez le problème que vous rencontrez, mettre à jour la liste des destinataires (facultative) et envoyez l’e-mail.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>La suppression du compte de travail et quitter votre espace de travail

Vous pouvez supprimer le compte de travail créées à tout moment comme suit :

**Pour supprimer le compte à partir du menu Paramètres**

1. Dans le Gestionnaire de comptes, sélectionnez **compte de travail**.
2. Dans l’écran compte de travail, dans **Paramètres généraux**, sélectionnez **les paramètres de compte – laissez votre réseau d’entreprise**.
3. Sélectionnez le **laissez** sur l’écran de **Jointure de l’espace de travail** .
4. Cliquez sur **OK** lorsque le message suivant : « êtes-vous sûr de que vous souhaitez faire de l’espace de travail » s’affiche.
5. Cela garantit que vous avez supprimé votre compte à partir de votre espace de travail.

>[AZURE.NOTE]
>Il est recommandé que vous n’utilisez pas l’option Supprimer le compte à supprimer un compte que cette option peut ne pas fonctionne dans certaines versions antérieures de Android.

##<a name="uninstalling-the-app"></a>Désinstallation de l’application

Sur un appareil Android de Samsung, administrateur de périphérique doit être supprimé comme suit avant de désinstaller le 
1. Dans **paramètres**, sous **système**, sélectionnez **sécurité**.
2. Au sein D**Administration de l’unité de sauvegarde**, cliquez sur **administrateurs de l’équipement**. Assurez-vous que la case à cocher **Azure authentificateur** est désactivée.

##<a name="troubleshooting"></a>Résolution des problèmes

Si vous voyez l' **Erreur du Keystore**, cela peut être parce que vous n’avez pas le verrou de l’écran de jeu avec un code confidentiel. Pour contourner ce problème, désinstallez l’application Azure authentificateur, configurer un code confidentiel pour votre écran de verrouillage et réinstallez l’application.
