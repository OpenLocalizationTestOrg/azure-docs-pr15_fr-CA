# <a name="securing-your-iot-deployment"></a>Sécurisation de votre déploiement IoT

Cet article fournit le niveau suivant de détails pour la sécurisation de l’infrastructure de l’Internet des objets basés sur Azure IoT (IoT). Il lie aux détails d’implémentation au niveau de la configuration et le déploiement de chaque composant. Il fournit également les comparaisons et le choix entre différentes méthodes de concurrents.

Sécurisation du déploiement d’Azure IoT peut être divisé en zones de trois sécurité suivantes :

- **Dispositif de sécurité**: sécurisation du périphérique IoT pendant qu’il est déployé dans la nature.
- **Sécurité de la connexion**: toutes les données transmises entre le IoT périphérique et IoT concentrateur est confidentielle et inviolable.
- **Sécurité du nuage**: fournir un moyen pour sécuriser les données pendant qu’il parcourt et est stocké dans le nuage.

![Trois zones de sécurité][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Mise en service du dispositif de sécurité et authentification

La Suite de IoT Azure sécurise les périphériques IoT par le des deux méthodes suivantes :

- En fournissant une clé d’identité unique (jetons de sécurité) pour chaque périphérique, ce qui peut être utilisé par le périphérique pour communiquer avec le IoT Hub.

- À l’aide d’un [certificat X.509] sur périphérique[ lnk-x509] et la clé privée comme un moyen d’authentifier le périphérique sur le concentrateur IoT. Cette méthode d’authentification permet de s’assurer que la clé privée sur le périphérique ne connaît pas à l’extérieur de l’appareil à tout moment, fournissant un niveau de sécurité supérieur.

La méthode de jeton de sécurité fournit une authentification pour chaque appel effectué par le périphérique pour le concentrateur de IoT en associant la clé symétrique à chaque appel. Authentification basée sur les X.509 permet l’authentification d’un dispositif IoT au niveau de la couche physique dans le cadre de l’établissement de la connexion TLS. La méthode basée sur des jetons de sécurité peut être utilisée sans l’authentification X.509, qui est un modèle moins sécurisé. Le choix entre les deux méthodes dépend principalement la sécurisation du périphérique d’authentification doit être et la disponibilité de stockage sécurisé sur le périphérique (pour stocker la clé privée en toute sécurité).

## <a name="iot-hub-security-tokens"></a>Jetons de sécurité IoT concentrateur

IoT Hub utilise les jetons de sécurité pour authentifier les périphériques et les services afin d’éviter l’envoi de clés sur le réseau. En outre, les jetons de sécurité sont limités dans la portée et de la période de validité. Azure SDK de concentrateur IoT générer automatiquement des jetons sans configuration spéciale. Certains scénarios, toutefois, exigent de l’utilisateur de générer et d’utiliser directement les jetons de sécurité. Il s’agit notamment de l’utilisation directe des surfaces MQTT, AMQP ou HTTP, ou la mise en oeuvre du modèle service de jeton.

Vous trouverez plus de détails sur la structure du jeton de sécurité et de son utilisation dans les articles suivants :

-   [Structure de jeton de sécurité][lnk-security-tokens]
-   [À l’aide des jetons d’associations de sécurité en tant que périphérique][lnk-sas-tokens]

Chaque concentrateur IoT a un [Registre d’identité de périphérique] [ lnk-identity-registry] qui peut être utilisé pour créer des ressources de périphérique dans le service, comme une file d’attente contient les messages du nuage vers le périphérique en vol et pour permettre l’accès aux points de terminaison orientés vers le périphérique. Le Registre d’identité IoT Hub offre un stockage sécurisé des identités des appareils et des clés de sécurité pour une solution. Individu ou des groupes d’identités de périphérique peuvent être ajoutés à une liste ou une liste d’interdiction, permettant le contrôle total sur l’accès au périphérique. Les articles suivants fournissent des informations supplémentaires sur la structure du Registre d’identité de périphérique et prise en charge des opérations.

[Concentrateur de IoT prend en charge les protocoles tels que HTTP, AMQP et MQTT][lnk-protocols]. Chacun de ces protocoles utilisent des jetons de sécurité à partir du périphérique IoT IoT concentrateur différemment :

- AMQP : La sécurité SASL simple et basée sur les revendications de AMQP ({policyName}@sas.root.{iothubName} pour les jetons d’au niveau du concentrateur ; {deviceId} en cas de jetons de dispositif de portée).

- MQTT : Connecter des utilisations de paquet {deviceId} comme {ClientId}, {IoThubhostname} / {ID de périphérique} dans le champ **nom d’utilisateur** et un jeton SAS dans le champ **mot de passe** .

- HTTP : Jeton valide est dans l’en-tête de la demande d’autorisation.

Registre d’identité IoT concentrateur périphérique peut servir à configurer les informations d’identification de sécurité de périphérique et de contrôle d’accès. Toutefois, si une solution IoT a déjà consenti un investissement significatif dans un [mécanisme du Registre et d’authentification d’identité périphérique personnalisé][lnk-custom-auth], il peut être intégré dans une infrastructure existante avec IoT Hub en créant un service de jetons.

### <a name="x509-certificate-based-device-authentication"></a>Authentification du périphérique basé sur un certificat X.509

L’utilisation d’un [certificat X.509 périphérique] [ lnk-use-x509] et sa paire de clés publique et privée associée permet une authentification supplémentaire au niveau de la couche physique. La clé privée est stockée en toute sécurité dans le périphérique et n’est pas détectable à l’extérieur de l’appareil. Le certificat X.509 contient des informations sur le périphérique, telles que des ID de périphérique et d’autres détails d’organisation. Une signature de certificat est générée à l’aide de la clé privée.

Flux de provisionnement de dispositif de haut niveau :

- Associer un identificateur à un périphérique physique – l’identité du périphérique et/ou de certificat X.509 associé au périphérique lors de la fabrication ou la mise en service de périphérique.

- Créer une entrée d’identité correspondante dans IoT concentrateur – identité de périphérique et les informations de périphérique associé dans le Registre de périphérique du concentrateur IoT.

- Stocker en toute sécurité l’empreinte de certificat X.509 dans le Registre de périphérique IoT concentrateur.

### <a name="root-certificate-on-device"></a>Certificat racine sur le périphérique

Lors de l’établissement d’une connexion TLS sécurisée avec IoT concentrateur, le périphérique IoT authentifie concentrateur IoT à l’aide d’un certificat racine qui fait partie du Kit de développement logiciel de périphérique. Pour le Kit de développement logiciel du client C le certificat se trouve dans le dossier «\\c\\certificats » sous la racine de la mis en pension. Bien que ces certificats racines sont durables, ils toujours peuvent expirer ou être révoqués. S’il n’existe aucun moyen de mise à jour du certificat sur le périphérique, le périphérique n’est peut-être pas capable de se connecter par la suite le concentrateur IoT (ou tout autre service de cloud). Avoir un moyen de mettre à jour le certificat racine, une fois le périphérique IoT déployé réduire de manière efficace ce risque.

## <a name="securing-the-connection"></a>Sécurisation de la connexion

Connexion Internet entre le dispositif de IoT et IoT concentrateur est sécurisée à l’aide de la norme de sécurité TLS (Transport Layer). IoT Azure prend en charge [TLS 1.2][lnk-tls12], TLS 1.1 et TLS 1.0, dans cet ordre. Prise en charge de TLS 1.0 est fournie pour la compatibilité ascendante. Il est recommandé d’utiliser TLS 1.2 dans la mesure où il fournit le plus de sécurité.

Azure IoT Suite prend en charge les Suites de chiffrement suivants, dans cet ordre.

| Suite de chiffrement | Longueur |
|--------------|--------|
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 secp384r1 ECDH (0xc028) (ég. 7680 bits RSA) FS | 256    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 secp256r1 ECDH (0xc027) (ég. FS de 3072 bits RSA) | 128    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) secp384r1 ECDH (ég. 7680 bits RSA) FS           | 256    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) secp256r1 ECDH (ég. FS de 3072 bits RSA)           | 128    |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9C.) | 128    |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) | 256    |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35)    | 256    |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f)    | 128    |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa)    | 112    |

