<properties
   pageTitle="Gérer les enregistrements DNS inverses pour vos services Azure à l’aide de la CLI d’Azure | Microsoft Azure"
   description="Comment gérer les enregistrements DNS inversées ou enregistrements PTR pour les services Azure à l’aide de la CLI d’Azure dans le Gestionnaire de ressources"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Comment faire pour gérer les enregistrements DNS inverses pour vos services Azure à l’aide de la CLI d’Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validation de l’enregistrement DNS inverse
Pour garantir qu'un tiers ne peut pas créer l’enregistrement DNS inverse, mappage vers des domaines de votre DNS, Azure permet uniquement la création d’un enregistrement DNS inverse, où un des éléments suivants est vrai :

- Le « ReverseFqdn » est le même que le « nom de domaine complet » pour la ressource d’adresse IP publique pour lequel il a été spécifié, ou « Nom de domaine complet » pour n’importe quelle adresse IP publique au sein de l’abonnement même par exemple, « ReverseFqdn » est « contosoapp1.northus.cloudapp.azure.com. ».

- Le « ReverseFqdn » vers l’avant résout le nom ou l’adresse IP de l’adresse IP publique pour laquelle il a été spécifié, ou à n’importe quel IP ou l’adresse IP publique « Nom de domaine complet » dans l’abonnement même par exemple, « ReverseFqdn » est « app1.contoso.com ». qui est un alias CName pour « contosoapp1.northus.cloudapp.azure.com. »

Contrôles de validation effectuent uniquement lorsque la propriété DNS inversée pour une adresse IP publique est définie ou modifiée. Validation de réévaluation périodique n’est pas effectuée.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Ajouter DNS inversée sur les adresses IP publiques existantes
Vous pouvez ajouter DNS inversée pour une adresse IP publique existante en utilisant le jeu de public-ip réseau azure :

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Si vous souhaitez ajouter DNS inversée pour une adresse IP publique existante qui n’a pas encore un nom DNS, vous devez également spécifier un nom DNS. Vous pouvez ajouter cela en utilisant le jeu de public-ip réseau azure :

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Créer une adresse IP publique avec DNS inversée
Vous pouvez ajouter une nouvelle adresse IP publique avec la propriété DNS inverse spécifiée à l’aide de l’ip public azure réseau créer :

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Affichage DNS inverse pour les adresses IP publiques existantes
Vous pouvez afficher la valeur configurée pour une adresse IP publique existante azure réseau public ip afficher :

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Supprimer DNS inversée à partir des adresses IP publiques existantes
Vous pouvez supprimer une propriété DNS inversée à partir d’une adresse IP publique existante à l’aide d’azure réseau public ip ensemble. Pour ce faire, vous devez définir la valeur de la propriété ReverseFqdn à vide :

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]
