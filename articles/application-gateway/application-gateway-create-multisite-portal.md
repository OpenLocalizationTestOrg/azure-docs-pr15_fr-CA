<properties
   pageTitle="Configuration d’une passerelle d’application existants pour l’hébergement de plusieurs sites du portail Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour la configuration d’une passerelle d’application Azure existante pour l’hébergement de plusieurs applications web sur la même passerelle avec le portail Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configuration d’une passerelle d’application existants pour l’hébergement de plusieurs applications web

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-create-multisite-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Hébergement de plusieurs sites vous permet de déployer plusieurs applications web sur la même passerelle d’application. Il s’appuie sur la présence de l’en-tête de l’hôte dans la requête HTTP entrante, pour déterminer quel écouteur reçoit le trafic. L’écouteur dirige ensuite le trafic vers le pool de serveur principal approprié tel que configuré dans la définition de règles de la passerelle. Dans les applications web SSL activé, passerelle d’application dépend de l’extension de l’Indication de nom de serveur (SNI) pour choisir le port d’écoute approprié pour le trafic web. Une utilisation courante de l’hébergement de plusieurs sites est à la charge des demandes de solde pour les domaines web différents pour les pools des serveurs principaux différents. De la même façon plusieurs sous-domaines du domaine racine même peuvent également être hébergés sur la même passerelle d’application.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, passerelle d’application sert le trafic pour contoso.com et fabrikam.com avec deux pools de serveur back-end : contoso serveur pool et pool de serveur de fabrikam. Le programme d’installation similaire peut servir aux sous-domaines d’hôte telles que app.contoso.com et blog.contoso.com.

![scénario multisite][multisite]

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario ajoute la prise en charge de plusieurs sites à une passerelle d’application existant. Pour terminer ce scénario scénario une passerelle d’application doit être disponible à configurer. Visitez le site [créer une passerelle d’application via le portail](./application-gateway-create-gateway-portal.md) pour apprendre à créer une passerelle d’application de base dans le portail.

## <a name="requirements"></a>Configuration requise

- **Pool de serveurs back-end :** La liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent soit appartenir au sous-réseau de réseau virtuel ou doivent être un IP public/VIP. Nom de domaine complet peut également être utilisé.
- **Paramètres de pool de serveurs back-end :** Chaque pool dispose de paramètres comme port, le protocole et l’affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port front-end :** Ce port est le port public qui est ouvert sur la passerelle d’application. Le trafic atteint ce port et qui obtient ensuite redirigé vers l’un des serveurs principaux.
- **Récepteur :** L’écouteur a un port front-end, un protocole (Http ou Https, ces valeurs sont sensibles à la casse) et le nom du certificat SSL (si le décharge de la configuration de SSL). Pour les passerelles d’application activée de plusieurs sites, nom d’hôte et les indicateurs SNI sont également ajoutés.
- **Règle :** La règle lie l’écouteur, le pool de serveur principal et définit le pool de serveur back-end le trafic doit être dirigé vers lorsqu’il rencontre un écouteur particulier.
- **Les certificats :** Chaque écouteur requiert un certificat unique, dans cet exemple, 2 écouteurs sont créés pour plusieurs sites. Deux certificats .pfx et les mots de passe pour eux doivent être créés.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Les étapes nécessaires à la mise à jour de la passerelle d’application sont les suivantes :

1. Créer des regroupements de back-end à utiliser pour chaque site.
2. Créer un nouveau port d’écoute pour chaque passerelle d’application site prendra en charge.
3. Créer des règles pour mapper chaque écouteur et le back-end approprié.

## <a name="create-back-end-pools-for-each-site"></a>Créer des pools de back-end pour chaque site

Un pool principal pour chaque site sera de cette passerelle d’application prise en charge est nécessaire, dans ce cas 2 seront créées, une pour la contoso11.com et l’autre pour fabrikam11.com.

### <a name="step-1"></a>Étape 1

Accédez à une passerelle d’application existants dans le portail Azure (https://portal.azure.com). **Pools de serveur principal** , cliquez sur **Ajouter**

![ajouter des pools de back-end][7]

### <a name="step-2"></a>Étape 2

Remplissez les informations pour le back-end pool **pool1**, ajoutez les adresses ip ou les noms de domaine complets des serveurs principaux et cliquez sur **OK**

![paramètres de pool de pool1 back-end][8]

### <a name="step-3"></a>Étape 3

Sur la lame de pools-back-end, cliquez sur **Ajouter** pour ajouter un pool de back-end supplémentaires **pool2**, ajoutez les adresses ip ou les noms de domaine complets des serveurs principaux et cliquez sur **OK**

![paramètres de pool de pool2 back-end][9]

### <a name="create-listeners-for-each-back-end"></a>Créer des récepteurs pour chaque back-end

Passerelle d’application s’appuie sur les en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites Web sur la même adresse IP publique et un port. L’écouteur de base créée dans le portail ne contient-elle pas cette propriété.

### <a name="step-1"></a>Étape 1

**Les écouteurs** sur la passerelle d’application existant puis cliquez sur **plusieurs sites** pour ajouter le premier écouteur.

![lame de vue d’ensemble des écouteurs][1]

### <a name="step-2"></a>Étape 2

Remplissez les informations pour le port d’écoute, dans cet exemple, arrêt de SSL est configuré, créez un nouveau port frontal. Télécharger le certificat .pfx à utiliser pour l’arrêt de SSL. La seule différence sur cette blade par rapport à la lame d’écouteur de base standard est le nom d’hôte.

![lame de propriétés de port d’écoute][2]

### <a name="step-3"></a>Étape 3

Cliquez sur **plusieurs sites** et un autre écouteur comme décrit à l’étape précédente pour le deuxième site. Veillez à utiliser un certificat différent pour l’écouteur de la deuxième. La seule différence sur cette blade par rapport à la lame d’écouteur de base standard est le nom d’hôte. Remplissez les informations pour le port d’écoute, puis cliquez sur **OK**.

![lame de propriétés de port d’écoute][3]

> [AZURE.NOTE] Création de ports d’écoute dans Azure portal pour la passerelle d’application est une tâche de longue durée, il peut prendre un certain temps pour créer les deux écouteurs dans ce scénario. Une fois terminée l’émission des écouteurs dans le portail, comme illustré dans l’image suivante.

![vue d’ensemble de l’écouteur][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Créer des règles pour mapper les ports d’écoute aux pools de back-end

### <a name="step-1"></a>Étape 1

Accédez à une passerelle d’application existants dans le portail Azure (https://portal.azure.com). Sélectionnez **règles** et choisir la de règle par défaut **règle1** existant et cliquez sur **Modifier**.

### <a name="step-2"></a>Étape 2

Remplissez la lame de règles, comme illustré dans l’image suivante. Choisir le premier écouteur et le premier pool en cliquant sur **Enregistrer** lorsque vous avez terminé.

![modifier une règle existante][6]

### <a name="step-3"></a>Étape 3

Cliquez sur la **règle de base** pour créer la règle 2. Remplir le formulaire avec le deuxième port d’écoute et un deuxième pool de serveur principal, puis cliquez sur **OK** pour enregistrer.

![ajouter des lames de la règle de base][10]

Cela termine la configuration d’une passerelle d’application existante avec prise en charge de plusieurs sites via le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment protéger vos sites Web à une [Application passerelle - pare-feu pour applications Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png