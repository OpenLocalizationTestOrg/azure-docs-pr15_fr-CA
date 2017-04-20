
<properties
    pageTitle="L’accès à vos applications à partir de n’importe quel périphérique | Microsoft Azure"
    description="Découvrez quels clients sont pris en charge pour Azure RemoteApp et comment accéder à vos applications."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="accessing-your-apps-in-azure-remoteapp"></a>Accéder à vos applications dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

L’une des beautés de Azure RemoteApp est que vous pouvez accéder des applications à partir d’un de vos périphériques. Mieux encore, vous pouvez commencer à travailler sur un seul périphérique et ensuite une transition transparente vers un second périphérique et reprendre là où vous l’avez laissé. Mise en route vous devez télécharger le client approprié pour votre périphérique et vous connecter au service.

Dans cette section, nous allons revoir les clients actuellement pris en charge et comment les télécharger avant de voir comment se connecter à RemoteApp à partir de chacun des clients.

## <a name="supported-clients"></a>Clients pris en charge

Vous pouvez accéder à RemoteApp à l’aide de la procédure ci-dessous si votre périphérique est en cours d’exécution un de ces systèmes d’exploitation :

 - Windows 10 
 - 8.1 de Windows
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows Phone 8.1
 - e/s
 - Mac OS X
 - Android


 Que se passe-t-il pour les clients légers ? Les clients légers Windows Embedded suivants sont pris en charge :

- Windows Embedded Standard 7
- Windows Embedded Standard 8
- Windows Embedded industrie 8.1 Pro
- Windows 10 IoT Enterprise


## <a name="downloading-the-client"></a>Téléchargement du client

Quelle que soit la plate-forme utilisée, le client, que vous devez accéder aux RemoteApp vous trouverez sur la page de [téléchargement du client Bureau à distance](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) .

Cliquez sur les différents liens soit directement commencent le téléchargement du client ou enverra à que vous au client télécharger la page dans le magasin de l’application pour cette plate-forme. Installer le client en suivant les instructions à l’écran.

Une fois que vous avez installé le client sur votre périphérique et a lancé, accédez directement à la section correspondante ci-dessous pour savoir comment vous connecter à RemoteApp à partir de ce client.

## <a name="android"></a>Android

Une fois que vous avez installé l’application de bureau à distance de Microsoft à partir du magasin de lecture de Google, vous la trouverez dans votre liste d’app sous **Bureau à distance**.

1. Lancement de l’application vous propose à un centre de connexion vide, sauf si vous avez déjà été à l’aide de l’application. Pour vous familiariser avec Azure RemoteApp, cliquez sur le bouton d’ajouter **« « + » »** et cliquez sur **Azure RemoteApp**. 

     ![Centre de connexion vide](./media/remoteapp-clients/Android1.png)

2. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service. Cliquez sur **Démarrer**.

    ![Invite de connexion](./media/remoteapp-clients/Android2.png)

3. Sur la page suivante, tapez votre **adresse de courriel** et cliquez sur **Continuer**. Commence le processus de connexion à l’aide d’Azure Active Directory.

    ![Première page Azure Active Directory](./media/remoteapp-clients/Android3.png)

4. Suivez les instructions à l’écran pour vous connecter avec votre compte Microsoft (précédemment appelé « LiveID ») ou l’ID d’organisation. Une fois connecté, vous pouvez être présentées avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations que vous faites confiance et que vous cliquez sur **terminé**. 

    ![Page des invitations](./media/remoteapp-clients/Android4.png)

5. Après avoir accepté votre invitation, la liste des applications que vous avez accès, sera téléchargée sur votre ordinateur et disponible dans le centre de connexion. Cliquez sur une des applications à l’utiliser.

    ![Centre de connexion avec une alimentation](./media/remoteapp-clients/Android5.png)

6. Si vous ne disposez pas d’une invitation, vous pouvez toujours essayer le service. Pour ce faire, cliquez sur **Atteindre la version d’évaluation gratuite** lorsque vous y êtes invité.

    ![Démonstration d’alimentation invite](./media/remoteapp-clients/Android6.png)

