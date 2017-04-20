<properties 
    pageTitle="Configuration de la sécurité de la fusion et fractionnement | Microsoft Azure" 
    description="Configurer x409 certificats pour le chiffrement" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Configuration de la sécurité de la fusion et fractionnement  

Pour utiliser le service de fractionnement et de fusion, vous devez configurer correctement la sécurité. Le service fait partie de la fonctionnalité d’évolutivité élastique de base de données SQL de Microsoft Azure. Pour plus d’informations, consultez [fractionnement d’échelle élastique et fusionner le didacticiel Service](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configuration des certificats

Les certificats sont configurés de deux manières. 

1. [Pour configurer le certificat SSL](#To-Configure-the-SSL#Certificate)
2. [Pour configurer les certificats clients](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Pour obtenir des certificats

Les certificats peuvent être obtenues à partir des autorités de certification publiques (CA) ou à partir du [Service de certificats de Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Il s’agit des méthodes privilégiées pour obtenir des certificats.

Si ces options ne sont pas disponibles, vous pouvez générer **des certificats auto-signés**.
 
## <a name="tools-to-generate-certificates"></a>Outils pour générer des certificats

* [Makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2Pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Pour exécuter les outils

* À partir d’une invite de commande développeur pour Visual Studio, voir [invite de commandes de Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Si installé, passez à :

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obtenir le kit WDK de [Windows 8.1 : télécharger les kits et les outils de](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Pour configurer le certificat SSL
Un certificat SSL est requis pour authentifier le serveur et de crypter les communications. Choisissez la plus applicables des trois scénarios ci-dessous et exécutez toutes les étapes de son :

### <a name="create-a-new-self-signed-certificate"></a>Créer un nouveau certificat auto-signé

1.    [Créer un certificat auto-signé](#Create-a-Self-Signed-Certificate)
2.    [Créer un fichier PFX pour le certificat SSL d’auto-signature](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Télécharger le certificat SSL pour le Service en nuage](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Mettre à jour le certificat SSL dans le fichier de Configuration de Service](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importer l’autorité de Certification SSL](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Pour utiliser un certificat existant du magasin de certificats
1. [Exporter un certificat SSL à partir du magasin de certificats](#Export-SSL-Certificate-From-Certificate-Store)
2. [Télécharger le certificat SSL pour le Service en nuage](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Mettre à jour le certificat SSL dans le fichier de Configuration de Service](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Pour utiliser un certificat existant dans un fichier PFX

1. [Télécharger le certificat SSL pour le Service en nuage](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Mettre à jour le certificat SSL dans le fichier de Configuration de Service](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Pour configurer les certificats clients
Les certificats clients sont requis pour authentifier les demandes au service. Choisissez la plus applicables des trois scénarios ci-dessous et exécutez toutes les étapes de son :

### <a name="turn-off-client-certificates"></a>Désactiver les certificats clients
1.    [Désactiver l’authentification basée sur certificat du Client](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Émettre de nouveaux certificats autosignés client
1.    [Créer une autorité de Certification auto-signé](#Create-a-Self-Signed-Certification-Authority)
2.    [Télécharger le certificat de l’autorité de certification pour le Service en nuage](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Mettre à jour le certificat de l’autorité de certification dans le fichier de Configuration de Service](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Délivrer des certificats de Client](#Issue-Client-Certificates)
5.    [Créer les fichiers PFX pour les certificats clients](#Create-PFX-files-for-Client-Certificates)
6.    [Importer un certificat Client](#Import-Client-Certificate)
7.    [Copier les empreintes de certificat Client](#Copy-Client-Certificate-Thumbprints)
8.    [Configurer des Clients autorisés dans le fichier de Configuration de Service](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Utiliser des certificats de client existant
1.    [Trouver la clé publique d’autorité de certification](#Find-CA-Public Key)
2.    [Télécharger le certificat de l’autorité de certification pour le Service en nuage](#Upload-CA-certificate-to-cloud-service)
3.    [Mettre à jour le certificat de l’autorité de certification dans le fichier de Configuration de Service](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copier les empreintes de certificat Client](#Copy-Client-Certificate-Thumbprints)
5.    [Configurer des Clients autorisés dans le fichier de Configuration de Service](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configurer la vérification de révocation de certificats de Client](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Adresses IP autorisées

Accès aux points de terminaison de service peut être limité à des plages d’adresses IP spécifiques.

## <a name="to-configure-encryption-for-the-store"></a>Pour configurer le cryptage pour la banque

Un certificat est nécessaire pour chiffrer les informations d’identification qui sont stockées dans le magasin de métadonnées. Choisissez la plus applicables des trois scénarios ci-dessous et exécutez toutes les étapes de son :

### <a name="use-a-new-self-signed-certificate"></a>Utiliser un nouveau certificat auto-signé

1.     [Créer un certificat auto-signé](#Create-a-Self-Signed-Certificate)
2.     [Créer un fichier PFX auto-signé certificat de cryptage](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Télécharger le certificat de cryptage pour le Service en nuage](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Mettre à jour le certificat de cryptage de fichier de Configuration de Service](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utiliser un certificat existant du magasin de certificats

1.     [Exporter le certificat de chiffrement à partir du magasin de certificats](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Télécharger le certificat de cryptage pour le Service en nuage](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Mettre à jour le certificat de cryptage de fichier de Configuration de Service](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utiliser un certificat existant dans un fichier PFX

1.     [Télécharger le certificat de cryptage pour le Service en nuage](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Mettre à jour le certificat de cryptage de fichier de Configuration de Service](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>La configuration par défaut

La configuration par défaut refuse tout accès au point de terminaison HTTP. C’est le paramètre recommandé, dans la mesure où les demandes de ces points de terminaison peuvent contiennent des informations sensibles telles que des informations d’identification de la base de données.
La configuration par défaut autorise tous les accès au point de terminaison HTTPS. Ce paramètre peut être restreint davantage.

### <a name="changing-the-configuration"></a>Modification de la Configuration

Le groupe de règles de contrôle d’accès qui s’appliquent à et de point de terminaison sont configurés dans le **<EndpointAcls>** section dans le **fichier de configuration de service**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Les règles d’un groupe de contrôle d’accès sont configurées dans un <AccessControl name=""> section du fichier de configuration du service. 

Le format est expliqué dans la documentation de listes de contrôle d’accès réseau.
Par exemple, pour autoriser uniquement les adresses IP dans la plage 100.100.0.0 à 100.100.255.255 pour accéder au point de terminaison HTTPS, les règles ressemblerait à ceci :

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Service prévention du déni de

Il existe deux mécanismes différents pris en charge pour détecter et empêcher les attaques par déni de Service :

*    Limiter le nombre de requêtes simultanées par hôte distant (désactivé par défaut)
*    Limiter les taux d’accès par l’hôte distant (sur par défaut)

Ils sont basés sur les fonctionnalités dans la sécurité IP dynamique dans IIS. Lorsque la modification de cette configuration prise en compte des facteurs suivants :

* Le comportement des serveurs proxy et des périphériques de traduction d’adresses réseau sur les informations d’hôte distant
* Chaque demande adressée à une ressource dans le rôle web est considéré comme (par exemple, chargement de scripts, images, etc.)

## <a name="restricting-number-of-concurrent-accesses"></a>Limiter le nombre d’accès simultanés

Configurer ce comportement sont les suivantes :

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Modifier le DynamicIpRestrictionDenyByConcurrentRequests sur true pour activer cette protection.

## <a name="restricting-rate-of-access"></a>Taux de restriction d’accès

Configurer ce comportement sont les suivantes :

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configuration de la réponse à une demande refusée

Le paramètre suivant configure la réponse à une demande refusée :

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Reportez-vous à la documentation de la sécurité IP dynamique dans IIS pour les autres valeurs prises en charge.

## <a name="operations-for-configuring-service-certificates"></a>Opérations de configuration de certificats de service
Cette rubrique est pour référence uniquement. Veuillez suivre les étapes de configuration décrites dans :

* Configurer le certificat SSL
* Configurer les certificats clients

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé
Exécuter :

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Pour personnaliser :

*    -n avec l’URL du service. Les caractères génériques ("CN = * .cloudapp .net ») et d’autres noms (CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net ») sont pris en charge.
*    -e, avec la date d’expiration de certificat créer un mot de passe et le spécifier lorsque vous y êtes invité.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Créez le fichier PFX pour le certificat SSL auto-signé

Exécuter :

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Entrez le mot de passe, puis exporter le certificat avec ces options :
* Oui, exporter la clé privée
* Exporter toutes les propriétés étendues

## <a name="export-ssl-certificate-from-certificate-store"></a>Exporter un certificat SSL à partir du magasin de certificats

* Trouver un certificat
* Cliquez sur Actions -> toutes les tâches -> Exporter...
* Exportez le certificat dans un. Fichiers PFX avec ces options :
    * Oui, exporter la clé privée
    * Inclure tous les certificats dans le chemin d’accès de certification, si possible * exporter toutes les propriétés étendues

## <a name="upload-ssl-certificate-to-cloud-service"></a>Télécharger un certificat SSL au service de cloud

Téléchargement du certificat avec l’existant ou généré. Fichiers PFX avec la paire de clés SSL :

* Entrez le mot de passe protège les informations de clé privée

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Mettre à jour le certificat SSL dans le fichier de configuration de service

Mettre à jour la valeur de l’empreinte numérique du paramètre suivant dans le fichier de configuration de service avec l’empreinte numérique du certificat téléchargé vers le service en nuage :

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importer l’autorité de certification SSL

Suivez ces étapes dans tous les compte/machine qui doivent communiquer avec le service :

* Double-cliquez sur le. Fichier CER dans l’Explorateur Windows
* Dans la boîte de dialogue certificat, cliquez sur Installer le certificat...
* Importer un certificat dans le magasin d’autorités de Certification racines de confiance

## <a name="turn-off-client-certificate-based-authentication"></a>Désactiver l’authentification basée sur certificat du client

Uniquement basée sur les certificats authentification du client est pris en charge et sa désactivation permet pour l’accès du public aux points de terminaison de service, à moins que les autres mécanismes sont en place (par ex. Microsoft Azure réseau virtuel).

Modifiez ces paramètres sur false dans le fichier de configuration pour désactiver la fonctionnalité de service :

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Ensuite, copiez la même empreinte que le certificat SSL dans le paramètre de certificat d’autorité de certification :

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Créer une autorité de certification auto-signé
Exécutez les étapes suivantes pour créer un certificat auto-signé pour agir comme une autorité de Certification :

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Personnaliser

*    -e, avec la date d’expiration de certification


## <a name="find-ca-public-key"></a>Trouver la clé publique d’autorité de certification

Tous les certificats de client doivent avoir été émis par une autorité de Certification approuvée par le service. Trouver la clé publique de l’autorité de Certification qui a émis le client les certificats qui vont être utilisées pour l’authentification pour télécharger vers le service en nuage.

Si le fichier avec la clé publique n’est pas disponible, l’exporter à partir du magasin de certificats :

* Trouver un certificat
    * Rechercher un certificat client émis par la même autorité de Certification
* Double-cliquez sur le certificat.
* Dans la boîte de dialogue certificat, sélectionnez l’onglet chemin d’accès de Certification.
* Double-cliquez sur l’entrée de l’autorité de certification dans le chemin d’accès.
* Prendre des notes, des propriétés du certificat.
* Fermez la boîte de dialogue **certificat** .
* Trouver un certificat
    * Recherchez l’autorité de certification mentionnée ci-dessus.
* Cliquez sur Actions -> toutes les tâches -> Exporter...
* Exportez le certificat dans un. Région d’exécution limitée avec ces options :
    * **Non, ne pas exporter la clé privée**
    * Inclure tous les certificats dans le chemin d’accès de certification si possible.
    * Exporter toutes les propriétés étendues.

## <a name="upload-ca-certificate-to-cloud-service"></a>Télécharger le certificat de l’autorité de certification au service de cloud

Téléchargement du certificat avec l’existant ou généré. Fichier de région d’exécution limitée avec la clé publique de l’autorité de certification.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certificat d’autorité de certification mise à jour dans le fichier de configuration de service

Mettre à jour la valeur de l’empreinte numérique du paramètre suivant dans le fichier de configuration de service avec l’empreinte numérique du certificat téléchargé vers le service en nuage :

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Mettre à jour la valeur du paramètre suivant avec la même empreinte :

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Délivrer des certificats de client

Chaque utilisateur autorisé à accéder au service doit avoir un certificat client émis pour his/hers exclusif à utiliser et choisissez que HIS/hers propre mot de passe fort pour protéger la clé privée. 

Les étapes suivantes doivent être exécutées dans le même ordinateur où le certificat d’autorité de certification auto-signé a été généré et stocké :

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personnalisation :

* -n avec un ID pour le client qui sera authentifié avec ce certificat
* -e, avec la date d’expiration du certificat
* MyID.pvk et MyID.cer avec des noms de fichier uniques pour ce certificat client

Cette commande vous demandera un mot de passe et ensuite une fois. Utilisez un mot de passe.

## <a name="create-pfx-files-for-client-certificates"></a>Créer des certificats pour le client, les fichiers PFX

Pour chaque certificat client généré, exécutez :

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personnalisation :

    MyID.pvk and MyID.cer with the filename for the client certificate

Entrez le mot de passe, puis exporter le certificat avec ces options :

* Oui, exporter la clé privée
* Exporter toutes les propriétés étendues
* La personne à qui est émis ce certificat doit choisir le mot de passe d’exportation

## <a name="import-client-certificate"></a>Importer un certificat client

Chaque individu pour lequel un certificat client a été délivré doit importer la paire de clés dans les ordinateurs qu’il utilisera pour communiquer avec le service :

* Double-cliquez sur le. Fichier PFX dans l’Explorateur Windows
* Importer un certificat dans le personnel stocker au moins cette option :
    * Inclure toutes les propriétés étendues activées

## <a name="copy-client-certificate-thumbprints"></a>Copier les empreintes de certificat client
Chaque individu pour lequel un certificat client a été délivré doit suivre ces étapes afin d’obtenir l’empreinte du his/hers les certificats qui seront ajoutées au fichier de configuration du service :
* Exécution de certmgr.exe
* Sélectionnez l’onglet personnel
* Double-cliquez sur le certificat à utiliser pour l’authentification du client
* Dans la boîte de dialogue certificat qui s’ouvre, sélectionnez l’onglet Détails
* Assurez-vous qu’Afficher affiche tous les
* Sélectionnez le champ nommé empreinte numérique dans la liste
* Copie la valeur de l’empreinte numérique **des caractères Unicode non visible devant le premier chiffre de supprimer** supprimer tous les espaces

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurer les clients autorisés dans le fichier de configuration de service

Mettre à jour la valeur du paramètre suivant dans le fichier de configuration de service avec une liste séparée par des virgules les empreintes des certificats client autorisés à accéder au service :

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurer la vérification de révocation de certificats de client

Le paramètre par défaut ne vérifie pas avec l’autorité de Certification pour l’état de révocation du certificat client. Pour activer les contrôles, si l’autorité de Certification qui a délivré les certificats de client prend en charge ces contrôles, modifiez le paramètre suivant avec l’une des valeurs définies dans l’énumération X509RevocationMode :

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Créez le fichier PFX pour les certificats auto-signés de chiffrement

Un certificat de cryptage, exécutez :

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personnalisation :

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Entrez le mot de passe, puis exporter le certificat avec ces options :
*    Oui, exporter la clé privée
*    Exporter toutes les propriétés étendues
*    Vous aurez besoin du mot de passe lors du téléchargement du certificat au service cloud.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exporter le certificat de chiffrement à partir du magasin de certificats

*    Trouver un certificat
*    Cliquez sur Actions -> toutes les tâches -> Exporter...
*    Exportez le certificat dans un. Fichiers PFX avec ces options : 
  *    Oui, exporter la clé privée
  *    Inclure tous les certificats dans le chemin d’accès de certification, si possible 
*    Exporter toutes les propriétés étendues

## <a name="upload-encryption-certificate-to-cloud-service"></a>Télécharger le certificat de cryptage de service cloud

Téléchargement du certificat avec l’existant ou généré. Fichiers PFX avec la paire de clés de cryptage :

* Entrez le mot de passe protège les informations de clé privée

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Mettre à jour le certificat de cryptage de fichier de configuration de service

Mettre à jour la valeur de l’empreinte numérique des paramètres suivants dans le fichier de configuration de service avec l’empreinte numérique du certificat téléchargé vers le service en nuage :

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Opérations courantes du certificat

* Configurer le certificat SSL
* Configurer les certificats clients

## <a name="find-certificate"></a>Trouver un certificat

Procédez comme suit :

1. Exécutez mmc.exe.
2. Fichier -> Ajouter/supprimer un composant logiciel enfichable...
3. Sélectionnez **certificats**.
4. Cliquez sur **Ajouter**.
5. Choisissez l’emplacement de magasin de certificats.
6. Cliquez sur **Terminer**.
7. Cliquez sur **OK**.
8. Développez **certificats**.
9. Développez le nœud du magasin de certificats.
10. Développez le nœud enfant de certificat.
11. Sélectionnez un certificat dans la liste.

## <a name="export-certificate"></a>Certificat d’exportation
Dans l' **Assistant Exportation de certificat**:

1. Cliquez sur **suivant**.
2. Sélectionnez **Oui**, **Exporter la clé privée**.
3. Cliquez sur **suivant**.
4. Sélectionnez le format de fichier de sortie souhaité.
5. Vérifiez les options de votre choix.
6. Vérifiez le **mot de passe**.
7. Entrez un mot de passe et confirmez-le.
8. Cliquez sur **suivant**.
9. Tapez ou recherchez un nom de fichier où stocker le certificat (utilisez un. Extension PFX).
10. Cliquez sur **suivant**.
11. Cliquez sur **Terminer**.
12. Cliquez sur **OK**.

## <a name="import-certificate"></a>Importer un certificat

Dans l’Assistant Importation de certificat :

1. Sélectionnez l’emplacement de stockage.

    * Sélectionnez **l’Utilisateur actuel** si une seule des processus en cours d’exécution sous un utilisateur actuel accède au service
    * Sélectionnez **Ordinateur Local** si d’autres processus sur cet ordinateur accède au service
2. Cliquez sur **suivant**.
3. Si vous importez à partir d’un fichier, vérifiez le chemin d’accès du fichier.
4. Si vous importez un. Fichiers PFX :
    1.     Entrez le mot de passe protégeant la clé privée
    2.     Sélectionnez les options d’importation
5.     Sélectionnez les certificats « Place » dans le magasin suivant
6.     Cliquez sur **Parcourir**.
7.     Sélectionnez le magasin souhaité.
8.     Cliquez sur **Terminer**.
       
    * Si la banque d’informations de l’autorité de Certification racine de confiance a été choisi, cliquez sur **Oui**.
9.     Cliquez sur **OK** dans toutes les fenêtres de la boîte de dialogue.

## <a name="upload-certificate"></a>Télécharger le certificat

Dans le [portail Azure](https://portal.azure.com/)

1. Sélectionnez les **Services en nuage**.
2. Sélectionnez le service cloud.
3. Dans le menu supérieur, cliquez sur **certificats**.
4. Sur la barre du bas, cliquez sur **Télécharger**.
5. Sélectionnez le fichier de certificat.
6. Si elle est un. PFX fichier, entrez le mot de passe pour la clé privée.
7. Une fois terminé, copiez l’empreinte numérique du certificat à partir de la nouvelle entrée dans la liste.

## <a name="other-security-considerations"></a>Autres considérations sur la sécurité
 
Les paramètres SSL décrites dans ce document crypter les communications entre le service et ses clients lorsque le point de terminaison HTTPS est utilisé. Ceci est important depuis les informations d’identification pour l’accès de la base de données et éventuellement d’autres informations sensibles sont contenues dans la communication. Notez, cependant, que le service persiste d’état interne, y compris les informations d’identification, dans ses tables internes de la base de données SQL Azure de Microsoft que vous avez fournies pour le stockage de métadonnées dans votre abonnement Microsoft Azure. Cette base de données a été défini en tant que partie du paramètre suivant dans votre fichier de configuration de service (. Fichier CSCFG) : 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Informations d’identification stockées dans cette base de données sont cryptées. Toutefois, pour obtenir les meilleurs résultats, assurez-vous que les rôles web et worker de vos déploiements de service sont mis à jour et sécurisé comme ils ont accès à la base de données de métadonnées et le certificat utilisé pour le cryptage et le décryptage des informations d’identification stockées. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
