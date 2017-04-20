<properties 
    pageTitle="Serveur d’authentification à plusieurs facteurs d’authentification LDAP et Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui les aidera dans le déploiement de l’authentification LDAP et Azure plusieurs facteurs d’authentification serveur."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Serveur d’authentification à plusieurs facteurs d’authentification LDAP et Azure


Par défaut, le serveur de l’authentification multifactorielle Azure est configuré pour l’importation ou de synchroniser les utilisateurs à partir d’Active Directory. Toutefois, il peut être configuré pour créer une liaison avec les différents annuaires LDAP, comme un annuaire ADAM ou le contrôleur de domaine Active Directory spécifique. Lorsqu’il est configuré pour se connecter à un répertoire via LDAP, Azure plusieurs facteurs d’authentification serveur peut être configuré pour se comporter comme un serveur de proxy LDAP pour effectuer les authentifications. Il permet également l’utilisation de la liaison LDAP comme cible de RADIUS, pour authentification préalable des utilisateurs lors de l’utilisation de l’authentification IIS, ou d’authentification principal dans le portail Azure plusieurs facteurs d’authentification utilisateur.

Lorsque vous utilisez l’authentification à plusieurs facteurs Azure comme un proxy LDAP, le serveur de l’authentification multifactorielle Azure est inséré entre le client LDAP (par exemple, un appareil VPN, application) et le serveur d’annuaire LDAP pour ajouter l’authentification à plusieurs facteurs. Pour une authentification à plusieurs facteurs Azure de la fonction, le serveur de l’authentification multifactorielle Azure doit être configuré pour communiquer avec les serveurs du client et de l’annuaire LDAP. Dans cette configuration, le serveur de l’authentification multifactorielle Azure accepte les demandes LDAP à partir des serveurs du client et les applications et les transmet au serveur de répertoire LDAP cible pour valider les informations d’identification primaires. Si la réponse à partir de l’annuaire LDAP indique qu’ils principales d’informations d’identification sont valides, authentification à plusieurs facteurs Azure exécute la seconde authentification et envoie une réponse au client LDAP. L’authentification complète réussira uniquement si l’authentification au serveur LDAP et l’authentification à plusieurs facteurs de réussite.





## <a name="ldap-authentication-configuration"></a>Configuration de l’authentification LDAP


Pour configurer l’authentification LDAP, installez le serveur de l’authentification multifactorielle Azure sur un serveur Windows. Utilisez la procédure suivante :

