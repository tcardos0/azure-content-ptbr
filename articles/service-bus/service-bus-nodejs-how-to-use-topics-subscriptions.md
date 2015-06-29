<properties 
	pageTitle="Como usar tópicos do Barramento de serviço (Node.js) - Azure" 
	description="Aprenda a usar assinaturas e tópicos do barramento de serviço no Azure. Exemplos de código são escritos para aplicativos Node.js." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>






# Como Usar tópicos do barramento de serviço e assinaturas

Este guia descreve como usar tópicos do barramento de serviço e assinaturas de aplicativos Node.js. Os cenários abordados incluem a **criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas][].

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Criar um aplicativo do Node.js

Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure], [Serviço em Nuvem Node.js][Serviço de Nuvem Node.js] (usando o Windows PowerShell) ou [site com WebMatrix].

## Configurar seu aplicativo para usar o Barramento de serviço

Para usar o barramento de serviço, baixe o pacote do Node.js do azure. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1.  Use uma interface de linha de comando, como o **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix), e vá até a pasta onde você criou a amostra do aplicativo.

2.  Digite **npm install azure** na janela de comando, que deve
    resultar na seguinte saída:

         azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Você pode executar o comando **ls** manualmente para verificar se uma pasta **nó_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure**, que contém as bibliotecas necessárias para acessar os tópicos do Barramento de Serviço.

### Importar o módulo

Usando um bloco de notas, adicione o seguinte ao início do arquivo **server.js** do aplicativo:

    var azure = require('azure');

### Configurar uma conexão do Barramento de Serviço

O módulo azure lerá as variáveis de ambiente AZURE_SERVICEBUS_NAMESPACE e AZURE_SERVICEBUS_ACCESS_KEY para obter as informações necessárias para se conectar ao Barramento de serviço do Azure. Se essas variáveis de ambiente não estiverem definidas, você deve especificar as informações da conta ao chamar **createServiceBusService**.

Para obter um exemplo de como definir as variáveis de ambiente em um arquivo de configuração para um Serviço de Nuvem do Azure, consulte [Serviço de Nuvem do Node.js com Armazenamento].

Para obter um exemplo de como definir as variáveis de ambiente no portal de gerenciamento para um Site do Azure, confira [Aplicativo da Web do Node.js com Armazenamento].

## Como criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo ao início do arquivo **server.js**, após a instrução de importação do módulo azure:

    var serviceBusService = azure.createServiceBusService();

Ao chamar **createTopicIfNotExists** no objeto **ServiceBusService**, o tópico especificado (se houver) será retornado ou um novo tópico com o nome especificado será criado. O código a seguir usa
**createTopicIfNotExists** para criar ou conectar-se ao tópico denominado
'MyTopic':

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** também dá suporte para opções adicionais, que permitem a substituição de configurações padrão do tópico, como a vida útil da mensagem ou o tamanho máximo do tópico. O exemplo a seguir mostra a definição do tamanho máximo do tópico para 5 GB e da vida útil para 1 minuto:

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

### Filtros

É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

		function handle (requestOptions, next)

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura a seguir:

		function (returnObject, finalCallback, next)

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar avançar, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js,**ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## Como criar assinaturas

As assinaturas do tópico também são criadas com o objeto **ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

> [AZURE.NOTE] As assinaturas são persistentes e continuarão existindo até que elas ou o tópico ao qual estão associadas sejam excluídos. Se o seu aplicativo contiver a lógica para criar uma assinatura, ele deverá primeiro verificar se a assinatura já existe usando o método
**Método** getSubscription.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada  'AllMessages' e usa o filtro padrão **MatchAll**.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Criar assinaturas com os filtros

Você também pode configurar filtros que permitem atribuir um escopo a quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo mais flexível de filtro suportado por assinaturas é o
**SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Os filtros podem ser adicionados a uma assinatura usando o método **createRule**do objeto **ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

> [AZURE.NOTE]

> Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, você deve primeiro remover o filtro padrão ou o
<strong>MatchAll</strong> substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método <strong>deleteRule</strong> do objeto
<strong>ServiceBusService</strong> .

