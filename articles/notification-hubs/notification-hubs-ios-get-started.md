<properties 
	pageTitle="Introdução aos Hubs de notificação do Azure" 
	description="Saiba como usar os Hubs de notificação do Azure para notificações por push." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você cria um aplicativo para iOS em branco que recebe notificações por push usando o serviço de Notificação por Push da Apple (APNs). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

Este tutorial demonstra o cenário de transmissão simples usando hubs de notificação.

##Pré-requisitos

Este tutorial requer os seguintes pré-requisitos:

+ [SDK do iOS dos Serviços Móveis]
+ [XCode 4.5][Install Xcode]
+ Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   >[AZURE.NOTE]Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos para iOS.

> [AZURE.NOTE]Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Habilitar Notificações por push da Apple](../../includes/enable-apple-push-notifications.md)]

##Configurar seu Hub de Notificação

1. No Conjunto de Chaves de Acesso, clique com botão direito no novo certificado do aplicativo de início rápido **Meus Certificados**. Clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e, em seguida, clique em **Salvar**.

    ![][26]

  Anote o nome do arquivo e o local do certificado exportado.

>[AZURE.NOTE]Este tutorial cria um arquivo QuickStart.p12. O nome do arquivo e o local podem ser diferentes.

2. Faça logon no [Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

3. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

   ![][27]

4. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   ![][28]

5. Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**), e clique na guia **Configurar** na parte superior.

   ![][29]

6. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   ![][210]

7. Selecione a guia **Configurar** na parte superior e clique em **Carregar** para as configurações de notificação da Apple. Em seguida, selecione o certificado **.p12** que você exportou anteriormente e a senha para o certificado. Certifique-se de escolher se você deseja usar a **Produção** (se desejar enviar notificações por push para os usuários que adquiriram seu aplicativo na loja) ou o serviço de envio por push **Área Segura** (durante o desenvolvimento).

   ![][211]

8. Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   ![][212]

Seu hub de notificação agora está configurado para funcionar com o APNs e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

##Conectando seu aplicativo ao Hub de Notificação

1. No XCode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**.

   ![][31]

2. Em **Destinos**, clique no nome do projeto e expanda a opção **Identidade de Assinatura de Código** e, em seguida, em **Depurar**, selecione o perfil de identidade de assinatura de código. Além disto, se estiver usando uma versão mais recente do XCode, altere a opção **Níveis** de **Básico** para **Todos** e defina o item de linha **Perfil de Provisionamento** para o perfil de provisionamento.

   ![][32]

3. Baixe o SDK de Serviços Móveis do Windows Azure para iOS. Abra o arquivo .zip e arraste a pasta WindowsAzureMessaging.framework para a pasta Estrutura em seu projeto do XCode. Selecione **Copiar itens na pasta do grupo de destino** e, em seguida, clique em **OK**.

   ![][33]

4. Em seu arquivo AppDelegate.h, adicione a seguinte diretiva de importação:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. Em seu arquivo AppDelegate.m, adicione o seguinte código no método `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Para iOS 8
   
	 UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

6. No mesmo arquivo, adicione o seguinte método:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(opcional)* Novamente, no mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** caso a notificação seja recebida enquanto o aplicativo estiver ativo:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. Execute o aplicativo em seu dispositivo.

##Enviar notificações de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) Neste tutorial você envia as notificações com um aplicativo de console do .NET. Para obter um exemplo de como enviar notificações a partir de um back-end de Serviços Móveis do Azure integrado com os Hubs de Notificação, consulte **Introdução às notificações por push nos Serviços Móveis** ([back-end do .NET](../mobile-services-javascript-backend-ios-get-started-push.md) | [back-end do JavaScript](../mobile-services-javascript-backend-ios-get-started-push.md)). Para obter um exemplo de como enviar notificações usando as APIs FREST, consulte **Como usar os Hubs de Notificação a partir do Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. No Visual Studio, no menu **Arquivo** selecionar **Novo** e, em seguida, **Projeto...**, em seguida, em **Visual C#** clique no **Windows** e **Aplicativo de console** e clique em **OK**.  

   ![][20]

	This creates a new console application project.

2. No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**.

	Isso exibe a Console do Gerenciador de Pacotes.

6. Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus

	Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>.

5. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

6. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Aém disso, substitua o espaço reservado da cadeia de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação".

	>[AZURE.NOTE]Verifique se você está usando a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

5. Em seguida, adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

5. Pressione a tecla F5 para executar o aplicativo de console.

	Você deverá receber um alerta em seu dispositivo. Se você estiver usando Wi-Fi, certifique-se de que a conexão esteja funcionando.

Você encontrará todas as cargas possíveis no [Guia de Programação Local e de Notificação por Push] da Apple.

##Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos iOS. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].



<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[SDK do iOS dos Serviços Móveis]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-ios-mobile-services-register-user-push-notifications.md
[Usar Hubs de Notificação para enviar notícias de última hora]: notification-hubs-ios-send-breaking-news.md

[Guia de Programação Local e de Notificação por Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


<!--HONumber=52-->
 