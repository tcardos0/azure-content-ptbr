<properties 
	pageTitle="Como criar e configurar um banco de dados SQL do Azure - Tutorial do Azure" 
	description="Como criar e configurar um banco de dados SQL do Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2014" 
	ms.author="sidneyh"/>

# Como criar e configurar um banco de dados SQL do Azure

Neste tópico, você irá criar e configurar um novo banco de dados SQL Azure usando a opção **CRIAÇÃO RÁPIDA** do Portal de gerenciamento Azure. Esse processo mostra como criar um banco de dados SQL usando um servidor existente e também mostra como criar um novo servidor, se necessário.

> [AZURE.NOTE] Criando um banco de dados SQL com o **CRIAÇÃO RÁPIDA** provisiona um banco de dados padrão (S0). Para criar um banco de dados SQL em um nível de desempenho e de camada de serviço que não seja de uso padrão (S0) use **CRIAÇÃO PERSONALIZADA**. Para obter detalhes sobre como criar um banco de dados SQL usando **CRIAÇÃO PERSONALIZADA**, consulte [Guia de Introdução ao Microsoft Azure SQL Database](http://azure.microsoft.com/documentation/articles/sql-database-get-started/).

## Como: Criar um banco de dados SQL do Azure

1. Entre no [Portal de Gerenciamento](https://portal.azure.com/).

2. Na parte inferior da página, clique em **NOVO**.

	![Click SQL Databases][1]

3. Clique em **SERVIÇOS DE DADOS**, e **BANCO DE DADOS SQL** depois em **CRIAÇÃO PERSONALIZADA**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Em **CRIAÇÃO RÁPIDA**, insira um nome para o novo banco de dados, selecione uma assinatura e, em seguida, selecione um servidor da lista de **SERVIDOR** (ou pule para a próxima etapa para criar um novo servidor).

	![Create a new SQL Database in an existing server][7]

	Opcionalmente, crie um novo servidor selecionando **Novo servidor de banco de dados SQL**.
    ![Create a new SQL Database and a new server][8]

	1. Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.
	2. Digite um nome de logon como uma palavra sem espaços. 

		O Banco de Dados SQL usa a Autenticação do SQL por meio de uma conexão criptografada. Será criado um novo logon de autenticação do SQL Server atribuído à função do servidor fixo do sysadmin usando o nome fornecido. 

		O logon não pode ser um endereço de email, conta de usuário do Windows ou um ID do Windows Live. Declarações ou autenticação do Windows não são suportadas no Banco de Dados SQL. 
	3. Forneça uma senha forte com mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

	


9. Clique na marca de seleção **CRIAR BANCO DE DADOS SQL** na parte inferior da página quando tiver terminado.

### Nome do servidor gerado automaticamente

Observe que se você tiver criado um novo servidor você não especificou um nome de servidor. O Banco de dados SQL gera automaticamente o nome do servidor para garantir que não existam entradas do DNS duplicadas. O nome do servidor é uma cadeia de 10 caracteres alfanuméricos. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Na próxima etapa, você configurará o firewall para que as conexões de aplicativos em execução em sua rede tenham permissão de acesso.

<a id="configFWLogical"></a>

## Como: Configurar o firewall para o servidor lógico

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **banco de dados SQL**, então clique em **Servidores**

	![Click Servers][4]
2. Na lista, clique no servidor que você acabou de criar.

2. Clique em **Configurar**.

	![Click Configure][5]

3. Na seção **endereços ip permitidos**, clique em **ADICIONAR OS ENDEREÇOS IP PERMITIDOS**. Esse é o endereço IP no qual o roteador ou o servidor proxy está escutando no momento. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual e cria uma regra de firewall para aceitar solicitações de conexão nesse dispositivo. 
	![Click Add to the allowed IP addresses][6]

	Um nome padrão para a regra é gerado. Edite o nome conforme desejar. 
	

4. Quando você se conectar ao banco de dados de outro computador, deve criar uma nova regra para permitir que o endereço IP se conecte. Use as caixas Início e Término para criar um intervalo de endereços IP.

	Se os computadores cliente usam endereços IP atribuídos dinamicamente, você pode especificar um intervalo que seja amplo o suficiente para incluir os endereços IP atribuídos aos computadores em sua rede. Inicie com um intervalo estreito e depois expanda-o apenas se for necessário.

7. Clique em **Salvar** na parte inferior da página para concluir a etapa. 

Agora você tem um banco de dados SQL, um servidor lógico e uma regra de firewall que permite conexões de entrada de seu endereço IP e um logon de administrador. 

**Observação:** o servidor lógico que você acabou de criar é virtual e será hospedado dinamicamente em servidores físicos em um data center. A exclusão do servidor é uma ação não recuperável. Certifique-se de fazer o backup de bancos de dados que você posteriormente carregará para o servidor. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png




<!--HONumber=47-->
 