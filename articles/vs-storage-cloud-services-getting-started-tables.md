﻿<properties title="Getting Started with Azure Storage" pageTitle="Introdução ao Armazenamento do Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)
> - [O que aconteceu](/documentation/articles/vs-storage-cloud-services-what-happened/)

##Introdução ao armazenamento do Azure (Projetos de serviço de nuvem)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Filas](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Tabelas](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

O serviço de armazenamento de Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.  Consulte [Como usar o Armazenamento de Blob do .NET](http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET") para obter mais informações.

Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####Obter a cadeia de conexão do armazenamento
Antes de poder realizar qualquer coisa com uma tabela, será necessário obter a cadeia de conexão para a conta de armazenamento onde as tabelas residirão. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Para projetos de serviço de nuvem, você pode usar o tipo **CloudConfigurationManager** para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]