7. Cela vous donnera accès à un ensemble de base d’applications pour vous aider à démarrer avec RemoteApp.

    ![Démonstration d’alimentation pour Azure RemoteApp](./media/remoteapp-clients/Android7.png)

## <a name="ios"></a>e/s

Une fois que vous avez installé l’application de bureau à distance de Microsoft dans le magasin de l’application, vous pouvez le trouver dans votre liste d’application sous le **Client du Bureau à distance**.

1. Lancement de l’application vous propose à un centre de connexion vide, sauf si vous avez déjà été à l’aide de l’application. Pour vous familiariser avec Azure RemoteApp, cliquez sur le bouton d’ajouter **« « + » »** et cliquez sur **Ajouter un RemoteApp Azure**.

    ![Centre de connexion vide](./media/remoteapp-clients/IOS1.png)

2. Vous devez, pour vous connecter avec votre adresse de messagerie pour accéder au service, pour démarrer ce processus, tapez votre **adresse e-mail** et cliquez sur **Continuer**.

    ![Invite de connexion](./media/remoteapp-clients/picture1.png)

3. Suivez les instructions à l’écran pour vous connecter avec votre compte de Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez être présentées avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations que vous faites confiance et que vous cliquez sur **terminé**.

    ![Page des invitations](./media/remoteapp-clients/IOS3.png)

4. Après avoir accepté votre invitation, la liste des applications que vous avez accès, sera téléchargée sur votre ordinateur et disponible dans le centre de connexion. Cliquez sur une des applications à lancer et commencer à l’utiliser.

    ![Centre de connexion avec une alimentation](./media/remoteapp-clients/IOS4.png)

5. Si vous ne disposez pas d’une invitation, vous pouvez toujours essayer le service. Pour ce faire, cliquez sur **Atteindre la version d’évaluation gratuite** lorsque vous y êtes invité.

    ![Démonstration d’alimentation invite](./media/remoteapp-clients/IOS5.png)

6. Cela vous donnera accès à un ensemble de base d’applications pour vous aider à démarrer avec RemoteApp.

    ![Démonstration d’alimentation pour Azure RemoteApp](./media/remoteapp-clients/IOS6.png)

## <a name="mac-os-x"></a>Mac OS X

Une fois que vous avez installé l’application de bureau à distance de Microsoft dans le magasin de l’application, vous pouvez le trouver dans votre liste d’app sous **Bureau à distance de Microsoft**.

1. Lancement de l’application vous propose à un centre de connexion vide, sauf si vous avez déjà été à l’aide de l’application. Pour vous familiariser avec Azure RemoteApp, cliquez sur le bouton **Azure RemoteApp** .

    ![Centre de connexion vide](./media/remoteapp-clients/Mac1.png)

2. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service, pour démarrer ce processus, cliquez sur **Mise en route**.

    ![Invite de connexion](./media/remoteapp-clients/Mac2.png)

3. Sur la page suivante, tapez votre **adresse de courriel** et cliquez sur **Continuer**. Le signe de processus à l’aide d’Azure Active Directory commence.

    ![Première page Azure Active Directory](./media/remoteapp-clients/picture2.png)

4. Suivez les instructions à l’écran pour vous connecter avec votre compte de Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez être présentées avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations que vous faites confiance et que vous fermez la boîte de dialogue.

    ![Page des invitations](./media/remoteapp-clients/Mac4.png)

5. Après avoir accepté votre invitation, la liste des applications que vous avez accès, sera téléchargée sur votre ordinateur et disponible dans le centre de connexion. Double-cliquez sur une des applications à lancer et commencer à l’utiliser.

    ![Centre de connexion avec une alimentation](./media/remoteapp-clients/Mac5.png)

6. Si vous ne disposez pas d’une invitation, vous pouvez toujours essayer le service. Pour ce faire, cliquez sur **Atteindre la version d’évaluation gratuite** lorsque vous y êtes invité.

    ![Démonstration d’alimentation invite](./media/remoteapp-clients/Mac6.png)

7. Cela vous donnera accès à un ensemble de base d’applications pour vous aider à démarrer avec RemoteApp.

    ![Démonstration d’alimentation pour Azure RemoteApp](./media/remoteapp-clients/Mac7.png)

