﻿

Por padrão, todas as solicitações para os Aplicativos Móveis do Serviço de Aplicativo são restritas aos clientes que apresentam a chave de aplicativo, que não protege estritamente o acesso aos recursos.  Para proteger seus recursos, é necessário restringir o acesso apenas a clientes autenticados.

1. No Visual Studio, abra o projeto que contém o código de aplicativo móvel. 

2. No Gerenciador de Soluções, expanda a pasta Controllers e abra o arquivo de projeto TodoItemController.cs.

	A classe **TodoItemController** implementa o acesso a dados da tabela TodoItem. 

3. Adicione a seguinte instrução `using` na parte superior da página de código:

		using Microsoft.Azure.Mobile.Security;

4. Aplique o seguinte atributo AuthorizeLevel à classe **TodoItemController**:

		[AuthorizeLevel(AuthorizationLevel.User)] 

	Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. 

	>[AZURE.NOTE]Aplique o atributo AuthorizeLevel a métodos individuais para definir níveis de autorização específicos nos métodos expostos pelo controlador.

5. Se desejar depurar a autenticação localmente, expanda a pasta App_Start, abra o arquivo de projeto WebApiConfig.cs e adicione o seguinte código ao método **Register**:

		config.SetIsHosted(true);
	
	Isso informa ao projeto local para executar como se estivesse sendo hospedado no Azure, incluindo respeitar as configurações de AuthorizeLevel.  Sem essa configuração, todas as solicitações HTTP para *localhost* são permitidas sem autenticação, apesar da configuração de AuthorizeLevel.  

6. Republique seu projeto de aplicativo móvel.


<!--HONumber=49-->