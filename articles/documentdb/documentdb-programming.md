<properties 
	pageTitle="Programando o Banco de Dados de Documentos: Procedimentos armazenados, gatilhos e UDFs | Azure" 
	description="Saiba como usar o Banco de Dados de Documentos do Microsoft Azure para escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário (UDF) nativamente em JavaScript." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="mimig"/>

# Programando o Banco de Dados de Documentos: procedimentos armazenados, gatilhos e UDFs

Saiba como a execução transacional e integrada do JavaScript pelo Banco de Dados de Documentos permite que desenvolvedores escrevam **procedimentos armazenados**, **gatilhos** e **UDFs (funções definidas pelo usuário)** nativamente no JavaScript. Isso permite que você escreva uma lógica de aplicativo que pode ser enviada e executada diretamente nas partições de armazenamento do banco de dados. 

É recomendável começar assistindo ao vídeo a seguir, em que Andrew Liu fornece uma breve introdução ao modelo de programação do lado do servidor do Banco de Dados de Documentos. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Em seguida, volte a este artigo, onde você aprenderá as respostas para as seguintes perguntas:  

- Como eu escrevo um procedimento armazenado, gatilho ou UDF usando JavaScript?
- Como o Banco de Dados de Documentos garante o ACID?
- Como funcionam as transações no Banco de Dados de Documentos?
- O que são pré-gatilhos e pós-gatilhos e como eu escrevo um?
- Como eu registro e executo um procedimento armazenado, gatilho ou UDF de modo RESTful usando HTTP?
- Que SDKs do Banco de Dados de Documentos estão disponíveis para criar e executar procedimentos armazenados, gatilhos e UDFs?

##Introdução

Essa abordagem de *"JavaScript como um T-SQL moderno"* libera os desenvolvedores de aplicativos das complexidades das incompatibilidades do sistema de tipos e tecnologias de mapeamento relacionais do objeto. Também possui uma série de vantagens intrínsecas que podem ser utilizadas para criar aplicativos ricos:  

-	**Lógica de procedimento:** JavaScript como uma linguagem de programação de alto nível, fornece uma interface familiar e avançada para expressar lógicas de negócios. Você pode executar sequências complexas de operações de forma mais próxima aos dados.

-	**Transações atômicas:** o Banco de Dados de Documentos garante que operações de banco de dados executadas em um único procedimento armazenado ou gatilho sejam atômicas. Isso permite que um aplicativo combine operações relacionadas em um único lote, de modo que todas ou nenhuma delas sejam bem-sucedidas. 

-	**Desempenho:** o fato de o JSON ser mapeado intrinsecamente para o sistema com linguagem JavaScript, e de ser a unidade básica de armazenamento do Banco de Dados de Documentos, permite uma série de otimizações, como a materialização lenta de documentos JSON no pool de buffers e disponibilizá-los sob demanda ao código em execução. Há mais benefícios de desempenho associados ao envio de lógica de negócios para o banco de dados:
	-	Envio em lote - Os desenvolvedores podem agrupar operações como inserções e enviá-las em massa. O custo de latência de tráfego de rede e a sobrecarga de armazenamento para criar transações separadas são reduzidos significativamente. 
	-	Pré-compilação - O Banco de Dados de Documentos pré-compila os procedimentos armazenados, gatilhos e funções definidas pelo usuário (UDFs) a fim de evitar custos de compilação de JavaScript para cada invocação. A sobrecarga de construir o código de bytes para a lógica de procedimento é amortizada a um valor mínimo.
	-	Sequenciamento - Várias operações precisam de um efeito colateral ("gatilho") que possivelmente envolve realizar uma ou mais operações de armazenamento secundárias. Além da atomicidade, o desempenho é melhor quando movido ao servidor. 
