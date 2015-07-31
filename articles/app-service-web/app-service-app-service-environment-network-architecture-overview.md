<properties 
	pageTitle="Visão geral da arquitetura de rede dos Ambientes de Serviço de Aplicativo" 
	description="Visão geral da arquitetura da topologia de rede dos Ambientes de Serviço de Aplicativo." 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="stefsch"/>

# Visão geral da arquitetura de rede dos Ambientes de Serviço de Aplicativo

## Introdução ##
Os Ambientes de Serviço de Aplicativo sempre são criados em uma sub-rede de uma [rede virtual][virtualnetwork]. Aplicativos em execução em um Ambiente de Serviço de Aplicativo podem se comunicar com pontos de extremidade privados localizados na mesma topologia de rede virtual. Uma vez que os clientes podem bloquear partes da respectiva infraestrutura de rede virtual, é importante entender os tipos de fluxo de comunicação de rede que ocorrem com um Ambiente de Serviço de Aplicativo.

## Fluxo de rede geral ##
 
Um Ambiente de Serviço de Aplicativo sempre tem um endereço IP virtual público (VIP). Todo o tráfego de entrada chega nesse VIP público, incluindo o tráfego HTTP e HTTPS para aplicativos, bem como outro tráfego para FTP, funcionalidade de depuração remota e operações de gerenciamento do Azure. Para obter uma lista completa de portas específicas (necessárias e opcionais) que estão disponíveis no VIP público, consulte o artigo sobre [Como controlar o tráfego de entrada][controllinginboundtraffic] para um Ambiente de Serviço de Aplicativo.

O diagrama abaixo mostra uma visão geral dos vários fluxos de rede de entrada e saída:

![Fluxos de rede geral][GeneralNetworkFlows]

Um Ambiente de Serviço de Aplicativo pode se comunicar com uma variedade de pontos de extremidade privados do cliente. Por exemplo, aplicativos que são executados no Ambiente de Serviço de Aplicativo podem se conectar a servidores de banco de dados em execução em máquinas virtuais de IaaS na mesma topologia de rede virtual.

Os Ambientes de Serviço de Aplicativo também se comunicam com recursos do Banco de Dados SQL e Armazenamento do Azure necessários para gerenciar e operar um Ambiente de Serviço de Aplicativo. Alguns dos recursos do SQL e Armazenamento com os quais o Ambiente de Serviço de Aplicativo se comunica estão localizados na mesma região do Ambiente de Serviço de Aplicativo, enquanto outros estão localizados em regiões remotas do Azure. Consequentemente, a conectividade de saída com a Internet sempre é exigida para que um Ambiente de Serviço de Aplicativo funcione corretamente.

Uma vez que o Ambiente de Serviço de Aplicativo é implantado em uma sub-rede, os grupos de segurança de rede podem ser usados para controlar o tráfego de entrada para a sub-rede. Para obter detalhes sobre como controlar o tráfego de entrada para um Ambiente de Serviço de Aplicativo, consulte este [artigo][controllinginboundtraffic].

Para obter detalhes sobre como permitir a conectividade de saída da Internet de um Ambiente de Serviço de Aplicativo, consulte o seguinte artigo sobre como trabalhar com a [Rota Expressa][ExpressRoute]. A mesma abordagem descrita no artigo se aplica ao trabalhar com conectividade site a site e usar túnel forçado.

## Endereços de rede de saída ##
Quando um Ambiente de Serviço de Aplicativo faz chamadas de saída, um endereço IP sempre é associado às chamadas de saída. O endereço IP específico que é usado depende de onde o ponto de extremidade que está sendo chamado está localizado, se dentro ou fora da topologia de rede virtual.

Se o ponto de extremidade que está sendo chamado estiver **fora** da topologia de rede virtual, o endereço de saída (também conhecido como endereço NAT de saída) que será usado é o VIP público do Ambiente de Serviço de Aplicativo. Esse endereço pode ser encontrado na interface do usuário do portal para o Ambiente de Serviço de Aplicativo (Observação: UX pendente).

Esse endereço também pode ser determinado pela criação de um aplicativo no Ambiente de Serviço de Aplicativo e pela execução de um *nslookup* no endereço do aplicativo. O endereço IP resultante é o VIP público, bem como o endereço NAT de saída do Ambiente de Serviço de Aplicativo.

Se o ponto de extremidade que está sendo chamado estiver **dentro** da topologia de rede virtual, o endereço de saída do aplicativo que está chamando será o endereço IP interno do recurso de computação individual que está executando o aplicativo. No entanto, não há um mapeamento persistente de endereços IP internos de rede virtual para aplicativos. Os aplicativos podem se mover entre os diferentes recursos de computação e o pool de recursos de computação disponíveis em um Ambiente de Serviço de Aplicativo pode mudar devido às operações de dimensionamento.

No entanto, uma vez que o Ambiente de Serviço de Aplicativo está sempre localizado em uma sub-rede, você tem a garantia de que o endereço IP interno de um recurso de computação executando um aplicativo sempre estará no intervalo CIDR da sub-rede. Como resultado, quando ACLs refinadas ou grupos de segurança de rede são usados para proteger o acesso a outros pontos de extremidade na rede virtual, o intervalo de sub-rede que contém o Ambiente de Serviço de Aplicativo precisa ter acesso.

O diagrama a seguir mostra esses conceitos em mais detalhes:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

- Como o VIP público do Ambiente de Serviço de Aplicativo é 192.23.1.2, esse é o endereço IP de saída usado ao fazer chamadas para pontos de extremidade de "Internet".
- O intervalo CIDR da sub-rede contida para o Ambiente de Serviço de Aplicativo é 10.0.1.0/26. Outros pontos de extremidade na mesma infraestrutura de rede virtual verá chamadas de aplicativos como originados de algum lugar dentro desse intervalo de endereços.

## Informações e links adicionais ##
Os detalhes sobre portas de entrada usadas pelos Ambientes de Serviço de Aplicativo e como usar grupos de segurança de rede para controlar o tráfego de entrada estão disponíveis [aqui][controllinginboundtraffic].

Detalhes sobre como usar rotas definidas pelo usuário para conceder acesso à Internet de saída aos Ambientes de Serviço de Aplicativo estão disponíveis neste [artigo][ExpressRoute].


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png

<!---HONumber=July15_HO3-->