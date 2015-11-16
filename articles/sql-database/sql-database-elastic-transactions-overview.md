<properties
   pageTitle="Visão geral das transações de banco de dados elástico com o Banco de dados SQL do Azure (em visualização)"
   description="Visão geral das transações de banco de dados elástico com o Banco de dados SQL do Azure (em visualização)"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jeffreyg"
   editor="sidneyh"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="11/02/2015"
   ms.author="torsteng"/>

# Visão geral das transações de banco de dados elástico com o Banco de dados SQL do Azure (em visualização)

As transações de banco de dados elástico para Banco de dados SQL (BD SQL) do Azure permitem que você execute transações que abranjam vários bancos de dados no BD SQL. As transações de banco de dados elástico do BD SQL estão disponíveis para aplicativos .NET usando ADO .NET e se integram à experiência de programação conhecida usando as classes [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

No local, esse cenário normalmente exigiria a execução do recurso MSDTC (Coordenador de Transações Distribuídas da Microsoft). Como o MSDTC não está disponível para aplicativos de plataforma como serviço no Azure, a capacidade de coordenar transações distribuídas agora foi diretamente integrada ao BD SQL. Os aplicativos podem se conectar a qualquer Banco de Dados SQL para iniciar transações distribuídas, e um dos bancos de dados coordenará de forma transparente a transação distribuída, como mostra a figura a seguir.

  ![Transações distribuídas com o Banco de Dados SQL do Azure usando transações de banco de dados elástico][1]

## Cenários comuns

As transações de banco de dados elástico do BD SQL permitem que os aplicativos façam alterações atômicas nos dados armazenados em vários Bancos de Dados SQL diferentes. A visualização se concentra nas experiências de desenvolvimento do lado do cliente em C# e .NET. Há planos para uma experiência do lado do servidor usando o T-SQL para um momento posterior. As transações de banco de dados elástico têm como objetivo os seguintes cenários:

* Aplicativos de vários bancos de dados no Azure: com esse cenário, os dados são particionados verticalmente em vários bancos de dados no BD SQL, de modo que os diferentes tipos de dados residam em bancos de dados diferentes. Algumas operações exigem alterações nos dados, que são mantidos em dois ou mais bancos de dados. O aplicativo usa transações de banco de dados elástico para coordenar as alterações nos bancos de dados e garantir a atomicidade.

* Aplicativos de banco de dados fragmentado no Azure: com esse cenário, a camada de dados usa a biblioteca do cliente de banco de dados elástico ou a [autofragmentação](http://social.technet.microsoft.com/wiki/contents/articles/17987.cloud-service-fundamentals.aspx) para particionar horizontalmente os dados em vários bancos de dados no BD SQL. Um caso de uso importante é a necessidade de realizar mudanças atômicas em um aplicativo multilocatário fragmentado quando as alterações se estendem aos locatários. Pense, por exemplo, em uma transferência de um locatário para outro, ambos residentes em bancos de dados diferentes. Um segundo caso é a fragmentação refinada para acomodar as necessidades de capacidade de um locatário grande, o que, por sua vez, geralmente implica na necessidade de extensão de algumas operações atômicas em vários bancos de dados usados para o mesmo locatário. Um terceiro caso são as atualizações atômicas de dados de referência que são replicados nos bancos de dados. As operações atômicas e transacionadas, juntamente com estas linhas, agora podem ser coordenadas em vários bancos de dados usando a visualização. As transações de banco de dados elástico usam o protocolo 2PC para garantir a atomicidade das transações nos bancos de dados. É uma boa opção para as transações que envolvem menos de 100 bancos de dados por vez em uma única transação. Esses limites não são impostos, mas o desempenho e as taxas de êxito das transações de banco de dados elástico provavelmente serão prejudicados se eles forem ultrapassados.


## Instalação e migração

Os recursos das transações de banco de dados elástico no BD SQL são fornecidos por meio de atualizações das bibliotecas .NET System.Data.dll e System.Transactions.dll. As DLLs garantem que o protocolo 2PC seja usado quando necessário para garantir a atomicidade. Para começar a desenvolver aplicativos usando as transações de banco de dados elástico, instale o [.NET 4.6.1 Release Candidate](http://blogs.msdn.com/b/dotnet/archive/2015/10/29/announcing-net-framework-4-6-1-rc.aspx) ou uma versão posterior do .NET Framework. Quando executadas em uma versão anterior do .NET Framework, as transações não serão promovidas a uma transação distribuída, e uma exceção será gerada.

Após a instalação, você pode usar as APIs de transação distribuída em System.Transactions com conexões ao BD SQL. Se você já tiver aplicativos do MSDTC usando essas APIs, basta recompilar os aplicativos existentes para o .NET 4.6 depois de instalar a versão Release Candidate. Se seus projetos visam o .NET 4.6, eles usarão automaticamente as DLLs atualizadas da Release Candidate, e as chamadas de API de transação distribuída em conjunto com as conexões ao BD SQL passarão a funcionar.

Lembre-se de que as transações de banco de dados elástico não requerem a instalação do MSDTC. Em vez disso, as transações de banco de dados elástico são gerenciadas diretamente pelo BD SQL, dentro dele. Isso simplifica significativamente os cenários de nuvem, já que a implantação de um MSDTC não é necessária para o uso das transações distribuídas com o BD SQL. A Seção 4 explica detalhadamente como implantar as transações de banco de dados elástico e o .NET Framework necessário juntamente com seus aplicativos em nuvem no Azure.

## Experiência de desenvolvimento

###	Aplicativos de vários bancos de dados

O exemplo de código a seguir usa a experiência de programação conhecida com o .NET System.Transactions. A classe TransactionScope estabelece uma transação de ambiente no .NET. Uma “transação de ambiente” é aquela que reside no thread atual. Todas as conexões abertas no TransactionScope participam da transação. Se diferentes bancos de dados participarem, a transação será elevada automaticamente a uma transação distribuída. O resultado da transação é controlado pela definição do escopo a concluir para indicar uma confirmação.

	using (var scope = new TransactionScope())
	{
		using (var conn1 = new SqlConnection(connStrDb1))
		{
			conn1.Open();
			SqlCommand cmd1 = conn1.CreateCommand();
			cmd1.CommandText = string.Format("insert into T1 values(1)");
			cmd1.ExecuteNonQuery();
		}

		using (var conn2 = new SqlConnection(connStrDb2))
		{
			conn2.Open();
			var cmd2 = conn2.CreateCommand();
			cmd2.CommandText = string.Format("insert into T2 values(2)");
			cmd2.ExecuteNonQuery();
		}

		scope.Complete();
	}

### Aplicativos de banco de dados fragmentado
 
As transações de banco de dados elástico do BD SQL também oferecem suporte a coordenação de transações distribuídas, em que você usa o método OpenConnectionForKey da biblioteca do cliente de banco de dados elástico para abrir conexões para uma camada de dados escalados horizontalmente. Considere os casos em que você precisa garantir a consistência transacional das alterações em vários valores-chave de fragmentação diferentes. As conexões com os fragmentos que hospedam os diferentes valores-chave de fragmentação são intermediadas usando o OpenConnectionForKey. Em geral, as conexões podem ocorrer para diferentes fragmentos, de modo que para assegurar as garantias transacionais seja necessária uma transação distribuída. O exemplo de código a seguir ilustra essa abordagem. Ele pressupõe que uma variável chamada shardmap seja usada para representar um mapa do fragmento na biblioteca do cliente de banco de dados elástico:

	using (var scope = new TransactionScope())
	{
		using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
		{
			conn1.Open();
			SqlCommand cmd1 = conn1.CreateCommand();
			cmd1.CommandText = string.Format("insert into T1 values(1)");
			cmd1.ExecuteNonQuery();
		}
		
		using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
		{
			conn2.Open();
			var cmd2 = conn2.CreateCommand();
			cmd2.CommandText = string.Format("insert into T1 values(2)");
			cmd2.ExecuteNonQuery();
		}

		scope.Complete();
	}


## Configuração das funções de trabalho no Azure

Você pode automatizar a instalação e a implantação da versão e das bibliotecas .NET necessárias para as transações de banco de dados elástico no Azure (no SO convidado do seu serviço de nuvem). Para funções de trabalho no Azure, use as tarefas de inicialização. Os conceitos e as etapas estão documentados em [Instalar o .NET em uma Função do Serviço de Nuvem](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/).

## Monitorando o status da transação

Use as DMVs (Exibições de Gerenciamento Dinâmico) no BD SQL para monitorar o status e o progresso das transações de banco de dados elástico em andamento. Todas as DMVs relacionadas com transações são relevantes para as transações distribuídas no BD SQL. Você pode encontrar a lista correspondente de DMVs aqui: [Transaction Related Dynamic Management Views and Functions (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Estas DMVs são especialmente úteis:

* **sys.dm\_tran\_active\_transactions**: lista as transações atualmente ativas e seu status. A coluna UOW (Unidade de Trabalho) pode identificar as diferentes transações filho que pertencem à mesma transação distribuída. Todas as transações dentro da mesma transação distribuída carregam o mesmo valor UOW. Consulte a [documentação da DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais detalhes.
* **sys.dm\_tran\_database\_transactions**: fornece informações adicionais sobre as transações, como a colocação da transação no log. Consulte a [documentação da DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais detalhes.
* **sys.dm\_tran\_locks**: fornece informações sobre os bloqueios que são atualmente mantidos por transações em andamento. Consulte a [documentação da DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais detalhes.

## Limitações 

As seguintes limitações se aplicam atualmente para as transações de banco de dados elástico no BD SQL:

* Há suporte somente para transações em bancos de dados no BD SQL. Outros provedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bancos de dados fora do BD SQL não podem participar de transações de banco de dados elástico. Isso significa que as transações de banco de dados elástico não podem se estender para o SQL Server e o Banco de Dados SQL do Azure no local. Para transações distribuídas no local, continue a usar o MSDTC. 
* Há suporte somente para transações coordenadas pelo cliente a partir de um aplicativo .NET. Há planos para suporte do lado do servidor para o T-SQL, como INICIAR TRANSAÇÃO DISTRIBUÍDA, mas ainda não está disponível. 
* Há suporte somente para bancos de dados no V12 do BD SQL do Azure.
* Há suporte somente para bancos de dados que pertençam ao mesmo servidor lógico no BD SQL.

## Saiba mais

Você ainda não está usando os recursos de banco de dados elástico nos seus aplicativos do Azure? Confira o nosso [Mapa da Documentação](https://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-documentation-map/). Em caso de dúvidas, entre em contato conosco pelo fórum [Banco de Dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e, para solicitações de recursos, adicione-as ao [Fórum de comentários do SQL Database](http://feedback.azure.com/forums/217321-sql-database).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png

<!---HONumber=Nov15_HO2-->