-	**Encapsulamento:** procedimentos armazenados podem ser usados para agrupar a lógica de negócios em um lugar. Isso tem duas vantagens:
	-	Adiciona uma camada de abstração sobre os dados brutos, o que permite que os arquitetos de dados desenvolvam seus aplicativos de maneira independente dos dados. Isso é ainda mais vantajoso quando os dados não possuem esquema, devido às suposições que precisam ser integradas ao aplicativo se precisarem lidar diretamente com os dados.  
	-	Essa abstração permite que as empresas protejam seus dados simplificando o acesso pelos scripts.  

A criação e execução de gatilhos, procedimentos armazenados e operadores de consulta personalizados têm suporte por meio da [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e dos [SDKs clientes](https://msdn.microsoft.com/library/azure/dn781482.aspx) em diversas plataformas, incluindo .NET, Node.js e JavaScript. **Esse tutorial utiliza o [SDK Node.js](http://dl.windowsazure.com/documentDB/nodedocs/)** para ilustrar a sintaxe e o uso de procedimentos armazenados, gatilhos e UDFs.   

##Exemplo: Escrever um procedimento armazenado simples 
Vamos começar com um procedimento armazenado simples que retorna uma resposta "Hello World".

	var helloWorldStoredProc = {
	    id: "helloWorld",
	    body: function () {
	        var context = getContext();
	        var response = context.getResponse();
	
	        response.setBody("Hello, World");
	    }
	}


Os procedimentos armazenados são registrados por coleção e podem operar em qualquer documento e anexo presente na coleção. O trecho a seguir mostra como registrar o procedimento armazenado helloWorld com uma coleção. 

	// register the stored procedure
	var createdStoredProcedure;
	client.createStoredProcedureAsync(collection._self, helloWorldStoredProc)
		.then(function (response) {
		    createdStoredProcedure = response.resource;
		    console.log("Successfully created stored procedure");
		}, function (error) {
		    console.log("Error", error);
		});


Uma vez que o procedimento armazenado é registrado, podemos executá-lo em relação à coleção e ler os resultados no cliente. 

	// execute the stored procedure
	client.executeStoredProcedureAsync(createdStoredProcedure._self)
		.then(function (response) {
		    console.log(response.result); // "Hello, World"
		}, function (err) {
		    console.log("Error", error);
		});


O objeto de contexto oferece acesso a todas as operações que podem ser realizadas no armazenamento do Banco de Dados de Documentos, bem como acesso aos objetos de solicitação e resposta. Nesse caso, usamos o objeto de resposta para definir o corpo da resposta que foi enviada ao cliente. Para obter mais detalhes, consulte a [documentação do SDK do servidor de JavaScript do Banco de Dados de Documentos](http://dl.windowsazure.com/documentDB/jsserverdocs/).  

Vamos ampliar esse exemplo e adicionar mais funcionalidades relativas ao banco de dados ao procedimento armazenado. Procedimentos armazenados podem criar, atualizar, ler, consultar e excluir documentos e anexos dentro da coleção.    

##Exemplo: Escrever um procedimento armazenado para criar um documento 
O próximo trecho mostra como usar o objeto de contexto para interagir com recursos do Banco de Dados de Documentos.

	var createDocumentStoredProc = {
	    id: "createMyDocument",
	    body: function createMyDocument(documentToCreate) {
	        var context = getContext();
	        var collection = context.getCollection();
	
	        var accepted = collection.createDocument(collection.getSelfLink(),
	              documentToCreate,
				function (err, documentCreated) {
				    if (err) throw new Error('Error' + err.message);
				    context.getResponse().setBody(documentCreated.id)
				});
	        if (!accepted) return;
	    }
	}


Esse procedimento armazenado assume como entrada documentToCreate, o corpo de um documento a ser criado na coleção atual. Todas essas operações são assíncronas e dependem de retornos de chamada de função do JavaScript. A função de retorno de chamada possui dois parâmetros, um para o objeto de erro no caso de falhas na operação e um para o objeto criado. Dentro da chamada de retorno, os usuários podem lidar com a exceção ou lançar um erro. Caso uma chamada de retorno não seja fornecida e haja um erro, o tempo de execução do Banco de Dados de Documentos lança um erro.   

No exemplo acima, a chamada de torno lançará um erro se a operação falhar. Caso contrário, ela definirá a ID do documento criada como o corpo da resposta ao cliente. A seguir, mostramos como esse procedimento armazenado é executado com parâmetros de entrada.

	// register the stored procedure
	client.createStoredProcedureAsync(collection._self, createDocumentStoredProc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
	
		    // run stored procedure to create a document
		    var docToCreate = {
		        id: "DocFromSproc",
		        book: "The Hitchhiker's Guide to the Galaxy",
		        author: "Douglas Adams"
		    };
	
		    return client.executeStoredProcedureAsync(createdStoredProcedure._self,
	              docToCreate);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response); // "DocFromSproc"
	}, function (error) {
	    console.log("Error", error);
	});

	
Observe que esse procedimento armazenado pode ser modificado para assumir uma matriz de corpos de documentos como entrada e criá-los todos na mesma execução do procedimento armazenado ao invés de em várias solicitações de rede para criar cada um deles individualmente. Isso pode ser usado para implementar um importador em massa eficiente para o Banco de Dados de Documentos (discutido posteriormente neste tutorial).   

O exemplo descrito demonstra como usar procedimentos armazenados. Iremos discutir os gatilhos e funções definidas pelo usuário (UDFs) posteriormente no tutorial. Primeiro, vamos observar as características gerais do suporte a script do Banco de Dados de Documentos.  

##Suporte de tempo de execução
[O SDK do lado do servidor de JavaScript do Banco de Dados de Documentos](http://dl.windowsazure.com/documentDB/jsserverdocs/) dá suporte para a maioria dos principais recursos de linguagem JavaScript conforme o padrão [ECMA-262](documentdb-interactions-with-resources.md).
 
##Transações
A transação em um banco de dados típico pode ser definida como uma sequência de operações realizadas como uma única unidade lógica de trabalho. Cada transação oferece **garantias ACID**. ACID é um acrônimo bem conhecido que representa quatro propriedades: atomicidade, consistência, isolamento e durabilidade.  

Em resumo, a atomicidade garante que todo o trabalho realizado dentro de uma transação seja tratado como uma única unidade em que tudo é confirmado ou não. A consistência garante que os dados estejam sempre em uma boa condição interna entre as transações. O isolamento garante que duas transações não interfiram uma com a outra; geralmente, a maioria dos sistemas comerciais oferece vários níveis de isolamento que podem ser usados com base nas necessidades do aplicativo. A durabilidade garante que qualquer alteração confirmada no banco de dados esteja sempre presente.   

No Banco de Dados de Documentos, o JavaScript é hospedado no mesmo espaço de memória que o banco de dados. Portanto, as solicitações realizadas dentro de procedimentos armazenados e gatilhos são executadas no mesmo escopo de uma sessão do banco de dados. Isso permite que o Banco de Dados de Documentos garanta ACID para todas as operações que fazem parte de um único procedimento/gatilho armazenado. Considere a seguinte definição de um procedimento armazenado:

	// JavaScript source code
	var exchangeItemsSproc = {
	    name: "exchangeItems",
	    body: function (playerId1, playerId2) {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        var player1Document, player2Document;
	
	        // query for players
	        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
	            function (err, documents, responseOptions) {
	                if (err) throw new Error("Error" + err.message);
	
	                if (documents.length != 1) throw "Unable to find both names";
	                player1Document = documents[0];
	
	                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
	                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
	                    function (err2, documents2, responseOptions2) {
	                        if (err2) throw new Error("Error" + err2.message);
	                        if (documents2.length != 1) throw "Unable to find both names";
	                        player2Document = documents2[0];
	                        swapItems(player1Document, player2Document);
	                        return;
	                    });
	                if (!accept2) throw "Unable to read player details, abort ";
	            });
	
	        if (!accept) throw "Unable to read player details, abort ";
	
	        // swap the two players' items
	        function swapItems(player1, player2) {
	            var player1ItemSave = player1.item;
	            player1.item = player2.item;
	            player2.item = player1ItemSave;
	
	            var accept = collection.replaceDocument(player1._self, player1,
	                function (err, docReplaced) {
					    if (err) throw "Unable to update player 1, abort ";
	
					    var accept2 = collection.replaceDocument(player2._self, player2,
	                        function (err2, docReplaced2) {
							    if (err) throw "Unable to update player 2, abort"
							});
	
					    if (!accept2) throw "Unable to update player 2, abort";
					});
	
	            if (!accept) throw "Unable to update player 1, abort";
	        }
	    }
	}
	
	// register the stored procedure in Node.js client
	client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
		}
	);

