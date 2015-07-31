<properties 
   pageTitle="Visão geral de API de Hubs de Eventos"
   description="Um resumo de algumas das principais APIs de cliente .NET de Hubs de Eventos."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/10/2015"
   ms.author="sethm" />

# Visão geral de API de Hubs de Eventos

Este artigo resume algumas das principais APIs de cliente .NET de Hubs de Eventos. Há duas categorias: APIs de gerenciamento e de tempo de execução. APIs de tempo de execução consistem em todas as operações necessárias para enviar e receber uma mensagem. Operações de gerenciamento permitem gerenciar o estado da entidade Hubs de Eventos, ao criar, atualizar e excluir entidades.

Os cenários de monitoramento abrangem gerenciamento e tempo de execução. Para obter documentação de referência detalhada sobre as APIs do .NET, consulte a [Biblioteca de classes do .NET](https://msdn.microsoft.com/library/jj933431.aspx) e referências da [API EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.aspx).

## API de Gerenciamento

Para executar as seguintes operações de gerenciamento, você deve ter permissões para **Gerenciar** no namespace de Barramento de Serviço:

### Criar

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### Atualizar

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### Excluir

```
namespaceManager.DeleteEventHubAsync("event hub name").Wait();
```

## APIs de tempo de execução

### Criar publicador

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("event hub name");
```

### Publicar mensagem

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### Criar consumidor

```
// Create the Event Hub client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default subscriber group
EventHubSubscriberGroup defaultSubscriberGroup = eventHubClient.GetDefaultSubscriberGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### Consumir mensagem

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## APIs de host do processador de eventos

Essas APIs fornecem resiliência aos processos de trabalho que podem se tornar indisponíveis, mas distribuindo fragmentos entre os trabalhadores disponíveis.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.
string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

A interface [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx) é definida da seguinte maneira:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## Próximas etapas

Para saber mais sobre os cenários de Hubs de Eventos, consulte estes links:

- [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
- [Visão geral de Hubs de Evento](event-hubs-overview.md)
- [Exemplos de código de Hubs de Eventos](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)

As referências de API .NET estão aqui:

- [Referência de API do .NET de Hubs de Eventos e Barramento de Serviço](https://msdn.microsoft.com/library/jj933424.aspx)
- [Referência de API do host de processador de eventos](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)
- 

<!---HONumber=July15_HO3-->