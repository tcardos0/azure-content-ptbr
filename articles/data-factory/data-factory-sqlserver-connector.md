<properties 
	pageTitle="SQL Server Connector - mover dados de e para o SQL Server" 
	description="Saiba mais sobre o SQL Server Connector para o serviço Data Factory que permite mover dados de/para um banco de dados do SQL Server local ou em uma VM do Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# SQL Server Connector - mover dados para e do SQL Server local ou em IaaS (VM do Azure)

Este artigo descreve como você pode usar a Atividade de Cópia em uma Azure Data Factory para mover dados de outro repositório de dados para o SQL Server. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## Habilitando a conectividade

Os conceitos e as etapas necessários para conectar-se com o SQL Server hospedado localmente ou em máquinas virtuais do Azure IaaS (infraestrutura como serviço) são os mesmos. Em ambos os casos, você precisa utilizar o Gateway de gerenciamento de dados para ter conectividade.

Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway. Configurar uma instância de gateway é um pré-requisito para a conexão com o SQL Server.

Embora você possa instalar o gateway no mesmo computador local ou instância VM de nuvem que o SQL Server, é recomendável instalá-los em computadores separados ou na VM de nuvem, para obter melhor desempenho e evitar contenção de recursos.

## Exemplo: copiar dados do SQL Server para Blob do Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo OnPremisesSqlServer.
2.	Um serviço vinculado do tipo AzureStorage.
3.	Um conjunto de dados de entrada do tipo SqlServerTable. 
4.	Um conjunto de dados de saída do tipo AzureBlob.
4.	O pipeline com Atividade de cópia que usa SqlSource e BlobSink.

O exemplo copia a cada hora dados pertencentes a uma série temporal de uma tabela no banco de dados do SQL Server para um blob. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do SQL Server**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada do SQL Server**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Definir “external”: ”true” e especificar a política externalData informa o serviço Azure Data Factory que essa é uma tabela externa ao data factory e não é produzida por uma atividade desse data factory.

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. No definição JSON do pipeline, o tipo **source** está definido como **SqlSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \'{0:yyyy-MM-dd HH:mm}\' AND timestampcolumn < \'{1:yyyy-MM-dd HH:mm}\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Exemplo: copiar dados do Blob do Azure para o SQL Server

O exemplo a seguir mostra:

1.	O serviço vinculado do tipo OnPremisesSqlServer.
2.	O serviço vinculado do tipo AzureStorage.
3.	Um conjunto de dados de entrada do tipo AzureBlob.
4.	Um conjunto de dados de saída do tipo SqlServerTable.
4.	Um pipeline com Atividade de cópia que usa BlobSource e SqlSink.

O exemplo copia, a cada hora, dados pertencentes a uma série temporal do Blob do Azure para uma tabela no banco de dados do SQL Server. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Server**
	
	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e nome de arquivo usa a parte de hora da hora de início. A configuração “external”: ”true” informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade na data factory.
	
	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}
	
**Conjunto de dados de saída do SQL Server**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL Server. Você deve criar a tabela no SQL Server com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.
	
	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": " SqlServerOutput "
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Propriedades do serviço vinculado do SQL Server

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade de tipo deve ser configurada como **OnPremisesSqlServer**. | Sim |
| connectionString | Especifique as informações de connectionString necessárias para conexão com o banco de dados do SQL Server local usando a autenticação do SQL ou então a autenticação do Windows. | Sim |
| gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados do SQL Server local. | Sim |
| Nome de Usuário | Especifique o nome de usuário se você estiver usando a Autenticação do Windows. | Não |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Não |

### Exemplos

**JSON para usar Autenticação SQL**
	
	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "<gateway name>"
	    }
	}

**JSON para usar Autenticação Windows**

Se o nome de usuário e senha forem especificados, o gateway os usará para representar a conta de usuário especificada para se conectar ao banco de dados do SQL Server local. Caso contrário, o gateway se conectará ao SQL Server diretamente com o contexto de segurança do Gateway (a sua conta de inicialização).

	{ 
	     "Name": " MyOnPremisesSQLDB", 
	     "Properties": 
	     { 
	         "type": "OnPremisesSqlLinkedService", 
	         "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;", 
	         "username": "<username>", 
	         "password": "<password>", 
	         "gatewayName": "<gateway name>" 
	     } 
	}

## Propriedades de tipo de conjunto de dados do SQL Server

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Server, blob do Azure, tabela do Azure, etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **SqlServerTable** tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela na instância do banco de dados do SQL Server à qual o serviço vinculado se refere. | Sim |

## Propriedades de tipo de atividade de cópia do SQL Server

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso de Atividade de cópia, quando a fonte é do tipo **SqlSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. Se não for especificada, a instrução SQL que é executada é: select from MyTable. | Não |

**SqlSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| sqlWriterStoredProcedureName | Nome do procedimento armazenado especificado pelo usuário para dados de inserção (atualização/inserção) na tabela de destino. | Nome do procedimento armazenado. | Não |
| sqlWriterTableType | Nome do tipo de tabela especificado pelo usuário a ser utilizado no procedimento armazenado acima. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. | Um nome de tipo de tabela. | Não |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize | Inteiro. (unidade = Contagem de Linhas) | Não (Padrão = 10.000) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. | (Unidade = timespan) Exemplo: "00:30:00" (30 minutos). | Não | 
| sqlWriterCleanupScript | A consulta especificada pelo usuário para atividade de cópia a ser executada para assegurar que os dados de uma fatia específica serão limpos. | Uma instrução de consulta. | Não | 
| sliceIdentifierColumnName | Nome de coluna especificado pelo usuário para a Atividade de cópia preencher com o identificador de fatia gerado automaticamente, que será usado para limpar os dados de uma fatia específica quando executado novamente. | Nome de uma coluna com tipo de dados de binário (32). | Não |

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### Mapeamento de tipo para SQL Server e SQL Azure

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), a atividade de cópia executa conversões automáticas de tipo de tipos de fonte para tipos de coletor, com a abordagem em duas etapas descrita a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de e para o SQL Azure, SQL Server, Sybase e os seguintes mapeamentos serão usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binário | Byte |
| bit | Booliano |
| char | Cadeia de caracteres, caractere |
| data | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Atributo FILESTREAM (varbinary(max)) | Byte |
| Float | Duplo |
| imagem | Byte | 
| int | Int32 | 
| money | Decimal |
| nchar | Cadeia de caracteres, caractere |
| ntext | Cadeia de caracteres, caractere |
| numérico | Decimal |
| nvarchar | Cadeia de caracteres, caractere |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql\_variant | Objeto * |
| texto | Cadeia de caracteres, caractere |
| tempo real | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | Cadeia de caracteres, caractere |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO6-->