Esse procedimento armazenado utiliza transações dentro de um aplicativo de jogos para negociar itens entre dois jogadores em uma única operação. O procedimento armazenado tenta ler dois ou mais documentos, cada um correspondente às IDs dos jogadores transmitidos como um argumento. Se os documentos de ambos os jogadores forem encontrados, então, o procedimento armazenado atualizará os documentos trocando seus itens. Se forem encontrados erros pelo caminho, uma exceção JavaScript será lançada, o que aborta implicitamente a transação.
	

##Confirmação e reversão
As transações são profunda e nativamente integradas ao modelo de programação de JavaScript do Banco de Dados de Documentos. Dentro de uma função de JavaScript, todas as operações são automaticamente encapsuladas em uma única transação. Se o JavaScript for concluído sem nenhuma exceção, as operações de banco de dados serão confirmadas. Com isso, as declarações "BEGIN TRANSACTION" e "COMMIT TRANSACTION" em bancos de dados relacionais são implícitas no Banco de Dados de Documentos.  
 
Se houver qualquer exceção propagada do script, o tempo de execução de JavaScript do Banco de Dados de Documentos reverterá toda a transação. Como mostrado no exemplo anterior, lançar uma exceção é efetivamente equivalente a uma declaração "ROLLBACK TRANSACTION" no Banco de Dados de Documentos. 

