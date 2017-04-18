Organisations utilisent plusieurs applications de [logiciels en tant que Service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) pour la productivité, car les outils et les technologies de cloud sont de plus en plus accessibles. À mesure que le nombre d’applications de SaaS augmente, il devient difficile pour les administrateurs à gérer les comptes et les droits d’accès et pour les utilisateurs à se souvenir de leurs mots de passe différents. La gestion de ces applications individuellement crée du travail supplémentaire et est moins sécurisé.


- Les employés qui ont suivi de nombreux mots de passe ont tendance à utiliser des moins sécurisé des méthodes pour vous en souvenir, soit écrire des mots de passe ou à l’aide des mêmes mots de passe sur plusieurs comptes.

- Lors de l’arrivée d’un nouvel employé ou une laisse, tous les comptes de leurs doivent être mis en service ou désactiver la mise en service individuellement.

- En outre, les employés peuvent démarrer à l’aide d’applications de SaaS pour leur travail sans passer par l’informatique, c'est-à-dire qu’ils sont en train de créer leurs propres comptes sur les systèmes qui les administrateurs informatiques n’ont pas approuvés et qui ne sont pas de surveillance.  

Une solution à tous ces défis est de session unique (SSO). Il est le moyen le plus simple pour gérer plusieurs applications et de fournir aux utilisateurs une expérience cohérente de session. Azure Active Directory (AD Azure) fournit une solution puissante de l’authentification unique et a de nombreuses applications pré-intégrées disponibles, avec des didacticiels pour les administrateurs à configurer une nouvelle application rapidement et de commencer la mise en service des utilisateurs.


## <a name="how-does-azure-active-directory-integrate-apps"></a>Comment Azure Active Directory intégrer des applications ?  

Annonce Azure vous permet d’intégrer vos applications et la mise en service des comptes. Pour ce faire par le biais d’une des deux approches.

- Si l’application est déjà intégrée dans l’application bibliothèque, vous pouvez accéder par l’intermédiaire de ce portail pour définir des applications et de configurer les paramètres pour permettre l’authentification unique. Pour n’importe quelle application de bibliothèque, vous pouvez commencer par suivre les simples instructions détaillées présentées dans la galerie d’application et dans le portail Azure pour activer l’ouverture de session unique.

- Si l’application n’est pas dans la galerie, vous pouvez toujours configurer la plupart des applications dans Azure AD sous la forme d’une application personnalisée. Cela nécessite une expertise technique un peu plus à configurer. Vous pouvez ajouter n’importe quelle application qui prend en charge les SAML 2.0 comme une application fédérée, ou n’importe quelle application qui a une basé sur HTML-page de connexion sous la forme d’une application d’authentification de mot de passe.

Dans le cas où les utilisateurs ont créé leurs propres comptes pour les applications SaaS qui ne sont pas gérées par informatique, l’outil de [Découverte d’application Cloud](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) fournit une solution. Cet outil surveille le trafic web afin d’identifier les applications qui sont utilisées dans l’ensemble de l’organisation et le nombre de personnes à l’aide de chacun d’eux. INFORMATIQUES peuvent d’utiliser ces informations pour savoir quelles applications les utilisateurs préfèrent et décident d’intégrer dans Azure AD pour l’authentification unique.  

Lorsque vous intégrez une application dans Azure AD, vous pouvez mapper les identités d’application établis utilisateurs à respectives des identités AD Azure.  
