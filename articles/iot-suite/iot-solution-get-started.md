<properties
    pageTitle="Exemple de MyDriving Azure IoT : démarrage rapide | Microsoft Azure"
    description="Commencez avec une application qui est une démonstration complète de l’architecture d’un système IoT à l’aide de Microsoft Azure, y compris les flux Analytique, l’apprentissage de la Machine et concentrateurs d’événement."
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>

# <a name="mydriving-iot-system-quick-start"></a>Système de MyDriving IoT : démarrage rapide

MyDriving est un système qui illustre la création et l’implémentation d’une solution [Internet des objets](iot-suite-overview.md) (IoT) classique qui rassemble TELEMETRIE de dispositifs, traite ces données dans le nuage et applique l’apprentissage fournir une réponse adaptative de l’ordinateur. La démonstration enregistre des données sur vos trajets en voiture, en utilisant des données à partir de votre téléphone portable et un adaptateur qui collecte des informations sur le système de contrôle de votre voiture. Il utilise ces données pour fournir des commentaires sur votre style de conduite par rapport à d’autres utilisateurs.

Le véritable but de MyDriving est pour vous aider lors de la création de votre propre solution IoT. Mais avant cela, nous allons vous commencer avec l’application MyDriving--en tant que membre de notre équipe d’utilisateur test. Cela vous donne une expérience de l’application et le système derrière elle en tant que consommateur, avant de vous plonger dans l’architecture. Il présente également HockeyApp, un bon moyen les distributions alpha et bêta de vos applications de gestion des utilisateurs de test.

## <a name="use-the-mobile-experience"></a>Utilisez l’expérience mobile

Vous pouvez utiliser l’application MyDriving si vous disposez d’un périphérique Windows 10, iOS ou Android.

### <a name="android-and-windows-10-mobile-installation"></a>Android et l’installation de Windows 10 Mobile

Sur votre périphérique :

1.  Autoriser les applications de développement :

    -   Android : Dans les **paramètres** > **sécurité**, autoriser les applications provenant de **sources inconnues**.

    -   Windows 10 : Dans les **paramètres** > **mises à jour de** > **Pour les développeurs**, définir le **mode développeur**.

