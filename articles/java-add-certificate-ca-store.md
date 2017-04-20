<properties 
    pageTitle="Ajouter un certificat dans le magasin d’autorité de certification Java | Microsoft Azure" 
    description="Apprenez à ajouter un certificat d’autorité de certificat au magasin de certificats (cacerts) autorité de certification Java pour service de Twilio ou de Bus des services Azure." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Ajout d’un certificat dans le magasin de certificats d’autorité de certification Java
Les étapes suivantes vous montrent comment ajouter un certificat d’autorité de certificat dans le magasin de certificat (cacerts) autorité de certification de Java. L’exemple utilisé est pour le certificat de l’autorité de certification requis par le service Twilio. Les informations fournies ultérieurement dans la rubrique explique comment installer le certificat de l’autorité de certification pour le Bus de Service d’Azure. 

Vous pouvez utiliser keytool pour ajouter le certificat d’autorité de certification avant de compresser votre JDK et son ajout à Azure **approot** dossier de votre projet, ou vous pouvez exécuter une tâche de démarrage Azure qui utilise keytool pour ajouter le certificat. Cet exemple suppose que vous allez ajouter un certificat d’autorité de certification avant le JDK en cours zippé. En outre, un certificat d’autorité de certification spécifique sera utilisé dans l’exemple, mais la procédure d’obtention d’un certificat d’autorité de certification différent et les importer dans la banque cacerts serait similaire.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Pour ajouter un certificat au magasin cacerts

1. À une invite de commande est définie sur le dossier de **jdk\jre\lib\security** de votre JDK, exécutez la commande suivante pour voir quels certificats sont installés :

    `keytool -list -keystore cacerts`

    Vous serez invité pour le mot de passe de banque. Le mot de passe par défaut est **changeit**. (Si vous souhaitez modifier le mot de passe, consultez la documentation de keytool à <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Cet exemple suppose que le certificat avec MD5 d’empreinte digitale 67:CB:9 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 n’est pas répertorié, et que vous souhaitez importer (ce certificat est requise par le service de l’API de Twilio).
2. À partir de la liste des certificats figurant sur [Les certificats racine GeoTrust](http://www.geotrust.com/resources/root-certificates/), obtenir le certificat. Cliquez sur le lien pour le certificat portant le numéro de série 35:DE:F4:CF et enregistrez-le dans le dossier **jdk\jre\lib\security** . Pour les besoins de cet exemple, il a été enregistré dans un fichier nommé **Equifax\_Secure\_certificat de\_Authority.cer**.
3. Importer le certificat via la commande suivante :

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Lorsque vous êtes invité à faire confiance à ce certificat, si le certificat a 67:CB:9 d’empreinte digitale MD5 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, répondre en tapant **y**.
4. Exécutez la commande suivante pour vérifier que le certificat de l’autorité de certification a été importé avec succès :

    `keytool -list -keystore cacerts`

5. Le code postal du JDK et l’ajouter à Azure **approot** dossier de votre projet.

Pour plus d’informations sur keytool, consultez <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificats racine Azure

Vos applications qui utilisent des services Azure (par exemple, le Bus des services Azure) ont besoin d’approuver le certificat racine de CyberTrust Baltimore. (Début le 15 avril 2013, Azure a commencé la migration à partir de la racine globale de GTE CyberTrust à la racine de CyberTrust Baltimore. Cette migration a pris plusieurs mois.)

Le certificat peut déjà être installé dans votre Boutique cacerts, de Baltimore il vous suffit d’exécuter la **keytool-liste** commande pour voir si elle existe déjà.

Si vous devez ajouter la racine de CyberTrust Baltimore, il a un numéro de série 02:00:00:b9 et SHA1 d’empreintes d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c : 78:db:28:52:ca:e4:74. Il peut être téléchargé à partir de <https://cacert.omniroot.com/bc2025.crt>, enregistré dans un fichier local avec l' extension **.cer**et puis importés à l’aide de **keytool** comme indiqué ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les certificats racine utilisé par Azure, consultez [Migration de certificat racine Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Pour plus d’informations sur Java, consultez le [Centre pour développeurs Java](/develop/java/).
