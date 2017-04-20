<properties
   pageTitle="Azure AD Connect : Concepts de conception | Microsoft Azure"
   description="Cette rubrique détaille certaines zones de conception mise en œuvre"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure Connect d’annonce : Les concepts de conception
L’objectif de cette rubrique est de décrire les zones qui doivent être représentées par le biais de lors de la conception de la mise en oeuvre d’Azure Connect d’Active Directory. Cette rubrique est une immersion totale dans certaines zones et que ces concepts sont brièvement décrites dans d’autres rubriques ainsi.

## <a name="sourceanchor"></a>sourceAnchor
L’attribut sourceAnchor est défini en tant *qu’attribut immuable pendant la durée de vie d’un objet*. Il identifie de manière unique un objet comme étant le même objet local et dans Active Directory Azure. L’attribut est également appelé **immutableId** et les deux noms sont utilisés interchangeables.

Le mot immuable, ce qui est « ne peut pas être modifié », il est important de cette rubrique. Étant donné que la valeur de cet attribut ne peut pas être modifiée une fois qu’il a été défini, il est important de choisir un modèle qui prend en charge votre scénario.

L’attribut est utilisé pour les scénarios suivants :

- Lorsqu’un nouveau serveur de moteur de synchronisation est construit ou reconstruit après un scénario de récupération après sinistre, cet attribut lie les objets existants dans Azure AD avec les objets locaux.
- Si vous déplacez à partir d’une identité de nuage uniquement à un modèle de synchronisation d’identité, cet attribut permet aux objets aux objets existants de « correspondance exacte » dans Azure AD avec les objets locaux.
- Si vous utilisez la fédération, cet attribut et **userPrincipalName** est utilisé dans la demande de remboursement pour identifier de manière unique un utilisateur.

Cette rubrique aborde uniquement sourceAnchor par rapport aux utilisateurs. Les mêmes règles s’appliquent à tous les types d’objet, mais uniquement pour les utilisateurs, il est en règle générale, ce problème est une préoccupation.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Sélection d’un attribut de bonne sourceAnchor
La valeur de l’attribut doit respecter les règles suivantes :

- Pas moins de 60 caractères
    - Des caractères n’est pas d’a à z, A-Z ou 0-9 sont codés et assimilée à 3 caractères.
- Ne contient pas un caractère spécial : & #, 92 ; ! # $ % & * + / = ? ^ & #, 96 ; { } | ~ (de < >) » ; : , [ ] " @ _
- Doit être globalement unique.
- Doit être une chaîne, entier ou binaire
- Ne doit pas être basé sur le nom de l’utilisateur, ces modifications
- Doivent pas respecter la casse et éviter les valeurs peuvent varier selon les cas
- Doit être affecté lorsque l’objet est créé.

Si le sourceAnchor sélectionné n’est pas de type chaîne, puis Azure Base64Encode de connexion Active Directory la valeur d’attribut pour ne garantir aucun caractère spécial n’apparaît. Si vous utilisez un autre serveur de fédération à ADFS, assurez-vous que votre serveur peut également Base64Encode de l’attribut.

L’attribut sourceAnchor est sensible à la casse. La valeur « JohnDoe » n’est pas le même que « johndoe ». Mais vous ne devez pas deux objets différents, avec seulement une différence de casse.

Si vous disposez d’une seule forêt locale, puis sur l’attribut que vous devez utiliser est **objectGUID**. C’est également l’attribut utilisé lorsque vous utilisez les paramètres express dans Azure Connect d’annonce et l’attribut utilisé par la synchronisation d’annuaire.

Si vous avez plusieurs forêts et que vous ne déplacez pas des utilisateurs entre forêts et domaines, **objectGUID** est un bon attribut à utiliser même dans ce cas.

Si vous déplacez des utilisateurs entre forêts et domaines, puis vous devez trouver un attribut qui ne change pas ou peut être déplacé avec les utilisateurs au cours du déplacement. Une approche recommandée est d’introduire un attribut synthétique. Un attribut qui pourrait contenir quelque chose qui ressemble à un GUID convient. Lors de la création de l’objet, un nouveau GUID est créé et affecté à l’utilisateur. Une règle de synchronisation personnalisée peut être créée dans le serveur de moteur de synchronisation pour créer cette valeur selon l' **objectGUID** et mise à jour de l’attribut sélectionné dans ajoute. Lorsque vous déplacez l’objet, veillez à également copier le contenu de cette valeur.

Une autre solution consiste à choisir un attribut existant que vous savez ne change pas. Les attributs fréquemment utilisés incluent **employeeID**. Si vous envisagez un attribut qui contient des lettres, assurez-vous qu’il y a qu'aucun risque de la casse (majuscule et minuscule) ne pouvez modifier pour la valeur de l’attribut. Des attributs incorrects qui ne doivent pas être utilisés incluent ces attributs avec le nom de l’utilisateur. Dans un mariage ou le divorce, le nom est supposé changer, qui n’est pas autorisée pour cet attribut. C’est également l’une des raisons pour lesquelles les attributs comme **userPrincipalName**, **messagerie**et **targetAddress** ne sont pas même possibles de sélectionner dans l’Assistant d’installation Azure Connect d’Active Directory. Ces attributs contiennent également les @-character, qui n’est pas autorisé dans le sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Modification de l’attribut sourceAnchor
La valeur de l’attribut sourceAnchor ne peut pas être modifiée une fois que l’objet a été créé dans Active Directory Azure et l’identité est synchronisée.

