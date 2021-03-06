
<properties
	pageTitle="Introdução às notificações por push (Android JavaScript) | Microsoft Azure"
	description="Saiba como usar serviços móveis do Azure para enviar notificações por push para seu aplicativo JavaScript do Android."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	writer="ricksal"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>


# Adicionar notificações por push ao seu aplicativo de Serviços Móveis para Android

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## Resumo

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo Android usando o Google Cloud Messaging (“GCM”). Você adicionará notificações por push ao projeto de início rápido, que é um pré-requisito para este tutorial. As notificações por push são habilitadas por meio do Hub de Notificação do Azure incluído no seu serviço móvel. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

## Pré-requisitos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Exemplo de código
Para ver o código-fonte completo, clique [aqui](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPush).

## Habilitar o sistema de mensagens em nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Configurar Serviços Móveis para enviar solicitações de push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Adicionar notificações de push para seu aplicativo



A próxima etapa é instalar os serviços do Google Play. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo.

Se estiver testando com um dispositivo mais antigo, consulte [Configurar o SDK do Google Play Services] para determinar o mínimo que pode ser definido para esse valor e defina-o de maneira apropriada.

###Inclua o Google Play Services no projeto

[AZURE.INCLUDE [Adicionar Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Incluir código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


## Atualizar o script de inserção registrado no Portal clássico do Azure

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## Testar notificações por push no seu aplicativo

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

###Configurando o emulador do Android para teste

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que dá suporte a APIs do Google.

1. Na extremidade direita da barra de ferramentas, selecione o Android Virtual Device Manager, selecione seu dispositivo, clique no ícone de edição à direita.

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Selecione **Alterar** na linha de descrição do dispositivo, selecione **APIs do Google** e, em seguida, clique em OK.

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Destina-se ao AVD para usar APIs do Google.

###Executando o teste

1. No item de menu **Executar**, clique em **Executar aplicativo** para iniciar o aplicativo.

2. No aplicativo, digite um texto significativo, como _Uma nova tarefa de Serviços Móveis_ e clique no botão **Incluir**.

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. Passe o dedo para baixo da parte superior da tela para abrir a Gaveta de Notificação do dispositivo para ver a notificação.


Este tutorial foi concluído com êxito.

## Solucionar problemas

### Verificar Versão de SDK do Android

[AZURE.INCLUDE [Verifique o SDK](../../includes/mobile-services-verify-android-sdk-version.md)]

## Próximas etapas

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Como usar a biblioteca de cliente Android para os Serviços Móveis] <br/>Saiba mais sobre como usar os Serviços Móveis com o Android.

* [Referência de script de servidor dos Serviços Móveis] <br/>Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[Introdução à autenticação]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Configurar o SDK do Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure classic portal]: https://manage.windowsazure.com/
[Como usar a biblioteca de cliente Android para os Serviços Móveis]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[O que são Hubs de Notificação?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md

<!---HONumber=AcomDC_0121_2016-->