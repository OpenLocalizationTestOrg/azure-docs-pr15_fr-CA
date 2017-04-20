<properties 
    pageTitle="L’utilisation du stockage Azure avec une Solution d’intégration continue Jenkins | Microsoft Azure" 
    description="Ce didacticiel montrent comment utiliser le service d’objet blob Azure comme un référentiel pour générer des artefacts créés par une solution d’intégration continue Jenkins." 
    services="storage" 
    documentationCenter="java" 
    authors="dineshmurthy" 
    manager="jahogg" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>À l’aide du stockage Azure avec une solution d’intégration continue de Jenkins

## <a name="overview"></a>Vue d’ensemble

Les informations suivantes montrent l’utilisation de stockage des objets Blob en tant que référentiel des artefacts de build créé par une solution d’intégration continue Jenkins (CI), ou en tant que source des fichiers téléchargeables pour être utilisée dans un processus de génération. L’un des scénarios où cela serait utile est lorsque vous écrivez votre code dans un environnement de développement agile (à l’aide de Java ou autres langages), builds sont en cours d’exécution en fonction de l’intégration continue, et vous devez un référentiel pour les artefacts de votre build, afin que vous pourriez, par exemple, les partager avec d’autres membres de l’organisation, vos clients, ou maintenir une archive. Un autre scénario est lorsque votre travail de génération lui-même requiert d’autres fichiers, par exemple, les dépendances à télécharger en tant que partie de l’entrée de construction.

Dans ce didacticiel vous utiliserez le plug-in du stockage Azure pour CI Jenkins mis à disposition par Microsoft.

## <a name="overview-of-jenkins"></a>Vue d’ensemble de Jenkins ##

Jenkins permet une intégration continue d’un projet de logiciel en permettant aux développeurs d’intégrer facilement leurs modifications du code et ont builds produits automatiquement et régulièrement, augmentant ainsi la productivité des développeurs. Les builds sont échelonnées et les artefacts de build peuvent être téléchargés dans des référentiels différents. Cette rubrique affiche l’utilisation du stockage blob Azure comme référentiel des artefacts de build. Il affiche également la façon de télécharger les dépendances depuis le stockage blob Azure.