## <a name="securing-the-cloud"></a>Sécurisation du cloud

Concentrateur de IoT Azure permet la définition des [stratégies de contrôle d’accès] [ lnk-protocols] pour chaque clé de sécurité. Il utilise l’ensemble des autorisations suivantes pour accorder l’accès à chacun des points de terminaison du concentrateur IoT. Autorisations de limitent l’accès à un concentrateur IoT basé sur la fonctionnalité.

- **RegistryRead**. Accorde l’accès en lecture à l’identité de périphérique du Registre. Pour plus d’informations, consultez le [Registre d’identité de périphérique][lnk-identity-registry].

- **RegistryReadWrite**. Lire des subventions et l’accès en écriture sur le Registre d’identité de périphérique. Pour plus d’informations, consultez le [Registre d’identité de périphérique][lnk-identity-registry].

- **ServiceConnect**. Accorde l’accès à la communication et surveillance des points de terminaison orientés vers le service en nuage. Par exemple, il accorde l’autorisation pour les services en nuage de back-end pour recevoir des messages de périphérique-nuage, envoyer des messages du nuage vers le périphérique et récupérer les accusés de réception de livraison correspondante.

- **DeviceConnect**. Accorde l’accès aux points de terminaison de communication orientés vers le périphérique. Par exemple, il accorde l’autorisation d’envoyer des messages de périphérique-nuage et de recevoir des messages de nuage vers le périphérique. Cette autorisation est utilisée par les périphériques.

