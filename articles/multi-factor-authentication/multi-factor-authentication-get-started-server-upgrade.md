<properties 
    pageTitle="Mise à niveau de l’Agent PhoneFactor pour le serveur d’authentification à plusieurs facteurs Azure"
    description="Ce document décrit comment démarrer avec Azure AMF serveur et la mise à niveau à partir de l’ancien agent de phonefactor."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Mise à niveau de l’Agent PhoneFactor pour le serveur d’authentification à plusieurs facteurs Azure

Mise à niveau à partir de la version v5.x PhoneFactor Agent ou ancien vers le serveur de l’authentification multifactorielle Azure requiert le PhoneFactor Agent et les composants affiliés à être désinstallé avant que le serveur de l’authentification multifactorielle et ses composants liées peuvent être installés.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Pour mettre à niveau le PhoneFactor Agent pour le serveur de l’authentification multifactorielle Azure
<ol>
<li>Tout d’abord, sauvegardez le fichier de données PhoneFactor. L’emplacement d’installation par défaut est C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Si le portail de l’utilisateur est installé :</li>
<ol>
<li>Naviguez vers le dossier d’installation et sauvegardez le fichier web.config. L’emplacement d’installation par défaut est C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Si vous avez ajouté des thèmes personnalisés sur le portail, sauvegardez votre dossier personnalisé sous le répertoire C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Désinstaller le portail de l’utilisateur par le biais de le PhoneFactor Agent (disponible uniquement si installé sur le même serveur que le PhoneFactor Agent) ou par le biais de programmes de Windows et les fonctionnalités.</li></ol>




<li>Si le Service Web d’application Mobile est installé :
<ol>
<li>Allez dans le dossier d’installation et sauvegardez le fichier web.config. L’emplacement d’installation par défaut est C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Désinstaller le Service Mobile application Web Windows par le biais de programmes et fonctionnalités.</li></ol>

<li>Si le Kit de développement de Service Web est installée, désinstallez-la via le PhoneFactor Agent ou par le biais de programmes de Windows et les fonctionnalités.

<li>Désinstaller l’Agent PhoneFactor Windows par le biais de programmes et fonctionnalités.

<li>Installez le serveur d’authentification à plusieurs facteurs. Notez que le chemin d’installation est récupérée à partir du Registre de l’installation précédente de PhoneFactor Agent afin qu’il doit installer dans le même emplacement (par exemple, C:\Program Files\PhoneFactor). Nouvelles installations auront un autre par défaut à installer le chemin d’accès (par exemple, C:\Program Files\Multi-facteur d’authentification serveur). Le fichier de données vers la gauche le PhoneFactor Agent précédent doit être mis à niveau lors de l’installation, vos utilisateurs et vos paramètres doivent toujours être il après avoir installé le nouveau serveur d’authentification selon plusieurs facteurs.

<li>Si vous y êtes invité, activez le serveur de l’authentification selon plusieurs facteurs et vous assurer qu’il est affecté au groupe de réplication approprié.

<li>Si le Kit de développement de Service Web a été précédemment installé, installez le nouveau SDK de Service Web par le biais de l’Interface utilisateur de plusieurs facteurs d’authentification serveur. Notez que le nom de répertoire virtuel par défaut est désormais « MultiFactorAuthWebServiceSdk » au lieu de « PhoneFactorWebServiceSdk ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel lors de l’installation. Dans le cas contraire, si vous autorisez l’installation utiliser le nouveau nom par défaut, vous devrez modifier l’URL dans les applications qui référencent le Kit de développement de services Web tels que le portail de l’utilisateur et le Mobile application Web Service au point à l’emplacement correct.

<li>Si le portail de l’utilisateur a été précédemment installé sur le serveur de l’Agent PhoneFactor, installez le nouveau portail utilisateur d’authentification selon plusieurs facteurs par l’intermédiaire de l’Interface utilisateur de plusieurs facteurs d’authentification serveur. Notez que le nom de répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PhoneFactor ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel lors de l’installation. Dans le cas contraire, si vous autorisez l’installation utiliser le nouveau nom par défaut, vous cliquez sur l’icône du portail de l’utilisateur dans le serveur de l’authentification multifactorielle et mise à jour de l’URL du portail utilisateur sous l’onglet Paramètres.

<li>Si le portail de l’utilisateur et/ou le Service application Web Mobile a été installé précédemment sur un autre serveur de le PhoneFactor Agent :
<ol>
<li>Accédez à l’emplacement d’installation (par exemple, C:\Program Files\PhoneFactor) et copier l’installer(s) approprié à un autre serveur. Il existe des programmes d’installation 32 bits et 64 bits pour le portail de l’utilisateur et le Service application Web Mobile. Elles sont respectivement appelées MultiFactorAuthenticationUserPortalSetupXX.msi et MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.</li>
<li>Pour installer le portail de l’utilisateur sur le serveur web, ouvrez une invite de commande en tant qu’administrateur et exécuter le MultiFactorAuthenticationUserPortalSetupXX.msi. Notez que le nom de répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PhoneFactor ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel lors de l’installation. Dans le cas contraire, si vous autorisez l’installation utiliser le nouveau nom par défaut, vous cliquez sur l’icône du portail de l’utilisateur dans le serveur de l’authentification multifactorielle et mise à jour de l’URL du portail utilisateur sous l’onglet Paramètres. Les utilisateurs existants auront besoin d’être informés de la nouvelle URL.</li>
<li>Accédez au portail utilisateur installer emplacement (par exemple, C:\inetpub\wwwroot\MultiFactorAuth) et modifier le fichier web.config. Copier les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d’origine qui a été sauvegardé avant la mise à niveau dans le fichier web.config. Si le nouveau nom de répertoire virtuel par défaut a été conservé lors de l’installation du Kit de développement de Service Web, modifiez l’URL dans la section applicationSettings pour pointer vers l’emplacement correct. Si les valeurs par défaut ont été modifiées dans le fichier web.config précédente, appliquer ces mêmes modifications dans le nouveau fichier web.config.</li>
<li>Pour installer le Service Web d’application Mobile sur le serveur web, ouvrez une invite de commande en tant qu’administrateur et exécuter le MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Notez que le nom de répertoire virtuel par défaut est désormais « MultiFactorAuthMobileAppWebService » au lieu de « PhoneFactorPhoneAppWebService ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel lors de l’installation. Vous souhaiterez peut-être choisir un nom plus court pour faciliter le travail des utilisateurs finaux à taper dans sur leurs appareils mobiles. Dans le cas contraire, si vous autorisez l’installation utiliser le nouveau nom par défaut, vous cliquez sur l’icône de l’application Mobile sur le serveur d’authentification selon plusieurs facteurs et mise à jour de l’URL de Service Web Mobile application.</li>
<li>Accédez au Service Web Mobile application installer emplacement (par exemple, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) et modifier le fichier web.config. Copier les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d’origine qui a été sauvegardé avant la mise à niveau dans le fichier web.config. Si le nouveau nom de répertoire virtuel par défaut a été conservé lors de l’installation du Kit de développement de Service Web, modifiez l’URL dans la section applicationSettings pour pointer vers l’emplacement correct. Si les valeurs par défaut ont été modifiées dans le fichier web.config précédente, appliquer ces mêmes modifications dans le nouveau fichier web.config.</li></ol>
