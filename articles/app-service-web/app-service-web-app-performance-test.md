<properties
   pageTitle="Tester les performances de votre application web Azure | Microsoft Azure"
   description="Exécuter des tests de performances web Azure app pour vérifier la manière dont votre application gère la charge utilisateur. Mesurer le temps de réponse et recherchez les défaillances susceptibles d’indiquer des problèmes."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/25/2016"
   ms.author="estfan; manasma; ahomer"/>

# <a name="performance-test-your-azure-web-app-under-load"></a>Test des performances de votre application web Azure sous charge

Vérifiez les performances de votre application web avant de lancer ou de déployer des mises à jour à la production. De cette façon, vous pouvez mieux évaluer si votre application est prête à être publiée. Plus il est probable que votre application peut gérer le trafic pendant les pics d’utilisation ou à votre programme de marketing ambitieux suivant la convivialité.

Au cours de la présentation publique, vous pouvez test de performances de votre application gratuitement dans le portail Azure.
Ces tests de simulent la charge utilisateur sur votre application sur une période spécifique et mesurent la réponse de votre application. Par exemple, les résultats des tests affichent la vitesse à laquelle votre application répond à un nombre spécifique d’utilisateurs. Ils indiquent également le nombre de requêtes n’a pas pu, qui peuvent indiquer des problèmes avec votre application.      

