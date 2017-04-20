<properties
    pageTitle="Messages d’erreur spécifiques RDP pour les machines virtuelles d’Azure | Microsoft Azure"
    description="Comprendre les messages d’erreur spécifiques que vous pouvez recevoir lorsque vous essayez d’utilisent la connexion Bureau à distance à une machine virtuelle Windows dans Azure"
    keywords="Erreur du Bureau à distance, erreur de connexion Bureau à distance, ne peut pas se connecter à la machine virtuelle, dépannage de bureau à distance"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Dépannage des messages d’erreur RDP spécifiques à un ordinateur virtuel Windows Azure
Vous pouvez recevoir un message d’erreur spécifique lors de l’utilisation de connexion Bureau à distance à un ordinateur virtuel de Windows (VM) dans Azure. Cet article décrit certains des messages d’erreur plus courants rencontrés, ainsi que de la résolution des problèmes des étapes pour les résoudre. Si vous rencontrez des problèmes de connexion à votre ordinateur virtuel à l’aide de RDP mais ne pas rencontrerez un message d’erreur spécifique, consultez le [guide de dépannage pour le Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md).

Pour plus d’informations sur les messages d’erreur spécifiques, consultez les rubriques suivantes :

- [La session distante a été déconnectée car aucun serveur de licences bureau à distance fournir une licence](#rdplicense).
- [Bureau à distance ne peut pas trouver l’ordinateur « nom »](#rdpname).
- Erreur de [d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée](#rdpauth).
- [Erreur de sécurité de Windows : vos informations d’identification n’a pas fonctionné](#wincred).
- [Cet ordinateur ne peut pas se connecter à l’ordinateur distant](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>La session distante a été déconnectée car aucun serveur de licences bureau à distance fournir une licence.

Cause : La période de grâce de licence 120 jours pour le rôle serveur Bureau à distance a expiré et vous devez installer des licences.

Pour résoudre ce problème, enregistrez une copie locale du fichier RDP à partir du portail et exécutez la commande suivante à une invite de commande PowerShell pour se connecter. Cette étape désactive le Gestionnaire de licences pour que cette connexion :

        mstsc <File name>.RDP /admin

Si vous n’avez réellement pas besoin plus de deux connexions Bureau à distance simultanées à l’ordinateur virtuel, vous pouvez utiliser le Gestionnaire de serveur pour supprimer le rôle serveur Bureau à distance.

Pour plus d’informations, consultez le blog [Azure VM échoue avec « Aucun serveur de licences des services Bureau à distance disponible »](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Bureau à distance ne peut pas trouver l’ordinateur « nom ».

Cause : Le client Bureau à distance sur votre ordinateur ne peut pas résoudre le nom de l’ordinateur dans les paramètres du fichier RDP.

Solutions possibles :

- Si vous êtes sur l’intranet d’une entreprise, assurez-vous que votre ordinateur dispose d’un accès au serveur proxy et qu’il puisse le trafic HTTPS lui envoyer.

- Si vous utilisez un fichier RDP stocké localement, essayez d’utiliser celui qui est généré par le portail. Cette étape garantit que le nom DNS pour l’ordinateur virtuel, ou le service en nuage et le port du point de terminaison de la machine virtuelle. Voici un exemple de fichier RDP généré par le portail :

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

La partie de l’adresse de ce fichier RDP a :
- Le nom de domaine pleinement qualifié du service cloud qui contient de la machine virtuelle (« tailspin-azdatatier.cloudapp.net » dans cet exemple).

- Le port TCP externe du point de terminaison pour le trafic de bureau à distance (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Une erreur d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée.

Cause : L’ordinateur virtuel de la cible ne peut pas localiser l’autorité de sécurité dans la partie nom d’utilisateur de vos informations d’identification.

Lorsque votre nom d’utilisateur est dans le *système de sécurité*du formulaire\\*nom d’utilisateur* (exemple : CORP\User1), la partie du *système de sécurité* est le nom de la machine virtuelle de l’ordinateur (pour l’autorité de sécurité locale) ou un nom de domaine Active Directory.

Solutions possibles :

- Si le compte est local pour l’ordinateur virtuel, assurez-vous que le nom de la machine virtuelle est correctement orthographié.

- Si le compte est dans un domaine Active Directory, vérifiez l’orthographe du nom de domaine.

- Si c’est un compte de domaine Active Directory et le nom de domaine est correcte, vérifiez qu’un contrôleur de domaine est disponible dans ce domaine. Il s’agit d’un problème courant dans Azure réseaux virtuels qui contiennent des contrôleurs de domaine qu’un contrôleur de domaine n’est pas disponible car il n’a pas été démarré. Pour résoudre ce problème, vous pouvez utiliser un compte d’administrateur local au lieu d’un compte de domaine.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Erreur de sécurité de Windows : vos informations d’identification n’a pas fonctionné.

Cause : L’ordinateur virtuel de la cible ne peut pas valider votre nom de compte et le mot de passe.

Un ordinateur fonctionnant sous Windows peut valider les informations d’identification d’un compte local ou un compte de domaine.

- Pour les comptes locaux, utilisez *NomOrdinateur*\\syntaxe de*nom d’utilisateur* (exemple : SQL1\Admin4798).
- Pour les comptes de domaine, utilisez le *nom_domaine*\\syntaxe de*nom d’utilisateur* (exemple : CONTOSO\peterodman).

Si vous avez promu votre machine virtuelle à un contrôleur de domaine dans une nouvelle forêt Active Directory, le compte administrateur local que vous connecté avec est converti en un équivalent compte avec le même mot de passe dans la forêt et le domaine. Le compte local est alors supprimé.

Par exemple, si vous connectez avec le compte local DC1\DCAdmin et puis promu l’ordinateur virtuel en tant que contrôleur de domaine dans une nouvelle forêt pour le domaine corp.contoso.com, le compte local DC1\DCAdmin est supprimé et un nouveau compte de domaine (CORP\DCAdmin) est créé avec le même mot de passe.

Assurez-vous que le nom de compte est un nom de la machine virtuelle peut vérifier qu’un compte valide, et que le mot de passe est correct.

Si vous devez modifier le mot de passe du compte administrateur local, voir [Comment faire pour réinitialiser un mot de passe ou le service de bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Cet ordinateur ne peut pas se connecter à l’ordinateur distant.

Cause : Le compte utilisé pour se connecter n’a pas les droits d’ouverture de session Bureau à distance.

Chaque ordinateur Windows possède un groupe local utilisateurs du Bureau à distance, qui contient les comptes et les groupes qui peuvent signer à distance dedans. Les membres du groupe Administrateurs local ont également accès, même si ces comptes ne sont pas répertoriés dans le groupe local utilisateurs du Bureau à distance. Pour les ordinateurs à un domaine, le groupe Administrateurs local contient également les administrateurs du domaine pour le domaine.

Assurez-vous que le compte que vous utilisez pour vous connecter avec dispose des droits d’ouverture de session Bureau à distance. Pour résoudre ce problème, utilisez un domaine ou un compte d’administrateur local pour vous connecter via le Bureau à distance. Pour ajouter le compte de votre choix au groupe local utilisateurs du Bureau à distance, utilisez le composant logiciel enfichable Microsoft Management Console (**les outils système > utilisateurs et groupes locaux > groupes > utilisateurs du Bureau à distance**).


## <a name="next-steps"></a>Étapes suivantes
Si aucune de ces erreurs est survenue et que vous avez un problème inconnu de connexion utilisant le protocole RDP, consultez le [guide de dépannage pour le Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Pour le dépannage de l’accès à des applications qui s’exécutent sur une machine virtuelle, consultez [Dépannage des accès à une application en cours d’exécution sur un ordinateur virtuel d’Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Si vous rencontrez des problèmes à l’aide de SSH (Secure Shell) pour se connecter à une VM Linux dans Azure, consultez [résoudre les problèmes de connexions SSH à une VM Linux dans Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).