###Consistência de dados
Procedimentos armazenados e gatilhos são sempre executados na réplica primária da coleção do Banco de Dados de Documentos. Isso assegura que as leituras de dentro de procedimentos armazenados ofereçam uma forte consistência. As consultas que utilizam funções definidas pelo usuário podem ser executadas na réplica primária ou em qualquer réplica secundária, porém, garantimos que o nível de consistência solicitado seja atendido ao escolher a réplica adequada.

##Segurança
Procedimentos armazenados e gatilhos de JavaScript são colocados em uma área restrita para que os efeitos de um script não vazem para o outro sem passar pelo isolamento da transação de captura instantânea no nível do banco de dados. Os ambientes de tempo de execução são colocados em pools, porém, seu contexto é limpo após cada execução. Portanto, sua segurança é garantida e cada um deles está livre de qualquer efeito colateral inesperado advindo do outro.

##Pré-compilação
Os procedimentos armazenados, gatilhos e UDFs são pré-compilados implicitamente para o formato de código de bytes a fim de evitar o custo de compilação no momento da invocação de cada script. Isso assegura que as invocações dos procedimentos armazenados sejam rápidas e possuam baixa pegada.

##<a id="trigger"></a> Gatilhos
###Pré-gatilhos
O Banco de Dados de Documentos oferece gatilhos que são executados ou desencadeados por uma operação em um documento. Por exemplo, você pode especificar um pré-gatilho ao criar um documento; esse pré-gatilho será executado antes que o documento seja criado. A seguir está um exemplo de como os pré-gatilhos podem ser usados para validar as propriedades de um documento que está sendo criado:

	var validateDocumentContentsTrigger = {
	    name: "validateDocumentContents",
	    body: function validate() {
	        var context = getContext();
	        var request = context.getRequest();
	
	        // document to be created in the current operation
	        var documentToCreate = request.getBody();
	
	        // validate properties
	        if (!("timestamp" in documentToCreate)) {
	            var ts = new Date();
	            documentToCreate["my timestamp"] = ts.getTime();
	        }
	
	        // update the document that will be created
	        request.setBody(documentToCreate);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.Create
	}


E o código de registro do lado do cliente do Node.js para o gatilho:

	// register pre-trigger
	client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
		.then(function (response) {
		    console.log("Created", response.resource);
		    var docToCreate = {
		        id: "DocWithTrigger",
		        event: "Error",
		        source: "Network outage"
		    };
	
		    // run trigger while creating above document 
		    var options = { preTriggerInclude: "validateDocumentContents" };
	
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response.resource); // document with timestamp property added
	}, function (error) {
	    console.log("Error", error);
	});


