<properties 
    pageTitle="Nova configuração do Banco de Dados SQL com o PowerShell | Microsoft Azure" 
    description="Saiba como criar um novo banco de dados SQL com o PowerShell. As tarefas comuns de configuração de banco de dados podem ser gerenciadas por meio de cmdlets do PowerShell." 
    keywords="criar um novo banco de dados sql, configuração do banco de dados"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# Criar um novo banco de dados SQL e executar tarefas comuns de instalação de banco de dados com cmdlets do PowerShell 

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


Saiba como criar um novo banco de dados SQL e executar tarefas comuns de instalação de banco de dados usando cmdlets do PowerShell.


Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

- Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.


## Configurar suas credenciais e selecionar sua assinatura

Agora, executando o módulo Gerenciador de Recursos do Azure, você terá acesso a todos os cmdlets necessários para criar um banco de dados SQL.

Primeiramente, você deve estabelecer acesso à sua conta do Azure, de modo que ao executar o cmdlet a seguir seja exibida uma tela de conexão para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	Add-AzureRmAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, você precisa da ID da assinatura. Você pode copiar essas informações da etapa anterior ou, se tiver várias assinaturas, poderá executar o cmdlet **Get-AzureRmSubscription** e copiar as informações da assinatura desejada do conjunto de resultados. Quando tiver sua assinatura, execute o seguinte cmdlet:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureRmSubscription** com êxito, você retornará ao prompt do PowerShell. Se tiver mais de uma assinatura, você poderá executar **Get-AzureRmSubscription** e verificar se a assinatura que deseja usar mostra **IsCurrent: True**.

## Configuração do banco de dados: criar um grupo de recursos, servidor e regra de firewall

Agora que você tem acesso para executar cmdlets em sua assinatura do Azure selecionada, a próxima etapa é estabelecer o grupo de recursos que contém o servidor no qual o banco de dados será criado. Você pode editar o próximo comando a fim de usar qualquer local válido de sua escolha. Execute **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** para obter uma lista de locais válidos.

Execute o seguinte comando para criar um novo grupo de recursos:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Depois de criar com êxito o novo grupo de recursos, você verá informações na tela que incluem **ProvisioningState : Succeeded**.


### Criar um servidor 

Os Bancos de Dados SQL são criados dentro dos servidores de Banco de Dados SQL. Execute **New-AzureRmSqlServer** para criar um novo servidor. Substitua ServerName pelo nome do servidor. Esse nome deve ser exclusivo a todos os Servidores SQL do Azure, de modo que você receberá um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Você pode editar o comando para usar qualquer local válido escolhido, mas deve usar o mesmo local que usou para o grupo de recursos criado na etapa anterior.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Ao executar esse comando, uma janela é aberta solicitando um **Nome de usuário** e **Senha**. Essas não são suas credenciais do Azure, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.

Os detalhes do servidor são exibidos após a criação bem-sucedida do servidor.

### Configurar uma regra de firewall de servidor para permitir o acesso ao servidor

Estabeleça uma regra de firewall para acessar o servidor. Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu computador.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes da regra de firewall são exibidos após a criação bem-sucedida da regra.

Para permitir que outros serviços do Azure acessem o servidor, adicione uma regra de firewall e defina StartIpAddress e EndIpAddress como 0.0.0.0. Observe que isso permite que o tráfego do Azure de qualquer assinatura do Azure acesse o servidor.

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md).


## Criar um novo banco de dados SQL

Agora que você tem um grupo de recursos, um servidor e uma regra de firewall configurados, é possível acessar o servidor.

O comando a seguir cria um novo (em branco) banco de dados SQL na camada de serviço Standard com um nível de desempenho S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes do banco de dados são exibidos após a criação bem-sucedida do banco de dados.

## Criar um novo script do PowerShell para o banco de dados SQL

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Próximas etapas
Depois de criar um novo banco de dados SQL e de realizar tarefas básicas de configuração de banco de dados, você estará pronto para o seguinte:

- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)


## Recursos adicionais

- [Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0121_2016-->