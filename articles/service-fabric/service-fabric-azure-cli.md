<properties
   pageTitle="Interaction avec les clusters de tissu de Service à l’aide de la CLI | Microsoft Azure"
   description="L’utilisation de CLI d’Azure pour interagir avec un cluster Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>À l’aide de la CLI de Azure pour interagir avec un Service de Cluster de tissu

Vous pouvez interagir avec le cluster Service Fabric à partir d’ordinateurs Linux à l’aide de la CLI de Azure sous Linux.

La première étape est d’obtenir la dernière version de l’interface CLI à partir de la représentation git et définissez-le dans votre chemin d’accès à l’aide des commandes suivantes :

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Pour chaque commande, il prend en charge, vous pouvez taper le nom de la commande pour obtenir de l’aide pour la commande. La saisie semi-automatique est pris en charge pour les commandes. Par exemple, la commande suivante donne vous aide pour toutes les commandes de l’application. 

```sh
 azure servicefabric application 
```

Vous pouvez filtrer davantage l’aide pour une commande spécifique, comme le montre l’exemple suivant :

```sh
 azure servicefabric application  create
```

Pour activer la saisie semi-automatique dans l’interface CLI, exécutez les commandes suivantes :

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Les commandes suivantes, connectent au cluster et vous montrent les nœuds dans le cluster :

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Pour utiliser des paramètres nommés et trouver ce qu’ils sont, vous pouvez taper--aide après une commande. Par exemple :

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Lors de la connexion à un cluster comportant plusieurs ordinateur à partir d’un ordinateur **qui ne fait pas partie du cluster**, utilisez la commande suivante :

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Selon le cas, remplacez la balise PublicIPorFQDN avec le réel IP ou le nom de domaine complet. Lors de la connexion à un cluster comportant plusieurs ordinateur à partir d’un ordinateur **qui fait partie du cluster**, utilisez la commande suivante :

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Vous pouvez utiliser PowerShell ou CLI pour interagir avec votre Service de Fabric Linux Cluster créé via le portail Azure. 

**Attention :** Ces clusters ne le sont pas, par conséquent, vous ouvre votre boîte d’un en ajoutant l’adresse IP publique dans le manifeste du cluster.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>À l’aide de la CLI de Azure pour se connecter à un Service de Cluster de tissu

Les commandes CLI d’Azure suivantes décrivent comment se connecter à un cluster sécurisé. Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Si votre certificat a des autorités de certification (AC), vous devez ajouter le paramètre de l’autorité de certification---cert-chemin d’accès à l’exemple suivant : 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si vous avez plusieurs autorités de certification, utilisez une virgule comme séparateur.
 
Si votre nom commun dans le certificat ne correspond pas à l’extrémité de la connexion, vous pouvez utiliser le paramètre `--strict-ssl` ignorer la vérification, comme indiqué dans la commande suivante : 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Si vous souhaitez ignorer la vérification de l’autorité de certification, vous pouvez ajouter la--paramètre non autorisé de rejeter comme indiqué dans la commande suivante : 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Après que vous être connecté, il se peut que vous devez être en mesure d’exécuter d’autres commandes CLI pour interagir avec le cluster. 

## <a name="deploying-your-service-fabric-application"></a>Déploiement de votre application de Service Fabric

Exécutez les commandes suivantes pour copier, enregistrer et lancer l’application de service de fabric :

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Mise à niveau de votre application.

Le processus est similaire au [processus dans Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Créer, copier, enregistrer et créer votre application à partir du répertoire racine du projet. Si votre instance d’application est nommé fabric : / MySFApp et que le type est MySFApp, les commandes se présente comme suit :

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Apporter la modification à votre application et reconstruire le service modifié.  Mettre à jour le fichier de manifeste du service modifié (ServiceManifest.xml) avec les versions mises à jour pour le Service (et Code ou de configuration ou les données selon le cas). Modifier le manifeste de l’application (ApplicationManifest.xml) avec le numéro de version mis à jour pour l’application et le service modifié.  Maintenant, copiez et inscrivez votre application mise à jour à l’aide des commandes suivantes :

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Maintenant, vous pouvez démarrer la mise à niveau de l’application avec la commande suivante :

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Vous pouvez maintenant contrôler la mise à niveau de l’application à l’aide de SFX. Dans quelques minutes, l’application serait ont été mis à jour.  Vous pouvez également essayer une application mis à jour avec une erreur et vérifier la fonctionnalité de restauration automatique de fabric du service.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="copying-of-the-application-package-does-not-succeed"></a>Copie du package d’application ne fonctionne pas

Vérifiez si `openssh` est installé. Par défaut, bureau Ubuntu n’a pas installé. Installer à l’aide de la commande suivante :

```
 sudo apt-get install openssh-server openssh-client**
```

Si le problème persiste, essayez de désactiver PAM pour ssh en modifiant le fichier **sshd_config** à l’aide des commandes suivantes :

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Si le problème persiste, essayez d’augmenter le nombre de ssh sessions en exécutant les commandes suivantes :

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
À l’aide de clés de ssh authentification (et non des mots de passe) n’est pas encore prise en charge (depuis la plate-forme utilise ssh pour copier les packages), donc utiliser à la place l’authentification du mot de passe.


## <a name="next-steps"></a>Étapes suivantes

Configurer l’environnement de développement et le déploiement d’une application de Service Fabric à un cluster Linux.
