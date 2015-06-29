<properties 
	pageTitle="Introdução aos Hubs de Evento" 
	description="Siga este tutorial para começar a usar Hubs de eventos do Azure enviando eventos com C# e recebendo-os em um cluster do Apache Storm" 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="csharp" 
	ms.devlang="csharp" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Introdução aos Hubs de Evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introdução

Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte [Visão geral de Hubs de Eventos].

Neste tutorial, você aprenderá como receber mensagens em um Hub de Eventos usando um aplicativo de console em C# e recuperá-los em paralelo usando Apache Storm.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio Express 2013 para Windows

+ Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos pressupor o [Eclipse](https://www.eclipse.org/)

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

## Criar um Hub de Evento

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de aplicativo**, em seguida, **Barramento de serviço**, em seguida, **Hub de eventos**e, em seguida, **Criação rápida**.

   	![][1]

3. Digite um nome para o Hub de Evento, selecione a região desejada e clique em **Criar um novo Hub de Evento**.

   	![][2]

4. Clique no namespace que você acabou de criar (geralmente ***event hub name*-ns**).

   	![][3]

5. Clique na guia **Hubs de Evento** parte superior da página e clique no Hub de Evento que acabou de criar.

   	![][4]

6. Clique na guia **Configurar** na parte superior da página, adicione uma regra chamada **SendRule** com direitos *Send*, adicione outra regra chamada **ReceiveRule** com direitos *Listen* e clique em **Salvar**.

   	![][5]

7. Na mesma página, tome nota das chaves geradas para **ReceiveRule**.

   	![][6c]

8. Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   	![][6]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	Execute a classe **LogTopology** do Eclipse, então espere para ela iniciar os receptores para todas as partições.

2.	Execute o projeto **Remetente**, pressione **Enter** na janela do console e veja os eventos aparecerem na janela do receptor.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Host do processador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de Eventos]: http://msdn.microsoft.com/library/azure/dn836025.aspx


<!--HONumber=52--> 