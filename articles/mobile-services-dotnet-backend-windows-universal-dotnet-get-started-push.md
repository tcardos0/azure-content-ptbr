﻿<properties pageTitle="Introdução à notificação por push usando um serviço móvel de back-end do .NET" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução às notificações por push nos Serviços Móveis](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

> Se você não pode atualizar para o Visual Studio Professional 2013 Update 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push](#register)
2. [Atualizar o serviço para enviar notificações por push](#update-service)
3. [Habilitar notificações por push para teste local](#local-testing)
4. [Testar notificações por push no seu aplicativo](#test)

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) ativa.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Atualização 3 ou versão posterior). <br/>Uma versão de avaliação gratuita está disponível. 

##<a id="register"></a>Registrar seu aplicativo para notificações por push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navegue na pasta de projeto <code>\Services\MobileServices\your_service_name</code>, abra o arquivo de código push.register.cs gerado e inspecione o método <strong>UploadChannel</strong> que registra a URL do canal do dispositivo com o hub de notificação.</p></li> 
<li><p>Abra o arquivo de código App.xaml.cs compartilhado e avise que uma chamada para o novo método <strong>UploadChannel</strong> foi adicionada no manipulador de evento <strong>OnLaunched</strong>.</p> <p>Isso garante a tentativa do registro do dispositivo sempre que o aplicativo for iniciado.</p></li>
<li><p>Repita as etapas anteriores para adicionar notificações por push ao projeto do aplicativo Windows Phone Store e, em seguida, no arquivo App.xaml.cs compartilhado, remova a chamada extra para <strong>UploadChannel</strong> e o wrapper condicional <code>#if...#endif</code> restante.</p> <p>Ambos os projetos podem agora compartilhar uma única chamada para <strong>UploadChannel</strong>.</p>
<div class="dev-callout"><strong>Observação</strong> <p>Você também pode simplificar o código gerado ao unificar as definições (contidas em um wrapper <code>#if...#endif</code>) do <a href="http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> em uma única  definição sem wrapper, usada por ambas as versões do aplicativo.</p></div></li>
</ol>

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push. 

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

As seguintes etapas atualizam a classe TodoItemController existente para enviar uma notificação por push para todos os dispositivos registrados quando um novo item é inserido. Você pode implementar código semelhante em qualquer [ApiController] ou [TableController] personalizado, ou em qualquer lugar nos seus serviços de back-end. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

As etapas restantes nesta seção são opcionais. Elas permitem que você teste seu aplicativo em relação ao seu serviço móvel em execução em um computador local. Se você planeja testar as notificações por push usando o serviço móvel em execução no Azure, você pode apenas pular para a última seção. Isso ocorre porque o assistente Adicionar Notificação por Push já configurou seu aplicativo para se conectar ao seu serviço em execução no Azure. 

>[WACOM.NOTE]Nunca use um serviço móvel de produção para testar e desenvolver o trabalho. Sempre publique seu projeto de serviço móvel para um serviço de preparação para testes.

<ol start="5">
<li><p>Abra o arquivo de projeto App.xaml.cs compartilhado e localize qualquer uma das linhas do código que cria uma nova instância da classe <a href="http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> para acessar o serviço móvel em execução no Azure.</p></li>
<li><p>Comente este código e adicione o código que cria um novo <a href="http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> com o mesmo nome, mas usando a URL do host local no construtor, semelhante ao seguinte:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>Usando este <a href="http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, o aplicativo conectará ao serviço local ao invés da versão hospedada no Azure. Quando quiser voltar e executar o aplicativo no serviço móvel hospedado no Azure, mude de volta para as definições <a href="http://msdn.microsoft.com/pt-br/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> originais.</p></li>
</ol>

##<a id="test"></a> Testar notificações por push no seu aplicativo

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push de um Serviço Móvel para um único usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando os serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação. 

* [Como usar um cliente .NET para os Serviços Móveis do Azure]
  <br/>Saiba mais sobre como usar os Serviços Móveis de aplicativos C# do Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK do Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users

[Enviar notificações por push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Quais são os Hubs de notificação?]: /pt-br/documentation/articles/notification-hubs-overview/

[Como usar um cliente .NET de serviços móveis do Azure]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/