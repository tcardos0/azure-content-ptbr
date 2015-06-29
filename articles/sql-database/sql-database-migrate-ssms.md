<properties
   pageTitle="Migrando para o Banco de Dados SQL usando SSMS"
   description="Banco de Dados SQL do Microsoft Azure, migrar banco de dados sql, migrar usando ssms"
   services="sql-database"
   documentationCenter=""
   authors="pehteh"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#Migrando um banco de dados compatível usando SSMS 

![texto alt](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Se um esquema de banco de dados já for compatível com o Banco de Dados SQL do Azure, a migração será muito simples. Uma vez que não há qualquer necessidade de transformação, a migração exigirá apenas que o banco de dados seja importado para o Azure. Isso pode ser feito em uma única etapa com o SSMS “implantando” o banco de dados no Banco de Dados SQL do Azure, ou como um processo de duas etapas, primeiro exportando um BACPAC e, em seguida, importando-o para um servidor do Banco de Dados SQL do Azure a fim de criar um novo banco de dados.

Você pode carregar o BACPAC exportado no Armazenamento do Azure e importá-lo usando o portal. A execução da importação na nuvem reduzirá a latência na etapa de importação, o que aprimorará o desempenho e a confiabilidade da migração com bancos de dados de grande porte.

A implantação diretamente do SSMS sempre implantará o esquema e os dados, enquanto a exportação e importação sempre implantará o esquema e fornecerá uma opção para implantar os dados de todas as tabelas ou de um subconjunto delas. Se você implantar do SSMS ou exportar e importar do SSMS (ou, posteriormente, do portal), a mesma tecnologia DAC será usada e o resultado será o mesmo.

Essa opção também é usada como a etapa final na opção nº 3 para migrar os bancos de dados depois que eles forem atualizados para torná-los compatíveis com o Banco de Dados SQL do Azure.

##Usando o SSMS para implantar no Banco de Dados SQL do Azure
1.	Provisione um servidor usando o Portal do Azure, conforme descrito em Criar o servidor de destino para o banco de dados migrado.
2. Localize o banco de dados de origem no Pesquisador de Objetos do SSMS e execute a tarefa **Implantar o banco de dados no Banco de Dados SQL do Windows Azure...**

	![texto alt](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	No assistente de implantação, configure a conexão com o servidor do Banco de Dados SQL do Azure de destino fornecido na etapa.
4.	Forneça o **nome** do banco de dados e defina a **Edição** (camada de serviço) e o **Objetivo do Serviço** (nível de desempenho). Consulte Escolhendo um nível desempenho/camada de preços de banco de dados para a migração para saber mais sobre como definir essas configurações. 

	![texto alt](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Conclua o assistente para executar a migração do banco de dados. Dependendo do tamanho e da complexidade da implantação do banco de dados, talvez de demore alguns minutos até muitas horas. Se ocorrerem erros que indicam que o esquema do banco de dados é incompatível com o Banco de Dados SQL, será necessário usar outra opção.
##Usar o SSMS para exportar um BACPAC e, em seguida, importá-lo no Banco de Dados SQL
O processo de implantação pode ser dividido em duas etapas: exportação e importação. Na primeira etapa, um arquivo BACPAC é criado e, em seguida, usado como informação na segunda etapa.

1.	Provisione um servidor usando a Atualização do Banco de Dados SQL mais recente, conforme descrito em Criar o servidor de destino para o banco de dados migrado.
2.	Localize o banco de dados de origem no Pesquisador de Objetos do SSMS e selecione a tarefa **Implantar o banco de dados no Banco de Dados SQL do Windows Azure...**

	![texto alt](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. No assistente de exportação, configure a exportação para salvar o arquivo BACPAC localmente. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você quiser excluir dados de algumas ou de todas as tabelas, use a guia Avançado. Por exemplo, você pode optar por exportar somente os dados de tabelas de referência.
	>[AZURE.NOTE]Observação: quando o Portal de Gerenciamento do Azure oferecer suporte à importação em execução no Azure, você poderá optar por salvar o arquivo BACPAC exportado no Armazenamento do Azure e executar a importação na nuvem.

	![texto alt](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Quando o BACPAC tiver sido criado, conecte-se ao servidor criado na etapa 1, clique com o botão direito do mouse na pasta Bancos de Dados e selecione Importar Aplicativo da Camada de Dados...

	![texto alt](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	No assistente de importação, selecione o arquivo BACPAC recém-exportado para criar o novo Banco de Dados SQL do Azure.

	![texto alt](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Forneça o nome do banco de dados e defina a Edição (camada de serviço) e o Objetivo do Serviço (nível de desempenho).
	 
7.	Conclua o assistente para importar o arquivo BACPAC e criar o banco de dados no Banco de Dados SQL do Azure.

	![texto alt](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##Alternativas
Também é possível usar o utilitário de linha de comando sqlpackage.exe para implantar o banco de dados ou exportar e importar um BACPAC. Sqlpackage.exe usa a mesma tecnologia DAC que o SSMS. Portanto, o resultado é o mesmo. Para saber mais, clique [aqui](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=58--> 