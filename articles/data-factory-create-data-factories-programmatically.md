﻿<properties title="Create, monitor, and manage Azure data factories using Data Factory SDK" pageTitle="Criar, monitorar e gerenciar data factories do Azure usando o SDK da Data Factory" description="Saiba como criar, monitorar e gerenciar de forma programática os dados de fábrica do Azure, usando o Data Factory SDK." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="spelluru" />

# Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET da Data Factory
Você pode criar, monitorar e gerenciar as Data Factory do Azure programaticamente usando o SDK do .NET da Data Factory. Este artigo contém uma explicação passo a passo que você pode seguir para criar um aplicativo de console .NET de exemplo que cria e monitora uma Data Factory. Consulte [Referência de biblioteca de classe de Data Factory][adf-class-library-reference] para obter detalhes sobre o SDK do .NET da Data Factory. 

## Passo a passo: Criar data factory do Azure usando o SDK de .NET da Data Factory.

**Pré-requisitos:**

- Visual Studio 2012 ou 2013
- Baixe e instale o [SDK do .NET do Microsoft Azure][azure-developer-center]
- Baixe e instale os pacotes NuGet para Data Factory do Azure. As instruções estão no passo a passo.

### Etapa 1: Criar data factory do Azure usando o SDK de .NET da Data Factory
1. Usando o Visual Studio 2012 ou 2013, crie um aplicativo de console C# .NET.
	<ol type="a">
		<li>Iniciar <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
		<li>Clique em <b>Arquivo</b>, aponte para <b>Novo</b>e clique em <b>Projeto</b>.</li> 
		<li>Expanda <b>Modelos</b>e selecione <b>Visual C#</b>. Neste passo a passo, você usa C#, mas você pode usar qualquer linguagem .NET.</li> 
		<li>Selecionar <b>Aplicativo de console</b> a partir da lista de tipos de projetos à direita.</li>
		<li>Digite <b>DataFactoryAPITestApp</b> para <b>Name</b>.</li> 
		<li>Selecionar <b>C:\ADFGetStarted</b> para <b>Local</b>.</li>
		<li>Clique em <b>OK</b> para criar o projeto.</li>
	</ol>
2. Clique em <b>Ferramentas</b>, aponte para <b>Gerenciador de Pacotes NuGet</b>e clique em <b>Console do Gerenciador de Pacotes</b>.
3.	No <b>Console do Gerenciador de Pacotes</b>, execute os seguintes comandos, um por um.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories -Pre
		Install-Package Microsoft.DataFactories.Runtime -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Adicione a seguinte seção **appSetttings** no arquivo **App.config**. Eles são usados pelo método auxiliar: **GetAuthorizationHeader**. 

	Substitua os valores por **SubscriptionId** e **ActiveDirectoryTenantId** com suas IDs de assinatura e locatário do Azure. Você pode obter esses valores, executando **Get-AzureAccount** a partir do PowerShell do Azure (talvez seja necessário fazer logon pela primeira vez usando Add-AzureAccount).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. Adicione as seguintes declarações **usando** o arquivo de origem (Program.cs) no projeto.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.WindowsAzure; 
6. Adicione o seguinte código que cria uma instância de classe **DataPipelineManagementClient** no método **Principal**. Você usará esse objeto para criar um data factory, um serviço vinculado, tabelas de entrada e saídas e uma pipeline. Você também irá usar esse objeto para monitorar os subconjuntos de uma tabela em tempo de execução.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Add the following code that creates a **data factory** to the **Main** method.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. Adicione o seguinte código que cria um **vinculado serviço** no método **Principal**. 
	> [WACOM.NOTE] Use o**nome da conta** e a **chave de conta** da sua conta de armazenamento do Azure para o **ConnectionString**. 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. Adicione o seguinte código que cria **tabelas de entrada e saídas** no métdo **Principal**. 

	Observe que o **FolderPath** para o blob de entrada é definido como **adftutorial/** em que **adftutorial** é o nome do contêiner em seu armazenamento de blob. Se esse contêiner não existir em seu armazenamento de blob do Azure, crie um contêiner com este nome: **adftutorial** e carregue um arquivo de texto no contêiner.
	
	Observe que o FolderPath para o blob de saída é definido como: **adftutorial/apifactoryoutput / {fatiar}** onde a **Fatia** é dinamicamente calculada com base no valor de **SliceStart** (iniciar data e hora de cada fatia).  

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Adicione o seguinte código que **cria e ativa uma pipeline** no método **Principal**. Essa pipeline tem uma **CopyActivity** que usa **BlobSource** como fonte e **BlobSink** como coletor. 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. Adicione o seguinte método auxiliar usado pelo método **Principal** na classe **Programa**. Esse método exibe uma caixa de diálogo que permite que você forneça o**nome de usuário** e a **senha** que você usa para fazer logon no portal do Azure. 
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. Adicione o seguinte código no método **Principal** para obter o status de uma fatia de dados da tabela de saída. Há apenas uma fatia esperada neste exemplo.   
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. Adicione o seguinte código para obter detalhes de uma fatia da fatia de dados para executar o método **Principal**.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    }

15. Compile o aplicativo de console. Clique eno menu **Compilar** e clique em **Solução de Compilação**.
16. Confirme se há pelo menos um arquivo no contêiner adftutorial no seu armazenamento de BLOBs do Azure. Caso contrário, crie o arquivo de Emp.txt no bloco de notas com o seguinte conteúdo e carregue-o no contêiner adftutorial.

        John, Doe
		Jane, Doe
	 
17. Execute o exemplo, clicando no menu **Depurar** -> **Iniciar Depuração**.
18. Use o Portal de Visualização para verificar se a data factory: **APITutorialFactory** foi criado com os seguintes artefatos: 
	- Serviço vinculado: **LinkedService_AzureStorage** 
	- Tabelas: **TableBlobSource** e **TableBlobDestination**.
	- Pipeline: **PipelineBlobSample** 
18. Verifique se um arquivo de saída foi criado na pasta **apifactoryoutput** no contêiner **adftutorial**.


## Consulte também

Artigo | Descrição
------ | ---------------
[Introdução à Data Factory do Azure][data-factory-introduction] | Este artigo apresenta os conceitos, o serviço da Data Factory do Azure, o valor que ele oferece e os cenários que ele dá suporte.
[Introdução à Data Factory do Azure][adf-getstarted]Este artigo fornece um tutorial de ponta a ponta que mostra como criar um exemplo de data factory do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local em um blob do Azure.
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando a Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em uma Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em uma pipeline.
[Referência do Desenvolvedor da Data Factory do Azure][developer-reference]A Referência do Desenvolvedor tem um conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 


[data-factory-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/pt-br/downloads/

<!--HONumber=35.2-->