<properties
   pageTitle="Procédure pas à pas détaillé de l’utilisation de l’aperçu de collaboration Azure Active Directory B2B | Microsoft Azure"
   description="Collaboration d’Active Directory B2B Azure prend en charge vos relations intersociétés en activant les partenaires commerciaux de manière sélective accéder à vos applications d’entreprise"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Aperçu de collaboration B2B de publicité Azure : procédure pas à pas détaillé

Cette procédure pas à pas explique comment utiliser la collaboration d’Azure AD B2B. Comme l’administrateur de Contoso, nous voulons partager des applications avec des employés à partir de trois sociétés partenaires. Aucune des sociétés partenaires doivent disposer d’Azure AD.

- Alice à partir de l’organisation de simplicité pour les partenaires
- Bob, à partir de l’organisation partenaire de support, a besoin d’accéder à un ensemble d’applications
- Carol, à partir de complexes partenaire Org, a besoin d’accéder à un ensemble d’applications et l’appartenance aux groupes de Contoso

Après que invitations sont envoyées aux utilisateurs partenaire, nous pouvons les configurer dans Azure AD pour accorder l’accès aux applications et à l’appartenance aux groupes via le portail Azure. Commençons par ajouter Alice.

## <a name="adding-alice-to-the-contoso-directory"></a>Ajout d’Alice dans le répertoire de Contoso
1. Créer un fichier .csv avec les en-têtes comme indiqué, ne remplissage qu’Alice **E-mail**, **DisplayName**et **InviteContactUsUrl**. **DisplayName** est le nom qui apparaît dans l’invitation, ainsi que le nom qui apparaît dans le répertoire de Contoso AD Azure. **InviteContactUsUrl** est un moyen pour Alice contacter Contoso. Dans l’exemple suivant, InviteContactUsUrl Spécifie le profil LinkedIn de Contoso. Il est important de l’orthographe dans les étiquettes dans la première ligne du fichier .csv exactement comme spécifié dans la [référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md).  
![Exemple de fichier CSV pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Dans le portail Azure, ajouter un utilisateur dans le répertoire de Contoso (Active Directory > Contoso > utilisateurs > Ajouter un utilisateur). Dans la liste déroulante « Type d’utilisateur », sélectionnez « Utilisateurs dans les sociétés partenaires ». Téléchargez le fichier .csv. Assurez-vous que le fichier .csv est fermé avant de le télécharger.  
![Chargement du fichier CSV pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice est maintenant représentée par un utilisateur externe dans le répertoire de Contoso AD Azure.  
![Alice est répertoriée dans AD Azure](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice reçoit l’e-mail suivant.  
![Courrier électronique d’invitation pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice clique sur le lien, et il est invitée à accepter l’invitation et à vous connecter à l’aide de ses informations d’identification du travail. Si Alice n’est pas dans l’annuaire AD Azure, Alice est invitée à s’inscrire.  
![Inscrire après invitation pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice est redirigée vers le panneau d’accès App, vide jusqu'à ce qu’elle bénéficie de l’accès aux applications.  
![Panneau d’accès pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Cette procédure permet à la forme la plus simple des collaboration B2B. En tant qu’utilisateur dans le répertoire de Contoso AD Azure, Alice peut accéder aux applications et aux groupes via le portail Azure. Maintenant nous allons ajouter Bob, qui a besoin d’accéder aux applications Moodle et la force de vente.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>Ajout de Bob dans le répertoire de Contoso et accorder l’accès aux applications
1. Utilisation de Windows PowerShell avec le Module AD Azure pour rechercher l’ID d’application de Moodle et de la force de vente. Les identificateurs peuvent être récupérées à l’aide de l’applet de commande : `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` s’affiche une liste de toutes les applications disponibles dans Contoso et leurs AppPrincialIds.  
![Extraire les ID pour Bob](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Créez un fichier .csv contenant des E-mails Bob et DisplayName, **InviteAppID**, **InviteAppResources**et InviteContactUsUrl. Remplir **InviteAppResources** avec le AppPrincipalIds de Moodle et de la force de vente trouvée à partir de PowerShell, séparé par un espace. Remplir **InviteAppId** avec la même AppPrincipalId de Moodle marque l’e-mail et de signer dans les pages avec un logo de Moodle.  
![Exemple de fichier CSV à Bob](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Télécharger le fichier .csv via le portail Azure comme il a été fait pour Alice. Bob est désormais un utilisateur externe dans le répertoire de Contoso AD Azure.

4. Bob reçoit le message suivant.  
![Courrier électronique d’invitation de Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob clique sur le lien et est invité à accepter l’invitation. Une fois qu’il est connecté, il est dirigé vers le panneau d’accès et permet déjà de Moodle et la force de vente.  
![Panneau d’accès pour Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Nous allons ajouter Carol suivant, qui a besoin d’accéder à des applications, ainsi que l’appartenance aux groupes dans le répertoire de Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Ajout de Carol au répertoire Contoso, accorder l’accès aux applications et accordant l’appartenance de groupe

1. Utiliser Windows PowerShell avec le Module AD Azure pour rechercher l’ID d’application et l’ID de groupe au sein de Contoso.
 - Récupérer à l’aide de la cmdlet de AppPrincipalId `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, de même que pour Bob
 - Récupérer un ObjectId de groupes à l’aide de la cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Elle affiche une liste de tous les groupes de Contoso et leurs ObjectID. ID de groupe peuvent également être récupérées en tant que l’ID d’objet dans l’onglet Propriétés du groupe dans le portail Azure.  
![Récupérer des codes et des groupes pour Carol](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Créer un fichier .csv, remplissage de Carol E-mail DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources**et InviteContactUsUrl. **InviteGroupResources** est complété par l’ObjectID des groupes MyGroup1 et externes, séparés par un espace.  
![Exemple de fichier CSV pour Carol](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Téléchargez le fichier .csv via le portail Azure.

4. Carol est un utilisateur dans le répertoire de Contoso et est également membre des groupes MyGroup1 et externes, comme dans le portail Azure.  
![Carol est répertoriée dans un groupe dans AD Azure](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol reçoit un e-mail contenant un lien pour accepter l’invitation. Une fois qu’elle se connecte, elle est redirigée vers le panneau d’accès application d’accéder à Moodle et la force de vente.  

C’est tout pour l’ajout d’utilisateurs à partir des entreprises partenaires en collaboration d’Azure AD B2B. Cette procédure pas à pas vous a montré comment ajouter des utilisateurs, Alice, Bob et Carol dans le répertoire de Contoso à l’aide de trois fichiers .csv distincts. Ce processus peut être facilité par la condensation les fichiers .csv distincts en un seul fichier.  
![Exemple de fichier CSV pour Alice, Bob et Carol](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Articles connexes
Parcourir nos autres articles sur Azure AD B2B collaboration :

- [Quelle est la collaboration d’Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limites d’aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
