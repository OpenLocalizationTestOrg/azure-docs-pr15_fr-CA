<properties
   pageTitle="Pen Testing | Microsoft Azure"
   description="L’article fournit une vue d’ensemble du test de pénétration (pentest) processus et comment effectuer des pentest par rapport à vos applications en cours d’exécution dans l’infrastructure Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Test de la plume

Un des grands avantages de l’utilisation de Microsoft Azure pour le test de l’application et de déploiement est que vous n’avez pas besoin de mettre en place une infrastructure sur site pour développer, tester et déployer vos applications. Toute l’infrastructure est pris en charge par les services de la plate-forme Microsoft Azure. Vous n’avez pas à vous soucier de la gèrent, acquisition et « montage en rack et empilage » votre propre matériel sur site.

C’est génial, mais vous devez toujours vous assurer que vous effectuez votre sécurité normale vérification au préalable. L’une des choses que vous devez faire est de pénétration tester les applications que vous déployez dans Azure.

Vous savez sans doute déjà que Microsoft effectue [des tests de pénétration de notre environnement Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Cela nous aide à améliorer notre plateforme et des guides de nos actions en termes d’amélioration des contrôles de sécurité, introduire de nouveaux contrôles de sécurité et l’amélioration de nos processus de sécurité.

Nous ne le stylet tester votre application pour vous, mais nous ne comprennent que vous le souhaitez et devez effectuer Plume tests sur vos propres applications. C’est une bonne chose, car vous pouvez améliorer la sécurité de vos applications, vous aidez à renforcer la sécurité de l’écosystème Azure ensemble.

Lorsque le stylet vous testez vos applications, il peut se présenter comme une attaque pour nous. Nous [surveiller en permanence les](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) attaques de modèles et lancera un processus de réponse aux incidents si nous devons. Il ne vous aide, et il ne nous aide si nous déclencher une réponse aux incidents en raison de votre propre échéance stylet d’obligation de diligence test.

Que faire ?

Lorsque vous êtes prêt à stylet tester vos applications Azure hébergé, vous devez nous indiquer. Une fois que nous savons que vous allez effectuer des tests, nous ne par inadvertance arrêter (par exemple, l’adresse IP que vous testiez à partir de blocage), tant que vos tests sont conformes à l’Azure stylet test des termes et conditions.
Vous pouvez effectuer des tests standard sont les suivantes :

- Tests sur vos points de terminaison pour découvrir les [principales vulnérabilités de 10 Open Web Application Security Project (OWASP avoir)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Tests de robustesse](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de vos points de terminaison
- [L’analyse des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

Un type de test que vous ne pouvez pas effectuer est tout type d’attaque [Par déni de Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Cela inclut le lancement d’une attaque de déni de service elle-même ou effectuant des tests associés qui peuvent déterminer, montrez ou simuler n’importe quel type d’attaque de déni de service.

Prêt à commencer avec stylet vous testez vos applications hébergées dans Microsoft Azure ? Si tel est le cas, puis accédez à la [Vue d’ensemble des tests de pénétration](https://security-forms.azure.com/penetration-testing/terms) de la page (et cliquez sur le bouton Créer un test de demander au bas de la page. Vous avez trouverez plus d’informations sur le stylet de tester des conditions et des liens utiles sur la façon dont vous pouvez signaler des failles de sécurité liées à Azure ou tout autre service de Microsoft.
