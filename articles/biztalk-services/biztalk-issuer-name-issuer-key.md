<properties 
    pageTitle="Nom de l’émetteur et la clé de l’émetteur dans les Services BizTalk | Microsoft Azure" 
    description="Découvrez comment récupérer le nom de l’émetteur et la clé de l’émetteur de Bus des services ou de contrôle d’accès (ACS) dans les Services BizTalk. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur

Les Services BizTalk Azure utilise le nom de l’émetteur de Bus de Service et clé de l’émetteur et le nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur. En particulier :

Tâche | Les nom de l’émetteur et la clé de l’émetteur
--- | ---
Déploiement de votre application à partir de Visual Studio | Nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur
Configuration du portail de Services BizTalk Azure | Nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur
Création des relais de cœur de métier avec les Services de l’adaptateur BizTalk dans Visual Studio | Nom de l’émetteur de Bus de service et de la clé de l’émetteur

Cette rubrique répertorie les étapes pour récupérer le nom de l’émetteur et la clé de l’émetteur. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur
Le nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur sont utilisées par le texte suivant :

- Votre application Azure BizTalk Service créée dans Visual Studio : pour déployer avec succès votre application de BizTalk Service dans Visual Studio sur Azure, vous permet d’entrer le nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur. 
- Le portail de Services BizTalk Azure : Lorsque vous créez un BizTalk Service et ouvrez le portail de Services BizTalk, votre nom d’émetteur de contrôle de l’accès et la clé de l’émetteur sont automatiquement enregistrées pour vos déploiements avec les mêmes valeurs de contrôle d’accès.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Pour copier et coller le nom de l’émetteur de contrôle de l’accès et la clé de l’émetteur

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **Services de BizTalk**.
3. Sélectionnez votre Service BizTalk. 
4. Dans la barre des tâches, sélectionnez **Informations de connexion** . Le Namespace de contrôle de l’accès, par défaut l’émetteur (nom de l’émetteur) et par défaut de clé (clé de l’émetteur) sont répertoriés et peuvent être copié et collé.  

Pour résumer :  
Nom de l’émetteur = par défaut émetteur  
Clé de l’émetteur = clé par défaut


Vous pouvez également sélectionner le **Portail de gestion ouverte ACS** pour obtenir les valeurs de contrôle d’accès :

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **Services de BizTalk**.
3. Sélectionnez votre Service BizTalk.
4. Cliquez sur le bouton informations de connexion et sélectionnez **Portail de gestion ouverte ACS**.
5. Dans le portail sous **Paramètres de Service**, sélectionnez les **Identités de Service**. Cela affiche votre identité de Service, qui est la valeur du nom de l’émetteur de contrôle de l’accès. Sélectionnez le lien de l’identité du Service pour afficher le mot de passe, qui est la valeur de la clé de l’émetteur. Leurs valeurs peuvent être copiées.<br/><br/>
Par exemple, dans les **Identités de Service**, voir « propriétaire ». « Propriétaire » est votre nom d’émetteur de contrôle de l’accès. Lorsque vous cliquez sur le lien « propriétaire », vous voyez le **mot de passe**. Lorsque vous cliquez sur le lien « Password », vous voyez la valeur. Cette valeur de mot de passe est votre clé d’accès contrôle l’émetteur.  

Pour résumer :  
Nom de l’émetteur = nom de l’identité du Service  
Clé de l’émetteur = valeur de mot de passe

Dans le volet de navigation de gauche, vous pouvez également sélectionner **Active Directory** pour récupérer les valeurs de contrôle d’accès. 

> [AZURE.IMPORTANT]Lorsqu’un Namespace de contrôle d’accès est créée à l’aide **d’Active Directory**, une identité de Service est **pas** automatiquement créée. Lorsque vous configurez un BizTalk Service, un Namespace de contrôle d’accès, l’identité du Service nommé « propriétaire » (nom de l’émetteur), mot de passe (clé de l’émetteur), et la clé symétrique sont créés automatiquement.<br /> 
[Comment : utiliser le Service de gestion ACS pour configurer les identités de Service](http://go.microsoft.com/fwlink/p/?LinkID=303942) fournit des informations sur les identités de Service de contrôle d’accès.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Nom de l’émetteur de Bus de service et de la clé de l’émetteur
Nom de l’émetteur de Bus de service et de la clé de l’émetteur sont utilisés par les Services de l’adaptateur BizTalk. Dans votre projet BizTalk Services dans Visual Studio, vous utilisez les Services de l’adaptateur BizTalk pour se connecter à un système de ligne d’activité (LOB) sur site. Pour vous connecter, vous créez le relais LOB et entrez les détails de votre système cœur de métier. Lors de cette opération, vous entrez également le nom de l’émetteur de Bus de Service et de la clé de l’émetteur.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Pour récupérer le nom de l’émetteur de Bus de Service et de la clé de l’émetteur

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez le **Bus de Service**.
3. Sélectionnez votre espace de noms. Dans la barre des tâches, sélectionnez **Informations de connexion**. Cela affiche **Par défaut de l’émetteur** (nom de l’émetteur) et **Par défaut de clé** (clé de l’émetteur). Leurs valeurs peuvent être copiées.  

Pour résumer :  
Nom de l’émetteur = par défaut émetteur  
Clé de l’émetteur = clé par défaut

## <a name="next"></a>Suivant
Rubriques Azure BizTalk Services supplémentaires :

-  [L’installation des Services BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Les Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Voir aussi
-  [Comment : utiliser le Service de gestion d’ACS pour configurer les identités de Service](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Les Services BizTalk : Développeur, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Les Services BizTalk : Mise en service de portail de classique à l’aide de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Services de BizTalk : Graphique de l’état de mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Services de BizTalk : Sauvegarde et restauration](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Les Services BizTalk : limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
