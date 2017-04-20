<properties
    pageTitle="Azure B2C de répertoire actif : Personnalisation de l’interface (interface utilisateur) utilisateur | Microsoft Azure"
    description="Une rubrique sur les fonctionnalités de personnalisation de l’interface (interface utilisateur) utilisateur dans Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure B2C de répertoire actif : Personnaliser l’interface utilisateur de Azure AD B2C (interface utilisateur)

L’expérience utilisateur est primordiale dans une application pour les consommateurs. Il s’agit de la différence entre une bonne application et un excellent et entre les consommateurs simplement actives et ceux qui sont réellement engagés. Azure B2C d’Active Directory (AD Azure) vous permet de personnaliser consommateur d’abonnement, connexion à (*voir Remarque ci-dessous*), de modification, du profil et pages avec contrôle de pixel parfaite de réinitialisation de mot de passe.

> [AZURE.NOTE]
Actuellement, compte local connexion pages, son mot de passe accompaning réinitialiser les pages et les e-mails de vérification peuvent être personnalisés uniquement à l’aide de la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md) et non par les mécanismes décrits dans cet article.

Dans cet article, vous allez en savoir plus sur :

- La fonctionnalité de personnalisation de page utilisateur interface (interface utilisateur).
- Un outil qui vous permettra de tester la fonctionnalité de personnalisation de l’interface utilisateur de page à l’aide de notre exemple de contenu.
- Les éléments d’interface utilisateur principaux dans chaque type de page.
- Les méthodes conseillées lors de l’exercice de cette fonction.

## <a name="the-page-ui-customization-feature"></a>La fonctionnalité de personnalisation de l’interface utilisateur de page

Avec la fonctionnalité de personnalisation de l’interface utilisateur de page, vous pouvez personnaliser l’apparence du consommateur, inscription, inscription, pages de réinitialisation de mot de passe et la modification de profils (en configurant les [stratégies](active-directory-b2c-reference-policies.md)). Vos consommateurs auront une expérience transparente lors de la navigation entre votre application et vos pages pris en charge par Azure AD B2C.

Contrairement à d’autres services, où les options d’interface utilisateur sont limitées ou sont uniquement disponibles via l’API, Azure AD B2C utilise une approche de moderne (et plus simple) pour la personnalisation de l’interface utilisateur de page.

