<properties 
	pageTitle="Introdução à Sincronização de dados dos Bancos de Dados SQL" 
	description="Esse tutorial ajudará você a começar a Sincronização de Dados do SQL do Azure (visualização)." 
	services="sql-database" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2015" 
	ms.author="spelluru"/>


#Introdução à Sincronização de Dados do SQL do Azure (visualização)
Neste tutorial, você aprenderá os conceitos básicos da Sincronização de Dados do SQL do Azure usando o portal do Azure (Visualização). 

Este tutorial pressupõe uma experiência mínima anterior com SQL Server e com o Banco de Dados SQL do Azure. Neste tutorial, você criará um grupo de sincronização híbrido (instâncias do Banco de Dados SQL e do SQL Server) totalmente configurado e sincronizando no agendamento definido.


## Etapa 1: Conecte-se ao Banco de Dados SQL do Azure

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com).

2. Clique em **BANCOS DE DADOS SQL** no painel esquerdo.

3. Clique em **SINCRONIZAR** na parte inferior da página. Quando você clica em SINCRONIZAR, uma lista é exibida mostrando os itens que você pode adicionar - **Novo Grupo de Sincronização** e **Novo Agente de Sincronização**.

4. Para iniciar o assistente Novo Agente de Sincronização de Dados SQL, clique em **Novo Agente de Sincronização**.

