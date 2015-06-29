<properties 
	pageTitle="Tutorial de últimas notícias localizadas de Hubs de Notificação para iOS" 
	description="Saiba como usar Hubs de notificação do barramento de serviço do Azure para enviar notificações localizadas de últimas notícias (iOS)." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>
# Usar Hubs de Notificação para enviar últimas notícias localizadas para dispositivos iOS

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows Store C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>

##Visão geral

Este tópico mostra como usar o recurso **template** dos Hubs de Notificação do Azure para transmitir notificações de últimas notícias que foram localizadas por idioma e dispositivo.  Neste tutorial você começa com o aplicativo da Windows Store criado em [Usar Hubs de Notificação para enviar últimas notícias].  Ao concluir, você poderá se registrar em categorias de seu interesse, especificar um idioma no qual deseja receber as notificações e receber notificações por push para as categorias selecionadas nesse idioma.


Há duas partes que compõem esse cenário:

- o aplicativo iOS permite que dispositivos cliente especifiquem um idioma e se inscrevam em diferentes categorias de últimas notícias;

- o back-end transmite as notificações usando os recursos **tag** e **template** dos Hubs de Notificação do Azure.



##Pré-requisitos

Você já deve ter concluído o tutorial [Usar Hubs de Notificação para envio de últimas notícias] e ter o código disponível, porque este tutorial se baseia diretamente no código.

O Visual Studio 2012 também é necessário.



##Conceitos de modelo

Em [Usar Hubs de Notificação para envio de últimas notícias] você criou um aplicativo que usou **marcas** para assinar notificações para diferentes categorias de notícias.
No entanto, muitos aplicativos são destinados a vários mercados e requerem localização.  Isso significa que o próprio conteúdo das notificações deve ser localizado e entregue para o conjunto de dispositivos correto.
Neste tópico, mostraremos como usar o recurso **template** de Hubs de Notificação para entregar facilmente notificações de últimas notícias localizadas.

Observação: uma maneira de enviar notificações localizadas é criar várias versões de cada marca.  Por exemplo, para oferecer suporte a inglês, francês e mandarim, precisamos de três marcas diferentes para notícias do mundo:  "world_en", "world_fr" e "world_ch".  Em seguida, precisamos enviar uma versão localizada das notícias do mundo para cada uma dessas marcas.  Neste tópico, usamos modelos para evitar a proliferação de marcas e a necessidade de enviar várias mensagens.

Em um alto nível, os modelos são uma maneira de especificar como um dispositivo específico deve receber uma notificação.  O modelo especifica o formato exato da carga referindo-se às propriedades que fazem parte da mensagem enviada por seu aplicativo de back-end.  Em nosso caso, enviaremos uma mensagem independente de localidade contendo todos os idiomas com suporte:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Em seguida, verificaremos se os dispositivos se registram com um modelo que faz referência à propriedade correta.  Por exemplo, um aplicativo iOS que deseje se registrar para notícias em francês registrará o seguinte:

	{
		aps:{
			alert: "$(News_French)"
		}
	}

Os modelos são um recurso muito avançado sobre o qual você pode aprender em nosso artigo [Diretrizes dos Hubs de Notificação].  Uma referência para a linguagem de expressão do modelo está em nosso [Como:  Hubs de notificação de Barramento de Serviço (aplicativos iOS)].

##A interface do usuário do aplicativo

Agora vamos modificar o aplicativo Breaking News que você criou no tópico [Usar Hubs de Notificação para envio de últimas notícias] para enviar últimas notícias localizadas usando modelos.


No MainStoryboard_iPhone.storyboard, adicione um Controle Segmentado com três idiomas que oferecemos suporte:  Inglês, francês e Mandarim.

![][13]

Em seguida, adicione um IBOutlet em seu ViewController.h, conforme mostrado abaixo:

![][14]

##Criando o aplicativo iOS

Para adaptar seus aplicativos cliente para receber mensagens localizadas, você precisa substituir seus registros  *nativos* (ou seja, registros que você especifica em um modelo) pelos registros do modelo.

1. No Notification.h, adicione o método  *retrieveLocale* e modifique os métodos store e subscribe conforme mostrado abaixo:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	No Notification.m, modifique o método  *storeCategoriesAndSubscribe*, adicionando o parâmetro locale e armazenando-o nos padrões do usuário:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Em seguida, modifique o método  *subscribe* para incluir o locale:

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{"aps":{"alert":"$(News_%@)"},"inAppMessage":"$(News_%@)"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	Observe como agora estamos usando o método *registerTemplateWithDeviceToken*, em vez do  *registerNativeWithDeviceToken*.  Ao registrar um modelo, precisamos fornecer o modelo json e também um nome para o modelo (uma vez que nosso aplicativo talvez queira registrar diferentes modelos).  Registre suas categorias como marcas, pois queremos garantir que receberemos as notificações sobre essas notícias.

	Finalmente, adicione um método para recuperar o locale das configurações padrão do usuário:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. Agora que modificamos nossa classe Notifications, temos que verificar que nosso ViewController use o novo UISegmentControl.  Adicione a seguinte linha ao método  *viewDidLoad* para ter certeza de mostrar o local que está selecionado no momento:

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	Em seguida, no seu método  *subscribe*, altere sua chamada para  *sstoreCategoriesAndSubscribe* para o seguinte:

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

4. Finalmente, você deve atualizar o método *didRegisterForRemoteNotificationsWithDeviceToken* em seu AppDelegate.m, de forma que você possa atualizar corretamente seu registro quando seu aplicativo for iniciado.   Altere sua chamada para o método  *subscribe* de notificações com o seguinte:

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##Enviar notificações localizadas de seu back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]


## Próximas etapas

Para obter mais informações sobre o uso de modelos, consulte:

- [Notificar usuários com Hubs de Notificação:  ASP.NET]
- [Notificar usuários com Hubs de Notificação:  Serviços Móveis]
- [Diretrizes dos Hubs de Notificação]

Uma referência à linguagem de expressão do modelo por ser encontrada em [Instruções sobre Hubs de Notificação para iOS].






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[Como:  Hubs de notificação de Barramento de Serviço (aplicativos iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usar hubs de notificação para enviar notícias recentes]: /manage/services/notification-hubs/breaking-news-ios
[Serviço Móvel]: /develop/mobile/tutorials/get-started
[Notificar usuários com Hubs de Notificação:  ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notificar usuários com Hubs de Notificação:  Serviços Móveis]: /manage/services/notification-hubs/notify-users
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /develop/mobile/tutorials/get-started-with-push-ios
[Notificações por push para usuários do aplicativo]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizar usuários com scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript e HTML]: ../get-started-with-push-js.md

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Etapas de registro do Windows Developer Preview nos Serviços Móveis]: ../mobile-services-windows-developer-preview-registration.md
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para iOS]: http://msdn.microsoft.com/library/jj927168.aspx

<!--HONumber=49--> 