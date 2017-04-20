<properties
   pageTitle="Configuration nommée d’identification | Microsoft Azure"
   description="Apprenez à pour fournir des informations d’identification que Visual Studio peut utiliser pour authentifier les demandes sur Azure pour publier une application dans Azure à partir de Visual Studio ou à surveiller un service cloud existant... "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="setting-up-named-authentication-credentials"></a>Configuration des informations d’authentification nommée

Pour publier une application dans Azure à partir de Visual Studio ou à surveiller un service cloud existant, vous devez fournir des informations d’identification que Visual Studio peut utiliser pour authentifier les demandes sur Azure. Il y a plusieurs endroits dans Visual Studio, où vous pouvez signer de fournir ces informations d’identification. Par exemple, à partir de l’Explorateur de serveurs, vous pouvez ouvrir le menu contextuel pour le nœud **Azure** et choisissez **se connecter vers Azure**. Lorsque vous vous connectez, les informations d’abonnement associées à votre compte Azure sont disponibles dans Visual Studio, et il n’y a rien de plus, que vous devez faire.

Outils Azure prend également en charge un ancien moyen de fournir des informations d’identification, en utilisant le fichier d’abonnement (fichier .publishsettings). Cette rubrique décrit cette méthode, qui est toujours pris en charge dans Azure SDK 2.2.

Les éléments suivants sont requis pour l’authentification sur Azure.

- Votre ID d’abonnement

- Un certificat X.509 v3 valide

>[AZURE.NOTE] La longueur de clé du certificat X.509 v3 doit être d’au moins 2048 bits. Azure rejette tout certificat qui ne répond pas à cette exigence, ou qui n’est pas valide.

Visual Studio utilise votre ID d’abonnement avec les données de certificat en tant qu’informations d’identification. Les informations d’identification appropriées sont référencées dans le fichier d’abonnement (fichier .publishsettings), qui contient une clé publique pour le certificat. Le fichier d’abonnement peut contenir des informations d’identification de plus d’un abonnement.

Vous pouvez modifier les informations d’abonnement à partir de la boîte de dialogue **Créer/modifier abonnement** , comme expliqué plus loin dans cette rubrique.

Si vous souhaitez créer un certificat vous-même, vous pouvez voir les instructions de la [créer et télécharger un certificat de gestion pour Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) et ensuite télécharger manuellement le certificat au [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Ces informations d’identification que Visual Studio a besoin pour gérer vos services de nuage ne sont pas les mêmes informations d’identification qui sont nécessaires pour authentifier une demande aux services de stockage Azure.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modifier ou exporter des informations d’identification dans Visual Studio

Vous pouvez également définir, modifier ou exporter vos informations d’identification dans la boîte de dialogue **Nouvel abonnement** qui s’affiche si vous effectuez une des actions suivantes :

- Dans l' **Explorateur de serveurs**, ouvrez le menu contextuel pour le nœud **d’Azure** , cliquez sur **Gérer les abonnements**, cliquez sur l’onglet **certificats** et cliquez sur le bouton **Nouveau** ou **Modifier** .

- Lorsque vous publiez un service cloud Azure à partir de l’Assistant **Publier un Application Azure** , choisissez **Gérer** dans la liste **Choisir votre abonnement** , cliquez sur l’onglet certificats, puis cliquez sur le bouton **Nouveau** ou **Modifier** .

La procédure suivante suppose que la boîte de dialogue **Nouvel abonnement** est ouverte.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Pour configurer les informations d’identification dans Visual Studio

1. Dans **Sélectionner un certificat existant** pour la liste d’authentification, sélectionnez un certificat.

1. Cliquez sur le bouton **Copier le chemin d’accès complet** . Le chemin d’accès du certificat (fichier .cer) est copié dans le Presse-papiers.

    >[AZURE.IMPORTANT] Pour publier votre application Azure à partir de Visual Studio, vous devez télécharger ce certificat au [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Pour télécharger le certificat au [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Cliquez sur le lien Azure Portal.

         Le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885) s’ouvre.

    1. Connectez-vous au [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885)et cliquez sur le bouton **Services en nuage** .

    1. Choisissez le service de cloud qui vous intéresse.

        La page du service s’ouvre.

    1. Sur l’onglet **certificats** , cliquez sur le bouton **Télécharger** .

    1. Collez le chemin d’accès complet du fichier .cer que vous venez de créer, puis entrez le mot de passe que vous avez spécifié.
