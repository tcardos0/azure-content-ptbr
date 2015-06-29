<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="Como começar a usar o armazenamento de fila do Azure em um projeto ASP.NET 5 no Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Introdução ao Armazenamento do Azure (Projetos ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte [Como usar o Armazenamento de Filas do .NET](storage-dotnet-how-to-use-queues.md/ "Como usar o Armazenamento de Fila no .NET") para obter mais informações.

Para acessar programaticamente filas em projetos do ASP.NET 5, você precisa adicionar os itens a seguir, se eles ainda não existirem.

1. Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. Use o seguinte código para obter a definição da configuração.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obter a cadeia de conexão do armazenamento
Antes de fazer qualquer coisa com uma fila, é necessário obter a cadeia de conexão da conta de armazenamento na qual as filas residirão. Você pode usar o tipo **CloudStorageAccount** para representar as informações da sua conta de armazenamento. Se estiver usando um projeto ASP.NET 5, você poderá chamar o método do objeto Configuration para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

**OBSERVAÇÃO:** as APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Confira [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para saber mais. O código a seguir pressupõe que os métodos de programação assíncrona estão sendo usados.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Criar uma fila
Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient**. Todos os códigos neste tópico usam uma cadeia de conexão de armazenamento armazenada na configuração dos serviços do aplicativo do Azure. Também existem outras maneiras de criar um objeto **CloudStorageAccount**. Confira a documentação da [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") para ver detalhes.

**OBSERVAÇÃO:** as APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Confira [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para saber mais. O código a seguir pressupõe que os métodos de programação assíncrona estão sendo usados.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que deseja usar. O código tenta fazer referência a uma fila denominada “myqueue”. Se não localizar uma fila com esse nome, ele criará uma.

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**OBSERVAÇÃO:** use todo esse código antes do código nas seções a seguir.

#####Inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila existente, crie primeiramente um novo objeto **CloudQueueMessage**. Em seguida, chame o método AddMessageAsync(). Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World'.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####Espiar a próxima mensagem
Você pode espiar a mensagem na frente de uma fila sem removê-la da fila, chamando o método PeekMessageAsync().

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####Remover a próxima mensagem
Seu código pode remover uma mensagem de uma fila em duas etapas.


1. Chame GetMessageAsync() para obter a próxima mensagem em uma fila. As mensagens retornadas de GetMessageAsync() tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. 
2.	Para concluir a remoção da mensagem da fila, chame DeleteMessageAsync(). 

Este processo de duas etapas para remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chamará DeleteMessageAsync() logo depois que a mensagem for processada.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Saiba mais sobre o armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/) Confira também [Procurando recursos de armazenamento no Gerenciador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx) e [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=58_postMigration-->