2.  Rejoignez notre équipe de test de version bêta par inscrire auprès de, ou la connexion à [HockeyApp](https://rink.hockeyapp.net). HockeyApp facilite la distribution des premières versions de votre application pour tester les utilisateurs.

    Si vous utilisez Windows 10, utilisez le navigateur de bord.

    Si vous êtes un participant de génération 2016, connectez-vous avec le même e-mail du compte Microsoft que vous avez enregistré pour la conférence, en utilisant un des boutons de Microsoft. Vous êtes déjà inscrit avec HockeyApp.

    ![Écran de connexion HockeyApp](./media/iot-solution-get-started/image1.png)

3.  Téléchargez et installez l’application à partir d’ici :

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [Windows 10](http://rink.io/spMyDrivingUWP)

    Il existe deux éléments. Installer le certificat de **Personnes de confiance**. Ensuite, installez l’application.

*Les problèmes de démarrage de l’application sur Windows Mobile de 10 ?* Votre téléphone est peut-être une mise à jour, ou deux derrière. Assurez-vous que vous avez les dernières mises à jour ou installer :

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### <a name="ios-installation"></a>installation d’e/s

Si vous avez assisté Build 2016, téléchargez l’application en tant que membre de notre équipe de test sur HockeyApp :

1.  Sur votre périphérique iOS, connectez-vous à [HockeyApp](https://rink.hockeyapp.net).
    Utilisez un des boutons de connexion Microsoft, connectez-vous avec le même e-mail du compte Microsoft que vous avez enregistrée avec la conférence. (N’utilisez pas les champs e-mail et mot de passe).

    ![Écran de connexion HockeyApp](./media/iot-solution-get-started/image1.png)

2.  Dans le tableau de bord HockeyApp, sélectionnez MyDriving et téléchargez-le.

3.  Autoriser la version bêta de HockeyApp :

    une barre d’outils. Accédez à **paramètres** > **générale** > **profils et gestion des périphériques.**

    b. Approuver le certificat de **Bits Stadium GmbH** .

Si vous n’avez pas assister à 2016 de Build, vous pouvez générer et déployer l’application vous-même :

1.   Télécharger le code [à partir de GitHub].

2.   Générez et déployez à [l’aide de Xamarin].

Afficher des informations détaillées dans le [Guide de référence de MyDriving](http://aka.ms/mydrivingdocs).

## <a name="get-an-obd-adapter-optional"></a>Obtenir une carte OBD (facultatif)

Il s’agit de la partie qui en fait un véritable système de l’Internet des objets ! Vous pouvez utiliser l’application, mais c’est plus amusant avec la réalité, et ils ne sont pas chers.

Systèmes de diagnostic embarqués (OBD) est la fonctionnalité de votre voiture le garage utilise pour optimiser votre voiture et de diagnostiquer les bruits étranges et des feux d’avertissement. À moins que votre voiture est de grande antiquité, vous trouverez un socket quelque part dans la cabine, généralement derrière un rabat sous le tableau de bord. Avec le connecteur droit, vous pouvez obtenir des métriques de performances du moteur et apporter certaines modifications. Vous pouvez acheter un connecteur OBD peu coûteux à partir des emplacements habituels. Il se connecte à l’aide de Bluetooth ou Wi-Fi pour une application sur votre téléphone.

Dans ce cas, nous allons connecter votre voiture dans le nuage. La connexion directe entre le système OBD est votre numéro de téléphone, mais notre application fonctionne comme un relais. TELEMETRIE de votre voiture est envoyé directement au concentrateur MyDriving IoT, où elle est traitée pour connecter vos voyages route et évaluer votre style de conduite.

Pour connecter un dispositif OBD :

1.  Vérifiez que votre voiture a un socket OBD.

2.  Obtenir une carte OBD :

    -   Si vous utilisez un téléphone Android ou Windows, vous avez besoin d’une carte compatible Bluetooth le II OBD. Nous avons utilisé [d’Outil d’analyse des produits de la BAFX 34t5 Bluetooth OBDII].

    -   Si vous utilisez un téléphone iOS, vous avez besoin d’une carte Wi-Fi-activé l’OBD. Nous avons utilisé [ScanTool OBDLink MX Wi-Fi : Analyseur de Diagnostic/carte OBD].

3.  Suivez les instructions fournies avec votre carte système OBD pour le connecter à votre téléphone. Gardez les éléments suivants à l’esprit :

    -   Une carte Bluetooth doit être associée avec le téléphone, dans la page **paramètres** .

    -   Une carte Wi-Fi doit avoir une adresse dans la plage 192.168.xxx.xxx.

4.  Si vous avez plusieurs voitures, vous pouvez obtenir un adaptateur séparé pour chaque (maximum de trois).

Si vous n’avez pas un adaptateur OBD, l’application sera toujours envoyer des données d’emplacement et de la vitesse de GPS sur du téléphone pour le back-end et vous demande si vous souhaitez simuler un système OBD.

Vous trouverez plus d’informations sur l’utilisation de données à partir de la carte système OBD par l’application et sur les options pour la création de votre propre dispositif OBD dans la section 2.1, « Périphériques IoT » dans le [Guide de référence de MyDriving](http://aka.ms/mydrivingdocs).

## <a name="use-the-app"></a>Utilisation de l’application

Démarrer l’application. Il existe un Quickstart initiale pour vous guider dans la façon dont il fonctionne.

### <a name="track-your-trips"></a>Effectuer le suivi de vos voyages

Cliquez sur le bouton Enregistrer (grand cercle rouge en bas de l’écran) pour démarrer un voyage et cliquez de nouveau sur pour terminer.

![Illustration du bouton Enregistrer pour un voyage de suivi](./media/iot-solution-get-started/image2.png)

Chaque fois que vous démarrez un voyage, s’il n’existe aucun dispositif OBD, vous demandera si vous souhaitez utiliser le simulateur.

À la fin d’un voyage, cliquez sur le bouton Arrêter, et vous obtenez un résumé.

![Exemple d’un résumé du voyage](./media/iot-solution-get-started/image3.png)

### <a name="review-your-trips"></a>Passez en revue vos voyages

![Exemple d’un voyage en cours](./media/iot-solution-get-started/image4.png)

### <a name="review-your-profile"></a>Revoir votre profil

![Exemple de profil style de conduite](./media/iot-solution-get-started/image5.png)

## <a name="send-us-your-test-feedback"></a>Envoyez-nous vos commentaires de test

Car nous avons créé MyDriving pour vous aider à démarrer votre propre systèmes IoT, nous voulons certainement vous entendre à propos de la façon dont il fonctionne. Faites-nous savoir si :

- Vous rencontrez des difficultés ou des défis.

- Il existe un point d’extension qui rendrait plus adapté à votre scénario.

- Vous recherchez un moyen plus efficace pour accomplir certains besoins.

- Vous disposez de vos suggestions d’amélioration de MyDriving ou cette documentation.

Dans l’application MyDriving lui-même, vous pouvez utiliser le mécanisme de commentaires HockeyApp intégré : sur iOS et Android, venez donner votre téléphone un agiter ou utiliser la commande de menu **commentaires** . Une capture d’écran, est automatiquement joint afin que nous saurons vous parlez. Et s’il existe des incidents malheureux, HockeyApp collecte les journaux des incidents pour nous dire à leur sujet. Vous pouvez également donner vos commentaires via le [portail de HockeyApp].

Vous pouvez également un [problème sur le GitHub]de fichier, ou laisser un commentaire ci-dessous (en-us édition).

Nous nous réjouissons à l’audition de vous !

## <a name="next-steps"></a>Étapes suivantes

-   Découvrez le [Guide de référence de MyDriving](http://aka.ms/mydrivingdocs) pour comprendre comment nous avons conçu et construit l’ensemble du système MyDriving.

-   [Créer et déployer un système de vos](iot-solution-build-system.md) à l’aide de nos scripts Azure le Gestionnaire de ressources. Le [Guide de référence de MyDriving](http://aka.ms/mydrivingdocs) vous guide également les zones où vous allez effectuer la plupart des personnalisations.

  [à partir de GitHub]: https://github.com/Azure-Samples/MyDriving
  [à l’aide de Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [Outil d’analyse des BAFX produits 34t5 Bluetooth OBDII]: http://www.amazon.com/gp/product/B005NLQAHS
  [Analyseur de Diagnostic/carte OBD ScanTool OBDLink MX Wi-Fi :]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [Portail de HockeyApp]: https://rink.hockeyapp.org
  [problème de GitHub]: https://github.com/Azure-Samples/MyDriving/issues
