<properties 
	pageTitle="Data Factory - Log de alterações de API do .NET | Microsoft Azure" 
	description="Descreve alterações significativas, novos recursos e correções de bugs etc... em uma versão específica de API do .NET para o Azure Data Factory." 
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
	ms.date="10/28/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Log de alterações SDK do .NET 
Este artigo fornece informações sobre alterações no SDK do Azure Data Factory em uma versão específica. Você pode baixar o mais recente pacote Nuget para o Azure Data Factory [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

## Versão 4.1.0
Data de lançamento: 28/10/2015

### Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Os seguintes tipos de atividades foram adicionados: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Os seguintes tipos de fonte e coletor de atividade para Atividade de Cópia foram adicionados:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## Versão 4.0.1
Data do lançamento: 13-10-2015

### Alterações de última hora
As classes a seguir foram renomeadas. Os novos nomes foram os nomes originais de classes antes da liberação 4.0.0.
 
Nome no 4.0.0 | Nome no 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Versão 4.0.0
Data do lançamento: 02-10-2015

### Alterações de última hora



- As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Novo nome |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabela | [Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- A **versão de API** para esta versão é: **01/10/2015**.

- Os métodos **List** agora retornam resultados paginados. Se a resposta contiver uma propriedade não vazia **NextLink**, o aplicativo cliente precisa continuar buscando a próxima página até que todas as páginas sejam retornadas. Aqui está um exemplo:

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- O API da **Lista** retorna apenas o resumo de um pipeline, em vez de todos os detalhes. Por exemplo, as atividades em um resumo de pipeline contém apenas nome e tipo.

### Adições de recursos
- A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) dá suporte a duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para dar suporte à cópia idempotente no SQL Data Warehouse do Azure. Confira o artigo [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md), especificamente, as seções [Mecanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) e [Mecanismo 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) para saber mais sobre essas propriedades.

- Agora há suporte para executar o procedimento armazenado nas fontes do Banco de Dados SQL do Azure e Azure SQL Data Warehouse como parte da Atividade de Cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades para dar suporte a **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Confira os artigos [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#sqlsource) e [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) em Azure.com para saber mais sobre essas propriedades.

<!---HONumber=Nov15_HO1-->