Pré-gatilhos não podem ter parâmetros de entrada. O objeto de solicitação pode ser usado para manipular a mensagem de solicitação associada à operação. Aqui, o pré-gatilho está sendo executado com a criação de um documento, e o corpo da mensagem de solicitação contém o documento a ser criado no formato JSON.   

Quando os gatilhos são registrados, os usuários podem especificar as operações com as quais eles podem ser executados. Este gatilho foi criado com TriggerOperation.Create, o que significa que a situação a seguir não é permitida.

	var options = { preTriggerInclude: "validateDocumentContents" };
	
	client.replaceDocumentAsync(docToReplace.self,
	              newDocBody, options)
	.then(function (response) {
	    console.log(response.resource);
	}, function (error) {
	    console.log("Error", error);
	});
	
	// Fails, can't use a create trigger in a replace operation

###Pós-gatilhos
Pós-gatilhos, assim como pré-gatilhos, são associados a uma operação em um documento e não assumem parâmetros de entrada. Eles são executados **após** a operação ter sido concluída, e têm acesso à mensagem de resposta que é enviada ao cliente.   

O exemplo a seguir mostra pós-gatilhos em ação:

	var updateMetadataTrigger = {
	    name: "updateMetadata",
	    body: function updateMetadata() {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        // document that was created
	        var createdDocument = response.getBody();
	
	        // query for metadata document
	        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
	            updateMetadataCallback);
	        if(!accept) throw "Unable to update metadata, abort";
	 
	        function updateMetadataCallback(err, documents, responseOptions) {
	            if(err) throw new Error("Error" + err.message);
	                     if(documents.length != 1) throw 'Unable to find metadata document';
	                     
	                     var metadataDocument = documents[0];
	                     
	                     // update metadata
	                     metadataDocument.createdDocuments += 1;
	                     metadataDocument.createdNames += " " + createdDocument.id;
	                     var accept = collection.replaceDocument(metadataDocument._self,
	                           metadataDocument, function(err, docReplaced) {
	                                  if(err) throw "Unable to update metadata, abort";
	                           });
	                     if(!accept) throw "Unable to update metadata, abort";
	                     return;                    
	        }																							
	    },
	    triggerType: TriggerType.Post,
	    triggerOperation: TriggerOperation.All
	}


O gatilho pode ser registrado como mostrado na amostra a seguir.

	// register post-trigger
	client.createTriggerAsync(collection.self, updateMetadataTrigger)
		.then(function(createdTrigger) { 
		    var docToCreate = { 
		        name: "artist_profile_1023",
		        artist: "The Band",
		        albums: ["Hellujah", "Rotators", "Spinning Top"]
		    };
	
		    // run trigger while creating above document 
		    var options = { postTriggerInclude: "updateMetadata" };
		
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});


Esse gatilho consulta o documento de metadados e o atualiza com detalhes sobre o documento recém-criado.  

É importante observar a execução **transacional** de gatilhos no Banco de Dados de Documentos. Esse pós-gatilho é executado como parte da mesma transação como a criação do documento original. Portanto, se lançarmos uma exceção do pós-gatilho (digamos, se não for possível atualizar o documento de metadados), toda a transação falhará e será revertida. Nenhum documento será criado e uma exceção será retornada.  

