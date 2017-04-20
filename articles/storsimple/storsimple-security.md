<properties 
   pageTitle="Sécurité des StorSimple | Microsoft Azure" 
   description="Décrit les fonctionnalités de sécurité et de confidentialité qui protègent vos données sur site et dans le cloud, dispositif et service StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple sécurité et protection des données

## <a name="overview"></a>Vue d’ensemble

La sécurité est une préoccupation majeure pour toute personne qui a adopté une nouvelle technologie, en particulier lorsque la technologie est utilisée avec des données confidentielles ou propriétaires. Lorsque vous évaluez des technologies différentes, vous devez prendre en compte des risques accrus et des coûts de la protection des données. Microsoft Azure StorSimple fournit à la fois une solution de confidentialité et de sécurité pour la protection des données, vous aidant à garantir : 

- **Confidentialité** : seules les entités autorisées peuvent afficher vos données. 
- **Intégrité** – seules les entités autorisées peut modifier ou supprimer vos données.

La solution Microsoft Azure StorSimple se compose de quatre principaux composants qui interagissent entre eux :

- **Service de gestionnaire de StorSimple hébergé dans Microsoft Azure** – le service de gestion qui vous permet de configurer et de provisionner le périphérique StorSimple.
- **Dispositif de StorSimple** – un périphérique physique installé dans votre centre de données. Tous les hôtes et les clients qui génèrent des données de se connectent au périphérique StorSimple et le périphérique gère les données et le déplace vers le nuage Azure comme il convient.
- **Clients/hôtes connectés au périphérique** – les clients de votre infrastructure de se connecter à l’appareil de StorSimple et de générer des données qui doivent être protégées.
- **Stockage en nuage** , l’emplacement dans le nuage Azure où sont stockées les données.

Les sections suivantes décrivent les fonctionnalités de sécurité StorSimple qui aident à protéger chacun de ces composants et les données y sont stockées. Il inclut également une liste de questions que vous pourriez rencontrer sur la sécurité de Microsoft Azure StorSimple et les réponses correspondantes.

## <a name="storsimple-manager-service-protection"></a>Protection du service Gestionnaire de StorSimple

Le service Gestionnaire de StorSimple est un service de gestion hébergé dans Microsoft Azure et permet de gérer tous les périphériques StorSimple votre organisation a achetées. Vous pouvez accéder au service de gestionnaire de StorSimple à l’aide de vos informations d’identification d’organisation pour vous connecter au portail classique Azure via un navigateur web. 

Accès au service Gestionnaire de StorSimple nécessite que votre organisation dispose d’un abonnement Azure qui inclut StorSimple. Votre abonnement détermine les fonctionnalités auxquelles vous pouvez accéder dans Azure portal classique. Si votre organisation ne dispose pas d’un abonnement Azure et si vous souhaitez en savoir plus sur les, voir [Inscrivez-vous pour Azure sous la forme d’une organisation](../active-directory/sign-up-organization.md). 