Vous trouverez plus d’informations sur Jenkins à [Jenkins de répondre](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Avantages de l’utilisation du service d’objet Blob ##

Avantages de l’utilisation du service d’objet Blob pour héberger les artefacts de build de votre développement agile :

- Disponibilité élevée de vos artefacts de build et/ou les dépendances téléchargeables.
- Performances lorsque votre solution Jenkins CI télécharge les artefacts de votre build.
- Performances lors de vos clients et partenaires téléchargent les artefacts de votre build.
- Contrôler les stratégies d’accès utilisateur, grâce à un choix entre l’accès anonyme, l’accès de signature d’expiration-accès partagé, privé, accès, etc..

## <a name="prerequisites"></a>Conditions préalables ##

Vous aurez besoin pour utiliser le service d’objet Blob avec votre solution Jenkins CI :

- Une solution d’intégration continue de Jenkins.

    Si vous n’avez actuellement pas une solution Jenkins CI, vous pouvez exécuter une solution Jenkins CI à l’aide de la technique suivante :

    1. Sur un ordinateur prenant en charge de Java, téléchargez jenkins.war à partir de <http://jenkins-ci.org>.
    2. À une invite de commandes qui s’ouvre dans le dossier qui contient jenkins.war, exécutez la commande :

        `java -jar jenkins.war`

    3. Dans votre navigateur, ouvrez `http://localhost:8080/`. Le tableau de bord Jenkins, que vous allez utiliser pour installer et configurer le plug-in de stockage Azure s’ouvre.

        Pendant une solution Jenkins CI standard est configurée pour s’exécuter en tant que service, la guerre Jenkins en cours d’exécution sur la ligne de commande sera suffisante pour ce didacticiel.

- Un compte Azure. Vous pouvez vous inscrire pour un compte Azure à <http://www.azure.com>.

- Un compte de stockage Azure. Si vous ne disposez pas d’un compte de stockage, vous pouvez créer un à l’aide de la procédure à [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

- Vous familiariser avec la solution Jenkins CI est recommandée mais pas obligatoire, comme le contenu suivant utilise un exemple simple pour vous montrer la procédure à suivre lorsque vous utilisez le service d’objet Blob comme un référentiel pour Jenkins CI générer des artefacts.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Comment faire pour utiliser le service d’objet Blob avec Jenkins CI ##

Pour utiliser le service d’objet Blob avec Jenkins, vous devez installer le plug-in de stockage Azure, configurer le plug-in pour utiliser votre compte de stockage et ensuite créer une action post-build qui télécharge les artefacts de votre build à votre compte de stockage. Ces étapes sont décrites dans les sections suivantes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Comment faire pour installer le plug-in du stockage Azure ##

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer les Jenkins**.
2. Dans la page **Gérer les Jenkins** , cliquez sur **Gérer les plug-ins**.
3. Cliquez sur l’onglet **disponible** .
4. Dans la section **Télépartageurs de l’artefact** , vérifier le **plug-in de Microsoft Azure Storage**.
5. Cliquez sur **installer sans redémarrage** ou **Télécharger maintenant et installer après le redémarrage**.
6. Redémarrez Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Comment faire pour configurer le plug-in de stockage Azure pour utiliser votre compte de stockage ##

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer les Jenkins**.
2. Dans la page **Gérer les Jenkins** , cliquez sur **Configuration du système**.
3. Dans la section de **Configuration du compte de stockage Microsoft Azure** :
    1. Entrez votre nom de compte de stockage, vous pouvez obtenir à partir du [Portail Azure](https://portal.azure.com).
    2. Entrez votre clé de compte de stockage, également accessible à partir du [Portail Azure](https://portal.azure.com).
    3. Utilisez la valeur par défaut pour **l’URL du point de terminaison de Service Blob** si vous utilisez le nuage public Azure. Si vous utilisez un nuage Azure différent, vous pouvez utiliser le point de terminaison comme spécifié dans le [Portail Azure](https://portal.azure.com) pour votre compte de stockage. 
    4. Cliquez sur les **informations d’identification du stockage de validation** pour valider votre compte de stockage. 
    5. [Facultatif] Si vous avez des comptes de stockage supplémentaire que vous souhaitez à disposition pour votre CI Jenkins, cliquez sur **ajouter d’autres comptes de stockage**.
    6. Cliquez sur **Enregistrer** pour enregistrer vos paramètres.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Comment faire pour créer une action post-build qui télécharge les artefacts de votre build à votre compte de stockage ##

À des fins d’instruction, nous vous devez d’abord créer une tâche qui crée plusieurs fichiers et ajoutez de l’action post-build pour télécharger les fichiers à votre compte de stockage.

1. Dans le tableau de bord Jenkins, cliquez sur **Nouvel élément**.
2. Nommez le projet **MyJob**et cliquez sur **créer un projet de logiciel de la liberté de style**, puis cliquez sur **OK**.
3. Dans la section **génération** de la configuration de la tâche, cliquez sur **étape de génération ajouter** et choisissez la **commande batch de Windows de s’exécuter**.
4. Dans la **commande**, utilisez les commandes suivantes :

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Dans la section **Actions de post-build** , de la configuration de la tâche, cliquez sur **Ajouter une action post-build** , puis choisissez **télécharger des artefacts pour le stockage des objets Blob Azure**.
6. **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
7. **Nom du conteneur**, spécifiez le nom du conteneur. (Le conteneur sera créé si elle n’existe pas lorsque les artefacts de build sont téléchargés.) Vous pouvez utiliser des variables d’environnement, pour que cet exemple Entrez **${nom_du_travail}** comme le nom du conteneur.

    **Info-bulle**
    
    Sous la **commande** de la section où vous avez saisi un script pour **exécuter de Windows de commande batch** est un lien vers les variables d’environnement reconnu par Jenkins. Cliquez sur ce lien pour en savoir plus les noms des variables d’environnement et les descriptions. Notez que les variables d’environnement qui contiennent des caractères spéciaux, tels que de la variable d’environnement **BUILD_URL** ne sont pas autorisées en tant que nom du conteneur ou chemin d’accès virtuel commun.

8. Pour cet exemple, cliquez sur **Créer nouveau conteneur public par défaut** . (Si vous souhaitez utiliser un conteneur privé, vous devrez créer une signature accès partagé pour autoriser l’accès. Qui est au-delà de la portée de cette rubrique. Vous pouvez en savoir plus sur les signatures d’un accès partagé à [L’utilisation partagée accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Facultatif] Cliquez sur **Nettoyer conteneur avant de le télécharger** si vous souhaitez que le conteneur à effacer du contenu avant que les artefacts de build sont téléchargées (laissez-la désactivée si vous ne souhaitez pas nettoyer le contenu du conteneur).
10. Pour une **Liste d’artefacts à télécharger**, entrez * *texte /*.txt**.
11. Pour **des chemin d’accès virtuel commune pour les artefacts téléchargés**, pour les besoins de ce didacticiel, entrez **${BUILD\_ID} / ${BUILD\_nombre}**.
12. Cliquez sur **Enregistrer** pour enregistrer vos paramètres.
13. Dans le tableau de bord Jenkins, cliquez sur **Créer maintenant** pour exécuter **MyJob**. Examinez la sortie de la console pour l’état. Messages d’état pour le stockage Azure figureront dans la sortie de console au démarrage de l’action post-build à télécharger les artefacts de build.
14. En cas de réussite de la tâche, vous pouvez examiner les artefacts de build en ouvrant le blob publique.
    1. Connexion au [portail Azure](https://portal.azure.com).
    2. Cliquez sur **stockage**.
    3. Cliquez sur le nom du compte de stockage que vous avez utilisé pour Jenkins.
    4. Cliquez sur **les conteneurs**.
    5. Cliquez sur le conteneur nommé **myjob**, qui est la version en minuscules du nom du projet que vous avez affectée lors de la création du travail Jenkins. Les noms de conteneur et blob sont en minuscules (et sensible à la casse) dans le stockage Azure. Dans la liste des objets BLOB pour le conteneur nommé **myjob** , vous devez voir **hello.txt** et **date.txt**. Copier l’URL pour une ou l’autre de ces éléments et l’ouvrir dans votre navigateur. Vous verrez un fichier texte qui a été téléchargé sous la forme d’un objet de génération.

Seule une action post-build qui télécharge des artefacts dans le stockage blob Azure peut être créée par la tâche. Notez que la seule action post-build à télécharger les artefacts pour le stockage des objets blob Azure peut spécifier différents fichiers (y compris des caractères génériques) et les chemins d’accès aux fichiers dans **Liste des artefacts à télécharger** à l’aide d’un point-virgule comme séparateur. Par exemple, si votre build Jenkins génère des fichiers JAR et des fichiers TXT dans le dossier de **génération** de votre espace de travail et que vous souhaitez télécharger à la fois pour le stockage des objets blob Azure, utilisez les éléments suivants pour la valeur de la **Liste des artefacts à télécharger** : **build /\*.jar ; génération /\*.txt**. Vous pouvez également utiliser la syntaxe du double signe des deux-points pour spécifier un chemin d’accès à utiliser dans le nom de l’objet blob. Par exemple, si vous souhaitez que les fichiers JAR à soient téléchargées à l’aide des **fichiers binaires** dans le chemin d’accès de l’objet blob et les fichiers TXT à soient téléchargées à l’aide de **notifications** dans le chemin d’accès de l’objet blob, utilisez les éléments suivants pour la valeur de la **Liste des artefacts à télécharger** : **build /\*. jar::binaries ; génération /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Comment faire pour créer une étape de génération qui télécharge depuis le stockage blob Azure ##

Les étapes suivantes indiquent comment configurer une étape de génération pour télécharger les éléments depuis le stockage blob Azure. Cela est utile si vous souhaitez inclure des éléments dans votre build, par exemple, les fichiers JAR que vous conservez dans Azure stockage blob.

1. Dans la section **génération** de la configuration de la tâche, cliquez sur **étape de génération ajouter** et choisissez de **télécharger à partir du stockage des objets Blob Azure**.
2. **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
3. **Nom du conteneur**, spécifiez le nom du conteneur qui comprend les objets BLOB que vous souhaitez télécharger. Vous pouvez utiliser des variables d’environnement.
4. **Nom de l’objet Blob**, spécifiez le nom de blob. Vous pouvez utiliser des variables d’environnement. En outre, vous pouvez utiliser un astérisque, comme un caractère générique après avoir spécifié la lettre initiale du nom de l’objet blob. Par exemple, **projet\* ** Spécifiez tous les BLOB dont les noms commencent par **projet**.
5. [Facultatif] Pour **Télécharger le chemin d’accès**, spécifiez le chemin d’accès sur l’ordinateur Jenkins où vous souhaitez télécharger des fichiers depuis le stockage blob Azure. Variables d’environnement peuvent également être utilisés. (Si vous ne fournissez pas une valeur pour le **chemin de téléchargement**, les fichiers depuis le stockage blob Azure seront téléchargés vers l’espace de travail de la tâche.)

Si vous avez d’autres éléments que vous souhaitez télécharger depuis le stockage blob Azure, vous pouvez créer des étapes de génération supplémentaires.

Après avoir exécuté une génération, vous vérifiez la sortie de console de l’historique de génération, et à l’emplacement de téléchargement, pour voir si les BLOB prévu ont été téléchargés avec succès.  

## <a name="components-used-by-the-blob-service"></a>Composants utilisés par le service d’objet Blob ##

La liste suivante fournit une vue d’ensemble des composants du service d’objet Blob.

- **Compte de stockage**: tous les accès au stockage Azure s’effectue via un compte de stockage. Il s’agit du niveau le plus élevé de l’espace de noms pour accéder à des objets BLOB. Un compte peut contenir un nombre illimité de conteneurs, tant que leur taille totale est inférieure à 100 To.
- **Conteneur**: un conteneur fournit un regroupement d’un ensemble d’objets BLOB. Tous les BLOB doivent être dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d’objets BLOB.
- **BLOB**: un fichier de tout type et la taille. Il existe deux types d’objets BLOB qui peut être stockés dans le stockage Azure : BLOB de bloc et de la page. La plupart des fichiers sont des objets BLOB de bloc. Un blob de bloc unique peut être jusqu'à 200 Go. Ce didacticiel utilise des objets BLOB de bloc. BLOB de page, un autre type de blob, peut être jusqu'à 1 To, et sont plus efficace lorsque les plages d’octets dans un fichier sont fréquemment modifiés. Pour plus d’informations sur les objets BLOB, reportez-vous à [présentation bloc BLOB, ajouter un BLOB et BLOB de la Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Format de l’URL**: objets BLOB est adressables en utilisant le format d’URL suivant :

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (Le format ci-dessus s’applique au nuage public Azure. Si vous utilisez un nuage Azure différent, utilisez le point de terminaison au sein du [Portail Azure](https://portal.azure.com) pour déterminer votre point de terminaison URL.)

    Dans le format ci-dessus, `storageaccount` représente le nom de votre compte de stockage, `container_name` représente le nom de votre conteneur, et `blob_name` représente le nom de votre objet blob, respectivement. Dans le nom du conteneur, vous pouvez avoir plusieurs chemins, séparés par une barre oblique, **/**. Le nom de conteneur d’exemple dans ce didacticiel a été **MyJob**, et **${BUILD\_ID} / ${BUILD\_nombre}** a été utilisé pour le chemin d’accès virtuel commune, entraînant le blob ayant une URL sous la forme suivante :

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Étapes suivantes

- [Répondre aux Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
- [Stockage Azure SDK pour Java](https://github.com/azure/azure-storage-java)
- [Référence du Kit de développement logiciel Client stockage Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Pour plus d’informations, consultez également le [Centre pour développeurs Java](https://azure.microsoft.com/develop/java/).