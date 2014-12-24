﻿<properties urlDisplayName="Using Offline Data" pageTitle="Usando dados offline em Serviços Móveis (Xamarin iOS) | Centro de desenvolvimento móvel" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Usando sincronização de dados offline em Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

Este tópico mostra como usar os recursos offline dos Serviços Móveis do Azure. Esses recursos permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Os recursos offline permitem que você sincronize suas alterações locais com o serviço móvel quando estiver online novamente. 

	Neste tutorial, você atualizará o aplicativo do tutorial [Introdução aos Serviços Móveis] ou [Introdução aos Dados] para dar suporte aos recursos offline dos Serviços Móveis do Azure. Você adicionará dados em um cenário offline desconectado, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

>[WACOM.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo da Windows Store. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis].
>
>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>. 

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para dar suporte aos recursos offline]
2. [Testar o aplicativo conectado ao Serviço Móvel]

Este tutorial exige o seguinte:

* XCode 4.5 e iOS 6.0 (ou versões posteriores) 
* Visual Studio com a [extensão Xamarin] **ou** [Xamarin Studio] no SO X
* Conclusão do tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados]
* [SDK dos Serviços Móveis do Azure versão 1.3.0-beta2 (ou posterior)][Mobile Services SDK Nuget]
* [SQLite Store para os Serviços Móveis do Azure versão 1.0.0-beta2 (ou posterior)][SQLite store nuget]

>[WACOM.NOTE] As instruções abaixo presumem que você esteja usando o Visual Studio 2012 ou uma versão posterior com a extensão Xamarin. Se você estiver usando o Xamarin Studio nos SO X, use o suporte interno de Gerenciador de pacote NuGet.

## <a name="enable-offline-app"></a>Atualizar o aplicativo para dar suporte aos recursos offline

A sincronização offline de serviços móveis do Azure permite aos usuários finais interagirem com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, você deve inicializar `MobileServiceClient.SyncContext` para um armazenamento local. Referencie sua tabela pela interface `IMobileServiceSyncTable`.

Um projeto com o estado concluído deste tutorial está disponível [aqui](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).

1. No Visual Studio abra o projeto que você concluiu no tutorial [Introdução aos Serviços Móveis] ou no [Introdução aos Dados]. No Gerenciador de Soluções, remova a referência ao **SDK de Serviços Móveis do Azure** em **Componentes**.

2. Instale o pacote de pré-lançamento do SQLiteStore dos Serviços Móveis usando o seguinte comando no Console do Gerenciador de Pacotes: 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Isso também instalará todas as dependências necessárias.
    
3. No nó de referências, remova as referências para `System.IO`, `System.Runtime` e `System.Threading.Tasks`.

### Editar o arquivo QSTodoService.cs 

Edite a classe `QSTodoService` para habilitar o uso dos recursos offline dos Serviços Móveis com um repositório local do SQLite.

1. Adicione o seguinte usando as instruções na parte superior do arquivo.

		using Microsoft.WindowsAzure.MobileServices; 
		using Microsoft.WindowsAzure.MobileServices.Sync; 
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2. Alterar o tipo de membro `todoTable` de `IMobileServiceTable` para `IMobileServicesSyncTable`

		IMobileServiceSyncTable<ToDoItem> todoTable; 

3. No construtor de `QSTodoService`, altere o inicializador para `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4. No construtor de `QSTodoService`,adicione uma chamada a `SQLitePCL.CurrentPlatform.Init()` como a segunda linha de código:

		QSTodoService ()
		{
			CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

			// Initialize the Mobile Service client with your URL and key
			client = new MobileServiceClient (applicationURL, applicationKey, this);

			// Create an MSTable instance to allow us to work with the TodoItem table
			todoTable = client.GetSyncTable <ToDoItem> ();
		}
 
5. Na classe `QSTodoService`, defina um novo método `InitializeAsync`:
 
		public async Task InitializeStoreAsync()
		{
		    string path = "syncstore.db";
		    var store = new MobileServiceSQLiteStore(path);
		    store.DefineTable<ToDoItem>();
		    await client.SyncContext.InitializeAsync(store);
		}

6. Na classe `QSTodoService`, defina um novo método `SyncAsync`:
 
		public async Task SyncAsync()
		{
		    try
		    {
		        await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    }
		    catch (MobileServiceInvalidOperationException e)
		    {
		        Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
		    }
		}

### Editar ToDoItem.cs 

1. Adicione a instrução de uso: 

        using Microsoft.WindowsAzure.MobileServices; 


2. Adicione os seguintes membros à classe `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

