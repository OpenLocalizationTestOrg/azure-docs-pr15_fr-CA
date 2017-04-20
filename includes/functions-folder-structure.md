
Le code de toutes les fonctions dans une application de la fonction donnée se trouve dans un dossier racine qui contient un fichier de configuration d’hôte et un ou plusieurs sous-dossiers qui contiennent le code d’une fonction distincte, comme dans l’exemple suivant

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Le fichier *host.json* contient une configuration spécifique au moment de l’exécution et se trouve dans le dossier racine de l’application de la fonction. Pour plus d’informations sur les paramètres disponibles, consultez [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) dans le wiki de référentiel de WebJobs.Script.

Chaque fonction a un dossier qui contient un ou plusieurs fichiers de code, de la configuration de function.json et d’autres dépendances.