<properties 
   pageTitle="Configurar balanceador de carga para SQL sempre ativo | Microsoft Azure"
   description="Configurar o balanceador de carga para trabalhar com o SQL sempre ativo e como aproveitar o PowerShell para criar o balanceador de carga para a implementação do SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/21/2015"
   ms.author="joaoma" />

# Configurar o balanceador de carga para SQL sempre ativo

Grupos de Disponibilidade AlwaysOn do SQL Server agora podem ser executados com ILB. O Grupo de Disponibilidade é uma solução fundamental do SQL Server para alta disponibilidade e recuperação de desastres. O Ouvinte do Grupo de Disponibilidade permite que aplicativos cliente conectem-se continuamente com a réplica primária, independentemente do número de réplicas na configuração.

O nome do ouvinte (DNS) é mapeado para um endereço IP com balanceamento de carga e o balanceador de carga do Azure direciona o tráfego de entrada apenas para o servidor primário no conjunto de réplicas.


Você pode usar o suporte do ILB para pontos de extremidade AlwaysOn do SQL Server (ouvinte). Agora você tem controle sobre a acessibilidade do ouvinte e pode escolher o endereço IP de balanceamento de carga de uma sub-rede específica em sua VNet (Rede Virtual).

Usando o ILB no ouvinte, o ponto de extremidade do SQL Server (por exemplo, Server=tcp:ListenerName,1433;Database=DatabaseName) é acessível somente por:

Serviços e VMs nos mesmos Serviços de Rede Virtual, VMs de Serviços de Rede locais conectadas e VMs de VNets interconectadas

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


Balanceador de carga interno só pode ser configurado por meio do PowerShell.


## Adicionar balanceador de carga interno ao serviço 

### Etapa 1.

No exemplo a seguir, configuraremos uma Rede Virtual que contém uma sub-rede chamada "Subnet-1":

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

Etapa 2.

## Adicionar pontos de extremidade de balanceamento de carga ao ILB em cada VM

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

No exemplo acima, você tem 2 VMs chamadas "sqlsvc1" e "sqlsvc2" em execução no serviço de nuvem "Sqlsvc". Depois de criar o ILB com a opção "DirectServerReturn", você adicionará pontos de extremidade com balanceamento de carga ao ILB para permitir que o SQL configure os ouvintes para os grupos de disponibilidade.

Você pode encontrar mais detalhes sobre a criação de um SQL AlwaysOn [na implantação do SQL AlwaysOn usando o modelo do Gerenciador de Recursos do Azure](virtual-machines-workload-template-sql-alwayson.md) ou [usando a Galeria do Portal](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).


## Consulte também

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-internet-getstarted.md)

[Introdução à configuração de um balanceador de carga interno](load-balancer-internal-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_1223_2015-->