<properties
    pageTitle="Comment faire pour générer et transférer les clés protégées par HSM pour Azure clé coffre-fort | Microsoft Azure"
    description="Utilisez cet article pour vous aider à planifier, générer et transférer vos propres clés protégés par HSM à utiliser avec Azure clé coffre-fort."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Comment faire pour générer et transférer protégé HSM clés pour Azure clé coffre-fort

##<a name="introduction"></a>Introduction

Pour la garantie, lorsque vous utilisez Azure coffre-fort de clé, vous pouvez importer ou générer des clés dans les modules de sécurité matérielle (HSM) qui ne jamais laisser la frontière HSM. Ce scénario est souvent appelé à *mettre votre propre clé*, ou BYOK. Les modules HSM sont FIPS 140-2 de niveau 2 est validé. Coffre-fort de clé Azure utilise la famille de nShield de Thales des modules HSM pour protéger vos clés.

Utilisez les informations de cette rubrique pour vous aider à planifier, générer et transférer vos propres clés protégés par HSM à utiliser avec Azure clé coffre-fort.

Cette fonctionnalité n’est pas disponible pour la Chine d’Azure. 

>[AZURE.NOTE] Pour plus d’informations sur le coffre-fort de clé d’Azure, consultez [Quel est le coffre-fort de clé Azure ?](key-vault-whatis.md)  
>
>Pour lors de l’obtention démarré, qui inclut la création d’un coffre-fort de clés pour les clés protégées par HSM, visionnez le didacticiel [mise en route de la chambre forte de clé Azure](key-vault-get-started.md).

Plus d’informations sur la génération et le transfert d’une clé protégée par HSM sur Internet :

- Vous générez la clé à partir d’une station de travail en mode hors connexion, ce qui réduit la surface d’attaque.

- La clé est chiffrée avec une clé Exchange Key (KEK), qui reste crypté tant qu’il est transféré vers le coffre-fort de clé Azure modules HSM. Uniquement la version cryptée de votre clé quitte la station de travail d’origine.

- L’ensemble d’outils définit des propriétés sur votre clé cliente qui lie votre clé vers le monde de la sécurité Azure clé coffre-fort. Ainsi, après que les modules HSM coffre-fort Azure clé recevoir et décrypter votre clé, uniquement ces modules HSM permet. Votre clé ne peut pas être exportée. Cette liaison est appliquée par les modules HSM de Thales.

- La clé d’Exchange clé (KEK) qui est utilisée pour crypter votre clé est généré à l’intérieur du coffre-fort de clé Azure modules HSM et n’est pas exportable. Les modules HSM appliquent qu’il ne peut y avoir aucune version claire de la KEK en dehors du HSM. En outre, l’ensemble d’outils inclut des attestation de Thales que le KEK n’est pas exportable et qu’il a été généré à l’intérieur d’un module HSM authentique qui a été fabriqué par Thales.

- L’ensemble d’outils inclut des attestation de Thales que le monde de la sécurité Azure clé coffre-fort a également été généré sur un module HSM authentique fabriqué par Thales. Cette attestation s’avère vous que Microsoft est l’utilisation de matériel authentique.

- Microsoft utilise des KEKs distincts et séparer les mondes de sécurité dans chaque région géographique. Cette séparation permet de s’assurer que votre clé peut être utilisée uniquement dans les centres de données dans la région dans laquelle vous le cryptées. Par exemple, une clé à partir d’un client européen ne peut pas être utilisée dans des centres de données en Amérique du Nord ou en Asie.

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Plus d’informations sur les services Microsoft et les modules HSM de Thales

Thales e-Security est un leader mondial des solutions de sécurité informatiques pour les services financiers, haute technologie, fabrication, gouvernement et des secteurs de la technologie et le cryptage de. Avec un 40 ans enregistrement du suivi de la protection d’entreprise et les informations du gouvernement, les solutions de Thales sont utilisées par quatre des cinq entreprises plus importantes d’énergie et de l’aéronautique. Leurs solutions sont également utilisées par 22 pays de l’OTAN et sécuriser plus de 80 pour cent des transactions de paiement dans le monde entier.

