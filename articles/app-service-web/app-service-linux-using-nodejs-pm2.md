<properties 
    pageTitle="À l’aide de la Configuration de PM2 pour NodeJS dans les applications Web sous Linux | Microsoft Azure" 
    description="À l’aide de la Configuration de PM2 pour NodeJS dans les applications Web sous Linux" 
    keywords="service d’application Azure, application web, nodejs, pm2, linux, systèmes d’exploitation"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>À l’aide de la Configuration de PM2 pour Node.js dans les applications Web sous Linux

Si vous affectez la pile Node.js pour les applications Web sous Linux, vous obtenez la possibilité de définir un fichier de démarrage Node.js, comme illustré dans l’image ci-dessous.

![][1]

Vous pouvez l’utiliser pour une ou l’autre

-   Spécifier le script de démarrage pour votre application Node.js (par exemple : /bin/server.js)
-   Spécifier le fichier de configuration à utiliser pour votre application Node.js PM2 (par exemple : /foo/process.json)

 >[AZURE.NOTE] Si vous souhaitez que votre processus de nœud pour redémarrer automatiquement lorsque certains fichiers sont modifiés, vous devez utiliser la configuration de PM2. Dans le cas contraire, votre application ne redémarre pas lorsqu’il reçoit des notifications de modification à partir des choses telles que le déploiement en continu lors de la modification de votre code d’application.

Vous pouvez vérifier le [fichier documentation du processus](http://pm2.keymetrics.io/docs/usage/application-declaration/) de Node.js pour toutes les options, mais ci-dessous êtes un exemple de ce que vous utilisez comme votre fichier process.json

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Points importants à noter dans cette configuration sont 

-   La propriété « script » spécifie le script de démarrage de votre application.
-   La propriété « instances » indique le nombre d’instances de lancer le processus de nœud. Si vous exécutez votre application sur une machine virtuelle plus volumineux qui ont plusieurs cœurs, vous souhaitez optimiser vos ressources en définissant une valeur plus élevée ici.
-   Le tableau « watch » indique tous les fichiers dont la modification vous souhaitez redémarrer votre processus de nœud.
-   Pour le « watch_options », vous devez actuellement spécifier « usePolling » avec la valeur true en raison de la façon dont le contenu de votre application est monté.


## <a name="next-steps"></a>Étapes suivantes ##

* [Quel est le Service d’application sous Linux ?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png