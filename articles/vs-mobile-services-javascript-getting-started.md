﻿<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-mobile-services-javascript-getting-started/)
> - [O que aconteceu](/documentation/articles/vs-mobile-services-javascript-what-happened/)

## Introdução aos Serviços Móveis

A primeira etapa necessária para seguir o código nesses exemplos depende de a qual tipo de serviço móvel você está conectado.

Para obter um serviço móvel de back-end JavaScript, crie uma tabela chamada TodoItem.  Para criar uma tabela, localize o serviço móvel sob o nó Azure no Gerenciador de servidores, clique com o botão direito no nó do serviço móvel para abrir o menu de contexto e escolha **Criar Tabela**. Digite "TodoItem" como o nome da tabela.

Se ao invés de você ter um serviço móvel de back-end .NET, já houver uma tabela TodoItem no modelo do projeto padrão que o Visual Studio criou para você, será necessário publicá-lo no Azure. Para publicá-lo, abra o menu de contexto do projeto de serviço móvel no Gerenciador de servidores e escolha **Publicar na Web**. Aceite os padrões e escolha o botão **Publicar**.

#####Obtenha referência para uma tabela

O objeto cliente já foi adicionado ao seu projeto.  Seu nome é o nome de seu serviço móvel com "Cliente" anexado a ele. O código a seguir obtém uma referência de uma tabela que contém dados para um TodoItem, que pode ser usada em operações posteriores para ler e atualizar a tabela de dados.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

#####Adicionar entrada 

Inserir um novo item em uma tabela de dados. Uma id (um GUID da cadeia de caracteres do tipo) é criada automaticamente como a chave primária para a nova linha. Não altere o tipo de coluna de ID, já que a infraestrutura de serviços móveis o utiliza.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

#####Ler/consultar tabela

O código a seguir consulta uma tabela para todos os itens, atualiza uma coleção local e vincula o resultado aos listItems do elemento da interface do usuário.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Você pode usar o onde o método deverá modificar a consulta. Veja um exemplo que filtra itens concluídos.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Para ver mais exemplos de consultas que podem ser usadas, consulte o [objeto de consulta](http://msdn.microsoft.com/library/azure/jj613353.aspx).

#####Atualizar entrada

Atualizar uma linha em uma tabela de dados. Nesse exemplo, todoItem é o item atualizado e item é o mesmo item retornado do serviço móvel. Quando o serviço móvel responde, o item é atualizado na lista de todoItems locais usando o método [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Chame o método [done]() no objeto [Promise]() retornado para obter uma cópia do objeto inserido e manipular os erros.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####Excluir entrada

Exclui uma linha em uma tabela de dados. Chame o método [done]() no objeto [Promise]() retornado para obter uma cópia do objeto inserido e manipular os erros.

	todoTable.delete(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)