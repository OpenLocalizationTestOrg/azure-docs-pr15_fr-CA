<properties
    pageTitle="Méthodes conseillées pour le Service d’application Azure"
    description="Découvrez les meilleures pratiques et dépannage pour le Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Méthodes conseillées pour le Service d’application Azure

Cet article résume les méthodes conseillées pour utiliser le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Emplacement partagé
Lors de la composition d’une solution telle qu’une application web et une base de données de ressources Azure se trouvent dans différentes régions, les effets peuvent sont les suivants :

*  Latence accrue dans la communication entre les ressources
*  Frais monétaires pour les données sortantes de transfert entre-région comme indiqué sur la [page de tarification Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Emplacement partagé dans la même région est idéale pour composer une solution comme une application web et un compte de stockage ou de base de données utilisée pour stocker le contenu ou les données de ressources Azure. Lors de la création de ressources, que vous devez vous assurer qu’ils sont dans la même région d’Azure, sauf si vous avez spécifiques de l’entreprise ou que vous concevez la raison ne doit ne pas être. Vous pouvez déplacer une application de Service de l’application à la même région que votre base de données grâce à la [fonction de clonage de Service de l’application](app-service-web-app-cloning-portal.md) actuellement disponibles pour les applications de planification du Service Premium App.   

## <a name="memoryresources"></a>Lorsque les applications de consomment plus de mémoire que prévu
Si vous remarquez une application consomme plus de mémoire que prévu comme indiqué par le biais de surveillance ou des recommandations de service prendre en compte la [fonctionnalité d’application Service autoréparation](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Une des options de la fonctionnalité d’autoréparation effectue des actions personnalisées en fonction d’un seuil de mémoire. Actions s’étendent sur le spectre de notifications par courrier électronique à l’enquête via le vidage de la mémoire pour une atténuation sur place par le recyclage du processus de travail. Autoréparation peut être configuré via web.config et via une interface utilisateur conviviale, comme décrit dans cette publication de blog à pour l' [Extension du Site App Service prise en charge](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Lorsque les applications consomment plus d’UC que prévu
Lorsque vous remarquez qu'une application consomme plus d’UC que prévu ou expériences répétées UC comme indiqué par le biais de surveillance ou des recommandations de service prendre en compte l’évolution verticale ou mise à l’échelle le plan de Service de l’application. Si votre application est menés, mise à l’échelle est la seule option, alors que si votre application est sans état, mise à l’échelle de la sortie vous donnera plus de souplesse et de potentiel d’échelle supérieur. 

Pour plus d’informations sur les applications « sans état » « avec état » vs, vous pouvez visionner cette vidéo : [planification d’une Application à plusieurs niveaux d’évolutive de bout en bout sur Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Pour plus d’informations sur les options de mise à l’échelle et autoscaling App Service lire : [échelle d’une application Web dans le Service d’application Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Lorsque les ressources de socket sont épuisées
Une raison courante pour épuiser les connexions TCP sortantes est l’utilisation des bibliothèques de clients qui ne sont pas implémentés pour réutiliser les connexions TCP, ou dans le cas d’un protocole de niveau supérieur, tels que HTTP - n’étant ne pas exploité Keep-Alive. Veuillez consulter la documentation de chacune des bibliothèques référencées par les applications dans votre Plan de Service application pour s’assurer qu’ils sont configurés ou accessible dans votre code pour une réutilisation efficace des connexions sortantes. Également, suivez les instructions de la documentation bibliothèque de création approprié et de version ou de nettoyage afin d’éviter une fuite de connexions. Lors de ces enquêtes de bibliothèques client impact de progression peut-être être corrigée par l’évolution horizontale à plusieurs instances.  

## <a name="appbackup"></a>Lorsque votre application démarre Échec de sauvegarde
Les deux raisons les plus courantes pourquoi Échec de sauvegarde d’application est : les paramètres de stockage non valide et la configuration de la base de données non valide. Ces pannes se produisent généralement lorsqu’il existe des modifications aux ressources de stockage ou de base de données, ou pour savoir comment accéder à ces ressources (par exemple, informations d’identification de mise à jour pour la base de données sélectionnée dans les paramètres de sauvegarde). Généralement, les sauvegardes s’exécutent selon une planification et ont besoin d’accéder à des bases de données et de stockage (pour la sortie de la sauvegarde des fichiers) (pour la copie et la lecture de contenu à inclure dans la sauvegarde). Le résultat d’une incapacité à accéder à une de ces ressources est Échec de la sauvegarde cohérente. 

Lorsque des échecs de sauvegarde se produisent, vérifiez les résultats les plus récents pour comprendre quel type de panne se produit. Dans le cas des échecs d’accès de stockage, vérifiez et mettre à jour les paramètres stockage utilisés dans la configuration de la sauvegarde. Dans le cas des échecs d’accès de base de données, veuillez consulter et mettre à jour vos chaînes de connexion dans le cadre des paramètres de l’application ; Continuez à mettre à jour votre configuration de sauvegarde pour inclure correctement les bases de données requises. Pour plus d’informations sur la sauvegarde de l’application, consultez la documentation de [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md) .

## <a name="nodejs"></a>Lorsque les nouvelles applications Node.js sont déployées sur Azure Application Service
Azure configuration par défaut de Service d’application pour les applications de Node.js vise à mieux répondre aux besoins des applications courantes. Si la configuration de votre application Node.js peut bénéficier de réglage personnalisé pour améliorer les performances ou d’optimiser l’utilisation des ressources pour les ressources de la CPU/mémoire/réseau, vous pourriez consulter nos meilleures pratiques et les étapes de dépannage. Cette documentation décrit les paramètres iisnode, vous devrez peut-être configurer votre application de Node.js, décrit les différents scénarios ou problèmes que votre application peut être confrontés et indique comment résoudre ces problèmes : [meilleures pratiques et guide de dépannage pour les applications de nœud de Service d’application Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   