##<a id="udf"></a>Funções definidas pelo usuário
UDFs (funções definidas pelo usuário) são usadas para estender a gramática da linguagem de consulta SQL do Banco de Dados de Documentos e para implementar uma lógica de negócios personalizada. Elas podem ser invocadas somente de dentro das consultas. Elas não possuem acesso ao objeto de contexto e devem ser usadas como JavaScript somente para cálculo. Portanto, UDFs podem ser executadas em réplicas secundárias do serviço do Banco de Dados de Documentos.  
 
A amostra a seguir cria uma UDF para calcular o imposto de renda com base nas taxas para diversos intervalos de renda, e depois a utiliza dentro de uma consulta para descobrir todas as pessoas que pagaram mais de $20.000 em impostos.

	var taxUdf = {
	    name: "tax",
	    body: function tax(income) {
	
	        if(income == undefined) 
	            throw 'no input';
	
	        if (income < 1000) 
	            return income * 0.1;
	        else if (income < 10000) 
	            return income * 0.2;
	        else
	            return income * 0.4;
	    }
	}


A UDF pode, subsequentemente, ser usada em consultas como na amostra a seguir:

	// register UDF
	client.createUserDefinedFunctionAsync(collection.self, taxUdf)
		.then(function(response) { 
		    console.log("Created", response.resource);
	
		    var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
		    return client.queryDocuments(collection.self,
	               query).toArrayAsync();
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    var documents = response.feed;
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});

##Execução vinculada
Todas as operações do Banco de Dados de Documentos devem ser concluídas dentro da duração de tempo limite da solicitação especificada pelo servidor. Essa restrição também se aplica à função de JavaScript (procedimentos armazenados, gatilhos e funções definidas pelo usuário). Se uma operação não for concluída com esse limite de tempo, a transação será retrocedida. Funções JavaScript devem ser concluídas dentro do limite de tempo ou implementar um modelo com base em uma continuação para criar um lote/retomar a execução.  

A fim de simplificar o desenvolvimento de procedimentos armazenados e gatilhos para lidar com limites de tempo, todas as funções no objeto de coleção (para a criação, leitura, substituição e exclusão de documentos e anexos) retornam um valor booliano que representa se a operação será concluída. Se esse valor for falso, isso indica que o limite de tempo está prestes a expirar e que o procedimento deve encerrar a execução.  Operações colocadas em fila antes da primeira operação de armazenamento não aceita serão concluídas com certeza se o procedimento armazenado for concluído dentro do tempo e não colocar nenhuma outra solicitação em fila.  

Funções de JavaScript também são vinculadas quanto ao consumo de recursos. O Banco de Dados de Documentos reserva a produtividade por coleção com base no tamanho provisionado de uma conta do banco de dados. A produtividade é expressa em termos de uma unidade normalizada de consumo de CPU, memória e E/S chamada unidade de solicitação ou RU. Funções de JavaScript podem usar um grande número de RUs dentro de um curto período, e podem ter sua taxa limitada se o limite da coleção for atingido. Procedimentos armazenados ricos em recursos também podem ser postos em quarentena para garantir a disponibilidade das operações primitivas do banco de dados.  