Pour cette raison, les restrictions suivantes s’appliquent pour établir la connexion AD Azure :

- L’attribut sourceAnchor peut uniquement être définie lors de l’installation initiale. Si vous réexécutez l’Assistant d’installation, cette option est en lecture seule. Si vous devez modifier ce paramètre, vous devez désinstaller et réinstaller.
- Si vous installez un autre serveur Azure Connect d’Active Directory, vous devez sélectionner le même attribut sourceAnchor précédemment utilisé. Si vous avez précédemment été à l’aide de synchronisation d’annuaire et déplacez vers Azure Connect d’AD, vous devez utiliser **objectGUID** puisqu’il s’agit de l’attribut utilisé par la synchronisation d’annuaire.
- Si la valeur de sourceAnchor est modifiée après l’objet a été exportée vers Azure AD, puis Azure de connexion Active Directory synchronisation génère une erreur et n’autorise pas plus de modifications pour que l’objet avant que le problème a été résolu et la sourceAnchor est modifié dans le répertoire source.

## <a name="azure-ad-sign-in"></a>Dans le signe Azure AD
Lors de l’intégration de votre répertoire local avec AD Azure, il est important de comprendre comment les paramètres de synchronisation peuvent affecter la manière dont les utilisateurs s’authentifie. Annonce Azure utilise UPN (userPrincipalName) pour authentifier l’utilisateur. Toutefois, lorsque vous synchronisez vos utilisateurs, vous devez choisir l’attribut à utiliser pour la valeur d’userPrincipalName soigneusement.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Choisir l’attribut userPrincipalName
Lorsque vous sélectionnez l’attribut pour fournir la valeur de nom UPN pour être utilisée dans un Azure doit s’assurer

- Les valeurs d’attribut sont conformes à la syntaxe de nom UPN (RFC 822), c'est-à-dire qu’il doit être au formatusername@domain
- Le suffixe dans les valeurs correspond à l’un des domaines personnalisés vérifiés dans Azure AD

Dans les paramètres express, le choix par défaut de l’attribut est userPrincipalName. Si l’attribut userPrincipalName ne contient-elle pas la valeur, vous voulez que vos utilisateurs à se connecter à Azure, puis vous devez cliquez sur **Installation personnalisée**.

### <a name="custom-domain-state-and-upn"></a>État du domaine personnalisé et UPN
Il est important de s’assurer qu’il existe un domaine vérifié pour le suffixe UPN.

Jean est un utilisateur de contoso.com. Vous souhaitez que John à utiliser le nom UPN local john@contoso.com connecter à Azure une fois que vous avez synchronisé les utilisateurs à votre contoso.onmicrosoft.com de répertoire Active Directory Azure. Pour ce faire, vous devez ajouter et vérifier des contoso.com comme un domaine personnalisé dans Azure annonce avant de commencer la synchronisation des utilisateurs. Si le suffixe UPN Jean, par exemple contoso.com, ne correspond pas à un domaine vérifié dans AD Azure, puis AD Azure remplace le suffixe UPN contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Les domaines locaux non routables et UPN pour Azure AD
Certaines organisations ont des domaines non routables, comme contoso.local ou domaines unique simple comme contoso. Vous n’êtes pas en mesure de vérifier un domaine non routable dans Azure AD. Azure Connect d’annonce peut synchroniser uniquement un domaine vérifié dans Azure AD. Lorsque vous créez un annuaire AD d’Azure, il crée un domaine pouvant être routé qui devienne le domaine par défaut pour votre annonce d’Azure, par exemple, contoso.onmicrosoft.com. Par conséquent, il devient nécessaire de vérifier les autres domaines pouvant être routés dans un tel scénario, au cas où vous ne souhaitez pas synchroniser le domaine onmicrosoft.com par défaut.

Pour plus d’informations sur l’ajout et vérification de domaines, lire [Ajouter votre nom de domaine personnalisé pour Azure Active Directory](active-directory-add-domain.md) .

Si vous exécutez dans un environnement de domaine non routable et souhaitez appropriée à vous avertir de poursuivre avec les paramètres express détecte les Azure Connect d’Active Directory. Si vous êtes dans un domaine non routable, il est probable que le nom UPN des utilisateurs possède trop de suffixes non routable. Par exemple, si vous exécutez sous contoso.local, Azure Connect de publicité suggère vous permettant d’utiliser les paramètres personnalisés, plutôt que d’utiliser les paramètres express. À l’aide de paramètres personnalisés, vous ne pouvez spécifier l’attribut qui doit être utilisé comme UPN pour ouvrir une session Azure une fois que les utilisateurs sont synchronisées dans AD Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
