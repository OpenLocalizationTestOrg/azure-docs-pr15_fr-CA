<properties
pageTitle="Installer et configurer les outils de création de GitHub"
description="Les outils et les étapes à suivre pour obtenir défini pour la création de contenu à Azure dans GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Installer et configurer les outils de création de GitHub

Suivez les étapes décrites dans cet article pour configurer des outils pour contribuer à la documentation technique Azure. Collaborateurs occasionnels et occasionnels permet probablement le GitHub de l’interface utilisateur décrit à l’étape 2.

Si vous ne connaissez pas Git, vous souhaiterez peut-être consulter certains termes Git : [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). En outre, ce thread StackOverflow contient un glossaire des termes Git que vous rencontrerez dans cet ensemble d’étapes : [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Contenu

- [Créer un compte de GitHub et de configurer votre profil](#create-a-github-account-and-set-up-your-profile)
- [Inscrivez-vous à Disqus](#sign-up-for-disqus)
- [Déterminez si vous devez vraiment de suivre le reste des étapes suivantes](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Autorisations de GitHub](#permissions-in-github)
- [Installer Git pour Windows](#install-git-for-windows)
- [Activer l’authentification à deux facteurs](#enable-two-factor-authentication)
- [Installer un éditeur de démarque](#install-a-markdown-editor)
- [Configurer Atom](#configure-atom)
- [Branche du référentiel et le copier sur votre ordinateur](#fork-the-repository-and-copy-it-to-your-computer)
- [Configurer votre nom d’utilisateur et d’un e-mail localement](#configure-your-user-name-and-email-locally)
- [Étapes suivantes](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Créer un compte de GitHub et de configurer votre profil

Pour contribuer au contenu technique Azure, vous aurez besoin d’un compte [GitHub](http://www.github.com) .

Si vous êtes un collaborateur de Microsoft, vous devez configurer votre compte GitHub afin que vous avez clairement identifié comme un employé de Microsoft. Définissez votre profil comme suit :

- **Image de profil**: une photo de vous (obligatoire)
- **Nom**: votre nom et prénom (obligatoire)
- **E-mail**: votre adresse de messagerie de Microsoft (facultatif)
- **Société**: Microsoft Corporation (obligatoire)
- **Emplacement**: liste site (facultatif)

Votre profil doit ressembler à ce profil :

<p align="center">
 ![Exemple de profil GitHub](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>Inscrivez-vous à Disqus

Chaque article publié de technique Azure a un flux de commentaire fourni par le service Disqus.

 ![Logo de disque](./media/tools-and-setup/discus.png)

Si vous êtes un employé de Microsoft, et si vous êtes l’auteur d’ou un collaborateur à un article, vous devez inscrire pour Disqus, donc vous pouvez participer au flux de commentaire pour l’article.

1. Inscrivez-vous à un compte [http://www.disqus.com/](http://www.disqus.com/)
2. Remplissez votre profil comme suit :

 - **Nom complet**: votre nom complet tel qu’il est affiché dans votre liste de carnet d’adresses Microsoft, ainsi que les informations entre crochets, qui est votre alias plus @MSFT. Format : *tout d’abord la dernière [alias@MSFT] *
 - **Emplacement**: votre emplacement
 - **Courte biographie**: votre titre

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Déterminez si vous devez vraiment de suivre le reste des étapes suivantes

Vous devrez peut-être pas à suivre toutes les étapes de cet article. Il dépend de l’ordre de tri de la contribution de contenu vous voulez ou devez faire.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Envoyez une modification de texte uniquement à un article existant

Si vous seulement devez ou que vous souhaitez effectuer des mises à jour de textuels dans un article existant, vous ne devrez probablement suivez le reste des étapes. Vous pouvez utiliser l’éditeur de démarque de basée sur le web de GitHub pour soumettre vos modifications. Cliquez simplement sur le lien GitHub dans l’article que vous souhaitez modifier :

 ![Exemple de profil GitHub](./media/tools-and-setup/contributetogit.png)

 Puis, cliquez sur l’icône Modifier dans la version GitHub de l’article

 ![Exemple de profil GitHub](./media/tools-and-setup/editicon.PNG)

 Qui ouvre l’éditeur web facile d’utilisation qui facilite l’envoi des modifications. Vous n’avez pas besoin de suivre les autres étapes de cet article.

###<a name="all-other-changes"></a>Toutes les autres modifications
L’UI GitHub ne prend pas en charge la création de nouveaux fichiers et glissez -déplacez des images. Toutefois, lorsque vous travaillez dans l’interface utilisateur, gestion des branches peuvent être déroutant nous vous recommandons généralement vous installez les outils et découvrez les commandes pour la création et la gestion des articles. Si vous souhaitez utiliser l’interface utilisateur, voir :

- [Création de fichiers sur Github](https://github.com/blog/1327-creating-files-on-github)
- [Télécharger des fichiers vers les référentiels](https://github.com/blog/2105-upload-files-to-your-repositories)

Pour les types suivants de travail, nous vous recommandons d’installer et apprenez à utiliser les outils :

 - Des modifications importantes à un article
 - Création et publication d’un nouvel article
 - Ajout de nouvelles images ou la mise à jour d’images
 - Mise à jour d’un article sur une période de jours sans publication des modifications de chaque de ces jours.
 - Création de contenu pour une mise à jour est de sortir un jour donné à un moment donné

##<a name="permissions-in-github"></a>Autorisations de GitHub

Toute personne disposant d’un compte GitHub peut contribuer à Azure contenu technique par le biais de notre référentiel public à [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). Aucune autorisation spéciale n’est requise.

Si vous êtes un chef de projet Microsoft ou writer travaillant sur Azure le contenu, vous devez travailler dans notre privé référentiel de contenu, azure-contenu-pr. Visitez [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) pour demander les autorisations de lecture qui vous permettra d’effectuer des versements par l’intermédiaire de la mis en pension privés - se connecter à GitHub à l’aide du bouton > cliquez sur Azure > cliquez sur **une équipe de jointure** ou de **joindre une autre équipe**, puis recherchez et joindre le groupe **azure-contenu-en lecture** .

## <a name="install-git-for-windows"></a>Installer Git pour Windows

Installez le Git pour Windows à partir de [http://git-scm.com/download/win](http://git-scm.com/download/win). Ce téléchargement installe le système de contrôle de version Git et qu’il installe Git Bash, l’application de ligne de commande que vous allez utiliser pour interagir avec votre référentiel Git local.

Vous pouvez accepter les paramètres par défaut ; Si vous souhaitez que les commandes disponibles dans la ligne de commande Windows, sélectionnez l’option qui lui permet de.

<p align="center">
 ![Exemple de profil GitHub](./media/tools-and-setup/gitbashinstall.png)

(Remarque : il n’est pas le même que « Github for Windows ». « Github pour Windows » est un autre outil GUI qui fonctionne également si vous souhaitez lire sur vous-même. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Activer l’authentification à deux facteurs

Vous devez activer l’authentification à deux facteurs (2FA) sur votre compte de GitHub si vous travaillez dans le référentiel de contenu privé. Elle est requise dans le référentiel privé.

Pour ce faire, suivez les instructions dans les deux GitHub rubriques d’aide suivantes :

- [Sur l’authentification à deux facteurs](https://help.github.com/articles/about-two-factor-authentication/)
- [Création d’un jeton d’accès pour l’utilisation de ligne de commande](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Lorsque vous créez le jeton, sélectionnez toutes les portées disponibles dans l’interface utilisateur de création de jeton ([plus de détails sur chaque étendue](https://developer.github.com/v3/oauth/#scopes))

Après avoir activé le 2FA, vous devez entrer le jeton d’accès au lieu de votre mot de passe de GitHub à l’invite de commande, lorsque vous essayez d’accéder à un référentiel de GitHub à partir de la ligne de commande. Le jeton d’accès n’est pas le code d’authentification que vous obtenez dans un message texte lorsque vous configurez 2FA. Il s’agit d’une longue chaîne qui ressemble à ceci : fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Quelques remarques sur ce sujet :

- Lorsque vous créez votre jeton d’accès, l’enregistrer dans un fichier texte pour le rendre facilement accessible lorsque vous en avez besoin.

- Lorsque vous devez coller le jeton, connaître plus loin, il existe deux méthodes pour coller dans la ligne de commande :

 - Cliquez sur l’icône dans le coin supérieur gauche de la fenêtre de ligne de commande > Edition > Coller.
 - Cliquez sur l’icône dans le coin supérieur gauche de la fenêtre et cliquez sur Propriétés > Options > Mode d’édition rapide. Cela permet de configurer la ligne de commande afin de le coller en cliquant dans la fenêtre de ligne de commande.

## <a name="install-a-markdown-editor"></a>Installer un éditeur de démarque

Nous rédigeons le contenu à l’aide de la notation simple « promotions » dans les fichiers, plutôt que complexes « balisage » (HTML, XML, etc.). Par conséquent, vous devrez installer un éditeur de démarque.

- **Atome**: la plupart d'entre nous utilise l’éditeur de démarque atome de GitHub : [http://atom.io](http://atom.io). Il ne nécessite pas une licence pour une utilisation professionnelle. Il a la vérification de l’orthographe.

- **Le bloc-notes**: vous pouvez utiliser le bloc-notes pour une option très léger.

- **Prose**: il s’agit d’un éditeur de démarque légère, élégante, en ligne et ouverts qui offre un aperçu. Visitez le site [http://prose.io](http://prose.io) et autoriser la Prose dans votre référentiel.

- **[Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)** - entrée de Microsoft dans cet espace.

## <a name="configure-atom"></a>Configurer Atom

Si vous utilisez Atom, vous devez paramétrer quelques éléments.

- Atome par défaut à l’aide de 2 les locaux pour les onglets, mais démarque attend 4 espaces. Si vous laissez la valeur par défaut de deux, votre article est superbes dans l’aperçu local, mais pas lorsqu’il est importé dans Azure. Ainsi, configurer Atom pour utiliser 4 espaces - ce paramètre se trouve sous fichier > Paramètres > Paramètres de l’éditeur > longueur de l’onglet.
- Vous allez sans doute également activer la renvoyer à la ligne paramétrés dans cette section, qui fait de même, « retour » dans le bloc-notes.
- Pour activer l’aperçu de la démarque, cliquez sur Packages > Aperçu de la démarque > Aperçu de Toggle. Vous pouvez utiliser Ctrl-Maj-M pour basculer vers le mode HTML de l’aperçu.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Branche du référentiel et le copier sur votre ordinateur

1. Créer une branche du référentiel dans le GitHub - atteindre le haut à droite de la page et cliquez sur le bouton de la branche. Si vous y êtes invité, sélectionnez votre compte de l’emplacement où la branche doit être créée. Cette opération crée une copie du référentiel au sein de votre compte Git Hub. En règle générale, rédacteurs techniques et les responsables de programme doivent fourche azure-contenu-pr, le mis en pension privés. Collaborateurs de la Communauté doivent fourche azure-contenu, le mis en pension public. Vous ne devez branche une fois ; après l’installation de votre premier, si vous souhaitez copier votre branche à un autre ordinateur, vous suffit exécuter les commandes qui suivent dans cette section pour copier la mis en pension sur votre ordinateur.  Si vous choisissez de créer des branches de deux référentiels, vous devrez créer une branche pour chaque référentiel.

2. Copiez le personnel jeton d’accès que vous avez obtenu à partir de [https://github.com/settings/tokens](https://github.com/settings/tokens). Vous pouvez accepter les autorisations par défaut pour le jeton.  Enregistrer le jeton d’accès personnelles dans un fichier texte pour une réutilisation ultérieure.

3. Ensuite, copiez le référentiel à votre ordinateur avec vos informations d’identification incorporées dans la chaîne de commande.  Pour ce faire, ouvrez le Git Bash et l’exécuter en tant qu’administrateur. À l’invite de commandes, saisissez la commande suivante.  Cette commande crée un répertoire azure-content(-pr) sur votre ordinateur.  Si vous utilisez l’emplacement par défaut, elle sera à c:\users<your Windows user name>\azure-content(-pr).

Mis en pension public :

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Mis en pension privés :

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Par exemple, cette commande de clone peut ressembler à ceci :

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Connexion de référentiel distant et configurer les informations d’identification

Créer une référence au référentiel racine en saisissant ces commandes. Cela définit les connexions dans le référentiel dans GitHub afin que vous pouvez obtenir les dernières modifications apportées sur votre ordinateur local, puis remettez vos modifications à GitHub. Cette commande configure également votre jeton localement afin que vous n’êtes pas obligé d’entrer votre nom et votre mot de passe chaque fois que vous essayez d’accéder à la mis en pension en amont et votre branche sur GitHub.

Mis en pension public :

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Mis en pension privés :

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Généralement, cela prend un certain temps. Après cela, vous n’aurez pas à nouveau une branche ou entrer de nouveau vos informations d’identification. Vous devrez uniquement copier les branches à un ordinateur local à nouveau si vous configurez les outils sur un autre ordinateur.


## <a name="configure-your-user-name-and-email-locally"></a>Configurer votre nom d’utilisateur et d’un e-mail localement

Pour vous assurer que vous êtes répertorié correctement en tant que collaborateur, vous devez configurer votre e-mail et le nom de l’utilisateur localement dans Git.

1. Démarrer le Git Bash et basculez en contenu azure ou azure-contenu-pr :

   ````
   cd azure-content
   ````

 ou

   ````
   cd azure-content-pr
   ````

2. Configurer votre nom d’utilisateur afin qu’il correspond à votre nom tel que vous le configuré dans votre profil de GitHub :

    ````
    git config --global user.name "John Doe"
    ````
3. Configurer votre messagerie afin qu’il corresponde à l’e-mail principal désigné dans votre profil de GitHub. Si vous êtes un employé MSFT, il devrait être votre adresse de courriel MSFT :

    ````
    git config --global user.email "alias@example.com"
    ````
4. Type de `git config -l` et passez en revue vos paramètres locaux pour vérifier le nom d’utilisateur et e-mail dans la configuration sont corrects.

##<a name="next-steps"></a>Étapes suivantes

- Comprendre le type de contenu qui appartient à la mis en pension contenu technique et savoir ce que ne fait pas partie. Consultez les [conseils de canal de contenu](./content-channel-guidance.md)!
- Procédez comme [suit pour créer ou modifier un article et qu’il envoie ensuite à la publication](./git-commands-for-master.md).
- Copiez [le modèle de la démarque](../markdown templates/markdown-template-for-new-articles.md) comme base pour un nouvel article.
- Utilisez [cette liste de vérification pour vérifier votre demande d’extraction répondent aux critères de qualité](./contributor-guide-pr-criteria.md) pour la fusion.


###<a name="contributors-guide-navigation"></a>Navigation de guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
