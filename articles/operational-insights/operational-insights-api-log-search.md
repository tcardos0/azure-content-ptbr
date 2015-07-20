<properties
   pageTitle="API de Pesquisa de Log de Insights Operacionais"
   description="Fornece uma visão geral da API de pesquisa de Insights Operacionais e inclui exemplos que mostram como usá-la."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/02/2015"
   ms.author="banders" />



# Referência e visão geral da API de pesquisa de Insights Operacionais

Este guia fornece um tutorial básico que descreve como você pode usar a API de pesquisa de Log de Insights Operacionais e fornece exemplos que mostram como usar os comandos.

## Visão geral da API de pesquisa de log

A API de pesquisa de log de Insights Operacionais é RESTful e pode ser acessada por meio da API do Gerenciador de Recursos do Azure. Neste documento, você encontrará exemplos em que a API é acessada por meio de [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Gerenciador de Recursos do Azure. O uso do ARMClient e do PowerShell é uma das muitas opções para acessar a API de pesquisa de log de Insights Operacionais. Com essas ferramentas, você pode utilizar a API RESTful do Gerenciador de Recursos do Azure para fazer chamadas aos espaços de trabalho do Insights Operacionais e executar comandos de pesquisa dentro deles. A API produzirá resultados da pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de diferentes maneiras por meio de programação.

O Gerenciador de Recursos do Azure pode ser usado por meio de uma [Biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) bem como por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Examine as páginas da Web vinculadas para obter mais informações.

## Tutorial de API de pesquisa básica

### Para usar o cliente ARM

1. Instale [Chocolatey](https://chocolatey.org/), que é um Gerenciador de Pacotes de Máquina de software livre para Windows.
2. Abra uma janela do PowerShell como administrador e execute o seguinte comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

3. Instale o ARMClient abrindo um novo prompt de comando e execute o seguinte comando:

    ```
    choco install armclient
    ```

### Para realizar uma pesquisa simples usando o ARMClient

1. Faça logon em sua conta da Microsoft ou OrgID:

    ```
    armclient login
```
  Um logon bem-sucedido lista todas as assinaturas vinculadas à conta especificada. Por exemplo:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenha os espaços de trabalho do Operations Management Suite. Por exemplo:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2014-10-10
    ```

    Uma chamada Get bem-sucedida geraria todos os espaços de trabalho associados à assinatura. Por exemplo:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crie sua variável de pesquisa. Por exemplo:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}”;
    ```
4. Pesquise usando sua nova variável de pesquisa. Por exemplo:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2014-10-10 $mySearch
    ```

## Exemplos de referência da API de pesquisa
Os exemplos a seguir mostram como você pode usar a API de pesquisa.

### Pesquisa - ação/leitura

**Url de exemplo:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2014-10-10
```

**Solicitação:**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
```
A tabela a seguir descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|top|O número máximo de resultados a serem retornados.|
|highlight|Contém parâmetros anteriores e subsequentes, geralmente usados para realçar campos correspondentes|
|pre|Usado como prefixo da cadeia de caracteres específica para os campos correspondentes.|
|post|Acrescenta a cadeia de caracteres específica para os campos correspondentes.|
|query|A consulta de pesquisa usada para coletar e retornar os resultados.|
|iniciar|O início da janela de tempo em que você deseja que os resultados sejam encontrados.|
|end|O fim da janela de tempo em que você deseja que os resultados sejam encontrados.|


**Resposta:**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### Pesquisa/{ID} - ação/leitura

**Solicite o conteúdo de uma pesquisa salva:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2014-10-10
```

>[AZURE.NOTE]Se a pesquisa retornar com um status 'Pendente', a sondagem dos resultados atualizados poderá ser feita por essa API. Após seis minutos, o resultado da pesquisa será eliminado do cache e Http Gone será retornado. Se a solicitação inicial de pesquisa retornar um status 'Bem-sucedido' imediatamente, ela não será adicionada ao cache, fazendo essa API retornar Http Gone se consultada. O conteúdo de um resultado de Http 200 estará no mesmo formato que a solicitação inicial de pesquisa, apenas com valores atualizados.

### Pesquisas salvas - somente REST

**Solicite a lista de pesquisas salvas:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2014-10-10
  ```

Supported methods: GET PUT DELETE

Supported collection methods: GET

The following table describes the properties that are available.

|Property|Description|
|---|---|
|Id|The unique identifier.|
|Etag|**Required for Patch**. Updated by server on each write. Value must be equal to the current stored value or ‘*’ to update. 409 returned for old/invalid values.|
|properties.query|**Required**. The search query.|
|properties.displayName|**Required**. The user defined display name of the query. If modeled as an Azure resource, this would be a Tag.|
|properties.category|**Required**. The user defined category of the query. If modeled as an Azure resource this would be a Tag.|

>[AZURE.NOTE]The Operational Insights Search API currently returns user-created saved searches when polled for saved searches in a workspace. The API will not return saved searches provided by solutions at this time. This functionality will be added at a later date.

### Delete saved searches

**Request:**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10
```

### Update saved searches

 **Request:**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime'2015-04-16T23%3A35%3A35.3182423Z'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10 $savedSearchParametersJson
```

### Metadata - JSON only

Here’s a way to see the fields for all log types for the data collected in your workspace. For example, if you want you know if the Event type has a field named Computer, then this is one way to look up and confirm.

**Request for Fields:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2014-10-10
```

**Response:**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

The following table describes the properties that are available.

|**Property**|**Description**|
|---|---|
|name|Field name.|
|displayName|The display name of the field.|
|type|The Type of the field value.|
|facetable|Combination of current ‘indexed’, ‘stored ‘and ‘facet’ properties.|
|display|Current ‘display’ property. True if field is visible in search.|
|ownerType|Reduced to only Types belonging to onboarded IP’s.|


## Optional parameters
The following information describes optional parameters available.

### Highlighting

The “Highlight” parameter is an optional parameter you may use to request the search subsystem include a set of markers in its response.

These markers indicate the start and end highlighted text that matches the terms provided in your search query.
You may specify the start and end markers that will be used by search to wrap the highlighted term.

**Example search query**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
```

**Sample result:**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

Observe que o resultado acima contém uma mensagem de erro que foi acrescida de um prefixo e anexada.

<!---HONumber=July15_HO2-->