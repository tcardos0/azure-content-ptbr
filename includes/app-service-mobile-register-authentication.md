﻿

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade.  Você deve registrar o segredo do cliente gerado pelo provedor com o Serviço de Aplicativo.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Procurar**, **Grupo de recursos** e selecione o grupo de recursos do Aplicativo móvel.

2. Selecione seu gateway e anote o valor do **URL** em **Propriedades**.  Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. Escolha um provedor de identidade com suporte na lista abaixo e siga as etapas para configurar o seu aplicativo com o provedor:

 - <a href="/pt-br/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Active Directory do Azure</a>
 - <a href="/pt-br/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Logon no Google</a>
 - <a href="/pt-br/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Logon no Twitter</a>

	Agora, o seu aplicativo está configurado para trabalhar com o seu provedor de autenticação escolhido.

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->