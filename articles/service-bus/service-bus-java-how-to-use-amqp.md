<properties 
	urldisplayname="Service Bus AMQP" 
	headerexpose="" 
	pageTitle="Como usar o AMQP 1.0 com a API de barramento de serviço do Java - Azure" 
	metakeywords="Java Messsage AMQP, Service Bus AMQP, download AMQP JMS library" 
	footerexpose="" 
	description="Saiba como usar o Java Message Service (JMS) com o barramento de serviço do Azure e Advanced Message Queuing Protocol (AMQP) 1.0." 
	umbraconavihide="0" 
	disquscomments="1" 
	authors="sethmanheim" 
	documentationCenter="java" 
	writer="sethm" 
	manager="timlt" 
	editor="mattshel" 
	services="service-bus"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="sethm"/>


# Como usar a API do Serviço de Mensagem Java (JMS) com Barramento de Serviço e AMQP 1.0

## Introdução

O Protocolo de Enfileiramento de Mensagens Avançadas (AMQP, Advanced Message Queuing Protocol) 1.0 é um protocolo de mensagens eficiente, confiável e conectado que pode ser usado para criar aplicativos de mensagens avançados entre plataformas. Suporte ao AMQP 1.0 foi adicionado ao Service Bus do Azure em outubro de 2012 e passou por transição para Disponibilidade Geral (GA) em maio de 2013.

A inclusão de AMQP 1.0 significa que agora você pode aproveitar o enfileiramento de mensagens e publicação/assinatura comunicação recursos de mensagem do Service Bus entre uma variedade de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos compostos de componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

Este Guia de Instruções explica como usar os recursos do sistema de mensagens agenciado do Barramento de Serviço (tópicos sobre filas e publicação/assinatura) de aplicativos Java usando o popular padrão de API do Java Message Service (JMS).

## Introdução ao Barramento de serviço

Este guia presume que você já tenha um namespace do Barramento de Serviço que contém uma fila denominada "queue1." Caso contrário, você pode criar o namespace e a fila usando o [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) Para obter mais informações sobre como criar namespaces e filas do Service Bus, consulte [Como usar filas do Barramento de Serviço](service-bus-dotnet-how-to-use-queues.md).

### Baixando a biblioteca do cliente do JMS do AMQP 1.0