##Importação em massa de dados
Abaixo está um exemplo de um procedimento armazenado gravado para documentos de importação em massa em uma coleção. Observe como o procedimento armazenado lida com a execução vinculada verificando o valor de retorno booliano em createDocument, e depois utiliza a contagem de documentos inserida em cada invocação do procedimento armazenado para rastrear e retomar o progresso nos lotes.

	function bulkImport(docs) {
	    var collection = getContext().getCollection();
	    var collectionLink = collection.getSelfLink();
	
	    // The count of imported docs, also used as current doc index.
	    var count = 0;
	
	    // Validate input.
	    if (!docs) throw new Error("The array is undefined or null.");
	
	    var docsLength = docs.length;
	    if (docsLength == 0) {
	        getContext().getResponse().setBody(0);
	    }
	
	    // Call the create API to create a document.
	    tryCreate(docs[count], callback);
	
	    // Note that there are 2 exit conditions:
	    // 1) The createDocument request was not accepted. 
	    //    In this case the callback will not be called, we just call setBody and we are done.
	    // 2) The callback was called docs.length times.
	    //    In this case all documents were created and we don't need to call tryCreate anymore. Just call setBody and we are done.
	    function tryCreate(doc, callback) {
	        var isAccepted = collection.createDocument(collectionLink, doc, callback);
	
	        // If the request was accepted, callback will be called.
	        // Otherwise report current count back to the client, 
	        // which will call the script again with remaining set of docs.
	        if (!isAccepted) getContext().getResponse().setBody(count);
	    }
	
	    // This is called when collection.createDocument is done in order to process the result.
	    function callback(err, doc, options) {
	        if (err) throw err;
	
	        // One more document has been inserted, increment the count.
	        count++;
	
	        if (count >= docsLength) {
	            // If we created all documents, we are done. Just set the response.
	            getContext().getResponse().setBody(count);
	        } else {
	            // Create next document.
	            tryCreate(docs[count], callback);
	        }
	    }
	}


##Executar operações de forma RESTful
Todas as operações do Banco de Dados de Documentos podem ser realizadas de maneira RESTful. Procedimentos armazenados, gatilhos e funções definidas pelo usuário podem ser registrados em uma coleção usando HTTP POST. A seguir está um exemplo sobre como registrar um procedimento armazenado:

	POST https://<url>/sprocs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	
	
	var x = {
	  "name": "createAndAddProperty",
	  "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
	            var collectionManager = getContext().getCollection();
	            collectionManager.createDocument(
	                collectionManager.getSelfLink(),
	                docToCreate,
	                function(err, docCreated) {
	                  if(err) throw new Error('Error:  ' + err.message);
	                  docCreated[addedPropertyName] = addedPropertyValue;
	                  getContext().getResponse().setBody(docCreated);
	                });
	        }
	}


O procedimento armazenado é registrado executando uma solicitação POST em relação à URI dbs/sehcAA==/colls/sehcAIE2Qy4=/sprocs com o corpo contendo o procedimento armazenado a ser criado. Disparadores e UDFs podem ser registrados da mesma forma, emitindo um POST para /triggers e /udfs respectivamente.
Este procedimento armazenado pode, então, ser executado emitindo uma solicitação POST ao link de recursos:

	POST https://<url>/sprocs/<sproc> HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
	
	
	[ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Aqui, a entrada para o procedimento armazenado é transmitida no corpo de solicitação. Observe que a entrada é transmitida como uma matriz JSON de parâmetros de entrada. O procedimento armazenado assume a primeira entrada como um documento que é um corpo de resposta. A resposta recebida é a seguinte:

	HTTP/1.1 200 OK
	 
	{ 
	  name: 'TestDocument',
	  book: 'Autumn of the Patriarch',
	  id: 'V7tQANV3rAkDAAAAAAAAAA==',
	  ts: 1407830727,
	  self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
	  etag: '6c006596-0000-0000-0000-53e9cac70000',
	  attachments: 'attachments/',
	  Price: 200
	}


Gatilhos, diferentemente dos procedimentos armazenados, não podem ser executados diretamente. Ao invés disso, eles são executados como parte de uma operação em um documento. Podemos especificar os gatilhos a serem executados com uma solicitação usando cabeçalhos HTTP. A seguir está uma solicitação para criar um documento.

	POST https://<url>/docs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	x-ms-documentdb-pre-trigger-include: validateDocumentContents 
	x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
	
	
	{
	   "name": "newDocument",
	   "title": "The Wizard of Oz",
	   "author": "Frank Baum",
	   "pages": 92
	}


Aqui, o pré-gatilho a ser executado com a solicitação é especificado no cabeçalho x-ms-documentdb-pre-trigger-include. Da mesma forma, qualquer pós-gatilho é fornecido no cabeçalho x-ms-documentdb-post-trigger-include. Observe que pré e pós-gatilhos podem ser especificados para uma determinada solicitação.

##Suporte a SDK
Além do cliente [Node.js](http://dl.windowsazure.com/documentDB/nodedocs/), o Banco de Dados de Documentos dá suporte a SDKs [.NET](https://msdn.microsoft.com/library/azure/dn783362.aspx), [Java](http://dl.windowsazure.com/documentdb/javadoc/), [JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/) e [Python](http://dl.windowsazure.com/documentDB/pythondocs/). Os procedimentos armazenados, gatilhos e UDFs também podem ser criados e executados usando qualquer um desses SDKs. O exemplo a seguir mostra como criar e executar um procedimento armazenado usando o cliente .NET. Observe como os tipos .NET são transferidos para o procedimento armazenado como JSON e lidos novamente.

	var markAntiquesSproc = new StoredProcedure
	{
	    Id = "ValidateDocumentAge",
	    Body = @"
	            function(docToCreate, antiqueYear) {
	                var collection = getContext().getCollection();    
	                var response = getContext().getResponse();    
	
			        if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
				        docToCreate.antique = true;
			        }
	
	                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
	                    function(err, docCreated, options) { 
	                        if(err) throw new Error('Error while creating document: ' + err.message);                              
	                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
	                        response.setBody(docCreated);
	                });
	 	    }"
	};
	
	// register stored procedure
	StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(collection.SelfLink, markAntiquesSproc);
	dynamic document = new Document() { Id = "Borges_112" };
	document.Title = "Aleph";
	document.Year = 1949;
	
	// execute stored procedure
	Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(createdStoredProcedure.SelfLink, document, 1920);


