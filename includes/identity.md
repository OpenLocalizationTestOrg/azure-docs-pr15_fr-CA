Gestion d’identité est tout aussi important dans le cloud public étant sur le site. Pour faciliter cela, Azure prend en charge de plusieurs technologies d’identité nuage différent. Ils sont les suivants :

- Vous pouvez exécuter Windows Server Active Directory (généralement appelé simplement AD) dans le cloud à l’aide de machines virtuelles créées avec les machines virtuelles Azure. Cette approche est logique lorsque vous utilisez Azure pour étendre votre centre de données local dans le nuage.


- Vous pouvez utiliser Azure Active Directory pour donner à vos utilisateurs de l’authentification unique aux applications [logicielles en tant que Service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) . Microsoft Office 365 utilise cette technologie, par exemple, et applications qui s’exécutent sur Azure ou d’autres plates-formes de nuage peuvent également l’utiliser.


- Les applications s’exécutant dans le nuage ou sur site permet Azure le contrôle d’accès Active Directory journal permettent aux utilisateurs de l’utilisation des identités à partir d’autres fournisseurs d’identité, Microsoft, Facebook et Google.


Cet article décrit les trois de ces options.

## <a name="table-of-contents"></a>Table des matières

- [Active Directory de Windows Server en cours d’exécution sur des machines virtuelles](#adinvm)

- [Utilisation d’Azure Active Directory](#ad)

- [À l’aide du contrôle d’accès Azure Active Directory](#ac)


## <a name="adinvm"></a>Active Directory de Windows Server en cours d’exécution sur des machines virtuelles

Active Directory de Windows Server en cours d’exécution sur des machines virtuelles Azure est très semblable à son exécution en local. [La figure 1](#fig1) illustre un exemple typique de cet aspect.

![Azure Active Directory dans la Machine virtuelle](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figure 1 : Active Directory de Windows Server peut exécuter dans des machines virtuelles Azure connectés au centre de données sur site d’une organisation à l’aide de réseau virtuel d’Azure.

Dans l’exemple présenté ici, Windows Server, Active Directory est en cours d’exécution dans les ordinateurs virtuels créés à l’aide de Machines virtuelles de Azure, la technologie de la plateforme IaaS. Ces ordinateurs virtuels et quelques autres sont regroupées dans un réseau virtuel est connecté à un centre de données local à l’aide de réseau virtuel d’Azure. Le réseau virtuel carves un groupe d’ordinateurs virtuels de nuage qui interagissent avec le réseau local via une connexion de réseau privé virtuel (VPN). Effectuant ce vous permet de ces ordinateurs virtuels Azure semblable à un autre sous-réseau au centre de données sur site. Comme le montre la figure, deux de ces ordinateurs virtuels exécutant des contrôleurs de domaine Windows Server Active Directory. Les autres ordinateurs virtuels dans le réseau virtuel peut exécuter des applications, telles que SharePoint, ou utilisé d’une autre manière, par exemple pour le développement et le test. Le centre de données local exécute également les deux contrôleurs de domaine Windows Server Active Directory.

Il existe plusieurs options pour connecter les contrôleurs de domaine dans le nuage à ceux en cours d’exécution sur les lieux :

- Toutes les parties d’un seul domaine Active Directory.

- Créer distinct AD domaines locaux et dans le cloud qui font partie de la même forêt.

- Créer des forêts Active Directory distinctes dans le nuage et sur site, puis connecter les forêts à l’aide d’approbations entre forêts ou Windows Server Active Directory Federation Services (Active Directory Federation Services), qui peut également fonctionner dans des machines virtuelles sur Azure.

Le choix est fait, un administrateur doit s’assurer que les demandes d’authentification des utilisateurs sur site atteindre le nuage des contrôleurs de domaine uniquement lorsque cela est nécessaire, dans la mesure où le lien vers le nuage est susceptible d’être plus lent que les réseaux locaux. Un autre facteur à prendre en compte dans le cloud qui se connecte et les contrôleurs de domaine local est le trafic généré par la réplication. Contrôleurs de domaine dans le nuage sont généralement de leur propre site Active Directory, qui permet à un administrateur de planifier la fréquence à laquelle la réplication est effectuée. Frais Azure pour le trafic envoyé en dehors d’un centre de données Azure, mais ne pas pour les octets envoyés dans, qui peuvent affecter les choix de l’administrateur réplication. Il est également important de noter que tandis que Azure ne fournit pas sa propre prise en charge de système de nom de domaine (DNS), ce service ne dispose pas les fonctionnalités requises par Active Directory (par exemple, la prise en charge pour les enregistrements dynamiques DNS et SRV). De ce fait, Windows Server, Active Directory en cours d’exécution sur Azure nécessite la configuration de vos propres serveurs DNS dans le nuage.

Exécutant Windows Server Active Directory dans Azure VM peut sens dans plusieurs situations. Voici quelques exemples :

- Si vous utilisez des ordinateurs virtuels Azure comme une extension de votre propre centre de données, vous pouvez exécuter des applications dans le cloud qui ont besoin de contrôleurs de domaine locaux pour gérer des éléments tels que les demandes d’authentification intégrée de Windows ou des requêtes LDAP. SharePoint, par exemple, interagit souvent avec Active Directory, et il est possible d’exécuter une batterie de serveurs SharePoint sur Azure à l’aide d’un répertoire local, configuration de contrôleurs de domaine dans le nuage considérablement améliorera les performances. (Il est important de réaliser que ce n’est pas forcément nécessaire, toutefois ; beaucoup d’applications peuvent s’exécuter dans le nuage en utilisant uniquement des contrôleurs de domaine locaux).

- Supposons que les ressources pour exécuter ses propres contrôleurs de domaine ne dispose pas d’une succursale qui habitent loin. Aujourd'hui, ses utilisateurs doivent s’authentifier auprès des contrôleurs de domaine de l’autre côté du monde, les connexions sont lentes. Exécutant Active Directory sur Azure dans un centre de données Microsoft détaillée peut accélérer ce processus sans nécessiter plus de serveurs de la succursale.

- Une organisation qui utilise Azure pour la reprise après sinistre peut gérer un petit ensemble de VMs actives dans le nuage, y compris un contrôleur de domaine. Il peut être préparé pour développer ce site pour prendre le relais d’échecs ailleurs.

Il existe également d’autres possibilités. Par exemple, vous n’êtes pas requis pour Windows Server Active Directory dans le nuage de se connecter à un centre de données sur site. Si vous souhaitez exécuter une batterie de serveurs SharePoint pris en charge un ensemble spécifique d’utilisateurs, par exemple, qui serait connecter uniquement avec les identités de nuage, vous pouvez créer une forêt autonome sur Azure. Utilisation de cette technologie dépend de quels sont vos objectifs. (Pour des conseils plus détaillés sur l’utilisation d’Active Directory du serveur Windows Azure, [consultez ici](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Utilisation d’Azure Active Directory

Comme les applications SaaS deviennent de plus en plus courantes, ils déclenchent une question évidente : ces applications sur le cloud de quel type de service d’annuaire doivent utiliser ? Réponse de Microsoft à cette question est Azure Active Directory.

Il existe deux options principales pour l’utilisation de ce service d’annuaire dans le nuage :

- Personnes et organisations qui utilisent uniquement des applications SaaS peuvent reposer sur Azure Active Directory en tant que leur service d’annuaire unique.

- Organisations qui exécutent Windows Server Active Directory, vous peuvent connecter leur répertoire local vers Azure Active Directory, puis il permet de donner à leur utilisateurs de l’authentification unique pour les applications SaaS.


[La figure 2](#fig2) illustre la première de ces deux options, où Azure Active Directory est tout ce qui est nécessaire.

![Azure Active Directory dans la Machine virtuelle](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figure 2 : Azure permet utilisateurs de l’authentification unique d’une organisation aux applications SaaS, y compris Office 365.

Comme le montre la figure, Azure est un service partagé. Cela signifie qu’il peut prendre simultanément en charge plusieurs organisations, stocker les informations d’annuaire concernant les utilisateurs de chacun d’eux. Dans cet exemple, un utilisateur au niveau de l’organisation A tente d’accéder à une application de SaaS. Cette application peut être la partie d’Office 365, tels que SharePoint Online, ou il peut être quelque chose d’autre - non Microsoft applications peuvent également utiliser cette technologie. Annonce Azure prenant en charge le protocole SAML 2.0, tout ce qui est nécessaire à partir d’une application est la capacité d’interagir à l’aide de cette norme. (En fait, les applications qui utilisent Active Directory Azure peuvent s’exécuter dans n’importe quel centre de données, et pas seulement un centre de données Azure.)

Le processus commence lorsque l’utilisateur accède à une application de SaaS (étape 1). Pour utiliser cette application, l’utilisateur doit présenter un jeton émis par AD Azure.

Ce jeton contient des informations qui identifient l’utilisateur, et il est signé numériquement par AD Azure. Pour obtenir le jeton, l’utilisateur s’authentifie lui-même pour Azure AD en fournissant un nom d’utilisateur et le mot de passe (étape 2). Annonce Azure puis retourne le jeton qu’il a besoin (étape 3).

Ce jeton est ensuite envoyé à l’application de SaaS (étape 4), qui valide les signature du jeton et utilise son contenu (étape 5). En général, l’application utilisera les informations d’identité que le jeton contient pour décider quelles informations que l’utilisateur est autorisé à access et peut-être d’autres manières.

Si l’application a besoin de plus d’informations sur l’utilisateur que ce qui est inclus dans le jeton, il peut en demander directement à partir d’Azure AD à l’aide de l’API Azure AD graphique (étape 6). Dans la version initiale de publicité Azure, le schéma d’annuaire est très simple : il contient seulement des utilisateurs et des groupes et des relations entre eux. Les applications peuvent utiliser ces informations pour en savoir plus sur les connexions entre les utilisateurs. Par exemple, supposons qu’une application a besoin de savoir qui est responsable de l’utilisateur à décider si il a autorisé l’accès à un bloc de données. Il peut apprendre cela en interrogeant Azure AD via l’API de graphique.

L’API de graphique utilise un protocole RESTful ordinaire, ce qui le rend simple à utiliser à partir de la plupart des clients, y compris les périphériques mobiles. L’API prend également en charge les extensions définies par OData, en ajoutant notamment un langage de requête pour vous permettre d’accéder aux données de clients de manière plus utile. (Pour plus d’informations sur OData, voir [Présentation de OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf)). Dans la mesure où les API de graphique peut être utilisée pour obtenir des informations sur les relations entre les utilisateurs, il permet aux applications de comprendre le graphique social qui est incorporé dans le schéma AD Azure pour une organisation spécifique (ce qui explique pourquoi il s’agit de l’API de graphique). Et pour authentifier lui-même pour Azure AD pour les demandes de graphique API, une application utilise OAuth 2.0.

Si une organisation n’utilise pas Active Directory de Windows Server, il n’a ni domaines - serveurs sur site et se base uniquement sur les applications en nuage qui utilisent Active Directory Azure, en utilisant simplement ce répertoire nuage donnerait les utilisateurs de l’authentification unique l’entreprise à chacun d’eux. Encore alors que ce scénario est plus courant chaque jour, la plupart des entreprises utilisent encore sur site domaines créés avec Windows Server Active Directory. Annonce Azure a un rôle utile pour lire ici, comme le montre la [Figure 3](#fig3) .

![Azure Active Directory dans la Machine virtuelle](./media/identity/identity_03_AD.png)
<a id="fig3"></a>Figure 3 : une organisation pouvant créer une fédération Windows Server Active Directory avec Azure Active Directory pour donner à ses utilisateurs de l’authentification unique pour les applications SaaS.

Dans ce scénario, un utilisateur au sein de l’organisation B souhaite accéder à une application de SaaS. Avant de commencer, les administrateurs d’annuaire de l’organisation doivent établir une relation de fédération avec Azure AD à l’aide d’ADFS, comme le montre la figure. Les administrateurs doivent également configurer la synchronisation des données entre sur site Windows Server l’organisation Active Directory et AD Azure. Cette copie automatiquement utilisateur et les informations de groupe à partir du répertoire local vers Azure AD. Notez que ceci permet : en effet, l’entreprise étend son répertoire local dans le nuage. Combinant Windows Server Active Directory et AD Azure de cette manière permet l’organisation d’un service d’annuaire qui peut être géré comme une entité unique, tout en disposant d’une empreinte à la fois sur site et dans le cloud.

Pour utiliser Active Directory Azure, l’utilisateur ouvre la première fois son domaine d’Active Directory sur site comme d’habitude (étape 1). Lorsqu’elle essaie d’accéder à l’application de SaaS (étape 2), les résultats du processus de fédération dans Azure AD qui lui délivre un jeton pour cette application (étape 3). (Pour plus d’informations sur le fonctionne de la fédération, reportez-vous à la section [basée sur les revendications d’identité de Windows : les scénarios et les Technologies](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) Comme avant, ce jeton contient des informations qui identifient l’utilisateur et il est signé numériquement par AD Azure. Ce jeton est ensuite envoyé à l’application de SaaS (étape 4), qui valide les signature du jeton et utilise son contenu (étape 5). Et dans le scénario précédent, le SaaS application peut utiliser l’API de graphique pour plus d’informations sur cet utilisateur si nécessaire (étape 6).

Aujourd'hui, AD Azure n’est pas un remplacement complet sur site Windows Server Active Directory. Comme déjà mentionné, le répertoire de nuage a bien un schéma de plus simple, et il manque également des éléments tels que la stratégie de groupe, la possibilité de stocker des informations sur les ordinateurs et la prise en charge de LDAP. (En fait, un ordinateur Windows ne peut pas être configuré pour permettre aux utilisateurs de se connecter à l’aide de rien d’autre qu’Azure AD - ce n’est pas un scénario pris en charge.) Au lieu de cela, les objectifs initiaux de publicité Azure incluent permettant d’entreprise utilisateurs accéder aux applications dans le nuage sans maintenir une connexion séparée et libération sur site administrateurs de répertoire à partir de la synchronisation manuelle de leur répertoire local avec toutes les applications SaaS qu'utilise de leur organisation. Au fil du temps, cependant, attendez ce service d’annuaire pour résoudre un large éventail de scénarios de nuage.

## <a name="ac"></a>À l’aide du contrôle d’accès Azure Active Directory

Technologies d’identité basé sur le nuage peuvent servir à résoudre de nombreux problèmes. Azure Active Directory peut donner utilisateurs de l’authentification unique d’une organisation pour plusieurs applications SaaS, par exemple. Mais les technologies d’identité dans le nuage peuvent également être utilisés par d’autres moyens.

Supposons, par exemple, qu’une application souhaite laissez les utilisateurs se connecter en utilisant des jetons émis par plusieurs *fournisseurs d’identité (IdPs)*. Beaucoup de différents fournisseurs d’identité existe aujourd'hui, y compris de Facebook, Google, Microsoft et autres, et applications permettent souvent aux utilisateurs de se connecter en utilisant l’une de ces identités. Pourquoi une application de méthode pour mettre à jour sa propre liste d’utilisateurs et mots de passe lorsqu’il peut s’appuient plutôt sur les identités qui existent déjà ? Accepter les identités existantes rend la vie plus simple à la fois pour les utilisateurs, qui ont un moins un nom d’utilisateur et le mot de passe à mémoriser et pour les personnes qui créent l’application, ce qui n’est plus nécessaire de maintenir leurs propres listes de noms d’utilisateurs et mots de passe.

Mais alors que chaque fournisseur d’identité émet un certain type de jeton, ces jetons ne sont pas standard - chaque IdP a son propre format. En outre, les informations contenues dans ces jetons n’est pas standard. Une application qui souhaite accepter des jetons émis par, par exemple, Facebook, Google et Microsoft est confrontée aux défis de l’écriture de code unique pour gérer chacun de ces formats différents.

Mais pourquoi faire cela ? Pourquoi ne pas créer d’à la place un intermédiaire qui peut générer un format de jeton unique avec une représentation commune des informations d’identité ? Cette approche rendrait vie plus simple pour les développeurs qui créent des applications, dans la mesure où ils doivent désormais ne gérer qu’un seul type de jeton. Contrôle d’accès de répertoire actif Azure fait cela, prévoyant l’utilisation de divers jetons intermédiaire dans le nuage. [La figure 4](#fig4) illustre la façon dont il fonctionne

![Azure Active Directory dans la Machine virtuelle](./media/identity/identity_04_IdentityProviders.png)
<a id="fig4"></a>Figure 4 : Azure le contrôle d’accès Active Directory facilite l’utilisation des applications d’accepter les jetons d’identité délivrés par différents fournisseurs d’identité.

Le processus commence lorsqu’un utilisateur tente d’accéder à l’application à partir d’un navigateur. L’application redirige lui un IdP de son choix (et que l’application approuve également). Elle s’authentifie lui-même cette IdP, comme en entrant un nom d’utilisateur et le mot de passe (étape 1), et le IdP renvoie un jeton contenant des informations sur le son (étape 2).

Comme le montre la figure, le contrôle d’accès prend en charge un éventail de différents IdPs basée sur le nuage, y compris les comptes créés par Google, Yahoo, Facebook, Microsoft (anciennement de Windows Live ID) et n’importe quel fournisseur de OpenID. Il prend également en charge les identités créées à l’aide d’Azure Active Directory et, par le biais de la fédération avec AD FS, Active Directory de Windows Server. L’objectif est de couvrir les identités plus couramment utilisées aujourd'hui, si ils sont émis par des IdPs dans le nuage ou sur site.

Une fois que le navigateur de l’utilisateur a un jeton IdP de son choisi IdP, elle envoie ce jeton au contrôle d’accès (étape 3). Contrôle d’accès valide le jeton, en vérifiant qu’il vraiment a été émis par cet IdP, puis crée un nouveau jeton selon les règles qui ont été définis pour cette application. Comme Azure Active Directory, le contrôle d’accès est un service partagé, mais les locataires sont des applications plutôt qu’aux entreprises. Chaque application peut obtenir de son propre espace de noms, comme le montre la figure et pouvez définir des règles sur l’autorisation et bien plus encore.

Ces règles permettent de définir comment les jetons de diverses IdPs doivent être transformées en un jeton de contrôle d’accès administrateur de chaque application. Par exemple, si différents IdPs utilisent différents types pour représenter les noms d’utilisateur, les règles de contrôle d’accès peuvent transformer tous ces éléments dans un type de nom d’utilisateur commun. Contrôle d’accès envoie ensuite ce nouveau jeton au navigateur (étape 4), qui l’envoie à l’application (étape 5). Une fois qu’il a le jeton de contrôle d’accès, l’application vérifie que ce jeton vraiment a été émis par le contrôle d’accès, puis utilise les informations qu’il contient (étape 6).

Bien que ce processus peut paraître un peu compliqué, il en fait rend la vie beaucoup plus simple pour le créateur de l’application. Plutôt que de gérer divers jetons contenant des informations différentes, l’application peut accepter les identités émises par plusieurs fournisseurs d’identité lors de la réception de toujours seulement un seul jeton avec les informations courantes. En outre, au lieu d’exiger de chaque application à être configuré pour approuver les différentes IdPs, ces relations d’approbation sont plutôt gérées par le contrôle d’accès - une application ont besoin de confiance uniquement.

Il est important de noter que rien à propos du contrôle d’accès est lié à Windows, il pourrait tout aussi bien être utilisé par une application Linux qui a n'accepté que les identités de Google et Facebook. Et même si le contrôle d’accès est la partie de la famille Azure Active Directory, vous pouvez la considérer comme un service totalement distinct de ce qui a été décrit dans la section précédente. Bien que les deux technologies fonctionnent avec l’identité, elles répondent à des problèmes très différents (même si Microsoft a déclaré qu’elle attend intégrer les deux à un moment donné).

Utilisation de l’identité est importante dans presque toutes les applications. L’objectif de contrôle d’accès est pour le rendre plus facile pour les développeurs à créer des applications qui acceptent des identités à partir de fournisseurs d’identité divers. En plaçant ce service dans le nuage, Microsoft a rendu disponible pour toute application en cours d’exécution sur n’importe quelle plate-forme.

##<a name="about-the-author"></a>À propos de l’auteur

David Chappell est directeur de Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) à San Francisco, Californie.