Voici comment il fonctionne : Azure AD B2C exécute le code dans le navigateur de votre client et utilise une approche moderne appelée [Partage de ressources entre origine (CORS)](http://www.w3.org/TR/cors/) pour charger le contenu d’une URL que vous spécifiez dans une stratégie. Vous pouvez spécifier des URL différentes pour différentes pages. Le code fusionne les éléments d’interface utilisateur à partir d’Azure AD B2C avec le contenu chargé à partir de l’URL et affiche la page pour le consommateur. Tout ce que vous devez faire est :

1. Créer du contenu avec HTML5 bien formé une `<div id="api"></div>` élément (doit être un élément vide) se trouve quelque part dans le `<body>`. Cette marque d’élément où est inséré le contenu Azure AD B2C.
2. Héberger votre contenu sur un point de terminaison HTTPS (avec CORS autorisé).
3. Style Azure AD B2C insère dans les éléments d’interface utilisateur.

## <a name="test-out-the-ui-customization-feature"></a>Tester la fonctionnalité de personnalisation de l’interface utilisateur

Si vous souhaitez essayer la fonctionnalité de personnalisation de l’interface utilisateur à l’aide de notre exemple HTML et le contenu CSS, nous vous avons fourni [un outil d’assistance simple](active-directory-b2c-reference-ui-customization-helper-tool.md) qui télécharge et configure des exemples de contenu sur votre stockage Azure Blob.

> [AZURE.NOTE]
Vous pouvez héberger du contenu de l’interface utilisateur n’importe où : sur les serveurs web, CDN, AWS S3, systèmes de partage de fichiers, etc.. Tant que le contenu est hébergé sur un point de terminaison HTTPS accessible publiquement (avec CORS autorisé), vous êtes fin prêt. Nous utilisons le stockage des objets Blob Azure uniquement à des fins d’illustration.

### <a name="the-most-basic-html-content-for-testing"></a>Le contenu HTML de base pour les tests

Indiqué ci-dessous est la plus élémentaire HTML contenu que vous pouvez utiliser pour tester cette fonction. Utilisez le même outil d’assistance fourni plus haut pour télécharger et configurer ce contenu sur votre stockage Azure Blob. Vous pouvez ensuite vérifier que les boutons de base, non stylisé & champs de formulaire sur chaque page sont affichées et fonctionnel.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Les principaux éléments de l’interface utilisateur dans chaque type de page

Dans les sections suivantes, vous trouverez des exemples de fragments de HTML5 Azure AD B2C fusionne dans le `<div id="api"></div>` élément se trouve dans votre contenu. **N’insérez pas ces fragments dans votre contenu de HTML 5.** Le service Azure AD B2C les insère au moment de l’exécution. Utiliser ces exemples pour concevoir vos propres feuilles de style.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Azure AD B2C contenu inséré dans l’identité fournisseur sélection « page »

Cette page contient une liste de fournisseurs d’identité que l’utilisateur peut choisir lors de l’inscription ou ouverture de session. Il s’agit de fournisseurs d’identité sociaux tels que Facebook et Google +, ou comptes locaux (basés sur le nom d’utilisateur ou une adresse de messagerie).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Azure AD B2C contenu inséré dans la « page d’abonnement à un compte Local »

Cette page contient un formulaire d’inscription de l’utilisateur à remplir lors de l’inscription d’un compte local qui est basé sur une adresse de messagerie ou un nom d’utilisateur. Le formulaire peut contenir des contrôles d’entrée différents, tels que la zone de texte, zone de saisie de mot de passe, case d’option, zones de liste déroulante de sélection unique et les cases à cocher à sélection multiple.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure AD B2C contenu inséré dans la « Social compte d’abonnement « page »

Cette page contient un formulaire d’inscription que le consommateur a à renseigner lors de l’inscription à l’aide d’un compte existant auprès d’un fournisseur d’identité social tel que Facebook ou Google +. Cette page est similaire à la page d’abonnement à un compte local (indiquée dans la section précédente), à l’exception des champs de saisie du mot de passe.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Azure AD B2C contenu inséré dans l’unifiée d’abonnement ou d’ouverture de session « page »

Cette page gère l’inscription et connexion à des consommateurs, qui peuvent utiliser les fournisseurs d’identité social tel que Facebook Google + ou comptes locaux.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Azure AD B2C contenu inséré dans la page « authentification à plusieurs facteurs »

Sur cette page, les utilisateurs peuvent vérifier leurs numéros de téléphone (à l’aide de texte ou une voix) lors de l’inscription ou ouverture de session.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Azure AD B2C contenu inséré dans la « erreur « page »

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Points à retenir lors de la création de votre propre contenu.

Si vous envisagez d’utiliser la fonctionnalité de personnalisation de l’interface utilisateur de page, passez en revue les méthodes conseillées suivantes :

- Ne copier le contenu par défaut de la AD B2C Azure et tenter de le modifier. Il est préférable de créer votre contenu de HTML5 à partir de zéro et à utiliser le contenu par défaut comme référence.
- Dans toutes les pages (à l’exception des pages d’erreur) pris en charge par la connexion, d’inscription et de modification de profil des stratégies, des feuilles de style que vous fournissez doivent remplacer les feuilles de style par défaut qui nous ajouter dans ces pages dans le <head> fragments. Dans toutes les pages pris en charge par la procédure d’abonnement ou de connexion et stratégies de réinitialisation de mot de passe et les pages d’erreur dans toutes les stratégies, vous devez fournir toutes le style vous-même.
- Pour des raisons de sécurité, nous ne vous permettent d’inclure du code JavaScript dans votre contenu. La majeure partie de ce dont vous avez besoin doit être disponible hors de la zone. Si ce n’est pas le cas, utilisez la [Voix de l’utilisateur](http://feedback.azure.com/forums/169401-azure-active-directory) pour demander de nouvelles fonctionnalités.
- Prise en charge des versions du navigateur :
    - Internet Explorer 11
    - Internet Explorer 10
    - Internet Explorer 9 (limité)
    - Internet Explorer 8 (limité)
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0
