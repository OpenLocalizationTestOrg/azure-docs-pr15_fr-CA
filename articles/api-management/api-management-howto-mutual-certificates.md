<properties 
    pageTitle="Comment sécuriser les services back-end à l’aide du client d’authentification de certificat dans la gestion des API Azure" 
    description="Découvrez comment sécuriser les services back-end à l’aide de l’authentification de certificat client dans la gestion des API Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Comment sécuriser les services back-end à l’aide du client d’authentification de certificat dans la gestion des API Azure

API de gestion permet de sécuriser l’accès au service back-end d’une API à l’aide de certificats clients. Ce guide explique comment gérer les certificats dans le portail de publisher API et comment configurer une API pour utiliser un certificat pour accéder à son service back-end.

Pour plus d’informations sur la gestion des certificats à l’aide de l’API REST de gestion API, reportez-vous à la section [Azure API gestion reste API certificat entité][].

## <a name="prerequisites"> </a>Conditions préalables

Ce guide vous explique comment configurer votre instance de service de gestion de l’API pour utiliser l’authentification par certificat client pour accéder au service back-end pour une API. Avant de suivre les étapes décrites dans cette rubrique, vous devez avoir votre service principal configuré pour l’authentification de certificat client ([pour configurer l’authentification par certificat dans les sites Web d’Azure reportez-vous à cet article][]) et avoir accès au certificat et le mot de passe pour le certificat à télécharger dans le portail d’éditeur de gestion de l’API.

## <a name="step1"> </a>Télécharger un certificat de client

Pour commencer, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de l’éditeur de l’API][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **sécurité** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **certificats clients**.

![Certificats clients][api-management-security-client-certificates]

Pour télécharger un nouveau certificat, cliquez sur **Télécharger le certificat**.

![Télécharger le certificat][api-management-upload-certificate]

Accédez à votre certificat et puis entrez le mot de passe pour le certificat.

>Le certificat doit être dans le format **.pfx** . Les certificats auto-signés sont autorisés.

![Télécharger le certificat][api-management-upload-certificate-form]

Cliquez sur **Télécharger** pour télécharger le certificat.

>Le mot de passe du certificat est validée pour le moment. Si elle est incorrecte, un message d’erreur s’affiche.

![Certificat chargé][api-management-certificate-uploaded]

Une fois que le certificat est téléchargé, il s’affiche sur l’onglet **certificats clients** . Si vous disposez de plusieurs certificats, créer une note de l’objet, ou que les quatre derniers caractères de l’empreinte numérique, qui sont utilisés pour sélectionner le certificat lorsque vous configurez une API pour utiliser des certificats, comme décrit dans la section de [configuration d’une API pour utiliser un certificat client pour l’authentification de la passerelle][] suivante.

>Pour désactiver la validation de chaîne de certificat lors de l’utilisation, par exemple, un certificat signé automatiquement, suivez les étapes décrites dans cet [article](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)du Forum aux questions.

## <a name="step1a"> </a>Supprimer un certificat client

Pour supprimer un certificat, cliquez sur **Supprimer** en regard du certificat de votre choix.

![Supprimer le certificat][api-management-certificate-delete]

Cliquez sur **Oui, le supprimer** pour confirmer.

![Confirmer la suppression][api-management-confirm-delete]

Si le certificat est en cours d’utilisation par une API, un écran d’avertissement s’affiche. Pour supprimer le certificat vous devez d’abord supprimer le certificat à partir des API qui sont configurés pour l’utiliser.

![Confirmer la suppression][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurer une API pour utiliser un certificat client pour l’authentification de passerelle

Cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, cliquez sur le nom de l’API de votre choix et cliquez sur l’onglet **sécurité** .

![Sécurité de l’API][api-management-api-security]

Sélectionnez **les certificats de Client** dans la liste déroulante **avec les informations d’identification** .

![Certificats clients][api-management-mutual-certificates]

Sélectionnez le certificat souhaité dans la liste déroulante de **certificat Client** . S’il y a plusieurs certificats, que vous pouvez examiner l’objet ou les quatre derniers caractères de l’empreinte numérique comme indiqué dans la section précédente pour déterminer le certificat approprié.

![Sélectionner un certificat][api-management-select-certificate]

Cliquez sur **Enregistrer** pour enregistrer la modification de la configuration de l’API.

>Cette modification prend effet immédiatement, et les appels aux opérations de cette API utilise le certificat pour l’authentification sur le serveur principal.

![Enregistrer les modifications de l’API][api-management-save-api]

>Lorsqu’un certificat est spécifié pour l’authentification de passerelle pour le service back-end d’une API, il devient partie de la stratégie de cette API et peut être affichée dans l’éditeur de stratégie.

![Stratégie de certificat][api-management-certificate-policy]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres méthodes pour sécuriser votre service back-end, tels que base ou partagée secrète l’authentification HTTP, voir la vidéo suivante.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Mise en route de la gestion des API Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[Entité de certificat d’API API gestion reste Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Pour configurer l’authentification par certificat dans les sites Web d’Azure reportez-vous à cet article]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurer une API pour utiliser un certificat client pour l’authentification de passerelle]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
