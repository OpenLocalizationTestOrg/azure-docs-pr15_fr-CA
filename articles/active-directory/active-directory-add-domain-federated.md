<properties
    pageTitle="Ajoutez votre nom de domaine personnalisé et définir ouverture de session fédérés sur Azure Active Directory | Microsoft Azure"
    description="Comment ajouter les noms de domaine de votre société à Azure Active Directory et la configuration fédérée de session entre Azure Active Directory et de votre solution de fédération local."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Ajoutez votre nom de domaine personnalisé pour Azure Active Directory

Vous pouvez configurer un nom de domaine personnalisé, par exemple, « contoso.com », afin que les utilisateurs dans contoso.com peuvent avoir un fédéré unique ouvertures de session à partir de votre réseau d’entreprise. Si vous avez déjà des Services de fédération Active Directory (Active Directory Federation Services) ou un serveur de fédération en cours d’exécution sur votre réseau d’entreprise, vous pouvez configurer AD Azure pour utiliser votre nom de domaine personnalisé à l’aide de l’outil Azure Connect d’Active Directory. Vous pouvez également utiliser Azure Connect d’Active Directory pour déployer un nouvel environnement AD FS et qui configurer pour fédéré de l’authentification unique pour Azure AD.

Si vous n’avez pas et que vous n’envisagez pas de déployer AD FS ou un autre serveur de fédération, suivez ces instructions : [Ajouter un nom de domaine personnalisé pour Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Ajouter un nom de domaine personnalisé à votre répertoire

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com/) avec un compte d’utilisateur est un administrateur global de votre annuaire AD Azure.

2. Dans **Active Directory**, ouvrez votre répertoire et sélectionnez l’onglet **domaines** .

3. Dans la barre de commande, sélectionnez **Ajouter**, puis entrez le nom de votre domaine personnalisé, par exemple, « contoso.com ». Veillez à inclure le .com, .net ou une autre extension de niveau supérieur.

4. Activez la case à cocher **planifier configurer ce domaine pour l’authentification unique avec mon Active Directory local** .

5. Sélectionnez **Ajouter**.

Exécutez l’outil Azure Connect d’Active Directory pour obtenir l’entrée DNS qui annonce Azure permet de vérifier le domaine. Vous verrez l’entrée DNS dans le **Domaine Active Directory de Azure** étape de l’Assistant. Vous pouvez voir que cette étape de l’Assistant ressemble [dans ces instructions](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si vous ne disposez pas de l’outil Azure Connect d’Active Directory, vous pouvez [le télécharger ici](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajoutez l’entrée DNS dans le Registre des noms de domaine pour le domaine

L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD est mise à jour le fichier de zone DNS pour le domaine. Cela permet d’Azure AD vérifier que votre organisation possède le nom de domaine personnalisé.

1. Ouvrez une session sur le site Web pour le Registre des noms de domaine pour votre nom de domaine. Si vous n’avez pas accès pour ce faire, demandez à la personne ou l’équipe de votre organisation qui a accès à l’étape 2 et pour vous avertir lorsqu’il est terminé.

2. Mettre à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par AD Azure. Cette entrée DNS permet d’Azure AD vérifier que vous possédez du domaine. L’entrée DNS ne change pas les comportements, tels que le routage de courrier ou l’hébergement web.

Pour l’aide sur cette étape, lire les [Instructions pour l’ajout d’une entrée DNS sur les bureaux d’enregistrement DNS les plus courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifiez le nom de domaine avec Active Directory Azure

Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Active Directory Azure.

Pour vérifier le domaine, cliquez sur **suivant** à l’étape de **Domaine AD de Azure** de l’Assistant d’Azure Connect d’Active Directory. Annonce Azure recherchera l’entrée DNS dans le fichier de zone DNS pour le domaine. Annonce Azure vérifier uniquement le nom de domaine une fois les enregistrements DNS ont été propagées. Souvent la propagation ne prend que quelques secondes, mais il peut parfois prendre une heure ou plus. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

Poursuivez les étapes restantes de l’Assistant d’Azure Connect d’Active Directory. Les utilisateurs de votre Active Directory du serveur Windows Azure AD sont alors synchronisées. Synchronisé dans le domaine que vous avez configurée pour la fédération pourront obtenir fédérés une seule expérience de session à partir de votre réseau d’entreprise pour Azure AD.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne pouvez pas vérifier un nom de domaine personnalisé, essayez ce qui suit. Nous allons commencer par les plus courants et fonctionne sur les moins courantes.

1.  **Patientez une heure**. Enregistrements DNS ont besoin de se propager avant AD Azure peut vérifier le domaine. Cette opération peut prendre une heure ou plus.

2.  **Assurez-vous que l’enregistrement DNS a été saisie, et qu’il est correct**. Effectuez cette étape sur le site Web pour le Registre des noms de domaine pour le domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS n’est pas présente dans le fichier de zone DNS, ou s’il n’est pas une correspondance exacte avec l’entrée DNS cette publicité Azure vous a fourni. Si vous n’avez pas accès à mettre à jour les enregistrements DNS pour le domaine dans le Registre des noms de domaine, partager l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter l’entrée DNS.

3.  **Supprimer le nom de domaine à partir d’un autre répertoire dans Azure AD**. Un nom de domaine peut être vérifié dans uniquement un seul répertoire. Si un nom de domaine a été préalablement vérifié dans un autre répertoire, il doit être supprimé il avant qu’il peut être vérifié dans le nouveau répertoire. Pour en savoir plus sur la suppression de noms de domaine, lire des [noms de domaine personnalisé de gérer](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Ajouter d’autres noms de domaine personnalisé

Si votre organisation utilise plusieurs noms de domaine personnalisé, par exemple « contoso.com » et « contosobank.com », vous pouvez les ajouter au maximum 900 des noms de domaine. Utilisez les mêmes étapes dans cet article pour ajouter chacun de vos noms de domaine.

## <a name="next-steps"></a>Étapes suivantes

-   [Gérer les noms de domaine personnalisé](active-directory-add-manage-domain-names.md)
-   [En savoir plus sur les concepts de gestion de domaine dans Active Directory Azure](active-directory-add-domain-concepts.md)
-   [Afficher que votre société de personnalisation lorsque les utilisateurs se connecter](active-directory-add-company-branding.md)
-   [Utiliser PowerShell pour gérer des noms de domaine dans Active Directory Azure](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
