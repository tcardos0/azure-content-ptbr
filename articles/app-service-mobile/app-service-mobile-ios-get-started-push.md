<properties
	pageTitle="Adicionar notificações por push ao aplicativo iOS com aplicativos móveis do Azure"
	description="Saiba como usar aplicativos móveis do Azure para enviar notificações por push para seu aplicativo iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="krisragh"/>


# Adicionar as notificações por push ao seu aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do iOS] para que, sempre que um registro for inserido, uma notificação por push seja enviada. Este tutorial baseia-se no tutorial de [início rápido do iOS], que você deve concluir primeiro. Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

O [simulador de iOS não dá suporte às notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), portanto, para este tutorial, você precisa de um dispositivo iOS físico e uma [associação ao Programa de Desenvolvedores da Apple](https://developer.apple.com/programs/ios/).

##<a name="create-hub"></a>Criar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Registre o aplicativo para obter notificações por push

[AZURE.INCLUDE [Habilitar Notificações por push da Apple](../../includes/enable-apple-push-notifications.md)]

## Configurar o Azure para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Atualizar projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Adicionar notificações por push ao aplicativo

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Testar notificações por push no aplicativo

[AZURE.INCLUDE [Testar notificações por push no aplicativo](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Mais

* Os modelos fornecem a flexibilidade para enviar pushes de plataforma cruzada e pushes localizados. [Como usar a biblioteca de cliente do iOS para aplicativos móveis do Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra como registrar os modelos.
* As marcas permitem que você tenha como alvo clientes segmentados com pushes. [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) mostra como adicionar marcas à instalação de um dispositivo.

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[início rápido do iOS]: app-service-mobile-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->