![Rechercher des problèmes de performances dans votre application web](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## <a name="before-you-start"></a>Avant de commencer

* Vous aurez besoin un [abonnement Azure](https://account.windowsazure.com/subscriptions), si vous n’en avez pas déjà. Découvrez comment vous pouvez [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).

* Vous aurez besoin d’un compte de [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) pour conserver votre historique des tests des performances. Un compte approprié sera automatiquement créé lorsque vous configurez votre test de performances. Vous pouvez également créer un nouveau compte ou utiliser un compte existant si vous êtes le propriétaire du compte. 

* Déployer votre application de test dans un environnement hors production. Avoir votre application d’utiliser un plan de Service d’application autre que le plan de production. De cette façon, vous n’affectent tous les clients existants ou de ralentir votre application en production. 

## <a name="set-up-and-run-your-performance-test"></a>Configurer et exécuter votre test de performances

0.  Connectez-vous au [portail Azure](https://portal.azure.com). Pour utiliser un compte de Services d’équipe Visual Studio que vous possédez, vous connecter en tant que le propriétaire du compte.

0.  Accédez à votre application web.

    ![Accédez à parcourir toutes les applications Web, votre application web](./media/app-service-web-app-performance-test/azure-np-web-apps.png)

0.  Accédez au **Test de performances**.

    ![Cliquez sur Outils, Test de performances](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
 
0. Maintenant, vous allez lier un compte de [Services d’équipe Visual Studio](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) pour conserver votre historique des tests des performances.

    Si vous avez un compte de Services de l’équipe à utiliser, sélectionnez ce compte. Si vous ne le faites pas, créez un nouveau compte.

    ![Sélectionnez le compte de Services d’équipe existant, ou créer un nouveau compte](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)

0.  Créer votre test de performances. Définir les détails et exécutez le test. 

Vous pouvez regarder les résultats en temps réel pendant l’exécution du test.

Par exemple, supposons que nous disposons d’une application qui a donné des bons d’achat à la vente des jours fériés de l’année dernière. Cet événement a une durée de 15 minutes avec un pic de charge de 100 clients simultanés. Nous souhaitons double le nombre de clients cette année. Nous avons également d’améliorer la satisfaction client en réduisant le temps de chargement de page de 5 secondes à 2 secondes. Ainsi, nous allons tester les performances de notre application mis à jour avec 250 utilisateurs pendant 15 minutes.

Nous allons simuler la charge sur notre application en générant des utilisateurs virtuels (clients) qui visitent notre site web en même temps. Cela permet de visualiser le nombre de requêtes échouent ou n’est répond lentement.

  ![Créer, configurer et exécuter votre test de performances](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

   *  URL de valeur par défaut de votre application web est automatiquement ajouté. 
   Vous pouvez modifier l’URL pour tester les autres pages (les requêtes GET HTTP uniquement).

   *  Pour simuler des conditions locales et de réduire la latence, sélectionnez l’emplacement le plus proche de vos utilisateurs pour la génération de charge.

  Voici le test en cours. Au cours de la première minute, notre page se charge plus lentement que nous voulons.

  ![Test de performances en cours avec des données en temps réel](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)

  Une fois le test terminé, nous apprendre que la page se charge plus rapidement après la première minute. Cela permet d’identifier où nous voulons démarrer la résolution des problèmes.

  ![Test de performances terminées affiche des résultats, y compris les demandes ayant échoué](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## <a name="test-multiple-urls"></a>Plusieurs URL du test

Vous pouvez également exécuter des tests de performances comprenant plusieurs URL qui représentent un scénario de bout en bout pour utilisateur en téléchargeant un fichier de Test de site Web Visual Studio. Certaines méthodes, vous pouvez créer un Test de site Web Visual Studio sont des fichiers :

* [Capturer le trafic à l’aide de Fiddler et exporter sous la forme d’un fichier de Test de site Web Visual Studio](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Créer un fichier de test de charge dans Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Pour télécharger et exécuter un fichier de Test de site Web Visual Studio :
 
0. Suivez les étapes ci-dessus pour ouvrir la lame de **test de performances de nouveau** .
   Dans cette blade, choisissez l’option tester à l’aide de CONFIGFURE pour ouvrir la lame de **test de la configuration à l’aide** .  

    ![Ouverture de la lame de test de charge de configurer](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)

0. Vérifiez que le TYPE de TEST est défini pour le **Test de site Web Visual Studio** et sélectionnez votre fichier d’Archive de HTTP.
    Utilisez l’icône de « dossier » pour ouvrir la boîte de dialogue de sélecteur de fichier.

    ![Téléchargement d’un fichier de Test de site Web Visual Studio URL multiples](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)

    Une fois que le fichier a été téléchargé, vous consultez la liste des URL à tester dans la section Détails de l’URL.
 
0. Spécifier la charge utilisateur et durée du test, puis cliquez sur **exécuter le test**.

    ![Sélection de la charge de l’utilisateur et la durée](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)

    Une fois le test terminé, vous consultez les résultats dans les deux volets. Le volet de gauche affiche les informations de performnace sous la forme d’une série de graphiques.

    ![Le volet de résultats de performances](./media/app-service-web-app-performance-test/multiple-01a-results.png)

    Le volet droit affiche une liste de demandes ayant échoué, avec le type d’erreur et le nombre de fois où qu'elle s’est produite.

    ![Le volet d’échecs de demande](./media/app-service-web-app-performance-test/multiple-01b-results.png)

0. Réexécuter le test en sélectionnant l’icône **relancer** en haut du volet droit.

    ![Réexécuter le test](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

##  <a name="q--a"></a>Q & r

#### <a name="q-is-there-a-limit-on-how-long-i-can-run-a-test"></a>Q : y a-t-il une limite sur la durée, je peux exécuter un test ? 

**A**: Oui, vous pouvez exécuter votre test jusqu'à une heure dans le portail Azure.

#### <a name="q-how-much-time-do-i-get-to-run-performance-tests"></a>Q : combien de temps puis-je obtenir exécuter des tests de performances ? 

**A**: après la version d’évaluation, vous recevez 20 000 utilisateurs virtuels minutes (VUMs) disponible chaque mois avec votre compte de Visual Studio Team Services. Un VUM est le nombre d’utilisateurs virtuels multipliés par le nombre de minutes dans votre test. Si vos besoins dépassent la limite disponible, vous pouvez acheter plus de temps et payez uniquement ce que vous utilisez.

#### <a name="q-where-can-i-check-how-many-vums-ive-used-so-far"></a>Q : où puis-je vérifier combien VUMs que j’ai utilisé jusqu’ici ?

**A**: vous pouvez vérifier ce montant dans le portail Azure.

![Accédez à votre compte de Services de l’équipe](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Vérifiez VUMs utilisés](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### <a name="q-what-is-the-default-option-and-are-my-existing-tests-impacted"></a>Q : quelle est l’option par défaut et mes tests existants affectés ?

**A**: l’option par défaut pour les tests de performances de charge est un test manuel - identique à celle de l’option de test avant de l’URL a été ajouté sur le portail.
Les tests existants continuent d’utiliser l’URL configurée et fonctionnent comme avant.

#### <a name="q-what-features-not-supported-in-the-visual-studio-web-test-file"></a>Q : quelles sont les fonctionnalités non prises en charge dans le fichier de Test de site Web Visual Studio ?

**A**: À présent cette fonctionnalité ne pas prend en charge les plug-ins Test Web, les sources de données et les règles d’extraction. Vous devez modifier votre fichier de Test Web pour les supprimer. Nous espérons que d’ajouter le support pour ces fonctionnalités à l’avenir des mises à jour.

#### <a name="q-does-it-support-any-other-web-test-file-formats"></a>Q : il prend-il en charge les formats de fichier Web Test ?
  
**A**: À présent uniquement Visual Studio Web Test des fichiers de format sont pris en charge.
Nous serions heureux d’entendre si vous avez besoin d’une prise en charge d’autres formats de fichier. Envoyez-nous un e-mail à [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### <a name="q-what-else-can-i-do-with-a-visual-studio-team-services-account"></a>Q : que faire avec un compte de Visual Studio Team Services ?

**A**: pour trouver votre nouveau compte, allez à ```https://{accountname}.visualstudio.com```. Partager votre code, générer, tester, effectuer le suivi du travail et logiciel de livraison – dans le nuage en utilisant n’importe quel outil ou le langage. Découvrez comment les services et les fonctionnalités de [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) aident votre équipe à collaborer plus facilement et déployer en continu.

## <a name="see-also"></a>Voir aussi

* [Exécuter des tests de performances de nuage simple](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Exécuter des tests de performances de Apache Jmeter](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Enregistrement et de lecture des tests de charge basé sur le nuage](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [Test des performances de votre application dans le nuage](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)