## <a name="windows-all-supported-versions-except-windows-phone"></a>Windows (prise en charge de toutes les versions sauf Windows Phone)

Le client lance automatiquement lorsqu’il a terminé l’installation, cependant lorsque vous avez besoin d’y accéder ultérieurement il vous pouvez trouver dans votre liste d’application sous le nom **Azure RemoteApp**.

1. ARD, lancez le client, la première page que vous consultez vous souhaite la bienvenue à Azure RemoteApp. Pour continuer, cliquez sur **Mise en route**.

    ![Page d’accueil du client Azure RemoteApp](./media/remoteapp-clients/Windows1.png)

2. La page suivante démarre le signe dans le processus de RemoteApp Azure à l’aide d’Azure Active Directory. Ce processus doit vous paraître familier si vous avez utilisé les services Microsoft dans le passé. Démarrez en tapant votre **adresse e-mail** et cliquez sur **Continuer**.

    ![Première invite Azure Active Directory](./media/remoteapp-clients/Windows2.png)

3. Suivez les instructions à l’écran pour vous connecter avec votre compte de Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez être présentées avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations que vous faites confiance et que vous cliquez sur **terminé**.

    ![Page d’invitation du client Azure RemoteApp](./media/remoteapp-clients/Windows3.png)

4. Après avoir accepté votre invitation, la liste des applications que vous avez accès, sera téléchargée sur votre ordinateur et disponible dans le centre de connexion. Double-cliquez sur une des applications à lancer et commencer à l’utiliser.

    ![Centre de connexion du client Azure RemoteApp](./media/remoteapp-clients/Windows4.png)

5. Si la personne vous a envoyé une invitation encore, ne vous inquiétez pas nous avons ce qu’il vous faut ! Vous aurez toujours accès à une collection de démonstration pour vous pouvez de tester le service.

    ![Démonstration d’alimentation pour Azure RemoteApp](./media/remoteapp-clients/Windows5.png)

## <a name="windows-phone-81"></a>Windows Phone 8.1

Une fois que vous avez installé l’application de bureau à distance de Microsoft à partir du magasin de 8.1 de Windows Phone, vous pouvez le trouver dans votre liste d’app sous **Bureau à distance**.

1. Lancement de l’application pour accéder directement à un centre de connexion vide, sauf si vous avez déjà été à l’aide de l’application. Pour vous familiariser avec Azure RemoteApp, cliquez sur l’ajouter bouton **« « + » »** en bas de l’écran.

    ![Centre de connexion vide](./media/remoteapp-clients/WinPhone1.png)

2. Ensuite, cliquez sur **Azure RemoteApp**.

    ![Ajouter une page de l’élément](./media/remoteapp-clients/WinPhone2.png)

3. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service, pour démarrer ce processus, cliquez sur **se connecter**.

    ![Invite de connexion](./media/remoteapp-clients/WinPhone3.png)

4. Sur la page suivante, tapez votre **adresse de courriel** et cliquez sur **Continuer**. Le signe de processus à l’aide d’Azure Active Directory commence.

    ![Première page Azure Active Directory](./media/remoteapp-clients/WinPhone4.png)

5. Suivez les instructions à l’écran pour vous connecter avec votre compte de Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez être présentées avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations que vous faites confiance et que vous cliquez sur **Enregistrer**.

    ![Page des invitations](./media/remoteapp-clients/WinPhone5.png)

6. Après avoir accepté votre invitation, la liste des applications que vous avez accès, sera téléchargée sur votre ordinateur et disponible dans le centre de connexion. Cliquez sur une des applications à lancer et commencer à l’utiliser.

    ![Centre de connexion avec une alimentation](./media/remoteapp-clients/WinPhone6.png)

7. Si vous ne disposez pas d’une invitation, vous pouvez toujours essayer le service. Pour ce faire, cliquez sur **Oui** lorsque vous y êtes invité.

    ![Démonstration d’alimentation invite](./media/remoteapp-clients/WinPhone7.png)

8. Cela vous donnera accès à un ensemble de base d’applications pour vous aider à démarrer avec RemoteApp.

    ![Démonstration d’alimentation pour Azure RemoteApp](./media/remoteapp-clients/WinPhone8.png)
 