O exemplo a seguir cria uma assinatura denominada  'HighMessages' com um
**SqlFilter** que seleciona somente mensagens com uma propriedade
**messagenumber** personalizada maior que 3:

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada 'LowMessages' com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **messagenumber** menor ou igual a 3:

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

Quando uma mensagem é agora enviada para 'MyTopic', ela sempre será entregue aos destinatários inscritos para a  'AllMessages' assinatura do tópico e entregue de forma seletiva aos destinatários inscritos para o 'HighMessages' e
assinaturas do tópico 'LowMessages' (dependendo do conteúdo da mensagem).

## Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Barramento de Serviço, você deve usar o método **sendTopicMessage** do objeto **ServiceBusService**. As mensagens enviadas aos tópicos do Service Bus são objetos **BrokeredMessage**.
Objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário que é usado para armazenar propriedades específicas de aplicativo personalizado e um corpo de dados de cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem transmitindo um valor da cadeia ao**sendTopicMessage** e todas as propriedades padrão exigidas serão preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para 'MyTopic'. Observe que o valor da propriedade **messagenumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura o receberá):

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

Os tópicos de Barramento de Serviço oferecem suporte a um tamanho máximo de mensagem de 256 MB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 MB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## Como receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura, usando o
método **receiveSubscriptionMessage** do objeto **ServiceBusService**. Por padrão, as mensagens são excluídas da assinatura na medida em que são lidas. No entanto, você pode ler (espiar) e bloquear a mensagem sem excluí-la da assinatura, definindo o parâmetro opcional **isPeekLock** para **true**.

O comportamento padrão da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** estiver definido como **true**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo.
Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método **deleteMessage** e fornecendo a mensagem a ser excluída como um parâmetro. O método **deleteMessage** marcará a mensagem como tendo sido consumida e a removerá da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando **receiveSubscriptionMessage**. O exemplo primeiro recebe e exclui uma mensagem a partir da assinatura 'LowMessages' e, em seguida, recebe uma mensagem da assinatura 'HighMessages' usando
**isPeekLock** definido como true. Em seguida, ele exclui a mensagem usando
**deleteMessage**:

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo,ele chamará o método **unlockMessage** no objeto **ServiceBusService**. Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura, e se o aplicativo falhar em processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), então
Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete_subscription_message()** seja chamado, a mensagem será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é feito usando a
**MessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## Como excluir tópicos e assinaturas

Os tópicos e as assinaturas são persistentes e devem ser explicitamente excluídos por meio do portal de Gerenciamento do Azure ou de forma programática.
O exemplo a seguir demonstra como excluir o tópico denominado 'MyTopic':

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada
'HighMessages' do tópico 'MyTopic':

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas][].
-   Referência da API para [SqlFilter][].
-   Visite o repositório [SDK do Azure para o nó] no GitHub.

  [SDK do Azure para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Próximas etapas]: #nextsteps
  [O que são os tópicos e as assinaturas do Barramento de Serviço?]: #what-are-service-bus-topics
  [Criar um namespace de serviço]: #create-a-service-namespace
  [Obter as Credenciais de Gerenciamento Padrão para o Namespace]: #obtain-default-credentials
  [Criar um aplicativo do Node.js]: #Create_a_Nodejs_Application
  [Configurar seu aplicativo para usar o Barramento de serviço]: #Configure_Your_Application_to_Use_Service_Bus
  [Como: Criar um tópico]: #How_to_Create_a_Topic
  [Como: Criar assinaturas]: #How_to_Create_Subscriptions
  [Como: Enviar mensagens para um tópico]: #How_to_Send_Messages_to_a_Topic
  [Como: Receber mensagens de uma assinatura]: #How_to_Receive_Messages_from_a_Subscription
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Como: Excluir tópicos e assinaturas]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site com WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Serviço de Nuvem do Node.js]: /documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Criar e implantar um aplicativo do Node.js em um Site do Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js com Armazenamento]: /develop/nodejs/tutorials/web-app-with-storage/
  [Aplicativo Web Node.js com Armazenamento]: /develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=47-->
 