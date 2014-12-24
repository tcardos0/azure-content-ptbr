<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Como garantir serviços de back-end usando autenticação de certificado mútuo no Gerenciamento de API do Azure

O Gerenciamento de API fornece a capacidade para garantir acesso ao serviço back-end de uma API usando certificados mútuos. Este guia mostra como gerenciar certificados no console de Gerenciamento de API e como configurar uma API para usar um certificado para acessar seu serviço back-end.

> Para obter mais informações sobre gerenciamento de certificados usando API REST de Gerenciamento de API, consulte [entidade de Certificado da API REST de Gerenciamento de API do Azure][entidade de Certificado da API REST de Gerenciamento de API do Azure].

## Neste tópico

-   [Pré-requisitos][Pré-requisitos]
-   [Carregar um certificado do cliente][Carregar um certificado do cliente]
-   [Excluir um certificado do cliente][Excluir um certificado do cliente]
-   [Configurar uma API para usar um certificado mútuo para autenticação de proxy][Configurar uma API para usar um certificado mútuo para autenticação de proxy]

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autenticação de certificado mútuo para acessar o serviço back-end para uma API. Antes de seguir as etapas neste tópico, você deve ter seu serviço back-end configurado para autenticação de certificado mútuo e ter acesso ao certificado e a senha para o certificado para carregamento no console de Gerenciamento de API.

## <a name="step1"> </a>Carregar um certificado do cliente

Para começar, clique em **Console de gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

![Console de Gerenciamento de API][api-management-management-console]

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

Clique em **Segurança** no menu **Gerenciamento de API** à esquerda e clique em **Certificados do cliente**.

![Certificados do cliente][api-management-security-client-certificates]

Para carregar um novo certificado, clique em **Carregar um certificado**.

![Carregar um certificado][api-management-upload-certificate]

Navegue para o seu certificado e digite a senha para o certificado.

> O certificado deve estar no formato **.pfx**. Os certificados autoassinados são permitidos.

![Carregar um certificado][api-management-upload-certificate-form]

Clique em **Carregar** para carregar o certificado.

> A senha do certificado é validada neste momento. Se ela estiver incorreta, é exibida uma mensagem de erro.

![Certificado carregado][api-management-certificate-uploaded]

Uma vez que o certificado é carregado, ele aparece na guia **Certificados do cliente**. Se você tiver certificados múltiplos, tome nota do assunto, ou dos últimos quatro caracteres da impressão digital, que são usados para selecionar o certificado ao configurar uma API para usar certificados, conforme cobertos na seguinte seção [Configurar uma API para usar um certificado mútuo para autenticação de proxy][Configurar uma API para usar um certificado mútuo para autenticação de proxy].

## <a name="step1a"> </a>Excluir um certificado do cliente

Para excluir um certificado, clique em **Excluir** ao lado do certificado desejado.

![Excluir Certificado][api-management-certificate-delete]

Clique em **Sim, excluir** para confirmar.

![Confirmar exclusão][api-management-confirm-delete]

Se o certificado está em uso por uma API, então uma tela de aviso é exibida. Para excluir o certificado, você primeiro deve remover o certificado de quaisquer APIs que são configuradas para usá-la.

![Confirmar exclusão][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurar uma API para usar um certificado mútuo para autenticação de proxy

Clique nas **APIs** no menu **Gerenciamento de API** à esquerda, clique no nome da API desejada e clique na guia **Segurança**.

![Segurança da API][api-management-api-security]

Selecione **Certificados mútuos** da lista suspensa **Com credenciais**.

![Certificados mútuos][api-management-mutual-certificates]

Selecione o certificado desejado da lista suspensa **Certificado do cliente**. Se houver certificados múltiplos, você pode olhar no assunto ou nos últimos quatro caracteres da impressão digital como observado na seção anterior para determinar o certificado correto.

![Selecionar certificado][api-management-select-certificate]

Clique em **Salvar** para salvar as alterações de configuração para a API.

> Essa alteração tem efeito imediato e chama para operações desta API, que usará o certificado para autenticar no servidor back-end.

![Salvar alterações da API][api-management-save-api]

> Quando um certificado é especificado na autenticação de proxy para o serviço back-end de uma API, se torna parte da política para essa API e pode ser exibido no editor de política.

![Política do certificado][api-management-certificate-policy]

  [entidade de Certificado da API REST de Gerenciamento de API do Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [Pré-requisitos]: #prerequisites
  [Carregar um certificado do cliente]: #step1
  [Excluir um certificado do cliente]: #step1a
  [Configurar uma API para usar um certificado mútuo para autenticação de proxy]: #step2
  [api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png