1. Au sein du serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de l’authentification LDAP dans le menu de gauche.
2. Cochez la case Activer l’authentification LDAP.![Authentification LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Sous l’onglet Clients modifier le port TCP et le port SSL si le service Azure plusieurs facteurs d’authentification LDAP doit être lié à des ports non standard pour écouter les demandes LDAP à partir des clients qui seront configurés.
4. Si vous envisagez d’utiliser LDAPS à partir du client vers le serveur de l’authentification multifactorielle Azure, un certificat SSL doit être installé sur le serveur qui exécute sur le serveur. Cliquez sur Parcourir... bouton en regard de la case du certificat SSL et sélectionnez le certificat installé qui sera utilisé pour la connexion sécurisée.
5. Cliquez sur Ajouter... bouton.
6. Dans la boîte de dialogue Ajouter un Client LDAP, entrez l’adresse IP de la solution matérielle-logicielle, le serveur ou l’application qui s’authentifient au serveur et un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports d’authentification à plusieurs facteurs Azure et peut être affiché dans les messages d’authentification SMS ou application Mobile.
7. La case Exiger l’authentification selon plusieurs facteurs Azure utilisateur correspondance si tous les utilisateurs ont été ou seront importés sur le serveur et de l’authentification à facteurs multiples se. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou est exempté de l’authentification à facteurs multiples se, laissez la case désactivée. Consultez le fichier d’aide pour plus d’informations sur cette fonctionnalité.
8. Vous pouvez répéter les étapes 5 à 7 pour ajouter d’autres clients LDAP.
9. Lorsque l’authentification à plusieurs facteurs Azure est configurée pour recevoir les authentifications LDAP, il doit proxy les authentifications à l’annuaire LDAP. Par conséquent, l’onglet cible s’affiche uniquement un seul, désactivé permet d’utiliser une cible LDAP. Pour configurer la connexion d’annuaire LDAP, cliquez sur l’icône de l’intégration d’annuaire.
10. Sous l’onglet Paramètres, sélectionnez le bouton radio utiliser une configuration LDAP spécifique.
11. Cliquez sur l’édition... bouton.
12. Dans la boîte de dialogue Modifier la Configuration de LDAP, remplissez les champs avec les informations requises pour se connecter à l’annuaire LDAP. Descriptions des champs sont incluses dans le tableau ci-dessous. Remarque : Cette information est également incluse dans le fichier d’aide Azure plusieurs facteurs d’authentification serveur.![Intégration d’annuaire](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Tester la connexion LDAP en cliquant sur le bouton Test.
14. Si le test de la connexion LDAP a réussi, cliquez sur le bouton OK.
15. Cliquez sur l’onglet filtres. Le serveur est préconfiguré pour charger des conteneurs, des utilisateurs et des groupes de sécurité à partir d’Active Directory. Si une liaison vers un autre répertoire LDAP, vous devrez probablement modifier les filtres d’affichage. Cliquez sur le lien aide pour plus d’informations sur les filtres.
16. Cliquez sur onglet attributs. Le serveur est préconfiguré pour mapper les attributs de Active Directory.
17. Si une liaison vers un autre répertoire LDAP ou pour modifier les mappages d’attributs préconfigurée, cliquez sur l’édition... bouton.
18. Dans la boîte de dialogue Modifier les attributs, modifiez les mappages d’attributs LDAP de votre annuaire. Les noms d’attributs peuvent être tapés dans ou sélectionnés en cliquant sur le... bouton en regard de chaque champ.
19. Cliquez sur le lien aide pour plus d’informations sur les attributs.
20. Cliquez sur le bouton OK.
21. Cliquez sur l’icône des paramètres de la société et sélectionnez l’onglet résolution de nom d’utilisateur.
22. Si la connexion à Active Directory à partir d’un serveur à un domaine, il se peut que vous devez être en mesure de laisser les identificateurs de sécurité (SID) Windows de l’utiliser pour la correspondance des noms d’utilisateur case d’option sélectionnée. Dans le cas contraire, sélectionnez l’attribut d’identificateur unique utiliser LDAP pour la case d’option noms d’utilisateur correspondant. Lorsqu’elle est sélectionnée, le serveur de l’authentification multifactorielle Azure tentera de résoudre chaque nom d’utilisateur à un identificateur unique dans l’annuaire LDAP. Une recherche LDAP sera effectuée sur le nom d’utilisateur, les attributs définis dans l’intégration d’annuaire -> onglet attributs. Lorsqu’un utilisateur s’authentifie, le nom d’utilisateur sera résolu en un identificateur unique dans l’annuaire LDAP et l’identificateur unique sera utilisé pour la correspondance de l’utilisateur dans le fichier de données d’authentification à plusieurs facteurs Azure. Ainsi, pour les comparaisons sans respecter la casse, comme nom d’utilisateur ainsi que long et court met en forme ce termine la configuration du serveur de l’authentification multifactorielle Azure. Le serveur est maintenant à l’écoute sur les ports configurés pour les demandes d’accès LDAP à partir des clients configurés et affectez ces demandes à l’annuaire LDAP pour l’authentification proxy.


## <a name="ldap-client-configuration"></a>Configuration des clients LDAP

Pour configurer le client LDAP, suivez les instructions :

- Configurez votre solution matérielle-logicielle, le serveur ou l’application à s’authentifier via LDAP sur le serveur de l’authentification multifactorielle Azure comme s’il s’agissait de votre répertoire LDAP. Vous devez utiliser les mêmes paramètres que vous utiliseriez normalement pour se connecter directement à votre annuaire LDAP, à l’exception du nom du serveur ou l’adresse IP qui correspond à celui du serveur d’authentification selon plusieurs facteurs Azure.
- Configurer le délai d’attente LDAP de 30 à 60 secondes, afin qu’il est temps de valider les informations d’identification de l’utilisateur avec l’annuaire LDAP, effectuez la seconde authentification, recevoir leur réponse et de répondre à la demande d’accès LDAP.
- Si vous utilisez le protocole LDAPS, le matériel ou le serveur effectue les requêtes LDAP doit approuver le certificat SSL installé sur le serveur de l’authentification multifactorielle Azure.
