<properties 
   pageTitle="Créer des certificats auto-signés pour réseau virtuel Point-à-Site coexistence connexions à l’aide de makecert | Microsoft Azure"
   description="Cet article contient les étapes pour créer des certificats auto-signés sur 10 de Windows à l’aide de makecert."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Fonctionne avec des certificats autosignés pour les connexions Site-à-Point

Cet article vous aide à créer un certificat auto-signé à l’aide de **makecert**et de générer des certificats de client à partir de celui-ci. Les étapes sont écrits de makecert sur Windows 10. Makecert a été validée pour créer des certificats qui sont compatibles avec les connexions de P2S. 

Pour les connexions de P2S, est la méthode recommandée pour les certificats à utiliser votre solution de certificat d’entreprise, veillant à émettre des certificats de client avec le format de la valeur nom commun 'name@yourdomain.com', et non au format « NetBIOS domaine\nom d’utilisateur ».

Si vous n’avez pas une solution d’entreprise, un certificat auto-signé est nécessaire pour permettre aux clients de P2S pour se connecter à un réseau virtuel. Nous sommes conscients que makecert a été désapprouvée, mais il est toujours une méthode valide pour la création de certificats auto-signés qui sont compatibles avec les connexions de P2S. Nous travaillons sur une autre solution pour la création de certificats auto-signés, mais pour l’instant, makecert est la méthode recommandée.

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Makecert est une manière de créer un certificat auto-signé. Les étapes suivantes vous guident dans le processus de création d’un certificat auto-signé à l’aide de makecert. Ces étapes ne sont pas spécifiques au modèle de déploiement. Ils sont valides pour le Gestionnaire de ressources et classique.

### <a name="to-create-a-self-signed-certificate"></a>Pour créer un certificat auto-signé