Il existe deux méthodes pour obtenir les autorisations de **DeviceConnect** avec IoT Hub avec des [jetons de sécurité][lnk-sas-tokens]: à l’aide d’une clé d’identité de périphérique ou une clé de stratégie d’accès partagé. En outre, il est important de noter que toutes les fonctionnalités accessibles à partir de périphériques sont exposée par la conception de points de terminaison avec le préfixe `/devices/{deviceId}`.

[Composants de service ne peuvent générer que des jetons de sécurité] [ lnk-service-tokens] à l’aide de partage des stratégies d’accès accorder les autorisations appropriées.

Concentrateur de IoT Azure et d’autres services qui peuvent faire partie de la solution permettent la gestion des utilisateurs à l’aide d’Active Directory Azure.

Données ingérées par Azure IoT concentrateur peuvent être consommées par une variété de services tels que Azure flux Analytique et stockage blob Azure. Ces services permettent l’accès à l’administration. Découvrez ces services et les options disponibles ci-dessous :

- [Azure DocumentDB][lnk-docdb]: un service de base de données évolutive et entièrement indexés pour fournir des données semi-structurées qui gère les métadonnées pour les périphériques vous mettre en service, telles que les attributs, la configuration et les propriétés de sécurité. DocumentDB offre un traitement hautes performances et haut débit, indépendant du schéma d’indexation des données et une interface de requête SQL riche.

- [Analytique de flux Azure][lnk-asa]: dans le cloud qui vous permet de rapidement développer et déployer une solution économique analytique pour découvrir des informations en temps réel à partir de périphériques, les capteurs, l’infrastructure et les applications de traitement de flux en temps réel. Les données de ce service entièrement géré peuvent s’adapter à n’importe quel volume tout en atteignant la résilience, une faible latence et un débit élevé.

- [Les Services d’application Azure][lnk-appservices]: une plate-forme de nuage pour construire des web puissante et des applications mobiles qui se connectent à des données de n’importe où. dans le nuage ou de locaux. Générer l’engagement des applications mobiles pour iOS, Android et Windows. Intégration avec le logiciel en tant que Service (SaaS) et des applications d’entreprise avec l’emploi d’une connectivité à des dizaines de services en nuage et les applications d’entreprise. Le code dans votre langage préféré et l’IDE (.NET, NodeJS, PHP, Python ou Java) pour générer des applications web et des API plus rapidement que jamais.

- [Logique d’applications][lnk-logicapps]: fonctionnalité de l’applications de logique de Service d’application Azure permet d’intégrer votre solution IoT à vos systèmes existants de secteur d’activité et d’automatiser les processus de flux de travail. Logique d’applications permet aux développeurs de concevoir des workflows avec démarrer à partir d’un déclencheur et exécuter une série d’étapes, les règles et les actions qui utilisent des connecteurs puissants pour s’intégrer à vos processus d’entreprise. Logique d’applications offre une connectivité de-l’emploi pour une vaste écosystème de SaaS, basée sur le nuage et des applications sur site.

- [Stockage des objets blob Azure][lnk-blob]: stockage en nuage fiable et économique pour les données que vos périphériques envoyer vers le nuage.

## <a name="conclusion"></a>Conclusion

Cet article fournit des détails du niveau pour la conception et le déploiement d’une infrastructure IoT à l’aide d’Azure IoT vue d’ensemble de la mise en oeuvre. Configuration de chaque composant pour être sécurisé est la clé dans la sécurisation de l’infrastructure globale de IoT. Les choix de conception disponibles dans Azure IoT fournissent un certain niveau de souplesse et de choix ; Cependant, chaque choix peuvent avoir des conséquences sur la sécurité. Il est recommandé que chacun de ces choix évaluée par une évaluation des risques/coût.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
