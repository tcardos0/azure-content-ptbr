<properties
	pageTitle="Introdução ao Azure Data Factory (Azure PowerShell)"
	description="Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando o Azure PowerShell."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Introdução ao Azure Data Factory (Azure PowerShell)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Neste artigo, você aprenderá a usar o Azure PowerShell para criar seu primeiro data factory do Azure.


## Pré-requisitos
Além dos pré-requisitos listados no tópico Visão Geral do Tutorial, você precisa instalar o seguinte:

- Você **deve** ler o artigo [Visão Geral do Tutorial](data-factory-build-your-first-pipeline.md) e concluir as etapas de pré-requisito antes de continuar.
- **PowerShell do Azure**. Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a última versão do Azure PowerShell no computador.
- (opcional) Este artigo não cobre todos os cmdlets de Data Factory. Consulte [Referência de cmdlet de Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) para obter uma documentação abrangente sobre os cmdlets de Data Factory. 

Se você estiver usando o Azure PowerShell com a **versão < 1.0**, você precisará usar os cmdlets que estão documentados [aqui][cmdlet-reference]. Você também precisará executar os seguintes comandos antes de usar os cmdlets do Data Factory:
 
1. Inicie o Azure PowerShell e execute os comandos a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	1. Execute **Add-AzureAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.
	2. Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	3. Execute **Select-AzureSubscription** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
4. Alterne para o modo AzureResourceManager, pois os cmdlets do Azure Data Factory estão disponíveis nesse modo: **Switch-AzureMode AzureResourceManager**.

Veja [Substituição da opção AzureMode no Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell) para obter os detalhes.


## Etapa 1: Criar o data factory

Nesta etapa, é possível usar o Azure PowerShell para criar um Azure Data Factory denominado **FirstDataFactoryPSH**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar o script do Hive para transformar os dados de entrada em dados de saída do produto. Vamos começar com a criação do data factory nesta etapa.

1. Inicie o Azure PowerShell e execute o comando a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	- Execute **Login-AzureRmAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.  
	- Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	- Execute **Select-AzureSubscription <Name of the subscription>** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
3. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado ADFTutorialResourceGroup. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial.
4. Execute o cmdlet **New-AzureRmDataFactory** para criar um data factory denominada **FirstDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT] O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "FirstDataFactoryPSH" não está disponível**, altere o nome (por exemplo, seunomeFirstDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	> 
	> O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.

Antes de criar um pipeline, primeiro você precisará criar algumas entidades do Data Factory. Primeiro você cria serviços vinculados para vincular serviços de armazenamento/computação de dados ao seu armazenamento de dados, define conjuntos de dados de entrada e de saída para representar os dados em armazenamentos de dados vinculados e, em seguida, cria o pipeline com uma atividade que utilize esses conjuntos de dados.

## Etapa 2: Criar serviços vinculados 
Nesta etapa, você vinculará sua conta do Armazenamento do Azure e um cluster do HDInsight do Azure sob demanda ao data factory. A conta do Armazenamento do Azure manterá os dados de entrada e de saída para o pipeline neste exemplo. O serviço vinculado do HDInsight é usado para executar o script do Hive especificado na atividade do pipeline neste exemplo. Você precisa identificar quais serviços de armazenamento/computação de dados serão usados em seu cenário e vincular esses serviços ao data factory criando serviços vinculados.

### Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vinculará a conta do Armazenamento do Azure ao data factory. Para o tutorial, use a mesma conta do Armazenamento do Azure para armazenar dados de entrada/saída e o arquivo do script do HQL.

1. Crie um arquivo JSON chamado StorageLinkedService.json na pasta C:\\ADFGetStarted com o conteúdo a seguir. Crie a pasta ADFGetStarted se ela ainda não existir.

		{
	    	"name": "StorageLinkedService",
	    	"properties": {
	        	"type": "AzureStorage",
	        	"description": "",
	        	"typeProperties": {
	            	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        	}
	    	}
		}

	Substitua o **nome da conta** pelo nome da conta de armazenamento do Azure e a **chave de conta** pela chave de acesso da sua conta do armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. No Azure PowerShell, alterne para a pasta ADFGetStarted.
3. Você pode usar o cmdlet **New-AzureDataFactoryLinkedService** para criar um serviço vinculado. Esse cmdlet e outros cmdlets de Data Factory que você usa neste tutorial exigem que os valores sejam passados aos parâmetros *ResourceGroupName* e *DataFactoryName*. Como alternativa, é possível usar **Get-AzureRmDataFactory** para obter um objeto **DataFactory** e passar o objeto sem digitar *ResourceGroupName* e *DataFactoryName* sempre que você executar um cmdlet. Execute o comando a seguir para atribuir a saída do cmdlet **Get-AzureDataFactory** a uma variável **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Agora, execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se você não tivesse executado o cmdlet **Get-AzureRmDataFactory** e atribuído a saída à variável **$df**, precisaria especificar valores para os parâmetros *ResourceGroupName* e *DataFactoryName*, como demonstrado a seguir.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	Se você fechar o Azure PowerShell no meio do tutorial, precisará executar o cmdlet **Get-AzureRmDataFactory** na próxima vez que iniciar o Azure PowerShell para concluir o tutorial.

### Criar o serviço vinculado do Azure HDInsight
Nesta etapa, você vinculará um cluster do HDInsight sob demanda ao seu data factory. O cluster do HDInsight é automaticamente criado no tempo de execução e excluído após a conclusão do processamento, ficando ocioso durante o período especificado. Você pode usar seu próprio cluster do HDInsight em vez de usar um cluster do HDInsight sob demanda. Veja [Serviços vinculados de computação](data-factory-compute-linked-services.md) para obter detalhes.

1. Crie um arquivo JSON chamado **HDInsightOnDemandLinkedService**.json na pasta **C:\\ADFGetStarted** com o conteúdo a seguir.

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:

	| Propriedade | Descrição |
	| :------- | :---------- |
	| Versão | Isso especifica a versão do HDInsight criada como a 3.2. | 
	| ClusterSize | Cria um cluster do HDInsight de um nó. | 
	| TimeToLive | Especifica tempo ocioso de cluster HDInsight antes de ser excluído. |
	| linkedServiceName | Especifica a conta de armazenamento que será usada para armazenar os logs gerados pelo HDInsight |

	Observe o seguinte:
	
	- O Data Factory cria para você um cluster HDInsight **baseado no Windows** com o JSON acima. Você também pode fazer com que ele crie um cluster HDInsight **baseado em Linux**. Veja [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
	- Você pode usar **seu próprio cluster do HDInsight** em vez de usar um cluster do HDInsight sob demanda. Veja [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
	- O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado em JSON (**linkedServiceName**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Esse comportamento é intencional. Com o serviço vinculado do HDInsight sob demanda, um cluster HDInsight será criado sempre que uma fatia precisar ser processada, a menos que haja um cluster ativo existente (**timeToLive**) e que ele seja excluído quando o processamento for concluído.
	
		À medida que mais e mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. O nome desses contêineres segue um padrão: "adf**nomedodatafactory**-**nomedoserviçovinculado**-carimbodedatahora". Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

	Veja [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
2. Execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado denominado HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Etapa 3: Criar conjuntos de dados
Nesta etapa, você criará conjuntos de dados para representar dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados fazem referência ao **StorageLinkedService** que você criou anteriormente neste tutorial. Os pontos de serviço apontam para uma conta do Armazenamento do Azure e os conjuntos de dados especificam o contêiner, a pasta, o nome no armazenamento que contém os dados de entrada e de saída.

### Criar o conjunto de dados de entrada
1. Crie um arquivo JSON denominado **InputTable.json** na pasta **C:\\ADFGetStarted** com o seguinte conteúdo:

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	O JSON acima define um conjunto de dados chamado **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, ele especifica que os dados de entrada estão localizados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **inputdata**.

	A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:

	| Propriedade | Descrição |
	| :------- | :---------- |
	| type | A propriedade type é definida como AzureBlob porque os dados residem no armazenamento de blobs do Azure. |  
	| linkedServiceName | refere-se ao StorageLinkedService que você criou anteriormente. |
	| fileName | Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Nesse caso, somente o input.log será processado. |
	| type | Os arquivos de log estão em formato de texto, então utilizaremos TextFormat. | 
	| columnDelimiter | as colunas nos arquivos de log são delimitadas por , (vírgula) |
	| frequência/intervalo | a frequência é definida como Mês e o intervalo como 1, o que significa que as fatias de entrada estão disponíveis mensalmente. | 
	| externo | essa propriedade será definida como true se os dados de entrada não forem gerados pelo serviço Data Factory. | 

2. Execute o comando a seguir no Azure PowerShell para criar o conjunto de dados do Data Factory.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Criar o conjunto de dados de saída
Agora, você criará o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. Crie um arquivo JSON denominado **OutputTable.json** na pasta **C:\\ADFGetStarted** com o seguinte conteúdo:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

	O JSON acima define um conjunto de dados chamado **AzureBlobOutput**, que representa dados de saída para uma atividade no pipeline. Além disso, ele especifica que os resultados são armazenados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.

2. Execute o comando a seguir no Azure PowerShell para criar o conjunto de dados do Data Factory.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Etapa 3: Criar seu primeiro pipeline
Nesta etapa, você criará seu primeiro pipeline com a atividade **HDInsightHive**. Observe que a fatia de entrada está disponível mensalmente (frequência: mês, intervalo: 1), a fatia de saída é produzida mensalmente e a propriedade do agendador para a atividade também é definida como mensal (veja abaixo). As configurações para o conjunto de dados de saída e o agendador de atividades devem corresponder. Neste momento, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. As propriedades usadas no JSON a seguir são explicadas no final desta seção.


1. Crie um arquivo JSON denominado MyFirstPipelinePSH.json na pasta C:\\ADFGetStarted com o conteúdo a seguir:

	> [AZURE.IMPORTANT] Substitua **storageaccountname** pelo nome da sua conta de armazenamento no JSON.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	No trecho de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.
	
	O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **StorageLinkedService**) e na pasta **script** no contêiner **adfgetstarted**.

	A seção **defines** é usada para especificar as configurações de tempo de execução que serão passadas para o script do hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

	Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.
2.  Confirme que vê o arquivo **input.log** na pasta **adfgetstarted/inputdata** no armazenamento de blobs do Azure e execute o comando a seguir para implantar o pipeline. Como as horas de **início** e de **término** são definidas no passado e **isPaused** está definido como false, o pipeline (a atividade no pipeline) é imediatamente executado após a implantação. 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Parabéns, você criou com sucesso seu primeiro pipeline usando o Azure PowerShell!

### <a name="MonitorDataSetsAndPipeline"></a> Monitorar os conjuntos de dados e o pipeline
Nesta etapa, você usará o Azure PowerShell para monitorar o que está acontecendo em um Azure Data Factory.

1. Execute **Get-AzureRmDataFactory** e atribua a saída a uma variável **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Observe que o valor de StartDateTime especificado aqui é a mesma hora de início especificada no pipeline de JSON. Você deve ver saídas semelhantes às seguintes.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. Execute **Get-AzureRmDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia específica.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Você deve ver saídas semelhantes às seguintes.
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Pode continuar executando este cmdlet até ver a fatia no estado **Pronto** ou **Falha**. Quando a fatia estiver no estado Pronto, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída. Observe que a criação de um cluster do HDInsight sob demanda geralmente leva algum tempo.

	![dados de saída](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight do Azure sob demanda. Para saber como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: Copiar dados de um blob do Azure para o SQL do Azure](./data-factory-get-started.md).

### Referências
| Tópico | Descrição |
| :---- | :---- |
| [Referência de cmdlet do Data Factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) | Consulte a documentação abrangente sobre os cmdlets do Data Factory |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajudará a compreender pipelines e atividades no Azure Data Factory e como aproveitá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | o artigo escrito por ele ajudará você a entender os conjuntos de dados no Azure Data Factory.
| [Planejamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines. Ele também fornece informações sobre como criar alertas e ser notificado sobre falhas. |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_0224_2016-->