<properties
	pageTitle="Introdução a trabalhos de banco de dados elástico"
	description="como usar trabalhos de banco de dados elástico"
	services="sql-database"
	documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="sidneyh; ddove" />

# Introdução a trabalhos de Banco de Dados Elástico

Trabalhos de Banco de Dados Elástico (visualização) para o Banco de Dados SQL do Azure permite que você execute, de modo confiável, scripts T-SQL que abrangem vários bancos de dados, repetindo tentativas automaticamente e fornecendo eventuais garantias de conclusão. Para obter mais informações sobre o recurso de trabalho de Banco de Dados Elástico, consulte a [página de visão geral do recurso](sql-database-elastic-jobs-overview.md).

Este tópico estende o exemplo encontrado na [Introdução às ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md). Quando concluído, você aprenderá a criar e gerenciar trabalhos que gerenciam um grupo de bancos de dados relacionados.

## Pré-requisitos

Baixe e execute a [exemplo da Introdução às ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).

## Criar um gerenciador de mapa de fragmentos usando o aplicativo de exemplo

Aqui você vai criar um gerenciador de mapa de fragmentos juntamente com vários fragmentos, seguido pela inserção de dados nos fragmentos. Se você já tem fragmentos configurados com dados fragmentados, poderá ignorar as etapas a seguir e mover para a próxima seção.

1. Compile e execute o aplicativo de exemplo da **Introdução às ferramentas de Banco de Dados Elástico**. Siga as etapas até a etapa 7 na seção [Baixe e execute o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). No final da etapa 7, você verá o seguinte prompt de comando:

	![prompt de comando][1]

2.  Na janela Comando, digite "1" e pressione **Enter**. Isso cria o gerenciador de mapa de fragmentos e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e pressione **Enter**. Repita a ação mais quatro vezes. Isso insere linhas de dados de exemplo no seus fragmentos.