Para obter informações sobre onde baixar a versão mais recente da biblioteca do cliente Apache Qpid JMS do AMQP 1.0, [acessehttp://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)

Você deve adicionar os seguintes quatro arquivos JAR do arquivamento de distribuição do Apache Qpid JMS do AMQP 1.0 ao CLASSPATH do Java ao criar e executar aplicativos do JMS com o Barramento de Serviço:

*    geronimo-jms_1.1_spec-1.0.jar
*    qpid-amqp-1-0-client-[version].jar
*    qpid-amqp-1-0-client-jms-[version].jar
*    qpid-amqp-1-0-common-[version].jar

## Codificando os aplicativos Java

### Java Naming and Directory Interface (JNDI)

O JMS usa a Java Naming and Directory Interface (JNDI) para criar uma separação entre nomes lógicos e físicos. Dois tipos de objetos JMS são resolvidos usando a JNDI: ConnectionFactory e Destino. A JNDI usa um modelo de provedor no qual você pode conectar diferentes serviços de diretório para lidar com tarefas de resolução de nome. A biblioteca Apache Qpid JMS do AMQP 1.0 vem com um Provedor JNDI simples baseado em arquivo de propriedades que é configurado usando um arquivo de propriedades no seguinte formato:

	# servicebus.properties – sample JNDI configuration
	
	# Register a ConnectionFactory in JNDI using the form:
	# connectionfactory.[jndi_name] = [ConnectionURL]
	connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
	
	# Register some queues in JNDI using the form
	# queue.[jndi_name] = [physical_name]
	# topic.[jndi_name] = [physical_name]
	queue.QUEUE = queue1


<p><strong>Configurando o ConnectionFactory</strong></p>

A entrada usada para definir um **ConnectionFactory** no Provedor JNDI de arquivo de propriedades do Qpid tem o seguinte formato:

	connectionfactory.[jndi_name] = [ConnectionURL]

Onde [jndi_name] e [ConnectionURL] têm os seguintes significados:

<table>
  <tr>
    <td>[jndi_name]</td>
    <td>O nome lógico do ConnectionFactory. Este é o nome que será resolvido no aplicativo Java usando o método IntialContext.lookup() do JNDI.</td>
  </tr>
  <tr>
    <td>[ConnectionURL]</td>
    <td>Uma URL que fornece à biblioteca JMS as informações necessárias para o agenciador do AMQP.</td>
  </tr>
</table>

O formato de **ConnectionURL** é o seguinte:

	amqps://[username]:[password]@[namespace].servicebus.windows.net

Onde [namespace], [nome de usuário] e [senha] têm os seguintes significados:

<table>
  <tr>
    <td>[namespace]</td>
    <td>O namespace do Service Bus é obtido no Portal de Gerenciamento do Azure.</td>
  </tr>
  <tr>
    <td>[Nome de Usuário]</td>
    <td>O nome do emissor do Service Bus é obtido no Portal de Gerenciamento do Azure.</td>
  </tr>
  <tr>
    <td>[Senha]</td>
    <td>A forma codificada da URL da chave do emissor do Barramento de Serviço é obtida no Portal de Gerenciamento do Azure.</td>
  </tr>
</table>

**Observação**: você deve executar uma codificação de URL da senha manualmente. Um utilitário útil de codificação de URL está disponível em [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Por exemplo, se as informações obtidas no Portal de Gerenciamento do Azure forem as seguintes:

<table>
  <tr>
    <td>Namespace:</td>
    <td>foo.servicebus.windows.net</td>
  </tr>
  <tr>
    <td>Nome do emissor:</td>
    <td>proprietário</td>
  </tr>
  <tr>
    <td>Chave do emissor:</td>
    <td>j9VYv1q33Ea+cbahWsHFYnLkEzrF0yA5SAqcLNvU7KM=</td>
  </tr>
</table>

Então, para definir um **ConnectionFactory** chamado "SBCF", a cadeia de configuração é exibida da seguinte maneira:

	connectionfactory.SBCF = amqps://owner:j9VYv1q33Ea%2BcbahWsHFYnLkEzrF0yA5SAqcLNvU7KM%3D@foo.servicebus.windows.net

<p><strong>Configurando destinos</strong></p>

A entrada usada para definir um destino no Provedor JNDI do arquivo de propriedades do Qpid tem o seguinte formato:

	queue.[jndi_name] = [physical_name]
ou

	topic.[jndi_name] = [physical_name]

Onde [jndi_name] e [physical_name] têm os seguintes significados:

<table>
  <tr>
    <td>[jndi_name]</td>
    <td>O nome lógico do destino. Este é o nome que será resolvido no aplicativo Java usando o método IntialContext.lookup() do JNDI.</td>
  </tr>
  <tr>
    <td>[physical_name]</td>
    <td>O nome da entidade do Barramento de Serviço para a qual o aplicativo envia ou recebe mensagens.</td>
  </tr>
</table>

**Observação**: ao receber de uma assinatura de tópico do Service Bus, o nome físico especificado na JNDI deve ser o nome do tópico. O nome da assinatura é fornecido quando a assinatura durável é criada no código do aplicativo JMS. O [Guia do Desenvolvedor do Service Bus do AMQP 1.0](http://msdn.microsoft.com/library/jj841071.aspx) fornece mais detalhes sobre como trabalhar com assinaturas de tópico do Service Bus a partir do JMS.

### Escrevendo o aplicativo JMS

Não existem APIs ou opções especiais obrigatórias ao usar o JMS com o Service Bus. No entanto, existem algumas restrições que serão abordadas posteriormente. Da mesma forma que ocorre com qualquer aplicativo JMS, a primeiro item necessário é a configuração do ambiente JNDI, para ser capaz de resolver um **ConnectionFactory** e destinos.

<p><strong>Configurando o InitialContext de JNDI</strong></p>

O ambiente JNDI é configurado por meio da transmissão de uma tabela de hash com informações de configuração para o construtor da classe javax.naming.InitialContext. Os dois elementos necessários da tabela de hash são o nome da classe de Initial Context Factory e a URL do Provedor. O código a seguir mostra como configurar o ambiente JNDI para usar o Provedor JNDI com base em arquivo de propriedades do Qpid com um arquivo de propriedades chamado **servicebus.properties**.

	Hashtable<String, String> env = new Hashtable<String, String>(); 
	env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
	env.put(Context.PROVIDER_URL, "servicebus.properties"); 
	InitialContext context = new InitialContext(env); 

### Um aplicativo JMS simples que usa uma fila do Barramento de Serviço

O programa de exemplo a seguir envia TextMessages do JMS para uma fila do Barramento de Serviço com o nome lógico de JNDI da FILA e recebe as mensagens de volta.

	// SimpleSenderReceiver.java
	
	import javax.jms.*;
	import javax.naming.Context;
	import javax.naming.InitialContext;
	import java.io.BufferedReader;
	import java.io.InputStreamReader;
	import java.util.Hashtable;
	import java.util.Random;
	
	public class SimpleSenderReceiver implements MessageListener {
	    private static boolean runReceiver = true;
	    private Connection connection;
	    private Session sendSession;
	    private Session receiveSession;
	    private MessageProducer sender;
	    private MessageConsumer receiver;
	    private static Random randomGenerator = new Random();
	
	    public SimpleSenderReceiver() throws Exception {
	        // Configure JNDI environment
	        Hashtable<String, String> env = new Hashtable<String, String>();
	        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
	        env.put(Context.PROVIDER_URL, "servicebus.properties");
	        Context context = new InitialContext(env);
	
	        // Lookup ConnectionFactory and Queue
	        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	        Destination queue = (Destination) context.lookup("QUEUE");
	
	        // Create Connection
	        connection = cf.createConnection();
	
	        // Create sender-side Session and MessageProducer
	        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
	        sender = sendSession.createProducer(queue);
	
	        if (runReceiver) {
	            // Create receiver-side Session, MessageConsumer,and MessageListener
	            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
	            receiver = receiveSession.createConsumer(queue);
	            receiver.setMessageListener(this);
	            connection.start();
	        }
	    }
	
	    public static void main(String[] args) {
	        try {
	
	            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
	                runReceiver = false;
	            }
	
	            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
	            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
	
	            while (true) {
	                String s = commandLine.readLine();
	                if (s.equalsIgnoreCase("exit")) {
	                    simpleSenderReceiver.close();
	                    System.exit(0);
	                } else {
	                    simpleSenderReceiver.sendMessage();
	                }
	            }
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	
	    private void sendMessage() throws JMSException {
	        TextMessage message = sendSession.createTextMessage();
	        message.setText("Test AMQP message from JMS");
	        long randomMessageID = randomGenerator.nextLong() >>>1;
	        message.setJMSMessageID("ID:" + randomMessageID);
	        sender.send(message);
	        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
	    }
	
	    public void close() throws JMSException {
	        connection.close();
	    }
	
	    public void onMessage(Message message) {
	        try {
	            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
	            message.acknowledge();
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	}	

### Executando o aplicativo

A execução do aplicativo produz a saída do formulário:

	> java SimpleSenderReceiver
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
	Sent message with JMSMessageID = ID:2867600614942270318
	Received message with JMSMessageID = ID:2867600614942270318
	
	Sent message with JMSMessageID = ID:7578408152750301483
	Received message with JMSMessageID = ID:7578408152750301483
	
	Sent message with JMSMessageID = ID:956102171969368961
	Received message with JMSMessageID = ID:956102171969368961
	exit

## Mensagens em plataformas cruzadas entre JMS e .NET

Este guia mostrou como enviar e receber mensagens de e para o Service Bus usando o JMS. No entanto, um dos principais benefícios do AMQP 1.0 é que ele permite que os aplicativos sejam criados a partir de componentes escritos em diferentes linguagens, com mensagens trocadas de forma confiável e com total fidelidade.

Usando a amostra do aplicativo JMS descrito acima e um aplicativo .NET similar retirado de um guia complementar, [Como usar o AMQP 1.0 com a API do .NET do Service Bus do .NET](http://aka.ms/lym3vk), é possível trocar mensagens entre o .NET e o Java.

Para obter mais informações sobre os detalhes de mensagens em plataformas cruzadas usando o Service Bus e o AMQP 1.0, consulte o [Guia do Desenvolvedor do Service Bus e AMQP 1.0](http://msdn.microsoft.com/library/jj841071.aspx).

### Do JMS para o .NET

Para demonstrar as mensagens do JMS para o .NET:

* Inicie a amostra do aplicativo do .NET sem nenhum argumento de linha de comando.
* Inicie a amostra do aplicativo Java com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila; ele somente as enviará.
* Pressione **Enter** algumas vezes no console do aplicativo Java; isso fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo .NET.

<p><strong>Saída do aplicativo JMS</strong></p>

	> java SimpleSenderReceiver sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with JMSMessageID = ID:4364096528752411591
	Sent message with JMSMessageID = ID:459252991689389983
	Sent message with JMSMessageID = ID:1565011046230456854
	exit

<p><strong>Saída do aplicativo .NET</strong></p>

	> SimpleSenderReceiver.exe	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with MessageID = 4364096528752411591
	Received message with MessageID = 459252991689389983
	Received message with MessageID = 1565011046230456854
	exit

### Do .NET para o JMS

Para demonstrar as mensagens do .NET para o JMS:

* Inicie a amostra do aplicativo do .NET com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila; ele somente as enviará.
* Inicie a amostra do aplicativo do Java sem nenhum argumento de linha de comando.
* Pressione **Enter** algumas vezes no console do aplicativo .NET, isso fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo Java.

<p><strong>Saída do aplicativo .NET</strong></p>

	> SimpleSenderReceiver.exe sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


<p><strong>Saída do aplicativo JMS</strong></p>

	> java SimpleSenderReceiver	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

## Restrições e recursos sem suporte

As restrições a seguir ocorrem durante o uso do JMS sobre o AMQP 1.0 com o Service Bus, ou seja:

* Apenas um **MessageProducer** ou **MessageConsumer** é permitido por **Sessão**. Se precisar criar vários **MessageProducers** ou **MessageConsumers** em um aplicativo, crie uma **Session** dedicada para cada um deles.
* Assinaturas de tópico voláteis atualmente não têm suporte.
* **MessageSelectors** não são atualmente suportados.
* Destinos temporários como, por exemplo, **TemporaryQueue**, **TemporaryTopic** não são suportados, juntamente com as APIs de **QueueRequestor** e **TopicRequestor** que os utilizam.
* Não há suporte para as sessões transacionadas e transações distribuídas.

## Resumo

Este guia de instruções explicou como usar os recursos de mensagens agenciadas do Service Bus (tópicos sobre filas e publicação/assinatura) do Java usando a API popular JMS e o AMQP 1.0.

Você também pode usar o AMQP 1.0 do Service Bus de outras linguagens, incluindo .NET, C, Python e PHP. Os componentes criados com essas diferentes linguagens podem trocar mensagens de forma confiável e com total fidelidade usando o suporte do AMQP 1.0 no Service Bus. Para obter mais informações, consulte o [Guia do Desenvolvedor do AMQP 1.0 do Service Bus](http://msdn.microsoft.com/library/jj841071.aspx).

## Próximas etapas

* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](http://aka.ms/pgr3dp)
* [Como usar os tópicos AMQP 1.0 com o Service Bus .NET API](http://aka.ms/lym3vk)
* [Guia do desenvolvedor do AMQP 1.0 do Barramento de Serviço](http://msdn.microsoft.com/library/jj841071.aspx)
* [Como usar as filas do Barramento de Serviço](http://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)
 

<!---HONumber=62-->