<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="Como começar a usar o armazenamento de tabela do Azure em um projeto do Azure WebJobs 5 no Visual Studio" 
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
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# Introdução ao Armazenamento do Azure (Projetos WebJob do Azure)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-tables.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-webjobs-getting-started-blobs.md)
> - [Queues](vs-storage-webjobs-getting-started-queues.md)
> - [Tables](vs-storage-webjobs-getting-started-tables.md)



## Visão geral

O serviço de armazenamento de Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Confira [Como usar o Armazenamento de Tabelas do .NET](storage-dotnet-how-to-use-tables.md/#create-a-table "Como usar o Armazenamento de Tabela no .NET") para saber mais.

Este guia fornece exemplos de código em C# que mostram como usar o SDK do Azure WebJobs versão 1.x com o serviço de armazenamento de tabela do Azure. Os exemplos de código usam o [SDK WebJobs](websites-dotnet-webjobs-sdk.md) versão 1.x.

		
Alguns dos trechos de código mostram o atributo `Table` usado nas funções que são [chamadas manualmente](vs-storage-webjobs-getting-started-blobs.md#manual), ou seja, que não usam um dos atributos de acionamento.

##Como adicionar entidades a uma tabela

Para adicionar entidades em uma tabela, use o atributo `Table` com um parâmetro `ICollector<T>` ou `IAsyncCollector<T>`, no qual `T` especifica o esquema das entidades que você deseja adicionar. O construtor de atributo tem um parâmetro de cadeia que especifica o nome da tabela.

O exemplo de código a seguir adiciona entidades `Person` em uma tabela nomeada *entrada*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

Geralmente, o tipo que você usa com `ICollector` deriva de `TableEntity` ou implementa `ITableEntity`, mas ele não precisa fazer isso. Qualquer uma das seguintes classes `Person` funciona com o código mostrado no método `Ingress` anterior.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Para trabalhar diretamente com a API de armazenamento do Azure, você pode adicionar um parâmetro `CloudStorageAccount` à assinatura do método.

##Monitoramento em tempo real

Como as funções de entrada de dados geralmente processam grandes volumes de dados, o painel do SDK de Trabalhos Web fornece dados de monitoramento em tempo real. A seção **Log de Invocação** informa se a função ainda está em execução.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A página **Detalhes de Invocação** relata o progresso da função (número de entidades gravadas) enquanto ela está em execução e lhe dá a oportunidade de anulá-la.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Quando a função é concluída, a página **Detalhes de Invocação** relata o número de linhas gravadas.

![Função de entrada concluída](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

##Como ler várias entidades de uma tabela

Para ler uma tabela, use o atributo `Table` com um parâmetro `IQueryable<T>`, em que tipo `T` deriva de `TableEntity` ou implementa `ITableEntity`.

O seguinte exemplo de código lê e registra em log todas as linhas da tabela `Ingress`:
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

###Como ler uma única entidade de uma tabela

Há um construtor de atributo `Table` com dois parâmetros adicionais que permite especificar a chave de partição e a chave de linha quando você deseja associar a uma entidade de tabela única.

O seguinte exemplo de código lê uma linha de tabela para uma entidade `Person` com base nos valores de chave de partição e chave de linha recebidos em uma mensagem de fila:

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


A classe `Person` nesse exemplo não precisa implementar `ITableEntity`.

##Como usar a API de Armazenamento .NET diretamente para trabalhar com uma tabela

Você também pode usar o atributo `Table` com um objeto `CloudTable` para ter mais flexibilidade ao trabalhar com uma tabela.

A seguinte amostra de código usa um objeto `CloudTable` para adicionar uma única entidade de *entrada* à tabela.
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Para obter mais informações sobre como usar o objeto `CloudTable`, consulte [Como usar o Armazenamento de Tabela do .NET](../storage-dotnet-how-to-use-tables.md).

##Tópicos relacionados abordados no artigo de instruções sobre filas

Para obter informações sobre como lidar com o processamento de tabelas acionado por uma mensagem da fila ou para cenários do SDK de Trabalhos Web não específicos do processamento de tabelas, consulte [Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web](vs-storage-webjobs-getting-started-queues.md).



##Próximas etapas

Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com tabelas do Azure. Para obter mais informações sobre como usar os Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure – Recursos recomendados](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=August15_HO6-->