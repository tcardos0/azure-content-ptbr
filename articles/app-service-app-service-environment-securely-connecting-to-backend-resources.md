<properties 
	pageTitle="Conexão segura a recursos de Back-end a partir de um ambiente de Serviço de Aplicativo" 
	description="Saiba como realizar conexão segura a recursos de back-end a partir de um ambiente de Serviço de Aplicativo." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# Conexão segura a recursos de back-end a partir de um ambiente de Serviço de Aplicativo #

## Visão geral ##
Como um ambiente de serviço de aplicativo sempre é criado em uma sub-rede de uma [rede virtual][virtualnetwork] regional, conexões de saída de um ambiente de serviço de aplicativo para outros recursos de back-end podem fluir exclusivamente pela rede virtual.

Por exemplo, pode haver um SQL Server em execução em um cluster de máquinas virtuais com a porta 1433 bloqueada. O ponto de extremidade pode ser ACLd, para permitir apenas acesso de outros recursos na mesma rede virtual.

Como outro exemplo, pontos de extremidade confidenciais podem ser executados localmente e conectados ao Azure via conexões [Site a Site][SiteToSite] ou [Rota Expressa do Azure][ExpressRoute]. Como resultado, apenas os recursos nas redes virtuais conectadas a túneis Site a Site ou de rota expressa poderão acessar pontos de extremidade locais.

Para todos esses cenários, aplicativos em execução em um ambiente de serviço de aplicativo serão capazes de se conectar com segurança aos diversos servidores e recursos. Tráfego de saída de aplicativos que são executados em um ambiente de serviço de aplicativo para pontos de extremidade privados na mesma rede virtual \(ou conectados à mesma rede virtual\) passará apenas pela rede virtual. O tráfego de saída para pontos de extremidade privados não passará pela Internet pública.


## Conectando-se a um SQL Server
Uma configuração comum do SQL Server tem um ponto de extremidade escutando na porta 1433:

![Ponto de extremidade do SQL Server][SqlServerEndpoint]

Há duas abordagens para restringir o tráfego para esse ponto de extremidade:


- [Listas de controle de acesso a redes][NetworkAccessControlLists] \(ACLs de rede\)

- [Grupos de segurança de rede][NetworkSecurityGroups]


## Restringindo o acesso com uma ACL de rede

A porta 1433 pode ser protegida usando uma lista de controle de acesso de rede. O exemplo abaixo coloca endereços de cliente originados dentro de uma rede virtual em lista branca e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controle de acesso de rede][NetworkAccessControlListExample]

Quaisquer aplicativos em execução no ambiente de serviço de aplicativo na mesma rede virtual que o SQL Server serão capazes de se conectarem à instância do SQL Server usando o endereço IP **VNet interno** para a máquina virtual do SQL Server.

A cadeia de conexão de exemplo a seguir faz referência ao SQL Server usando seu endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Embora a máquina virtual também tenha um ponto de extremidade público, tentativas de conexão usando o endereço IP público serão rejeitadas devido à ACL de rede.

## Restringindo o acesso com um grupo de segurança de rede
Uma abordagem alternativa para proteger o acesso é com um grupo de segurança de rede. Grupos de segurança de rede podem ser aplicados a máquinas virtuais individuais ou a uma sub-rede que contenha as máquinas virtuais.

Primeiro é preciso criar um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso apenas ao tráfego VNet interno é muito simples com um grupo de segurança de rede. As regras padrão em um grupo de segurança de rede somente permitem o acesso a partir de outros clientes de rede na mesma rede virtual.

Como resultado, bloquear o acesso ao SQL Server é tão simples quanto a aplicação de um grupo de segurança de rede com suas regras padrão às máquinas virtuais executando o SQL Server ou então à sub-rede que contém as máquinas virtuais.

O exemplo a seguir aplica a um grupo de segurança de rede à sub-rede que o contém:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
O resultado final é um conjunto de regras de segurança que bloqueiam o acesso externo, permitindo acesso interno VNet:

![Regras de segurança de rede padrão][DefaultNetworkSecurityRules]


## Introdução

Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes sobre como controlar o tráfego de entrada para seu ambiente de serviço de aplicativo, consulte [Controlando o tráfego de entrada para um ambiente de serviço de aplicativo][ControlInboundASE]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://msdn.microsoft.com/library/azure/dn376541.aspx
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png
<!--HONumber=52-->