Étant donné que le service Gestionnaire de StorSimple est hébergé dans Azure, il est protégé par les fonctionnalités de sécurité Azure. Pour plus d’informations sur les fonctionnalités de sécurité fournies par Microsoft Azure, accédez au [Centre de confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Protection d’un dispositif StorSimple

Le StorSimple de périphérique est un périphérique de stockage sur site hybride qui contient des lecteurs de mémoire Flash (SSD) et des disques durs (HDD), ainsi que les contrôleurs redondants et des fonctions de basculement automatique. Les contrôleurs de gestion du stockage, la hiérarchisation, plaçant actuellement utilisées (ou active) des données sur le stockage local (dans les StorSimple dispositif ou sur site des serveurs), tout en déplaçant moins de données fréquemment utilisés vers le nuage.

Uniquement autorisé StorSimple périphériques sont autorisés à joindre le service Gestionnaire de StorSimple que vous avez créé votre abonnement Azure. Pour autoriser un périphérique, vous devez l’inscrire avec le service Gestionnaire de StorSimple par la fourniture de la clé de l’enregistrement du service. La clé d’inscription de service est une clé aléatoire de 128 bits générée dans Azure portal classique. 

![Clé d’enregistrement de service](./media/storsimple-security/ServiceRegistrationKey.png)

Pour savoir comment obtenir une clé de service d’enregistrement, accédez à [étape 2 : obtenir la clé d’inscription du service](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

La clé d’inscription de service est une clé qui contient plus de 100 caractères. Vous pouvez copier la clé et l’enregistrer dans un fichier texte dans un emplacement sécurisé de sorte que vous pouvez l’utiliser pour autoriser les périphériques supplémentaires si nécessaire. Si la clé d’inscription de service est perdue après avoir enregistré votre premier périphérique, vous pouvez générer une nouvelle clé à partir du service Gestionnaire de StorSimple. Cela n’affecte pas le fonctionnement des périphériques existants. 

Après avoir enregistré un périphérique, il utilise des jetons pour communiquer avec Microsoft Azure. La clé d’inscription de service n’est pas utilisée après l’enregistrement du périphérique.

> [AZURE.NOTE] Nous vous recommandons de régénérer la clé d’inscription de service après chaque utilisation.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Protéger votre solution StorSimple par mot de passe

Un aspect important de la sécurité informatique, qui sont largement utilisées dans la solution de StorSimple afin de garantir que vos données sont accessibles aux utilisateurs autorisés uniquement. StorSimple vous permet de configurer les mots de passe suivants :

- Mot de passe administrateur de périphérique StorSimple
- Challenge Handshake authentification Protocol (CHAP) initiateur et cible les mots de passe
- Gestionnaire de snapshots de StorSimple de mot de passe

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell pour StorSimple et le mot de passe administrateur de périphérique StorSimple

Windows PowerShell pour StorSimple est une interface de ligne de commande que vous pouvez utiliser pour gérer le périphérique StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d’inscrire votre appareil, configurer l’interface réseau sur votre périphérique, installer certains types de mises à jour, résoudre les problèmes de votre périphérique en accédant à la prise en charge session et modifier l’état du périphérique. Vous pouvez accéder à Windows PowerShell pour StorSimple en vous connectant à la console série sur l’appareil ou à l’aide de communication à distance de Windows PowerShell.

PowerShell distant peut être effectuée via le protocole HTTPS ou HTTP. Si la gestion à distance via le protocole HTTPS est activée, vous devrez télécharger le certificat de gestion à distance à partir du périphérique et l’installer sur le client distant. Pour plus d’informations sur l’accès distant de PowerShell, accédez à [se connecter à distance à votre périphérique de StorSimple](storsimple-remote-connect.md).

Après l’utilisation de Windows PowerShell pour le StorSimple pour se connecter au périphérique, vous devez fournir le mot de passe administrateur périphérique pour se connecter au périphérique.

![Mot de passe administrateur](./media/storsimple-security/DeviceAdminPW.png)

Gardez les recommandations suivantes à l’esprit :

- Gestion à distance est désactivée par défaut. Vous pouvez utiliser le service Gestionnaire de StorSimple pour l’activer. Pour des raisons de sécurité, l’accès à distance doit être activé uniquement au cours de la période de temps qu’il est réellement nécessaire.
- Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs d’accès distant afin qu’ils ne se produisent pas une perte de connectivité inattendu.
- Le service Gestionnaire de StorSimple ne peut pas récupérer les mots de passe existants : il peut réinitialiser uniquement. Nous vous conseillons de stocker tous les mots de passe dans un endroit sûr afin que vous n’êtes pas obligé de réinitialiser un mot de passe, si elle est oublié. Si vous n’avez pas besoin de réinitialiser un mot de passe, veillez à informer tous les utilisateurs avant de vous le réinitialiser. 

Vous pouvez accéder à l’interface de Windows PowerShell en utilisant une connexion série sur le périphérique. Vous pouvez également y accéder à distance à l’aide de HTTP ou HTTPS, qui offrent une sécurité supplémentaire. Le protocole HTTPS assure un niveau de sécurité supérieur à une série ou une connexion HTTP. Toutefois, pour utiliser le protocole HTTPS, vous devez tout d’abord installer un certificat sur l’ordinateur client qui va accéder au périphérique. Vous pouvez télécharger le certificat d’accès à distance à partir de la page de configuration de périphérique dans le service Gestionnaire de StorSimple. Si le certificat pour l’accès distant est perdu, vous devez télécharger un nouveau certificat et propager à tous les clients qui sont autorisés à utiliser la gestion à distance.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Challenge Handshake authentification Protocol (CHAP) initiateur et cible les mots de passe

CHAP est un schéma d’authentification utilisé par le périphérique StorSimple pour valider l’identité des clients distants. La vérification est basée sur un mot de passe partagé. CHAP peut être unidirectionnelle (unidirectionnelle) ou mutuelle (bidirectionnelle). Avec l’authentification CHAP unidirectionnelle, la cible (le périphérique StorSimple) authentifie l’initiateur (hôte). Protocole CHAP mutuel ou inverse requiert la cible authentifier l’initiateur et puis l’initiateur authentifie la cible. Votre StorSimple peut être configuré pour utiliser les deux méthodes.

Être conscient des éléments suivants lorsque vous configurez le protocole CHAP :

- Le nom d’utilisateur CHAP doit contenir moins de 233 caractères.
- Le mot de passe CHAP doit être comprise entre 12 et 16 caractères. Essayez d’utiliser un nom d’utilisateur plus long ou mot de passe entraîne un échec d’authentification sur l’hôte Windows.
- Vous ne pouvez pas utiliser le même mot de passe pour l’initiateur CHAP et CHAP cible.
- Après avoir défini le mot de passe, il peut être modifié, mais il ne peut pas être récupéré. Si le mot de passe, veillez à informer tous les utilisateurs d’accès distant afin qu’ils peuvent se connecter à l’appareil StorSimple.

Pour plus d’informations sur CHAP et comment le configurer pour votre solution StorSimple, passez à [Configurer le protocole CHAP pour votre périphérique StorSimple](storsimple-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Gestionnaire de snapshots de StorSimple de mot de passe

StorSimple Gestionnaire de snapshots est un composant logiciel enfichable Microsoft Management Console (MMC) qui utilise des groupes de volumes et le Service cliché instantané de Volume Windows pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser StorSimple Gestionnaire de snapshots pour créer des clones et des plannings de sauvegarde ou de restauration des volumes.

Lorsque vous configurez un périphérique pour utiliser le Gestionnaire de snapshots de StorSimple, vous devez fournir le mot de passe StorSimple Gestionnaire de snapshots. Ce mot de passe est tout d’abord défini dans Windows PowerShell pour StorSimple lors de l’inscription. Le mot de passe peut également être définie et modifiée à partir du service Gestionnaire de StorSimple. Ce mot de passe authentifie le périphérique avec le Gestionnaire de capture instantanée StorSimple.

![Gestionnaire de snapshots de StorSimple de mot de passe](./media/storsimple-security/SnapshotMgrPassword.png)

Le mot de passe du Gestionnaire de snapshots de StorSimple doit être de 14 à 15 caractères et doit contenir 3 ou plus d’une combinaison de caractères en majuscules, minuscules, numériques et spéciaux. Après avoir défini le mot de passe du Gestionnaire de snapshots de StorSimple, il peut être modifié, mais il ne peut pas être récupéré. Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs distants.

Pour plus d’informations sur le Gestionnaire de snapshots StorSimple, accédez à [qu’est le Gestionnaire de snapshots StorSimple ?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Meilleures pratiques de mot de passe

Nous vous recommandons d’utiliser les instructions suivantes pour vous assurer que les mots de passe StorSimple sont solides et bien protégé :

- Modifier votre mot de passe tous les trois mois. Modifier le mot de passe est appliquée une fois par an.
- Utilisez des mots de passe forts. Pour plus d’informations, accédez à [créer des mots de passe sûrs et les protéger](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
- Utilisez toujours des mots de passe différents pour les mécanismes d’accès différents ; chacun des mots de passe que vous spécifiez doit être unique.
- Ne pas partager les mots de passe avec quiconque n’est pas autorisé à accéder au périphérique de StorSimple.
- Ne pas parler à un mot de passe avant les autres ou indice sur le format de mot de passe.
- Si vous pensez qu’un compte ou un mot de passe a été compromis, signaler l’incident à votre service de sécurité informatique.
- Traiter tous les mots de passe comme des informations sensibles et confidentielles. 

## <a name="storsimple-data-protection"></a>Protection des données StorSimple

Cette section décrit les fonctionnalités de sécurité de StorSimple permettant de protéger les données en transit et les données stockées.

Comme décrit dans d’autres sections, les mots de passe sont utilisés pour autoriser et authentifier les utilisateurs avant qu’ils peuvent accéder à votre solution de StorSimple. Une autre considération de sécurité est protégeant les données contre les utilisateurs non autorisés lors de leur transfert entre les systèmes de stockage et pendant qu’il est stocké. Les sections suivantes décrivent les fonctionnalités de protection de données fournies avec StorSimple.

> [AZURE.NOTE] Déduplication fournit une protection supplémentaire pour les données stockées sur le périphérique StorSimple et dans le stockage Azure de Microsoft. Lorsque données sont dédupliquées, les objets de données sont stockées séparément à partir des métadonnées utilisées pour mapper et d’y accéder : aucun contexte au niveau du stockage disponible pour reconstruire les données en fonction de la structure d’un volume, un système de fichier ou nom de fichier.

## <a name="protect-data-flowing-through-the-service"></a>Protéger les données transitant par le service

L’objectif principal du service Gestionnaire de StorSimple est de gérer et de configurer le périphérique StorSimple. Le service Gestionnaire de StorSimple s’exécute dans Microsoft Azure. Le portail classique Azure vous permet d’entrer les données de configuration de périphérique, et Microsoft Azure utilise ensuite le service Gestionnaire de StorSimple pour envoyer les données vers le périphérique. StorSimple utilise un système de paires de clés asymétriques permettant de s’assurer qu’un compromis du service Azure n’implique pas compromettre des informations stockées. 

![Cryptage des données de vol](./media/storsimple-security/DataEncryption.png)

Le système de clés asymétrique permet de protéger les données qui transitent via le service comme suit :

1. Un certificat de cryptage de données qui utilise une clé publique et privée asymétrique paire est généré sur le périphérique et est utilisé pour protéger les données. Les clés sont générées lorsque le premier périphérique est enregistré. 
2. Les clés de certificat de cryptage de données sont exportées dans un fichier d’échange d’informations personnelles (.pfx) qui est protégé par la clé de cryptage de données de service, qui est une clé forte de 128 bits qui est générée de manière aléatoire par le premier périphérique lors de l’inscription.
3. La clé publique du certificat est effectuée en toute sécurité disponible pour le service de gestionnaire de StorSimple, et la clé privée reste avec le périphérique.
4. Entrant du service de données sont cryptées à l’aide de public clé et décrypté à l’aide de la clé privée stockée sur l’appareil, s’assurer que le service Azure ne peut pas décrypter les données qui circulent sur le périphérique.

La clé de cryptage de données service est générée sur le premier périphérique enregistré auprès du service. Tous les périphériques suivants qui sont enregistrés auprès du service doivent utiliser la même clé de cryptage de données de service. 

> [AZURE.IMPORTANT] 
> 
> Il est très important de faire une copie de la clé de cryptage de données de service et l’enregistrer dans un emplacement sécurisé. Une copie de la clé de cryptage de données de service doit être stockée de telle sorte qu’il est accessible par une personne autorisée et peut être facilement communiquée à l’administrateur de périphérique.
>
> Si la clé de cryptage de données de service est perdue, une personne du support technique Microsoft peut vous aider à récupérer autant que vous avez au moins un périphérique dans un état en ligne. Nous vous conseillons de modifier la clé de cryptage de données de service après leur récupération. Pour obtenir des instructions, consultez [Modifier la clé de cryptage de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Vous pouvez modifier la clé de cryptage de données de service et le certificat de cryptage de données correspondant en sélectionnant l’option **Modifier la clé de cryptage de données de service** sur le tableau de bord de service. Pour vous assurer que la sécurité des données n’est pas compromise, vous devez utiliser un périphérique physique de la StorSimple pour modifier la clé de cryptage de données de service. Modification des clés de cryptage requiert que tous les périphériques mis à jour avec la nouvelle clé. Par conséquent, nous vous conseillons de modifier la clé lorsque tous les périphériques sont en ligne. Si les périphériques sont en mode hors connexion, leurs clés peuvent être modifiées à un autre moment. Les périphériques avec des clés obsolètes sera toujours en mesure d’exécuter des sauvegardes, mais ils ne sera pas en mesure de restaurer les données jusqu'à ce que la clé est mis à jour. Pour plus d’informations, accédez à [utiliser le tableau de bord de service Gestionnaire de StorSimple](storsimple-service-dashboard.md).

Le certificat de cryptage de données et de la clé de cryptage de données de service n’expirent pas. Toutefois, nous recommandons que vous modifiez la clé de cryptage de données de service par an pour empêcher la clé compromise.

## <a name="protect-data-at-rest"></a>Protéger les données au repos

Le périphérique StorSimple gère les données en les stockant dans des niveaux localement et dans le cloud, en fonction de la fréquence d’utilisation. Tous les ordinateurs hôtes qui sont connectés au périphérique d’envoient des données sur le périphérique, ce qui déplace ensuite les données dans le nuage, selon le cas. Données sont transférées à partir du périphérique vers le nuage en toute sécurité sur Internet. Chaque périphérique possède une cible iSCSI qui couvre tous les volumes partagés sur ce périphérique. Toutes les données sont cryptées avant d’être envoyé au stockage en nuage. 

![Clé de cryptage de stockage cloud](./media/storsimple-security/CloudStorageEncryption.png)

Pour aider à garantir la sécurité et l’intégrité des données déplacées vers le nuage, StorSimple vous permet de définir des clés de cryptage de stockage cloud comme suit :

- Vous spécifiez la clé de cryptage de stockage cloud lorsque vous créez un conteneur de volume. La clé ne peut pas être modifiée ou ajoutée ultérieurement. 
- Tous les volumes dans un conteneur de volume partagent la même clé de cryptage. Si vous souhaitez une autre forme de cryptage pour un volume spécifique, nous vous conseillons de créer un nouveau conteneur de volume pour héberger ce volume.
- Lorsque vous entrez la clé de cryptage de stockage cloud dans le service Gestionnaire de StorSimple, la clé est cryptée à l’aide de la partie publique de la clé de cryptage de données de service et envoyée au périphérique.
- La clé de cryptage de stockage cloud n’est pas stockée dans le service et est uniquement connue de l’appareil.
- Spécification d’une clé de cryptage de stockage cloud est facultative. Vous pouvez envoyer des données qui ont été cryptées à l’hôte et le périphérique.

### <a name="additional-security-best-practices"></a>Meilleures pratiques de sécurité supplémentaires

- Fractionner le trafic : isoler votre SAN iSCSI du trafic d’utilisateur sur un réseau local d’entreprise par le déploiement d’un réseau totalement séparé et à l’aide de réseaux locaux virtuels où isolation physique n’est pas une option. Un réseau dédié pour le stockage iSCSI garantit la sécurité et les performances de vos données critiques. Mélange de trafic de stockage et de l’utilisateur sur un réseau local d’entreprise n’est pas recommandé et peut augmenter la latence et provoquer des pannes du réseau.

- Pour une sécurité réseau côté hôte, utilisez les interfaces réseau qui prennent en charge le moteur de déchargement TCP/IP (TOE). TOE réduit la charge de l’UC par traitement TCP sur la carte réseau.

## <a name="protect-data-via-storage-accounts"></a>Protéger des données via les comptes de stockage

Chaque abonnement Microsoft Azure peut créer un ou plusieurs comptes de stockage. (Un compte de stockage fournit un espace de noms unique pour travailler avec des données stockées dans le nuage Azure). Accès à un compte de stockage est contrôlé par l’abonnement et clés d’accès associés à ce compte de stockage. 

Lorsque vous créez un compte de stockage, Microsoft Azure génère deux touches d’accès de stockage de 512 bits, dont est utilisé pour l’authentification lorsque le périphérique StorSimple accède au stockage compte de. Notez que seul l’un de ces clés est en cours d’utilisation. L’autre clé est gardé en réserve, ce qui permet de faire pivoter les clés périodiquement. Pour faire pivoter les clés, vous rendre la clé secondaire active et puis supprimez la clé primaire. Vous pouvez ensuite créer une nouvelle clé pour une utilisation au cours de la rotation suivante. (Pour des raisons de sécurité, de nombreux centres de données nécessitent la rotation des clés). 

Nous vous recommandons de suivre ces méthodes recommandées pour la rotation des clés :

- Vous devez rotation des clés de compte de stockage régulièrement pour vous assurer que votre compte de stockage n’est pas accessible par les utilisateurs non autorisés.
- Régulièrement, votre administrateur Azure doit modifier ou régénérer la clé primaire ou secondaire à l’aide de la section de stockage du portail classique Azure directement accéder au compte de stockage.


## <a name="protect-data-via-encryption"></a>Protéger des données via le cryptage

StorSimple utilise les algorithmes de chiffrement suivants pour protéger les données stockées dans ou qui transitent entre les composants de la solution StorSimple.

| Algorithme | Longueur de la clé | Protocoles/applications/commentaires |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | RSA PKCS 1 v1.5 est utilisé par le portail classique Azure pour crypter les données de configuration qui sont envoyées au périphérique : par exemple, stockage compte les informations d’identification, StorSimple de configuration de périphérique et les clés de cryptage de stockage en nuage. |
| AES       | 256        | AES avec CBC est utilisé pour chiffrer la partie publique de la clé de cryptage de données de service avant d’être envoyée au portail Azure classique à partir du périphérique StorSimple. Il est également utilisé par le périphérique StorSimple pour chiffrer les données avant que les données sont envoyées pour le compte de stockage cloud. |


## <a name="storsimple-virtual-device-security"></a>Sécurité du périphérique virtuel StorSimple

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Forum aux questions (FAQ)

Voici quelques questions et réponses sur la sécurité et Microsoft Azure StorSimple.

**Q:** Mon service est compromise. Quelle doit être mes prochaines étapes ?

**A:** Vous devez changer immédiatement de la clé de cryptage de données de service et les clés de compte de stockage pour le compte de stockage qui est utilisé pour les données de hiérarchisation. Pour obtenir des instructions, consultez : 

- [Modifier la clé de cryptage de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Rotation des clés des comptes de stockage](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** J’ai un nouveau périphérique StorSimple demandant la clé d’inscription du service. Comment l’extraire ?

**A:** Cette clé a été créée lorsque vous avez créé tout d’abord le service Gestionnaire de StorSimple. Lorsque vous utilisez le service Gestionnaire de StorSimple pour se connecter au périphérique, vous pouvez utiliser la page de démarrage rapide de service permet d’afficher ou de régénérer la clé d’inscription du service. Création d’un service d’inscription n’affectera pas les périphériques inscrits existants. Pour obtenir des instructions, consultez :

- [Permet d’afficher ou de régénérer la clé d’inscription de service](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** J’ai perdu ma clé de cryptage de données de service. Que dois-je faire ?

**A:** Contactez le support technique de Microsoft. Se connecter à une session de prise en charge sur votre périphérique et l’aide que vous récupérez la clé (à condition qu’au moins une unité est en ligne). Immédiatement après avoir obtenu la clé de cryptage de données de service, vous devez la modifier pour vous assurer que la nouvelle clé est constitue seulement de vous. Pour obtenir des instructions, consultez :

- [Modifier la clé de cryptage de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  J’autorisé d’un dispositif de changement de clé de cryptage de données service, mais n’a pas démarré le processus de modification de la clé. Que dois-je faire ?

**A:** Si le délai d’attente a expiré, vous devez autoriser à nouveau le périphérique pour que la modification clé de cryptage de données de service et recommencez le processus.

**Q:**  Vous avez modifié la clé de cryptage de données de service, mais je n’a pas pu mettre à jour les autres périphériques dans les 4 heures. Dois-je à nouveau de démarrer ?

**A:** Le délai de 4 heures est uniquement pour l’origine de la modification. Une fois que vous démarrez le processus de mise à jour sur le périphérique StorSimple autorisé, l’autorisation est valide jusqu'à ce que tous les périphériques sont mis à jour.

**Q:** Notre administrateur StorSimple a quitté la société. Que dois-je faire ?

**A:** Remplacez et réinitialiser les mots de passe permettant d’accéder à l’équipement StorSimple et la clé de cryptage de données de service pour vous assurer que les nouvelles informations ne sont pas connues à personnel non autorisé. Pour obtenir des instructions, consultez :

- [Le service Gestionnaire de StorSimple permet de modifier votre mot de passe storsimple](storsimple-change-passwords.md)
- [Modifier la clé de cryptage de données de service](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Configurer la sécurité CHAP pour votre périphérique StorSimple](storsimple-configure-chap.md)

**Q:** Je souhaite fournir le mot de passe du Gestionnaire de snapshots de StorSimple à un hôte qui se connecte au périphérique StorSimple, mais le mot de passe n’est pas disponible. Que puis-je faire ?

**A:** Si vous avez oublié le mot de passe, vous devez créer un nouveau. Ensuite, n’oubliez pas d’informer tous les utilisateurs existants que le mot de passe a été modifié et qu’ils doivent mettre à jour leurs clients pour utiliser le nouveau mot de passe. Pour obtenir des instructions, consultez :

- [Modifier le mot de passe du Gestionnaire de snapshots de StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Authentifier un périphérique](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** Le certificat pour l’accès distant pour le Windows PowerShell pour StorSimple a été modifié sur le périphérique. Comment mettre à jour mes clients d’accès distant ?

**A:** Vous pouvez télécharger le nouveau certificat du service Gestionnaire de StorSimple et donnez-le à installer dans le magasin de certificats de vos clients d’accès distant. Pour obtenir des instructions, consultez :

- [Applet de commande de certificat d’importation](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** Est Mes données protégé le Gestionnaire de StorSimple service est compromis ?

**A:** Les données de configuration de service sont toujours chiffrées avec votre clé publique lorsque vous l’affichez dans un navigateur web. Car le service n’a pas accès à la clé privée, il se peut que le service ne sera pas en mesure de voir toutes les données. Si le service Gestionnaire de StorSimple est compromis, n’a aucun impact, il ne sont a pas de clés stockées dans le service Gestionnaire de StorSimple.

**Q:** Si quelqu'un parvient à accéder au certificat de chiffrement de données, seront mes données compromises ?

**A:** Microsoft Azure stocke la clé de chiffrement de données du client (fichier .pfx) dans un format crypté. Étant donné que le fichier .pfx est crypté et que le service StorSimple n’a pas la clé de cryptage de données de service pour décrypter le fichier .pfx, simplement l’accès au fichier .pfx expose pas les secrets.

**Q:** Que se passe-t-il si une entité gouvernementale demande à Microsoft de mes données ?

**A:** Étant donné que toutes les données sont cryptées sur le service et la clé privée est conservé avec le périphérique, l’autorité gouvernementale doit demander au client pour les données. 

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre périphérique StorSimple](storsimple-deployment-walkthrough.md).
 