Microsoft a collaboré avec Thales pour améliorer l’état de l’art pour les modules HSM. Ces améliorations vous permettent d’obtenir les avantages classiques de services hébergés sans désaffecté contrôler vos clés. Plus précisément, ces améliorations permettent à Microsoft de gérer les modules HSM afin que vous n’avez pas à. Sous la forme d’un service en nuage, Azure clé Vault augmente à bref délai afin de répondre aux pics d’utilisation de votre organisation. Dans le même temps, votre clé est protégée à l’intérieur avec des modules HSM de Microsoft : vous conservez le contrôle sur le cycle de vie de clés car vous générez la clé et la transmettre à avec des modules HSM Microsoft.

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Mise en œuvre d’amener votre propre clé (BYOK) pour Azure clé coffre-fort

Utilisez les informations et les procédures suivantes si vous générer votre propre clé protégé par HSM et transférez-les sur Azure clé coffre-fort — l’amener votre propre scénario clé (BYOK).


##<a name="prerequisites-for-byok"></a>Conditions préalables pour BYOK

Consultez le tableau suivant pour une liste des conditions requises pour mettre votre propre clé (BYOK) pour Azure clé coffre-fort.

|Exigence|Plus d’informations|
|---|---|
|Un abonnement vers Azure|Pour créer un coffre-fort de clé Azure, vous avez besoin d’un abonnement Azure : [Inscrivez-vous pour une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)|
|Le niveau de service Premium de coffre-fort Azure clé pour prendre en charge les clés de HSM protégé|Pour plus d’informations sur les fonctions et les niveaux de service pour Azure clé coffre-fort, voir le site Web de [Tarification d’Azure clé coffre-fort](https://azure.microsoft.com/pricing/details/key-vault/) .|
|Thales HSM, cartes à puce et les logiciels de prise en charge|Vous devez disposer de l’accès à un Module de sécurité matériel Thales et la base connaissances opérationnelles des modules HSM de Thales. Pour obtenir la liste des modèles compatibles, ou pour acheter un module HSM, si vous n’en avez pas, reportez-vous à la section [Module de sécurité matériel de Thales](https://www.thales-esecurity.com/msrms/buy) .|
|Les matériels et logiciels suivants :<ol><li>Un x64 hors connexion poste de travail avec un système d’exploitation Windows minimal de Windows 7 et Thales nShield logiciels au moins la version 11.50.<br/><br/>Si cette station de travail s’exécute Windows 7, vous devez [installer Microsoft.NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Une station de travail qui est connectée à Internet et a un système d’exploitation Windows minimal de Windows 7.</li><li>Un lecteur USB ou un autre périphérique de stockage portable qui dispose au moins de 16 Mo d’espace libre.</li></ol>|Pour des raisons de sécurité, nous recommandons que la première station de travail n’est pas connectée à un réseau. Toutefois, cette recommandation n’est pas appliquée par programme.<br/><br/>Notez que dans les instructions qui suivent, cette station de travail est appelée la station de travail déconnectée.</p></blockquote><br/>En outre, si votre clé de clients pour un réseau de production, nous vous recommandons d’utiliser une deuxième station de travail séparée pour télécharger le jeu d’outils de la clé du client. Mais à des fins de test, vous pouvez utiliser la même station de travail que le premier.<br/><br/>Notez que dans les instructions qui suivent, cette deuxième station de travail est appelée le poste de travail connecté à Internet.</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Générer et transférer votre clé à Azure clé coffre-fort HSM

Vous allez utiliser les cinq étapes suivantes pour générer et transférer votre clé à un coffre-fort de clé Azure module HSM :

- [Étape 1 : Préparez votre poste de travail connecté à Internet](#step-1-prepare-your-internet-connected-workstation)
- [Étape 2 : Préparer votre station de travail déconnectée](#step-2-prepare-your-disconnected-workstation)
- [Étape 3 : Générer votre clé](#step-3-generate-your-key)
- [Étape 4 : Préparer votre clé pour le transfert](#step-4-prepare-your-key-for-transfer)
- [Étape 5 : Transférer votre clé dans Azure clé coffre-fort](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Étape 1 : Préparez votre poste de travail connecté à Internet
Pour cette première étape, effectuez les procédures suivantes sur votre station de travail qui est connecté à Internet.


###<a name="step-11-install-azure-powershell"></a>Étape 1.1 : Installer PowerShell Azure

À partir de la station de travail connecté à Internet, téléchargez et installez le module de Azure PowerShell qui inclut les applets de commande pour gérer le coffre-fort de clé Azure. Cela nécessite une version minimale de 0.8.13.

Pour les instructions d’installation, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

###<a name="step-12-get-your-azure-subscription-id"></a>Étape 1.2 : Obtenir votre ID d’abonnement Azure

Démarrer une session PowerShell d’Azure et vous connecter à votre compte Azure à l’aide de la commande suivante :

        Add-AzureAccount
Dans la fenêtre contextuelle, entrez votre nom d’utilisateur de compte Azure et d’un mot de passe. Ensuite, utilisez la commande [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
À partir de la sortie, recherchez l’ID de l’abonnement que vous utiliserez pour Azure clé coffre-fort. Vous devez ensuite cet ID d’abonnement.

Ne fermez pas la fenêtre PowerShell d’Azure.

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Étape 1.3 : Téléchargez l’ensemble d’outils BYOK pour Azure clé coffre-fort

Atteindre le Microsoft Download Center et [Télécharger l’ensemble d’outils Azure clé coffre-fort BYOK](http://www.microsoft.com/download/details.aspx?id=45345) pour votre région géographique ou d’une instance d’Azure. Pour identifier le nom du package à télécharger et son hachage du package correspondant de SHA-256, utilisez les informations suivantes :

---

**Amérique du Nord :**

States.zip de KeyVault-BYOK-outils-Royaume-Uni

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**Europe :**

KeyVault-BYOK-outils-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**Asie :**

KeyVault-BYOK-outils-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**Amérique Latine :**

KeyVault-BYOK-outils-LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**Japon :**

KeyVault-BYOK-outils-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**Australie :**

KeyVault-BYOK-outils-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Gouvernement Azure :**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-outils-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**Canada :**

KeyVault-BYOK-outils-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**Allemagne :**

KeyVault-BYOK-outils-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**Inde :**

KeyVault-BYOK-outils-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

Pour valider l’intégrité de vos outils BYOK téléchargé, à partir de votre session PowerShell d’Azure, utilisez l’applet de commande [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

L’ensemble d’outils comprend les éléments suivants :

- Un package de clé Exchange Key (KEK) qui a un nom commençant par **BYOK-KEK - pkg-.**
- Un package de sécurité mondiale qui a un nom commençant par **BYOK-SecurityWorld - pkg-.**
- Un script de python nommé v**erifykeypackage.py.**
- Un exécutable de ligne de commande du fichier nommé **KeyTransferRemote.exe** et les fichiers DLL associés.
- Un Package redistribuable Visual C++, nommé **vcredist_x64.exe.**

Copier le package vers un lecteur USB ou tout autre stockage portable.

##<a name="step-2-prepare-your-disconnected-workstation"></a>Étape 2 : Préparer votre station de travail déconnectée

Pour cette deuxième étape, effectuez les procédures suivantes sur la station de travail n’est pas connecté à un réseau (Internet ou votre réseau interne).


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Étape 2.1 : Préparation de la station de travail déconnectée avec Thales HSM

Installer le logiciel de prise en charge de nCipher (Thales) sur un ordinateur Windows et associez un HSM Thales à cet ordinateur.

Assurez-vous que les outils de Thales sont dans votre chemin d’accès (**%nfast_home%\bin** et **%nfast_home%\python\bin**). Par exemple, tapez ce qui suit :

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Pour plus d’informations, consultez le guide de l’utilisateur inclus dans le module HSM de Thales.

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Étape 2.2 : Installer l’ensemble d’outils BYOK sur la station de travail déconnectée

Copiez le package d’outils BYOK à partir de la clé USB ou tout autre stockage portable, puis effectuez le des opérations suivantes :

1. Extrayez les fichiers du package téléchargé dans n’importe quel dossier.
2. À partir de ce dossier, exécutez vcredist_x64.exe.
3. Suivez les instructions pour l’installation les composants d’exécution Visual C++ de Visual Studio 2013.

##<a name="step-3-generate-your-key"></a>Étape 3 : Générer votre clé

Pour cette troisième étape, effectuez les procédures suivantes sur la station de travail déconnectée.

###<a name="step-31-create-a-security-world"></a>Étape 3.1 : Créer un monde de sécurité

Démarrez une invite de commande et exécutez le programme de nouveau monde de Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Ce programme crée un fichier de **Sécurité World** à % NFAST_KMDATA%\local\world, qui correspond au dossier Data\local de gestion C:\ProgramData\nCipher\Key. Vous pouvez utiliser des valeurs différentes pour le quorum, mais dans notre exemple, vous êtes invité à entrer les trois broches et cartes vierges pour chacun d’eux. Puis, les deux cartes donnent un accès complet pour le monde de la sécurité. Ces cartes devient l' **Administrateur carte défini** pour le nouveau monde de la sécurité.

Puis effectuez les opérations suivantes :

- Sauvegardez le fichier world. Sécuriser et protéger le fichier world, les cartes de l’administrateur et de leurs broches et assurez-vous qu’aucune personne n’a accès à plus d’une carte.

###<a name="step-32-validate-the-downloaded-package"></a>Étape 3.2 : Valider le package téléchargé

Cette étape est facultative, mais recommandée afin que vous pouvez valider les données suivantes :

- La clé d’échange de clé qui est inclus dans le jeu d’outils a été générée à partir d’un module HSM Thales authentique.
- La valeur de hachage du monde qui est inclus dans le jeu d’outils de sécurité a été générée dans un module HSM Thales authentique.
- La clé d’échange de clé est non exportable.

>[AZURE.NOTE]Pour valider le package téléchargé, le HSM doit être connecté, sous tension et doit avoir un monde de sécurité qu’il contient (par exemple, celui que vous venez de créer).

Pour valider le package téléchargé :

1.  En reliant une des valeurs suivantes, selon votre région géographique ou d’une instance d’Azure, exécutez le script verifykeypackage.py :
    - Pour l’Amérique du Nord :

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - Pour l’Europe :

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - Pour l’Asie :

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - Pour l’Amérique latine :

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - Pour le Japon :

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - Pour l’Australie :

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - Pour le [Gouvernement d’Azure](https://azure.microsoft.com/features/gov/), qui utilise l’instance du gouvernement américain d’Azure :

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - Pour le Canada :

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - Pour l’Allemagne :

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - Pour l’Inde :

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]Le logiciel de Thales inclut les python à %NFAST_HOME%\python\bin

2.  Vérifiez que vous consultez la rubrique suivante, qui indique si la validation réussit : **résultat : réussite**

Ce script vérifie la chaîne du signataire à la clé de racine de Thales. La valeur de hachage de cette clé racine est incorporé dans le script, et sa valeur doit être **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Vous pouvez également confirmer cette valeur séparément en visitant le [site Web de Thales](http://www.thalesesec.com/).

Vous êtes maintenant prêt à créer une nouvelle clé.

###<a name="step-33-create-a-new-key"></a>Étape 3.3 : Création d’une nouvelle clé

Générer une clé en utilisant le programme de **méthodes generatekey** Thales.

Exécutez la commande suivante pour générer la clé :

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Lorsque vous exécutez cette commande, utilisez les instructions suivantes :

- Le paramètre de *protection* doit être définie à la valeur du **module**, comme indiqué. Cette opération crée une clé protégée par module. L’ensemble d’outils BYOK ne supporte pas les clés protégées par OCS.

- Remplacez la valeur de *contosokey* pour **l’identification** et la **plainname** par n’importe quelle valeur de chaîne. Pour réduire les frais généraux administratifs et réduire le risque d’erreurs, nous vous recommandons de que vous utilisez la même valeur pour les deux. La valeur **d’ident** doit contenir uniquement des chiffres, des tirets et des lettres minuscules.

- Le pubexp est vide (par défaut) dans cet exemple, mais vous pouvez spécifier des valeurs spécifiques. Pour plus d’informations, consultez la documentation de Thales.

Cette commande crée un fichier sous forme de jetons de clé dans votre dossier %NFAST_KMDATA%\local avec un nom commençant par **key_simple_**, suivie de l' **identification** qui a été spécifié dans la commande. Par exemple : **key_simple_contosokey**. Ce fichier contient une clé chiffrée.

Sauvegardez ce fichier sous forme de jetons de clé dans un endroit sûr.

>[AZURE.IMPORTANT] Lorsque vous transférez plus tard votre clé dans Azure clé coffre-fort, Microsoft ne peut pas exporter cette clé vous afin qu’il devienne extrêmement important que vous sauvegardez votre clé et sécurité du monde en toute sécurité. Pour les instructions et méthodes conseillées pour la sauvegarde de votre clé, contactez Thales.

Vous êtes maintenant prêt à transférer votre clé à Azure clé coffre-fort.

##<a name="step-4-prepare-your-key-for-transfer"></a>Étape 4 : Préparer votre clé pour le transfert

Pour cette quatrième étape, effectuez les procédures suivantes sur la station de travail déconnectée.

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Étape 4.1 : Créer une copie de votre clé avec des autorisations restreintes

Pour limiter les autorisations sur la clé, à partir d’une invite de commande, exécutez une des valeurs suivantes, selon votre région géographique ou d’une instance d’Azure :

- Pour l’Amérique du Nord :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Pour l’Europe :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Pour l’Asie :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- Pour l’Amérique latine :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Pour le Japon :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Pour l’Australie :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Pour le [Gouvernement d’Azure](https://azure.microsoft.com/features/gov/), qui utilise l’instance du gouvernement américain d’Azure :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- Pour le Canada :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- Pour l’Allemagne :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- Pour l’Inde :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


Lorsque vous exécutez cette commande, remplacez *contosokey* par la même valeur que vous avez spécifié dans **étape 3.3 : créer une clé de** à partir de l’étape de [générer votre clé](#step-3-generate-your-key) .

Vous êtes invité à connecter vos cartes de visite de sécurité world admin.

Lorsque la commande a terminé, vous consultez **résultat : réussite** et la copie de votre clé avec des autorisations réduites sont dans le fichier nommé key_xferacId_<contosokey>.

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>Étape 4.2 : Inspecter la nouvelle copie de la clé

Le cas échéant, exécuter le Thales les utilitaires pour vérifier les autorisations minimales sur la nouvelle clé :

- aclprint.py :

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe :

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Lorsque vous exécutez ces commandes, remplacez contosokey par la même valeur que vous avez spécifié dans **étape 3.3 : créer une clé de** à partir de l’étape de [générer votre clé](#step-3-generate-your-key) .

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Étape 4.3 : Crypter votre clé à l’aide, clé de Microsoft d’Exchange

Exécutez une des commandes suivantes, en fonction de votre zone géographique ou d’une instance d’Azure :

- Pour l’Amérique du Nord :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Europe :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Asie :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Amérique latine :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le Japon :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Australie :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le [Gouvernement d’Azure](https://azure.microsoft.com/features/gov/), qui utilise l’instance du gouvernement américain d’Azure :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le Canada :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Allemagne :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Inde :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Lorsque vous exécutez cette commande, utilisez les instructions suivantes :

- Remplacez *contosokey* par l’identificateur que vous avez utilisé pour générer la clé de **étape 3.3 : créer une clé de** à partir de l’étape de [générer votre clé](#step-3-generate-your-key) .

- Remplacez le *SubscriptionID* avec l’ID de l’abonnement Azure qui contient votre clé coffre-fort. Vous avez récupéré cette valeur auparavant, dans **étape 1.2 : obtenir votre ID d’abonnement Azure** à partir de l’étape de la [Préparation de votre poste de travail connecté à Internet](#step-1-prepare-your-internet-connected-workstation) .

- Remplacez *ContosoFirstHSMKey* par une étiquette qui est utilisée pour le nom de votre fichier de sortie.

Fin de l’opération avec succès, il affiche **résultat : réussite** et un nouveau fichier dans le dossier en cours ayant le nom suivant : TransferPackage -*ContosoFirstHSMkey*.byok

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Étape 4.4 : Copie de votre package de transfert de clés pour le poste de travail connecté à Internet

Utiliser un lecteur USB ou tout autre stockage portable pour copier le fichier de sortie de l’étape précédente (KeyTransferPackage-ContosoFirstHSMkey.byok) à votre poste de travail connecté à Internet.

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>Étape 5 : Transférer votre clé dans Azure clé coffre-fort

Pour cette dernière étape, sur le poste de travail connecté à Internet, utilisez l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) pour télécharger le package de transfert de clés que vous avez copiés à partir de la station de travail déconnectée pour le HSM de coffre-fort de clé Azure :

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Si le téléchargement réussit, vous affiche les propriétés de la clé que vous venez d’ajouter.


##<a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant utiliser cette clé protégé par HSM dans votre chambre forte de clé. Pour plus d’informations, consultez la section **Si vous souhaitez utiliser un module de sécurité matériel (HSM)** dans le didacticiel [mise en route avec Azure clé coffre-fort](key-vault-get-started.md) .