5. Se você ainda não tiver adicionado um agente, **clique para baixá-lo aqui**.

	![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## Etapa 2: Adicione um Agente Cliente
Essa etapa somente será necessária se você pretender ter um banco de dados local do SQL Server incluído no seu grupo de sincronização. Você pode pular para a etapa 4: Crie um grupo de sincronização se o seu grupo de sincronização tiver apenas instâncias do Banco de Dados SQL.

<h3><a id="InstallRequiredSoftware"></a>Etapa 2a: Instale o software necessário</h3>
Certifique-se de que você tenha os itens a seguir no computador que você instalar o Agente Cliente.

- **.NET Framework 4.0** 

 Você pode instalar o .NET Framework 4.0 [daqui](http://go.microsoft.com/fwlink/?linkid=205836).

- **Tipos de CLR do sistema Microsoft SQL Server 2008 R2 SP1 (x86)**

 Você pode instalar os tipos de CLR do sistema Microsoft SQL Server 2008 R2 SP1 (x86) [daqui](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Objetos de gerenciamento compartilhado do Microsoft SQL Server 2008 R2 SP1 (x86)**

 Você pode instalar os objetos de gerenciamento compartilhado do Microsoft SQL Server 2008 R2 SP1 (x86) [daqui](http://www.microsoft.com/download/en/details.aspx?id=26728)


<h3><a id="InstallClient"></a>Etapa 2b: Instale um novo Agente Cliente</h3>

Siga a instrução em [Instalar um Agente Cliente (Sincronização de Dados do SQL)](http://msdn.microsoft.com/library/jj823137.aspx) para instalar o agente. 


<h3><a id="RegisterSSDb"></a>Etapa 2c: Conclua o assistente Novo Agente de Sincronização de Dados SQL</h3> 

1. 	Retorne ao o assistente Novo Agente de Sincronização de Dados SQL.
2.	Atribua um nome significativo ao agente.
3.	No menu suspenso, selecione a **REGIÃO** (data center) para hospedar esse agente.
4.	No menu suspenso, selecione a **ASSINATURA** para hospedar esse agente.
5.	Clique na seta para a direita.



## Etapa 3: Registre um banco de dados do SQL Server com o Agente Cliente

Depois que o Agente Cliente for instalado, registre cada banco de dados do SQL Server local que você pretende incluir em um grupo de sincronização com o agente.
Para registrar um banco de dados com o agente, siga as instruções em [Registrar um banco de dados do SQL Server com um Agente Cliente](http://msdn.microsoft.com/library/jj823138.aspx)



## Etapa 4: Crie um grupo de sincronização

<h3><a id="StartNewSGWizard"></a>Etapa 4a: Inicie o assistente Novo Grupo de Sincronização</h3>
1.	Retorne ao [Portal de Gerenciamento](http://manage.windowsazure.com).
2.	Clique em **BANCOS DE DADOS SQL**.
3.	Clique em **ADICIONAR SINCRONIZAÇÃO** na parte inferior da página e selecione Novo Grupo de Sincronização na gaveta.

	![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)


<h3><a id=""></a>Etapa 4b: Insira as configurações básicas</h3>	
1.	Insira um nome significativo para o grupo de sincronização.
2.	No menu suspenso, selecione a **REGIÃO** (Data Center) para hospedar esse grupo de sincronização.
3. Clique na seta para a direita.

	![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)
 
<h3><a id="DefineHubDB"></a>Etapa 4c: Defina o hub de sincronização</h3>
1. No menu suspenso, selecione a instância do Banco de Dados SQL para servir como hub do grupo de sincronização.
2. Insira as credenciais dessa instância do Banco de Dados SQL - **NOME DE USUÁRIO DO HUB** e **SENHA DO HUB**.
3. Aguarde a Sincronização de Dados do SQL para confirmar o NOME DE USUÁRIO e a SENHA. Uma marca de seleção verde é exibida à direita da SENHA quando as credenciais são confirmadas.
4. No menu suspenso, selecione a política **RESOLUÇÃO DE CONFLITOS**.

 **Hub Vence** - qualquer alteração gravada no banco de dados hub será gravada nos bancos de dados de referência, substituindo as alterações no mesmo registro de banco de dados de referência. Funcionalmente, isso significa que a primeira alteração gravada no hub é propagada para os outros bancos de dados.


 **Cliente Vence** - as alterações gravadas no hub são substituídas pelas alterações nos bancos de dados de referência. Funcionalmente, isso significa que a última alteração gravada no hub é a mantida e propagada para os outros bancos de dados.

5.	Clique na seta para a direita.

	![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<h3><a id="AddRefDB"></a>Etapa 4d: Adicione um banco de dados de referência</h3>

Repita essa etapa para cada banco de dados adicional que você quiser adicionar ao grupo de sincronização.

1. No menu suspenso, selecione o banco de dados a ser adicionado.

	Os bancos de dados no menu suspenso incluem os bancos de dados do SQL Server registrados com o agente e as instâncias do Banco de Dados SQL.
2.	Insira as credenciais desse banco de dados - **NOME DE USUÁRIO** e **SENHA**.
3.	No menu suspenso, selecione a **DIREÇÃO DE SINCRONIZAÇÃO** para esse banco de dados.

	**Bidirecional** - as alterações no banco de dados de referência são gravadas nos bancos de dados hub, e as alterações no banco de dados hub são gravadas no banco de dados de referência.

	**Sincronizar do Hub** - o banco de dados recebe atualizações do Hub. Ele não envia alterações para o Hub.

	**Sincronizar com o Hub** - o banco de dados envia atualizações para o Hub. As alterações no Hub não são gravadas nesse banco de dados.

4.	Para concluir a criação do grupo de sincronização, clique na marca de seleção localizada no canto inferior direito do assistente. Aguarde até que a Sincronização de Dados do SQL confirme as credenciais. Uma marca de seleção verde indica que as credenciais foram confirmadas.

5.	Clique na marca de seleção uma segunda vez. Isso o retornará à página **SINCRONIZAR** em Bancos de Dados SQL. Esse grupo de sincronização agora está listado com seus outros grupos de sincronização e agentes.

	![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## Etapa 5: Defina os dados a serem sincronizados

A Sincronização de Dados do SQL do Azure permite que você selecione tabelas e colunas para sincronização. Se desejar filtrar uma coluna de modo que apenas as linhas com valores específicos (como Idade > = 65) sejam sincronizadas, use o portal Sincronização de Dados do SQL no Azure e a documentação em Selecione as tabelas, colunas e linhas para sincronizar para definir os dados para sincronização.

1.	Retorne ao [Portal de Gerenciamento](http://manage.windowsazure.com).
2.	Clique em **BANCOS DE DADOS SQL**.
3.	Clique na guia **SINCRONIZAR**.
4.	Clique no nome desse grupo de sincronização.
5.	Clique na guia **REGRAS DE SINCRONIZAÇÃO**.
6.	Selecione o banco de dados que você deseja fornecer ao esquema de grupo de sincronização.
7.	Clique na seta para a direita.
8.	Clique em **ATUALIZAR ESQUEMA**.
9.	Para cada tabela no banco de dados, selecione as colunas a serem incluídas nas sincronizações. 
	- Não é possível selecionar colunas com tipos de dados sem suporte. 
	- Se nenhuma coluna de uma tabela estiver selecionadas, a tabela não será incluída no grupo de sincronização. 
	- Para selecionar/cancelar a seleção de todas as tabelas, clique em SELECIONAR na parte inferior da tela.
10.	Clique em **SALVAR** e aguarde até que o grupo de sincronização conclua o provisionamento.
11.	Para retornar à página de aterrissagem Sincronização de Dados, clique na seta para voltar localizada na parte superior esquerda da tela (acima do nome do grupo de sincronização).

	![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## Etapa 6: Configure seu grupo de sincronização

Você poderá sempre sincronizar um grupo de sincronização, clicando em SINCRONIZAR na parte inferior da página de aterrissagem Sincronização de Dados.
Se desejar que um grupo de sincronização seja sincronizado em um agendamento, configure o grupo de sincronização.

1.	Retorne ao [Portal de Gerenciamento](http://manage.windowsazure.com).
2.	Clique em **BANCOS DE DADOS SQL**.
3.	Clique na guia **SINCRONIZAR**.
4.	Clique no nome desse grupo de sincronização.
5.	Clique na guia **CONFIGURAR**.
6.	**SINCRONIZAÇÃO AUTOMÁTICA**
	- Para configurar o grupo de sincronização para sincronizar em uma frequência definida, clique em **ATIVAR**. Você ainda poderá sincronizar sob demanda, clicando em SINCRONIZAR.
	- Clique em **DESATIVAR** para configurar o grupo de sincronização para sincronizar somente quando você clicar em SINCRONIZAR.
7.	**FREQUÊNCIA DE SINCRONIZAÇÃO**
	- Se SINCRONIZAÇÃO AUTOMÁTICA estiver definida como ATIVAR, defina a frequência de sincronização. A frequência deve ser um valor entre 5 Minutos e 1 Mês.
8.	Clique em **SALVAR**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Parabéns. Você criou um grupo de sincronização que inclui uma instância do Banco de Dados SQL e um banco de dados do SQL Server.

## Próximas etapas
Para obter informações adicionais sobre banco de dados SQL e Sincronização de Dados do SQL, consulte:

* [Inscreva-se para a oferta Premium do banco de dados SQL] (sign-up-for-sql-database-premium.md)
* [Sincronização de Dados do SQL (Portal do WA)](http://msdn.microsoft.com/library/windowsazure/jj856263.aspx)
* [Introdução ao banco de dados SQL do Azure](../getting-started-w-sql-databases.md)
* [Ciclo de vida de banco de dados do SQL Server ](http://go.microsoft.com/fwlink/?LinkId=275193)










<!--HONumber=47-->
 