### Editar QSTodoListViewController.cs 

Modifique `QSTodoListViewController` para chamar o novo método `SyncAsync` quando o usuário executa o gesto de atualização.
 
1. Adicione uma chamada a `InitializeStoreAsync` em `ViewDidLoad()`, após a inicialização de `todoService`:

		public override async void ViewDidLoad ()
		{
		    base.ViewDidLoad ();
		
		    todoService = QSTodoService.DefaultService;
			await todoService.InitializeStoreAsync();
			
			...    // the rest of the code in the method is unchanged
		}

2. Modifique o método `AddRefreshControl` para chamar `SyncAsync` antes da chamada para `RefreshAsync`:


		RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
			await RefreshAsync();
		}; 

<!-- 
DM: commenting this out because this tutorial doesn't show OC conflict handling
### Edit ToDoItem.cs 

Modify the strongly-type data class to add a version field

1. In the top of the file, add the using statement: 

        using Microsoft.WindowsAzure.MobileServices; 

2. Add the following members to the class `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

-->

## <a name="test-online-app"></a>Testar o aplicativo 

Nesta seção, você testará o método `SyncAsync` que sincroniza o repositório local com o banco de dados do serviço móvel.

1. No Visual Studio, pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

2. Observe que a lista de itens no aplicativo está vazia. Como resultado das alterações de código na seção anterior, o aplicativo não lê mais itens do serviço móvel, mas sim do repositório local. 

3. Adicione itens à Lista de Tarefas Pendentes.

    ![][1]


4. Faça logon no Portal de Gerenciamento do Azure e procure o banco de dados de seu serviço móvel. Se o serviço usar o back-end do JavaScript para serviços móveis, você poderá procurar os dados na guia **Dados** do serviço móvel. Se estiver usando o back-end do .NET para o serviço móvel, você poderá clicar no botão **Gerenciar** do banco de dados no SQL Azure Extension para executar uma consulta na tabela.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

5. No aplicativo, faça o gesto de atualização puxando a lista de itens para baixo. Isso faz com que o aplicativo chame `MobileServiceClient.SyncContext.PushAsync` e `IMobileServiceSyncTable.PullAsync()`, depois`RefreshTodoItems` para atualizar o aplicativo com os itens do repositório local. 

    A operação de push faz com que o banco de dados do serviço móvel receba os dados do repositório. Ela é executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia por push as alterações em todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre as tabelas.
    
    Em contraste, a operação pull recupera os registros somente da tabela especificada. Se houver operações pendentes para essa tabela no contexto de sincronização, uma operação `PushAsync` será chamada implicitamente pelo SDK de Serviços Móveis.
        
    ![][3] 



    ![][2]


  

##Resumo

Para dar suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando queremos sincronizar o repositório local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*    Para enviar por push as alterações ao servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.
   
* Para efetuar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.

    Um pull sempre envia um push primeiro.  

    Este exemplo usa uma sobrecarga de **PullAsync()** que permite especificar uma chave de consulta e uma consulta. A chave de consulta é usada para sincronização incremental. O SDK dos serviços móveis rastreia o último carimbo de data/hora atualizado após cada operação de recepção bem-sucedida. No próximo recebimento, apenas os registros mais recentes serão recuperados. Se nenhuma chave de consulta for especificada, será executada uma sincronização completa para a tabela de sincronização.

## Próximas etapas

Você pode baixar a versão completa deste tutorial em nosso [repositório de exemplos GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).


<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Como usar a biblioteca de cliente Componente Xamarim para os Serviços Móveis do Azure]

<!-- Anchors. -->
[Atualizar o aplicativo para dar suporte aos recursos offline]: #enable-offline-app
[Testar o aplicativo conectado ao Serviço Móvel]: #test-online-app
[Próximas etapas]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
[2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png



<!-- URLs. -->
[Tratando conflitos com o suporte offline para Serviços Móveis]: /pt-br/documentation/articles/mobile-services-xamarin-ios-handling-conflicts-offline-data/ 
[Introdução aos dados]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
[Como usar a biblioteca de cliente Componente Xamarim para os Serviços Móveis do Azure]: /pt-br/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Nuget de SDK para Serviços Móveis]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[Nuget de armazenamento de SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Extensão Xamarin]: http://xamarin.com/visual-studio
[Suplemento do NuGet para Xamarin]: https://github.com/mrward/monodevelop-nuget-addin