Essa amostra mostra como usar o [SDK .NET](https://msdn.microsoft.com/library/azure/dn783362.aspx) para criar um pré-gatilho e um documento com o gatilho habilitado. 

	Trigger preTrigger = new Trigger()
	{
	    Id = "CapitalizeName",
	    Body = @"function() {
	        var item = getContext().getRequest().getBody();
	        item.id = item.id.toUpperCase();
	        getContext().getRequest().setBody(item);
	    }",
	    TriggerOperation = TriggerOperation.Create,
	    TriggerType = TriggerType.Pre
	};
	
	Document createdItem = await client.CreateDocumentAsync(collection.SelfLink, new Document { Id = "documentdb" },
	    new RequestOptions
	    {
	        PreTriggerInclude = new List<string> { "CapitalizeName" },
	    });


E o exemplo a seguir mostra como criar uma função definida pelo usuário (UDF) e utilizá-la em uma [consulta SQL do Banco de Dados de Documentos](documentdb-sql-query.md).

	UserDefinedFunction function = new UserDefinedFunction()
	{
	    Id = "LOWER",
	    Body = @"function(input) 
		{
	        return input.toLowerCase();
	    }"
	};
	
	foreach (Book book in client.CreateDocumentQuery(collection.SelfLink,
	    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
	{
	    Console.WriteLine("Read {0} from query", book);
	}
##Próximas etapas

Explorar os [SDKs do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781482.aspx) para criar procedimentos armazenados, gatilhos e UDFs adicionais.


##Referências

- JSON [http://www.json.org/](http://www.json.org/) 
- JavaScript ECMA-262 [http://www.ecma-international.org/publications/standards/Ecma-262.htm ](http://www.ecma-international.org/publications/standards/Ecma-262.htm )
-	JavaScript - Sistema do tipo JSON [http://www.json.org/js.html](http://www.json.org/js.html) 
-	Extensibilidade segura e portátil do banco de dados - [http://dl.acm.org/citation.cfm?id=276339](http://dl.acm.org/citation.cfm?id=276339) 
-	Arquitetura do banco de dados orientada a serviços - [http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
-	Hospedando o tempo de execução .NET no Microsoft SQL server - [http://dl.acm.org/citation.cfm?id=1007669](http://dl.acm.org/citation.cfm?id=1007669) 

<!--HONumber=49--> 