3.  O [Portal de Visualização do Azure](https://portal.azure.com) deve mostrar três novos bancos de dados em seu servidor v12:

	![Confirmação do Visual Studio][2]

	Neste ponto, vamos criar uma coleção de bancos de dados personalizados que reflete todos os bancos de dados no mapa de fragmentos. Isso nos permitirá criar e executar um trabalho que adicione uma nova tabela em fragmentos.

Agora, normalmente criaríamos aqui um destino para o mapa de fragmentos, usando o cmdlet **New-AzureSqlJobTarget**. O banco de dados do gerenciador do mapa de fragmentos deve ser definido como um destino de banco de dados e, em seguida, o mapa de fragmentos específico é especificado como um destino. Em vez disso, vamos enumerar todos os bancos de dados no servidor e adicionar os bancos de dados à nova coleção personalizada, com a exceção de banco de dados mestre.

##Cria uma coleção personalizada e adiciona todos os bancos de dados no servidor ao destino de coleção personalizada, com exceção do mestre.


	$customCollectionName = "dbs_in_server"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
	$ResourceGroupName = "ddove_samples"
	$ServerName = "samples"
	$dbsinserver = Get-AzureSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
	$dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
	
## Criar um script T-SQL para execução em bancos de dados

	$scriptName = "NewTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
	BEGIN
		CREATE TABLE Test(
			TestId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

##Criar o trabalho para executar um script no grupo personalizado de bancos de dados

	$jobName = "create on server dbs"
	$scriptName = "NewTable"
	$customCollectionName = "dbs_in_server"
	$credentialName = "ddove66"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job


##Executar o trabalho 

O script do PowerShell a seguir pode ser usado para executar um trabalho existente:

Atualize a variável a seguir para refletir o nome do trabalho desejado a ser executado:

	$jobName = "create on server dbs"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Recuperar o estado de uma única execução de trabalho

Use o mesmo cmdlet **Get-AzureSqlJobExecution** com o parâmetro **IncludeChildren** para exibir o estado de execuções de trabalhos filho, ou seja, o estado específico de cada execução do trabalho em relação a cada banco de dados a que o trabalho se destina.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Exibir o estado em várias execuções de trabalho

O cmdlet **Get-AzureSqlJobExecution** tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de trabalho, filtradas por meio dos parâmetros fornecidos. O exemplo a seguir demonstra algumas das possíveis maneiras de usar o Get-AzureSqlJobExecution:

Recupere todas as execuções de trabalhos ativos de nível superior:

	Get-AzureSqlJobExecution

Recupere todas as execuções do trabalho de nível superior, incluindo execuções de trabalhos inativos:

	Get-AzureSqlJobExecution -IncludeInactive

Recupere todas as execuções de trabalhos filho de uma ID de execução de trabalho fornecida, incluindo execuções de trabalhos inativos:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recupere todas as execuções de trabalho criadas usando uma combinação de agenda/trabalho, incluindo trabalhos inativos:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recupere todos os trabalhos direcionados a um mapa de fragmentos especificado, incluindo trabalhos inativos:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recupere todos os trabalhos direcionados a uma coleção personalizada especificada, incluindo trabalhos inativos:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recupere a lista de execuções de tarefas de trabalho contidas na execução de um trabalho específico:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Recupere detalhes de execução de tarefa de trabalho:

O script do PowerShell a seguir pode ser usado para exibir os detalhes de uma execução de tarefa de trabalho, o que é especialmente útil ao depurar falhas de execução.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Recuperar falhas em execuções de tarefa de trabalho

O objeto JobTaskExecution inclui uma propriedade Ciclo de Vida da tarefa, junto com uma propriedade Mensagem. Se uma execução de tarefa de trabalho falhar, a propriedade Ciclo de Vida será definida como *Falha* e a propriedade Mensagem será definida como a mensagem de exceção resultante e sua pilha. Se um trabalho não foi bem-sucedido, é importante exibir os detalhes das tarefas de trabalho que não foram bem-sucedidas para um determinado trabalho.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Aguardando a conclusão da execução de um trabalho

O script do PowerShell a seguir pode ser usado para aguardar a conclusão de uma tarefa de trabalho:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Criar uma política de execução personalizada

O recurso trabalhos de Banco de Dados Elástico dá suporte à criação de políticas de execução personalizadas, que podem ser aplicadas ao iniciar trabalhos.
  
Atualmente, as políticas de execução permitem definir:

* Nome: o identificador para a política de execução.
* Tempo Limite do Trabalho: tempo total antes que um trabalho seja cancelado por Trabalhos de Banco de Dados Elástico.
* Intervalo de Repetição Inicial: o intervalo de espera antes de primeira repetição de tentativa.
* Intervalo Máximo de Repetição: limite de intervalos de repetição a usar.
* Coeficiente de Retirada de Intervalo de Repetição: coeficiente usado para calcular o próximo intervalo entre as repetições de tentativas. A fórmula a seguir é usada: (Intervalo de Repetição Inicial) * Math.pow((Coeficiente de Retirada do Intervalo), (Número de Novas Tentativas) - 2). 
* Máximo de Tentativas: o número máximo de novas tentativas a repetir em um trabalho.

A política de execução padrão usa os seguintes valores:

* Nome: política de execução padrão
* Tempo Limite do Trabalho: 1 semana
* Intervalo de Repetição Inicial: 100 milissegundos
* Intervalo Máximo de Repetição: 30 minutos
* Coeficiente de Intervalo de Repetição: 2
* Máximo de Tentativas: 2.147.483.647

Crie a política de execução desejada:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Atualizar uma política de execução personalizada

Atualize a política de execução que deseja atualizar:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Cancelar um trabalho

Trabalhos de Banco de Dados Elástico dá suporte a solicitações de cancelamento de trabalhos. Se o trabalhos de Banco de Dados Elástico detecta uma solicitação de cancelamento de um trabalho que está atualmente em execução, ele tenta interromper o trabalho.

Há duas maneiras diferentes pelas quais o Trabalhos de Banco de Dados Elástico pode executar um cancelamento:

1. Cancelando tarefas atualmente em execução: se um cancelamento for detectado enquanto uma tarefa estiver em execução, será realizada uma tentativa de cancelamento no aspecto da tarefa atualmente em execução. Por exemplo: se houver uma consulta de execução longa sendo executada atualmente, quando houver uma tentativa de cancelamento, haverá também uma tentativa de cancelar a consulta.
2. Tentativas de Cancelar Tarefa: se um cancelamento é detectado pelo thread de controle antes de uma tarefa ser iniciada para execução, o thread de controle evitará iniciar a tarefa e declarará a solicitação como cancelada.

Se for solicitado um cancelamento de trabalho para um trabalho pai, a solicitação de cancelamento será atendida para o trabalho pai e todos os seus trabalhos filho.
 
Para enviar uma solicitação de cancelamento, use o cmdlet **Stop-AzureSqlJobExecution** e defina o parâmetro **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Excluir um trabalho por nome e pelo histórico do trabalho

Trabalhos de Banco de Dados Elástico dão suporte a exclusão assíncrona de trabalhos. Um trabalho pode ser marcado para exclusão e o sistema vai excluir o trabalho e todo o seu histórico de trabalho, depois que todas as execuções de trabalho para o trabalho em questão tenham sido concluídas. O sistema não cancelará automaticamente execuções de trabalhos ativos.

Em vez disso, Stop-AzureSqlJobExecution deve ser chamado para cancelar as execuções de trabalhos ativos.

Para disparar a exclusão de trabalho, use o cmdlet **Remove-AzureSqlJob** e defina o parâmetro **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Criar um destino de banco de dados personalizado
Destinos personalizados de banco de dados podem ser definidos em trabalhos de Banco de Dados Elástico, que podem ser usados para execução direta ou para inclusão em um grupo personalizado de bancos de dados. Uma vez que **pools de Banco de Dados Elástico** ainda não têm suporte direto por meio das APIs do PowerShell, basta simplesmente criar um destino de banco de dados personalizado e um destino de coleção de bancos de dados personalizada que englobe todos os bancos de dados no pool.

Defina as variáveis a seguir para refletirem as informações de banco de dados desejadas:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Criar um destino para a coleção de bancos de dados personalizada
Um destino para coleção de bancos de dados personalizada pode ser definido para habilitar a execução de vários destinos de banco de dados definidos. Após a criação de um grupo de banco de dados, bancos de dados podem ser associados ao destino da coleção personalizada.

Defina as variáveis a seguir para refletir a configuração desejada para destino da coleção personalizada:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Adicionar bancos de dados a um destino da coleção de bancos de dados personalizada

Destinos de banco de dados podem ser associados com destino de coleção de bancos de dados personalizada para criar um grupo de bancos de dados. Sempre que é criado um trabalho que tem como alvo um destino de coleção de bancos de dados personalizada, esse trabalho será expandido para bancos de dados associados ao grupo no momento da execução.

Adicione o banco de dados desejado a uma coleção personalizada específica:

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Examinar os bancos de dados contidos em um destino de coleção de bancos de dados personalizada

Use o cmdlet **Get-AzureSqlJobTarget** para recuperar os bancos de dados filho dentro de um destino de coleção de bancos de dados personalizada.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Criar um trabalho para executar um script em um destino de coleção de bancos de dados personalizada

Use o cmdlet **New-AzureSqlJob** para criar um trabalho para um grupo de bancos de dados definidos por um destino de coleção de bancos de dados personalizada. Trabalhos de Banco de Dados Elástico vão expandir o trabalho em vários trabalhos filho, cada um correspondendo a um banco de dados associado ao destino de coleção de bancos de dados personalizada e assegurando que o script seja executado em cada banco de dados. Novamente, é importante que os scripts sejam idempotentes para que sejam resistentes em relação a novas tentativas.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Coleta de dados em bancos de dados

**Trabalhos do Banco de Dados Elástico** dá suporte à execução de uma consulta em um grupo de bancos de dados e envia os resultados a uma tabela do banco de dados especificado. A tabela pode ser consultada após o fato para ver os resultados da consulta provenientes de cada banco de dados. Isso fornece um mecanismo assíncrono para executar uma consulta em vários bancos de dados. Casos de falha - como, por exemplo, um dos bancos de dados estar temporariamente indisponível - são tratados automaticamente por meio de novas tentativas.

A tabela de destino especificada será criada automaticamente se ela ainda não existir correspondendo ao esquema do conjunto de resultados retornado. Se uma execução de script retornar vários conjuntos de resultados, o trabalhos de Banco de Dados Elástico enviará somente o primeiro à tabela de destino fornecida.

O script PowerShell a seguir pode ser usado para executar um script coletando os resultados em uma tabela especificada. Este script presume que foi criado um script T-SQL, que produz um único conjunto de resultados; além disso, um destino de coleção de bancos de dados personalizada foi criado.

Defina o seguinte para refletir o script, credenciais e destino de execução desejados:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### Criar e iniciar um trabalho para cenários de coleta de dados
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Criar um agendamento para execução de trabalho usando um gatilho de trabalho

O script de PowerShell a seguir pode ser usado para criar uma agenda recorrente. Esse script usa um intervalo de minutos, mas o New-AzureSqlJobSchedule também dá suporte aos parâmetros -DayInterval, -HourInterval, -MonthInterval e -WeekInterval. Agendas que são executadas apenas uma vez podem ser criadas pela passagem de -OneTime.

Crie uma nova agenda:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Criar um gatilho de trabalho para que um trabalho seja executado segundo um cronograma

Um gatilho de trabalho pode ser definido para fazer com que um trabalho seja executado segundo um cronograma. O script de PowerShell a seguir pode ser usado para criar um gatilho de trabalho.

Defina as variáveis a seguir para corresponder ao trabalho e agenda desejados:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Remover uma associação agendada para impedir o trabalho de ser executado segundo a agenda

Para interromper a execução do trabalho recorrente por meio de um gatilho de trabalho, esse gatilho pode ser removido. Remova um gatilho de trabalho para impedir que um trabalho seja executado de acordo com um agendamento usando o cmdlet **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## Importar resultados de consulta de banco de dados elástico para o Excel

 Você pode importar os resultados de uma consulta para um arquivo do Excel.

1. Inicie o Excel 2013.
2. 	Navegue até a faixa de opções **Dados**.
3. 	Clique em **De Outras Fontes** e depois em **Do SQL Server**.

	![Importação de outras fontes para o Excel][5]
4. 	No **Assistente para conexão de dados**, digite as credenciais de logon e nome do servidor. Em seguida, clique em **Próximo**.
5. 	Na caixa de diálogo **Selecione o banco de dados que contém os dados que você deseja**, selecione o banco de dados **ElasticDBQuery**.
6. 	Selecione a tabela **Clientes** na exibição de lista e clique em **Avançar**. Em seguida, clique em **Concluir**.
7. 	No formulário **Importar dados** em **Selecione como deseja exibir esses dados na sua pasta de trabalho**, selecione **Tabela** e clique em **OK**.

Todas as linhas da tabela **Clientes**, armazenada em fragmentos diferentes, populam a planilha do Excel.

## Próximas etapas
Agora você pode usar funções de dados avançados do Excel. Você pode usar a cadeia de conexão com o nome do servidor, nome do banco de dados e credenciais para conectar suas ferramentas de integração de dados e BI ao banco de dados de consulta elástico. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Você pode consultar o banco de dados de consulta elástico e tabelas externas como qualquer outro banco de dados e tabela do SQL Server que se conectariam à sua ferramenta.

### Custo
Não há nenhum custo adicional para usar o recurso de consulta de Banco de Dados Elástico. No entanto, neste momento esse recurso está disponível apenas em bancos de dados Premium como um ponto de extremidade, mas os fragmentos podem ser de qualquer camada de serviço.

Para obter informações sobre os preços, consulte [Detalhes de preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=August15_HO7-->