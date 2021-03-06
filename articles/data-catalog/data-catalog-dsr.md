<properties
   pageTitle="Fontes de dados com suporte do Catálogo de Dados do Azure | Microsoft Azure"
   description="Especificação das fontes de dados com suporte no momento."
   services="data-catalog"
   documentationCenter=""
   authors="trhabe"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="01/15/2016"
   ms.author="trhabe"/>

# Fontes de dados com suporte do Catálogo de Dados do Azure

Os usuários do Catálogo de Dados do Azure podem publicar os metadados usando uma API pública, uma ferramenta de registro com um clique ou inserindo manualmente as informações de forma direta no portal da Web do Catálogo de Dados. A grade abaixo resume todas as fontes com suporte hoje do catálogo e os recursos de publicação para cada uma. Também estão listadas as ferramentas de dados externas que cada fonte pode inicializar a partir de nossa experiência "aberta" do portal. E mais, a seguir está uma segunda grade que tem uma especificação mais técnica de cada propriedade de conexão das fontes de dados.


## Lista das fontes de dados com suporte

<table>

    <tr>
       <td><b>Objeto da Fonte de Dados</b></td>
       <td><b>API</b></td>
       <td><b>Entrada Manual</b></td>
       <td><b>Ferramenta de Registro</b></td>
       <td><b>Ferramentas Abertas</b></td>
       <td><b>Observações</b></td>
    </tr>

    <tr>
      <td>Diretório de Armazenamento do Azure Data Lake</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do Repositório Azure Data Lake</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Blob de Armazenamento do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório de Armazenamento do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório do HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição de Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de Exibição do MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do Oracle Database</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do Oracle Database</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Outros (Ativo Genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensão do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Medida do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório do SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Navegador</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SAP Hana</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do Sistema de Arquivos</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório do FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório do HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ponto de extremidade do HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Conjunto de entidades do Odata</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Função do Odata</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SAP Hana</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objeto do SalesForce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Lista do SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>


<br> <br>
## Especificação de referência da fonte de dados

<table>
    <tr>
       <td><b>Tipo de Fonte</b></td>
       <td><b>Tipo de Raiz</b></td>
       <td><b>Tipo de objeto</b></td>
       <td><b>Contido no tipo de objeto</b></td>
       <td><b>Estrutura do DSR<b></td>
    </tr>


    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Tabela</td>
      <td>Diretório</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticação: {básica, oauth}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocolo: webhdfs}
            <br>autenticação: {básica, oauth}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Contêiner</td>
      <td>Cluster Virtual</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: cosmos
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Serviços de Relatório do SQL Server</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: reporting-services
            <br>autenticação: {windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; versão {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Blob</td>
      <td>Contêiner</td>
      <td>
        <font size=2> protocolo: azure-blobs
            <br>autenticação: {azure-access-key}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; domínio
            <br>&#160;&#160;&#160;&#160;&#160; conta
            <br>&#160;&#160;&#160;&#160;&#160; contêiner
            <br>&#160;&#160;&#160;&#160;&#160; nome </font>
      </td>
    </tr>

    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Diretório</td>
      <td>Contêiner</td>
      <td>
        <font size=2> protocolo: azure-blobs
            <br>autenticação: {azure-access-key}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; domínio
            <br>&#160;&#160;&#160;&#160;&#160; conta
            <br>&#160;&#160;&#160;&#160;&#160; contêiner
            <br>&#160;&#160;&#160;&#160;&#160; nome </font>
      </td>
    </tr>

    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Armazenamento do Azure</td>
      <td>
        <font size=2> protocolo: azure-tables
            <br>autenticação: {azure-access-key}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; domínio
            <br>&#160;&#160;&#160;&#160;&#160; conta
            <br>&#160;&#160;&#160;&#160;&#160; nome </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticação: {windows, básica, anônima}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>
            <br>* básica/anônima disponível somente em HTTPS </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Fluxo</td>
      <td>Cluster Virtual</td>
      <td>
        <font size=2> protocolo: cosmos
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Conjunto de Fluxo</td>
      <td>Cluster Virtual</td>
      <td>
        <font size=2> protocolo: cosmos
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Cluster Virtual</td>
      <td>
        <font size=2> protocolo: cosmos
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Db2</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: db2
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>Db2</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: db2
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Db2</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: db2
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Sistema de Arquivos</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: file
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; caminho </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Diretório</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: ftp
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: ftp
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticação: {windows, básica, anônima}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>
            <br>* básica/anônima disponível somente em HTTPS </font>
      </td>
    </tr>

    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Tabela</td>
      <td>Diretório</td>
      <td>HDInsight</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticação: {básica, oauth}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>HDInsight</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticação: {básica, oauth}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Contêiner</td>
      <td>Data Lake</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticação: {básica, oauth}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: hive
            <br>autenticação: {hdinsight, básica, nome de usuário}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: hive
            <br>autenticação: {hdinsight, básica, nome de usuário}
            <br>endereço: servidor
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Armazenamento do Azure</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: azure-tables
            <br>autenticação: {azure-access-key}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; domínio
            <br>&#160;&#160;&#160;&#160;&#160; conta </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Tabela</td>
      <td>Ponto de Extremidade</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: azure-blobs
            <br>autenticação: {Azure-Access-Key}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; domínio
            <br>&#160;&#160;&#160;&#160;&#160; conta
            <br>&#160;&#160;&#160;&#160;&#160; contêiner </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: mysql
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: mysql
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Contêiner</td>
      <td>HDInsight</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticação: {básica, oauth}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Odata</td>
      <td>Contêiner</td>
      <td>Contêiner da entidade</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: odata
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: hiveserver2
            <br>autenticação: {hdinsight, básica, nome de usuário, nenhuma}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; porta
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>Odata</td>
      <td>Tabela</td>
      <td>Conjunto de Entidades</td>
      <td>Contêiner da entidade</td>
      <td>
        <font size=2> protocolo: odata
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; recurso </font>
      </td>
    </tr>

    <tr>
      <td>Banco de dados Oracle</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: oracle
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Banco de dados Oracle</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: oracle
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Odata</td>
      <td>Tabela</td>
      <td>Função</td>
      <td>Contêiner da entidade</td>
      <td>
        <font size=2> protocolo: odata
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; recurso </font>
      </td>
    </tr>

    <tr>
      <td>Outros</td>
      <td>Tabela</td>
      <td>Outros</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: generic-asset
            <br>autenticação: {nenhuma, básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: postgresql
            <br>autenticação: {básica, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: postgresql
            <br>autenticação: {básica, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: postgresql
            <br>autenticação: {básica, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: mysql
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: sap-hana-sql
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Salesforce</td>
      <td>Tabela</td>
      <td>Objeto</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: salesforce-com
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>

    <tr>
      <td>SharePoint</td>
      <td>Tabela</td>
      <td>Listar</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: sharepoint-list
            <br>autenticação: {básica, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Função com valor de tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Banco de dados Oracle</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: oracle
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticação: {windows, básica, anônima}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Kpi}
            <br>
            <br>* básica/anônima disponível somente em HTTPS </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticação: {windows, básica, anônima}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Medida} </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Tabela</td>
      <td>Dimensão</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticação: {windows, básica, anônima}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimensão} </font>
      </td>
    </tr>

    <tr>
      <td>Hana SAP</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: sap-hana-sql
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; servidor </font>
      </td>
    </tr>

    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticação: {windows, básica, anônima}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Tabela}
            <br>
            <br>* básica/anônima disponível somente em HTTPS </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticação: {sql, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>Serviços de Relatório do SQL Server</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: reporting-services
            <br>autenticação: {windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; versão {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>N/D</td>
      <td>
        <font size=2> protocolo: teradata
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; banco de dados </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: teradata
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: teradata
            <br>autenticação: {protocolo, windows}
            <br>endereço:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>



</table>

<!---HONumber=AcomDC_0121_2016-->