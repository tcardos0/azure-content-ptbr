<properties 
   pageTitle="Arquitetura do Barramento de Serviço"
   description="Descreve a arquitetura de processamento de mensagens do Barramento de Serviço do Azure."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="sethm" />

# Arquitetura do Barramento de Serviço

A seção a seguir descreve a arquitetura de processamento de mensagens do Barramento de Serviço do Azure.

## Unidades de escala do Barramento de Serviço

O Barramento de Serviço é organizado por *unidades de escala*. Uma unidade de escala é uma unidade de implantação que contém todos os componentes necessários para executar o serviço. Cada região implanta uma ou mais unidades de escala do Barramento de Serviço.

Um namespace de Barramento de Serviço é mapeado para uma unidade de escala. A unidade de escala manipula todos os tipos de entidades do Barramento de Serviço: retransmissões, entidades do sistema de mensagens agenciado (filas, tópicos, assinaturas) e hubs de notificação. Uma unidade de escala do Barramento de Serviço consiste nos seguintes componentes:

- **Um conjunto de nós de gateway.** Os nós de gateway autenticam as solicitações de entrada e manipulam as solicitações de retransmissão. Cada nó de gateway tem um endereço IP público.

- **Um conjunto de nós do agente de mensagens.** Os nós do agente de mensagens processam solicitações referentes às entidades de mensagens.

- **Um conjunto de nós de notificação.** Os nós de notificação enviam notificações por push para todos os dispositivos registrados.

- **Um repositório de gateway.** O repositório de gateway mantém os dados para cada entidade definida nessa unidade de escala. O repositório de gateway é implementado sobre um banco de dados do SQL do Azure.

- **Muitos repositórios de mensagens.** Os repositórios de mensagens mantêm as mensagens de todas as filas, tópicos e assinaturas que são definidas na unidade de escala. Eles também contêm todos os dados de assinatura. A menos que o [Particionamento entidades de mensagens](https://msdn.microsoft.com/library/azure/dn520246.aspx) esteja habilitado, uma fila ou tópico é mapeado para um repositório de mensagens. As assinaturas são armazenadas no mesmo repositório de mensagens que o tópico pai. Os repositórios de mensagens são implementados no topo de bancos de dados do SQL Azure.

- **Vários repositórios de registros.** Os repositórios de registros contêm registros de dispositivos para todos os hubs de notificação que são definidos na unidade de escala. Os repositórios de registros são implementados no topo de bancos de dados do SQL Azure.

## Contêineres

Cada entidade de mensagens é atribuída a um contêiner específico. Um contêiner é um constructo lógico que utiliza exatamente um repositório de mensagens para todos os dados relevantes para esse contêiner. Cada contêiner é atribuído a um nó do agente de mensagens. Normalmente, há mais contêineres que nós do agente de mensagens. Portanto, cada nó do agente de mensagens carrega diversos contêineres. A distribuição de contêineres para um nó do agente de mensagens é organizada de modo que todos os nós do agende de mensagens sejam carregados igualmente. Se o padrão de carga mudar (por exemplo, um dos contêineres ficar muito ocupado) ou se um nó do agente de mensagens ficar indisponível temporariamente, os contêineres serão redistribuídos entre os nós de agente de mensagens.

## Processamento de mensagens de solicitações de entrada

Quando um cliente envia uma solicitação ao Barramento de Serviço, o balanceador de carga do Azure direciona para qualquer um de nós do gateway. O nó do gateway autoriza a solicitação. Se a solicitação for referente a uma entidade de mensagens (fila, tópico, assinatura), o nó do gateway busca a entidade no repositório de gateway e determina em qual repositório de mensagens a entidade está localizada. Depois, ele procura qual nó do agente de mensagens está servindo o contêiner e envia a solicitação para esse nó. O nó do agente de mensagens processa a solicitação e atualiza o estado da entidade no repositório do contêiner. O nó do agente de mensagens, em seguida, envia a resposta de volta ao nó do gateway, que envia uma resposta apropriada para o cliente que emitiu a solicitação original.

![Processamento de mensagens de solicitações de entrada](./media/service-bus-architecture/IC690644.png)

## Processamento de mensagens de solicitações de retransmissão

Quando um cliente envia uma solicitação ao Barramento de Serviço, o balanceador de carga do Azure direciona para qualquer um de nós do gateway. Se a solicitação for uma solicitação de escuta, o nó do gateway cria uma nova retransmissão. Se a solicitação for uma solicitação de conexão com uma retransmissão específica, o nó do gateway encaminha a solicitação de conexão para o nó do gateway que tem a retransmissão. O nó do gateway que tem a retransmissão envia uma solicitação de encontro ao cliente de escuta, pedindo ao ouvinte para criar um canal temporário para o nó do gateway que recebeu a solicitação de conexão.

Quando a conexão de retransmissão é estabelecida, os clientes podem trocar mensagens por meio do nó do gateway usado para o encontro.

![Processamento de mensagens de solicitações de retransmissão](./media/service-bus-architecture/IC690645.png)

## Processamento de solicitações recebidas do hub de notificação

Quando um cliente envia uma solicitação ao Barramento de Serviço, o balanceador de carga do Azure direciona para qualquer um de nós do gateway. Se a solicitação for um registro de dispositivo para um hub de notificação existente, o nó do gateway grava o registro no repositório de registros e envia uma resposta ao dispositivo de chamada. Se a solicitação for uma mensagem de notificação, o nó do gateway coloca a mensagem em uma fila de notificação. Um dos nós de notificação tira a mensagem da fila de notificação e envia a mensagem a todos os dispositivos que estão registrados no repositório de registros. Se uma mensagem for destinada a um grande número de dispositivos, vários nós de notificação participarão do envio das mensagens para os dispositivos.

![Processamento de solicitações recebidas do hub de notificação](./media/service-bus-architecture/IC690646.png)

## Próximas etapas

Agora que você teve uma visão geral de como funciona o Barramento de Serviço, para começar, visite os links a seguir:

- [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
- [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Uma solução de mensagens na fila usando filas do Barramento de Serviço](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

<!---HONumber=August15_HO6-->