1. À partir d’un ordinateur exécutant Windows 10, téléchargez et installez les [Software Development Kit (SDK) Windows pour Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Après l’installation, vous trouverez l’utilitaire makecert.exe sous ce chemin d’accès : C:\Program Files (x86) \Windows Kits\10\bin\<arch >. 
        
    Exemple :`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Ensuite, créer et installer un certificat dans le magasin de certificats personnel sur votre ordinateur. L’exemple suivant crée un fichier *.cer* correspondant que vous téléchargez vers Azure lors de la configuration de P2S. Exécutez la commande suivante, en tant qu’administrateur. Remplacez *ARMP2SRootCert* et *ARMP2SRootCert.cer* par le nom que vous souhaitez utiliser pour le certificat.<br><br>Le certificat se situeront dans vos certificats - volet en cours.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Pour obtenir la clé publique

Dans le cadre de la configuration de la passerelle VPN pour les connexions Site-à-Point, la clé publique pour le certificat racine est téléchargée vers Azure.

1. Pour obtenir un fichier .cer à partir du certificat, ouvrez **certmgr.msc**. Cliquez sur le certificat racine auto-signé et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. L' **Assistant Exportation de certificat**s’ouvre.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis cliquez sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base-64 encodé X.509 (. CER).** Ensuite, cliquez sur **suivant**. 

4. Du **fichier à exporter**, **accédez** à l’emplacement auquel vous souhaitez exporter le certificat. **Nom de fichier**, nommez le fichier de certificat. Puis cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

 
### <a name="export-the-self-signed-certificate-optional"></a>Exportez le certificat auto-signé (facultatif)

Vous souhaiterez peut-être exporter le certificat auto-signé et stocker en toute sécurité. Si besoin est, vous pouvez ultérieurement l’installer sur un autre ordinateur et générer plus de certificats client ou exporter un autre fichier .cer. N’importe quel ordinateur avec un certificat client installé et qui est également configuré avec le bon client VPN paramètres peuvent se connecter à votre réseau virtuel via P2S. Pour cette raison, vous souhaitez vous assurer que les certificats clients sont générés et installés uniquement si nécessaire et que le certificat auto-signé est stocké en toute sécurité.

Pour exporter le certificat auto-signé sous la forme d’un fichier .pfx, sélectionnez le certificat racine et utiliser les mêmes étapes, comme décrit dans [l’exportation d’un certificat client](#clientkey) à exporter.

## <a name="create-and-install-client-certificates"></a>Créer et installer des certificats clients

Vous ne pas installer le certificat auto-signé directement sur l’ordinateur client. Vous devez générer un certificat client à partir du certificat auto-signé. Puis, vous exportez et installez le certificat client sur l’ordinateur client. Les étapes suivantes ne sont pas spécifiques au modèle de déploiement. Ils sont valides pour le Gestionnaire de ressources et classique.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Partie 1 - générer un certificat client à partir d’un certificat auto-signé

Les étapes suivantes vous guident tout au long une façon de générer un certificat client à partir d’un certificat auto-signé. Vous pouvez générer plusieurs certificats clients à partir du même certificat. Chaque certificat client peut être exporté, puis installé sur l’ordinateur client. 

1. Sur le même ordinateur que vous avez utilisé pour créer le certificat auto-signé, ouvrez une invite de commande en tant qu’administrateur.

2. Dans cet exemple, « ARMP2SRootCert » désigne le certificat auto-signé que vous avez généré. 
    - Remplacez *« ARMP2SRootCert »* le nom de la racine auto-signé que vous générez le certificat client à partir de. 
    - Remplacez *ClientCertificateName* par le nom que vous souhaitez générer un certificat de client. 


    Modifier et exécuter l’exemple pour générer un certificat client. Si vous exécutez l’exemple de suivant sans le modifier, le résultat est un certificat client nommé ClientCertificateName dans votre magasin de certificats personnel qui a été généré à partir du certificat ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Tous les certificats sont stockés dans votre « certificats - volet actuel' stocker sur votre ordinateur. Vous pouvez générer comme bien plusieurs certificats clients si nécessaire en fonction de cette procédure.

### <a name="clientkey"></a>Partie 2 - exportation d’un certificat client

1. Pour exporter un certificat client, ouvrez **certmgr.msc**. Cliquez droit sur le certificat du client que vous souhaitez exporter et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. L' **Assistant Exportation de certificat**s’ouvre.

2. Dans l’Assistant, cliquez sur **suivant**, puis sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , vous pouvez laisser les valeurs par défaut sélectionnées. Puis cliquez sur **suivant**. 
 
4. Dans la page **sécurité** , vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à les enregistrer ou de mémoriser le mot de passe que vous définissez pour ce certificat. Puis cliquez sur **suivant**.

5. Du **fichier à exporter**, **accédez** à l’emplacement auquel vous souhaitez exporter le certificat. **Nom de fichier**, nommez le fichier de certificat. Puis cliquez sur **suivant**.

6. Cliquez sur **Terminer** pour exporter le certificat.  

### <a name="part-3---install-a-client-certificate"></a>Partie 3 : installation d’un certificat client

Chaque client que vous souhaitez connecter à votre réseau virtuel à l’aide d’une connexion Point-à-Site doit disposer d’un certificat client est installé. Ce certificat est en plus du package de configuration VPN requis. Les étapes suivantes vous guident tout au long de l’installation manuelle de certificat client.

1. Recherchez et copiez le fichier *.pfx* sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laisser l' **Emplacement de stockage** en tant **qu’Utilisateur actuel**, puis cliquez sur **suivant**.

2. Sur le **fichier** à importer de la page, ne modifiez pas. Cliquez sur **suivant**.

3. Sur la page de **protection par clé privée** , entrez le mot de passe pour le certificat si vous utilisé, ou vérifiez que l’entité de sécurité qui installe le certificat est correcte, puis cliquez sur **suivant**.

4. Dans la page **Magasin de certificats** , laissez l’emplacement par défaut, puis cliquez sur **suivant**.

5. Cliquez sur **Terminer**. Dans l' **Avertissement de sécurité** pour l’installation du certificat, cliquez sur **Oui**. Le certificat n’est maintenant importé avec succès.

## <a name="next-steps"></a>Étapes suivantes

Continuer avec la configuration de Point-à-Site. 

- Pour les étapes du modèle de déploiement de **Gestionnaire de ressources** , consultez [configurer une connexion Point-à-Site à un VNet à l’aide de PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Pour les étapes du modèle de déploiement **classique** , consultez [un Point-à-Site de configurer une connexion VPN avec un VNet de via le portail classique](vpn-gateway-point-to-site-create.md).